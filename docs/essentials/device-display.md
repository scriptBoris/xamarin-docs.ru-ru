---
title: Xamarin.Essentials. Сведения о дисплее устройства
description: В этом документе описан класс DeviceDisplay в Xamarin.Essentials, который предоставляет метрики экрана для устройства, на котором работает приложение.
ms.assetid: 2821C908-C613-490D-8E8C-1BD3269FCEEA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: ebe97cf7fbb78bff17196110e835bd35ff76b826
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674890"
---
# <a name="xamarinessentials-device-display-information"></a>Xamarin.Essentials. Сведения о дисплее устройства

![Предварительная версия NuGet](~/media/shared/pre-release.png)

Класс **DeviceDisplay** предоставляет сведения о метриках экрана устройства, получаемых при работе приложения.

## <a name="get-started"></a>Начало работы

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-devicedisplay"></a>Использование DeviceDisplay

Добавьте в свой класс ссылку на Xamarin.Essentials:

```csharp
using Xamarin.Essentials;
```

## <a name="screen-metrics"></a>Метрики экрана

В дополнение к базовой информации об устройстве класс **DeviceDisplay** содержит информацию об экране устройства и его ориентации.

```csharp
// Get Metrics
var metrics = DeviceDisplay.ScreenMetrics;

// Orientation (Landscape, Portrait, Square, Unknown)
var orientation = metrics.Orientation;

// Rotation (0, 90, 180, 270)
var rotation = metrics.Rotation;

// Width (in pixels)
var width = metrics.Width;

// Height (in pixels)
var height = metrics.Height;

// Screen density
var density = metrics.Density;
```

Класс **DeviceDisplay** также предоставляет событие, которое запускается при изменении любой метрики экрана (на это событие можно подписаться):

```csharp
public class ScreenMetricsTest
{
    public ScreenMetricsTest()
    {
        // Subscribe to changes of screen metrics
        DeviceDisplay.ScreenMetricsChanged += OnScreenMetricsChanged;
    }

    void OnScreenMetricsChanged(ScreenMetricsChangedEventArgs  e)
    {
        // Process changes
        var metrics = e.Metrics;
    }
}
```

## <a name="platform-differences"></a>Различия платформ

# <a name="androidtabandroid"></a>[Android](#tab/android)

Никаких различий.

# <a name="iostabios"></a>[iOS](#tab/ios)

* Доступ к `ScreenMetrics` должен выполняться в потоке пользовательского интерфейса, иначе возникнет исключение. Вы можете использовать метод [`MainThread.BeginInvokeOnMainThread`](~/essentials/main-thread.md) для запуска этого кода в потоке пользовательского интерфейса.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Никаких различий.

--------------


## <a name="api"></a>API

- [Исходный код DeviceDisplay](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceDisplay)
- [Документация по API DeviceDisplay](xref:Xamarin.Essentials.DeviceDisplay)
