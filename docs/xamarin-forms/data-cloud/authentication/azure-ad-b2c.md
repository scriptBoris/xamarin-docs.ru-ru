---
title: Проверка подлинности пользователей с Azure Active Directory B2C
description: Azure Active Directory B2C является это облаке решение управления удостоверениями для мобильных приложений и веб-потребитель. В этой статье показано, как интегрировать систему управления удостоверениями пользователей мобильного приложения с помощью библиотеки проверки подлинности Майкрософт и Azure Active Directory B2C.
ms.prod: xamarin
ms.assetid: B0A5DB65-0585-4A00-B908-22CCC286E6B6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: b6989782c438ec41911cc9317d9f911d6518132d
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38872725"
---
# <a name="authenticating-users-with-azure-active-directory-b2c"></a>Проверка подлинности пользователей с Azure Active Directory B2C

_Azure Active Directory B2C является это облаке решение управления удостоверениями для мобильных приложений и веб-потребитель. В этой статье показано, как интегрировать систему управления удостоверениями пользователей мобильного приложения с помощью библиотеки проверки подлинности Майкрософт и Azure Active Directory B2C._

![](~/media/shared/preview.png "Этот API в настоящее время в предварительной версии")

> [!NOTE]
> [Библиотека проверки подлинности Майкрософт](https://www.nuget.org/packages/Microsoft.Identity.Client) находится на стадии предварительной версии, но подходит для использования в рабочей среде. Тем не менее может критические изменения в API, формат внутреннего кэша и другие механизмы библиотеку, которая может повлиять на приложение.

## <a name="overview"></a>Обзор

Azure Active Directory B2C — это служба управления удостоверениями для клиентских приложений, позволяющее пользователям войти в приложение с:

- С помощью их существующих учетных записей социальных сетей (Microsoft, Google, Facebook, Amazon, LinkedIn).
- Создавать новые учетные данные (адрес электронной почты и пароль, или имя пользователя и пароль). Эти учетные данные, называются *локального* учетных записей.

Процесс для интеграции службы управления удостоверениям Azure Active Directory B2C в мобильные приложения выглядит следующим образом:

1. Создание клиента Azure Active Directory B2C. Дополнительные сведения см. в разделе [создание клиента Azure Active Directory B2C на портале Azure](/azure/active-directory-b2c/active-directory-b2c-get-started/).
1. Регистрации вашего мобильного приложения в клиенте Azure Active Directory B2C. Процесс регистрации назначает **идентификатор приложения** , однозначно определяющий приложение и **URL-адрес перенаправления** , можно использовать для направления ответов к приложению. Дополнительные сведения см. в разделе [Azure Active Directory B2C: регистрация приложения](/azure/active-directory-b2c/active-directory-b2c-app-registration/).
1. Создайте политику регистрации и входа в систему. Эта политика будет определять действия, которые пользователь должен выполнить во время регистрации и входа в систему и также определяет содержимое токенов, которые должно получить приложение при успешном регистрации или входа в систему. Дополнительные сведения см. в разделе [Azure Active Directory B2C: встроенные политики](/azure/active-directory-b2c/active-directory-b2c-reference-policies/).
1. Используйте [библиотека проверки подлинности Майкрософт](https://www.nuget.org/packages/Microsoft.Identity.Client) (MSAL) в мобильное приложение для запуска рабочего процесса проверки подлинности в клиенте Azure Active Directory B2C.

> [!NOTE]
> А также интеграции управления удостоверениями Azure Active Directory B2C в мобильные приложения, MSAL также может использоваться для интеграции управления удостоверениями Azure Active Directory в мобильных приложениях. Это можно сделать путем регистрации мобильного приложения с Azure Active Directory в [портал регистрации приложений](https://apps.dev.microsoft.com/). Процесс регистрации назначает **идентификатор приложения** , однозначно определяющий приложения, который должен быть указан с помощью MSAL. Дополнительные сведения см. в разделе [как зарегистрировать приложение с конечной точкой версии 2.0](/azure/active-directory/develop/active-directory-v2-app-registration/), и [проверки подлинности Your мобильных приложений с помощью Microsoft библиотеки проверки подлинности](https://blog.xamarin.com/authenticate-mobile-apps-using-microsoft-authentication-library/) в блоге Xamarin.

MSAL для выполнения проверки подлинности использует веб-браузере устройства. Это повышает удобство использования приложения, так как пользователям нужно только войти после каждого устройства, повышение ставки конвертации входа и авторизации потоки в приложении. Браузер на устройстве также обеспечивает повышенную безопасность. Как только пользователь завершит процесс проверки подлинности, элемент управления будет отправлять приложения из вкладку веб-браузера. Это достигается путем регистрации схему URL-адрес для URL-адрес перенаправления, который возвращается из процесса проверки подлинности и затем обнаружить и обработать пользовательский URL-адрес, после его отправки. Дополнительные сведения о выборе схему URL-адрес, см. в разделе [Выбор URI перенаправления собственного приложения](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-native-app-redirect-uri/).

> [!NOTE]
> Механизм для регистрации схему URL-адрес в операционную систему и обработке схеме конкретной платформы.

Каждый запрос, который отправляется с клиентом Azure Active Directory B2C, определяет *политики*. Политики описывают процесс идентификации пользователя, например регистрации или входа в систему. Например политику регистрации позволяет определить поведение клиента Azure Active Directory B2C можно настроить следующие параметры:

- Типы учетных записей, которые пользователи могут использовать для входа в приложение.
- Данные нужно получить от пользователя во время регистрации.
- Многофакторная проверка подлинности.
- Содержимое страницы регистрации.
- Токен утверждений, мобильное приложение получает при выполнении политики.

Клиент Azure Active Directory может содержать несколько политик различных типов, которые затем могут использоваться в приложении, при необходимости. Кроме того политики можно использовать в приложениях, позволяет определять и изменять процесс идентификации пользователя без изменения кода. Дополнительные сведения о политиках см. в разделе [Azure Active Directory B2C: встроенные политики](/azure/active-directory-b2c/active-directory-b2c-reference-policies/).

## <a name="setup"></a>Установка

Необходимо добавить к библиотеке NuGet библиотеки аутентификации Майкрософт (MSAL) для проекта переносимой библиотеки классов (PCL) и платформы проектов в решение Xamarin.Forms. Следующие разделы содержат дополнительные инструкции по установке для взаимодействия с клиентом Azure Active Directory B2C из мобильных приложений с помощью MSAL.

### <a name="portable-class-library"></a>Переносимая библиотека классов

PCL, которые используют MSAL потребуется использовать Profile7 перенаправления. Дополнительные сведения о переносимых библиотеках классов см. в разделе [Введение в переносимые библиотеки классов](~/cross-platform/app-fundamentals/pcl.md).

### <a name="ios"></a>iOS

В iOS, настраиваемой схемой URL-адрес, который был зарегистрирован с помощью Azure Active Directory B2C необходимо зарегистрировать в **Info.plist**, как показано на следующем снимке экрана:

![](azure-ad-b2c-images/customurl-ios.png "Регистрация схемы URL-адрес пользовательского в iOS")

По завершении запроса авторизации Azure Active Directory B2C перенаправляет URL-адрес зарегистрированного перенаправления. Так как URL-адрес использует схему приводит запуск мобильного приложения iOS, передачи в URL-адрес в качестве параметра запуска, где он обрабатывается `OpenUrl` переопределить приложения `AppDelegate` класс, который показан в следующем коде Пример:

```csharp
using Microsoft.Identity.Client;

namespace TodoAzure.iOS
{
    [Register("AppDelegate")]
    public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
    {
        ...
        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
            return true;
        }
    }
}
```

Код в `OpenURL` метод гарантирует, что элемент управления возвращается к MSAL, после завершения интерактивной часть рабочего процесса проверки подлинности.

### <a name="android"></a>Android

В Android настраиваемой схемой URL-адрес, который был зарегистрирован с помощью Azure Active Directory B2C необходимо зарегистрировать в **AndroidManifest.xml**, добавив `<activity>` элемент внутри существующего `<application>` элемент. `<activity>` Элемент указывает `IntentFilter` на `Activity` , обрабатывает схему и показано в следующем примере:

```xml
<application ...>
  <activity android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
      <action android:name="android.intent.action.VIEW" />
      <category android:name="android.intent.category.DEFAULT" />
      <category android:name="android.intent.category.BROWSABLE" />
      <data android:scheme="INSERT_URL_SCHEME_HERE" android:host="auth" />
    </intent-filter>
  </activity>
</application>
```

По завершении запроса авторизации Azure Active Directory B2C перенаправляет URL-адрес зарегистрированного перенаправления. Так как URL-адрес использует схему приводит к Android, запустив мобильное приложение, передачи в URL-адрес в качестве параметра запуска, где оно обрабатывается `microsoft.identity.client.BrowserTabActivity`. Обратите внимание, что `data android:scheme` свойство должно иметь значение пользовательских схему URL-адреса, который регистрируется с помощью приложения Azure Active Directory B2C.

Кроме того `MainActivity` класса должны быть изменены, как показано в следующем примере кода:

```csharp
using Microsoft.Identity.Client;

namespace TodoAzure.Droid
{
    ...
    public class MainActivity : FormsAppCompatActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);

            global::Xamarin.Forms.Forms.Init(this, bundle);
            Microsoft.WindowsAzure.MobileServices.CurrentPlatform.Init();
            LoadApplication(new App());
            App.UiParent = new UIParent(this);
        }

        protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
        {
            base.OnActivityResult(requestCode, resultCode, data);
            AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
        }
    }
}

```

`OnCreate` Метод изменяется путем назначения `UIParent` экземпляр `App.UiParent` свойство. Это гарантирует, что поток проверки подлинности происходит в контексте текущего действия.

Код в `OnActivityResult` метод гарантирует, что элемент управления возвращается к MSAL, после завершения интерактивной часть рабочего процесса проверки подлинности.

### <a name="universal-windows-platform"></a>Универсальная платформа Windows 

На универсальной платформе Windows без дополнительной настройки необходимо использовать MSAL.

## <a name="initialization"></a>Инициализация

Библиотека проверки подлинности Майкрософт используются члены `PublicClientApplication` класса для запуска рабочего процесса проверки подлинности. Пример приложения объявляются и инициализируются `public` свойство этого типа с именем `ADB2CClient`в `AuthenticationProvider` класса. В следующем примере кода показано, как это свойство инициализируется:

```csharp
ADB2CClient = new PublicClientApplication(Constants.ClientID, Constants.Authority);
```

При регистрации мобильного приложения с клиентом Azure Active Directory B2C, процесс регистрации, назначенным **идентификатор приложения**. Этот идентификатор должен быть указан в `PublicClientApplication` конструктор, вместе с `Authority` константа, которая состоит из базового URL-адреса и политики Azure Active Directory B2C для выполнения.

## <a name="signing-in"></a>Вход

На следующем снимке экрана показан экран входа в приложении-примере:

![](azure-ad-b2c-images/login.png "Страница входа")

Вход с помощью поставщиков удостоверений в социальных сетях или локальную учетную запись, разрешены. Хотя Microsoft, Google и Facebook, как показано выше, используются в качестве поставщиков удостоверений социальных сетей, можно использовать и другие поставщики удостоверений.

В следующем примере кода показано, как вызывается процесс входа в систему:

```csharp
using Microsoft.Identity.Client;

public async Task<bool> LoginAsync(bool useSilent = false)
{
    ...
    AuthenticationResult authenticationResult = await ADB2CClient.AcquireTokenAsync(
        Constants.Scopes,
        GetUserByPolicy(ADB2CClient.Users, Constants.PolicySignUpSignIn),
        App.UiParent);
    ...
}

```

`AcquireTokenAsync` Метод запускает веб-браузере устройства и отображает параметры проверки подлинности, определенные в политике Azure Active Directory B2C, который задается параметром политики, ссылка на через `Constants.Authority` константы. Эта политика определяет действия, которые пользователь должен выполнить во время регистрации и входа в систему и утверждения, что получить приложение при успешной регистрации или входа в систему.

Результат `AcquireTokenAsync` является вызов метода `AuthenticationResult` экземпляра. Если проверка подлинности успешна, `AuthenticationResult` маркер идентификации, который будет кэшироваться локально, будет содержать экземпляр. Если проверка подлинности завершилась неудачно, `AuthenticationResult` экземпляра будет содержать данные, который указывает на причину сбоя проверки подлинности.

В примере приложения, если проверка подлинности успешна `TodoList` перемещении по странице.

## <a name="silent-re-authentication"></a>Автоматическая повторная проверка подлинности

Когда `LoginPage` в образце приложения на экране предпринята попытка получить маркер пользователя без отображения пользовательского интерфейса проверки подлинности. Это достигается за счет `AcquireTokenSilentAsync` метод, как показано в следующем примере кода:

```csharp
public async Task<bool> LoginAsync(bool useSilent = false)
{
    ...
    AuthenticationResult authenticationResult;

    if (useSilent)
    {
        authenticationResult = await ADB2CClient.AcquireTokenSilentAsync(
            Constants.Scopes,
            GetUserByPolicy(ADB2CClient.Users, Constants.PolicySignUpSignIn),
            Constants.Authority,
            false);
    }
    ...
}
```

`AcquireTokenSilentAsync` Метод пытается получить маркер пользователя из кэша, не требуя от пользователя для входа в систему. Это обрабатывает сценарий, где маркер подходит могут уже присутствовать в кэше из предыдущих сеансов. При успешном выполнении, попытка получить маркер `TodoList` перемещении по странице. Если для получения маркера не удалось, ничего не происходит, и пользователю будет предоставлена возможность для запуска нового рабочего процесса проверки подлинности.

## <a name="signing-out"></a>Выход

В следующем примере кода показано, как вызывается процесс выхода:

```csharp
public async Task<bool> LogoutAsync()
{
    ...
    foreach (var user in ADB2CClient.Users)
    {
        ADB2CClient.Remove(user);
    }
    ...
}
```

Это очищает все токены проверки подлинности из локального кэша.

## <a name="summary"></a>Сводка

В этой статье было показано, как интегрировать систему управления удостоверениями пользователей мобильного приложения с помощью библиотеки аутентификации Майкрософт (MSAL) и Azure Active Directory B2C. Azure Active Directory B2C является это облаке решение управления удостоверениями для мобильных приложений и веб-потребитель.


## <a name="related-links"></a>Связанные ссылки

- [AzureADB2CAuth (пример)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureADB2CAuth/)
- [Azure Active Directory B2C](/azure/active-directory-b2c/)
- [Библиотека аутентификации Майкрософт](https://www.nuget.org/packages/Microsoft.Identity.Client)
