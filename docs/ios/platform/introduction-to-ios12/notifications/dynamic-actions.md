---
title: Кнопки действий динамического уведомлений в Xamarin.iOS
description: С помощью iOS 12 расширение notification content можно добавлять, удалять и обновлять действие кнопки, отображаемые вместе с уведомления. В этом документе описывается использование кнопки действий динамического уведомлений с помощью Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 6B34AD78-5117-42D0-B6E7-C8B4B453EAFF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 9/4/2018
ms.openlocfilehash: 8a2755de3dc43ccff88cbdf4dc9c4f9ba2d532c0
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111628"
---
# <a name="dynamic-notification-action-buttons-in-xamarinios"></a>Кнопки действий динамического уведомлений в Xamarin.iOS

В iOS 12 уведомления можно динамически добавлять, удалять и обновлять свои кнопки соответствующее действие. Подобная настройка позволяет предоставлять пользователям действия, которые непосредственно относятся к содержимому уведомления и взаимодействие пользователя с ним.

## <a name="sample-app-redgreennotifications"></a>Пример приложения: RedGreenNotifications

Фрагменты кода в этом руководстве взяты из [RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications) пример приложения, который показывает, как использовать Xamarin.iOS для работы с кнопками действие уведомления в iOS 12.

Этот пример приложения отправляет два вида локальных уведомлений: красный и зеленый.
После того, как приложение отправки уведомления, используйте 3D Touch для просмотра его пользовательский интерфейс. Затем используйте кнопки действий уведомления для поворота изображения, которые в нем отображаются. Как поворачивает изображение, **Отменить поворот** кнопка появляется и исчезает при необходимости.

Фрагменты кода, в этом руководстве, взяты из этого примера приложения.

## <a name="default-action-buttons"></a>Кнопки действий по умолчанию

Категория уведомления определяет его кнопки действий по умолчанию.

Создайте и зарегистрируйте категории уведомлений при запуске приложения.
Например, в [пример приложения](#sample-app-redgreennotifications), `FinishedLaunching` метод `AppDelegate` делает следующее:

- Определяет одну категорию для красного уведомлений, а другой — для зеленого уведомления
- Регистрирует эти категории путем вызова [`SetNotificationCategories`](https://developer.xamarin.com/api/member/UserNotifications.UNUserNotificationCenter.SetNotificationCategories/)
метод `UNUserNotificationCenter`
- Присоединяет один [`UNNotificationAction`](https://developer.xamarin.com/api/type/UserNotifications.UNNotificationAction/)
для каждой категории

В следующем примере кода показано, как это работает:

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    // Request authorization to send notifications
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.Provisional | UNAuthorizationOptions.ProvidesAppNotificationSettings;
    center.RequestAuthorization(options, (bool success, NSError error) =>
    {
        // ...
        var rotateTwentyDegreesAction = UNNotificationAction.FromIdentifier("rotate-twenty-degrees-action", "Rotate 20°", UNNotificationActionOptions.None);

        var redCategory = UNNotificationCategory.FromIdentifier(
            "red-category",
            new UNNotificationAction[] { rotateTwentyDegreesAction },
            new string[] { },
            UNNotificationCategoryOptions.CustomDismissAction
        );

        var greenCategory = UNNotificationCategory.FromIdentifier(
            "green-category",
            new UNNotificationAction[] { rotateTwentyDegreesAction },
            new string[] { },
            UNNotificationCategoryOptions.CustomDismissAction
        );

        var set = new NSSet<UNNotificationCategory>(redCategory, greenCategory);
        center.SetNotificationCategories(set);
    });
    // ...
}
```

На основе этого кода, все уведомления [`Content.CategoryIdentifier`](https://developer.xamarin.com/api/property/UserNotifications.UNNotificationContent.CategoryIdentifier/)
оно будет «красный category» или «зеленый category», по умолчанию, show **повернуть 20°** кнопкой действия.

## <a name="in-app-handling-of-notification-action-buttons"></a>Обработка кнопки действий для уведомлений в приложении

`UNUserNotificationCenter` имеет `Delegate` свойство типа [ `IUNUserNotificationCenterDelegate` ](https://developer.xamarin.com/api/type/UserNotifications.UNUserNotificationCenterDelegate_Extensions/).

В примере приложения `AppDelegate` устанавливается как делегат центра уведомлений для пользователей в `FinishedLaunching`:

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    // Request authorization to send notifications
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = // ...
    center.RequestAuthorization(options, (bool success, NSError error) =>
    {
        center.Delegate = this;
        // ...
```

Затем `AppDelegate` реализует [`DidReceiveNotificationResponse`](https://developer.xamarin.com/api/member/UserNotifications.UNUserNotificationCenterDelegate_Extensions.DidReceiveNotificationResponse/)
Чтобы обработать действие касания кнопки:

```csharp
[Export("userNotificationCenter:didReceiveNotificationResponse:withCompletionHandler:")]
public void DidReceiveNotificationResponse(UNUserNotificationCenter center, UNNotificationResponse response, System.Action completionHandler)
{
    if (response.IsDefaultAction)
    {
        Console.WriteLine("ACTION: Default");
    }
    if (response.IsDismissAction)
    {
        Console.WriteLine("ACTION: Dismiss");
    }
    else
    {
        Console.WriteLine($"ACTION: {response.ActionIdentifier}");
    }

    completionHandler();
        }
```

Эта реализация `DidReceiveNotificationResponse` не обрабатывает уведомления **повернуть 20°** кнопкой действия. Вместо этого расширение notification content обрабатывает нажатия этой кнопки. Дополнительно в следующем разделе рассматривается обработка кнопки действие уведомления.

## <a name="action-buttons-in-the-notification-content-extension"></a>Кнопки действий в расширение notification content

Расширение notification content содержит контроллер представления, который определяет пользовательский интерфейс для уведомления.

Можно использовать в этом контроллере представления `GetNotificationActions` и `SetNotificationActions` методов в его [`ExtensionContext`](https://developer.xamarin.com/api/property/UIKit.UIViewController.ExtensionContext/)
свойство для доступа и изменения кнопки действий уведомления.

В примере приложения контроллер представления расширение notification content изменяет кнопки действий только в том случае, при ответе на коснуться кнопки действия уже существующие.

> [!NOTE]
> Расширение содержимого может отвечать на действия нажатия кнопки в контроллер представления уведомление [ `DidReceiveNotificationResponse` ](https://developer.xamarin.com/api/member/UserNotificationsUI.UNNotificationContentExtension_Extensions.DidReceiveNotificationResponse/) метод, объявленный как часть [IUNNotificationContentExtension](https://developer.xamarin.com/api/type/UserNotificationsUI.IUNNotificationContentExtension/).
>
> На то, что она имеет одно имя с `DidReceiveNotificationResponse` метод [описанных выше](#in-app-handling-of-notification-action-buttons), это другой метод.
>
> После расширение notification content обработав путем нажатия кнопки, можно ли определить основного приложения для обработки этого же нажатия кнопки. Чтобы сделать это, необходимо передать соответствующее значение [UNNotificationContentExtensionResponseOption](https://developer.xamarin.com/api/type/UserNotificationsUI.UNNotificationContentExtensionResponseOption/) к очередной обработчик завершения:
>
> - [`Dismiss`](https://developer.xamarin.com/api/field/UserNotificationsUI.UNNotificationContentExtensionResponseOption.Dismiss/) Указывает, что интерфейс уведомлений должно быть закрыто и что основное приложение не нужно обрабатывать нажатия кнопки.
> - [`DismissAndForwardAction`](https://developer.xamarin.com/api/field/UserNotificationsUI.UNNotificationContentExtensionResponseOption.DismissAndForwardAction/) Указывает, что интерфейс уведомлений должно быть закрыто и основного приложения необходимо также обрабатывать нажатия кнопки.
> - [`DoNotDismiss`](https://developer.xamarin.com/api/field/UserNotificationsUI.UNNotificationContentExtensionResponseOption.DoNotDismiss/) Указывает, что интерфейс уведомлений не должно быть закрыто и что основное приложение не нужно обрабатывать нажатия кнопки.

Модуль содержимого `DidReceiveNotificationResponse` метод определяет, какая кнопка была касание, поворачивает изображение в интерфейс уведомлений и отображение или скрытие **Сброс** кнопки действия:

```csharp
[Export("didReceiveNotificationResponse:completionHandler:")]
public void DidReceiveNotificationResponse(UNNotificationResponse response, Action<UNNotificationContentExtensionResponseOption> completionHandler)
{
    var rotationAction = ExtensionContext.GetNotificationActions()[0];

    if (response.ActionIdentifier == "rotate-twenty-degrees-action")
    {
        rotationButtonTaps += 1;

        double radians = (20 * rotationButtonTaps) * (2 * Math.PI / 360.0);
        Xamagon.Transform = CGAffineTransform.MakeRotation((float)radians);

        // 9 rotations * 20 degrees = 180 degrees. No reason to
        // show the reset rotation button when the image is half
        // or fully rotated.
        if (rotationButtonTaps % 9 == 0)
        {
            ExtensionContext.SetNotificationActions(new UNNotificationAction[] { rotationAction });
        }
        else if (rotationButtonTaps % 9 == 1)
        {
            var resetRotationAction = UNNotificationAction.FromIdentifier("reset-rotation-action", "Reset rotation", UNNotificationActionOptions.None);
            ExtensionContext.SetNotificationActions(new UNNotificationAction[] { rotationAction, resetRotationAction });
        }
    }

    if (response.ActionIdentifier == "reset-rotation-action")
    {
        rotationButtonTaps = 0;

        double radians = (20 * rotationButtonTaps) * (2 * Math.PI / 360.0);
        Xamagon.Transform = CGAffineTransform.MakeRotation((float)radians);

        ExtensionContext.SetNotificationActions(new UNNotificationAction[] { rotationAction });
    }

    completionHandler(UNNotificationContentExtensionResponseOption.DoNotDismiss);
}
```

В этом случае метод передает `UNNotificationContentExtensionResponseOption.DoNotDismiss` к очередной обработчик завершения. Это значит, что интерфейс уведомления останется открытым.

## <a name="related-links"></a>Связанные ссылки

- [Пример приложения — RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications)
- [Платформа уведомлений пользователя в Xamarin.iOS](~/ios/platform/user-notifications/index.md)
- [Объявление типов практические уведомлений](https://developer.apple.com/documentation/usernotifications/declaring_your_actionable_notification_types?language=objc)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Новые возможности в уведомления для пользователей (WWDC 2018 г.)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Рекомендации и новые возможности в уведомления для пользователей (WWDC 2017 г.)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Создание удаленного уведомления (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
