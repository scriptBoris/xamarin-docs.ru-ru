---
title: С помощью карты Google API в приложении
description: Как реализовать возможности v2 Google Maps API Xamarin.Android приложения.
ms.prod: xamarin
ms.assetid: C0589878-2D04-180E-A5B9-BB41D5AF6E02
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/25/2018
ms.openlocfilehash: a0e010a8300eb4b4452737e34d2f55a35ab95428
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935143"
---
# <a name="using-the-google-maps-api-in-your-application"></a>С помощью API-интерфейса Google карты в приложении

С помощью приложения Maps отлично, но иногда требуется включить maps непосредственно в приложении. Помимо встроенной схемы приложения, также предлагает Google [встроенное сопоставление API для Android](https://developers.google.com/maps/documentation/android/).
Maps API подходит для случаев, где вы хотите поддерживать дополнительные контролировать процесс сопоставления. Объекты, которые возможны Maps API включают:

-  Программное изменение окна просмотра карты.
-  Добавление и настройка маркеры.
-  Аннотация карты с надписями.

В отличие от неподдерживаемые Google Maps Android API v1, v2 API, карты Google Android является частью [службы Google Play](http://developer.android.com/google/play-services/index.html).
Таким образом необходимо, чтобы выполнить некоторые обязательные предварительные условия, раньше, чем это можно использовать в приложении Xamarin.Android API Google Android карты.


## <a name="google-maps-api-prerequisites"></a>Google сопоставляет API предварительные требования

Несколько элементов должен быть настроен перед тем как использовать API карт, включая:

-  Установка пакета SDK служб Google Play
-  Создание эмулятор с интерфейсами API Google
-  Получить ключ API карт
-  Укажите необходимые разрешения



### <a name="install-the-google-play-services-sdk"></a>Установка пакета SDK служб Google Play

Службы Google Play — это технология из Google, позволяет использовать преимущества различных функций Google, например Google +, выставление счетов в приложении и карт Android приложениям. Эти функции доступны на устройствах, как фоновые службы, которые содержатся в [APK служб воспроизвести Google](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en).

Приложения Android взаимодействуют с службы Google Play с помощью клиентской библиотеки службы Google Play. Эта библиотека содержит интерфейсы и классы для отдельных служб, например карты. Следующая диаграмма показывает связь между Android приложения и службы Google Play:

![Диаграмма, иллюстрирующая обновление APK воспроизведение служб Google магазина Google Play](maps-api-images/play-services-diagram.png)

Android Maps API предоставляется как часть службы Google Play.
Прежде чем приложение Xamarin.Android можно использовать Maps API, пакета SDK служб воспроизвести Google должно быть установлено и привязан. Через диспетчер Android SDK установлен пакет SDK служб воспроизвести Google. На следующем рисунке показан where в диспетчер Android SDK клиента служб Google Play можно найти:

![Службы Google Play отображается под Android SDK Manager дополнения](maps-api-images/image01.png)

> [!NOTE]
> Службы Google Play APK — лицензированный продукт, возможно, отсутствует на всех устройствах. Если он не установлен, карты Google не работать на устройстве.


#### <a name="binding-google-play-services"></a>Google Play привязки служб

После установки клиентской библиотеки службы Google Play должен быть привязан библиотекой Xamarin.Android Java привязки. Это можно сделать двумя способами.

-  **Использовать Google воспроизвести служб Maps NuGet** -это наиболее простой способ (доступно только в Xamarin.Android 4.8 или более поздней версии).
   Установка [Xamarin Google воспроизвести Services Maps NuGet](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Maps); также будут установлены все пакеты зависимостей службы Google Play.
   В оставшейся части в этом руководстве основное внимание уделяется этот подход.

-  **Вручную связать клиентской библиотеки службы Google Play** -это более сложный подход, единственным способом для Xamarin.Android 4.4 или Xamarin.Android 4.6 для привязки пакета SDK служб воспроизвести Google.
   Вручную привязки клиентской библиотеки службы Google Play выходит за рамки настоящего документа, но пример того, как сделать это, можно найти в [карты и расположение демонстрации образец v3](https://github.com/xamarin/monodroid-samples/tree/master/MapsAndLocationDemo_v3) на Github.


#### <a name="adding-the-google-play-services-map-package"></a>Добавление карты пакета служб Google Play

Чтобы добавить пакет карты служб воспроизвести Google, щелкните правой кнопкой мыши **ссылки** папку проекта в обозревателе решений выберите **управление пакетами NuGet...** :

![Обозреватель решений, отображающий управление пакетами NuGet контекстном меню пункт в списке ссылок](maps-api-images/image02.png)

При этом откроется **диспетчера пакетов NuGet**. Нажмите кнопку **Обзор** и введите **карты воспроизвести служб Xamarin Google** в поле поиска. Выберите **Xamarin.GooglePlayServices.Maps** и нажмите кнопку **установить**. (Если ранее были установлены этот пакет, нажмите кнопку **обновление**.):

[![Диспетчер пакетов NuGet с выбранного пакета Xamarin.GooglePlayServices.Maps](maps-api-images/image03-sml.png)](maps-api-images/image03.png#lightbox)

Обратите внимание, что следующие пакеты зависимостей, также установлены:

-   **Xamarin.GooglePlayServices.Base**
-   **Xamarin.GooglePlayServices.Basement**
-   **Xamarin.GooglePlayServices.Tasks**



### <a name="create-an-emulator-with-google-apis"></a>Создание эмулятор с интерфейсами API Google

Хотя это не рекомендуется, можно установить эмулятор для поддержки API Android карты. Эмулятор должен быть настроен для целевой 17 уровень API Google (Android 4.2.2) или более поздней версии. На следующем снимке экрана образа эмулятора настроен для API уровня 19: 

![Диспетчер эмулятора Android с AVD, настроенных для API уровня 19](maps-api-images/image04.png)



### <a name="obtain-a-google-maps-api-key"></a>Получить ключ API Google карты

Последним шагом является получить ключ Google Maps API (Обратите внимание, что нельзя повторно использовать ключ API из предыдущих версий карты Google v1). Сведения о том, как получить и использовать ключ API с Xamarin.Android см. в разделе [получения ключа API Maps Google](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).
 


### <a name="specify-the-required-permissions"></a>Укажите необходимые разрешения

Следующие разрешения должен быть указан в **AndroidManifest.XML** для API Android карты Google:

-  **Доступ к состоянию сети** &ndash; Maps API должен иметь возможность проверить, если его можно загрузить мозаичных элементов.

-  **Доступ к Интернету** &ndash; необходим доступ к Интернету для загрузки плитки карты и взаимодействия с серверами воспроизвести Google для доступа к API.

-  **OpenGL ES v2** &ndash; приложения необходимо объявить требование OpenGL ES v2.

-  **Ключ API Google Maps** &ndash; ключ API используется для подтверждения, что приложение зарегистрировано и право на использование службы Google Play. В разделе [получить ключ API Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md) подробные сведения об этом ключе.

-  **Запись во внешнее хранилище** &ndash; Google Maps Android API будет кэшировать загруженные плитки на внешних носителях.

-  **Доступ к Google веб-службам** &ndash; приложению разрешения на доступ к Google веб-служб, которые поддерживают API Android карты.

-  **Разрешения для уведомления о служб воспроизвести Google** &ndash; приложение должно быть предоставлено разрешение для получения уведомлений, удаленного из службы Google Play.

-  **Доступ к поставщикам расположение** &ndash; это необязательные разрешения.
   Они позволит `GoogleMap` класса для отображения расположения устройства на карте.


Ниже приведен пример параметров, которые должны быть добавлены **AndroidManifest.XML**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionName="4.5" package="com.xamarin.docs.android.mapsandlocationdemo2" android:versionCode="6">
    <uses-sdk android:minSdkVersion="14" android:targetSdkVersion="17" />

    <!-- Google Maps for Android v2 requires OpenGL ES v2 -->
    <uses-feature android:glEsVersion="0x00020000" android:required="true" />

    <!-- We need to be able to download map tiles and access Google Play Services-->
    <uses-permission android:name="android.permission.INTERNET" />

    <!-- Allow the application to access Google web-based services. -->
    <uses-permission android:name="com.google.android.providers.gsf.permission.READ_GSERVICES" />

    <!-- Google Maps for Android v2 will cache map tiles on external storage -->
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

    <!-- Google Maps for Android v2 needs this permission so that it may check the connection state as it must download data -->
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <!-- Permission to receive remote notifications from Google Play Services -->
    <!-- Notice here that we have the package name of our application as a prefix on the permissions. -->
    <uses-permission android:name="<PACKAGE NAME>.permission.MAPS_RECEIVE" />
    <permission android:name="<PACKAGE NAME>.permission.MAPS_RECEIVE" android:protectionLevel="signature" />

    <!-- These are optional, but recommended. They will allow Maps to use the My Location provider. -->
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />


    <application android:label="@string/app_name">
        <!-- Put your Google Maps V2 API Key here. -->
        <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="YOUR_API_KEY" />
        <meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />
    </application>
</manifest>
```


## <a name="the-googlemap-class"></a>Класс GoogleMap

После занимается необходимые компоненты, пора начать разработку приложения и использовать API Android карты. [GoogleMap](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/GoogleMap) класс является основным API, который Xamarin.Android приложение будет использовать для отображения и работы с картами Google Android. Этот класс должен выполнить следующие действия:

-  Взаимодействие с службы Google Play, чтобы разрешить приложению веб-службе Google.

-  Загрузка, кэширование и отображения мозаичных элементов.

-  Отображение элементов управления пользовательского интерфейса, такие как сдвига и масштабирования для пользователя.

-  Рисование маркеры и геометрических фигур на картах.

`GoogleMap` Добавляется к действию в одном из двух способов:

-  **MapFragment** - [MapFragment](http://developer.android.com/reference/com/google/android/gms/maps/MapFragment.html) является специализированные фрагмент, который выступает в качестве базового для `GoogleMap` объекта. `MapFragment` Необходим уровень Android API, 12 или более поздней версии.
   Можно использовать старые версии Android [SupportMapFragment](http://developer.android.com/reference/com/google/android/gms/maps/SupportMapFragment.html).

-  **MapView** - [MapView](https://developer.xamarin.com/api/type/Android.GoogleMaps.MapView/) является специализированным подклассом представления, который может выступать в качестве узла для `GoogleMap` объекта. Пользователи этого класса все методы жизненного цикла действия, которые необходимо перенаправить `MapView` класса.

Каждый из этих контейнеров предоставляют `Map` свойство, которое возвращает экземпляр класса `GoogleMap`. Необходимо предоставить предпочтений [MapFragment](http://developer.android.com/reference/com/google/android/gms/maps/MapFragment.html) как простой API, который сокращает объем стандартный код, который разработчик вручную необходимо реализовать.


### <a name="adding-a-mapfragment-to-an-activity"></a>Добавление действия MapFragment

Снимке экрана ниже приведен пример очень простой `MapFragment`:

[![Снимок экрана устройства, отображение фрагмента карты](maps-api-images/image05-sml.png)](maps-api-images/image05.png#lightbox)

Как и другие классы фрагмент, существует два способа добавить это `MapFragment` для действия:

-   **Декларативно** - `MapFragment` можно добавлять при помощи XML-файл макета для действия. В следующем фрагменте XML показан пример использования `fragment` элемента:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <fragment xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/map"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              class="com.google.android.gms.maps.MapFragment" />
    ```

-   **Программным путем** - `MapFragment` можно добавить программно, как описано далее.

Для программного добавления `MapFragment`, необходимо реализовать свои действия `IOnMapReadyCallback` интерфейса. Поскольку инициализация `GoogleMap` объектов может занять некоторое время для завершения как API обменивается данными с Google Play, необходимо предоставить обратный вызов, который уведомляет приложения при `GoogleMap` готов.

Сначала добавьте `IOnMapReadyCallback` для `Activity` объявления класса.
Пример:

```csharp
public class MapWithMarkersActivity : Activity, IOnMapReadyCallback
```

Затем в `OnCreate` метод, добавьте `MapFragment` как показано в следующем примере кода ( `GoogleMapOptions` класс описан далее в этом руководстве):

```csharp
_mapFragment = FragmentManager.FindFragmentByTag("map") as MapFragment;
if (_mapFragment == null)
{
    GoogleMapOptions mapOptions = new GoogleMapOptions()
        .InvokeMapType(GoogleMap.MapTypeSatellite)
        .InvokeZoomControlsEnabled(false)
        .InvokeCompassEnabled(true);

    FragmentTransaction fragTx = FragmentManager.BeginTransaction();
    _mapFragment = MapFragment.NewInstance(mapOptions);
    fragTx.Add(Resource.Id.map, _mapFragment, "map");
    fragTx.Commit();
}
_mapFragment.GetMapAsync(this);
```

Объект `GoogleMap` должна быть получена с помощью `GetMapAsync`, как показано в конце предыдущего примера кода &ndash; это автоматически инициализирует система Maps и представление. (Обратите внимание, что этот метод не использует `await` / `async` семантику &ndash; `Async` реализуется путем Android.) При `GoogleMap` объект готов, ваше приложение вызывает Android `OnMapReady` метод (который должен реализовать как часть `IOnMapReadyCallback` интерфейс). Пример:

```csharp
public void OnMapReady (GoogleMap map)
{
    _map = map;
}
```

В приведенном выше примере кода `OnMapReady` инициализирует обратный вызов `_map` переменных на созданный `GoogleMap` объекта.

В качестве примера использования этого результата когда `OnResume` — он вызван, можно проверить на предмет `_map` отлично от null. Если `_map` равно `GoogleMap` объекта, `OnResume` может вызывать методы ее, чтобы добавить маркеры и переместить его камеры указанной широты и долготы. Полный пример кода см. в разделе [SimpleMapDemo](https://github.com/xamarin/monodroid-samples/tree/master/MapsAndLocationDemo_v3/SimpleMapDemo).



### <a name="map-types"></a>Типы карт

Имеются следующие пять разных типа карт из API сопоставляет Google.

-  **Обычный** -это тип карты по умолчанию. Он показывает дороги и важных компонентов естественным, а также некоторые искусственных Достопримечательности (например, здания и мосты).

-  **Вспомогательные** -на этой карте показано вспомогательных фотографии.

-  **Гибридные** — на этой карте показано вспомогательных фотографии и сопоставляет пути.

-  **Рельефа** -это в первую очередь показывает топографические возможности с помощью некоторых дороги.

-  **Нет** -это сопоставление не загружает все плитки, оно обрабатывается как пустой сетки.


На следующем рисунке показаны три различных типов карт, из слева направо (обычный, гибридный вид рельефа):

[![Три сопоставить примере снимки экрана: обычный, гибридных и рельефа](maps-api-images/map-types-sml.png)](maps-api-images/map-types.png#lightbox)

`GoogleMap.MapType` Свойство используется для задания или изменения, какой тип карты отображается. В следующем фрагменте кода показано, как отображать вспомогательные карту.

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...
if (_map != null) {
    _map.MapType = GoogleMap.MapTypeSatellite;
}
```


### <a name="googlemap-properties"></a>Свойства GoogleMap

`GoogleMap` определяет несколько свойств, определяющих функциональные возможности и внешнего вида карты. Один из способов настроить начальное состояние `GoogleMap` заключается в передаче [GoogleMapOptions](http://developer.android.com/reference/com/google/android/gms/maps/GoogleMapOptions.html) объекта при создании `MapFragment`. В следующем фрагменте кода является одним из примеров использования `GoogleMapOptions` объекта при создании `MapFragment`:

```csharp
GoogleMapOptions mapOptions = new GoogleMapOptions()
    .InvokeMapType(GoogleMap.MapTypeSatellite)
    .InvokeZoomControlsEnabled(false)
    .InvokeCompassEnabled(true);

FragmentTransaction fragTx = FragmentManager.BeginTransaction();
_mapFragment = MapFragment.NewInstance(mapOptions);
fragTx.Add(Resource.Id.map, _mapFragment, "map");
fragTx.Commit();
```

Другой способ настройки `GoogleMap` выделяется с помощью задания значения [UiSettings](http://developer.android.com/reference/com/google/android/gms/maps/UiSettings.html) свойство объекта карты. В следующем образце кода показано, как настроить `GoogleMap` отображать элементы управления масштабом и компаса:

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...
if (_map != null) {
    _map.UiSettings.ZoomControlsEnabled = true;
    _map.UiSettings.CompassEnabled = true;
}
```


## <a name="interacting-with-the-map"></a>Взаимодействие с карты

Android Maps API предоставляет API который разрешает действия изменить точки зрения, добавление маркеров, поместите специальные наложения или Рисование геометрических фигур. В этом разделе описывается, как выполнить некоторые из этих задач в Xamarin.Android.

### <a name="changing-the-viewpoint"></a>Изменение точки зрения

Карты являются modelled как плоский плоскости на экране, в зависимости от проекцию Меркатора. Представление схемы, представляет *камеры* привлекательных сверху на этой плоскости. Положение камеры можно управлять, изменив расположение, масштаб, наклон и влияет. [CameraUpdate](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/CameraUpdate) класс используется для перемещения расположения камеры. `CameraUpdate` объекты не будут создаваться напрямую, вместо Maps API предоставляет [CameraUpdateFactory](http://developer.android.com/reference/com/google/android/gms/maps/CameraUpdateFactory.html) класса.

Один раз `CameraUpdate` объект был создан, он передается как параметр либо [GoogleMap.MoveCamera](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/GoogleMap.html#moveCamera%28com.google.maps.CameraUpdate%29) или [GoogleMap.AnimateCamera](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/GoogleMap.html#animateCamera%28com.google.maps.CameraUpdate%29) методы. `MoveCamera` Метод обновляет схему мгновенно при `AnimateCamera` метод обеспечивает переход smooth, анимацию.

Этот фрагмент кода приведен простой пример демонстрирует использование `CameraUpdateFactory` для создания `CameraUpdate` уровень масштаба карты, увеличивает на единицу:

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...
if (_map != null) {
    _map.MoveCamera(CameraUpdateFactory.ZoomIn());
}
```

Предоставляет API карт [CameraPosition](http://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.html) которого будет проводить статистическое вычисление всех возможных значений для положение камеры. Экземпляр этого класса может быть предоставлен для [CameraUpdateFactory.NewCameraPosition](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/CameraUpdateFactory#newCameraPosition%28com.google.android.gms.maps.model.CameraPosition%29) метод, который будет возвращать `CameraUpdate` объекта. Также включает API карт [CameraPosition.Builder](http://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.Builder.html) класс, предоставляющий fluent API для создания `CameraPosition` объектов.
В следующем фрагменте кода показан пример создания `CameraUpdate` из `CameraPosition` и использовать его для изменения положения камеры на `GoogleMap`:

```csharp
LatLng location = new LatLng(50.897778, 3.013333);
CameraPosition.Builder builder = CameraPosition.InvokeBuilder();
builder.Target(location);
builder.Zoom(18);
builder.Bearing(155);
builder.Tilt(65);
CameraPosition cameraPosition = builder.Build();
CameraUpdate cameraUpdate = CameraUpdateFactory.NewCameraPosition(cameraPosition);

MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...
if (_map != null) {
    _map.MoveCamera(cameraUpdate);
}
```

В приведенном выше фрагменте кода в определенное расположение на схеме представлен [LatLng](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/model/LatLng) класса. Уровень масштаба равно 18. Влияет является компаса по часовой стрелке относительно Северная измерения. Наклон свойство управляет угол просмотра, оно указывает углом 25 градусов по вертикали. На следующем снимке экрана показано `GoogleMap` после выполнения предыдущего кода:

[![Пример схемы Google, отображение в указанном месте с tilted угол обзора](maps-api-images/image06-sml.png)](maps-api-images/image06.png#lightbox)


### <a name="drawing-on-the-map"></a>Рисование на карте

Android Maps API предоставляет API-Интерфейсы для рисования следующие элементы на карте:

-  **Маркеры** -это специальные значки, которые используются для идентификации в одном месте на карте.

-  **Перекрытия** -это образ, который может использоваться для идентификации коллекции для расположения или области на карте.

-  **Линий, кругов и многоугольников** -это интерфейсов API, позволяющих действий для добавления карты фигур.


#### <a name="markers"></a>Markers

Предоставляет API карт [маркер](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/model/Marker) класс, который инкапсулирует все данные об одном месте на карте. По умолчанию они используют стандартный значок, предоставляемые карты Google. Можно настроить внешний вид маркера и для ответа на щелчки мышью.


##### <a name="adding-a-marker"></a>Добавление маркера

Для добавления маркера к карте, необходимо создать новую [MarkerOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/MarkerOptions) объекта, а затем вызвать [AddMarker](http://developer.android.com/reference/com/google/android/gms/maps/GoogleMap.html#addMarker%28com.google.android.gms.maps.model.MarkerOptions%29) метод `GoogleMap` экземпляр. Этот метод будет возвращать [маркер](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/model/Marker) объекта.

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...
if (_map != null) {
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");
    _map.AddMarker(markerOpt1);
}
```

Название маркера будет отображаться в *информационное окно* при нажатии кнопки на маркер. На следующем снимке экрана показано, как выглядит этот маркер:

[![Пример схемы Google с маркером и окно сведений для Vimy ребра](maps-api-images/image07-sml.png)](maps-api-images/image07.png#lightbox)


##### <a name="customizing-a-marker"></a>Настройка маркера

Существует возможность настройки значка, используемого маркера за счет вызова `MarkerOptions.InvokeIcon` метод при добавлении к маркеру карты.
Этот метод принимает [BitmapDescriptor](http://developer.android.com/reference/com/google/android/gms/maps/model/BitmapDescriptor.html) объект, содержащий данные, необходимые для подготовки к просмотру значок. [BitmapDescriptorFactory](https://developer.android.com/reference/com/google/android/gms/maps/model/BitmapDescriptorFactory.html) класс предоставляет вспомогательные методы для упрощения создания `BitmapDescriptor`. В следующем списке перечислены некоторые из этих методов:

-   `DefaultMarker(float colour)` &ndash; С помощью метки карты Google по умолчанию, но изменить цвет.

-   `FromAsset(string assetName)` &ndash; Используйте пользовательский значок из указанного файла в папке средств.

-   `FromBitmap(Bitmap image)` &ndash; Используйте в качестве значка указанном точечном рисунке.

-   `FromFile(string fileName)` &ndash; Создайте пользовательский значок из файла по указанному пути.

-   `FromResource(int resourceId)` &ndash; Создание пользовательского значка из указанного ресурса.

В следующем фрагменте кода показан пример создания маркера голубой coloured по умолчанию.

```csharp
mapFrag.GetMapAsync(this);
...
if (_map != null)
{
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");
    markerOpt1.InvokeIcon(BitmapDescriptorFactory.DefaultMarker (BitmapDescriptorFactory.HueCyan));
    _map.AddMarker(markerOpt1);
}
```


#### <a name="info-windows"></a>Сведения о Windows

*Сведения о windows* — это специальные окна, всплывающее окно для отображения сведений для пользователя, когда они коснитесь определенного маркера. По умолчанию в окне сведения будут отображаться содержимое заголовка маркера. Если заголовок не присвоено, будет отображаться ни одного окна сведений. Одновременно могут отображаться только одно окно сведений.

Возможна настройка окна сведения путем реализации [GoogleMap.IInfoWindowAdapter](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/GoogleMap.InfoWindowAdapter) интерфейса. Существует два важных метода для этого интерфейса.

-  `public View GetInfoWindow(Marker marker)` &ndash; Этот метод вызывается для получения пользовательских информационное окно для маркера. Если он возвращает `null` , отрисовка окна по умолчанию будет использоваться. Если этот метод возвращает представление, представление будет располагаться внутри фрейма окна сведений.

-  `public View GetInfoContents(Marker marker)` &ndash; Этот метод будет вызван, только если GetInfoWindow возвращает `null` . Этот метод может возвращать `null` значение, если содержимое окна сведений отрисовки по умолчанию будет использоваться. В противном случае этот метод должен возвращать представление с содержимым окна сведения.

Информационное окно не обеспечивает динамическое представление — вместо Android будет преобразовать в статический растровое изображение представления и отображать, на изображении. Это означает, что информационное окно не может отвечать на любые события сенсорного экрана или жесты, а также он автоматически обновится. Чтобы обновить окно сведения, необходимые для вызова метода [GoogleMap.ShowInfoWindow](http://developer.android.com/reference/com/google/android/gms/maps/model/Marker.html#showInfoWindow()) метод.

На рисунке показаны некоторые примеры некоторых окон настраиваемые сведения. Изображение в левой части имеет ее содержимое настроены, хотя имеет образа в правой части окна и настроить содержимое его:

![Пример маркер windows для Мельбурн, включая значок и заполнение. Правом окне скругленные углы.](maps-api-images/marker-infowindows.png)


#### <a name="ground-overlays"></a>Перекрытия Наземный

В отличие от маркеры, которые идентифицируют определенное место на карте, [GroundOverlay](http://developer.android.com/reference/com/google/android/gms/maps/model/GroundOverlay.html) — это изображение, используемое для идентификации коллекции для расположения или области на карте.


##### <a name="adding-a-groundoverlay"></a>Добавление GroundOverlay

Добавление на карту наложения Наземный очень сходно с добавлением маркер на карту. Во-первых, [GroundOverlayOptions](http://developer.android.com/reference/com/google/android/gms/maps/model/GroundOverlayOptions.html) создан объект. Затем этот объект передается как параметр `GoogleMap.AddGroundOverlay` метод, который будет возвращать `GroundOverlay` объекта. Этот фрагмент кода является примером Добавление на карту наложения нуля:

```csharp
BitmapDescriptor image = BitmapDescriptorFactory.FromResource(Resource.Drawable.polarbear);
GroundOverlayOptions groundOverlayOptions = new GroundOverlayOptions()
    .Position(position, 150, 200)
    .InvokeImage(image);
GroundOverlay myOverlay = _map.AddGroundOverlay(groundOverlayOptions);
```

На следующем рисунке показан этот наложения на карте:

[![Пример схемы рисунком накладывается содержат полярной](maps-api-images/image09-sml.png)](maps-api-images/image09.png#lightbox)


#### <a name="lines-circles-and-polygons"></a>Линий, кругов и многоугольников

Существует три типа простые геометрические фигуры, которые могут быть добавлены к схеме.

-  **Ломаной линии** -это последовательность соединенных отрезков. Может пометить путь на карте или форме любой формы требуется.

-  **Многоугольник** -это замкнутую фигуру, помечающие области на карте.

-  **Круг** -это будет Рисование окружности на карте.



##### <a name="polylines"></a>Многоугольника

Объект [ломаной линии](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/model/Polyline) приведен перечень последовательных `LatLng` объекты, которые определяют вершины каждого сегмента линии. Ломаной создается путем первоначального создания `PolylineOptions` объекта и добавление точек на него. `PolylineOption` Объект затем передается `GoogleMap` путем вызова метода `AddPolyline` метод.

```csharp
PolylineOption rectOptions = new PolylineOption();
rectOptions.Add(new LatLng(37.35, -122.0));
rectOptions.Add(new LatLng(37.45, -122.0));
rectOptions.Add(new LatLng(37.45, -122.2));
rectOptions.Add(new LatLng(37.35, -122.2));
rectOptions.Add(new LatLng(37.35, -122.0)); // close the polyline - this makes a rectangle.
myMap.AddPolyline(rectOptions);
```


##### <a name="polygons"></a>Многоугольники

`Polygon`s очень похожи на `Polyline`s, однако они не открыты завершен. `Polygon`s находятся закрытые цикла и их внутренней части заполнено.
`Polygon`точно так же, как создаются `Polyline`, за исключением [GoogleMap.AddPolygon](http://developer.android.com/reference/com/google/android/gms/maps/GoogleMap.html#addPolygon(com.google.android.gms.maps.model.PolygonOptions)) вызванного метода.

В отличие от `Polyline`, `Polygon` автоматически закрывается. Когда `AddPolygon` вызывается метод будет автоматически закрыт off многоугольника путем рисования линии, соединяющей начальную и конечную точки. В следующем фрагменте кода создается прямоугольника с заливкой по той же области, как в предыдущем фрагменте кода `Polyline` примере.

```csharp
PolygonOptions rectOptions = new PolygonOptions();
rectOptions.Add(new LatLng(37.35, -122.0));
rectOptions.Add(new LatLng(37.45, -122.0));
rectOptions.Add(new LatLng(37.45, -122.2));
rectOptions.Add(new LatLng(37.35, -122.2));
// notice we don't need to close off the polygon
myMap.AddPolygon(rectOptions);
```


##### <a name="circles"></a>Окружности

Круги создаются путем создания первого экземпляра [CircleOption](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/model/CircleOptions) объекта, который укажет центра и радиус окружности в метрах. Круг рисуется на карте, вызвав [GoogleMap.AddCircle](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/GoogleMap#addCircle(com.google.android.gms.maps.model.CircleOptions)).
В следующем фрагменте кода показано, как Рисование окружности:

```csharp
CircleOptions circleOptions = new CircleOptions ();
circleOptions.InvokeCenter (new LatLng(37.4, -122.1));
circleOptions.InvokeRadius (1000);
_map.AddCircle (circleOptions);
```


## <a name="responding-to-events"></a>Реагирование на события

Существует три типа действий, которые пользователь может иметь с картой.

-  **Щелкните маркер** -пользователь перенаправляется на маркер.

-  **Перетащите маркер** -долго щелкнул пользователь на mparger

-  **Щелкните окно сведений** -пользователь нажал на информационное окно.

Каждое из этих событий обсуждаются более подробно ниже.


### <a name="marker-click-events"></a>Маркер события Click

Когда пользователь нажимает на маркер `MarkerClick` возникает событие и `GoogleMap.MarkerClickEventArgs` переданный. Этот класс содержит два свойства:

-  `GoogleMap.MarkerClickEventArgs.Handled` &ndash; Это свойство должно быть присвоено `true` для указания, что обработчик событий потребляет события. Если задано значение `false` поведения по умолчанию будет выполняться Помимо пользовательского поведения обработчика событий.

-  `P0` &ndash; Этот параметр неправильно имя является ссылкой на маркер, который возникает `MarkerClick` событий.


Этот фрагмент кода показан пример `MarkerClick` это приведет к изменению позиция камеры в новое место на карте:

```csharp
private void MapOnMarkerClick(object sender, GoogleMap.MarkerClickEventArgs markerClickEventArgs)
{
    markerClickEventArgs.Handled = true;
    Marker marker = markerClickEventArgs.P0;
    if (marker.Id.Equals(MyMarkerId)) // The ID of a specific marker the user clicked on.
    {
        _map.AnimateCamera(CameraUpdateFactory.NewLatLngZoom(new LatLng(20.72110, -156.44776), 13));
    }
    else
    {
        Toast.MakeText(this, String.Format("You clicked on Marker ID {0}", marker.Id), ToastLength.Short).Show();
    }
}
```


### <a name="marker-drag-events"></a>События перетаскивания маркеров

Это событие возникает, когда пользователь хочет перетащите маркер. По умолчанию маркеры не перетаскиваемые. Маркер можно задать как перетаскиваемые, задав `Marker.Draggable` свойства `true` или путем вызова `MarkerOptions.Draggable` метод с `true` как параметр.

Чтобы сначала перетащите маркер, пользователь должен долго, щелкните его и сохранить их пальца на карте. При перетаскивании их пальцем по экрану, переместит маркера. Когда палец пользователя отрывает вне экрана, маркер будет остаются без изменений.

Ниже приводятся различные события, которые будут созданы для перетаскиваемые маркера.

-   `GoogleMap.MarkerDragStart(object sender, GoogleMap.MarkerDragStartEventArgs e)` &ndash; Это событие возникает, когда пользователь перетаскивает сначала маркера.

-   `GoogleMap.MarkerDrag(object sender, GoogleMap.MarkerDragEventArgs e)` &ndash; Это событие возникает, как Перетаскивание маркера.

-   `GoogleMap.MarkerDragEnd(object sender, GoogleMap.MarkerDragEndEventArgs e)` &ndash; Это событие возникает при when пользователь закончил Перетаскивание маркера.

Каждый из `EventArgs` содержит одиночное свойство `P0` , ссылку на `Marker` объект, перетаскиваемый.


### <a name="info-window-click-events"></a>Информационное окно события Click

Одновременно можно отобразить только одно окно сведений. При нажатии на информационное окно в сопоставлении, вызывает объект карты `InfoWindowClick` событий. В следующем фрагменте кода показано, как подключить обработчик для события:

```csharp
private bool SetupMapIfNeeded()
{
    if (_map == null)
    {
        _map = _mapFragment.Map;
        if (_map != null)
        {
            _map.InfoWindowClick += MapOnInfoWindowClick;
            return true;
        }
        return false;
    }
    return true;
}

private void MapOnInfoWindowClick (object sender, GoogleMap.InfoWindowClickEventArgs e)
{
    Marker myMarker = e.P0;
    // Do something with marker.
}
```

Помните, что окна сведений с помощью статического `View` которого отображается в виде изображения на карте. Любой мини-приложений, таких как кнопки, флажки или текст представления, которые помещаются внутри окна сведения будут практически не изменяющихся и не может отвечать на любое из событий их целочисленные пользователя.



## <a name="related-links"></a>Связанные ссылки

- [Службы Google Play](http://developer.android.com/google/play-services/index.html)
- [Google сопоставляет v2 Android API](https://developers.google.com/maps/documentation/android/)
- [APK служб Google Play](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en)
- [Получить ключ API Google карты](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
