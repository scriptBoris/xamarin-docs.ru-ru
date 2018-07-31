---
title: 'Xamarin.Essentials: OrientationSensor'
description: Класс OrientationSensor позволяет наблюдать за ориентации устройства в трехмерном пространстве.
ms.assetid: F3091D93-E779-41BA-8696-23D296F2F6F5
author: charlespetzold
ms.author: chape
ms.date: 05/21/2018
ms.openlocfilehash: a15338795424885882ed9c86288342d196f6fda2
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353819"
---
# <a name="xamarinessentials-orientationsensor"></a>Xamarin.Essentials: OrientationSensor

![Предварительные версии NuGet](~/media/shared/pre-release.png)

**OrientationSensor** класс позволяет отслеживать ориентации устройства в трех многомерном пространстве.

> [!NOTE]
> Этот класс предназначен для определения ориентации устройства в трехмерном пространстве. Если вам нужно определить, если устройство видео отображается в книжном или альбомном режиме, используйте `Orientation` свойство `ScreenMetrics` объект, доступный из [ `DeviceDisplay` ](device-display.md) класса.

## <a name="using-orientationsensor"></a>С помощью OrientationSensor

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

`OrientationSensor` Включена, вызвав `Start` метод для отслеживания изменений в ориентации устройства и отключено, вызвав `Stop` метод. Любые изменения, отправляются обратно через `ReadingChanged` событий. Ниже приведен пример использования:

```csharp

public class OrientationSensorTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public OrientationSensorTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        OrientationSensor.ReadingChanged += OrientationSensor_ReadingChanged;
    }

    void OrientationSensor_ReadingChanged(object sender, OrientationSensorChangedEventArgs e)
    {
        var data = e.Reading;
        Console.WriteLine($"Reading: X: {data.Orientation.X}, Y: {data.Orientation.Y}, Z: {data.Orientation.Z}, W: {data.Orientation.W}");
        // Process Orientation quaternion (X, Y, Z, and W)
    }

    public void ToggleOrientationSensor()
    {
        try
        {
            if (OrientationSensor.IsMonitoring)
                OrientationSensor.Stop();
            else
                OrientationSensor.Start(speed);
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

`OrientationSensor` показания происходит возврат в виде [ `Quaternion` ](xref:System.Numerics.Quaternion) , описывающий ориентации устройства на базе двух трехмерных систем координат:

Устройство (обычно телефон или планшет) имеет в трехмерной системе координат с осями следующие:

- Положительный X указывает ось справа от отображения в книжной ориентации.
- Положительное оси Y точки в верхнюю часть устройство в книжной ориентации.
- Указывает положительное ось Z выходит из экрана.

Трехмерная система координат Земли имеет следующие оси:

- Положительное оси X была касательной к поверхности Земли и указывает Восток.
- Положительное оси Y также касательной к поверхности Земли и точки вверх.
- Положительное оси Z перпендикулярно поверхности Земли и точки вверх.

`Quaternion` Описывает поворот системы координат устройства по отношению к системе координат Земли.

Объект `Quaternion` значение очень тесно связана с поворот вокруг оси. Если ось поворота — нормализованный вектор (<sub>x</sub>,<sub>y</sub>,<sub>z</sub>), а угол поворота — Θ, а затем (X, Y, Z, W) компоненты кватерниона:

(<sub>x</sub>·sin(Θ/2),<sub>y</sub>·sin(Θ/2),<sub>z</sub>·sin(Θ/2), cos(Θ/2))

Это справа системы координат, поэтому с помощью ползунка справа, на который указывает в положительном направлении оси вращения, изгиб пальцы означает направление положительные значения угла поворота.

Примеры

* Если устройство находится в плоский для таблицы с экрана лицевой стороной вверх, по верхнему краю устройства (в книжной ориентации) указывал на север, выравниваются две системы координат. `Quaternion` Значение представляет единичное значение для кватерниона (0, 0, 0, 1). Все поворотов могут анализироваться по отношению к этой позиции.

* Если устройство находится в плоский для таблицы с экрана лицевой стороной вверх и верхней устройства (в книжной ориентации), Западная, указывающий `Quaternion` такое значение (0, 0, 0.707, 0.707). Устройство было повернуто 90 градусов вокруг оси Z Земли.

* Когда устройство удерживается вертикально, чтобы он (в книжной ориентации) указывает сторону небо и Север сталкивается задней панели устройства, на устройстве была повернута на 90 градусов вокруг оси X. `Quaternion` Значение — (0.707, 0, 0, 0.707).

* Если устройство находится в том случае, поэтому его левым краем относится к таблице, а верхней Север, указывает устройство было повернуто &ndash;90 градусов вокруг оси Y (или 90 градусов вокруг оси Y отрицательное). `Quaternion` Значение — (0,-0.707, 0, 0.707).

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [OrientationSensor исходного кода](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/OrientationSensor)
- [Документация по OrientationSensor API](xref:Xamarin.Essentials.OrientationSensor)
