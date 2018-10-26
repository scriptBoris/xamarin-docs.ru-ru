---
title: Диспетчер заданий firebase
description: В этом руководстве описывается планирование фоновой работы, с помощью библиотеки диспетчер заданий Firebase от Google.
ms.prod: xamarin
ms.assetid: 3DB9C7A3-D351-481D-90C5-BEC25D1B9910
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/05/2018
ms.openlocfilehash: 4ae1fb71209f8116b17ee7e2cb44318ef790d831
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116182"
---
# <a name="firebase-job-dispatcher"></a>Диспетчер заданий firebase

_В этом руководстве описывается планирование фоновой работы, с помощью библиотеки диспетчер заданий Firebase от Google._


## <a name="overview"></a>Обзор

Один из лучших способов сохранения приложение Android в других — убедитесь, что сложных или длительных работа выполняется в фоновом режиме. Тем не менее очень важно, что фоновая работа не скажется негативно на удобство работы пользователя с устройством. 

Например фоновое задание может выполнять опрос веб-сайта каждые три или четыре минуты запроса для изменения для конкретного набора данных. Это кажется истинно, однако он будет иметь катастрофические последствия на батареи. Приложение несколько раз будет пробуждения устройства, повышать ЦП до более высокого состояния питания, питание радио, сетевые запросы, а затем обработки результатов. Она становится еще серьезнее, так как устройство не сразу выключить питание и будут возвращены в состояние бездействия низкого энергопотребления. Плохо запланированных фоновой работы могут случайно сохранить устройства в состоянии требованиям ненужное и излишнее power. Этот внешне безобидный действия (веб-сайт опроса) сделает устройства недоступными в относительно короткий период времени.

Android предоставляет следующие интерфейсы API для выполнения работы в фоновом режиме, но сами по себе они не являются достаточными для планирования intelligent заданий. 

* **[Службы Intent](~/android/app-fundamentals/services/creating-a-service/intent-services.md)**  &ndash; цель службы отлично подходят для выполнения работы, однако они не предоставляют способа для планирования работы.
* **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)**  &ndash; эти программные интерфейсы приложения позволяют только работу, чтобы запланировать, но не позволяют для фактического выполнения работы. Кроме того AlarmManager разрешает только ограничения на основе времени, это означает, что создано оповещение в определенное время или после истечения определенного периода времени. 
* **[JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)**  &ndash; JobSchedule — это отличный API, который работает с операционной системой для планирования заданий. Тем не менее это только для этих приложений для Android, целевой уровень API 21 или более поздней версии. 
* **[Широковещательные приемники](~/android/app-fundamentals/broadcast-receivers.md)**  &ndash; приложение Android, можно настроить широковещательных получателей для выполнения работы в ответ на события уровня системы или намерения. Тем не менее широковещательных получателей не предоставляют контролировать выполнения задания. Также будет ограничить изменения в ОС Android при широковещательных получателей, или работать виды работ, который можно ответить. 

Существуют два ключевых компонента для эффективного выполнения фоновой работы (иногда называется _фоновое задание_ или _задания_):

1. **Интеллектуально планирования работы** &ndash; очень важно, что когда приложение выполняет работу в фоновом режиме, она делает это как соблюсти корректность. В идеальном случае приложения не должен требовать выполнение задания. Вместо этого приложение должно указать условия, которые должны быть выполнены для, когда задание можно запустить и запланировать выполнение этой работы, который будет выполняться при соблюдении условий. Это позволяет Android для интеллектуально выполнения работы. Например сетевых запросов может быть в пакетном режиме для запуска всех в то же время, чтобы обеспечивает максимальное использование издержки, связанные с сетью.
2. **Инкапсуляция работы** &ndash; код для выполнения фоновой работы должна быть включена в отдельный компонент, могут выполняться независимо от пользовательского интерфейса и будет сравнительно легко изменить расписание, если не удается завершить работу по некоторым причинам.

Диспетчер заданий Firebase — это библиотека, от Google, который предоставляет fluent API для упрощения планирования фоновой работы. Он предназначен для замены для диспетчера Google Cloud. Диспетчер заданий Firebase состоит из следующих API-интерфейсов:

* Объект `Firebase.JobDispatcher.JobService` — это абстрактный класс, который необходимо расширить с помощью логики, который будет выполняться в фоновом задании.
* Объект `Firebase.JobDispatcher.JobTrigger` объявляет начала задания. Это обычно выражается в виде окна времени, например, подождите по крайней мере 30 секунд перед запуском задания, но выполнение задания в течение пяти минут.
* Объект `Firebase.JobDispatcher.RetryStrategy` содержит сведения о какие действия должны выполняться, когда задание не может выполняться правильно. Стратегия повторов определяет, как долго ждать, прежде чем пытаться повторно запустить задание. 
* Объект `Firebase.JobDispatcher.Constraint` — это необязательное значение, которое описывает условие, которое должны быть выполнены перед запуском задания, такие как устройство находится в сети, unmetered или взимать плату.
* `Firebase.JobDispatcher.Job` — Это API, который объединяет в предыдущих API на единицу работы, можно запланировать с `JobDispatcher`. `Job.Builder` Класс используется для создания экземпляра `Job`.
* Объект `Firebasee.JobDispatcher.JobDispatcher` использует предыдущие три API-интерфейсы для планирования работы с операционной системой, так и для предоставления способа отменить задания, при необходимости.

Для планирования работы с диспетчер заданий Firebase, приложения Xamarin.Android необходимо инкапсулировать код в тип, который расширяет `JobService` класса. `JobService` имеет три метода жизненного цикла, который может быть вызван в течение времени существования задания:

* **`bool OnStartJob(IJobParameters parameters)`** &ndash; Этот метод является, где будет выполняться работа и всегда должен быть реализован. Он выполняется в основном потоке. Этот метод будет возвращать `true` остающуюся, если работу или `false` Если работу. 
* **`bool OnStopJob(IJobParameters parameters)`** &ndash; Вызывается, когда задание остановлено по некоторым причинам. Он должен вернуть `true` Если задание должно быть перепланировано на более поздний срок.
* **`JobFinished(IJobParameters parameters, bool needsReschedule)`** &ndash; Этот метод вызывается, когда `JobService` завершения либо асинхронную работу. 

Чтобы запланировать задание, будет создан экземпляр приложения `JobDispatcher` объекта. Затем `Job.Builder` используется для создания `Job` объект, который предоставляется `JobDispatcher` чего попробуйте и запланировать запуск задания.

В этом руководстве описывается, как добавить диспетчер заданий Firebase в приложение Xamarin.Android и использовать его для планирования работы в фоновом режиме.

## <a name="requirements"></a>Требования

Диспетчер заданий Firebase требуется Android API уровня 9 или более поздней версии. Диспетчер заданий Firebase библиотеки зависит от некоторых компонентов, предоставляемые службы Google Play; на устройстве должен быть установлен сервисы Google Play.

## <a name="using-the-firebase-job-dispatcher-library-in-xamarinandroid"></a>С помощью библиотеки диспетчер заданий Firebase в Xamarin.Android

Чтобы приступить к работе с диспетчер заданий Firebase, сначала добавьте [пакет Xamarin.Firebase.JobDispatcher NuGet](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher) в проект Xamarin.Android. Диспетчер пакетов NuGet для поиска **Xamarin.Firebase.JobDispatcher** пакет (который все еще находится в предварительной версии).

После добавления библиотеки диспетчер заданий Firebase, создайте `JobService` класса и запланировать его запуск с помощью экземпляра `FirebaseJobDispatcher`.


### <a name="creating-a-jobservice"></a>Создание JobService

Вся работа, выполняемых библиотекой диспетчер заданий Firebase должно производиться в тип, который расширяет `Firebase.JobDispatcher.JobService` абстрактного класса. Создание `JobService` очень похоже на создание `Service` с помощью платформы Android: 

1. Расширить `JobService` класса
2. Украшение подкласс с `ServiceAttribute`. Несмотря на то, что не обязательно, рекомендуется явно задавать `Name` параметр, чтобы помочь с отладкой `JobService`. 
3. Добавить `IntentFilter` для объявления `JobService` в **AndroidManifest.xml**. Это также поможет библиотеке диспетчер заданий Firebase найдите и вызвать `JobService`.

Ниже приведен пример простой `JobService` для приложения, используя TPL для асинхронного выполнения определенных действий:

```csharp
[Service(Name = "com.xamarin.fjdtestapp.DemoJob")]
[IntentFilter(new[] {FirebaseJobServiceIntent.Action})]
public class DemoJob : JobService
{
    static readonly string TAG = "X:DemoService";

    public override bool OnStartJob(IJobParameters jobParameters)
    {
        Task.Run(() =>
        {
            // Work is happening asynchronously (code omitted)
                       
        });

        // Return true because of the asynchronous work
        return true;  
    }

    public override bool OnStopJob(IJobParameters jobParameters)
    {
        Log.Debug(TAG, "DemoJob::OnStartJob");
        // nothing to do.
        return false;
    }
}
```

### <a name="creating-a-firebasejobdispatcher"></a>Создание FirebaseJobDispatcher

Прежде чем какой-либо работы можно запланировать, это необходимо для создания `Firebase.JobDispatcher.FirebaseJobDispatcher` объекта. `FirebaseJobDispatcher` Отвечает за планирование `JobService`. В следующем фрагменте кода является одним из способов создания экземпляра `FirebaseJobDispatcher`: 
 
 ```csharp
// This is the "Java" way to create a FirebaseJobDispatcher object
IDriver driver = new GooglePlayDriver(context);
FirebaseJobDispatcher dispatcher = new FirebaseJobDispatcher(driver);
```

В предыдущем фрагменте кода `GooglePlayDriver` является классом, который помогает `FirebaseJobDispatcher` взаимодействовать с некоторыми планирования API в сервисы Google Play на устройстве. Параметр `context` является любой Android `Context`, такие как действие. В настоящее время `GooglePlayDriver` является единственным `IDriver` реализация в библиотеке диспетчер заданий Firebase. 

Привязка Xamarin.Android диспетчер заданий Firebase предоставляет метод расширения для создания `FirebaseJobDispatcher` из `Context`: 

```csharp
FirebaseJobDispatcher dispatcher = context.CreateJobDispatcher();
```

Один раз `FirebaseJobDispatcher` был создан, его можно создать `Job` и запустить код в `JobService` класса. `Job` Создается путем `Job.Builder` объекта и обсуждаются в следующем разделе.

### <a name="creating-a-firebasejobdispatcherjob-with-the-jobbuilder"></a>Создание Firebase.JobDispatcher.Job с Job.Builder

`Firebase.JobDispatcher.Job` Класс отвечает за инкапсуляцию метаданных необходимо запускать `JobService`. Объект`Job` содержит сведения, такие как все ограничения, которое должно быть выполнено, прежде чем задание может выполняться, если `Job` повторяется, или какие-либо триггеры, которые вызовут выполнение задания.  Как минимум `Job` должен иметь _тега_ (уникальная строка, определяющая задания `FirebaseJobDispatcher`) и тип `JobService` должен выполняться. Будет создавать диспетчер заданий Firebase `JobService` когда пришло время для выполнения задания.  Объект `Job` создается с помощью экземпляра `Firebase.JobDispatcher.Job.JobBuilder` класса. 

В следующем фрагменте кода — это самый простой пример создания `Job` с использованием привязки Xamarin.Android:

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .Build();
```

`Job.Builder` Выполняет ряд проверок базовую проверку входных значений для задания. Будет создано исключение, если не симметричных `Job.Builder` для создания `Job`.  `Job.Builder` Создаст `Job` с следующие значения по умолчанию:

* Объект `Job` _время существования_ (как долго он будет выполняться для запуска) — только в том случае, пока устройство перезагрузится &ndash; после перезагрузки устройства `Job` теряется.
* Объект `Job` не повторяется &ndash; он будет работать только один раз.
* Объект `Job` будет запланирована для запуска как можно скорее.
* Стратегия повторов по умолчанию для `Job` заключается в использовании _экспоненциального откладывания_ (описано на более подробно ниже в разделе [параметр RetryStrategy](#Setting_a_RetryStrategy))

### <a name="scheduling-a-job"></a>Планирование задания

После создания `Job`, его необходимо запланировать с `FirebaseJobDispatcher` перед его запуском. Существует два способа планирования `Job`:

```csharp
// This will throw an exception if there was a problem scheduling the job
dispatcher.MustSchedule(myJob);

// This method will not throw an exception; an integer result value is returned
int scheduleResult = dispatcher.Schedule(myJob);
```

Значение, возвращенное `FirebaseJobDispatcher.Schedule` будет иметь одно из следующих целочисленных значений:

* `FirebaseJobDispatcher.ScheduleResultSuccess` &ndash; `Job` Успешно запланировано.
* `FirebaseJobDispatcher.ScheduleResultUnknownError` &ndash; Возникла неизвестная проблема, которая не позволяла `Job` выигрывают от планирования.
* `FirebaseJobDispatcher.ScheduleResultNoDriverAvailable` &ndash; Указан недопустимый `IDriver` использовался или `IDriver` каким-либо образом была недоступна. 
* `FirebaseJobDispatcher.ScheduleResultUnsupportedTrigger` &ndash; `Trigger` Не поддерживается.
* `FirebaseJobDispatcher.ScheduleResultBadService` &ndash; Служба неправильно настроена или недоступна.
 
### <a name="configuring-a-job"></a>Настройка задания

Это можно настроить задание. Следующие примеры как можно настроить задание:

* [Передача параметров задания](#Passing_Parameters_to_a_Job) &ndash; объект `Job` может потребоваться дополнительные значения для выполнения работы, например загрузки файла.
* [Задать ограничения](#Setting_Constraints) &ndash; может потребоваться запускать задание только при соблюдении определенных условий. Например, запускать только `Job` при зарядки устройства. 
* [Укажите, когда `Job` следует запускать](#Setting_Job_Triggers) &ndash; диспетчер заданий Firebase позволяет приложениям указывать время, когда должно выполняться задание.  
* [Объявите стратегию повторов для заданий с ошибками](#Setting_a_RetryStrategy) &ndash; объект _стратегию повторов_ содержатся рекомендации, которые `FirebaseJobDispatcher` на что следует делать с `Jobs` , не удается завершить. 

В каждом из этих разделов будут рассмотрены более в следующих разделах.

<a name="Passing_Parameters_to_a_Job" />

#### <a name="passing-jarameters-to-a-job"></a>Передача jarameters к заданию

Параметры передаются в задание, создав `Bundle` , передается вместе с `Job.Builder.SetExtras` метод:

```csharp
Bundle jobParameters = new Bundle();
jobParameters.PutInt(FibonacciCalculatorJob.FibonacciPositionKey, 25);

Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetExtras(jobParameters)
                      .Build();

```

`Bundle` Осуществляется из `IJobParameters.Extras` свойство `OnStartJob` метод:

```csharp
public override bool OnStartJob(IJobParameters jobParameters)
{
    int position = jobParameters.Extras.GetInt(FibonacciPositionKey, DEFAULT_VALUE);
    
    // rest of code omitted
} 
```

<a name="Setting_Constraints" />

#### <a name="setting-constraints"></a>Настройка ограничений

Ограничения могут помочь уменьшает стока затрат или аккумулятора на устройстве. `Firebase.JobDispatcher.Constraint` Класс определяет эти ограничения к целочисленным значениям:

* `Constraint.OnUnmeteredNetwork` &ndash; Запустите задание только в том случае, когда устройство подключено к сети unmetered. Это полезно для предотвращения расходов данных пользователя.
* `Constraint.OnAnyNetwork` &ndash; Запустите задание в любой сети, устройство подключено к. Если указан вместе с `Constraint.OnUnmeteredNetwork`, это значение будет иметь приоритет.
* `Constraint.DeviceCharging` &ndash; Запустите задание, только в том случае, если зарядки устройства.

Ограничения задаются с помощью `Job.Builder.SetConstraint` метод: 

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetConstraint(Constraint.DeviceCharging)
                      .Build();
```

<a name="Setting_Job_Triggers" />

`JobTrigger` Руководство в операционную систему о время начала задания. Объект `JobTrigger` имеет _выполнение окно_ , определяющий запланированное время, когда `Job` следует запустить. Окно выполнения имеет _запустить окно_ значение и _окончания окна_ значение. Окна запуска — это количество секунд ожидания перед запуском задания устройства и конечное значение окно — максимальное число секунд ожидания перед запуском `Job`. 

Объект `JobTrigger` могут создаваться с помощью `Firebase.Jobdispatcher.Trigger.ExecutionWindow` метод.  Например `Trigger.ExecutionWindow(15,60)` означает, что задание должно выполняться от 15 до 60 секунд, из когда запланировано. `Job.Builder.SetTrigger` Метод используется для 

```csharp
JobTrigger myTrigger = Trigger.ExecutionWindow(15,60);
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetTrigger(myTrigger)
                      .Build();
```

Значение по умолчанию `JobTrigger` для задания, представленный значением `Trigger.Now`, которое указывает, что задание выполняться как можно скорее после плановой...

<a name="Setting_a_RetryStrategy" />

#### <a name="setting-a-retrystrategy"></a>Параметр RetryStrategy

`Firebase.JobDispatcher.RetryStrategy` Используется для указания, какая часть задержки устройство следует использовать перед попыткой повторно запускать задание, завершившееся сбоем. Объект `RetryStrategy` имеет _политики_, который определяет, какой алгоритм базовое время будет использоваться для повторного планирования невыполненное задание и окно выполнения, указывающее окно, в котором следует планировать задания. Это _перепланирования окно_ определяется двумя значениями. Первое значение — количество секунд ожидания перед Перепланирование задания ( _начальной задержки_ значение), а второе число — максимальное количество секунд, прежде чем задание должно выполняться ( _Максимальная задержка_значение). 
 
Два типа политик повтора идентифицируются этих значений типа int:

* `RetryStrategy.RetryPolicyExponential` &ndash; _Экспоненциального откладывания_ политики экспоненциально увеличиваться значение начальной задержки после каждой ошибки. Первый раз, в случае сбоя задания библиотеки будет ожидать _начальное интервала, который указывается перед Перепланирование задания &ndash; пример 30 секунд. Во второй раз в случае сбоя задания библиотеки будет ожидать по крайней мере 60 секунд, прежде чем пытаться запустить задание. После третьей неудачной попытки, библиотека ожидания 120 секунд и т. д. Значение по умолчанию `RetryStrategy` диспетчер заданий Firebase библиотеки представляются `RetryStrategy.DefaultExponential` объекта. Она имеет начальной задержки 30 секунд и Максимальная задержка 3600 секунд.
* `RetryStrategy.RetryPolicyLinear` &ndash; Эта стратегия _линейной задержки_ что задания должны быть перепланировано на через установленные интервалы (до ее успешного выполнения). Линейный выдержкой лучше всего подходит для работы, которая должна быть выполнена как можно скорее или проблемы, которые быстро устранить самостоятельно. Диспетчер заданий Firebase библиотека определяет `RetryStrategy.DefaultLinear` которого имеет перепланирования окна по крайней мере 30 секунд до 3600 секунд.

Можно определить пользовательские `RetryStrategy` с `FirebaseJobDispatcher.NewRetryStrategy` метод. Он принимает три параметра:

1. `int policy` &ndash; _Политики_ является одним из предыдущего `RetryStrategy` значения, `RetryStrategy.RetryPolicyLinear`, или `RetryStrategy.RetryPolicyExponential`.
2. `int intialBackoffSeconds` &ndash; _Начальной задержки_ задержка, в секундах, которая необходима прежде чем пытаться повторно запустить задание. Это значение по умолчанию составляет 30 секунд. 
3. `int maximumBackoffSeconds` &ndash; _Максимальная задержка_ значение объявляет максимальное число секунд задержки прежде чем пытаться повторно запустить задание. Значение по умолчанию составляет 3600 секунд. 

```csharp
RetryStrategy retry = dispatcher.NewRetryStrategy(RetryStrategy.RetryPolicyLinear, initialBackoffSeconds, maximumBackoffSet);

// Create a Job and set the RetryStrategy via the Job.Builder
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetRetryStrategy(retry)
                      .Build();
```

### <a name="cancelling-a-job"></a>Отмена задания

Его невозможно отменить все задания, которые запланированы или просто одного задания с помощью `FirebaseJobDispatcher.CancelAll()` метод или `FirebaseJobDispatcher.Cancel(string)` метод:

```csharp
int cancelResult = dispatcher.CancelAll(); 

// to cancel a single job:

int cancelResult = dispatcher.Cancel("unique-tag-for-job");
```

Каждый из этих методов возвращает значение типа integer:

* `FirebaseJobDispatcher.CancelResultSuccess` &ndash; Задание было успешно отменено.
* `FirebaseJobDispatcher.CancelResultUnknownError` &ndash; Ошибка не позволила выполняется Отмена задания.
* `FirebaseJobDispatcher.CancelResult.NoDriverAvailable` &ndash; `FirebaseJobDispatcher` Не удалось отменить задание, так как отсутствует `IDriver` доступны.

## <a name="summary"></a>Сводка

В этом руководстве описывается, как использовать диспетчер заданий Firebase для интеллектуально выполнения работы в фоновом режиме. Рассматривался инкапсулировать работа, которую можно выполнить как `JobService` и способы использования `FirebaseJobDispatcher` запланировать эту работу, указав критерии, согласно `JobTrigger` и способ обработки ошибок с `RetryStrategy`.


## <a name="related-links"></a>Связанные ссылки

- [Xamarin.Firebase.JobDispatcher в NuGet](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher)
- [Диспетчер заданий firebase-на сайте GitHub](https://github.com/firebase/firebase-jobdispatcher-android)
- [Xamarin.Firebase.JobDispatcher привязки](https://github.com/xamarin/XamarinComponents/tree/master/Android/FirebaseJobDispatcher)
- [Интеллектуальные планирования заданий](https://developer.android.com/topic/performance/scheduling.html)
- [Android батареи и способы оптимизации памяти - 2016 Google ввода-вывода (видео)](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
