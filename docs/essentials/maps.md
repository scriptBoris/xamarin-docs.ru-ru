---
title: 'Xamarin.Essentials: карты'
description: Класс Maps в Xamarin.Essentials позволяет приложению открыть установленное приложение карт на определенном местоположении или метке.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 07/25/2018
ms.openlocfilehash: fb4cbc2fd334d574abc57a3359fa346bc6795408
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674786"
---
# <a name="xamarinessentials-maps"></a>Xamarin.Essentials: карты

![Предварительная версия NuGet](~/media/shared/pre-release.png)

Класс **Maps** позволяет приложению открыть установленное приложение карт для определенного расположения или метки.

## <a name="get-started"></a>Начало работы

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-maps"></a>Использование класса Maps

Добавьте в свой класс ссылку на Xamarin.Essentials:

```csharp
using Xamarin.Essentials;
```

Чтобы использовать функции класса Maps, вызовите метод `OpenAsync` с параметром `Location` или `Placemark` для открытия с дополнительными параметрами `MapsLaunchOptions`.

```csharp
public class MapsTest
{
    public async Task NavigateToBuilding25()
    {
        var location = new Location(47.645160, -122.1306032);
        var options =  new MapsLaunchOptions { Name = "Microsoft Building 25" };

        await Maps.OpenAsync(location, options);
    }
}
```

При открытии с параметром `Placemark` требуется следующая информация:

- `CountryName`
- `AdminArea`
- `Thoroughfare`
- `Locality`

```csharp
public class MapsTest
{
    public async Task NavigateToBuilding25()
    {
        var placemark = new Placemark
            {
                CountryName = "United States",
                AdminArea = "WA",
                Thoroughfare = "Microsoft Building 25",
                Locality = "Redmond"
            };
        var options =  new MapsLaunchOptions { Name = "Microsoft Building 25" };

        await Maps.OpenAsync(placemark, options);
    }
}
```

## <a name="extension-methods"></a>Методы расширения

Если у вас уже есть ссылка на `Location` или `Placemark`, вы можете использовать встроенный метод расширения `OpenMapsAsync` с дополнительными параметрами `MapsLaunchOptions`:

```csharp
public class MapsTest
{
    public async Task OpenPlacemarkOnMaps(Placemark placemark)
    {
        await placemark.OpenMapsAsync();
    }
}
```

## <a name="directions-mode"></a>Режим направлений

Если вызвать `OpenMapsAsync` без параметров `MapsLaunchOptions`, карта запустится для указанного расположения. При желании можно рассчитать навигационный маршрут с текущего положения устройства. Для этого нужно установить `MapDirectionsMode` в `MapsLaunchOptions`:

```csharp
public class MapsTest
{
    public async Task NavigateToBuilding25()
    {
        var location = new Location(47.645160, -122.1306032);
        var options =  new MapsLaunchOptions { MapDirectionsMode = MapDirectionsMode.Driving };

        await Maps.OpenAsync(location, options);
    }
}
```

## <a name="platform-differences"></a>Различия платформ

# <a name="androidtabandroid"></a>[Android](#tab/android)

- `MapDirectionsMode` поддерживает езду на велосипеде, вождение и ходьбу.

# <a name="iostabios"></a>[iOS](#tab/ios)

- `MapDirectionsMode` поддерживает вождение, перемещение в общественном транспорте и ходьбу.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

- `MapDirectionsMode` поддерживает вождение, перемещение в общественном транспорте и ходьбу.

--------------

## <a name="platform-implementation-specifics"></a>Особенности реализации для платформ

# <a name="androidtabandroid"></a>[Android](#tab/android)

Android использует схему `geo:` Uri для запуска приложения карт на устройстве. Для пользователя может отобразиться запрос на выбор из существующих приложений того, которое поддерживает эту схему Uri.  Xamarin.Essentials тестируется с помощью службы Google Maps, которая поддерживает эту схему.

# <a name="iostabios"></a>[iOS](#tab/ios)

Нет особенностей реализации для платформы.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Нет особенностей реализации для платформы.

--------------

## <a name="api"></a>API

- [Исходный код класса Maps](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Maps)
- [Документация по API Maps](xref:Xamarin.Essentials.Maps)
