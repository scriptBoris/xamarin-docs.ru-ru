---
title: 'Xamarin.Essentials: Geocoding'
description: Класс Geocoding в Xamarin.Essentials предоставляет API-интерфейсы для геокодирования метки в позиционные координаты и обратного геокодирования координат в метку.
ms.assetid: 3ADC440C-B000-4708-A2CC-296F5160AF90
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 0f44f179588c564ac9fa46d42ec18de6d303e239
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898983"
---
# <a name="xamarinessentials-geocoding"></a>Xamarin.Essentials: Geocoding

Класс **Geocoding** предоставляет API-интерфейсы для геокодирования метки в позиционные координаты и обратного геокодирования координат в метку.

## <a name="get-started"></a>Начало работы

[!include[](~/essentials/includes/get-started.md)]

Чтобы получить доступ к функции класса **Geocoding**, нужно создать описанную ниже конфигурацию для конкретной платформы.

# <a name="androidtabandroid"></a>[Android](#tab/android)

Дополнительная настройка не требуется.

# <a name="iostabios"></a>[iOS](#tab/ios)

Дополнительная настройка не требуется.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Для применения геокодирования нужно использовать ключ API Карт Bing. Зарегистрируйтесь для получения бесплатной учетной записи [Карт Bing](https://www.bingmapsportal.com/) В разделе **Моя учетная запись > My keys** (Мои ключи) создайте ключ и заполните сведения на основе типа приложения (которое должно быть **общедоступным приложением Windows (UWP, 8.x и более ранних версий)** для приложений UWP).

На раннем этапе работы приложения перед вызовом любого метода **Geocoding** задайте ключ API (он доступен только на UWP):

```csharp
Platform.MapServiceToken = "YOUR-KEY-HERE";
```

-----

## <a name="using-geocoding"></a>Использование Geocoding

Добавьте в свой класс ссылку на Xamarin.Essentials:

```csharp
using Xamarin.Essentials;
```

Получение координат [расположения](xref:Xamarin.Essentials.Location) для адреса:

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
    // Handle exception that may have occurred in geocoding
}
```

Значения высоты не всегда доступно. Если она недоступна, свойство `Altitude` может быть со значением `null` или равным нулю. Если высота доступна, значение указывается в метрах над уровнем моря.

Получение [меток](xref:Xamarin.Essentials.Placemark) для имеющегося набора координат:

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

Классы [`Location`](xref:Xamarin.Essentials.Location) и [`LocationExtensions`](xref:Xamarin.Essentials.LocationExtensions) определяют методы вычисления расстояния между двумя расположениями. Пример см. в статье [**Xamarin.Essentials: Geolocation**](geolocation.md#calculate-distance)

## <a name="api"></a>API

- [Исходный код Geocoding](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Geocoding)
- [Документация по API Geocoding](xref:Xamarin.Essentials.Geocoding)
