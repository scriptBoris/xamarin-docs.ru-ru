---
title: Службы, запущенные с Xamarin.Android
ms.prod: xamarin
ms.assetid: 8CC3A850-4CD2-4F93-98EE-AF3470794000
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 02/16/2018
ms.openlocfilehash: c0aeeaad3798dd840e69c6da6d7857298f4da3c1
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30767470"
---
# <a name="started-services-with-xamarinandroid"></a>Службы, запущенные с Xamarin.Android

## <a name="started-services-overview"></a>Обзор работы служб

Службы, запущенные обычно выполняются единица работы без предоставления любого отзывов или результаты клиенту. Пример единица работы — это служба, которая отправляет файл на сервер. Клиент будет сделать запрос к службе для отправки файла на веб-сайт с устройства. Службы без вмешательства пользователя отправить файл, (даже если приложение не содержит мероприятий на переднем плане) и завершиться после завершения передачи. Очень важно помнить, что запущена служба будет выполняться в потоке пользовательского интерфейса приложения. Это означает, что если служба будет выполнять работу, которая будет блокировать поток пользовательского интерфейса, его необходимо создать и dispose потоков при необходимости.

В отличие от привязанной службы нет не канал связи между «чистые» запущенной службой и клиентами. Это означает, что служба, запускаемая будет реализует ряд методов разных жизненного цикла чем привязанной службы. В следующем списке перечислены общие методы жизненного цикла в запущенной службе:

* `OnCreate` &ndash; Вызывается один раз, при первом запуске службы. Это происходит, где должен быть реализован код инициализации.
* `OnBind` &ndash; Этот метод должен быть реализован классами, все службы, однако обычно служба, запускаемая нет клиента привязанным к нему. По этой причине служба, запускаемая просто возвращает `null`. Напротив, имеет гибридной службы (которая является сочетание привязанной службы и служба, запускаемая) для реализации и возвращать `Binder` для клиента.
* `OnStartCommand` &ndash; Вызывается для каждого запроса запустить службу, либо в ответ на вызов `StartService` или перезагрузка системы. Это начинаются любой длительную задачу службы. Метод возвращает `StartCommandResult` значение, указывающее, каким образом или если система должна обрабатывать перезапуск службы после завершения работы из-за нехватки памяти. Этот вызов выполняется в основном потоке. Этот метод является более подробно ниже.
* `OnDestroy` &ndash; Этот метод вызывается при уничтожении службы. Он используется для выполнения любой конечный очистку необходимых.

Является важным методом для работы службы `OnStartCommand` метод. Он будет вызываться каждый раз, служба получает запрос, чтобы выполнить ряд действий. В следующем фрагменте кода приведен пример `OnStartCommand`: 

```csharp
public override StartCommandResult OnStartCommand (Android.Content.Intent intent, StartCommandFlags flags, int startId)
{
    // This method executes on the main thread of the application.
    Log.Debug ("DemoService", "DemoService started");
    ...
    return StartCommandResult.Sticky;
}
```

Первый параметр является `Intent` объект, содержащий метаданные о выполнять работу. Второй параметр содержит `StartCommandFlags` значение, которое предоставляет определенную информацию о вызове метода. Этот параметр может иметь одно из двух возможных значений:

* `StartCommandFlag.Redelivery` &ndash; Это означает, что `Intent` является повторной доставке предыдущей `Intent`. Это значение используется, если служба вернула `StartCommandResult.RedeliverIntent` , но было остановлено, прежде чем удалось правильно завершить ее работу.
* `StartCommandFlag.Retry` &dash; Это значение получается при отправке предыдущего `OnStartCommand` сбой вызова и Android попытка повторного запуска службы с одной целью как предыдущих неудавшихся попыток.
 
Наконец третий параметр — целочисленное значение, являющееся уникальным для приложения, которое определяет запрос. Это возможно, несколько вызывающим объектам может вызова одного объекта службы. Это значение используется для связи на запрос остановки службы с помощью указанного запроса для запуска службы. Обсуждаются более подробно в разделе [остановка службы](#Stopping_the_Service). 

Значение `StartCommandResult` возвращается службой как предложение Android о том, что делать, если служба удаляется из-за нехватки ресурсов. Существует три возможных значения для `StartCommandResult`:

* **[StartCommandResult.NotSticky](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.NotSticky/)**  &ndash; это значение говорит, Android, нет необходимости перезапустить службу, он завершен. В качестве примера это рассмотрим службу, которая создает эскизов для коллекции в приложении. Если служба завершен, это не так важны для повторного создания эскиза немедленно &ndash; эскиз можно создать заново при следующем запуске приложения.
* **[StartCommandResult.Sticky](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.Sticky/)**  &ndash; сообщает Android для перезапуска службы, но не для доставки назначение последнего запуска службы. Если нет ожидающих способы для обработки, а затем `null` будет предоставляться для параметра намерения. Примером этого может быть в приложение проигрывателя музыки; Служба будет перезапущена готовый послушать музыку, но воспроизведение последнего песню. 
* **[StartCommandResult.RedeliverIntent](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.RedeliverIntent/)**  &ndash; это значение будет сообщить Android для перезапуска службы и повторно доставки последней `Intent`. Примером этого является служба, которая загружает файл данных для приложения. Если служба завершен, файл данных по-прежнему необходимо загрузить. Путем возвращения `StartCommandResult.RedeliverIntent`при Android перезапускает службу, он также предоставляет цель (который содержит URL-адрес для загрузки файла) к службе. Это позволит загрузки перезапустить или возобновить (в зависимости от конкретная реализация кода).

Отсутствует значение четвертый `StartCommandResult` &ndash; `StartCommandResult.ContinuationMask`. Это значение возвращается по `OnStartCommand` и описывает, как Android будет продолжить работу службы он завершен. Это значение обычно не используется для запуска службы.

На этой диаграмме показаны события жизненного цикла ключа работы службы: 

![Схема, показывающая порядок вызова методов жизненного цикла](started-services-images/started-service-01.png "схема, показывающая порядок вызова методов жизненного цикла.")


<a name="Stopping_the_Service" />

## <a name="stopping-the-service"></a>Остановка службы

Служба, запускаемая будут продолжать работать бесконечно; Android будет хранить запущенной службе при условии, что имеется достаточно ресурсов системы. Клиента необходимо остановить службу, либо служба может остановить себя при завершении работы. Чтобы остановить службу двумя способами. 
 
* **[Android.Content.Context.StopService()](https://developer.xamarin.com/api/member/Android.Content.Context.StopService/p/Android.Content.Intent/)**  &ndash; может запрашиваться клиентом (например, действия), остановите службу, вызов `StopService` метод: 

    ```csharp
    StopService(new Intent(this, typeof(DemoService));
    ```

* **[Android.App.Service.StopSelf()](https://developer.xamarin.com/api/member/Android.App.Service.StopSelf()/)**  &ndash; службы может завершиться самостоятельно путем вызова `StopSelf`:

    ```csharp
    StopSelf();
    ```
    
### <a name="using-startid-to-stop-a-service"></a>С помощью startId, чтобы остановить службу

Несколько вызывающим объектам можно запросить запуска службы. Если запрос на запуск необработанных, служба может использовать `startId` , передается в `OnStartCommand` для предотвращения преждевременного остановки службы. `startId` Будет соответствовать последней вызов `StartService`и увеличивается при каждом вызове. Таким образом Если последующие запросы к `StartService` не еще привел к вызову `OnStartCommand`, служба может вызывать `StopSelfResult`, передав его последнего значения `startId` было получено (вместо того чтобы просто вызывать `StopSelf`). Если вызов `StartService` не еще привела к соответствующим вызовом метода `OnStartCommand`, система не останавливает службу, так как `startId` используется в `StopSelf` вызов не будет соответствовать последней версии `StartService` вызова.


## <a name="related-links"></a>Связанные ссылки

- [StartedServicesDemo (пример)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/StartedServicesDemo/)
- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service)
- [Android.App.StartCommandFlags](https://developer.xamarin.com/api/type/Android.App.StartCommandFlags)
- [Android.App.StartCommandResult](https://developer.xamarin.com/api/type/Android.App.StartCommandResult)
- [Android.Content.BroadcastReceiver](https://developer.xamarin.com/api/type/Android.Content.BroadcastReceiver/)
- [Android.Content.Intent](https://developer.xamarin.com/api/type/Android.Content.Intent)
- [Android.OS.Handler](https://developer.xamarin.com/api/type/Android.OS.Handler/)
- [Android.Widget.Toast](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
- [Значки строки состояния](http://developer.android.com/guide/practices/ui_guidelines/icon_design_status_bar.html)
