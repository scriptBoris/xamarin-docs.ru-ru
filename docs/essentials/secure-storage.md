---
title: 'Xamarin.Essentials: Безопасное хранение'
description: Этот документ описывает класс SecureStorage в Xamarin.Essentials, что позволяет обеспечить безопасное хранение пар "ключ значение". В этом примере рассматривается использование класса, особенности реализации платформы и ограничения.
ms.assetid: 78856C0D-76BB-406E-A880-D5A3987B7D64
author: redth
ms.author: jodick
ms.date: 05/04/2018
ms.openlocfilehash: 2dfdb7051b269e73c68290a557849b9ae606c165
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353299"
---
# <a name="xamarinessentials-secure-storage"></a>Xamarin.Essentials: Безопасное хранение

![Предварительные версии NuGet](~/media/shared/pre-release.png)

**SecureStorage** класс помогает обеспечить безопасное хранение пар "ключ значение".

## <a name="getting-started"></a>Начало работы

Чтобы получить доступ к **SecureStorage** функциональные возможности, необходима следующая настройка платформы:

# <a name="androidtabandroid"></a>[Android](#tab/android)

Дополнительная настройка не требуется.

# <a name="iostabios"></a>[iOS](#tab/ios)

При разработке приложения в симуляторе iOS, включите **цепочки ключей** прав и добавьте группу цепочки ключей доступа для приложения идентификатор пакета.

Откройте **Entitlements.plist** в проект iOS и найти **цепочки ключей** прав и включите ее. Идентификатор приложения будут автоматически добавлены как группу.

В свойствах проекта в разделе **подписывание пакета iOS** задать **настраиваемые назначения** для **Entitlements.plist**.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Дополнительная настройка не требуется.

-----

## <a name="using-secure-storage"></a>С помощью защищенного хранилища

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

Для сохранения значения для заданного _ключ_ в защищенного хранилища:

```csharp
try
{
  await SecureStorage.SetAsync("oauth_token", "secret-oauth-token-value");
}
catch (Exception ex)
{
  // Possible that device doesn't support secure storage on device.
}
```

Для извлечения значения из защищенного хранилища:

```csharp
try
{
  var oauthToken = await SecureStorage.GetAsync("oauth_token");
}
catch (Exception ex)
{
  // Possible that device doesn't support secure storage on device.
}
```

> [!NOTE]
> Если нет значения, связанные с запрошенный раздел `GetAsync` вернет `null`.

Для удаления конкретного ключа, вызовите:

```csharp
SecureStorage.Remove("oauth_token");
```

Чтобы удалить все ключи, вызовите:

```csharp
SecureStorage.RemoveAll();
```


## <a name="platform-implementation-specifics"></a>Особенности реализации платформы

# <a name="androidtabandroid"></a>[Android](#tab/android)

[Хранилища ключей Android](https://developer.android.com/training/articles/keystore.html) используется для хранения шифра ключ, используемый для шифрования значения, до его сохранения в [общих настроек](https://developer.android.com/training/data-storage/shared-preferences.html) с помощью имени файла **.xamarinessentials [YOUR-APP-пакет-ID]** .  Ключ, используемый в общего файла настроек является _хэш MD5_ ключа, переданные `SecureStorage` API-интерфейсы.

## <a name="api-level-23-and-higher"></a>Уровень API 23 и более поздних версий

На более новые уровни API **AES** ключ извлекается из хранилища ключей Android и использовании **AES-GCM/NoPadding** шифрования для шифрования значения перед его сохранением в общего файла настроек.

## <a name="api-level-22-and-lower"></a>Уровень API 22 и ниже

В более старых API уровней хранилища ключей Android поддерживает только хранение **RSA** ключи, которые используются с **RSA/ECB/PKCS1Padding** шифрования для шифрования **AES** ключа (случайным образом создаются во время выполнения) и хранится в файле Общие настройки в разделе _SecureStorageKey_, если он уже не был создан.

**SecureStorage** использует [предпочтения](preferences.md) API и следующим же сохраняемости данных описано в [предпочтения](preferences.md#persistence) документации. Если устройства будут обновлены с API уровня 22 или нижний уровень API 23 и более поздних версий, этот тип шифрования будут продолжать использоваться только в случае удаления приложения или **RemoveAll** вызывается.

# <a name="iostabios"></a>[iOS](#tab/ios)

[Цепочки ключей](https://developer.xamarin.com/api/type/Security.SecKeyChain/) позволяет безопасно хранить значения на устройствах iOS.  `SecRecord` Используется для хранения значения имеет `Service` значение **.xamarinessentials [YOUR-APP-BUNDLE-ID]**.

В некоторых случаях данные цепочки ключей синхронизируется с iCloud и удаление приложения запрещается удалять безопасных значений с iCloud и другие устройства пользователя.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

[DataProtectionProvider](https://docs.microsoft.com/uwp/api/windows.security.cryptography.dataprotection.dataprotectionprovider) используется для зашифрованных значений безопасно на устройствах универсальной платформы Windows.

Зашифрованные значения хранятся в `ApplicationData.Current.LocalSettings`, внутри контейнера с именем **.xamarinessentials [YOUR-APP-ID]**.

**SecureStorage** использует [предпочтения](preferences.md) API и следующим же сохраняемости данных описано в [предпочтения](preferences.md#persistence) документации.

-----

## <a name="limitations"></a>Ограничения

Этот API предназначен для хранения небольших объемов текста.  Производительность может снизиться, если попытаться использовать для хранения больших объемов текста.

## <a name="api"></a>API

- [SecureStorage исходного кода](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/SecureStorage)
- [Документация по SecureStorage API](xref:Xamarin.Essentials.SecureStorage)
