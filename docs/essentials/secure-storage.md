---
title: 'Xamarin.Essentials: Надежное хранение'
description: Этот документ описывает класс SecureStorage в Xamarin.Essentials, что позволяет обеспечить безопасное хранение пар «ключ значение». В этом примере рассматривается использование класса, особенностей реализации платформы и ограничения.
ms.assetid: 78856C0D-76BB-406E-A880-D5A3987B7D64
author: redth
ms.author: jodick
ms.date: 05/04/2018
ms.openlocfilehash: df2aa1fd23976e8db34d7c466317a8630408af7a
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080356"
---
# <a name="xamarinessentials-secure-storage"></a>Xamarin.Essentials: Надежное хранение

![Предварительная версия NuGet](~/media/shared/pre-release.png)

**SecureStorage** класс помогает обеспечить безопасное хранение пар «ключ значение».

## <a name="getting-started"></a>Начало работы

Чтобы получить доступ к **SecureStorage** функциональные возможности, требуется следующая настройка платформ:

# <a name="androidtabandroid"></a>[Android](#tab/android)

Дополнительная настройка не требуется.

# <a name="iostabios"></a>[iOS](#tab/ios)

При разработке приложения в симуляторе iOS, включите **цепочки ключей** правах и добавить группу доступа к цепочке ключей для идентификатора пакета приложения.

Откройте **Entitlements.plist** в проекте iOS и найти **цепочки ключей** правах и включите ее. Идентификатор приложения автоматически добавляются в группу.

В свойствах проекта в разделе **подписывание пакета iOS** задать **пользовательские права** для **Entitlements.plist**.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Дополнительная настройка не требуется.

-----

## <a name="using-secure-storage"></a>С помощью защищенного хранилища

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

Чтобы сохранить значение для данного _ключ_ в защищенного хранилища:

```csharp
await SecureStorage.SetAsync("oauth_token", "secret-oauth-token-value");
```

Чтобы получить значение из защищенного хранилища.

```csharp
var oauthToken = await SecureStorage.GetAsync("oauth_token");
```

Для удаления определенного ключа, вызовите:

```csharp
SecureStorage.Remove("oauth_token");
```

Чтобы удалить все ключи, вызовите:

```csharp
SecureStorage.RemoveAll();
```


## <a name="platform-implementation-specifics"></a>Особенности реализации платформы

# <a name="androidtabandroid"></a>[Android](#tab/android)

[Android ключей](https://developer.android.com/training/articles/keystore.html) используется для сохранения ключа шифрования для шифрования значение перед сохранением в [общих настроек](https://developer.android.com/training/data-storage/shared-preferences.html) с именем файла из **.xamarinessentials [YOUR--ПАКЕТА-идентификатор приложения]** .  Ключ, используемый в файле Общие настройки является _хэш MD5_ ключа, переданные в `SecureStorage` API-интерфейса.

## <a name="api-level-23-and-higher"></a>API уровня 23 и выше

На новые уровни API **AES** ключа можно было получить из хранилища ключей Android и использовать с **AES, GCM и NoPadding** шифр, чтобы зашифровать значение, прежде чем оно сохранено в файле Общие настройки.

## <a name="api-level-22-and-lower"></a>Уровень API 22 и ниже

На более старые уровни API Android хранилище ключей поддерживает только хранение **RSA** ключи, которые используются с **RSA, ECB/PKCS1Padding** шифрования для шифрования **AES** ключа (случайным образом создаются во время выполнения) и хранится в файле Общие настройки в разделе _SecureStorageKey_, если он уже не был создан.

Все зашифрованные значения будут удалены, когда приложение удаляется с устройства.

# <a name="iostabios"></a>[iOS](#tab/ios)

[Цепочки ключей](https://developer.xamarin.com/api/type/Security.SecKeyChain/) используется для безопасного хранения значений на устройствах iOS.  `SecRecord` Используется для хранения значения имеет `Service` значение **.xamarinessentials [YOUR--ПАКЕТА-идентификатор приложения]**.

В некоторых случаях данные цепочки ключей синхронизируется с iCloud и после удаления приложение может не удаляются безопасные значения iCloud и другие устройства пользователя.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

[DataProtectionProvider](https://docs.microsoft.com/uwp/api/windows.security.cryptography.dataprotection.dataprotectionprovider) используется для значения encryped безопасно на устройствах UWP.

Encryped значения хранятся в `ApplicationData.Current.LocalSettings`, внутри контейнера с именем **.xamarinessentials [идентификатор ВАШЕГО приложения]**.

После удаления приложения _LocalSettings_и все зашифрованные значения также удаляются.

-----

## <a name="limitations"></a>Ограничения

Этот API предназначен для хранения небольших объемов текста.  Производительность может снизиться при попытке использовать для хранения больших объемов текста.

## <a name="api"></a>API

- [SecureStorage исходного кода](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/SecureStorage)
- [Документация по SecureStorage API](xref:Xamarin.Essentials.SecureStorage)
