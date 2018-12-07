---
title: 'Xamarin.Essentials: Battery'
description: В этом документе описан класс Battery в Xamarin.Essentials, который позволяет проверить информацию об аккумуляторе устройства и отслеживать изменения.
ms.assetid: 47EB26D8-8C62-477B-A13C-6977F74E6E43
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 5c457bb8ad9796396f24264e27f6762569ea542c
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898873"
---
# <a name="xamarinessentials-battery"></a>Xamarin.Essentials: Battery

Класс **Battery** позволяет получать сведения о батарее устройства, отслеживать изменения и просматривать информацию о состоянии энергосбережения (находится ли устройство в режиме пониженного энергопотребления). Приложениям следует избегать фоновой обработки, если на устройстве включено состояние экономии электроэнергии.

## <a name="get-started"></a>Начало работы

[!include[](~/essentials/includes/get-started.md)]

Для доступа к функции **Battery** нужно создать описанную ниже конфигурацию для конкретной платформы.

# <a name="androidtabandroid"></a>[Android](#tab/android)

Требуется разрешение `Battery`, которое следует настроить в проекте Android. Для этого можно применить любой из следующих методов:

Откройте файл **AssemblyInfo.cs** в папке **Свойства** и добавьте в него:

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
var level = Battery.ChargeLevel; // returns 0.0 to 1.0 or 1.0 when on AC or no battery.

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

    void Battery_BatteryChanged(object sender, BatteryInfoChangedEventArgs   e)
    {
        var level = e.ChargeLevel;
        var state = e.State;
        var source = e.PowerSource;
        Console.WriteLine($"Reading: Level: {level}, State: {state}, Source: {source}");
    }
}
```

Устройства, работающие на аккумуляторах, можно перевести в режим низкого энергопотребления. Иногда устройства переключаются в этот режим автоматически, например, если остаток заряда аккумулятора опускается ниже 20 %. Операционная система в режиме экономии электроэнергии сокращает количество действий, при которых быстро разряжается аккумулятор. В приложениях при включенном режиме экономии не выполняется фоновая обработка и другие энергоемких действий.

Вы также можете узнать текущее состояние энергосбережения на устройстве с помощью статического свойства `Battery.EnergySaverStatus`:

```csharp
// Get energy saver status
var status = Battery.EnergySaverStatus;
```

Это свойство возвращает элемент перечисления `EnergySaverStatus`, значением которого является `On`, `Off` или `Unknown`. Если свойство возвращает значение `On`, приложению следует избегать фоновой обработки и выполнения других действий, которые могут потреблять много электроэнергии.

Также приложению следует установить обработчик событий. Класс **Battery** предоставляет событие, которое активируется при изменении состояния энергосбережения:

```csharp
public class EnergySaverTest
{
    public EnergySaverTest()
    {
        // Subscribe to changes of energy-saver status
        Batter.EnergySaverStatusChanged += OnEnergySaverStatusChanged;
    }

    private void OnEnergySaverStatusChanged(EnergySaverStatusChangedEventArgs e)
    {
        // Process change
        var status = e.EnergySaverStatus;
    }
}
```

Если состояние экономии электроэнергии изменится на `On`, приложение должно прекратить выполнение фоновой обработки. Если состояние изменится на `Unknown` или `Off`, приложение может возобновить фоновую обработку.


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
