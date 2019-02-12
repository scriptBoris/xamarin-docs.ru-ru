---
title: 'Xamarin.Essentials: Сведения о дисплее устройства'
description: В этом документе описан класс DeviceDisplay в Xamarin.Essentials, который предоставляет метрики экрана для устройства, на котором работает приложение.
ms.assetid: 2821C908-C613-490D-8E8C-1BD3269FCEEA
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 61d0a77d7a6a862ec5e06c7b693f8e23e4cdb975
ms.sourcegitcommit: 6d41b5d48fd626d3f649809ed5480e5356755f14
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55986241"
---
# <a name="xamarinessentials-device-display-information"></a>Xamarin.Essentials: Сведения о дисплее устройства

Класс **DeviceDisplay** предоставляет сведения о метриках экрана устройства, на котором запущено приложение. Он также позволяет запросить, чтобы при работе приложения устройство не переводилось в спящий режим.

## <a name="get-started"></a>Начало работы

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-devicedisplay"></a>Использование DeviceDisplay

Добавьте в свой класс ссылку на Xamarin.Essentials:

```csharp
using Xamarin.Essentials;
```

## <a name="main-display-info"></a>Основные сведения об экране

В дополнение к базовой информации об устройстве класс **DeviceDisplay** содержит информацию об экране устройства и его ориентации.

```csharp
// Get Metrics
var mainDisplayInfo = DeviceDisplay.MainDisplayInfo;

// Orientation (Landscape, Portrait, Square, Unknown)
var orientation = mainDisplayInfo.Orientation;

// Rotation (0, 90, 180, 270)
var rotation = mainDisplayInfo.Rotation;

// Width (in pixels)
var width = mainDisplayInfo.Width;

// Height (in pixels)
var height = mainDisplayInfo.Height;

// Screen density
var density = mainDisplayInfo.Density;
```

Класс **DeviceDisplay** также предоставляет событие, которое запускается при изменении любой метрики экрана (на это событие можно подписаться):

```csharp
public class DisplayInfoTest
{
    public DisplayInfoTest()
    {
        // Subscribe to changes of screen metrics
        DeviceDisplay.MainDisplayInfoChanged += OnMainDisplayInfoChanged;
    }

    void OnMainDisplayInfoChanged(object sender, DisplayInfoChangedEventArgs  e)
    {
        // Process changes
        var displayInfo = e.DisplayInfo;
    }
}
```

Класс **DeviceDisplay** предоставляет свойство `bool` с именем `KeepScreenOn`, задание которого предотвращает отключение или блокировку экрана устройства.

```csharp
public class KeepScreenOnTest
{
    public void ToggleScreenLock()
    {
        DeviceDisplay.KeepScreenOn = !DeviceDisplay.KeepScreenOn;
    }
}
```

## <a name="platform-differences"></a>Различия платформ

# <a name="androidtabandroid"></a>[Android](#tab/android)

Никаких различий.

# <a name="iostabios"></a>[iOS](#tab/ios)

* Доступ к `DeviceDisplay` должен выполняться в потоке пользовательского интерфейса, иначе возникнет исключение. Вы можете использовать метод [`MainThread.BeginInvokeOnMainThread`](~/essentials/main-thread.md) для запуска этого кода в потоке пользовательского интерфейса.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Никаких различий.

--------------


## <a name="api"></a>API

- [Исходный код DeviceDisplay](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceDisplay)
- [Документация по API DeviceDisplay](xref:Xamarin.Essentials.DeviceDisplay)
