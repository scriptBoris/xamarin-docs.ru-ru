---
title: Пошаговое руководство. Использование локальной уведомлений в Xamarin.iOS
description: В этом разделе мы рассмотрим способы использования локального уведомлений в приложения Xamarin.iOS. Он описывается основные принципы создания и публикации на уведомление, которое появится оповещение при получении приложением.
ms.prod: xamarin
ms.assetid: 32B9C6F0-2BB3-4295-99CB-A75418969A62
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: bb133d16f12249cbd31e4fce2b227162b4b28333
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30776785"
---
# <a name="walkthrough---using-local-notifications-in-xamarinios"></a>Пошаговое руководство. Использование локальной уведомлений в Xamarin.iOS

_В этом разделе мы рассмотрим способы использования локального уведомлений в приложения Xamarin.iOS. Он описывается основные принципы создания и публикации на уведомление, которое появится оповещение при получении приложением._

> [!IMPORTANT]
> Сведения в этом разделе, относятся к iOS 9 и предыдущий, она была оставлена здесь для поддержки более старых версий iOS. Для iOS, 10 и более поздней версии, см. в разделе [руководство по Framework уведомления пользователя](~/ios/platform/user-notifications/index.md) за поддержку локальных и удаленных уведомлений на устройства iOS.

## <a name="walkthrough"></a>Пошаговое руководство

Позволяет создать простое приложение, которое будет показывать локального уведомления в действии. Это приложение будет иметь одну кнопку на нем. При нажатии кнопки, оно создает локальный уведомления. По истечении указанного периода времени, мы увидим, что отображается уведомление.


1. В Visual Studio для Mac, создайте новое решение единое представление операций ввода-вывода и назовите его `Notifications`.
1. Откройте `Main.storyboard` файл и перетащите кнопку в представлении. Имя создаваемой кнопки **кнопку**и присвойте ему заголовок **добавьте уведомления**. Можно также настроить некоторые [ограничения](~/ios/user-interface/designer/designer-auto-layout.md) к кнопке на этом этапе: 

    ![](local-notifications-in-ios-walkthrough-images/image3.png "Задание некоторых ограничений на кнопке")
1. Изменить `ViewController` класс и добавьте следующий обработчик событий в метод ViewDidLoad:

    ```csharp
    button.TouchUpInside += (sender, e) =>
    {
        // create the notification
        var notification = new UILocalNotification();

        // set the fire date (the date time in which it will fire)
        notification.FireDate = NSDate.FromTimeIntervalSinceNow(60);

        // configure the alert
        notification.AlertAction = "View Alert";
        notification.AlertBody = "Your one minute alert has fired!";

        // modify the badge
        notification.ApplicationIconBadgeNumber = 1;

        // set the sound to be the default sound
        notification.SoundName = UILocalNotification.DefaultSoundName;

        // schedule it
        UIApplication.SharedApplication.ScheduleLocalNotification(notification);
    };
    ```

    Этот код создает уведомление, которое использует звука, устанавливает значение для эмблемы значок 1 и отображает предупреждение для пользователя.

1. Затем измените файл `AppDelegate.cs`, сначала добавьте следующий код в `FinishedLaunching` метод. Мы проверили возможно, если устройство работает под управлением iOS 8, поэтому мы будем **необходимые** запрашивать разрешение пользователя для получения уведомлений:

    ```csharp
    if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
            var notificationSettings = UIUserNotificationSettings.GetSettingsForTypes (
                UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound, null
            );

            application.RegisterUserNotificationSettings (notificationSettings);
        }
    ```

1. В по-прежнему `AppDelegate.cs`, добавьте следующий метод, который будет вызываться при получении уведомления:

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

1. Мы должны обрабатывать случаи, где уведомление было запущено из-за локального уведомления. Изменение метода `FinishedLaunching` в `AppDelegate` для включения в следующем фрагменте кода:


    ```csharp
    // check for a notification

    if (launchOptions != null)
    {
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
    }

    ```

1. Наконец запустите приложение. В iOS 8, вам будет предложено разрешить уведомления. Нажмите кнопку **ОК** и нажмите кнопку **добавить уведомление** кнопки. После короткой задержки появится окно предупреждения, как показано на следующем снимке экрана:

    ![](local-notifications-in-ios-walkthrough-images/image0.png "Возможность отправки уведомлений Подтверждение") ![ ] (local-notifications-in-ios-walkthrough-images/image1.png "уведомления, добавить кнопки") ![ ] (local-notifications-in-ios-walkthrough-images/image2.png "предупреждения диалоговое окно с предупреждением")

## <a name="summary"></a>Сводка

В этом пошаговом руководстве показано, как использовать различные API-Интерфейсы для создания и публикации уведомления в iOS. Он также было показано, как обновить значок эмблемы, чтобы отправить отзыв некоторые приложения для пользователя.


## <a name="related-links"></a>Связанные ссылки

- [Локальный уведомления (пример)](https://developer.xamarin.com/samples/monotouch/LocalNotifications)
- [Локальные и руководство по программированию Push-уведомлений](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
