---
title: Планировщик заданий Android
description: В этом руководстве описывается планирование фоновой работы, с помощью Android API планировщика заданий.
ms.prod: xamarin
ms.assetid: 673BB8C3-C5CC-43EC-BA8F-758F15D986C9
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/19/2018
ms.openlocfilehash: 4bbb217fa8a3192905d016763b961e182224aa67
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108773"
---
# <a name="android-job-scheduler"></a>Планировщик заданий Android

_В этом руководстве описывается планирование фоновой работы, с помощью Android API планировщика заданий, которое доступно на устройствах Android под управлением Android 5.0 (уровень API 21) и более поздних версий._


## <a name="overview"></a>Обзор 

Один из лучших способов сохранения приложение Android в других — убедитесь, что сложных или длительных работа выполняется в фоновом режиме. Тем не менее очень важно, что фоновая работа не скажется негативно на удобство работы пользователя с устройством. 

Например фоновое задание может выполнять опрос веб-сайта каждые три или четыре минуты запроса для изменения для конкретного набора данных. Это кажется истинно, однако он будет иметь катастрофические последствия на батареи. Приложение несколько раз будет пробуждения устройства, повышать ЦП до более высокого состояния питания, питание радио, сетевые запросы, а затем обработки результатов. Она становится еще серьезнее, так как устройство не сразу выключить питание и будут возвращены в состояние бездействия низкого энергопотребления. Плохо запланированных фоновой работы могут случайно сохранить устройства в состоянии требованиям ненужное и излишнее power. Этот внешне безобидный действия (веб-сайт опроса) сделает устройства недоступными в относительно короткий период времени.

Android предоставляет следующие интерфейсы API для выполнения работы в фоновом режиме, но сами по себе они не являются достаточными для планирования intelligent заданий. 

* **[Службы Intent](~/android/app-fundamentals/services/creating-a-service/intent-services.md)**  &ndash; цель службы отлично подходят для выполнения работы, однако они не предоставляют способа для планирования работы.
* **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)**  &ndash; эти программные интерфейсы приложения позволяют только работу, чтобы запланировать, но не позволяют для фактического выполнения работы. Кроме того AlarmManager разрешает только ограничения на основе времени, это означает, что создано оповещение в определенное время или после истечения определенного периода времени. 
* **[Широковещательные приемники](~/android/app-fundamentals/broadcast-receivers.md)**  &ndash; приложение Android, можно настроить широковещательных получателей для выполнения работы в ответ на события уровня системы или намерения. Тем не менее широковещательных получателей не предоставляют контролировать выполнения задания. Также будет ограничить изменения в ОС Android при широковещательных получателей, или работать виды работ, который можно ответить. 

Существуют два ключевых компонента для эффективного выполнения фоновой работы (иногда называется _фоновое задание_ или _задания_):

1. **Интеллектуально планирования работы** &ndash; очень важно, что когда приложение выполняет работу в фоновом режиме, она делает это как соблюсти корректность. В идеальном случае приложения не должен требовать выполнение задания. Вместо этого приложение должно указать условия, которые должны быть выполнены для, когда задание можно запускать и запланировать выполнение задания с операционной системой, которое будет выполнять работу, при соблюдении условий. Это позволяет Android для запуска задания для обеспечения максимальной эффективности на устройстве. Например сетевых запросов может быть в пакетном режиме для запуска всех в то же время, чтобы обеспечивает максимальное использование издержки, связанные с сетью.
2. **Инкапсуляция работы** &ndash; код для выполнения фоновой работы должна быть включена в отдельный компонент, могут выполняться независимо от пользовательского интерфейса и будет сравнительно легко изменить расписание, если не удается завершить работу по некоторым причинам.

Планировщик заданий Android — это платформа, встроенная в ОС Android, предоставляющий fluent API для упрощения планирования фоновой работы.  Планировщик заданий Android состоит из следующих типов:

* `Android.App.Job.JobScheduler` — Это системная служба, которая используется для планирования, выполнения и при необходимости отменить, задания от имени приложения Android.
* `Android.App.Job.JobService` — Это абстрактный класс, который необходимо расширить с помощью логики, который будет выполняться задание в основном потоке приложения. Это означает, что `JobService` отвечает за как работа будет выполняться асинхронно.
* `Android.App.Job.JobInfo` Объект содержит условия, которым руководство по Android, если задание должно запускаться.

Для планирования работы с Android планировщик заданий, приложения Xamarin.Android необходимо инкапсулировать код в класс, расширяющий `JobService` класса. `JobService` имеет три метода жизненного цикла, который может быть вызван в течение времени существования задания:

* **bool (JobParameters параметров) OnStartJob** &ndash; этот метод вызывается `JobScheduler` для выполнения работы и выполняется в основном потоке приложения. Он отвечает за `JobService` для выполнения работы в асинхронном режиме и `true` остающуюся, если работу или `false` Если работу.
    
    Когда `JobScheduler` вызывает этот метод, он будет запрашивать и сохранять wakelock из Android в течение всего задания. После завершения задания он отвечает за `JobService` сообщить `JobScheduler` этого факта вызовом `JobFinished` (описывается далее).

* **JobFinished (параметры JobParameters, bool needsReschedule)** &ndash; этот метод должен вызываться `JobService` сообщить `JobScheduler` , работу. Если `JobFinished` не вызывается, `JobScheduler` не удалит wakelock, приводит к разряду батареи ненужные. 

* **bool (JobParameters параметров) OnStopJob** &ndash; вызывается, когда задание останавливается преждевременно системой Android. Он должен вернуть `true` Если задание должно быть перепланировано на основе критериев повторных попыток (ниже более подробно рассматривается).

Можно указать _ограничения_ или _триггеры_ , будет контролировать, когда задание можно или следует запустить. Например можно ограничить задание будет выполняться только при зарядки устройства или для запуска задания, если изображение извлекается.

В этом руководстве подробно расскажет способы реализации `JobService` класса и запланировать его с `JobScheduler`.

## <a name="requirements"></a>Требования

Планировщик заданий Android требуется Android API уровня 21 (Android 5.0) или более поздней версии. 

## <a name="using-the-android-job-scheduler"></a>С помощью планировщика заданий Android

С помощью Android JobScheduler API трех этапов:

1. Реализуйте JobService тип для инкапсуляции работы.
2. Используйте `JobInfo.Builder` создаваемого объекта `JobInfo` , содержащий критерии для `JobScheduler` для запуска задания. 
3. Запланировать задание с помощью `JobScheduler.Schedule`.

### <a name="implement-a-jobservice"></a>Реализуйте JobService

Вся работа, выполняемых библиотекой Android планировщик заданий, которые должны выполняться в тип, который расширяет `Android.App.Job.JobService` абстрактного класса. Создание `JobService` очень похоже на создание `Service` с помощью платформы Android: 

1. Расширить `JobService` класса.
2. Украшение подкласс с `ServiceAttribute` и задайте `Name` параметр в строку, состоящую из имени пакета и имя класса (см. ниже).
3. Задайте `Permission` свойство `ServiceAttribute` к строке `android.permission.BIND_JOB_SERVICE`.
4. Переопределить `OnStartJob` метод, добавив код для выполнения работы. Android будет вызывать этот метод в основном потоке приложения для запуска задания. Трудозатраты займет больше времени, что несколько миллисекунд должна выполняться в потоке, чтобы избежать блокировки приложения.
5. По завершении работы `JobService` необходимо вызвать `JobFinished` метод. Этот метод является как `JobService` сообщает `JobScheduler` выполняется работа. Сбой при вызове `JobFinished` приведет к `JobService` помещения ненужные требования на устройстве, Сокращение срока службы аккумулятора. 
6. Рекомендуется также переопределить `OnStopJob` метод. Этот метод вызывается системой Android, когда задание завершает работу, прежде чем его завершит и предоставляет `JobService` возможность правильно ликвидируйте все ресурсы. Этот метод должен возвращать `true` при необходимости изменить расписание задания, или `false` если он не является желательным, чтобы повторно запустить задание.
   

Ниже приведен пример простой `JobService` для приложения, используя TPL для асинхронного выполнения определенных действий:

```csharp
[Service(Name = "com.xamarin.samples.downloadscheduler.DownloadJob", 
         Permission = "android.permission.BIND_JOB_SERVICE")]
public class DownloadJob : JobService
{
    public override bool OnStartJob(JobParameters jobParams)
    {            
        Task.Run(() =>
        {
            // Work is happening asynchronously
                      
            // Have to tell the JobScheduler the work is done. 
            JobFinished(jobParams, false);
        });

        // Return true because of the asynchronous work
        return true;  
    }

    public override bool OnStopJob(JobParameters jobParams)
    {
        // we don't want to reschedule the job if it is stopped or cancelled.
        return false; 
    }
}
```

### <a name="creating-a-jobinfo-to-schedule-a-job"></a>Создание JobInfo планирование выполнения заданий

Не следует создавать экземпляры приложений Xamarin.Android `JobService` напрямую, вместо этого они будут передавать `JobInfo` объект `JobScheduler`. `JobScheduler` Будет создавать экземпляр запрошенного `JobService` объекта, планирования и запуска `JobService` в соответствии с метаданными `JobInfo`. Объект `JobInfo` объект должен содержать следующие сведения:

* **JobId** &ndash; это `int` значение, используемое для идентификации задания `JobScheduler`. Повторное использование этого значения будет обновлять все существующие задания. Значение должно быть уникальным для приложения. 
* **JobService** &ndash; этот параметр является `ComponentName` , явным образом определяющий тип, `JobScheduler` следует использовать для выполнения задания. 
  

Этот метод расширения демонстрирует создание `JobInfo.Builder` с Android `Context`, например, действия:

```csharp
public static class JobSchedulerHelpers
{
    public static JobInfo.Builder CreateJobBuilderUsingJobId<T>(this Context context, int jobId) where T:JobService
    {
        var javaClass = Java.Lang.Class.FromType(typeof(T));
        var componentName = new ComponentName(context, javaClass);
        return new JobInfo.Builder(jobId, componentName);
    }
}

// Sample usage - creates a JobBuilder for a DownloadJob andsets the Job ID to 1.
var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1);

var jobInfo = jobBuilder.Build();  // creats a JobInfo object.
```

Это мощная функция Android планировщик заданий является возможность контролировать, когда выполняется задание или условий задания могут выполняться. В следующей таблице описаны некоторые из методов на `JobInfo.Builder` , которые позволяют приложению влияют на время выполнения заданий:  


|  Метод | Описание   |
|---|---|
| `SetMinimumLatency`  | Выполнение задержки (в миллисекундах), необходимо соблюдать перед заданием. |
| `SetOverridingDeadline`  | Объявляет, что задание необходимо выполнить до истечения этого времени (в миллисекундах). |
| `SetRequiredNetworkType`  | Указывает требования к сети для задания. |
| `SetRequiresBatteryNotLow` | Задание может выполняться только в том случае, когда устройство не отображает предупреждение «низкого заряда батарей» для пользователя. |
| `SetRequiresCharging` | Задание может выполняться только в том случае, когда зарядки. |
| `SetDeviceIdle` | Задание выполняется в том случае, если устройство занято. |
| `SetPeriodic` | Указывает, что задание должно выполняться регулярно. |
| `SetPersisted` | Задание должно perisist сохранялся при перезагрузке устройства. | 


`SetBackoffCriteria` Даны некоторые рекомендации о том, как долго `JobScheduler` ожидания перед попыткой снова выполнить задание. Критерии отсрочки, состоит из двух частей: задержки в миллисекундах (значение по умолчанию 30 секунд) и тип отхода, которая должна использоваться (иногда называется _политики отсрочки_ или _политика повтора_) . Две политики, инкапсулируются в `Android.App.Job.BackoffPolicy` перечисления:

* `BackoffPolicy.Exponential` &ndash; Политику экспоненциального откладывания будет увеличиваться экспоненциально значение начальной задержки после каждой ошибки. При первом сбое задания библиотеки будет ожидать начальный интервал, который указывается перед Перепланирование задания — пример 30 секунд. Во второй раз в случае сбоя задания библиотеки будет ожидать по крайней мере 60 секунд, прежде чем пытаться запустить задание. После третьей неудачной попытки, библиотека ожидания 120 секунд и т. д. Это значение по умолчанию.
* `BackoffPolicy.Linear` &ndash; Эта стратегия является линейной задержки задания должен быть запланирован повторно для выполнения через заданные интервалы времени (до ее успешного выполнения). Линейный выдержкой лучше всего подходит для работы, которая должна быть выполнена как можно скорее или проблемы, которые быстро устранить самостоятельно. 

Дополнительные сведения о создании `JobInfo` объекта, см. в статье [документации Google для `JobInfo.Builder` класс](https://developer.android.com/reference/android/app/job/JobInfo.Builder.html).

#### <a name="passing-parameters-to-a-job-via-the-jobinfo"></a>Передача параметров задания с помощью JobInfo

Параметры передаются в задание, создав `PersistableBundle` , передается вместе с `Job.Builder.SetExtras` метод:

```csharp
var jobParameters = new PersistableBundle();
jobParameters.PutInt("LoopCount", 11);

var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1)
                     .SetExtras(jobParameters)
                     .Build();
```

`PersistableBundle` Осуществляется из `Android.App.Job.JobParameters.Extras` свойство в `OnStartJob` метод `JobService`:

```csharp
public override bool OnStartJob(JobParameters jobParameters)
{
    var loopCount = jobParams.Extras.GetInt("LoopCount", 10);
    
    // rest of code omitted
} 
```

### <a name="scheduling-a-job"></a>Планирование задания

Чтобы запланировать задание, приложения Xamarin.Android будет получить ссылку на `JobScheduler` системной службы и вызов `JobScheduler.Schedule` метод с `JobInfo` , созданного на предыдущем шаге. `JobScheduler.Schedule` немедленно вернет с одним из двух целочисленных значений:

* **JobScheduler.ResultSuccess** &ndash; задание был успешно запланировано. 
* **JobScheduler.ResultFailure** &ndash; не удалось запланировать задание. Обычно это вызвано конфликтующие `JobInfo` параметров.

Этот код является примером планирования задания и уведомления пользователя о результатах попытки планирования:

```csharp
var jobScheduler = (JobScheduler)GetSystemService(JobSchedulerService);
var scheduleResult = jobScheduler.Schedule(jobInfo);

if (JobScheduler.ResultSuccess == scheduleResult)
{
    Snackbar.Make(FindViewById(Android.Resource.Id.Content), Resource.String.jobscheduled_success, Snackbar.LengthShort);
}
else
{
    Snackbar.Make(FindViewById(Android.Resource.Id.Content), Resource.String.jobscheduled_failure, Snackbar.LengthShort);
}
```
 
### <a name="cancelling-a-job"></a>Отмена задания

Его невозможно отменить все задания, которые запланированы или просто одного задания с помощью `JobsScheduler.CancelAll()` метод или `JobScheduler.Cancel(jobId)` метод:

```csharp
// Cancel all jobs
jobSchduler.CancelAll(); 

// to cancel a job with jobID = 1
jobScheduler.Cancel(1)
```
  
## <a name="summary"></a>Сводка

В этом руководстве рассматриваются способы использования Android планировщика заданий для интеллектуально выполнения работы в фоновом режиме. Рассматривался инкапсулировать работа, которую можно выполнить как `JobService` и способы использования `JobScheduler` запланировать эту работу, указав критерии, согласно `JobTrigger` и способ обработки ошибок с `RetryStrategy`.

## <a name="related-links"></a>Связанные ссылки

- [Интеллектуальные планирования заданий](https://developer.android.com/topic/performance/scheduling.html)
- [Справочник по JobScheduler API](https://developer.android.com/reference/android/app/job/JobScheduler.html)
- [Планирование заданий, как профессионал, используя JobScheduler](https://medium.com/google-developers/scheduling-jobs-like-a-pro-with-jobscheduler-286ef8510129)
- [Android батареи и способы оптимизации памяти - 2016 Google ввода-вывода (видео)](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
- [Android JobScheduler - René Рупперт - Xamarin University](https://www.youtube.com/watch?v=aSjBBPYjelE)