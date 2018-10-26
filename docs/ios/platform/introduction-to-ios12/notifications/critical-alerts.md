---
title: Критические оповещения в Xamarin.iOS
description: В этом документе описывается использование критические оповещения с помощью Xamarin.iOS. Критические оповещения, появившиеся с iOS 12, уведомляются может нарушить работу системы, воспроизведение звука, независимо от того не беспокоить на или переключатель звонка отключен.
ms.prod: xamarin
ms.assetid: 75742257-081D-44F4-B49E-FB807DF85262
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 9/4/2018
ms.openlocfilehash: 4f847a86f3f92bcf7168c2e104471e1ca052969c
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50131871"
---
# <a name="critical-alerts-in-xamarinios"></a>Критические оповещения в Xamarin.iOS

Приложения iOS 12, может отправлять критические оповещения. Критические оповещения воспроизведение звука, независимо от того, является ли включена не беспокоить или переключатель звонка отключен. Эти уведомления являются ли они критическими и должен использоваться только когда пользователи должны отреагировать немедленно.

## <a name="custom-critical-alert-entitlement"></a>Пользовательских прав критических оповещений

Для отображения критических оповещений в приложении, сначала [запросить назначение настраиваемые критические оповещения](https://developer.apple.com/contact/request/notifications-critical-alerts-entitlement/) от Apple.

После получения это право у компании Apple и следующие связанные инструкции о том, как настроить приложение для использования, добавить настраиваемый [прав](~/ios/deploy-test/provisioning/entitlements.md) в приложение **Entitlements.plist** файлов. Затем настройте вашей **подписывание пакета iOS** параметры, используемые **Entitlements.plist** при входе в приложение на симуляторе и устройства.

## <a name="request-authorization"></a>Запрос авторизации

Запрос авторизации для уведомления приложения предлагает пользователю разрешить или запретить уведомления в приложения. Если запрос на авторизацию уведомлений запрашивает разрешение на отправку критические оповещения, приложение также предоставит пользователям возможность согласиться на критические оповещения.

Следующий код запрашивает разрешение на отправку критические оповещения и уведомления standard и звуки, передав соответствующий [`UNAuthorizationOptions`](https://developer.xamarin.com/api/type/UserNotifications.UNAuthorizationOptions/)
значения [ `RequestAuthorization` ](https://developer.xamarin.com/api/member/UserNotifications.UNUserNotificationCenter.RequestAuthorization/):

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.CriticalAlert;
    center.RequestAuthorization(options, (bool success, NSError error) => {
        // ...
    );
    return true;
}
```

## <a name="local-critical-alerts"></a>Локальный критических оповещений

Для отправки локального Критическое оповещение, создание [`UNMutableNotificationContent`](https://developer.xamarin.com/api/type/UserNotifications.UNMutableNotificationContent/)
и задайте его `Sound` значение:

- `UNNotificationSound.DefaultCriticalSound`, который использует звук критическое уведомление по умолчанию.
- `UNNotificationSound.GetCriticalSound`, которой можно указать пользовательский звук, который входит в состав приложения и тома.

Затем создайте `UNNotificationRequest` из уведомления содержимого и добавить его в центр уведомлений:

```csharp
var content = new UNMutableNotificationContent()
{
    Title = "Critical alert title",
    Body = "Text of the critical alert",
    CategoryIdentifier = "my-critical-alert-category",
    // Sound = UNNotificationSound.DefaultCriticalSound
    Sound = UNNotificationSound.GetCriticalSound("my_critical_sound.m4a", 1.0f)
};

var request = UNNotificationRequest.FromIdentifier(
    Guid.NewGuid().ToString(),
    content,
    UNTimeIntervalNotificationTrigger.CreateTrigger(3, false)
);

var center = UNUserNotificationCenter.Current;
center.AddNotificationRequest(request, null);
```

> [!IMPORTANT]
> Критические оповещения не было доставлено, если они не включены для вашего приложения. Вместе с приглашение, которое отображается первый раз приложение запрашивает разрешение для отправки критических оповещений, пользователь может включить или отключить критические оповещения в вашем приложении **уведомления** раздел iOS **параметры**приложения.

## <a name="remote-critical-alerts"></a>Удаленный критических оповещений

Сведения об удаленном критических оповещений см. в разделе [Какова новые в пользовательские уведомления](https://developer.apple.com/videos/play/wwdc2018/710/) сеанса из WWDC 2018 и [созданием удаленного уведомления](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification) документа.

## <a name="related-links"></a>Связанные ссылки

- [Платформа уведомлений пользователя в Xamarin.iOS](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Новые возможности в уведомления для пользователей (WWDC 2018 г.)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Рекомендации и новые возможности в уведомления для пользователей (WWDC 2017 г.)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Создание удаленного уведомления (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
