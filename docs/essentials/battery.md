---
title: 'Xamarin.Essentials: Battery'
description: В этом документе описан класс Battery в Xamarin.Essentials, который позволяет проверить информацию об аккумуляторе устройства и отслеживать изменения.
ms.assetid: 47EB26D8-8C62-477B-A13C-6977F74E6E43
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 6a14c939064538a405a1fe64061e0bb2e903fedd
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675436"
---
# <a name="xamarinessentials-battery"></a>Xamarin.Essentials: Battery

![Предварительная версия NuGet](~/media/shared/pre-release.png)

Класс **Battery** позволяет проверить информацию об аккумуляторе устройства и отслеживать изменения.

## <a name="get-started"></a>Начало работы

[!include[](~/essentials/includes/get-started.md)]

Для доступа к функции **Battery** нужно создать описанную ниже конфигурацию для конкретной платформы.

# <a name="androidtabandroid"></a>[Android](#tab/android)

Требуется разрешение `Battery`, которое следует настроить в проекте Android. Для этого можно применить любой из следующих методов:

Откройте файл **AssemblyInfo.cs** в папке **Properties** и добавьте в него:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.BatteryStats)]
```

ИЛИ обновите манифест Android:

Откройте файл **AndroidManifest.xml** в папке **Properties** и добавьте приведенный ниже код в узел **manifest**.

```xml
<uses-permission android:name="android.permission.BATTERY_STATS" />
```

ИЛИ щелкните правой кнопкой мыши проект Android и откройте свойства проекта. В разделе **Манифест Android** найдите область **Требуемые разрешения:** и установите флажок для разрешения **Battery**. Это действие автоматически обновляет файл **AndroidManifest.xml**.

# <a name="iostabios"></a>[iOS](#tab/ios)

Дополнительная настройка не требуется.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Дополнительная настройка не требуется.

-----

## <a name="using-battery"></a>Использование Battery

Добавьте в свой класс ссылку на Xamarin.Essentials:

```csharp
using Xamarin.Essentials;
```

Проверьте текущую информацию об аккумуляторе:

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

При каждом изменении каких-либо свойств аккумулятора активируется событие:

```csharp
public class BatteryTest
{
    public BatteryTest()
    {
        // Register for battery changes, be sure to unsubscribe when needed
        Battery.BatteryChanged += Battery_BatteryChanged;
    }

    void Battery_BatteryChanged(object sender, BatteryChangedEventArgs   e)
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

Различия платформ отсутствуют.

# <a name="iostabios"></a>[iOS](#tab/ios)

* Устройство должно использоваться для тестирования интерфейсов API. 
* Для `PowerSource` будут возвращаться только значения `AC` или `Battery`.
* Невозможно отменить вибрацию.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

* Для `PowerSource` будут возвращаться только значения `AC` или `Battery`.

-----

## <a name="api"></a>API

- [Исходный код Battery](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Battery)
- [Документация по API аккумулятора](xref:Xamarin.Essentials.Battery)
