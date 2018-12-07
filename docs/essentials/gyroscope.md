---
title: 'Xamarin.Essentials: Gyroscope'
description: Класс Gyroscope в Xamarin.Essentials позволяет отслеживать датчик гироскопа устройства, который измеряет вращение вокруг трех основных осей устройства.
ms.assetid: DA4F968A-D988-41F5-8745-1BEE693660A1
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 1e19585e238d66568364be7ccdbdb52d22b04066
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898515"
---
# <a name="xamarinessentials-gyroscope"></a>Xamarin.Essentials: Gyroscope

Класс **Gyroscope** позволяет отслеживать датчик гироскопа устройства, который представляет собой поворот вокруг трех основных осей устройства.

## <a name="get-started"></a>Начало работы

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-gyroscope"></a>Использование Gyroscope

Добавьте в свой класс ссылку на Xamarin.Essentials:

```csharp
using Xamarin.Essentials;
```

Функция Gyroscope выполняется путем вызова методов `Start` и `Stop` для ожидания передачи данных об изменениях в гироскопе. Все изменения возвращаются через событие `ReadingChanged` в рад/с. Пример использования:

```csharp

public class GyroscopeTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public GyroscopeTest()
    {
        // Register for reading changes.
        Gyroscope.ReadingChanged += Gyroscope_ReadingChanged;
    }

    void Gyroscope_ReadingChanged(object sender, GyroscopeChangedEventArgs e)
    {
        var data = e.Reading;
        // Process Angular Velocity X, Y, and Z reported in rad/s
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

- [Исходный код Gyroscope](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Gyroscope)
- [Документация по API Gyroscope](xref:Xamarin.Essentials.Gyroscope)
