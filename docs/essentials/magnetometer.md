---
title: 'Xamarin.Essentials: магнитометр'
description: Класс магнитометр в Xamarin.Essentials позволяет отслеживать устройства магнитометр датчик, который указывает ориентации устройства относительно Земли в направлении магнитного поля.
ms.assetid: 64DD0D41-03E2-40DD-9EC8-101CA0ED852B
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3827b9a57ec2667a8716f5b56bfb4631b979d43a
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353793"
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
    SensorSpeed speed = SensorSpeed.UI;

    public MagnetometerTest()
    {
        // Register for reading changes.
        Magnetometer.ReadingChanged += Magnetometer_ReadingChanged;
    }

    void Magnetometer_ReadingChanged(object sender, MagnetometerChangedEventArgs e)
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
