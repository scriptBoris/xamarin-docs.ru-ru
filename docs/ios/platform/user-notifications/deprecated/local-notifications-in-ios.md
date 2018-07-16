---
title: Уведомлений в Xamarin.iOS
description: В этом разделе показано, как реализовать локальных уведомлений в Xamarin.iOS. Он будет объясняются различные элементы пользовательского интерфейса iOS уведомления о и рассматриваются API-Интерфейс связанных с созданием и отображение уведомления.
ms.prod: xamarin
ms.assetid: 5BB76915-5DB0-48C7-A267-FA9F7C50793E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/13/2018
ms.openlocfilehash: ef5ce97736e60ff3a03bc0d496eadcae8c6f7e61
ms.sourcegitcommit: cb80df345795989528e9df78eea8a5b45d45f308
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39038538"
---
# <a name="notifications-in-xamarinios"></a>Уведомлений в Xamarin.iOS

> [!IMPORTANT]
> Сведения в этом разделе, относятся к iOS 9 и более. IOS 10 и более поздние версии, см. в разделе [руководство framework уведомление пользователя](~/ios/platform/user-notifications/index.md).

iOS имеет три способа уведомить пользователя, что было получено уведомление.

- **Звук или вибрация** -iOS можно воспроизвести звук для уведомления пользователей. Если звук отключен, устройство можно настроить для «вибрация».
- **Оповещения** -можно отобразить диалоговое окно на экране сведения об уведомлении.
- **Значки** — когда публикуется уведомление, это значение может отображаться (эмблемой), на значок приложения.

iOS также предоставляет *центр уведомлений* , будет отображать все уведомления, локальными и удаленными, для пользователя. Пользователи могут обратиться, проводя пальцем вниз из верхней части экрана:

![Центр уведомлений](local-notifications-in-ios-images/image13.png "центр уведомлений")

## <a name="creating-local-notifications-in-ios"></a>Создание локальных уведомлений в iOS

iOS делает довольно прост, для создания и обработки локальных уведомлений.
Во-первых iOS 8 требует, чтобы запросить разрешение пользователя отображать уведомления для приложения. Добавьте следующий код в приложение перед попыткой отправки локального уведомления - [присоединенного образец](https://developer.xamarin.com/samples/monotouch/LocalNotifications/) помещает его в **AppDelegate** **FinishedLaunching** метод.

```csharp
var notificationSettings = UIUserNotificationSettings.GetSettingsForTypes(
    UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound, null
);
application.RegisterUserNotificationSettings(notificationSettings);
```

[![Подтверждение возможность отправки локального уведомления](local-notifications-in-ios-images/image0-sml.png "подтверждения возможность отправки локального уведомления")](local-notifications-in-ios-images/image0.png#lightbox)

Чтобы запланировать локальное уведомление, создайте `UILocalNotification` установите `FireDate`и запланировать его через `ScheduleLocalNotification` метод `UIApplication.SharedApplication` объекта. В следующем фрагменте кода показано, как запланировать уведомление, которое будет срабатывать в будущем в одну минуту и отображение оповещений с сообщением:

```csharp
UILocalNotification notification = new UILocalNotification();
NSDate.FromTimeIntervalSinceNow(15);
//notification.AlertTitle = "Alert Title"; // required for Apple Watch notifications
notification.AlertAction = "View Alert";
notification.AlertBody = "Your 15 second alert has fired!";
UIApplication.SharedApplication.ScheduleLocalNotification(notification);
```

Снимке экрана ниже показано, как выглядит это предупреждение:

[![](local-notifications-in-ios-images/image2-sml.png "Пример предупреждения")](local-notifications-in-ios-images/image2.png#lightbox)

Обратите внимание, что если пользователь решает *допускает* уведомления, то ничего не отображается.

Если вы хотите применить эмблему на значок приложения с номером, можно задать его, как показано в следующем коде строки:

```csharp
notification.ApplicationIconBadgeNumber = 1;
```

В порядок воспроизведения звука со значком, для свойства SoundName уведомление как показано в следующем фрагменте кода:

```csharp
notification.SoundName = UILocalNotification.DefaultSoundName;
```

Если звуковой сигнал содержит более 30 секунд, iOS будет звук по умолчанию вместо этого.

> [!IMPORTANT]
> Это ошибка в симуляторе iOS, который запустит уведомление делегат дважды. Эта проблема может произойти при запуске приложения на устройстве.

## <a name="handling-notifications"></a>Обработка уведомлений

приложения iOS обрабатывают удаленных и локальных уведомлений в почти так же. Когда приложение выполняется, `ReceivedLocalNotification` метод или `ReceivedRemoteNotification` метод `AppDelegate` класс будет называться и данные уведомления будут передаваться в качестве параметра.

Приложение может обрабатывать уведомления по-разному. Например приложение может просто отображать оповещение, чтобы напомнить пользователям о некоторых событий. Или уведомление может использоваться для отображения предупреждения пользователю о завершении процесса, таких как синхронизация файлов на сервер.

Приведенный ниже показано, как обрабатывать локального уведомления и отображение оповещений и сброшено в ноль номер эмблемы:

```csharp
public override void ReceivedLocalNotification(UIApplication application, UILocalNotification notification)
{
    // show an alert
    UIAlertController okayAlertController = UIAlertController.Create(notification.AlertAction, notification.AlertBody, UIAlertControllerStyle.Alert);
    okayAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));

    Window.RootViewController.PresentViewController(okayAlertController, true, null);

    // reset our badge
    UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
}
```

Если приложение не выполняется, iOS будет воспроизведения звука и/или обновить значок эмблемы, где это применимо. Когда пользователь начинает работу приложения, связанные с оповещением, запустится приложение и `FinishedLaunching` будет вызываться метод в делегате приложения и данные уведомления будут передаваться в через `launchOptions` параметра. Если ключ в словаре параметров `UIApplication.LaunchOptionsLocalNotificationKey`, а затем `AppDelegate` знает, что приложение было запущено из локального уведомления. Этот процесс показан в следующем фрагменте кода:

```csharp
// check for a local notification
if (launchOptions.ContainsKey(UIApplication.LaunchOptionsLocalNotificationKey))
{
    var localNotification = launchOptions[UIApplication.LaunchOptionsLocalNotificationKey] as UILocalNotification;
    if (localNotification != null)
    {
        UIAlertController okayAlertController = UIAlertController.Create(localNotification.AlertAction, localNotification.AlertBody, UIAlertControllerStyle.Alert);
        okayAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));

        Window.RootViewController.PresentViewController(okayAlertController, true, null);

        // reset our badge
        UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
    }
}
```

Для удаленного уведомления `launchOptions` будет иметь `LaunchOptionsRemoteNotificationKey` со связанным `NSDictionary` полезными данными удаленного уведомления. Можно извлечь полезные данные уведомления с помощью `alert`, `badge`, и `sound` ключи. В следующем фрагменте кода показано, как для получения удаленных уведомлений:

```csharp
NSDictionary remoteNotification = options[UIApplication.LaunchOptionsRemoteNotificationKey];
if(remoteNotification != null)
{
    string alert = remoteNotification["alert"];
}
```

## <a name="summary"></a>Сводка

В этом разделе показано, как создать и опубликовать уведомление в Xamarin.iOS. Показать, как приложение может реагировать на уведомления путем переопределения `ReceivedLocalNotification` метод или `ReceivedRemoteNotification` метод в `AppDelegate`.

## <a name="related-links"></a>Связанные ссылки

- [Локальные уведомления (пример)](https://developer.xamarin.com/samples/monotouch/LocalNotifications)
- [Локальных и Push-уведомлений для разработчиков](https://developer.apple.com/notifications/)
- [Локальных и Push-уведомлений руководство по программированию](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
- [UIApplication](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIApplication)
- [UILocalNotification](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UILocalNotification)
