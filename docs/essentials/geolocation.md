---
title: 'Xamarin.Essentials: Географическое положение'
description: Этот документ описывает класс географического положения в Xamarin.Essentials, который предоставляет API для получения текущей координаты географического положения устройства.
ms.assetid: 8F66092C-13F0-4FEE-8AA5-901D5F79B357
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 11749107403fc99e1d49b63ee3b50ff105abaa57
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080291"
---
# <a name="xamarinessentials-geolocation"></a>Xamarin.Essentials: Географическое положение

![Предварительная версия NuGet](~/media/shared/pre-release.png)

**Geolocation** класс предоставляет API для получения текущей координаты географического положения устройства.

## <a name="getting-started"></a>Начало работы

Чтобы получить доступ к **Geolocation** функциональные возможности, требуется следующая настройка платформ:

# <a name="androidtabandroid"></a>[Android](#tab/android)

Грубое и нормально расположение разрешения являются обязательными и должен быть настроен в проекте Android. Кроме того Если приложение предназначено для Android 5.0 (уровень API 21) или более поздней версии, необходимо объявить, ваше приложение использует аппаратные компоненты в файле манифеста. Это можно сделать одним из следующих способов:

Откройте **AssemblyInfo.cs** файл **свойства** папки и добавьте:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessCoarseLocation)]
[assembly: UsesPermission(Android.Manifest.Permission.AccessFineLocation)]
[assembly: UsesFeature("android.hardware.location", Required = false)]
[assembly: UsesFeature("android.hardware.location.gps", Required = false)]
[assembly: UsesFeature("android.hardware.location.network", Required = false)]
```

Или обновление манифеста Android.

Откройте **AndroidManifest.xml** файл **свойства** папки и добавьте следующий код внутри блока **манифеста** узла:

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-feature android:name="android.hardware.location" android:required="false" />
<uses-feature android:name="android.hardware.location.gps" android:required="false" />
<uses-feature android:name="android.hardware.location.network" android:required="false" />
```

Или щелкните правой кнопкой мыши проект Android и откройте свойства проекта. В разделе **Android манифеста** найти **требуемые разрешения:** области и проверка **ACCESS_COARSE_LOCATION** и **ACCESS_FINE_LOCATION**разрешения. Будет автоматически обновлена **AndroidManifest.xml** файла.

# <a name="iostabios"></a>[iOS](#tab/ios)

Ваше приложение **Info.plist** должен содержать `NSLocationWhenInUseUsageDescription` ключом, чтобы получить доступ к расположению устройства.

Откройте редактор plist-файл и добавьте **конфиденциальность - расположение при в используйте Описание использования** свойство и введите значение для отображения пользователю.

Или вручную изменить файл и добавьте следующие строки:

```xml
<key>NSLocationWhenInUseUsageDescription</key>
<string>This app needs access location when open.</string>
```

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Необходимо задать `Location` разрешение для приложения. Это можно сделать, открыв **Package.appxmanifest** и выбрав команду **возможности** вкладка и проверка **расположение**.

-----

## <a name="using-geolocation"></a>С помощью географического положения

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

Geoloation API также пользователю будет предложено ввести разрешений при необходимости.

Вы можете получить последним известным [расположение](xref:Xamarin.Essentials.Location) устройства путем вызова `GetLastKnownLocationAsync` метод. Это часто выполняется быстрее, выполнив полный запрос, но может быть менее точным.

```csharp
try
{
    var location = await Geolocation.GetLastKnownLocationAsync();

    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}, Altitude: {location.Altitude}");
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Handle not supported on device exception
}
catch (PermissionException pEx)
{
    // Handle permission exception
}
catch (Exception ex)
{
    // Unable to get location
}
```

Высота над уровнем моря не всегда доступна. Если он недоступен, `Altitude` свойство может быть `null` или значение может равняться нулю. Если высота над уровнем моря, значение в метрах выше над уровнем моря. 

Чтобы запросить текущего устройства [расположение](xref:Xamarin.Essentials.Location) координаты, `GetLocationAsync` может использоваться. Следует передать в полной `GeolocationRequest` и `CancellationToken` , так как он может занять некоторое время, чтобы получить расположение устройства.

```csharp
try
{
    var request = new GeolocationRequest(GeolocationAccuracy.Medium);
    var location = await Geolocation.GetLocationAsync(request);

    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}, Altitude: {location.Altitude}");
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Handle not supported on device exception
}
catch (PermissionException pEx)
{
    // Handle permission exception
}
catch (Exception ex)
{
    // Unable to get location
}
```

## <a name="geolocation-accuracy"></a>Точность определения географического положения

В следующей таблице приведены точность каждой платформы:

### <a name="lowest"></a>Наименьшая

| Platform | Расстояние (в метрах) |
| --- | --- |
| Android | 500 |
| iOS | 3000 |
| UWP | 1000 - 5000 |

### <a name="low"></a>Low

| Platform | Расстояние (в метрах) |
| --- | --- |
| Android | 500 |
| iOS | 1000. |
| UWP | 300 - 3000 |

### <a name="medium-default"></a>Средний (по умолчанию)

| Platform | Расстояние (в метрах) |
| --- | --- |
| Android | 100 - 500 |
| iOS | 100 |
| UWP | 30-500 |

### <a name="high"></a>High

| Platform | Расстояние (в метрах) |
| --- | --- |
| Android | 0 - 100 |
| iOS | 10 |
| UWP | < = 10 |

### <a name="best"></a>Лучше всего

| Platform | Расстояние (в метрах) |
| --- | --- |
| Android | 0 - 100 |
| iOS | ~0 |
| UWP | < = 10 |

<a name="calculate-distance" />

## <a name="distance-between-two-locations"></a>Расстояние между двумя расположениями

[ `Location` ](xref:Xamarin.Essentials.Location) И [ `LocationExtensions` ](xref:Xamarin.Essentials.LocationExtensions) классы определяют `CalculateDistance` методы, которые позволяют выполнять вычисление расстояния между двумя географических регионах. С помощью этого вычисляемого расстояние учитывает дороги или другими методами и называется просто кратчайшее расстояние между двумя точками на поверхности Земли, также _расстояние высококачественных круг_ или разговорной речи, расстояние «как функцию crow.»

Ниже приведен пример:

```csharp
Location boston = new Location(42.358056, -71.063611);
Location sanFrancisco = new Location(37.783333, -122.416667);
double miles = Location.CalculateDistance(boston, sanFrancisco, DistanceUnits.Miles);
```

`Location` Конструктор имеет аргументы широты и долготы в указанном порядке. Положительное значение широты севернее экватора, и это положительное долготы к востоку от главного меридиана. Используйте последним аргументом для `CalculateDistance` для указания мили или километры. `Location` Класс также определяет `KilometersToMiles` и `MilesToKilometers` методы для преобразования между двумя единицы измерения.

## <a name="api"></a>API

- [Географическое расположение исходного кода](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Geolocation)
- [Документация по API географическое положение](xref:Xamarin.Essentials.Geolocation)
