---
title: 'Xamarin.Essentials: магнитометр'
description: Класс магнитометр в Xamarin.Essentials позволяет отслеживать устройства магнитометр датчик, который указывает ориентации устройства относительно Земли в направлении магнитного поля.
ms.assetid: 64DD0D41-03E2-40DD-9EC8-101CA0ED852B
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 52790f78c2d78347a35f111b3c4db63900c24ec7
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37947365"
---
# <a name="xamarinessentials-magnetometer"></a>Xamarin.Essentials: магнитометр

![Предварительные версии NuGet](~/media/shared/pre-release.png)

**Магнитометр** класс позволяет отслеживать устройства магнитометр датчик, который указывает ориентации устройства относительно Земли в направлении магнитного поля.

## <a name="using-magnetometer"></a>С помощью магнитометр

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

Функциональность магнитометр работает путем вызова `Start` и `Stop` методы для прослушивания изменений магнитометр. Любые изменения, отправляются обратно через `ReadingChanged` событий. Ниже приведен пример использования:

```csharp

public class MagnetometerTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Ui;

    public MagnetometerTest()
    {
        // Register for reading changes.
        Magnetometer.ReadingChanged += Magnetometer_ReadingChanged;
    }

    void Magnetometerr_ReadingChanged(MagnetometerChangedEventArgs e)
    {
        var data = e.Reading;
        // Process MagneticField X, Y, and Z
        Console.WriteLine($"Reading: X: {data.MagneticField.X}, Y: {data.MagneticField.Y}, Z: {data.MagneticField.Z}");
    }

    public void ToggleMagnetometer()
    {
        try
        {
            if (Magnetometer.IsMonitoring)
              Magnetometer.Stop();
            else
              Magnetometer.Start(speed);
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

Все данные возвращаются в microteslas.

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [Магнитометр исходного кода](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Magnetometer)
- [Документация по магнитометр API](xref:Xamarin.Essentials.Magnetometer)
