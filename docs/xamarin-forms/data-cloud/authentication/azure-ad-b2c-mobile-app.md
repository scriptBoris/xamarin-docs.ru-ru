---
title: Интеграция Azure Active Directory B2C с Azure мобильных приложений
description: Azure Active Directory B2C — это Облачное решение управления удостоверения для веб-потребительском и приложений для мобильных устройств. В этой статье показано, как использовать Azure Active Directory B2C для проверки подлинности и авторизации к экземпляру мобильных приложений Azure с помощью Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 53F52036-A997-4D0F-86B4-4302C6913136
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: cafc1e78779dc393fa0409daa08b3daa8948a1ee
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30787978"
---
# <a name="integrating-azure-active-directory-b2c-with-azure-mobile-apps"></a>Интеграция Azure Active Directory B2C с Azure мобильных приложений

_Azure Active Directory B2C — это Облачное решение управления удостоверения для веб-потребительском и приложений для мобильных устройств. В этой статье показано, как использовать Azure Active Directory B2C для проверки подлинности и авторизации к экземпляру мобильных приложений Azure с помощью Xamarin.Forms._

![](~/media/shared/preview.png "Этот API является в настоящее время в предварительной версии")

> [!NOTE]
> [Библиотеки проверки подлинности Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client) находится на стадии предварительной версии, но подходит для использования в рабочей среде. Тем не менее может критически важных изменений в API, формат внутреннего кэша и другие механизмы библиотеку, которая может повлиять на приложения.

## <a name="overview"></a>Обзор

Мобильные приложения Azure позволяют разрабатывать приложения с масштабируемой внутренних серверов, размещенных в службе приложений Azure, с поддержкой проверки подлинности мобильных устройств, автономной синхронизации и push-уведомлений. Дополнительные сведения о мобильных приложений Azure см. в разделе [использование мобильного приложения Azure](~/xamarin-forms/data-cloud/consuming/azure.md), и [проверки подлинности пользователей с помощью мобильных приложений Azure](~/xamarin-forms/data-cloud/authentication/azure.md).

Azure Active Directory B2C является служба управления идентификацией для потребительском приложений, который позволяет пользователю войти в приложение с:

- С помощью существующего социальных учетные записи (Microsoft, Google, Facebook, Amazon, LinkedIn).
- Создание новых учетных данных (адрес электронной почты и пароль, или имя пользователя и пароль). Эти учетные данные, называются *локального* учетные записи.

Дополнительные сведения об Azure Active Directory B2C см. в разделе [проверки подлинности пользователей с Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

Azure Active Directory B2C может использоваться для управления процессом проверки подлинности для мобильного приложения Azure. При таком подходе процедуру управления удостоверениями полностью определен в облаке и может изменяться без изменения кода приложений для мобильных устройств.

Существует два рабочих процесса проверки подлинности, может быть использована при интеграции клиента Azure Active Directory B2C с экземпляром мобильных приложений Azure.

- [Управляемый клиент](#client_managed) — в это приближаться к отметке инициирует Xamarin.Forms мобильного приложения, с клиентом Azure Active Directory B2C процедура проверки подлинности и передает токен проверки подлинности полученных экземпляру мобильных приложений Azure.
- [Сервер под](#server_managed) — при таком подходе мобильных приложений Azure экземпляр использует для запуска процесса проверки подлинности через рабочий процесс веб-клиента Azure Active Directory B2C.

В обоих случаях взаимодействие по проверке подлинности обеспечивается клиента Azure Active Directory B2C. В примере приложения это приводит к на экране входа в систему, показано на следующем снимке экрана:

![](azure-ad-b2c-mobile-app-images/screenshots.png "Страница входа")

Вход с поставщиками удостоверений из социальных сетей, или с учетной записью, разрешены. Microsoft, Google и Facebook, используются как поставщики удостоверений из социальных сетей в этом примере, другие поставщики удостоверений можно также использовать.

## <a name="setup"></a>Установка

Независимо от того, рабочий процесс проверки подлинности используется начальной интеграции клиента Azure Active Directory B2C с экземпляром мобильных приложений Azure выполняется следующим образом:

1. Создайте экземпляр мобильных приложений Azure. Дополнительные сведения см. в разделе [использование мобильного приложения Azure](~/xamarin-forms/data-cloud/consuming/azure.md).
1. Включите проверку подлинности в экземпляре Azure мобильные приложения и приложения Xamarin.Forms. Дополнительные сведения см. в разделе [проверки подлинности пользователей с помощью мобильных приложений Azure](~/xamarin-forms/data-cloud/authentication/azure.md).
1. Создание клиента Azure Active Directory B2C. Дополнительные сведения см. в разделе [проверки подлинности пользователей с Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

Обратите внимание, что библиотека проверки подлинности Microsoft (MSAL) требуется при использовании рабочего потока управляемой клиентом проверки подлинности. MSAL использует веб-браузере устройства для проверки подлинности. Это повышает удобство использования приложения, как пользователям нужно только вход после каждого устройства, повышение скорости преобразования входа и авторизации потоки в приложении. Обозреватель устройства также обеспечивает повышенную безопасность. Пользователь не завершит процесс проверки подлинности, элемент управления будет возвращать приложению из вкладку веб-браузера. Это достигается путем регистрации пользовательской схемы URL-адрес для перенаправления URL-адрес, возвращенный процесс проверки подлинности, а затем обнаружение и обработка пользовательский URL-адрес после отправки. Дополнительные сведения об использовании MSAL для взаимодействия с клиентом Azure Active Directory B2C см. в разделе [проверки подлинности пользователей с Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

<a name="client_managed" />

## <a name="client-managed-authentication"></a>Управляемый клиент проверки подлинности

В управляемой клиентом проверки подлинности мобильного приложения Xamarin.Forms обращается клиент Azure Active Directory B2C для инициации потока проверки подлинности. После успешного входа в Azure Active Directory B2C клиента возвращает токен удостоверения, который затем предоставляется во время входа на экземпляр мобильных приложений Azure. Это позволяет приложению Xamarin.Forms для выполнения действий на экземпляре мобильных приложений Azure, требует разрешения пользователя, прошедшего проверку подлинности.

### <a name="azure-active-directory-b2c-tenant-configuration"></a>Конфигурация клиента Azure Active Directory B2C

В рабочем процессе управляемой клиентом проверки подлинности клиента Azure Active Directory B2C настраивается следующим образом:

- Включить собственный клиент.
- Задать URI перенаправления, настраиваемый в схему URL-адрес, однозначно определяющее мобильного приложения, за которым следует `://auth/`. Дополнительные сведения о выборе пользовательской схемы URL-адрес см. в разделе [Выбор URI перенаправления собственного приложения](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-native-app-redirect-uri).

Следующий снимок экрана демонстрирует эту конфигурацию:

[![](azure-ad-b2c-mobile-app-images/client-flow-config-sml.png "Настройка Azure Active Directory B2C")](azure-ad-b2c-mobile-app-images/client-flow-config.png#lightbox "конфигурации Azure Active Directory B2C")

Политики, используемых в Azure Active Directory B2C клиента также должны быть настроены так, что URL-адрес ответа же пользовательскую схему URL-адрес, за которым следует `://auth/`. Следующий снимок экрана демонстрирует эту конфигурацию:

![](azure-ad-b2c-mobile-app-images/client-flow-policies.png "Политики Azure Active Directory B2C")

### <a name="azure-mobile-app-configuration"></a>Конфигурация Azure мобильного приложения

В рабочем процессе управляемой клиентом проверки подлинности экземпляра мобильных приложений Azure настраивается следующим образом:

- Проверка подлинности службы приложений должно быть включено.
- Действие, выполняемое, если запрос не прошел проверку подлинности должно быть присвоено **войти с использованием Azure Active Directory**.

Следующий снимок экрана демонстрирует эту конфигурацию:

![](azure-ad-b2c-mobile-app-images/client-flow-ama-config.png "Конфигурация мобильного приложения Azure")

Экземпляр мобильных приложений Azure также должны быть настроены для взаимодействия с клиентом Azure Active Directory B2C. Это можно сделать, включив **Дополнительно** режим для поставщика проверки подлинности Azure Active Directory, с **идентификатор клиента** , **идентификатор приложения** функции Azure Active Directory B2C клиента и **URL-адрес издателя** выполняется конечную точку метаданных для Azure Active Directory B2C политики. Следующий снимок экрана демонстрирует эту конфигурацию:

![](azure-ad-b2c-mobile-app-images/client-flow-ama-advanced-config.png "Расширенная настройка Azure мобильных приложений")

### <a name="signing-in"></a>Вход

В следующем примере кода показано, как запустить рабочий процесс управляемой клиентом проверки подлинности:

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

Библиотека проверки подлинности Microsoft (MSAL) используется для запуска рабочего процесса проверки подлинности с клиентом Azure Active Directory B2C. `AcquireTokenAsync` Метод запускает веб-браузере устройства и отображает параметры проверки подлинности, определенные в политике Azure Active Directory B2C, который указан в политике для ссылок на `Constants.Authority` константой. Эта политика определяет действия, необходимые, потребители проходит через во время регистрации и входа в систему и утверждений, приложение получит в успешной регистрации или входа в.

Результат `AcquireTokenAsync` вызов метода `AuthenticationResult` экземпляра. Если проверка подлинности прошла успешно, `AuthenticationResult` экземпляр будет содержать токен удостоверения, в которой будут кэширован локально. Если проверка подлинности завершается неудачно, `AuthenticationResult` экземпляр будет содержать данные о том, почему не удалось выполнить проверку подлинности. Сведения о том, как использовать MSAL для обмена данными с клиентом Azure Active Directory B2C см. в разделе [проверки подлинности пользователей с Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

Когда `MobileServiceClient.LoginAsync` вызывается метод, токен идентификации, заключенное в получении экземпляром мобильных приложений Azure `JObject`. Наличие допустимый токен означает, что экземпляр мобильных приложений Azure не требуется инициировать свой собственный поток проверки подлинности OAuth 2.0. Вместо этого `MobileServiceClient.LoginAsync` возвращает `MobileServiceUser` экземпляр, который будет храниться в `MobileServiceClient.CurrentUser` свойство. Это свойство предоставляет `UserId` и `MobileServiceAuthenticationToken` свойства. Они представляют, прошедшего проверку подлинности пользователя и маркер проверки подлинности для пользователя, который может использоваться до истечения срока его действия. Токен проверки подлинности будут включены во всех запросах к экземпляру мобильных приложений Azure, позволяя Xamarin.Forms приложению выполнять действия на экземпляре мобильных приложений Azure, требуются разрешения пользователя, прошедшего проверку подлинности.

### <a name="signing-out"></a>Выход

В следующем примере кода показано, как вызывается процесс выхода управляемого клиента:

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

## <a name="server-managed-authentication"></a>Управляемый сервер проверки подлинности

При проверке подлинности, управляемого сервером приложение Xamarin.Forms обращается экземпляр мобильных приложений Azure, который использует для управления поток проверки подлинности OAuth 2.0, отобразив страницу входа, определенный в политике B2C клиента Azure Active Directory B2C. После успешного входа экземпляр мобильных приложений Azure возвращает маркер, который позволяет выполнять действия на экземпляре мобильных приложений Azure, требуются разрешения пользователя, прошедшего проверку подлинности в приложении Xamarin.Forms.

### <a name="azure-active-directory-b2c-tenant-configuration"></a>Конфигурация клиента Azure Active Directory B2C

В рабочем процессе управляемого сервером проверки подлинности клиента Azure Active Directory B2C настраивается следующим образом:

- Включать web app и веб-API и разрешить неявного потока.
- Задать URL-адрес ответа на адрес мобильного приложения Azure, а затем `/.auth/login/aad/callback`.

Следующий снимок экрана демонстрирует эту конфигурацию:

[![](azure-ad-b2c-mobile-app-images/server-flow-config-sml.png "Настройка Azure Active Directory B2C")](azure-ad-b2c-mobile-app-images/server-flow-config.png#lightbox "конфигурации Azure Active Directory B2C")

Политики, используемых в Azure Active Directory B2C клиента также должны быть настроены так, что URL-адрес ответа адрес мобильного приложения Azure, за которым следует `/.auth/login/aad/callback`. Следующий снимок экрана демонстрирует эту конфигурацию:

![](azure-ad-b2c-mobile-app-images/server-flow-policies.png "Политики Azure Active Directory B2C")

### <a name="azure-mobile-apps-instance-configuration"></a>Конфигурация экземпляра Azure мобильных приложений

Для рабочего процесса, управляемого сервером проверки подлинности мобильных приложений Azure экземпляра должно быть настроено следующим образом:

- Проверка подлинности службы приложений должно быть включено.
- Действие, выполняемое, если запрос не прошел проверку подлинности должно быть присвоено **войти с использованием Azure Active Directory**.

Следующий снимок экрана демонстрирует эту конфигурацию:

![](azure-ad-b2c-mobile-app-images/server-flow-ama-config.png "Конфигурация мобильного приложения Azure")

Экземпляр мобильных приложений Azure также должны быть настроены для взаимодействия с клиентом Azure Active Directory B2C. Это можно сделать, включив **Дополнительно** режим для поставщика проверки подлинности Azure Active Directory, с **идентификатор клиента** , **идентификатор приложения** функции Azure Active Directory B2C клиента и **URL-адрес издателя** выполняется конечную точку метаданных для Azure Active Directory B2C политики. Следующий снимок экрана демонстрирует эту конфигурацию:

![](azure-ad-b2c-mobile-app-images/server-flow-ama-advanced-config.png "Расширенная настройка Azure мобильных приложений")

### <a name="signing-in"></a>Вход

В следующем примере кода показано, как запустить рабочий процесс проверки подлинности, управляемого сервером:

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

Когда `MobileServiceClient.LoginAsync` вызывается метод, экземпляр мобильных приложений Azure выполняет связанный Azure Active Directory B2C политику, которая инициирует поток проверки подлинности OAuth 2.0. Обратите внимание, что каждый `AuthenticateAsync` метод зависит от платформы. Однако каждый `AuthenticateAsync` использует метод `MobileServiceClient.LoginAsync` метод и указывает, что клиент Azure Active Directory будет использоваться в процессе проверки подлинности. Дополнительные сведения см. в разделе [вход пользователей](~/xamarin-forms/data-cloud/authentication/azure.md#logging-in).

`MobileServiceClient.LoginAsync` Возвращает `MobileServiceUser` экземпляр, который будет храниться в `MobileServiceClient.CurrentUser` свойство. Это свойство предоставляет `UserId` и `MobileServiceAuthenticationToken` свойства. Они представляют, прошедшего проверку подлинности пользователя и маркер проверки подлинности для пользователя, который может использоваться до истечения срока его действия. Токен проверки подлинности будут включены во всех запросах к экземпляру мобильных приложений Azure, позволяя Xamarin.Forms приложению выполнять действия на экземпляре мобильных приложений Azure, требуются разрешения пользователя, прошедшего проверку подлинности.

### <a name="signing-out"></a>Выход

В следующем примере кода показано, как вызывается процесс выхода управляемого сервером:

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

В этой статье показано, как использовать Azure Active Directory B2C для проверки подлинности и авторизации к экземпляру мобильных приложений Azure с помощью Xamarin.Forms. Azure Active Directory B2C — это Облачное решение управления удостоверения для веб-потребительском и приложений для мобильных устройств.


## <a name="related-links"></a>Связанные ссылки

- [TodoAzureAuth ServerFlow (пример)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CServerFlow/)
- [TodoAzureAuth ClientFlow (пример)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CClientFlow/)
- [Использование мобильного приложения Azure](~/xamarin-forms/data-cloud/consuming/azure.md)
- [Проверка подлинности пользователей с помощью Azure мобильных приложений](~/xamarin-forms/data-cloud/authentication/azure.md)
- [Проверка подлинности пользователей с Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)
- [Библиотека проверки подлинности Майкрософт](https://www.nuget.org/packages/Microsoft.Identity.Client)
