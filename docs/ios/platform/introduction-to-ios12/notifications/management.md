---
title: Управление уведомлений в Xamarin.iOS
description: В этом документе описывается, как использовать Xamarin.iOS, чтобы воспользоваться преимуществами новых возможностей управления уведомлений, добавленных в iOS 12.
ms.prod: xamarin
ms.assetid: F1D90729-F85A-425B-B633-E2FA38FB4A0C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 9/4/2018
ms.openlocfilehash: a7a3bb8f720f1c6a2370a2510659693bb28ea09b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111596"
---
# <a name="notification-management-in-xamarinios"></a>Управление уведомлений в Xamarin.iOS

В iOS 12 операционная система может прямая ссылка из центра уведомлений и параметрам приложения для экрана управления уведомлений приложения. Этот экран пользователи должны иметь возможность согласиться и за пределы различные типы уведомлений, которые приложение отправляет.

## <a name="sample-app-redgreennotifications"></a>Пример приложения: RedGreenNotifications

Чтобы увидеть пример того, как работает управление уведомлениями об, взгляните на [RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications) примера приложения.

Этот пример приложения отправляет два типа уведомлений — красный и зеленый — и предоставляет экран, на котором пользователи могут принять или отклонить обоих этих типов.

Фрагменты кода, в этом руководстве, взяты из этого примера приложения.

## <a name="notification-management-screen"></a>Экран управления уведомлений

В примере приложения `ManageNotificationsViewController` определяет пользовательский интерфейс, который позволяет пользователям независимо друг от друга Включение и отключение уведомлений красный и зеленый уведомлений. Это стандартный [`UIViewController`](https://developer.xamarin.com/api/type/UIKit.UIViewController/)
содержащий [ `UISwitch` ](https://developer.xamarin.com/api/type/UIKit.UISwitch/) для каждого типа уведомления. Включение и выключение коммутатора для каждого типа уведомлений сохраняет, пользовательские параметры по умолчанию, пользовательские настройки для этого типа уведомлений:

```csharp
partial void HandleRedNotificationsSwitchValueChange(UISwitch sender)
{
    NSUserDefaults.StandardUserDefaults.SetBool(sender.On, RedNotificationsEnabledKey);
}
```

> [!NOTE]
> На экран управления уведомлений также проверяет ли пользователь полностью отключает уведомления для приложения. Если Да, он скрывает переключатели для типов индивидуальное уведомление. Чтобы сделать это, на экран управления уведомлений:
>
> - Вызовы [ `UNUserNotificationCenter.Current.GetNotificationSettingsAsync` ](https://developer.xamarin.com/api/member/UserNotifications.UNUserNotificationCenter.GetNotificationSettingsAsync()/) и проверяет [ `AuthorizationStatus` ](https://developer.xamarin.com/api/property/UserNotifications.UNNotificationSettings.AuthorizationStatus/) свойство.
> - Скрывает переключатели для типов индивидуальное уведомление, если уведомления были полностью отключены для приложения.
> - Повторно проверяет, были ли уведомления отключены каждый раз, когда приложение перемещает на передний план, так как пользователь может включить или отключить уведомления в параметрах iOS в любое время.

В примере приложения `ViewController` класс, который отправляет уведомления, проверки пользовательские настройки до отправки локального уведомления для убедитесь, что уведомление типа пользователь действительно хочет получать:

```csharp
partial void HandleTapRedNotificationButton(UIButton sender)
{
    bool redEnabled = NSUserDefaults.StandardUserDefaults.BoolForKey(ManageNotificationsViewController.RedNotificationsEnabledKey);
    if (redEnabled)
    {
        // ...
```

## <a name="deep-link"></a>Прямая ссылка

iOS прямые ссылки на экран управления уведомлений приложения из центра уведомлений и параметры уведомлений приложения в настройках приложения. Для этого приложения необходимо:

- Что доступно экран управления уведомление путем передачи `UNAuthorizationOptions.ProvidesAppNotificationSettings` запрос авторизации для получения уведомлений приложения.
- Реализуйте `OpenSettings` метода из [ `IUNUserNotificationCenterDelegate` ](https://developer.xamarin.com/api/type/UserNotifications.IUNUserNotificationCenterDelegate/).

### <a name="authorization-request"></a>Запрос авторизации

Чтобы указать операционную систему, доступности экран управления уведомления, приложения должны передавать `UNAuthorizationOptions.ProvidesAppNotificationSettings` параметра (вместе с другими уведомлений параметров доставки ей) `RequestAuthorization` метод `UNUserNotificationCenter`.

Например, в примере приложения `AppDelegate`:

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    // Request authorization to send notifications
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.ProvidesAppNotificationSettings | UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.Provisional;
    center.RequestAuthorization(options, (bool success, NSError error) =>
    {
        // ...
```

### <a name="opensettings-method"></a>Метод OpenSettings

`OpenSettings` Вызывается системой для прямая ссылка на экран управления уведомлений приложения, метод должен перейти пользователь непосредственно на этом экране.

В примере приложения, этот метод выполняет переход к `ManageNotificationsViewController` при необходимости:

```csharp
[Export("userNotificationCenter:openSettingsForNotification:")]
public void OpenSettings(UNUserNotificationCenter center, UNNotification notification)
{
    var navigationController = Window.RootViewController as UINavigationController;
    if (navigationController != null)
    {
        var currentViewController = navigationController.VisibleViewController;
        if (currentViewController is ViewController)
        {
            currentViewController.PerformSegue(ManageNotificationsViewController.ShowManageNotificationsSegue, this);
        }

    }
}
```

## <a name="related-links"></a>Связанные ссылки

- [Пример приложения — RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications)
- [Платформа уведомлений пользователя в Xamarin.iOS](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Новые возможности в уведомления для пользователей (WWDC 2018 г.)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Рекомендации и новые возможности в уведомления для пользователей (WWDC 2017 г.)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Создание удаленного уведомления (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
