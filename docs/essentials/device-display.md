---
title: 'Xamarin.Essentials: Сведения об отображении устройства'
description: Этот документ описывает класс DeviceDisplay в Xamarin.Essentials, которая обеспечивает метрики экрана устройства, на котором выполняется приложение.
ms.assetid: 2821C908-C613-490D-8E8C-1BD3269FCEEA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3060d56e14fb0d3801a96ec0fe6e24c9efda4dac
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080317"
---
# <a name="xamarinessentials-device-display-information"></a>Xamarin.Essentials: Сведения об отображении устройства

![Предварительная версия NuGet](~/media/shared/pre-release.png)

**DeviceDisplay** класс предоставляет сведения о метриках экрана устройства, приложения.

## <a name="using-devicedisplay"></a>С помощью DeviceDisplay

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

## <a name="screen-metrics"></a>Метрики экрана

Помимо базовую информацию об устройстве **DeviceDisplay** класс содержит сведения о экрана устройства и ориентацию.

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

**DeviceDisplay** класс также предоставляет событие, которое можно подписаться, запускаемое каждый раз, когда любой метрики изменения экрана:

```csharp
public class ScreenMetricsTest
{
    public ScreenMetricsTest()
    {
        // Subscribe to changes of screen metrics
        DeviceDisplay.ScreenMetricsChanaged += OnScreenMetricsChanged;
    }

    void OnScreenMetricsChanged(ScreenMetricsChangedEventArgs  e)
    {
        // Process changes
        var metrics = e.Metrics;
    }
}
```

## <a name="api"></a>API

- [DeviceDisplay исходного кода](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceDisplay)
- [Документация по DeviceDisplay API](xref:Xamarin.Essentials.DeviceDisplay)
