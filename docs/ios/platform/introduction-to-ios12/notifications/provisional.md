---
title: Временное уведомлений в Xamarin.iOS
description: В этом документе описывается, как использовать Xamarin.iOS для работы с условным уведомления. Временное уведомления, впервые появился в iOS 12, позволяют приложениям отправлять quiet уведомления без явного разрешения.
ms.prod: xamarin
ms.assetid: 5DCB36B9-2637-48AE-8FC0-F6124F08AC48
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 9/4/2018
ms.openlocfilehash: 31b4c6e98945cd7b5dd4cea8be6f5e3857444f78
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50132061"
---
# <a name="provisional-notifications-in-xamarinios"></a>Временное уведомлений в Xamarin.iOS

Временное уведомления позволяют приложениям для доставки уведомлений без явного первоначальных согласия пользователя. Эти уведомления поступают в скрытом режиме и Показать только в центре уведомлений, которая позволяет пользователям осуществлять предварительный просмотр перед включением защиты или отклонить их непрерывной доставки.

В центре уведомлений пользователи могут указать, что приложения следует остановить доставки уведомлений, вы можете по-прежнему условно их доставку и доставлять их наиболее вероятными.

## <a name="sample-app-redgreennotifications"></a>Пример приложения: RedGreenNotifications

Взгляните на [RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications) пример приложения, который отправляет уведомления временное.

## <a name="sending-provisional-notifications"></a>Отправка уведомлений временное

Для отправки уведомлений, вы можете предоставить `UNAuthorizationOptions.Provisional` как параметр [`RequestAuthorization`](https://developer.xamarin.com/api/member/UserNotifications.UNUserNotificationCenter.RequestAuthorization/)
метод `UNUserNotificationCenter`:

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.Provisional;
    center.RequestAuthorization(options, (bool success, NSError error) => {
        // ...
    );
    return true;
}
```

Если пользователь повышает временное уведомления для известных доставки `UNAuthorizationOptions` значения, передаваемые в `RequestAuthorization` определяет новые параметры доставки уведомлений (в приведенном выше коде `UNAuthorizationOptions.Alert` и `UNAuthorizationOptions.Sound`).

## <a name="related-links"></a>Связанные ссылки

- [Пример приложения — RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications)
- [Платформа уведомлений пользователя в Xamarin.iOS](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Новые возможности в уведомления для пользователей (WWDC 2018 г.)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Рекомендации и новые возможности в уведомления для пользователей (WWDC 2017 г.)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Создание удаленного уведомления (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
