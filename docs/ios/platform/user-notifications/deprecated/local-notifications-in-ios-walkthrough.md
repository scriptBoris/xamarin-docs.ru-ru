---
title: Пошаговое руководство. Использование локальных уведомлений в Xamarin.iOS
description: В этом разделе мы рассмотрим способы использования локальных уведомлений в приложении Xamarin.iOS. Его мы рассмотрим основы создания и публикации, появится предупреждение, когда приложение получит уведомление.
ms.prod: xamarin
ms.assetid: 32B9C6F0-2BB3-4295-99CB-A75418969A62
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: cf1e44ba4176922234fc1b6b9bfe5c463611cc7b
ms.sourcegitcommit: 081a2d094774c6f75437d28b71d22607e33aae71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37403433"
---
# <a name="walkthrough---using-local-notifications-in-xamarinios"></a>Пошаговое руководство. Использование локальных уведомлений в Xamarin.iOS

_В этом разделе мы рассмотрим способы использования локальных уведомлений в приложении Xamarin.iOS. Его мы рассмотрим основы создания и публикации, появится предупреждение, когда приложение получит уведомление._

> [!IMPORTANT]
> Сведения в этом разделе, относятся к iOS 9 и предыдущий, она была оставлена здесь для поддержки более старых версий iOS. IOS 10 и более поздние версии, см. в разделе [руководство по Framework уведомление пользователя](~/ios/platform/user-notifications/index.md) за поддержку локальных и удаленных уведомлений на устройства iOS.

## <a name="walkthrough"></a>Пошаговое руководство

Позволяют создать простое приложение, которое будет отображать локальные уведомления в действии. На это приложение будет установлено одну кнопку. При нажатии кнопки, он создаст локального уведомления. По истечении указанного периода времени, мы увидим, что отображается уведомление.


1. В Visual Studio для Mac, создайте новое решение iOS единое представление и назовите его `Notifications`.
1. Откройте `Main.storyboard` файл и перетащить кнопку на представление. Назовите кнопку **кнопку**и присвойте ему название **добавления уведомлений**. Можно также задать несколько [ограничения](~/ios/user-interface/designer/designer-auto-layout.md) кнопке на этом этапе: 

    ![](local-notifications-in-ios-walkthrough-images/image3.png "Параметр некоторые ограничения на кнопке")
1. Изменить `ViewController` класса и добавьте следующий обработчик событий в метод ViewDidLoad:

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

    Этот код создает уведомление, которое использует звука, присваивает значение значок эмблемы 1 и отображает предупреждение для пользователя.

1. Затем измените файл `AppDelegate.cs`, сначала добавьте следующий код, чтобы `FinishedLaunching` метод. Мы проверили на предмет Если устройство работает под управлением iOS 8, то мы все **требуется** запрашивать разрешение пользователя для получения уведомлений:

    ```csharp
    if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
            var notificationSettings = UIUserNotificationSettings.GetSettingsForTypes (
                UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound, null
            );

            application.RegisterUserNotificationSettings (notificationSettings);
        }
    ```

1. По-прежнему в `AppDelegate.cs`, добавьте следующий метод, который будет вызываться при получении уведомления:

    ```csharp
    public override void ReceivedLocalNotification(UIApplication application, UILocalNotification notification)
            {
                // show an alert
                UIAlertController okayAlertController = UIAlertController.Create(notification.AlertAction, notification.AlertBody, UIAlertControllerStyle.Alert);
                okayAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));

                UIApplication.SharedApplication.KeyWindow.RootViewController.PresentViewController(okayAlertController, true, null);

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

1. Наконец запустите приложение. В iOS 8, вам будет предложено разрешить уведомления. Нажмите кнопку **ОК** и нажмите кнопку **добавить уведомление** кнопки. После короткой задержки вы увидите диалоговое окно оповещения, как показано на следующем снимке экрана:

    ![](local-notifications-in-ios-walkthrough-images/image0.png "Подтверждение возможность отправлять уведомления") ![ ] (local-notifications-in-ios-walkthrough-images/image1.png "кнопки Добавить уведомление") ![ ] (local-notifications-in-ios-walkthrough-images/image2.png "диалоговое окно уведомления для оповещения")

## <a name="summary"></a>Сводка

В этом пошаговом руководстве показано, как использовать различные API для создания и публикации уведомления в iOS. Кроме того, вы узнали, как обновить значок эмблемы, чтобы отправить отзыв некоторые приложения для пользователя.


## <a name="related-links"></a>Связанные ссылки

- [Локальные уведомления (пример)](https://developer.xamarin.com/samples/monotouch/LocalNotifications)
- [Локальный и руководство по программированию Push-уведомлений](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
