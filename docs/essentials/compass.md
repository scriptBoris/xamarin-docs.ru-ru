---
title: 'Xamarin.Essentials: компас'
description: Этот документ описывает класс компас в Xamarin.Essentials, в которой позволяет наблюдать за заголовком Северной магнитного устройства.
ms.assetid: BF85B0C3-C686-43D9-811A-07DCAF8CDD86
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: c3fe98c384a87bdc08ce94e7537d1a6343767561
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353887"
---
# <a name="xamarinessentials-compass"></a>Xamarin.Essentials: компас

![Предварительные версии NuGet](~/media/shared/pre-release.png)

**Компас** класс позволяет наблюдать за заголовком Северной магнитного устройства.

## <a name="using-compass"></a>С помощью компас

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

Функциональность компас работает путем вызова `Start` и `Stop` методы для прослушивания изменений компасу. Любые изменения, отправляются обратно через `ReadingChanged` событий. Пример:

```csharp
public class CompassTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public CompassTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Compass.ReadingChanged += Compass_ReadingChanged;
    }

    void Compass_ReadingChanged(object sender, CompassChangedEventArgs e)
    {
        var data = e.Reading;
        Console.WriteLine($"Reading: {data.HeadingMagneticNorth} degrees");
        // Process Heading Magnetic North
    }

    public void ToggleCompass()
    {
        try
        {
            if (Compass.IsMonitoring)
              Compass.Stop();
            else
              Compass.Start(speed);
        }
        catch (FeatureNotSupportedException fnsEx)
        {
            // Feature not supported on device
        }
        catch (Exception ex)
        {
            // Some other exception has occurred
        }
    }
}
```

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="platform-implementation-specifics"></a>Особенности реализации платформы

# <a name="androidtabandroid"></a>[Android](#tab/android)

Android не предоставляют API для извлечения по компасу. Мы используем, акселерометр и магнитометр для вычисления заголовком Северной магнитного рекомендуется Google.

В редких случаях вы может быть см. в разделе несогласованные результаты так, как нужно датчики можно уточнять, который включает в себя перемещение устройства перемещаемые рис. 8. Лучший способ сделать это для открытия карт Google, коснитесь точки с учетом своего местоположения и выберите **компас откалибровать**.

Имейте в виду, выполняется несколько датчиков из приложения, в то же время может регулировать скорость датчика.

## <a name="low-pass-filter"></a>Фильтр Low

Из-за как Android компас значения обновлены и расчет может существовать необходимость сгладить значения. Объект _фильтра передать низким_ можно применить, усредняющий значения синуса и косинуса углов и можно включить, задав `ApplyLowPassFilter` свойство `Compass` класса:

```csharp
Compass.ApplyLowPassFilter = true;
```

Применяется только на платформе Android. Дополнительные сведения вы найдете [здесь](https://github.com/xamarin/Essentials/pull/354#issuecomment-405316860).

--------------

## <a name="api"></a>API

- [Компаса исходного кода](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Compass)
- [Компаса документации по API](xref:Xamarin.Essentials.Compass)
