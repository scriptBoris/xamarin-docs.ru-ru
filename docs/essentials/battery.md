---
title: 'Xamarin.Essentials: батареи'
description: Этот документ описывает класс аккумулятора в Xamarin.Essentials, который позволяет проверить информацию о батарее устройства и отслеживайте изменения.
ms.assetid: 47EB26D8-8C62-477B-A13C-6977F74E6E43
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 1deafed85e9400bf7d4592fc06f71c22cc0015f0
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353458"
---
# <a name="xamarinessentials-battery"></a>Xamarin.Essentials: батареи

![Предварительные версии NuGet](~/media/shared/pre-release.png)

**Батареи** класс позволяет проверить информацию о батарее устройства и отслеживайте изменения.

## <a name="getting-started"></a>Начало работы

Чтобы получить доступ к **батареи** функциональность приведены следующие настройки платформы является обязательным.

# <a name="androidtabandroid"></a>[Android](#tab/android)

`Battery` Разрешение является обязательным и должен быть настроен в проекте Android. Это можно сделать одним из следующих способов:

Откройте **AssemblyInfo.cs** файл **свойства** папку и добавьте:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.BatteryStats)]
```

ИЛИ обновите манифест Android:

Откройте **AndroidManifest.xml** файл **свойства** папку и добавьте следующий код внутри класса **манифеста** узла.

```xml
<uses-permission android:name="android.permission.BATTERY_STATS" />
```

Или щелкнуть правой кнопкой мыши проект Android и откройте свойства проекта. В разделе **манифест Android** найти **необходимые разрешения:** области и проверьте **батареи** разрешение. Будет автоматически обновлена **AndroidManifest.xml** файла.

# <a name="iostabios"></a>[iOS](#tab/ios)

Дополнительная настройка не требуется.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Дополнительная настройка не требуется.

-----

## <a name="using-battery"></a>От батарей

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

Проверьте сведения о текущем аккумулятора:

```csharp
var level = Battery.ChargeLevel; // returns 0.0 to 1.0 or -1.0 if unable to determine.

var state = Battery.State;

switch (state)
{
    case BatteryState.Charging:
        // Currently charging
        break;
    case BatteryState.Full:
        // Battery is full
        break;
    case BatteryState.Discharging:
    case BatteryState.NotCharging:
        // Currently discharging battery or not being charged
        break;
    case BatteryState.NotPresent:
        // Battery doesn't exist in device (desktop computer)
    case BatteryState.Unknown:
        // Unable to detect battery state
        break;
}

var source = Battery.PowerSource;

switch (source)
{
    case BatteryPowerSource.Battery:
        // Being powered by the battery
        break;
    case BatteryPowerSource.AC:
        // Being powered by A/C unit
        break;
    case BatteryPowerSource.Usb:
        // Being powered by USB cable
        break;
    case BatteryPowerSource.Wireless:
        // Powered via wireless charging
        break;
    case BatteryPowerSource.Unknown:
        // Unable to detect power source
        break;
}
```

При каждом изменении каких-либо свойств батареи активируется событие:

```csharp
public class BatteryTest
{
    public BatteryTest()
    {
        // Register for battery changes, be sure to unsubscribe when needed
        Battery.BatteryChanged += Battery_BatteryChanged;
    }

    void Battery_BatteryChanged(BatteryChangedEventArgs   e)
    {
        var level = e.ChargeLevel;
        var state = e.State;
        var source = e.PowerSource;
        Console.WriteLine($"Reading: Level: {level}, State: {state}, Source: {source}");
    }
}
```

## <a name="platform-differences"></a>Различия платформ

# <a name="androidtabandroid"></a>[Android](#tab/android)

Различия не платформы.

# <a name="iostabios"></a>[iOS](#tab/ios)

* Устройство должно использовать для тестирования интерфейсов API. 
* Будет возвращать только `AC` или `Battery` для `PowerSource`.
* Не удается отменить вибрации.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

* Будет возвращать только `AC` или `Battery` для `PowerSource`.

-----

## <a name="api"></a>API

- [Исходный код батареи](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Battery)
- [Документация по API батареи](xref:Xamarin.Essentials.Battery)
