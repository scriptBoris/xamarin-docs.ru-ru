---
title: 'Xamarin.Essentials: OrientationSensor'
description: Класс OrientationSensor позволяет наблюдать за ориентацией устройства в трехмерном пространстве.
ms.assetid: F3091D93-E779-41BA-8696-23D296F2F6F5
author: jamesmontemagno
ms.author: jamont
ms.date: 05/21/2018
ms.openlocfilehash: 4ea6ebbb85510b5d7262cde73248af9df975b867
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112069"
---
# <a name="xamarinessentials-orientationsensor"></a>Xamarin.Essentials: OrientationSensor

![Предварительная версия NuGet](~/media/shared/pre-release.png)

Класс **OrientationSensor** позволяет наблюдать за ориентацией устройства в трехмерном пространстве.

> [!NOTE]
> Этот класс предназначен для определения расположения устройства в трехмерном пространстве. Если вам нужно определить, в какой ориентации отображается видео на устройстве (книжной или альбомной), используйте свойство `Orientation` объекта `ScreenMetrics`, который доступен из класса [`DeviceDisplay`](device-display.md).

## <a name="using-orientationsensor"></a>Использование OrientationSensor

Добавьте в свой класс ссылку на Xamarin.Essentials:

```csharp
using Xamarin.Essentials;
```

`OrientationSensor` включается вызовом метода `Start` для отслеживания изменений в ориентации устройства и отключается методом `Stop`. Все изменения возвращаются через событие `ReadingChanged`. Вот простой пример использования:

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

Показания `OrientationSensor` возвращаются в виде [`Quaternion`](xref:System.Numerics.Quaternion)с описанием ориентации устройства в двух трехмерных системах координат:

Устройство (обычно это телефон или планшет) использует трехмерную систему координат со следующими осями:

- Положительное значение X обозначает направление вправо от экрана в книжной ориентации.
- Положительное значение Y обозначает направление вверх от устройства в книжной ориентации.
- Положительное значение Z обозначает направление вперед перпендикулярно экрану.

Трехмерная система координат относительно Земли имеет следующие оси:

- Положительное значение X указывает на восток по касательной к поверхности Земли.
- Положительное значение Y указывает на север по касательной к поверхности Земли.
- Положительное значение X указывает вверх перпендикулярно поверхности Земли.

`Quaternion` описывает вращение системы координат устройства по отношению к системе координат Земли.

Значение `Quaternion` очень тесно связано с поворотом вокруг оси. Если ось вращения обозначить нормализованным вектором (<sub>x</sub>,<sub>y</sub>,<sub>z</sub>), а угол поворота — Θ, то компоненты кватерниона (X, Y, Z, W) вычисляются следующим образом:

(a<sub>x</sub>·sin(Θ/2), a<sub>y</sub>·sin(Θ/2), a<sub>z</sub>·sin(Θ/2), cos(Θ/2)).

Это правосторонние системы координат: если направить большой палец правой руки в направлении положительного изменения координат по оси вращения, изгиб пальцев будет указывать направление вращения с положительным значением угла поворота.

Примеры

* Если устройство лежит горизонтально на столе экраном вверх и при этом верхний край устройства (в книжной ориентации) указывает на север, эти системы координат совпадают. Значение `Quaternion` представляет единичный кватернион (0, 0, 0, 1). Все повороты можно анализировать относительно этой позиции.

* Если устройство лежит горизонтально на столе экраном вверх и при этом верхний край устройства (в книжной ориентации) указывает на запад, `Quaternion` принимает значение (0, 0, 0,707, 0,707). Это означает, что устройство повернуто на 90 градусов вокруг оси Z в системе координат Земли.

* Когда устройство удерживается вертикально так, что его верхняя часть (в книжной ориентации) направлена вверх, к небу, а задняя панель устройства расположена с северной стороны, устройство считается повернутым на 90 градусов вокруг оси X. Значение `Quaternion` равно (0,707, 0, 0, 0,707).

* Если устройство размещается так, что его левый край размещен на столе, в верхней край указывает на север, устройство считается повернутым на &ndash;90 градусов вокруг оси Y (или на 90 градусов вокруг отрицательной оси Y). Значение `Quaternion` равно (0, -0,707, 0, 0,707).

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [Исходный код OrientationSensor](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/OrientationSensor)
- [Документация по API для OrientationSensor](xref:Xamarin.Essentials.OrientationSensor)
