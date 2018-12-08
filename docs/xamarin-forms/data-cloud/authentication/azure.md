---
title: Проверка подлинности пользователей с мобильными приложениями Azure
description: В этой статье объясняется, как использовать мобильные приложения Azure для управления процессом проверки подлинности в приложении Xamarin.Forms.
ms.prod: xamarin
ms.assetid: D50D6F56-8B19-44E7-81F3-E0E1C6E240
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/02/2017
ms.openlocfilehash: 428e536d6895ff16a928f8cc40a8a7976d087471
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53060495"
---
# <a name="authenticating-users-with-azure-mobile-apps"></a>Проверка подлинности пользователей с мобильными приложениями Azure

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuth/)

_Мобильные приложения Azure используют различных внешних поставщиков удостоверений для поддержки проверки подлинности и авторизации пользователей приложения, включая Facebook, Google, Майкрософт, Twitter и Azure Active Directory. Разрешения могут устанавливаться в таблицах, чтобы ограничить доступ только пользователям, прошедшим проверку подлинности. В этой статье объясняется, как использовать мобильные приложения Azure для управления процессом проверки подлинности в приложении Xamarin.Forms._

## <a name="overview"></a>Обзор

Процесс использования мобильных приложений Azure управлять процессом проверки подлинности в приложении Xamarin.Forms выглядит следующим образом:

1. Регистрация мобильного приложения Azure на сайте поставщика удостоверений и задайте созданные поставщиком учетные данные в серверной части мобильных приложений. Дополнительные сведения см. в разделе [Регистрация приложения для проверки подлинности и настройка служб приложений](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#register-your-app-for-authentication-and-configure-app-services).
1. Определите новую схему URL-адрес для приложения Xamarin.Forms, что позволяет системе аутентификации выполнять перенаправление обратно в приложение Xamarin.Forms по завершении процесса проверки подлинности. Дополнительные сведения см. в разделе [Добавление приложения в разрешен внешний URL-адреса перенаправления](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#redirecturl).
1. Ограничение доступа к серверной части мобильных приложений Azure, чтобы только прошедшим проверку подлинности клиентов. Дополнительные сведения см. в разделе [ограничить разрешения пользователям, прошедшим проверку](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#restrict-permissions-to-authenticated-users).
1. Вызов проверки подлинности из приложения Xamarin.Forms. Дополнительные сведения см. в разделе [Добавление проверки подлинности переносимой библиотеки классов](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-the-portable-class-library), [Добавление проверки подлинности в приложение iOS](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-the-ios-app), [Добавление проверки подлинности в приложение Android](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-the-android-app)и [ Добавление проверки подлинности в проекты приложений Windows 10](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-windows-10-including-phone-app-projects).

> [!NOTE]
> В iOS 9 и более поздней версии приложение Transport Security (ATS) обеспечивает безопасных соединений между Интернет-ресурсов (например, server серверной части приложения) и приложения, тем самым предотвращая случайное раскрытие конфиденциальной информации. Поскольку ATS включена по умолчанию в приложениях, созданных для iOS 9, все подключения будут применяться требования к безопасности ATS. Если соединения не удовлетворяют этим требованиям, они вызовут сбой с исключением.
> Если невозможно использовать из могут быть присоединены ATS `HTTPS` протокола и безопасный обмен данными для Интернет-ресурсов. Это достигается путем обновления приложения **Info.plist** файла. Дополнительные сведения см. в разделе [безопасность транспорта приложения](~/ios/app-fundamentals/ats.md).

Исторически сложилось так, что мобильные приложения использовали embedded веб-представления для проверки подлинности с помощью поставщика удостоверений. Это больше не рекомендуется по следующим причинам:

- Приложения, на котором размещена веб-представлении можно получить доступ к полной проверки подлинности учетных данных пользователя, не только предоставления авторизации, предназначенного для приложения. Это нарушает принцип наименьших прав доступа, эти приложения имеют доступ к более мощные учетные данные, чем это необходимо, потенциально увеличивать поверхность атаки приложения.
- Ведущее приложение может записать имена пользователей и пароли, автоматически отправлять формы и обходить согласия пользователя и скопируйте файлы cookie сеанса и использовать их для выполнения действий, прошедшего проверку подлинности как пользователь.
- Embedded веб-представления не находиться в состоянии проверки подлинности с другим приложениям или веб-браузере устройства, требующее от пользователя для входа в систему для каждого запроса авторизации, которое считается взаимодействие с пользователем плох.
- Несколько конечных точек авторизации действий обнаружения и блокировки, полученные из веб-представлений запросов на авторизацию.

Альтернативой является использование веб-браузере устройства для проверки подлинности, который является подход используется в последнюю версию [пакета SDK Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/). С помощью обозревателя устройств для проверки подлинности запросов повышает удобство использования приложения, так как пользователям нужно только войти в поставщик удостоверений один раз в устройство, повышение скорости преобразования потоков входа и авторизации в приложении. Браузер на устройстве также обеспечивает повышенную безопасность, как приложения могут проверять и изменять содержимое в веб-представление, но не содержимое, отображаемое в браузере.

## <a name="using-an-azure-mobile-apps-instance"></a>С помощью экземпляра мобильных приложений Azure

[Пакета SDK Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) предоставляет `MobileServiceClient` класс, который используется приложением Xamarin.Forms для доступа к экземпляру мобильных приложений Azure.

Пример приложения использует Google как поставщика удостоверений, который позволяет пользователям с учетными записями Google для входа в приложение Xamarin.Forms. Хотя Google используется в качестве поставщика удостоверений в этой статье, этот подход также применима и для других поставщиков удостоверений.

<a name="logging-in" />

### <a name="logging-in-users"></a>Вход пользователей

На следующем снимке экрана показан экран входа в приложении-примере:

![](azure-images/login.png "Страница входа")

Google используется в качестве поставщика удостоверений, ряд других поставщиков удостоверений можно использовать, включая Facebook, Майкрософт, Twitter и Azure Active Directory.

В следующем примере кода показано, как вызывается процесс входа в систему:

```csharp
async void OnLoginButtonClicked(object sender, EventArgs e)
{
  ...
  if (App.Authenticator != null)
  {
    authenticated = await App.Authenticator.AuthenticateAsync();
  }
  ...
}
```

`App.Authenticator` Свойство `IAuthenticate` экземпляр, который задается параметром каждого проекта под конкретную платформу. `IAuthenticate` Интерфейс задает `AuthenticateAsync` операцию, которая должна предоставлять каждой платформы проекта. Следовательно, вызов `App.Authenticator.AuthenticateAsync` выполняет метод `IAuthenticate.AuthenticateAsync` метода в проекте платформы.

Все платформы `IAuthenticate.AuthenticateAsync` вызов методов `MobileServiceClient.LoginAsync` метод для отображения имени входа интерфейса и кэш данных. В следующем коде показано в примере `LoginAsync` метод для платформы iOS:

```csharp
public async Task<bool> AuthenticateAsync()
{
  ...
  // The authentication provider could also be Facebook, Twitter, or Microsoft
  user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
    UIApplication.SharedApplication.KeyWindow.RootViewController,
    MobileServiceAuthenticationProvider.Google,
    Constants.URLScheme);
  ...
}
```

В следующем коде показано в примере `LoginAsync` метод для платформы Android:

```csharp
public async Task<bool> AuthenticateAsync()
{
  ...
  // The authentication provider could also be Facebook, Twitter, or Microsoft
  user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
    this,
    MobileServiceAuthenticationProvider.Google,
    Constants.URLScheme);
  ...
}
```

В следующем коде показано в примере `LoginAsync` метод для универсальной платформы Windows:

```csharp
public async Task<bool> AuthenticateAsync()
{
  ...
  // The authentication provider could also be Facebook, Twitter, or Microsoft
  user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
    MobileServiceAuthenticationProvider.Google,
    Constants.URLScheme);
  ...
}
```

На всех платформах `MobileServiceAuthenticationProvider` перечисление используется для указания поставщика удостоверений, который будет использоваться в процессе проверки подлинности. Когда `MobileServiceClient.LoginAsync` вызова метода, мобильных приложений Azure инициирует поток проверки подлинности, отображая страницу входа выбранного поставщика и создания маркера проверки подлинности после успешного входа в систему с поставщиком удостоверений. `MobileServiceClient.LoginAsync` Возвращает `MobileServiceUser` экземпляр, который будет храниться в `MobileServiceClient.CurrentUser` свойство. Это свойство предоставляет `UserId` и `MobileServiceAuthenticationToken` свойства. Они представляют собой прошедшего проверку подлинности пользователя и маркер проверки подлинности для пользователя. Маркер проверки подлинности будут включены во все запросы к экземпляру мобильных приложений Azure, позволяя приложения Xamarin.Forms для выполнения действий на экземпляре мобильного приложения Azure, требуются разрешения пользователя, прошедшего аутентификацию.

<a name="logging-out" />

### <a name="logging-out-users"></a>Выход пользователей

В следующем примере кода показано, как вызывается процесс выхода:

```csharp
async void OnLogoutButtonClicked(object sender, EventArgs e)
{
  bool loggedOut = false;

  if (App.Authenticator != null)
  {
    loggedOut = await App.Authenticator.LogoutAsync ();
  }
  ...
}
```

`App.Authenticator` Свойство `IAuthenticate` экземпляра, которое устанавливается с каждой platformproject. `IAuthenticate` Интерфейс задает `LogoutAsync` операцию, которая должна предоставлять каждой платформы проекта. Следовательно, вызов `App.Authenticator.LogoutAsync` выполняет метод `IAuthenticate.LogoutAsync` метода в проекте платформы.

Все платформы `IAuthenticate.LogoutAsync` вызов методов `MobileServiceClient.LogoutAsync` способ снятие проверки подлинности пользователя вошедшего в систему с поставщиком удостоверений. В следующем коде показано в примере `LogoutAsync` метод для платформы iOS:

```csharp
public async Task<bool> LogoutAsync()
{
  ...
  foreach (var cookie in NSHttpCookieStorage.SharedStorage.Cookies)
  {
    NSHttpCookieStorage.SharedStorage.DeleteCookie (cookie);
  }
  await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();
  ...
}
```

В следующем коде показано в примере `LogoutAsync` метод для платформы Android:

```csharp
public async Task<bool> LogoutAsync()
{
  ...
  CookieManager.Instance.RemoveAllCookie();
  await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();
  ...
}
```

В следующем коде показано в примере `LogoutAsync` метод для универсальной платформы Windows:

```csharp
public async Task<bool> LogoutAsync()
{
  ...
  await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();
  ...
}
```

Когда `IAuthenticate.LogoutAsync` вызова метода, очищаются все файлов cookie, установленных с помощью поставщика удостоверений, прежде чем `MobileServiceClient.LogoutAsync` метод вызывается для отмены проверки подлинности пользователя вошедшего в систему с поставщиком удостоверений.

## <a name="summary"></a>Сводка

В этой статье было рассмотрено, как использовать мобильные приложения Azure для управления процессом проверки подлинности в приложении Xamarin.Forms. Мобильные приложения Azure используют различных внешних поставщиков удостоверений для поддержки проверки подлинности и авторизации пользователей приложения, включая Facebook, Google, Майкрософт, Twitter и Azure Active Directory. `MobileServiceClient` Класс используется для приложения Xamarin.Forms для управления доступом к экземпляру мобильных приложений Azure.


## <a name="related-links"></a>Связанные ссылки

- [TodoAzureAuth (пример)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuth/)
- [Использование мобильного приложения Azure](~/xamarin-forms/data-cloud/consuming/azure.md)
- [Добавление проверки подлинности в приложение Xamarin.Forms](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users/)
- [Azure SDK для мобильного клиента](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)
