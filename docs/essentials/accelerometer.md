---
title: 'Xamarin.Essentials: акселерометр'
description: Класс акселерометр в Xamarin.Essentials позволяет наблюдать за датчик акселерометра устройства, указывающий ускорения устройства в трех многомерном пространстве.
ms.assetid: 97883573-F0D9-4854-AC7C-A654814401C5
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 15e2cb69806f281e88e226b7bcd87a20e149d508
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37947313"
---
# <a name="xamarinessentials-accelerometer"></a>Xamarin.Essentials: акселерометр

![Предварительные версии NuGet](~/media/shared/pre-release.png)

**Акселерометр** класс позволяет отслеживать датчик акселерометра устройства, указывающий ускорения устройства в трех многомерном пространстве.

## <a name="using-accelerometer"></a>Используя акселерометр

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

Функциональность акселерометр работает путем вызова `Start` и `Stop` методы для прослушивания изменений ускорение. Любые изменения, отправляются обратно через `ReadingChanged` событий. Ниже приведен пример использования:

```csharp

public class AccelerometerTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Ui;

    public AccelerometerTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Accelerometer.ReadingChanged += Accelerometer_ReadingChanged;
    }

    void Accelerometer_ReadingChanged(AccelerometerChangedEventArgs e)
    {
        var data = e.Reading;
        Console.WriteLine($"Reading: X: {data.Acceleration.X}, Y: {data.Acceleration.Y}, Z: {data.Acceleration.Z}");
        // Process Acceleration X, Y, and Z
    }

    public void ToggleAcceleromter()
    {
        try
        {
            if (Accelerometer.IsMonitoring)
              Accelerometer.Stop();
            else
              Accelerometer.Start(speed);
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

Акселерометр показания происходит возврат в ж. G — это единое тяготения принудительно равно значению выдерживать по полю гравитации Земли (9,81 м/с ^ 2).

Система координат определяется относительно экрана телефона в его ориентацию по умолчанию. Оси не меняются местами, при изменении ориентации экрана устройства.

Горизонтальная ось X и точек вправо, оси Y по вертикали и обращена вверх и к внешней Передняя поверхность, экрана указывает ось Z. В этой системе координат за экрана иметь отрицательные значения Z.

Примеры

* Когда устройство находится плоской на таблицу и помещается в его слева направо, ускорение значение x будет положительным.

* Если устройство находится плоской на таблицы, задается значение ускорения G + 1,00 или (+ 9,81 м/с ^ 2), ускорение устройства, которые соответствуют (0 МБ в секунду ^ 2) за вычетом сила тяжести (-9,81 м/с ^ 2) и нормализованное как ж.

* Когда устройство находится плоской на таблицу и помещается в сторону sky с ускорением м/с ^ 2, значение ускорение равно + 9.81 соответствующими ускорение устройства (+ m/s ^ 2) за вычетом сила тяжести (-9,81 м/с ^ 2) и нормализованных в ж. 

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [Акселерометр исходного кода](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Accelerometer)
- [Документация по акселерометр API](xref:Xamarin.Essentials.Accelerometer)
