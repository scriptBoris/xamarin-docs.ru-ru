---
title: Интеграция Azure Active Directory B2C с мобильными приложениями Azure
description: Azure Active Directory B2C является это облаке решение управления удостоверениями для мобильных приложений и веб-потребитель. В этой статье показано, как использовать Azure Active Directory B2C для предоставления проверки подлинности и авторизации к экземпляру мобильных приложений Azure с помощью Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 53F52036-A997-4D0F-86B4-4302C6913136
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: cafc1e78779dc393fa0409daa08b3daa8948a1ee
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38815681"
---
# <a name="integrating-azure-active-directory-b2c-with-azure-mobile-apps"></a>Интеграция Azure Active Directory B2C с мобильными приложениями Azure

_Azure Active Directory B2C является это облаке решение управления удостоверениями для мобильных приложений и веб-потребитель. В этой статье показано, как использовать Azure Active Directory B2C для предоставления проверки подлинности и авторизации к экземпляру мобильных приложений Azure с помощью Xamarin.Forms._

![](~/media/shared/preview.png "Этот API в настоящее время в предварительной версии")

> [!NOTE]
> [Библиотека проверки подлинности Майкрософт](https://www.nuget.org/packages/Microsoft.Identity.Client) находится на стадии предварительной версии, но подходит для использования в рабочей среде. Тем не менее может критические изменения в API, формат внутреннего кэша и другие механизмы библиотеку, которая может повлиять на приложение.

## <a name="overview"></a>Обзор

Мобильные приложения Azure позволяют разрабатывать приложения с помощью масштабируемых серверных систем, размещенных в службе приложений Azure, поддерживая мобильная проверка подлинности, автономной синхронизации и Push-уведомлений. Дополнительные сведения о мобильных приложений Azure, см. в разделе [использование мобильное приложение Azure](~/xamarin-forms/data-cloud/consuming/azure.md), и [проверки подлинности пользователей с помощью мобильных приложений Azure](~/xamarin-forms/data-cloud/authentication/azure.md).

Azure Active Directory B2C — это служба управления удостоверениями для клиентских приложений, позволяющее пользователям войти в приложение с:

- С помощью их существующих учетных записей социальных сетей (Microsoft, Google, Facebook, Amazon, LinkedIn).
- Создавать новые учетные данные (адрес электронной почты и пароль, или имя пользователя и пароль). Эти учетные данные, называются *локального* учетных записей.

Дополнительные сведения о Azure Active Directory B2C, см. в разделе [проверки подлинности пользователей с помощью Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

Azure Active Directory B2C можно использовать для управления процессом проверки подлинности для мобильного приложения Azure. При таком подходе процедуру управления удостоверениями полностью определена и имеет в облаке и может изменяться без изменения кода мобильного приложения.

Существует два рабочих процесса проверки подлинности, которые могут использоваться при интеграции с клиентом Azure Active Directory B2C с экземпляром мобильных приложений Azure.

- [Управляемая клиентом](#client_managed) — в этом подход инициирует мобильного приложения Xamarin.Forms, процедура проверки подлинности с клиентом Azure Active Directory B2C и передает токен проверки подлинности полученных экземпляру мобильных приложений Azure.
- [Управляемая сервером](#server_managed) — при таком подходе мобильных приложений Azure экземпляр использует для запуска процесса проверки подлинности через рабочий процесс веб-клиента Azure Active Directory B2C.

В обоих случаях процесс проверки подлинности является предоставленное клиентом Azure Active Directory B2C. В примере приложения в результате экран входа в систему, показанный на снимках экрана ниже:

![](azure-ad-b2c-mobile-app-images/screenshots.png "Страница входа")

Вход с помощью поставщиков удостоверений в социальных сетях или локальную учетную запись, разрешены. Хотя Microsoft, Google и Facebook, используются как поставщики удостоверений социальных сетей в этом примере, можно также использовать других поставщиков удостоверений.

## <a name="setup"></a>Установка

Независимо от того, рабочий процесс проверки подлинности, используемый процесс начальной для интеграции с клиентом Azure Active Directory B2C с экземпляром мобильных приложений Azure выглядит следующим образом:

1. Создайте экземпляр мобильных приложений Azure. Дополнительные сведения см. в разделе [использование мобильное приложение Azure](~/xamarin-forms/data-cloud/consuming/azure.md).
1. Включите проверку подлинности в экземпляра мобильных приложений Azure и приложения Xamarin.Forms. Дополнительные сведения см. в разделе [проверки подлинности пользователей с помощью мобильных приложений Azure](~/xamarin-forms/data-cloud/authentication/azure.md).
1. Создание клиента Azure Active Directory B2C. Дополнительные сведения см. в разделе [проверки подлинности пользователей с помощью Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

Обратите внимание на то, что библиотеки аутентификации Майкрософт (MSAL) является обязательным при использовании рабочего процесса управляемая клиентом проверка подлинности. MSAL для выполнения проверки подлинности использует веб-браузере устройства. Это повышает удобство использования приложения, так как пользователям нужно только войти после каждого устройства, повышение ставки конвертации входа и авторизации потоки в приложении. Браузер на устройстве также обеспечивает повышенную безопасность. Как только пользователь завершит процесс проверки подлинности, элемент управления будет отправлять приложения из вкладку веб-браузера. Это достигается путем регистрации схему URL-адрес для URL-адрес перенаправления, который возвращается из процесса проверки подлинности и затем обнаружить и обработать пользовательский URL-адрес, после его отправки. Дополнительные сведения об использовании MSAL для взаимодействия с клиентом Azure Active Directory B2C, см. в разделе [проверки подлинности пользователей с помощью Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

<a name="client_managed" />

## <a name="client-managed-authentication"></a>Управляемая клиентом проверка подлинности

В управляемая клиентом проверка подлинности мобильного приложения Xamarin.Forms связывается с клиентом Azure Active Directory B2C, чтобы инициировать поток проверки подлинности. После успешного единого входа Azure Active Directory B2C клиент возвращает маркер идентификации, который затем указывается во время входа на экземпляр мобильных приложений Azure. Это позволяет приложению Xamarin.Forms для выполнения действий на экземпляре мобильных приложений Azure, требуются разрешения пользователя, прошедшего проверку подлинности.

### <a name="azure-active-directory-b2c-tenant-configuration"></a>Настройка клиента Azure Active Directory B2C

Для рабочего процесса, управляемая клиентом проверка подлинности клиента Azure Active Directory B2C следует настроить следующим образом:

- Включите собственный клиент.
- Значение URI перенаправления Custom схему URL-адрес, однозначно определяющее мобильного приложения, за которым следует `://auth/`. Дополнительные сведения о выборе схему URL-адрес, см. в разделе [Выбор URI перенаправления собственного приложения](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-native-app-redirect-uri).

Следующем снимке экрана показана эта конфигурация:

[![](azure-ad-b2c-mobile-app-images/client-flow-config-sml.png "Настройка Azure Active Directory B2C")](azure-ad-b2c-mobile-app-images/client-flow-config.png#lightbox "конфигурации Azure Active Directory B2C")

Политики, используемых в Azure Active Directory B2C клиента должны быть настроены так, что URL-адрес ответа же пользовательскую схему URL-адрес, за которым следует `://auth/`. Следующем снимке экрана показана эта конфигурация:

![](azure-ad-b2c-mobile-app-images/client-flow-policies.png "Политики Azure Active Directory B2C")

### <a name="azure-mobile-app-configuration"></a>Конфигурация мобильного приложения Azure

Для рабочего процесса, управляемая клиентом проверка подлинности экземпляра мобильных приложений Azure следует настроить следующим образом:

- Проверка подлинности службы приложений должно быть включено.
- Действие, выполняемое, когда запрос не прошел проверку подлинности должно быть присвоено **войдите с помощью Azure Active Directory**.

Следующем снимке экрана показана эта конфигурация:

![](azure-ad-b2c-mobile-app-images/client-flow-ama-config.png "Конфигурация мобильных приложений Azure")

Экземпляра мобильных приложений Azure также должны быть настроены для взаимодействия с клиентом Azure Active Directory B2C. Это можно сделать, включив **Дополнительно** режим для поставщика проверки подлинности Azure Active Directory, с помощью **идентификатор клиента** , **идентификатор приложения** функции Azure Клиент Active Directory B2C и **URL-адрес издателя** которого конечная точка метаданных для политики Azure Active Directory B2C. Следующем снимке экрана показана эта конфигурация:

![](azure-ad-b2c-mobile-app-images/client-flow-ama-advanced-config.png "Расширенная конфигурация мобильных приложений: Azure")

### <a name="signing-in"></a>Вход

В следующем примере кода показано, как для запуска рабочего процесса управляемая клиентом проверка подлинности:

```csharp
public async Task<bool> LoginAsync(bool useSilent = false)
{
    ...
    AuthenticationResult authenticationResult = await ADB2CClient.AcquireTokenAsync(
        Constants.Scopes,
        GetUserByPolicy(ADB2CClient.Users, Constants.PolicySignUpSignIn),
        App.UiParent);

    ...
    var payload = new JObject();
    payload["access_token"] = authenticationResult.IdToken;

    User = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory,
        payload);
    ...
}
```

Библиотеки аутентификации Майкрософт (MSAL) используется для запуска рабочего процесса проверки подлинности с клиентом Azure Active Directory B2C. `AcquireTokenAsync` Метод запускает веб-браузере устройства и отображает параметры проверки подлинности, определенные в политике Azure Active Directory B2C, который задается параметром политики, ссылка на через `Constants.Authority` константы. Эта политика определяет действия, которые пользователь должен выполнить во время регистрации и входа в систему и утверждения, что получить приложение при успешной регистрации или входа в систему.

Результат `AcquireTokenAsync` является вызов метода `AuthenticationResult` экземпляра. Если проверка подлинности успешна, `AuthenticationResult` маркер идентификации, который будет кэшироваться локально, будет содержать экземпляр. Если проверка подлинности завершилась неудачно, `AuthenticationResult` экземпляра будет содержать данные, который указывает на причину сбоя проверки подлинности. Сведения о том, как использовать MSAL для обмена данными с клиентом Azure Active Directory B2C, см. в разделе [проверки подлинности пользователей с помощью Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

Когда `MobileServiceClient.LoginAsync` вызова метода, экземпляра мобильных приложений Azure получает маркер идентификации, заключенное в `JObject`. Наличие допустимых маркеров означает, что экземпляр мобильных приложений Azure не требуется инициировать поток проверки подлинности OAuth 2.0. Вместо этого `MobileServiceClient.LoginAsync` возвращает `MobileServiceUser` экземпляр, который будет храниться в `MobileServiceClient.CurrentUser` свойство. Это свойство предоставляет `UserId` и `MobileServiceAuthenticationToken` свойства. Они представляют собой прошедшего проверку подлинности пользователя и маркер проверки подлинности для пользователя, который можно использовать до истечения срока его действия. Маркер проверки подлинности будут включены во все запросы к экземпляру мобильных приложений Azure, позволяя приложения Xamarin.Forms для выполнения действий на экземпляре мобильных приложений Azure, требуются разрешения пользователя, прошедшего аутентификацию.

### <a name="signing-out"></a>Выход

В следующем примере кода показан способ вызова управляемую клиентом процесс выхода:

```csharp
public async Task<bool> LogoutAsync()
{
    ...
    await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();

    foreach (var user in ADB2CClient.Users)
    {
        ADB2CClient.Remove(user);
    }
    ...
}
```

`MobileServiceClient.LogoutAsync` Метод отмены проверяет подлинность пользователя с экземпляром мобильных приложений Azure, а затем все токены проверки подлинности будут удалены из локального кэша, созданные MSAL.

<a name="server_managed" />

## <a name="server-managed-authentication"></a>Управляемая сервером проверка подлинности

В управляемая сервером проверка подлинности приложения Xamarin.Forms связывается с экземпляром мобильных приложений Azure, который использует клиент Azure Active Directory B2C для управления потоком проверки подлинности OAuth 2.0, отображая страницу входа в систему, как определено в политике B2C. После успешного входа экземпляра мобильных приложений Azure возвращает токен, который позволяет выполнять действия в экземпляре мобильных приложений Azure, требуются разрешения пользователя, прошедшего аутентификацию в приложении Xamarin.Forms.

### <a name="azure-active-directory-b2c-tenant-configuration"></a>Настройка клиента Azure Active Directory B2C

Для рабочего процесса, управляемая сервером проверка подлинности клиента Azure Active Directory B2C следует настроить следующим образом:

- Включить web app/веб-API, а также разрешить неявный поток.
- Значение URL-адрес ответа адрес мобильного приложения Azure, за которым следует `/.auth/login/aad/callback`.

Следующем снимке экрана показана эта конфигурация:

[![](azure-ad-b2c-mobile-app-images/server-flow-config-sml.png "Настройка Azure Active Directory B2C")](azure-ad-b2c-mobile-app-images/server-flow-config.png#lightbox "конфигурации Azure Active Directory B2C")

Политики, используемых в Azure Active Directory B2C клиента должны быть настроены так, что URL-адрес ответа адрес мобильного приложения Azure, за которым следует `/.auth/login/aad/callback`. Следующем снимке экрана показана эта конфигурация:

![](azure-ad-b2c-mobile-app-images/server-flow-policies.png "Политики Azure Active Directory B2C")

### <a name="azure-mobile-apps-instance-configuration"></a>Конфигурация экземпляра мобильных приложений Azure

Для рабочего процесса, управляемая сервером проверка подлинности экземпляра мобильных приложений Azure следует настроить следующим образом:

- Проверка подлинности службы приложений должно быть включено.
- Действие, выполняемое, когда запрос не прошел проверку подлинности должно быть присвоено **войдите с помощью Azure Active Directory**.

Следующем снимке экрана показана эта конфигурация:

![](azure-ad-b2c-mobile-app-images/server-flow-ama-config.png "Конфигурация мобильных приложений Azure")

Экземпляра мобильных приложений Azure также должны быть настроены для взаимодействия с клиентом Azure Active Directory B2C. Это можно сделать, включив **Дополнительно** режим для поставщика проверки подлинности Azure Active Directory, с помощью **идентификатор клиента** , **идентификатор приложения** функции Azure Клиент Active Directory B2C и **URL-адрес издателя** которого конечная точка метаданных для политики Azure Active Directory B2C. Следующем снимке экрана показана эта конфигурация:

![](azure-ad-b2c-mobile-app-images/server-flow-ama-advanced-config.png "Расширенная конфигурация мобильных приложений: Azure")

### <a name="signing-in"></a>Вход

В следующем примере кода показано, как для запуска рабочего процесса управляемая сервером проверка подлинности:

```csharp
public async Task<bool> AuthenticateAsync()
{
    ...
    MobileServiceUser user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
        UIApplication.SharedApplication.KeyWindow.RootViewController,
        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory,
        Constants.URLScheme);
    ...
}
```

Когда `MobileServiceClient.LoginAsync` вызова метода, экземпляра мобильных приложений Azure выполняет политику связанный Azure Active Directory B2C, которая инициирует процесс проверки подлинности OAuth 2.0. Обратите внимание, что каждый `AuthenticateAsync` метод зависит от платформы. Тем не менее каждый `AuthenticateAsync` использует метод `MobileServiceClient.LoginAsync` метода и указывает, что клиент Azure Active Directory будет использоваться в процессе проверки подлинности. Дополнительные сведения см. в разделе [входе пользователя](~/xamarin-forms/data-cloud/authentication/azure.md#logging-in).

`MobileServiceClient.LoginAsync` Возвращает `MobileServiceUser` экземпляр, который будет храниться в `MobileServiceClient.CurrentUser` свойство. Это свойство предоставляет `UserId` и `MobileServiceAuthenticationToken` свойства. Они представляют собой прошедшего проверку подлинности пользователя и маркер проверки подлинности для пользователя, который можно использовать до истечения срока его действия. Маркер проверки подлинности будут включены во все запросы к экземпляру мобильных приложений Azure, позволяя приложения Xamarin.Forms для выполнения действий на экземпляре мобильных приложений Azure, требуются разрешения пользователя, прошедшего аутентификацию.

### <a name="signing-out"></a>Выход

В следующем примере кода показан способ вызова управляемого сервером процесс выхода:

```csharp
public async Task<bool> LogoutAsync()
{
    ...
    await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();
    ...
}
```

`MobileServiceClient.LogoutAsync` Метод отмены проверяет подлинность пользователя с экземпляром мобильных приложений Azure. Дополнительные сведения см. в разделе [ведение журнала ожидания пользователей](~/xamarin-forms/data-cloud/authentication/azure.md#logging-out).

## <a name="summary"></a>Сводка

В этой статье было показано, как использовать Azure Active Directory B2C для предоставления проверки подлинности и авторизации к экземпляру мобильных приложений Azure с помощью Xamarin.Forms. Azure Active Directory B2C является это облаке решение управления удостоверениями для мобильных приложений и веб-потребитель.


## <a name="related-links"></a>Связанные ссылки

- [TodoAzureAuth ServerFlow (пример)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CServerFlow/)
- [TodoAzureAuth ClientFlow (пример)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CClientFlow/)
- [Использование мобильного приложения Azure](~/xamarin-forms/data-cloud/consuming/azure.md)
- [Проверка подлинности пользователей с мобильными приложениями Azure](~/xamarin-forms/data-cloud/authentication/azure.md)
- [Проверка подлинности пользователей с Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)
- [Библиотека аутентификации Майкрософт](https://www.nuget.org/packages/Microsoft.Identity.Client)
