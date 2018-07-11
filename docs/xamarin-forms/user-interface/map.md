---
title: Xamarin.Forms карты
description: В этой статье описывается использование класса Xamarin.Forms Map использовать собственный API карты на каждой платформе для предоставления знакомой карт для пользователей.
ms.prod: xamarin
ms.assetid: 59CD1344-8248-406C-9144-0C8A67141E5B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: d74ad52a2926fb30a528aeba29156259390c3edf
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37947248"
---
# <a name="xamarinforms-map"></a>Xamarin.Forms карты

_Xamarin.Forms использует карту собственного API-интерфейсы на каждой платформе._

Xamarin.Forms.Maps использует карту собственного API-интерфейсы на каждой платформе. Это обеспечивает быстрый, знакомы карт для пользователей, но также означает, что придерживаться каждой платформы определенные требования к API требуются некоторые шаги по настройке.
После настройки конфигурации `Map` работает так же, как и любой другой элемент Xamarin.Forms в общем коде контроль.

* [Сопоставляет инициализации](#Maps_Initialization) — с помощью `Map` требуется дополнительный код инициализации при запуске.
* [Конфигурация платформы](#Platform_Configuration) -часть конфигурации соотносятся с рабочими требуются для каждой платформы.
* [Использование карт в C#](#Using_Maps) -отображение сопоставляет и контакты, с помощью C#.
* [Использование карт в XAML](#Using_Xaml) -отображения карты с помощью XAML.

Элемент управления map был использован в [MapsSample](https://developer.xamarin.com/samples/WorkingWithMaps/) пример, как показано ниже.

 [![Карты в образце MobileCRM](map-images/maps-zoom-sml.png "пример элемента управления Map")](map-images/maps-zoom.png#lightbox "пример элемента управления Map")

Функциональностью карты можно улучшить путем создания [сопоставить пользовательское средство отрисовки](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md).

<a name="Maps_Initialization" />

## <a name="maps-initialization"></a>Инициализация сопоставлений

При добавлении карты в приложении Xamarin.Forms **Xamarin.Forms.Maps** — отдельный пакет NuGet, следует добавить в каждый проект в решении.
В Android это также имеет зависимость от GooglePlayServices (другой NuGet), который загружается автоматически при добавлении Xamarin.Forms.Maps.

После установки пакета NuGet, требуется код инициализации в каждый проект приложения *после* `Xamarin.Forms.Forms.Init` вызова метода. Для устройств iOS используйте следующий код:

```csharp
Xamarin.FormsMaps.Init();
```

На устройстве Android необходимо передать те же параметры, что `Forms.Init`:

```csharp
Xamarin.FormsMaps.Init(this, bundle);
```

Для универсальной платформы Windows (UWP), используйте следующий код:

```csharp
Xamarin.FormsMaps.Init("INSERT_AUTHENTICATION_TOKEN_HERE");
```

Добавьте этот вызов в следующих файлах для каждой платформы:

-  **iOS** -файл AppDelegate.cs, в `FinishedLaunching` метод.
-  **Android** -MainActivity.cs файле `OnCreate` метод.
-  **UWP** -файл MainPage.xaml.cs, в `MainPage` конструктор.

После добавления пакета NuGet и вызван метод инициализации внутри каждого applcation `Xamarin.Forms.Maps` интерфейсы API, которые могут использоваться в общий проект библиотеки .NET Standard или общий проект кода.

<a name="Platform_Configuration" />

## <a name="platform-configuration"></a>Конфигурация платформы

Дополнительные действия по настройке необходимы на некоторых платформах, прежде чем будет отображать карту.

### <a name="ios"></a>iOS

Чтобы открыть окно службы определения местоположения в iOS, необходимо задать следующие ключи в **Info.plist**:

- iOS 11
    - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) — для использования службы определения местоположения, когда приложение используется
    - [`NSLocationAlwaysAndWhenInUseUsageDescription`](https://developer.apple.com/documentation/corelocation/choosing_the_authorization_level_for_location_services/requesting_always_authorization?language=objc) — для использования службы определения местоположения все время
- iOS 10 и более ранних версий
    - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) — для использования службы определения местоположения, когда приложение используется
    - [`NSLocationAlwaysUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18) — для использования службы определения местоположения все время    

Для поддержки iOS 11 и более ранних версий, можно включить все три ключа: `NSLocationWhenInUseUsageDescription`, `NSLocationAlwaysAndWhenInUseUsageDescription`, и `NSLocationAlwaysUsageDescription`.

XML-представление для этих ключей в **Info.plist** показан ниже. Необходимо обновить `string` значения в том, как приложение использует сведения о расположении:

```xml
<key>NSLocationAlwaysUsageDescription</key>
<string>Can we use your location at all times?</string>
<key>NSLocationWhenInUseUsageDescription</key>
<string>Can we use your location when your app is being used?</string>
<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
<string>Can we use your location at all times?</string>
```

**Info.plist** записи также может быть добавлено в **источника** представления во время редактирования **Info.plist** файла:

![Info.plist для iOS 8](map-images/ios8-map-permissions.png "записи Info.plist требуется iOS 8")


### <a name="android"></a>Android

Чтобы использовать [v2 Google Maps API](https://developers.google.com/maps/documentation/android/) на устройстве Android необходимо создать ключ API и добавить его в проект Android.
Следуйте инструкциям в документе Xamarin на [Получение ключа Google Maps API v2](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).
После выполнения этих инструкций, вставьте ключ API в **Properties/AndroidManifest.xml** файл (Просмотр исходного кода и поиска или обновить следующий элемент):

```xml
<application ...>
    <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="YOUR_API_KEY" />
</application>
```

Не является допустимым ключом API элемента управления будет отображаться как серый квадрат на устройстве Android.

> [!NOTE]
> Обратите внимание, что, чтобы пакет APK для доступа к Google карты, необходимо включить отпечатки пальцев SHA-1 и упаковать имена для каждого хранилища ключей (отладочную и окончательную), которые используются для входа пакет APK. Например если вы используете один компьютер для отладки и другой компьютер для создания выпуска APK, должно содержать отпечаток SHA-1 сертификата из хранилища ключей отладки первого компьютера и отпечаток SHA-1 сертификата из хранилища ключей выпуска из второй компьютер. Также не забудьте изменить учетные данные ключа, если приложения **имя пакета** изменения. См. в разделе [Получение ключа Google Maps API v2](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).

Также необходимо включить соответствующие разрешения, щелкнув правой кнопкой мыши на проект Android и выбрав **параметры > Создать > приложение Android** и отсчет следующие:

* `AccessCoarseLocation`
* `AccessFineLocation`
* `AccessLocationExtraCommands`
* `AccessMockLocation`
* `AccessNetworkState`
* `AccessWifiState`
* `Internet`

На следующем снимке экрана показаны некоторые из них:

![Разрешения, необходимые для Android](map-images/android-map-permissions.png "необходимые разрешения для Android")

Последние два необходимы, так как приложения требуют подключения к сети для загрузки данных карты. Ознакомьтесь с Android [разрешения](http://developer.android.com/reference/android/Manifest.permission.html) для получения дополнительных сведений.

### <a name="universal-windows-platform"></a>Универсальная платформа Windows 

Для работы с картами на универсальной платформе Windows необходимо создать маркер авторизации. Дополнительные сведения см. в разделе [запроса ключа проверки подлинности карты](https://msdn.microsoft.com/library/windows/apps/mt219694.aspx) на сайте MSDN.

Затем маркер проверки подлинности следует указывать в `FormsMaps.Init("AUTHORIZATION_TOKEN")` вызов метода, для проверки подлинности приложения с помощью Bing Maps.

<a name="Using_Maps" />

## <a name="using-maps"></a>Использование карт

См. в разделе [MapPage.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/MobileCRM/MobileCRM.Shared/Pages/MapPage.cs) в образце MobileCRM пример использования элемента управления картой в коде. Простой `MapPage` класс может выглядеть как - уведомления, новый `MapSpan` создается для размещения представления карты:

```csharp
public class MapPage : ContentPage {
    public MapPage() {
        var map = new Map(
            MapSpan.FromCenterAndRadius(
                    new Position(37,-122), Distance.FromMiles(0.3))) {
                IsShowingUser = true,
                HeightRequest = 100,
                WidthRequest = 960,
                VerticalOptions = LayoutOptions.FillAndExpand
            };
        var stack = new StackLayout { Spacing = 0 };
        stack.Children.Add(map);
        Content = stack;
    }
}
```

### <a name="map-type"></a>Тип схемы

Также можно изменить, задав содержимого карты `MapType` свойства, чтобы показать карту регулярных улицы (по умолчанию), вспомогательных изображений или их сочетание.

```csharp
map.MapType == MapType.Street;
```

Допустимые `MapType` значения:

-  Гибридные
-  Вспомогательные
-  Улица (по умолчанию)


### <a name="map-region-and-mapspan"></a>Области карты и MapSpan

Как показано в приведенном выше фрагменте кода, указав `MapSpan` экземпляр конструктора карты задает первоначального представления (центральную точку и масштаб) сопоставления при его загрузке. `MoveToRegion` Метод класса map затем можно изменить уровень позиции или измените масштаб карты. Существует два способа для создания нового `MapSpan` экземпляр:

-  **MapSpan.FromCenterAndRadius()** -статический метод, чтобы создать диапазон из `Position` и указав `Distance` .
-  **New () MapSpan** -конструктор, использующий `Position` и преобразование градусов, широты и долготы для отображения.


Чтобы изменить уровень масштаба карты, не изменяя расположение, создайте новый `MapSpan` с использованием текущего расположения из `VisibleRegion.Center` свойство элемента управления картой. Объект `Slider` может использоваться для управления масштабом карты следующим образом (Однако масштабирование непосредственно в элементе управления картой не может обновить сейчас значение ползунка):

```csharp
var slider = new Slider (1, 18, 1);
slider.ValueChanged += (sender, e) => {
    var zoomLevel = e.NewValue; // between 1 and 18
    var latlongdegrees = 360 / (Math.Pow(2, zoomLevel));
    map.MoveToRegion(new MapSpan (map.VisibleRegion.Center, latlongdegrees, latlongdegrees));
};
```

 [![Карты с zoom](map-images/maps-zoom-sml.png "масштаб элемента управления Map")](map-images/maps-zoom.png#lightbox "масштаб элемента управления Map")

### <a name="map-pins"></a>Булавки

Расположений можно пометить на карте с `Pin` объектов.

```csharp
var position = new Position(37,-122); // Latitude, Longitude
var pin = new Pin {
            Type = PinType.Place,
            Position = position,
            Label = "custom pin",
            Address = "custom detail info"
        };
map.Pins.Add(pin);
```

 `PinType` может быть присвоено одно из следующих значений, которые могут влиять на способ ПИН-кода в подготовке к просмотру (в зависимости от платформы):

-  Универсальный
-  Место
-  SavedPin
-  SearchResult


<a name="Using_Xaml" />

## <a name="using-xaml"></a>С помощью Xaml

Maps также может быть размещен в макетах Xaml, как показано в этом фрагменте кода.

```xaml
<?xml version="1.0" encoding="UTF-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps"
    x:Class="MapDemo.MapPage">
    <StackLayout VerticalOptions="StartAndExpand" Padding="30">
        <maps:Map WidthRequest="320" HeightRequest="200"
            x:Name="MyMap"
            IsShowingUser="true"
            MapType="Hybrid"
        />
    </StackLayout>
</ContentPage>
```

`MapRegion` И `Pins` можно задать в коде с помощью `MyMap` ссылка (или все, что называется карты). Обратите внимание, что дополнительная `xmlns` для ссылки на элементы управления Xamarin.Forms.Maps требуется определение пространства имен.

```csharp
MyMap.MoveToRegion(
    MapSpan.FromCenterAndRadius(
        new Position(37,-122), Distance.FromMiles(1)));
```

<a name="Summary" />

## <a name="summary"></a>Сводка

Xamarin.Forms.Maps — это отдельный NuGet, которая должна быть добавлена в каждый проект в решение Xamarin.Forms. Как и некоторые действия по настройке iOS, Android и универсальной платформы Windows необходим дополнительный код инициализации.

После настройки сопоставляет API может использоваться для отрисовки карты с ПИН-код маркерами в несколько строк кода. Карты можно улучшить с помощью [пользовательское средство отрисовки](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md).


## <a name="related-links"></a>Связанные ссылки

- [MapsSample](https://developer.xamarin.com/samples/WorkingWithMaps/)
- [Пользовательское средство отрисовки карты](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
- [Примеры Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
