---
title: 'Xamarin.Essentials: гироскопа'
description: Класс гироскопа в Xamarin.Essentials позволяет наблюдать за датчик гироскопа устройства, который измеряет поворота вокруг устройства три основные оси.
ms.assetid: DA4F968A-D988-41F5-8745-1BEE693660A1
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 6b2df3b6c5061464a06730ad09cbbbfdbceeb247
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080573"
---
# <a name="xamarinessentials-gyroscope"></a>Xamarin.Essentials: гироскопа

![Предварительная версия NuGet](~/media/shared/pre-release.png)

**Гироскопа** позволяет отслеживать устройства гироскопа датчик которого является угол поворота вокруг устройства три основные оси.

## <a name="using-gyroscope"></a>С помощью гироскопа

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

Эта функциональность гироскопа работает путем вызова `Start` и `Stop` методы для прослушивания изменений гироскопа. Все изменения отправляются обратно через `ReadingChanged` событий. Ниже приведен пример использования:

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

## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[Скорость датчика](xref:Xamarin.Essentials.SensorSpeed)

- **Самый быстрый** — получить данные датчика максимально быстро (не гарантирует возврат в потоке пользовательского интерфейса).
- **Игра** — интенсивность подходит для игры (не гарантирует возврат в потоке пользовательского интерфейса).
- **Обычный** — курс по умолчанию, подходит для изменения ориентации экрана.
- **Пользовательский интерфейс** — интенсивность подходит для общего пользовательского интерфейса.

Если обработчик событий не обязательно выполняться в потоке пользовательского интерфейса и если обработчик событий должен иметь доступ к элементам пользовательского интерфейса, используйте [ `MainThread.BeginInvokeOnMainThread` ](main-thread.md) метод для выполнения этого кода в потоке пользовательского интерфейса.

## <a name="api"></a>API

- [Гироскопа исходного кода](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Gyroscope)
- [Документация по гироскопа API](xref:Xamarin.Essentials.Gyroscope)
