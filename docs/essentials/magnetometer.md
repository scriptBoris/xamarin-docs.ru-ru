---
title: 'Xamarin.Essentials: Magnetometer'
description: Класс Magnetometer в Xamarin.Essentials позволяет отслеживать датчик магнитометра устройства, который указывает на ориентацию устройства относительно магнитного поля Земли.
ms.assetid: 64DD0D41-03E2-40DD-9EC8-101CA0ED852B
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: bfc4916c40b47b715357692308d6b5dfa9db57bf
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898720"
---
# <a name="xamarinessentials-magnetometer"></a>Xamarin.Essentials: Magnetometer

Класс **Magnetometer** позволяет отслеживать датчик магнитометра устройства, который указывает на ориентацию устройства относительно магнитного поля Земли.

## <a name="get-started"></a>Начало работы

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-magnetometer"></a>Использование Magnetometer

Добавьте в свой класс ссылку на Xamarin.Essentials:

```csharp
using Xamarin.Essentials;
```

Функция Magnetometer выполняется путем вызова методов `Start` и `Stop` для ожидания передачи данных об изменениях магнитометра. Все изменения возвращаются через событие `ReadingChanged`. Пример использования:

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

Все данные возвращаются в µ (микротеслах).

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [Исходный код Magnetometer](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Magnetometer)
- [Документация по API Magnetometer](xref:Xamarin.Essentials.Magnetometer)
