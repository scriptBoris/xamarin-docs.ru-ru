---
title: Службы, запущенные с помощью Xamarin.Android
ms.prod: xamarin
ms.assetid: 8CC3A850-4CD2-4F93-98EE-AF3470794000
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 9e7dabf87314d87ab5ab335c220c0e292e56073b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110899"
---
# <a name="started-services-with-xamarinandroid"></a>Службы, запущенные с помощью Xamarin.Android

## <a name="started-services-overview"></a>Обзор работы служб

Службы, запущенные обычно выполняют единицу работы, не предоставляя прямого ответа или результаты клиенту. Пример единица работы — это служба, которая отправляет файл на сервер. Клиент будет сделать запрос к службе, необходимо отправить файл с устройства, на веб-сайт. Без подтверждения отправки файла (даже если приложения нет действий на переднем плане) и прекращать работу, когда Отправка завершится. Важно понимать, что запущена служба будет выполняться в потоке пользовательского интерфейса приложения. Это означает, что если служба для выполнения работы, который будет блокировать поток пользовательского интерфейса, его необходимо создать и dispose потоков при необходимости.

В отличие от привязки службы нет не коммуникационный канал между «чистые» работы службы и ее клиентами. Это означает, что запущена служба будет реализовать некоторые методы другом жизненном цикле, чем привязанную службу. В следующем списке перечислены распространенные методы жизненного цикла в службы:

* `OnCreate` &ndash; Вызывается один раз, когда служба запускается первый раз. Это происходит, когда код инициализации должен быть реализован.
* `OnBind` &ndash; Этот метод должен быть реализован все классы служб, однако обычно работы службы нет клиента, привязанным к нему. По этой причине служба, запускаемая просто возвращает `null`. Напротив, имеет это гибридная служба (которая представляет собой сочетание привязанной службы и службы) для реализации и возвращать `Binder` для клиента.
* `OnStartCommand` &ndash; Вызывается для каждого запроса запустить службу, либо в ответ на вызов `StartService` или перезагрузка системы. Это место служба начнет любой длительную задачу. Этот метод возвращает `StartCommandResult` значение, указывающее, каким образом или если система должна обрабатывать перезапуск службы после завершения работы из-за нехватки памяти. Этот вызов выполняется в основном потоке. Этот метод описан более подробно ниже.
* `OnDestroy` &ndash; Этот метод вызывается при уничтожении службы. Он используется для выполнения любой конечный требуется очистка.

Важным методом для работы службы является `OnStartCommand` метод. Он будет вызываться каждый раз служба получает запрос на выполнение действий. В следующем фрагменте кода приведен пример `OnStartCommand`: 

```csharp
public override StartCommandResult OnStartCommand (Android.Content.Intent intent, StartCommandFlags flags, int startId)
{
    // This method executes on the main thread of the application.
    Log.Debug ("DemoService", "DemoService started");
    ...
    return StartCommandResult.Sticky;
}
```

Первый параметр является `Intent` объект, содержащий метаданные о работе, для выполнения. Второй параметр содержит `StartCommandFlags` значение, которое предоставляет определенную информацию о вызове метода. Этот параметр имеет одно из двух возможных значений.

* `StartCommandFlag.Redelivery` &ndash; Это означает, что `Intent` re-поставкой предыдущей `Intent`. Данное значение предоставляется в том случае, когда служба вернула `StartCommandResult.RedeliverIntent` , но было остановлено, прежде чем удалось правильно завершить ее работу.
* `StartCommandFlag.Retry` &dash; Это значение поступает в том случае, если предыдущего `OnStartCommand` сбой вызова и пытается запустить службу еще раз с тем же намерением как предыдущей неудачной попыткой Android.
 
Наконец третий параметр — это целочисленное значение, являющееся уникальным для приложения, которое идентифицирует запрос. Вполне возможно, что несколько вызывающие объекты могут вызывать тот же объект службы. Это значение используется, чтобы связать запрос на остановку службы с помощью указанного запроса для запуска службы. Обсуждаются более подробно в разделе [остановка службы](#Stopping_the_Service). 

Значение `StartCommandResult` возвращается службой как предложение Android о том, что делать, если служба удаляется из-за нехватки ресурсов. Существует три возможных значения для `StartCommandResult`:

* **[StartCommandResult.NotSticky](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.NotSticky/)**  &ndash; это значение говорит, Android, нет необходимости перезапустить службу, он завершен. Как пример рассмотрим службу, которая создает эскизы для работы с коллекцией в приложении. Если службы завершен, это не так важны для повторного создания эскиза немедленно &ndash; эскиза можно создать заново при следующем запуске приложения.
* **[StartCommandResult.Sticky](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.Sticky/)**  &ndash; это означает, что Android для перезапуска службы, но не предоставлять последние рассчитывает, что служба запущена. Если нет ожидающих способы для обработки, то `null` будет предоставляться для параметра намерений. Примером этого может быть приложении проигрывателя музыки; Служба будет перезапущена Готово для воспроизведения музыки, но он будет воспроизвести последний. 
* **[StartCommandResult.RedeliverIntent](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.RedeliverIntent/)**  &ndash; это значение будет передать в Android, чтобы перезапустить службу и повторно предоставлять последние `Intent`. Пример этого — это служба, который загружает файл данных для приложения. Если службы завершен, файл данных по-прежнему необходимо загрузить. Путем возвращения `StartCommandResult.RedeliverIntent`, когда Android перезапуск службы, здесь также описываются намерение (которая содержит URL-адрес файла для скачивания) к службе. Это позволит загрузки перезапустить или возобновить (в зависимости от конкретная реализация кода).

Имеется четвертое значение для `StartCommandResult` &ndash; `StartCommandResult.ContinuationMask`. Это значение возвращается по `OnStartCommand` , и показывается, как Android будет продолжения работы службы он остановлен. Это значение обычно не используется для запуска службы.

На этой схеме показаны события ключа жизненного цикла службы: 

![Схема, демонстрирующая порядок, в котором вызываются методы жизненного цикла](started-services-images/started-service-01.png "схема, демонстрирующая порядок, в котором вызываются методы жизненного цикла.")


<a name="Stopping_the_Service" />

## <a name="stopping-the-service"></a>Остановка службы

Служба, запускаемая будут продолжать работать бесконечно; Android будут сохранены в службы, запущенной до тех пор, пока имеется достаточный объем системных ресурсов. Возможно, клиент необходимо остановить службу или служба может остановиться при завершении работы. Чтобы остановить службу двумя способами: 
 
* **[Android.Content.Context.StopService()](https://developer.xamarin.com/api/member/Android.Content.Context.StopService/p/Android.Content.Intent/)**  &ndash; может запрашиваться клиентом (например, действие), остановите службу, вызов `StopService` метод: 

    ```csharp
    StopService(new Intent(this, typeof(DemoService));
    ```

* **[Android.App.Service.StopSelf()](https://developer.xamarin.com/api/member/Android.App.Service.StopSelf()/)**  &ndash; услуга может завершает работу, вызвав `StopSelf`:

    ```csharp
    StopSelf();
    ```
    
### <a name="using-startid-to-stop-a-service"></a>С помощью startId, чтобы остановить службу

Несколько вызывающие объекты могут запрашивать запуска службы. Если запрос ожидающих запуска, служба может использовать `startId` , передаваемый в `OnStartCommand` для предотвращения преждевременно остановки службы. `startId` Будет соответствовать последней вызов `StartService`и увеличивается каждый раз, он вызывается. Таким образом Если последующие запросы к `StartService` еще не завершилась в вызове `OnStartCommand`, служба может вызывать `StopSelfResult`, передав его последнее значение `startId` она получила (вместо того чтобы просто вызывать `StopSelf`). Если в вызове `StartService` не еще привела к соответствующим вызовом метода `OnStartCommand`, система не останавливает службу, так как `startId` используется в `StopSelf` вызов не будет соответствовать до последней версии `StartService` вызова.


## <a name="related-links"></a>Связанные ссылки

- [StartedServicesDemo (пример)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/StartedServicesDemo/)
- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service)
- [Android.App.StartCommandFlags](https://developer.xamarin.com/api/type/Android.App.StartCommandFlags)
- [Android.App.StartCommandResult](https://developer.xamarin.com/api/type/Android.App.StartCommandResult)
- [Android.Content.BroadcastReceiver](https://developer.xamarin.com/api/type/Android.Content.BroadcastReceiver/)
- [Android.Content.Intent](https://developer.xamarin.com/api/type/Android.Content.Intent)
- [Android.OS.Handler](https://developer.xamarin.com/api/type/Android.OS.Handler/)
- [Android.Widget.Toast](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
- [Значки в строке состояния](http://developer.android.com/guide/practices/ui_guidelines/icon_design_status_bar.html)
