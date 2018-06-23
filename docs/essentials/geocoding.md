---
title: 'Xamarin.Essentials: геокодирования'
description: Класс геокодирования в Xamarin.Essentials предоставляет API-интерфейсы для обоих geocode placemark позиционные координаты и обратная координаты geocode на placemark.
ms.assetid: 3ADC440C-B000-4708-A2CC-296F5160AF90
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 001cca2524e495d64c6781d8a2fc5cb58e771e6e
ms.sourcegitcommit: 0be3d10bf08d1f76eab109eb891ed202615ac399
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321448"
---
# <a name="xamarinessentials-geocoding"></a>Xamarin.Essentials: геокодирования

![Предварительная версия NuGet](~/media/shared/pre-release.png)

**Геокодирования** класс предоставляет API-интерфейсы для geocode placemark позиционные координаты и обратная geocode coordincates на placemark.

## <a name="getting-started"></a>Начало работы

Чтобы получить доступ к **геокодирования** требуется функциональность следующие платформы определенные настройки.

# <a name="androidtabandroid"></a>[Android](#tab/android)

Дополнительная настройка не требуется.

# <a name="iostabios"></a>[iOS](#tab/ios)

Дополнительная настройка не требуется.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Для использования геокодирования funcationality необходим ключ Bing maps API. Подпишитесь на бесплатную [Bing Maps](https://www.bingmapsportal.com/) учетной записи. В разделе **моей учетной записи > ключи** создать новый ключ и введите сведения, зависящие от типа приложения (должно быть **открытые приложения Windows (UWP, 8.x и более ранних версий)** для приложений UWP).

На раннем этапе в жизни приложения перед вызовом любых **геокодирования** методы набор ключ API:

```csharp
Geocoding.MapKey = "YOUR-KEY-HERE";
```

-----

## <a name="using-geocoding"></a>С помощью геокодирования

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

Получение [расположение](xref:Xamarin.Essentials.Location) координаты для адреса:

```csharp
try
{
    var address =  "Microsoft Building 25 Redmond WA USA";
    var locations = await Geocoding.GetLocationsAsync(address);

    var location = locations?.FirstOrDefault();
    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}");
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Handle exception that may have occured in geocoding
}
```

Получение [placemarks](xref:Xamarin.Essentials.Placemark) для существующего набора координат:

```csharp
try
{
    var lat = 47.673988;
    var lon = -122.121513;

    var placemarks = await Geocoding.GetPlacemarksAsync(lat, lon);

    var placemark = placemarks?.FirstOrDefault();
    if (placemark != null)
    {
        var geocodeAddress =
            $"AdminArea:       {placemark.AdminArea}\n" +
            $"CountryCode:     {placemark.CountryCode}\n" +
            $"CountryName:     {placemark.CountryName}\n" +
            $"FeatureName:     {placemark.FeatureName}\n" +
            $"Locality:        {placemark.Locality}\n" +
            $"PostalCode:      {placemark.PostalCode}\n" +
            $"SubAdminArea:    {placemark.SubAdminArea}\n" +
            $"SubLocality:     {placemark.SubLocality}\n" +
            $"SubThoroughfare: {placemark.SubThoroughfare}\n" +
            $"Thoroughfare:    {placemark.Thoroughfare}\n";

        Console.WriteLine(geocodeAddress);
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Handle exception that may have occurred in geocoding
}
```

## <a name="api"></a>API

- [Геокодирования исходного кода](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Geocoding)
- [Документация геокодирования API](xref:Xamarin.Essentials.Geocoding)
