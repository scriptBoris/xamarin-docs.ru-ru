---
title: 'Xamarin.Essentials: магнитометр'
description: Класс магнитометр в Xamarin.Essentials позволяет наблюдать за датчик магнитометр устройства, указывающий ориентации устройства относительно поля магнитные Земли.
ms.assetid: 64DD0D41-03E2-40DD-9EC8-101CA0ED852B
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 2c02188b5282949559e0abc5fa1b61b6b451fc8e
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080553"
---
# <a name="xamarinessentials-magnetometer"></a>Xamarin.Essentials: магнитометр

![Предварительная версия NuGet](~/media/shared/pre-release.png)

**Магнитометр** позволяет отслеживать датчика магнитометр устройства, указывающий ориентации устройства относительно поля магнитные Земли.

## <a name="using-magnetometer"></a>С помощью магнитометр

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

Эта функциональность магнитометр работает путем вызова `Start` и `Stop` методы для прослушивания изменений магнитометр. Все изменения отправляются обратно через `ReadingChanged` событий. Ниже приведен пример использования:

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

## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[Скорость датчика](xref:Xamarin.Essentials.SensorSpeed)

- **Самый быстрый** — получить данные датчика максимально быстро (не гарантирует возврат в потоке пользовательского интерфейса).
- **Игра** — интенсивность подходит для игры (не гарантирует возврат в потоке пользовательского интерфейса).
- **Обычный** — курс по умолчанию, подходит для изменения ориентации экрана.
- **Пользовательский интерфейс** — интенсивность подходит для общего пользовательского интерфейса.

Если обработчик событий не обязательно выполняться в потоке пользовательского интерфейса и если обработчик событий должен иметь доступ к элементам пользовательского интерфейса, используйте [ `MainThread.BeginInvokeOnMainThread` ](main-thread.md) метод для выполнения этого кода в потоке пользовательского интерфейса.

## <a name="api"></a>API

- [Магнитометр исходного кода](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Magnetometer)
- [Магнитометр API документации](xref:Xamarin.Essentials.Magnetometer)
