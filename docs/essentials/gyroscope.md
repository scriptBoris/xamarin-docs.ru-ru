---
title: 'Xamarin.Essentials: гироскоп'
description: Класс гироскоп Xamarin.Essentials позволяет отслеживать устройства гироскоп датчик измеряет поворота вокруг устройства три основные оси.
ms.assetid: DA4F968A-D988-41F5-8745-1BEE693660A1
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3c83b3a9d8a7801e531006f50f8db2e1ad23e48c
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37947219"
---
# <a name="xamarinessentials-gyroscope"></a>Xamarin.Essentials: гироскоп

![Предварительные версии NuGet](~/media/shared/pre-release.png)

**Гироскоп** класс позволяет отслеживать устройства гироскоп датчика, являющегося поворота вокруг устройства три основные оси.

## <a name="using-gyroscope"></a>С помощью гироскоп

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

Функциональность гироскоп работает путем вызова `Start` и `Stop` методы для прослушивания изменений гироскоп. Любые изменения, отправляются обратно через `ReadingChanged` событий. Ниже приведен пример использования:

```csharp

public class GyroscopeTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Ui;

    public GyroscopeTest()
    {
        // Register for reading changes.
        Gyroscope.ReadingChanged += Gyroscope_ReadingChanged;
    }

    void Gyroscope_ReadingChanged(GyroscopeChangedEventArgs e)
    {
        var data = e.Reading;
        // Process Angular Velocity X, Y, and Z
        Console.WriteLine($"Reading: X: {data.AngularVelocity.X}, Y: {data.AngularVelocity.Y}, Z: {data.AngularVelocity.Z}");
    }

    public void ToggleGyroscope()
    {
        try
        {
            if (Gyroscope.IsMonitoring)
              Gyroscope.Stop();
            else
              Gyroscope.Start(speed);
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

## <a name="api"></a>API

- [Гироскоп исходного кода](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Gyroscope)
- [Документация по гироскоп API](xref:Xamarin.Essentials.Gyroscope)
