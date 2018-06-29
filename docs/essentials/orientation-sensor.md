---
title: 'Xamarin.Essentials: OrientationSensor'
description: Класс OrientationSensor позволяет наблюдать за ориентации устройства в трехмерном пространстве.
ms.assetid: F3091D93-E779-41BA-8696-23D296F2F6F5
author: charlespetzold
ms.author: chape
ms.date: 05/21/2018
ms.openlocfilehash: c7bbc849e5fa0b901f5b54e77d548b28bc2a72c6
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080557"
---
# <a name="xamarinessentials-orientationsensor"></a>Xamarin.Essentials: OrientationSensor

![Предварительная версия NuGet](~/media/shared/pre-release.png)

**OrientationSensor** позволяет отслеживать ориентации устройства в трех двухмерном пространстве.

> [!NOTE]
> Этот класс предназначен для определения ориентации устройства в трехмерном пространстве. Если необходимо определить, если устройство видео отображается в книжном или альбомном режиме, используйте `Orientation` свойство `ScreenMetrics` объект, доступный из [ `DeviceDisplay` ](device-display.md) класса.

## <a name="using-orientationsensor"></a>С помощью OrientationSensor

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

`OrientationSensor` Включена, вызвав `Start` метод, чтобы отслеживать изменения ориентации устройства и отключено, вызывая метод `Stop` метод. Все изменения отправляются обратно через `ReadingChanged` событий. Ниже приведен пример использования:

```csharp

public class OrientationSensorTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Ui;

    public OrientationSensorTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        OrientationSensor.ReadingChanged += OrientationSensor_ReadingChanged;
    }

    void OrientationSensor_ReadingChanged(AccelerometerChangedEventArgs e)
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

`OrientationSensor` показания передаются обратно в виде [ `Quaternion` ](xref:System.Numerics.Quaternion) , описывающий ориентации устройства на основании двух объемных системы координат:

Устройство (обычно телефон или планшет) имеет трехмерная система координат с осями следующие:

- Положительный знак X указывает ось справа от отображения в книжной ориентации.
- Положительное оси Y указывает на начало устройства в книжной ориентации.
- Положительное оси Z указывает за пределы экрана.

Трехмерная система координат Земли имеет следующие оси:

- Положительное ось X — тангенс на поверхности Земли и указывает Восток.
- Положительное оси Y также касательной на поверхность Земли и Север точек.
- Положительное оси Z перпендикулярно поверхности Земли и точек вверх.

`Quaternion` Описывает поворот система координат устройства относительно Земли системы координат.

Объект `Quaternion` значение тесную связь для поворота вокруг оси. Если ось вращения — нормализованный вектор (<sub>x</sub>,<sub>y</sub>,<sub>z</sub>), и угол поворота — Θ, а затем (X, Y, Z, W) кватерниона компонентами являются:

(<sub>x</sub>·sin(Θ/2),<sub>y</sub>·sin(Θ/2),<sub>z</sub>·sin(Θ/2), cos(Θ/2))

Это правом системы координат, поэтому с thumb правую, указывает в направлении положительного поворота оси, кривой пальцы указывают направление положительные значения угла поворота.

Примеры

* Если устройство находится плоской на таблицы с его экранные лицевой стороной вверх, с верхней части устройства (в книжной ориентации), указывающий выравниваются две системы координат. `Quaternion` Значение представляет единичным кватернионом (0, 0, 0, 1). Можно проанализировать все поворотов относительно этой позиции.

* Если устройство находится плоской на таблицы с его экранные лицевой стороной вверх и верхней устройства (в книжной ориентации), Запад, указывающий `Quaternion` значение — (0, 0, 0.707, 0.707). Устройство был повернут на 90 градусов вокруг оси Z Земли.

* Если устройство удерживается вертикально, чтобы верхней (в книжной ориентации) указывает сторону sky и задней панели устройства сталкивается Север, на устройстве была повернута на 90 градусов вокруг оси X. `Quaternion` Значение — (0.707, 0, 0, 0.707).

* Если устройство находится в его левый край относится к таблице и верхней Север, указывает устройство вращающийся &ndash;90 градусов вокруг оси Y (или вокруг оси Y отрицательное). `Quaternion` Значение — (0,-0.707, 0, 0.707).

## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[Скорость датчика](xref:Xamarin.Essentials.SensorSpeed)

- **Самый быстрый** — получить данные датчика максимально быстро (не гарантирует возврат в потоке пользовательского интерфейса).
- **Игра** — интенсивность подходит для игры (не гарантирует возврат в потоке пользовательского интерфейса).
- **Обычный** — курс по умолчанию, подходит для изменения ориентации экрана.
- **Пользовательский интерфейс** — интенсивность подходит для общего пользовательского интерфейса.

Если обработчик событий не обязательно выполняться в потоке пользовательского интерфейса и если обработчик событий должен иметь доступ к элементам пользовательского интерфейса, используйте [ `MainThread.BeginInvokeOnMainThread` ](main-thread.md) метод для выполнения этого кода в потоке пользовательского интерфейса.

## <a name="api"></a>API

- [OrientationSensor исходного кода](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/OrientationSensor)
- [Документация по OrientationSensor API](xref:Xamarin.Essentials.OrientationSensor)
