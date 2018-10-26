---
title: Уведомления интерактивных пользовательских интерфейсов в Xamarin.iOS
description: С помощью iOS 12 имеется возможность создавать интерактивные пользовательские интерфейсы для локальных и удаленных уведомлений. В этом руководстве описывается использование этих функций с помощью Xamarin.iOS.
ms.prod: xamarin
ms.assetid: E3562E1B-E0EF-4C99-9F51-59DE22AFDE46
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 9/4/2018
ms.openlocfilehash: 5a3a737360f898c2638bc5431cb8f8fc4882f3b0
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50131860"
---
# <a name="interactive-notification-user-interfaces-in-xamarinios"></a>Уведомления интерактивных пользовательских интерфейсов в Xamarin.iOS

[Расширения содержимого уведомлений](~/ios/platform/user-notifications/advanced-user-notifications.md), представленный в iOS 10, позволяют создавать настраиваемые пользовательские интерфейсы для получения уведомлений. Начиная с iOS 12, уведомление пользователя интерфейсы могут содержать интерактивные элементы, такие как кнопки и ползунков.

## <a name="sample-app-redgreennotifications"></a>Пример приложения: RedGreenNotifications

[RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications) пример приложения содержит расширение содержимого уведомлений с помощью интерактивного пользовательского интерфейса.

Фрагменты кода, в этом руководстве, взяты из этого примера.

## <a name="notification-content-extension-infoplist-file"></a>Файл Info.plist расширение содержимого уведомлений

В примере приложения **Info.plist** файл **RedGreenNotificationsContentExtension** проект содержит следующую конфигурацию:

```xml
<!-- ... -->
<key>NSExtension</key>
<dict>
    <key>NSExtensionAttributes</key>
    <dict>
        <key>UNNotificationExtensionCategory</key>
        <array>
            <string>red-category</string>
            <string>green-category</string>
        </array>
        <key>UNNotificationExtensionUserInteractionEnabled</key>
        <true/>
        <key>UNNotificationExtensionDefaultContentHidden</key>
        <true/>
        <key>UNNotificationExtensionInitialContentSizeRatio</key>
        <real>0.6</real>
    </dict>
    <key>NSExtensionMainStoryboard</key>
    <string>MainInterface</string>
    <key>NSExtensionPointIdentifier</key>
    <string>com.apple.usernotifications.content-extension</string>
    <key></key>
    <true/>
</dict>
<!-- ... -->
```

Обратите внимание на следующие компоненты:

- `UNNotificationExtensionCategory` Массив указывает на тип категории уведомлений маркеры расширение содержимого.
- Чтобы обеспечить поддержку интерактивное содержимое, задает расширение notification content `UNNotificationExtensionUserInteractionEnabled` ключа `true`.
- `UNNotificationExtensionInitialContentSizeRatio` Ключ указывает соотношение начальной высоту или ширину для интерфейса содержимого расширения.

## <a name="interactive-interface"></a>Интерактивный интерфейс

**MainInterface.storyboard**, который определяет интерфейс для расширение notification content, — это стандартный раскадровки, содержащий одно представление контроллера. В примере приложения, контроллер представления является типа `NotificationViewController`, и он содержит представление изображений, три кнопки и ползунок. Раскадровка связывает эти элементы управления с помощью обработчиков, определенных в **NotificationViewController.cs**:

- **Запуска приложения** кнопку вызовов обработчика `PerformNotificationDefaultAction` метод действия в `ExtensionContext`, который запускает приложение:

    ```csharp
    partial void HandleLaunchAppButtonTap(UIButton sender)
    {
        ExtensionContext.PerformNotificationDefaultAction();
    }
    ```

    Приложения, в центре уведомлений пользователя `Delegate` (в примере приложения, `AppDelegate`) может отвечать на действия в `DidReceiveNotificationResponse` метод:

    ```csharp
    [Export("userNotificationCenter:didReceiveNotificationResponse:withCompletionHandler:")]
    public void DidReceiveNotificationResponse(UNUserNotificationCenter center, UNNotificationResponse response, System.Action completionHandler)
    {
        if (response.IsDefaultAction)
        {
            Console.WriteLine("ACTION: Default");
            // ...
    ```

- **Отклонить уведомление** кнопку вызовов обработчика `DismissNotificationContentExtension` на `ExtensionContext`, который закрывает уведомления:

    ```csharp
    partial void HandleDismissNotificationButtonTap(UIButton sender)
    {
        ExtensionContext.DismissNotificationContentExtension();
    }
    ```

- **Удалить уведомление** обработчик кнопки закрывает уведомление и удаляет его из центра уведомлений:

    ```csharp
    partial void HandleRemoveNotificationButtonTap(UIButton sender)
    {
        ExtensionContext.DismissNotificationContentExtension();
        UNUserNotificationCenter.Current.RemoveDeliveredNotifications(new string[] { notification.Request.Identifier });
    }
    ```

- Метод, который обрабатывает изменения значений на ползунке обновляет альфа-канал изображения, отображаемого в интерфейсе уведомления:

    ```csharp
    partial void HandleSliderValueChanged(UISlider sender)
    {
        Xamagon.Alpha = sender.Value;
    }
    ```

## <a name="related-links"></a>Связанные ссылки

- [Пример приложения — RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications)
- [Платформа уведомлений пользователя в Xamarin.iOS](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Новые возможности в уведомления для пользователей (WWDC 2018 г.)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Рекомендации и новые возможности в уведомления для пользователей (WWDC 2017 г.)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Расширенные уведомления (WWDC 2017 г.)](https://developer.apple.com/videos/play/wwdc2017/817/)
- [Создание удаленного уведомления (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
