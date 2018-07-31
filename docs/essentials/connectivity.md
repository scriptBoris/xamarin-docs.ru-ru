---
title: 'Xamarin.Essentials: подключение'
description: Класс подключения в Xamarin.Essentials позволяет отслеживать наличие изменений в условия сети устройства, проверьте текущий доступ к сети, и как оно сейчас подключено.
ms.assetid: E1B1F152-B1D5-4227-965E-C0AEBF528F49
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 96b4ee0487034c651bec1dfb168fed7567b63c96
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353702"
---
# <a name="xamarinessentials-connectivity"></a>Xamarin.Essentials: подключение

![Предварительные версии NuGet](~/media/shared/pre-release.png)

**Подключения** класс позволяет отслеживать наличие изменений в устройства условий работы сети, проверьте текущий доступ к сети, и как оно сейчас подключено.

## <a name="getting-started"></a>Начало работы

Чтобы получить доступ к **подключения** функциональность приведены следующие настройки платформы является обязательным.

# <a name="androidtabandroid"></a>[Android](#tab/android)

`AccessNetworkState` Разрешение является обязательным и должен быть настроен в проекте Android. Это можно сделать одним из следующих способов:

Откройте **AssemblyInfo.cs** файл **свойства** папку и добавьте:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessNetworkState)]
```

ИЛИ обновите манифест Android:

Откройте **AndroidManifest.xml** файл **свойства** папку и добавьте следующий код внутри класса **манифеста** узла.

```xml
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

Или щелкнуть правой кнопкой мыши проект Android и откройте свойства проекта. В разделе **манифест Android** найти **необходимые разрешения:** области и проверьте **состояние доступа к сети** разрешение. Будет автоматически обновлена **AndroidManifest.xml** файла.

# <a name="iostabios"></a>[iOS](#tab/ios)

Дополнительная настройка не требуется.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Дополнительная настройка не требуется.

-----

## <a name="using-connectivity"></a>С помощью подключения

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

Проверьте текущий сетевой доступ:

```csharp
var current = Connectivity.NetworkAccess;

if (current == NetworkAccess.Internet)
{
    // Connection to internet is available
}
```

[Сетевой доступ](xref:Xamarin.Essentials.NetworkAccess) делятся на следующие категории:

* **Интернет** — Local и доступом в Интернет.
* **ConstrainedInternet** — ограниченный доступ к Интернету. Указывает captive подключения к порталу, где предоставляется локальный доступ к веб-портал, но требуется доступ к Интернету, что определенные учетные данные предоставляются через портал.
* **Локальный** — локальный только доступ к сети.
* **Нет** — доступно без подключения.
* **Неизвестный** — не удается определить подключение к Интернету.

Вы можете проверить, какой тип [профиля подключения](xref:Xamarin.Essentials.ConnectionProfile) устройство активно использует:

```csharp
var profiles = Connectivity.Profiles;
if (profiles.Contains(ConnectionProfile.WiFi))
{
    // Active Wi-Fi connection.
}
```

Каждый раз, когда профиль подключения или сетевого доступа к изменениям, можно получать событие при активации:

```csharp
public class ConnectivityTest
{
    public ConnectivityTest()
    {
        // Register for connectivity changes, be sure to unsubscribe when finished
        Connectivity.ConnectivityChanged += Connectivity_ConnectivityChanged;
    }

    void Connectivity_ConnectivityChanged(ConnectivityChangedEventArgs  e)
    {
        var access = e.NetworkAccess;
        var profiles = e.Profiles;
    }
}
```

## <a name="limitations"></a>Ограничения

Обратите внимание, что очень важно, `Internet` сообщается с помощью `NetworkAccess` , но не доступен полный доступ к веб. Из-за, как работает подключение на каждой платформе он гарантирует только подключение доступно. Для экземпляра устройство может быть подключено к сети Wi-Fi, но маршрутизатор отключен от Интернета. В этом экземпляре Интернет, могут указываться, но активное соединение не доступен.

## <a name="api"></a>API

* [Исходный код для подключения](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Connectivity)
* [Документация по API-интерфейса](xref:Xamarin.Essentials.Connectivity)
