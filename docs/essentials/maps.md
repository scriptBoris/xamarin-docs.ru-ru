---
title: 'Xamarin.Essentials: класс Map'
description: Класс Map в Xamarin.Essentials позволяет приложению открывать установленное приложение карт на определенном местоположении или метке.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 9797244a9f89d0658b65b132eaf541ed763be97b
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898970"
---
# <a name="xamarinessentials-map"></a>Xamarin.Essentials: класс Map

Класс **Map** позволяет приложению открывать установленное приложение карт на определенном местоположении или метке.

## <a name="get-started"></a>Начало работы

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-map"></a>Использование класса Map

Добавьте в свой класс ссылку на Xamarin.Essentials:

```csharp
using Xamarin.Essentials;
```

Чтобы использовать функции класса Map, вызовите метод `OpenAsync` с параметром `Location` или `Placemark` для открытия с дополнительными параметрами `MapLaunchOptions`.

```csharp
public class MapTest
{
    public async Task NavigateToBuilding25()
    {
        var location = new Location(47.645160, -122.1306032);
        var options =  new MapLaunchOptions { Name = "Microsoft Building 25" };

        await Map.OpenAsync(location, options);
    }
}
```

При открытии с параметром `Placemark` требуется следующая информация:

- `CountryName`
- `AdminArea`
- `Thoroughfare`
- `Locality`

```csharp
public class MapTest
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
        var options =  new MapLaunchOptions { Name = "Microsoft Building 25" };

        await Map.OpenAsync(placemark, options);
    }
}
```

## <a name="extension-methods"></a>Методы расширения

Если у вас уже есть ссылка на `Location` или `Placemark`, вы можете использовать встроенный метод расширения `OpenMapAsync` с дополнительными параметрами `MapLaunchOptions`:

```csharp
public class MapTest
{
    public async Task OpenPlacemarkOnMap(Placemark placemark)
    {
        await placemark.OpenMapAsync();
    }
}
```

## <a name="directions-mode"></a>Режим направлений

Если вызвать `OpenMapAsync` без параметров `MapLaunchOptions`, карта запустится для указанного расположения. При желании можно рассчитать навигационный маршрут с текущего положения устройства. Для этого нужно установить `NavigationMode` в `MapLaunchOptions`:

```csharp
public class MapTest
{
    public async Task NavigateToBuilding25()
    {
        var location = new Location(47.645160, -122.1306032);
        var options =  new MapLaunchOptions { NavigationMode = NavigationMode.Driving };

        await Map.OpenAsync(location, options);
    }
}
```

## <a name="platform-differences"></a>Различия платформ

# <a name="androidtabandroid"></a>[Android](#tab/android)

- `NavigationMode` поддерживает езду на велосипеде, вождение и ходьбу.

# <a name="iostabios"></a>[iOS](#tab/ios)

- `NavigationMode` поддерживает вождение, перемещение в общественном транспорте и ходьбу.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

- `NavigationMode` поддерживает вождение, перемещение в общественном транспорте и ходьбу.

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

- [Исходный код Map](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Map)
- [Документация по API для Map](xref:Xamarin.Essentials.Map)
