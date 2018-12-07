---
title: 'Xamarin.Essentials: барометр'
description: Класс Barometer в Xamarin.Essentials позволяет отслеживать барометрический датчик устройства, измеряющий давление.
ms.assetid: DA4F968A-D988-41F5-8745-1BEE693660A1
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 342ae1b64fefebaa4b3fa82e9f48c6e9a58d4751
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52899048"
---
# <a name="xamarinessentials-barometer"></a>Xamarin.Essentials: барометр

Класс **Barometer** позволяет отслеживать барометрический датчик устройства, измеряющий давление.

## <a name="get-started"></a>Начало работы

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-barometer"></a>Использование класса Barometer

Добавьте в свой класс ссылку на Xamarin.Essentials:

```csharp
using Xamarin.Essentials;
```

Чтобы использовать функции класса Barometer, вызывайте методы `Start` и `Stop`, которые отслеживают изменение показаний давления на барометре в гектопаскалях. Все изменения возвращаются через событие `ReadingChanged`. Пример использования:

```csharp

public class BarometerTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public BarometerTest()
    {
        // Register for reading changes.
        Barometer.ReadingChanged += Barometer_ReadingChanged;
    }

    void Barometer_ReadingChanged(object sender, BarometerChangedEventArgs e)
    {
        var data = e.Reading;
        // Process Pressure
        Console.WriteLine($"Reading: Pressure: {data.PressureInHectopascals} hectopascals");
    }

    public void ToggleBarometer()
    {
        try
        {
            if (Barometer.IsMonitoring)
              Barometer.Stop();
            else
              Barometer.Start(speed);
        }
        catch (FeatureNotSupportedException fnsEx)
        {
            // Feature not supported on device
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="platform-implementation-specifics"></a>Особенности реализации для платформ

# <a name="androidtabandroid"></a>[Android](#tab/android)

Нет особенностей реализации для платформы.

# <a name="iostabios"></a>[iOS](#tab/ios)

API использует класс [CMAltimeter](https://developer.apple.com/documentation/coremotion/cmaltimeter#//apple_ref/occ/cl/CMAltimeter) для отслеживания изменений давления. Это аппаратная функция, которая была добавлена в устройства iPhone 6 и более поздние версии. В устройствах, которые не поддерживают альтиметр, будет выдано исключение `FeatureNotSupportedException`.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Нет особенностей реализации для платформы.

-----

## <a name="api"></a>API

- [Исходный код Barometer](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Barometer).
- [Документация по API Barometer](xref:Xamarin.Essentials.Barometer).
