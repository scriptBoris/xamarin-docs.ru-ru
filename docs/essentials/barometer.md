---
title: 'Xamarin.Essentials: барометр'
description: Класс Barometer в Xamarin.Essentials позволяет отслеживать барометрический датчик устройства, измеряющий давление.
ms.assetid: DA4F968A-D988-41F5-8745-1BEE693660A1
author: jamesmontemagno
ms.author: jamont
ms.date: 08/16/2018
ms.openlocfilehash: 0217b89bc3f45347acecbdb3b8cdccfeed751744
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50130305"
---
# <a name="xamarinessentials-barometer"></a>Xamarin.Essentials: барометр

![Предварительная версия NuGet](~/media/shared/pre-release.png)

Класс **Barometer** позволяет отслеживать барометрический датчик устройства, измеряющий давление.

## <a name="using-barometer"></a>Использование класса Barometer

Добавьте в свой класс ссылку на Xamarin.Essentials:

```csharp
using Xamarin.Essentials;
```

Для доступа к функциям барометра нужно вызвать методы `Start` и `Stop`, которые отслеживают изменение показаний барометра в килопаскалях. Все изменения возвращаются через событие `ReadingChanged`. Пример использования:

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
        Console.WriteLine($"Reading: Pressure: {data.Pressure} kilopascals");
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
