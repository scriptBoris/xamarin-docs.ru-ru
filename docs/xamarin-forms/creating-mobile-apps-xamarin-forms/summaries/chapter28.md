---
title: Сводка Глава 28. Расположение и карты
description: 'Создание мобильных приложений с помощью Xamarin.Forms: Сводка Глава 28. Расположение и карты'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F6E20077-687C-45C4-A375-31D4F49BBFA4
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: bcca7bc7ac56b745945b6d7daef3c4ddd634b486
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53059409"
---
# <a name="summary-of-chapter-28-location-and-maps"></a>Сводка Глава 28. Расположение и карты

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)

> [!NOTE]
> Заметки на этой странице указывать области, где различаются Xamarin.Forms материал, представленный в книге.

Xamarin.Forms поддерживает [ `Map` ](xref:Xamarin.Forms.Maps.Map) элемент, производный от `View`. Из-за специальные требования к платформе связанные с использованием карт, они реализуются в отдельной сборке, **Xamarin.Forms.Maps**и они используют другое пространство имен: `Xamarin.Forms.Maps`.

## <a name="the-geographic-coordinate-system"></a>Используется географическая система координат

Географическая система координат определяет позиции в объекте сферическую (или почти сферической) как Земли. Координата состоит из двух *широты* и *долготы* выражается в градусах.

Вызывается большому кругу `equator` значение находится ровно посредине между двумя полюса, через которые концептуально расширяет Земли в направлении оси.

### <a name="parallels-and-latitude"></a>Parallels широту и долготу

Угол измеряемый к северу или югу экватора от центра Земли метки строк равно широты, известный как *parallels*. Эти варьируются от 0 градусов в экватора до 90 градусов в Северной и Южной полюса. По соглашению виде широт северу от экватора, положительные значения, а югу от экватора таковы отрицательные значения.

### <a name="longitude-and-meridians"></a>Долгота и меридианы

Половины круги отлично от северного к южному строк равно долгота, а также называются *меридианов*. Эти уровни определены относительно меридиана в по Гринвичу, Англия. По соглашению долгот к востоку от меридиана положительные значения от 0 до 180 градусов, а долгот западу меридиана — отрицательные значения от 0 градусов, на которое &ndash;180 градусов.

### <a name="the-equirectangular-projection"></a>Равнопромежуточная проекция

Любой неструктурированный карты Земли представлены искажений. Если все строки из широты и долготы являются прямыми, и если равно различия в градусах широты и долготы соответствуют одинаковые расстояния на карте, результат будет *равнопромежуточная проекция*. Эта карта искажает областей ближе к полюсам, так как они горизонтально растянутый.

### <a name="the-mercator-projection"></a>Проекция Меркатора

Популярный *проекцию Меркатора* пытается компенсировать горизонтальной растягивания также растягивая эти области по вертикали. Это приводит к карту расположения гораздо большим, чем на самом деле это, но любой локальной сети довольно точно соответствуют фактическая область области рядом с полюса.

### <a name="map-services-and-tiles"></a>Сопоставления служб и плитки

Карта службы используют разновидность проекцию Меркатора вызывается `Web Mercator`. Карты служб доставки растрового изображения плиток на клиенте, в зависимости от расположения и масштаб уровня.

## <a name="getting-the-users-location"></a>Получение расположения пользователя

Xamarin.Forms `Map` классы не включают средства для получения географическое расположение пользователя, но это часто желательно при работе с картами, поэтому службы зависимостей необходимо обработать его.

> [!NOTE]
> Вместо этого можно использовать приложения Xamarin.Forms [ `Geolocation` ](~/essentials/geolocation.md) классов, включенных в Xamarin.Essentials.

### <a name="the-location-tracker-api"></a>Расположение инспектор API

[ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) решение содержит код для расположения средство отслеживания API. [ `GeographicLocation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/GeographicLocation.cs) Структура инкапсулирует широты и долготы. [ `ILocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/ILocationTracker.cs) Интерфейс определяет два метода для запуска и приостановки инспектор расположение и событие, если доступно новое расположение.

#### <a name="the-ios-location-manager"></a>Диспетчером местоположения iOS

Реализация iOS `ILocationTracker` — [ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/LocationTracker.cs) классе, который делает использование iOS [ `CLLocationManager` ](https://developer.xamarin.com/api/type/CoreLocation.CLLocationManager/).

#### <a name="the-android-location-manager"></a>Диспетчер Android расположение

Android реализация `ILocationTracker` — [ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/LocationTracker.cs) класс, который использует Android [ `LocationManager` ](https://developer.xamarin.com/api/type/Android.Locations.LocationManager/) класса.

#### <a name="the-uwp-geo-locator"></a>Локатор географически универсальной платформы Windows

Реализация универсальной платформы Windows `ILocationTracker` — [ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/LocationTracker.cs) класс, который использует UWP [ `Geolocator` ](/uwp/api/Windows.Devices.Geolocation.Geolocator).

### <a name="display-the-phones-location"></a>Отобразить расположение телефона

[ **WhereAmI** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/WhereAmI) образце используется средство отслеживания расположение для отображения расположения телефона, в том случае, как в тексте, так и на карте равноугольная проекция.

### <a name="the-required-overhead"></a>Требуется издержки

Необходим некоторый запас для **WhereAmI** использовании регистрации расположение. Во-первых, все проекты в **WhereAmI** решение должно иметь ссылки на соответствующие проекты в **Xamarin.FormsBook.Platform**и каждый **WhereAmI** проекта необходимо вызвать `Toolkit.Init` метод.

Некоторыми дополнительными издержками конкретных платформ, в виде разрешения расположения является обязательным.

#### <a name="location-permission-for-ios"></a>Разрешение для расположения для iOS

Для iOS **info.plist** файл должен содержать элементы, содержащие текст вопроса, предлагающее пользователю разрешить получение расположения пользователя.

#### <a name="location-permissions-for-android"></a>Разрешения расположения для Android

Android приложениях, которые извлекают из местоположения пользователя необходимо разрешение ACCESS_FILE_LOCATION в файле AndroidManifest.xml.

#### <a name="location-permissions-for-the-uwp"></a>Разрешения расположения для универсальной платформы Windows

Приложение универсальной платформы Windows должен иметь `location` возможность устройства помечены в файл Package.appxmanifest.

## <a name="working-with-xamarinformsmaps"></a>Работа с Xamarin.Forms.Maps

Ряд требований связанные с применением `Map` класса.

### <a name="the-nuget-package"></a>Пакет NuGet

**Xamarin.Forms.Maps** NuGet библиотеки необходимо добавить в решение приложения. Номер версии должен совпадать с **Xamarin.Forms** в настоящее время установки пакета.

### <a name="initializing-the-maps-package"></a>Инициализация сопоставлений пакета

Проекты приложений необходимо вызвать `Xamarin.FormsMaps.Init` метод после вызова `Xamarin.Forms.Forms.Init`.

### <a name="enabling-map-services"></a>Включение служб карты

Так как `Map` можно получить из местоположения пользователя, приложению необходимо получить разрешение для пользователя, как описано выше в разделе:

#### <a name="enabling-ios-maps"></a>Включение iOS сопоставляет

Приложения iOS с помощью `Map` требуется две строки в файле info.plist.

#### <a name="enabling-android-maps"></a>Включение Android сопоставляет

Ключ авторизации является обязательным для использования служб Google карты. Этот ключ будет вставлена в **AndroidManifest.xml** файла. Кроме того **AndroidManifest.xml** файла требуется `manifest` теги, участвующих в получении из местоположения пользователя.

#### <a name="enabling-uwp-maps"></a>Включение UWP сопоставляет

Приложение универсальной платформы Windows требует наличия ключа авторизации за использование карт Bing. Этот ключ передается в качестве аргумента `Xamarin.FormsMaps.Init` метод. Приложение необходимо также включить для службы определения местоположения.

### <a name="the-unadorned-map"></a>Недекорируемое карты

[ **MapDemos** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/MapDemos) Образец состоит из [MapsDemoHomePage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml) файл и [MapsDemoHomePage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml.cs) файл фонового кода, допускает переход к различным демонстрационными программами.

[BasicMapPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/BasicMapPage.xaml) демонстрирует, как отобразить [ `Map` ](xref:Xamarin.Forms.Maps.Map) представления. По умолчанию он отображает города Рим, но карты можно управлять этим пользователем.

Чтобы отключить горизонтальной и вертикальной прокрутки, задайте [ `HasScrollEnabled` ](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled) свойства `false`. Чтобы отключить масштабирование, задайте [ `HasZoomEnabled` ](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) для `false`. Эти свойства могут не работать на всех платформах.

### <a name="streets-and-terrain"></a>Улиц и ландшафта

Различные типы карт можно отобразить, задав `Map` свойство [ `MapType` ](xref:Xamarin.Forms.Maps.Map.MapType) типа [ `MapType` ](xref:Xamarin.Forms.Maps.MapType), перечисление с тремя элементами:

- [`Street`](xref:Xamarin.Forms.Maps.MapType.Street), значение по умолчанию
- [`Satellite`](xref:Xamarin.Forms.Maps.MapType.Satellite)
- [`Hybrid`](xref:Xamarin.Forms.Maps.MapType.Hybrid)

[MapTypesPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypesPage.xaml) файла показан способ использования типа "переключатель" для выбора типа карты. Он использует [ `RadioButtonManager` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioButtonManager.cs) в класс [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) на основе библиотеки и класс [MapTypeRadioButton.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypeRadioButton.xaml) файл.

### <a name="map-coordinates"></a>Координаты

Программа может получить текущую область, `Map` отображение через [ `VisibleRegion` ](xref:Xamarin.Forms.Maps.Map.VisibleRegion) свойство. Это свойство является *не* поддерживаемый привязываемые свойства, и отсутствует механизм уведомлений указать после его изменения, чтобы программа, которая хочет отслеживать свойство, возможно, следует использовать таймер для этой цели.

`VisibleRegion` имеет тип [ `MapSpan` ](xref:Xamarin.Forms.Maps.MapSpan), класс с четырьмя свойствами только для чтения:

- [`Center`](xref:Xamarin.Forms.Maps.MapSpan.Center) типа [`Position`](xref:Xamarin.Forms.Maps.Position)
- [`LatitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LatitudeDegrees) типа `double`, указывающее высоту отображаемую область карты
- [`LongitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LongitudeDegrees) типа `double`, указывающее ширину отображаемую область карты
- [`Radius`](xref:Xamarin.Forms.Maps.MapSpan.Radius) типа [ `Distance` ](xref:Xamarin.Forms.Maps.Distance), показывающее размер наибольшего круговой области, видимой на карте

`Position` и `Distance` — обе структуры. `Position` Определяет два свойства только для чтения, настроенных с помощью [ `Position` конструктор](xref:Xamarin.Forms.Maps.Position.%23ctor(System.Double,System.Double)):

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude)
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude)

`Distance` должны давать расстояние независимые единицы путем преобразования между метрики и английской системе мер. Объект `Distance` значение могут создаваться несколькими способами:

- [`Distance` Конструктор](xref:Xamarin.Forms.Maps.Distance.%23ctor(System.Double)) с расстояние в метрах
- [`Distance.FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters(System.Double)) статический метод
- [`Distance.FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers(System.Double)) статический метод
- [`Distance.FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles(System.Double)) статический метод

Значение можно получить из трех свойств:

- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters) типа `double`
- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers) типа `double`
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles) типа `double`

[MapCoordinatesPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml) файл содержит несколько `Label` элементов для отображения `MapSpan` сведения. [MapCoordinatesPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml.cs) файл с выделенным кодом используется таймер для сохранения согласованности сведений, обновлять, так как он управляет карты.

### <a name="position-extensions"></a>Положение расширения

Эта книга с именем новой библиотеки [ **Xamarin.FormsBook.Toolkit.Maps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit.Maps) содержит типы, карты, но независимо от платформы. [ `PositionExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) Класс имеет `ToString` метод `Position`и метод для вычисления расстояния между двумя `Position` значения.

### <a name="setting-an-initial-location"></a>Настройка начальной местоположения

Можно вызвать [ `MoveToRegion` ](xref:Xamarin.Forms.Maps.Map.MoveToRegion(Xamarin.Forms.Maps.MapSpan)) метод `Map` программно задать расположение и масштабирования уровня на карте. Аргумент имеет тип `MapSpan`. Можно создать `MapSpan` с помощью одной из следующих:

- [`MapSpan` Конструктор](xref:Xamarin.Forms.Maps.MapSpan.%23ctor(Xamarin.Forms.Maps.Position,System.Double,System.Double)) с `Position`и диапазон широты и долготы
- [`MapSpan.FromCenterAndRadius`](xref:Xamarin.Forms.Maps.MapSpan.FromCenterAndRadius(Xamarin.Forms.Maps.Position,Xamarin.Forms.Maps.Distance)) с помощью `Position` и radius

Можно также создать новую `MapSpan` из существующей структуры с помощью методов [ `ClampLatitude` ](xref:Xamarin.Forms.Maps.MapSpan.ClampLatitude(System.Double,System.Double)) или [ `WithZoom` ](xref:Xamarin.Forms.Maps.MapSpan.WithZoom(System.Double)).

[WyomingPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml) файл и [WyomingPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml.cs) файл кода демонстрируется использование `MoveToRegion` метод для отображения состояния Вайоминг.

Также можно использовать [ `Map` конструктор](xref:Xamarin.Forms.Maps.Map.%23ctor(Xamarin.Forms.Maps.MapSpan)) с `MapSpan` для инициализации расположения карты. [XamarinHQPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/XamarinHQPage.xaml) демонстрирует, как это сделать полностью в XAML для отображения Xamarin штаб-квартиру в Сан-Франциско.

### <a name="dynamic-zooming"></a>Динамическое масштабирование

Можно использовать `Slider` динамически Масштаб карты. [RadiusZoomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml) файл и [RadiusZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml.cs) файл кода программной части показано, как изменить радиус карты на основе `Slider` значение.

[LongitudeZoomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml) файл и [LongitudeZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml.cs) файл с выделенным кодом отображения альтернативный подход, лучше работает на устройстве Android, но оба подхода успешно работает в Windows платформы.

### <a name="the-phones-location"></a>Расположение телефона

[ `IsShowingUser` ](xref:Xamarin.Forms.Maps.Map.IsShowingUser) Свойство `Map` работает немного по-разному на каждой платформе, как [ShowLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ShowLocationPage.xaml) демонстрирует файл:

- В iOS синяя точка обозначает расположение телефона, но вам необходимо вручную перейти туда
- В Android, отображается значок, что при передаче перемещает карту, чтобы расположение телефона
- UWP похож на устройства iOS, но иногда автоматически перейдет в расположение

**MapDemos** вводом пытается подражать Android подход определив основе значок кнопки в зависимости от проекта [MyLocationButton.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml) файл и [MyLocationButton.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml.cs) файл с выделенным кодом.

[GoToLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml) файл и [GoToLocationPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml.cs) файл с выделенным кодом Используйте эту кнопку, чтобы перейти к местоположению телефона.

### <a name="pins-and-science-museums"></a>ПИН-кодов и музеи обработки и анализа

Наконец `Map` класс определяет [ `Pins` ](xref:Xamarin.Forms.Maps.Map.Pins) свойство типа `IList<Pin>`. [ `Pin` ](xref:Xamarin.Forms.Maps.Pin) Класс определяет четыре свойства:

- [`Label`](xref:Xamarin.Forms.Maps.Pin.Label) типа `string`— обязательное свойство
- [`Address`](xref:Xamarin.Forms.Maps.Pin.Address) типа `string`, удобное для восприятия дополнительный адрес
- [`Position`](xref:Xamarin.Forms.Maps.Pin.Position) типа `Position`, указывающее, где отображается ПИН-кода на карте
- [`Type`](xref:Xamarin.Forms.Maps.Pin.Type) типа [ `PinType` ](xref:Xamarin.Forms.Maps.PinType), перечисление, которое не используется

**MapDemos** проект содержит файл [ScienceMuseums.xml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Data/ScienceMuseums.xml), обработки и анализа музеи, в США, в котором перечислены и [ `Locations` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Locations.cs) и [ `Site` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Site.cs) классы для десериализации данных.

[ScienceMuseumsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml) файл и [ScienceMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml.cs) ПИН-коды отображения файла кода для этих музеи обработки и анализа на карте. Когда пользователь касается ПИН-код, он отображает адрес и веб-сайт для музей.

### <a name="the-distance-between-two-points"></a>Расстояние между двумя точками

[ `PositionExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) Класс содержит [ `DistanceTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs#L88) метод с упрощенной Вычисление расстояния между двумя географических расположений.

Это используется в [LocalMuseumsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml) файл и [LocalMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml.cs) файл с выделенным кодом для отображения музей расстояние от расположения пользователя также:

[![Тройной снимок экрана страницы локального музеи](images/ch28fg28-small.png "расстояние в расположение")](images/ch28fg28-large.png#lightbox "расстояние в расположение")

Программа также демонстрирует, как динамически ограничить количество ПИН-кодов, исходя из расположения карты.

## <a name="geocoding-and-back-again"></a>Геокодирование и обратно

[ **Xamarin.Forms.Maps** ](xref:Xamarin.Forms.Maps) сборка также содержит [ `Geocoder` ](xref:Xamarin.Forms.Maps.Geocoder) класса [ `GetPositionsForAddressAsync` ](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync(System.String)) метод, который преобразует адрес текста в ноль или более возможных географического положения и другой метод [ `GetAddressesForPositionAsync` ](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync(Xamarin.Forms.Maps.Position)) , преобразует в другом направлении.

[GeocoderRoundTrip.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml) файл и [GeocoderRoundTrip.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml.cs) эта функция позволяет продемонстрировать файл с выделенным кодом.



## <a name="related-links"></a>Связанные ссылки

- [Глава 28 полнотекстового поиска (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch28-Aug2016.pdf)
- [Глава 28-примеры](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)
- [Xamarin.Forms карты](~/xamarin-forms/user-interface/map.md)
