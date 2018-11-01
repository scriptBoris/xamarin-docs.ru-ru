---
title: 'Xamarin.Essentials: подключение'
description: Класс Connectivity в Xamarin.Essentials позволяет отслеживать изменения в условиях подключения устройства к сети, проверять текущее состояние и используемый метод доступа.
ms.assetid: E1B1F152-B1D5-4227-965E-C0AEBF528F49
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: bc0cd206881356a92128c758f0225f98f88c2814
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675410"
---
# <a name="xamarinessentials-connectivity"></a>Xamarin.Essentials: подключение

![Предварительная версия NuGet](~/media/shared/pre-release.png)

Класс **Connectivity** позволяет отслеживать изменения в условиях подключения устройства к сети, проверять текущее состояние и используемый метод доступа.

## <a name="get-started"></a>Начало работы

[!include[](~/essentials/includes/get-started.md)]

Чтобы проверить функциональность класса **Connectivity**, нужно создать описанную ниже конфигурацию для конкретной платформы.

# <a name="androidtabandroid"></a>[Android](#tab/android)

Требуется разрешение `AccessNetworkState`, которое следует настроить в проекте Android. Для этого можно применить любой из следующих методов:

Откройте файл **AssemblyInfo.cs** в папке **Properties** и добавьте в него:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessNetworkState)]
```

ИЛИ обновите манифест Android:

Откройте файл **AndroidManifest.xml** в папке **Properties** и добавьте приведенный ниже код в узел **manifest**.

```xml
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

ИЛИ щелкните правой кнопкой мыши проект Android и откройте свойства проекта. В разделе **Манифест Android** найдите область **Требуемые разрешения:** и установите флажок для разрешения **Access Network State** (Доступ к состоянию сети). Это действие автоматически обновляет файл **AndroidManifest.xml**.

# <a name="iostabios"></a>[iOS](#tab/ios)

Дополнительная настройка не требуется.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Дополнительная настройка не требуется.

-----

## <a name="using-connectivity"></a>Использование подключения

Добавьте в свой класс ссылку на Xamarin.Essentials:

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

[Сетевой доступ](xref:Xamarin.Essentials.NetworkAccess) разделяется на следующие категории:

* **Internet** — локальная сеть и доступ к Интернет;у
* **ConstrainedInternet** — ограниченный доступ к Интернету. Обозначает режим условного доступа через портал, при котором предоставляется локальный доступ к веб-порталу, а доступ к Интернету осуществляется через этот портал со специальными учетными данными;
* **Local** — только доступ к локальной сети;
* **None** — подключение недоступно;
* **Unknown** — не удается определить режим подключения.

Вы можете проверить, какой тип [профиля подключения](xref:Xamarin.Essentials.ConnectionProfile) устройство активно использует:

```csharp
var profiles = Connectivity.Profiles;
if (profiles.Contains(ConnectionProfile.WiFi))
{
    // Active Wi-Fi connection.
}
```

Вы можете получать события с информацией о любых изменениях профиля подключения и (или) режима сетевого доступа:

```csharp
public class ConnectivityTest
{
    public ConnectivityTest()
    {
        // Register for connectivity changes, be sure to unsubscribe when finished
        Connectivity.ConnectivityChanged += Connectivity_ConnectivityChanged;
    }

    void Connectivity_ConnectivityChanged(object sender, ConnectivityChangedEventArgs  e)
    {
        var access = e.NetworkAccess;
        var profiles = e.Profiles;
    }
}
```

## <a name="limitations"></a>Ограничения

Обратите внимание, что при состоянии `Internet`, полученном от `NetworkAccess`, доступ к Интернету может быть не полным. Так как на разных платформах используются различные методы подключения, можно гарантировать только факт наличия подключения. Например, устройство может быть подключено к сети Wi-Fi, маршрутизатор которой не имеет подключения к Интернету. В этом случае будет зарегистрировано подключение к Интернету, но не будет возможности его активно использовать.

## <a name="api"></a>API

* [Исходный код Connectivity](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Connectivity)
* [Документация по API-интерфейсу Connectivity](xref:Xamarin.Essentials.Connectivity)
