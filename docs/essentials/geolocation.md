---
title: 'Xamarin.Essentials: Geolocation'
description: В этом документе описан класс Geolocation в Xamarin.Essentials, который предоставляет API для получения текущих геолокационных координат устройства.
ms.assetid: 8F66092C-13F0-4FEE-8AA5-901D5F79B357
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 533620fbfca9f2a7a235fe65e038b6dd89aa95a9
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674906"
---
# <a name="xamarinessentials-geolocation"></a>Xamarin.Essentials: Geolocation

![Предварительная версия NuGet](~/media/shared/pre-release.png)

Класс **Geolocation** предоставляет API для получения текущих геолокационных координат устройства.

## <a name="get-started"></a>Начало работы

[!include[](~/essentials/includes/get-started.md)]

Чтобы проверить функциональность класса **Geolocation**, нужно создать описанную ниже конфигурацию для конкретной платформы:

# <a name="androidtabandroid"></a>[Android](#tab/android)

Требуются разрешения Coarse и Fine Location, которые следует настроить в проекте Android. Кроме того, если приложение предназначено для платформы Android 5.0 (уровень API 21) или более поздней версии, в файле манифеста необходимо объявить, что приложение использует аппаратные возможности. Для этого можно применить любой из следующих методов:

Откройте файл **AssemblyInfo.cs** в папке **Properties** и добавьте в него:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessCoarseLocation)]
[assembly: UsesPermission(Android.Manifest.Permission.AccessFineLocation)]
[assembly: UsesFeature("android.hardware.location", Required = false)]
[assembly: UsesFeature("android.hardware.location.gps", Required = false)]
[assembly: UsesFeature("android.hardware.location.network", Required = false)]
```

Или обновите манифест Android:

Откройте файл **AndroidManifest.xml** в папке **Properties** и добавьте приведенный ниже код в узел **manifest**:

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-feature android:name="android.hardware.location" android:required="false" />
<uses-feature android:name="android.hardware.location.gps" android:required="false" />
<uses-feature android:name="android.hardware.location.network" android:required="false" />
```

Или щелкните правой кнопкой мыши проект Android и откройте свойства проекта. В разделе **Манифест Android** найдите область **Требуемые разрешения:** и установите флажок для разрешений **ACCESS_COARSE_LOCATION** и **ACCESS_FINE_LOCATION**. Это действие автоматически обновляет файл **AndroidManifest.xml**.

# <a name="iostabios"></a>[iOS](#tab/ios)

Приложение **Info.plist** должно содержать раздел `NSLocationWhenInUseUsageDescription`, чтобы получить доступ к расположению устройства.

Откройте редактор plist, добавьте свойство **Privacy — Location When In Use Usage Description** и введите значение, чтобы отобразить пользователя.

Кроме того, вы можете изменить текстовый файл вручную и добавить в него следующий текст:

```xml
<key>NSLocationWhenInUseUsageDescription</key>
<string>This app needs access location when open.</string>
```

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Необходимо задать разрешение `Location` для приложения. Для этого откройте файл **Package.appxmanifest**, выберите вкладку **Возможности** и установите флажок **Расположение**.

-----

## <a name="using-geolocation"></a>Использование геолокации

Добавьте в свой класс ссылку на Xamarin.Essentials:

```csharp
using Xamarin.Essentials;
```

В API геолокации пользователю также будет предложено предоставить разрешения при необходимости.

Вы можете получить последнее известное [расположение](xref:Xamarin.Essentials.Location) устройства, вызвав метод `GetLastKnownLocationAsync`. Зачастую эта операция быстрее, чем выполнение всего запроса, но не так точна.

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

Значения высоты не всегда доступно. Если она недоступна, свойство `Altitude` может быть со значением `null` или равным нулю. Если высота доступна, значение указывается в метрах над уровнем моря. 

Чтобы запросить текущие координаты [расположения](xref:Xamarin.Essentials.Location) устройства, можно использовать `GetLocationAsync`. Лучше всего передать полный `GeolocationRequest` и `CancellationToken`, так как получение расположения устройства может занять некоторое время.

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

## <a name="geolocation-accuracy"></a>Точность геолокации

В следующей таблице приведены сведения о точности для каждой платформы:

### <a name="lowest"></a>Наименьшая

| Platform | Расстояние (в метрах) |
| --- | --- |
| Android | 500 |
| iOS | 3000 |
| UWP | 1000–5000 |

### <a name="low"></a>Low

| Platform | Расстояние (в метрах) |
| --- | --- |
| Android | 500 |
| iOS | 1000. |
| UWP | 300–3000 |

### <a name="medium-default"></a>Среднее (по умолчанию)

| Platform | Расстояние (в метрах) |
| --- | --- |
| Android | 100–500 |
| iOS | 100 |
| UWP | 30–500 |

### <a name="high"></a>High

| Platform | Расстояние (в метрах) |
| --- | --- |
| Android | 0–100 |
| iOS | 10 |
| UWP | <= 10 |

### <a name="best"></a>Лучшее

| Platform | Расстояние (в метрах) |
| --- | --- |
| Android | 0–100 |
| iOS | ~0 |
| UWP | <= 10 |

<a name="calculate-distance" />

## <a name="distance-between-two-locations"></a>Расстояние между двумя расположениями

Классы [`Location`](xref:Xamarin.Essentials.Location) и [`LocationExtensions`](xref:Xamarin.Essentials.LocationExtensions) определяют методы `CalculateDistance`, которые дают возможность вычисления расстояния между двумя географическими расположениями. При расчете не учитываются дороги или другие пути. Это самое короткое расстояние между двумя точками на поверхности Земли, также известное как _расстояние по дуге большого круга_ или, говоря простым языком, расстояние напрямик.

Ниже приведен пример:

```csharp
Location boston = new Location(42.358056, -71.063611);
Location sanFrancisco = new Location(37.783333, -122.416667);
double miles = Location.CalculateDistance(boston, sanFrancisco, DistanceUnits.Miles);
```

В конструкторе `Location` аргументы широты и долготы указаны в следующем порядке. Положительные значения широты находятся к северу от экватора, а положительные значения долготы — к востоку от нулевого меридиана. Используйте последний аргумент `CalculateDistance`, чтобы указать единицы измерения: мили или километры. Класс `Location` также определяет методы `KilometersToMiles` и `MilesToKilometers` для преобразования между двумя единицами измерения.

## <a name="api"></a>API

- [Geolocation source code](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Geolocation) (Исходный код для геолокации)
- [Документация по API геолокации](xref:Xamarin.Essentials.Geolocation)
