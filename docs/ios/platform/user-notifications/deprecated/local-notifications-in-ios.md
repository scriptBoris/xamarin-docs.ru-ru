---
title: Уведомления в Xamarin.iOS
description: В этом разделе показано, как реализовать локальный уведомления в Xamarin.iOS. Он будет приведено описание различных элементов пользовательского интерфейса iOS уведомления о и рассматриваются API-Интерфейс связанные с созданием и отображает соответствующее уведомление.
ms.prod: xamarin
ms.assetid: 5BB76915-5DB0-48C7-A267-FA9F7C50793E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: d4dd759f52e52f417441f69e6417fab536daf6d3
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30777916"
---
# <a name="notifications-in-xamarinios"></a>Уведомления в Xamarin.iOS

_В этом разделе показано, как реализовать локальный уведомления в Xamarin.iOS. Он будет приведено описание различных элементов пользовательского интерфейса iOS уведомления о и рассматриваются API-Интерфейс связанные с созданием и отображает соответствующее уведомление._

> [!IMPORTANT]
> Сведения в этом разделе, относятся к iOS 9 и предыдущий, она была оставлена здесь для поддержки более старых версий iOS. Для iOS, 10 и более поздней версии, см. в разделе [руководство по Framework уведомления пользователя](~/ios/platform/user-notifications/index.md) за поддержку локальных и удаленных уведомлений на устройства iOS.

операций ввода-вывода есть три способа уведомить пользователя, что было получено уведомление:

-  **Звуковой или вибрация** -операций ввода-вывода может воспроизводить звук для уведомления пользователей. Если звук отключен, устройство можно настроить для компакт-дисков.
-  **Оповещения** -существует возможность отображения диалогового окна на экране с помощью сведений об уведомлении.
-  **Значки** — когда публикуется уведомление, это значение может отображаться (эмблемой) на значок приложения.


также предоставляет iOS *центр уведомлений* , будет отображать все уведомления, локальные и удаленные, для пользователя. Пользователи могут получить доступ к этому, проведя вниз в верхней части экрана:

 ![](local-notifications-in-ios-images/image13.png "Центр уведомлений")

## <a name="creating-local-notifications-in-ios"></a>Создание локального уведомления в iOS

iOS делает довольно проста, для создания и обработки локальных уведомления.
Во-первых iOS 8 требует, чтобы запрашивать разрешение пользователя отображать уведомления для приложения. Добавьте следующий код в приложение перед попыткой отправки уведомления локальных — вложенное пример помещает его в **AppDelegate** **FinishedLaunching** метод.

```csharp
var settings = UIUserNotificationSettings.GetSettingsForTypes(
  UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound
  , null);
UIApplication.SharedApplication.RegisterUserNotificationSettings (settings);
```

  [![](local-notifications-in-ios-images/image0-sml.png "Подтверждение возможность отправки локального уведомления")](local-notifications-in-ios-images/image0.png#lightbox)

Чтобы запланировать локальное уведомление создается `UILocalNotification` , задайте `FireDate`и запланировать его через `ScheduleLocalNotification` метод `UIApplication.SharedApplication` объекта. В следующем фрагменте кода показана запланировать уведомление, которое будет срабатывать в минуту в будущем и выводить предупреждение с сообщением.

```csharp
UILocalNotification notification = new UILocalNotification();
NSDate.FromTimeIntervalSinceNow(15);
//notification.AlertTitle = "Alert Title"; // required for Apple Watch notifications
notification.AlertAction = "View Alert";
notification.AlertBody = "Your 15 second alert has fired!";
UIApplication.SharedApplication.ScheduleLocalNotification(notification);
```

Следующий снимок экрана показывают, как выглядит данного предупреждения:

  [![](local-notifications-in-ios-images/image2-sml.png "Пример оповещения")](local-notifications-in-ios-images/image2.png#lightbox)

Обратите внимание, что если пользователь выбрал *допускает* уведомления, то ничего не отображается.

Если вы хотите применить эмблемы на значок приложения с номером, может быть задана как показано в следующем коде строки:

```csharp
notification.ApplicationIconBadgeNumber = 1;
```

В порядке воспроизведения звука с помощью значка свойству SoundName уведомление как показано в следующем фрагменте кода:

```csharp
notification.SoundName = UILocalNotification.DefaultSoundName;
```

По рекомендациям Apple по интерфейсам Если уведомление воспроизведения звука, он должен также соответствовать индикатор событий или предупреждение, которое помогает пользователю определить приложение, вызвавшее предупреждение. Кроме того Если звук более 30 секунд, операций ввода-вывода будет звуковое уведомление по умолчанию вместо него.

> [!IMPORTANT]
> Имеется ошибка в симуляторе iOS, который запустит уведомление делегат дважды. Эта проблема не будет выполняться при запуске приложения на устройстве.

## <a name="handling-notifications"></a>Обработка уведомлений

операций ввода-вывода приложения обрабатывают удаленные и локальные уведомления в почти так же. При запуске приложения, `ReceivedLocalNotification` метода или `ReceivedRemoteNotification` будет вызван метод класса AppDelegate и сведения об уведомлениях будет передана в качестве параметра.

Приложение может обрабатывать уведомления по-разному. Для экземпляра приложения может просто отображать оповещение, чтобы напомнить пользователям о некоторых событий. Или уведомление может использоваться для отображения предупреждения пользователю о завершении процесса, такие как синхронизация файлов на сервер.

Следующий код демонстрируется обработки локальных уведомления и выводить предупреждение и сбрасываются в нуль номер эмблемы:

```csharp
 public override void ReceivedLocalNotification(UIApplication application, UILocalNotification notification)
        {
            // show an alert
            UIAlertController okayAlertController = UIAlertController.Create (notification.AlertAction, notification.AlertBody, UIAlertControllerStyle.Alert);
            okayAlertController.AddAction (UIAlertAction.Create ("OK", UIAlertActionStyle.Default, null));
            viewController.PresentViewController (okayAlertController, true, null);

            // reset our badge
            UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
        }
```

Если приложение не выполняется, iOS воспроизведения звука или обновить значок эмблемы применимым. Когда пользователь запускает приложение, связанной с предупреждением, запускается приложение и будет вызван метод FinishedLaunching в делегате, приложения и данные уведомления в будут передаваться через параметр options. Если параметры словарь содержит ключ `UIApplication.LaunchOptionsLocalNotificationKey`, AppDelegate узнает, что приложение было запущено из локального уведомления. В следующем фрагменте кода показан этот процесс:

```csharp
// check for a local notification
if (options.ContainsKey(UIApplication.LaunchOptionsLocalNotificationKey))
 {
     var localNotification = options[UIApplication.LaunchOptionsLocalNotificationKey] as UILocalNotification;
     if (localNotification != null)
     {
        UIAlertController okayAlertController = UIAlertController.Create (localNotification.AlertAction, localNotification.AlertBody, UIAlertControllerStyle.Alert);
        okayAlertController.AddAction (UIAlertAction.Create ("OK", UIAlertActionStyle.Default, null));
        viewController.PresentViewController (okayAlertController, true, null);

         // reset our badge
         UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
     }
 }
```

Если это удаленный словарь параметров вместо этого будет содержать уведомление `LaunchOptionsRemoteNotificationKey`, и результирующее значение для этого ключа будет `NSDictionary` полезные данные уведомления удаленного объекта. Можно извлечь полезные данные уведомления через предупреждения, эмблемы и звуковой ключей. В следующем фрагменте кода показано, как получить удаленный уведомления:

```csharp
NSDictionary remoteNotification = options[UIApplication.LaunchOptionsRemoteNotificationKey];
if(remoteNotification != null)
{
    string alert = remoteNotification["alert"];
}
```

## <a name="summary"></a>Сводка

В этом разделе показано, как создать и опубликовать уведомление в Xamarin.iOS. В нем показано, как приложение может реагировать на уведомления путем переопределения `ReceivedLocalNotification` метода или `ReceivedRemoteNotification` метод в `AppDelegate`.


## <a name="related-links"></a>Связанные ссылки

- [Локальный уведомления (пример)](https://developer.xamarin.com/samples/monotouch/LocalNotifications)
- [Локальные и Push-уведомления для разработчиков](https://developer.apple.com/notifications/)
- [Локальные и отправить уведомления руководство по программированию](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
- [UIApplication](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIApplication)
- [UILocalNotification](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UILocalNotification)
