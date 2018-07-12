---
title: 'Xamarin.Essentials: геокодирования'
description: Класс геокодирования в Xamarin.Essentials предоставляет API-интерфейсы для обоих geocode placemark позиционные координаты и обратную координаты geocode на placemark.
ms.assetid: 3ADC440C-B000-4708-A2CC-296F5160AF90
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 063adba82d96e7fcc64d7ec49a0c0133e1cef8ef
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38831452"
---
# <a name="xamarinessentials-geocoding"></a>Xamarin.Essentials: геокодирования

![Предварительные версии NuGet](~/media/shared/pre-release.png)

**Геокодирования** класс предоставляет API-интерфейсы выполним геокодирование placemark позиционные координаты и обратная geocode coordincates на placemark.

## <a name="getting-started"></a>Начало работы

Чтобы получить доступ к **геокодирования** функциональность приведены следующие настройки платформы является обязательным.

# <a name="androidtabandroid"></a>[Android](#tab/android)

Дополнительная настройка не требуется.

# <a name="iostabios"></a>[iOS](#tab/ios)

Дополнительная настройка не требуется.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Для использования геокодирования funcationality необходим ключ Bing maps API. Зарегистрируйтесь для получения бесплатной [Bing Maps](https://www.bingmapsportal.com/) учетной записи. В разделе **моей учетной записи > Мои ключи** создайте новый раздел и заполните сведения, в зависимости от типа приложения (которое должно быть **общедоступному приложению Windows (UWP, 8.x и более ранних версий)** для приложений универсальной платформы Windows).

На раннем этапе жизненного цикла вашего приложения до вызова любого **геокодирования** методы задание ключа API:

```csharp
Geocoding.MapKey = "YOUR-KEY-HERE";
```

-----

## <a name="using-geocoding"></a>С помощью геокодирования

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

Начало [расположение](xref:Xamarin.Essentials.Location) координаты для адреса:

```csharp
try
{
    var address =  "Microsoft Building 25 Redmond WA USA";
    var locations = await Geocoding.GetLocationsAsync(address);

    var location = locations?.FirstOrDefault();
    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}, Altitude: {location.Altitude}");
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

Высота над уровнем моря не всегда доступна. Если она недоступна, `Altitude` свойство может быть `null` или значение может быть равно нулю. При наличии Высота над уровнем моря в метрах выше над уровнем моря является значение. 

Начало [placemarks](xref:Xamarin.Essentials.Placemark) для существующего набора координат:

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

## <a name="distance-between-two-locations"></a>Расстояние между двумя расположениями

[ `Location` ](xref:Xamarin.Essentials.Location) И [ `LocationExtensions` ](xref:Xamarin.Essentials.LocationExtensions) классы определяют методы для вычисления расстояния между двумя расположениями. См. в статье [ **Xamarin.Essentials: Geolocation** ](geolocation.md#calculate-distance) пример.

## <a name="api"></a>API

- [Геокодирование исходного кода](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Geocoding)
- [Документация по API геокодирования](xref:Xamarin.Essentials.Geocoding)
