---
title: С помощью Google Maps API в приложении
description: Инструкции по реализации Google Maps API v2 функций в приложении Xamarin.Android.
ms.prod: xamarin
ms.assetid: C0589878-2D04-180E-A5B9-BB41D5AF6E02
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/07/2018
ms.openlocfilehash: 8df8a4f5e59675164e7d06b682c61094078edc4f
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171044"
---
# <a name="using-the-google-maps-api-in-your-application"></a>В приложении используется Google Maps API

С помощью приложения карт отлично, но иногда требуется включить maps непосредственно в приложении. Помимо встроенной приложения на базе карт, Google также предлагает [встроенное сопоставление API для Android](https://developers.google.com/maps/documentation/android-sdk/intro).
API карт подходит для случаев, где вы хотите поддерживать больший контроль над работой сопоставления. Благодаря API карт относятся:

-  Программное изменение окна просмотра карты.
-  Добавление и настройка маркеры.
-  Создание примечаний к карту с наложениями.

В отличие от уже устаревшего Google Maps Android API v1, v2 Google Maps Android API является частью [сервисы Google Play](https://developers.google.com/android/guides/overview).
Приложения Xamarin.Android должны соответствовать некоторые обязательные предварительные условия, прежде чем его можно использовать Google Maps Android API.


## <a name="google-maps-api-prerequisites"></a>Предварительные требования Google Maps API

Несколько шагов нужно выполнить перед тем как использовать API для карт, включая:

-  [Получить ключ API для карт](#obtain-maps-key)
-  [Установка пакета SDK служб Google Play](#install-gps-sdk)
-  [Установите пакет Xamarin.GooglePlayServices.Maps из NuGet](#install-gpsmaps-nuget)
-  [Задать обязательные разрешения](#declare-permissions)
-  [При необходимости создайте эмуляторе с API-интерфейсы Google](#create-emulator-with-google-api)


### <a name="a-nameobtain-maps-key-obtain-a-google-maps-api-key"></a><a name="obtain-maps-key" />Получение ключа API Google Maps

Первым шагом является получение ключа Google Maps API (Обратите внимание, что вы не сможете использовать ключ API из предыдущих версий API Google Maps v1). Сведения о том, как получить и использовать ключ API с помощью Xamarin.Android см. в разделе [получить ключ API карт Google](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).
 

### <a name="a-nameinstall-gps-sdk--install-the-google-play-services-sdk"></a><a name="install-gps-sdk" /> Установка пакета SDK служб Google Play

Сервисы Google Play — это технология от Google, позволяющий приложениям Android, чтобы воспользоваться преимуществами различных функций Google Google +, выставление счетов в приложении и схем. Эти функции доступны на устройствах Android, как фоновые службы, f, содержащихся в [APK-службы Google Play](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en).

Android приложения взаимодействуют с помощью службы Google Play через клиентскую библиотеку служб Google Play. Эта библиотека содержит интерфейсы и классы для отдельных служб, такие как карты. Следующей схеме показана связь между Android приложения и сервисы Google Play:

![Схема, иллюстрирующая Store Google Play, обновление APK служб Google Play](maps-api-images/play-services-diagram.png)

Android Maps API предоставляется как часть службы Google Play.
Прежде чем приложения Xamarin.Android можно использовать API для карт, пакет SDK служб Google Play должны устанавливаться с помощью [диспетчер пакетов SDK Android](~/android/get-started/installation/android-sdk.md). На следующем рисунке показан where в диспетчер Android SDK можно найти на клиенте служб Google Play:

![Сервисы Google Play подчеркивается дополнения Диспетчер пакетов SDK для Android](maps-api-images/image01.png)

> [!NOTE]
> Сервисы Google Play apk-ФАЙЛ — это лицензированный продукт, которая может отсутствовать на всех устройствах. Если он не установлен, затем Google карты не будет работать на устройстве.

### <a name="a-nameinstall-gpsmaps-nuget--install-the-xamaringoogleplayservicesmaps-package-from-nuget"></a><a name="install-gpsmaps-nuget" /> Установите пакет Xamarin.GooglePlayServices.Maps из NuGet

[Xamarin.GooglePlayServices.Maps пакета](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Maps) содержит привязки Xamarin.Android Google Play Services API для карт.
Чтобы добавить пакет Map-службы Google Play, щелкните правой кнопкой мыши **ссылки** папку проекта в обозревателе решений и выберите **управление пакетами NuGet...** :

![Обозреватель решений, отображающий управление пакетами NuGet в контекстном меню в списке ссылок](maps-api-images/image02.png)

Откроется **диспетчер пакетов NuGet**. Нажмите кнопку **Обзор** и введите **Xamarin Google Play Services карты** в поле поиска. Выберите **Xamarin.GooglePlayServices.Maps** и нажмите кнопку **установить**. (Если ранее были установлены этот пакет, нажмите кнопку **обновления**.):

[![Диспетчер пакетов NuGet с помощью выбранных пакетов Xamarin.GooglePlayServices.Maps](maps-api-images/image03-sml.png)](maps-api-images/image03.png#lightbox)

Обратите внимание на то, что также устанавливаются следующие пакеты зависимостей:

-   **Xamarin.GooglePlayServices.Base**
-   **Xamarin.GooglePlayServices.Basement**
-   **Xamarin.GooglePlayServices.Tasks**

### <a name="a-namedeclare-permissions--specify-the-required-permissions"></a><a name="declare-permissions" /> Задать обязательные разрешения

Приложения необходимо определить требования к оборудованию и разрешение для использования Google Maps API.  Некоторые разрешения предоставляются автоматически, пакет SDK служб Google Play и нет необходимости для разработчика, чтобы явно добавить их в **AndroidManfest.XML**:

-  **Доступ к состоянию сети** &ndash; Maps API должен иметь возможность проверить, если он сможет загрузить фрагментов карты.

-  **Доступ к Интернету** &ndash; доступ к Интернету необходим для загрузки плитки карты и взаимодействия с серверами Google Play для доступа к API.

Следующие разрешения и функции должно быть указано в **AndroidManifest.XML** для API Android Google карт:

-  **OpenGL ES v2** &ndash; приложения необходимо объявить требования для версии OpenGL ES 2.

-  **Ключ API Google Maps** &ndash; ключ API используется для подтверждения, что приложение зарегистрировано и право на использование службы Google Play. См. в разделе [получения получения ключа API Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md) Дополнительные сведения об этом ключе.
   
- **Запросить устаревший клиент Apache HTTP** &ndash; приложений, предназначенных для Android 9.0 (уровень API 28) или более поздней версии необходимо указать, что устаревший клиент Apache HTTP библиотеку необязательно для использования. 

-  **Доступ к службам Google веб-** &ndash; приложению разрешения на доступ к Google веб-служб, которые поддерживают Android API для карт.

-  **Разрешения для уведомлений служб Google-Play** &ndash; приложение должно быть предоставлено разрешение для получения удаленных уведомлений от службы Google Play.

-  **Доступ к поставщики услуг размещения** &ndash; это необязательные разрешения.
   Они позволит `GoogleMap` класс для отображения расположения устройства на карте.


> [!NOTE]
> Очень старые версии пакета SDK Google Play требуется приложение для запроса `WRITE_EXTERNAL_STORAGE` разрешение. Это требование больше не требуется для последних привязки Xamarin для служб Google Play.

Ниже приведен пример настроек, которые необходимо добавить **AndroidManifest.XML**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionName="4.5" package="com.xamarin.docs.android.mapsandlocationdemo2" android:versionCode="6">
    <uses-sdk android:minSdkVersion="23" android:targetSdkVersion="28" />

    <!-- Google Maps for Android v2 requires OpenGL ES v2 -->
    <uses-feature android:glEsVersion="0x00020000" android:required="true" />
    
    <!-- Necessary for apps that target Android 9.0 or higher -->
    <uses-library android:name="org.apache.http.legacy" android:required="false" />


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

В дополнение к запрашиваются разрешения **AndroidManifest.XML**, приложение также необходимо выполнить разрешение проверки времени выполнения для `ACCESS_COARSE_LOCATION` и `ACCESS_FINE_LOCATION` разрешения. См. в разделе [Xamarin.Android разрешения](~/android/app-fundamentals/permissions.md) Дополнительные сведения о выполнении проверки разрешений во время выполнения.


### <a name="a-namecreate-emulator-with-google-api-create-an-emulator-with-google-apis"></a><a name="create-emulator-with-google-api" />Создание эмулятор с помощью API-интерфейсы Google

В случае, если физическое устройство Android с помощью службы Google Play не установлен, можно создать образ эмулятора для разработки. Дополнительные сведения см. в разделе [устройства](~/android/get-started/installation/android-emulator/device-manager.md).


## <a name="the-googlemap-class"></a>Класс GoogleMap

После условия соблюдены, пришло время приступить к разработке приложения и использовать API Android карты. [GoogleMap](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap) класс является основной API, который приложения Xamarin.Android будет использовать для отображения и взаимодействия с картами Google для Android. Этот класс имеет следующие обязанности:

-  Взаимодействие со службами Google Play для авторизации приложения к веб-службе Google.

-  Загрузка, кэширования и отображения мозаичных элементов.

-  Отображение элементов управления пользовательского интерфейса, такие как сдвига и масштабирования для пользователя.

-  Рисование маркеры и геометрических фигур на картах.

`GoogleMap` Добавляется к действию в одним из двух способов:

-  **MapFragment** - [MapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment) представляет собой специализированный фрагмент, который выступает в качестве узла для `GoogleMap` объекта. `MapFragment` Требуется Android API уровня 12 или более поздней версии.
   Можно использовать более старых версиях Android [SupportMapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/SupportMapFragment).  В этом руководстве подробно остановлюсь на использовании `MapFragment` класса.

-  **MapView** - [MapView](https://developers.google.com/android/reference/com/google/android/gms/maps/MapView) является специализированным подклассом представление, которое может выступать в качестве узла для `GoogleMap` объекта. Пользователи данного класса должен перенаправлять все методы жизненного цикла действия для `MapView` класса.

Каждая из этих контейнеров предоставляет `Map` свойство, которое возвращает экземпляр `GoogleMap`. Следует уделять предпочтений [MapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment) класса, так как это более простой API, который уменьшает объем стандартный код, который разработчик должен реализовать вручную.

### <a name="adding-a-mapfragment-to-an-activity"></a>Добавление действия MapFragment

Ниже приведен пример простого `MapFragment`:

[![Снимок экрана устройства, отображается фрагмент карты Google](maps-api-images/image05-sml.png)](maps-api-images/image05.png#lightbox)

Как и другие классы фрагмент, существует два способа добавления `MapFragment` в действие:

-   **Декларативно** - `MapFragment` могут добавляться с помощью XML-файл макета для действия. В следующем фрагменте XML показан пример использования `fragment` элемент:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <fragment xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/map"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              class="com.google.android.gms.maps.MapFragment" />
    ```

-   **Программно** - `MapFragment` можно программно создавать экземпляры с помощью [ `MapFragment.NewInstance` ](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment.html#newInstance()) метода и затем добавляются в действие. В этом фрагменте показан самый простой способ создания `MapFragment` и добавьте в действие:
    
    ```csharp
        var mapFrag = MapFragment.NewInstance();
        activity.FragmentManager.BeginTransaction()
                                .Add(Resource.Id.map_container, mapFrag, "map_fragment")
                                .Commit();

    ```

    Можно настроить `MapFragment` путем передачи [ `GoogleMapOptions` ](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions) объект `NewInstance`. Этот вопрос рассматривается в разделе [GoogleMap свойства](#googlemap_object) , появится позже в этом руководстве.

`MapFragment.GetMapAsync` Метод используется для инициализации [ `GoogleMap` ](#googlemap_object) , размещена в фрагменте и получить ссылку на объект-сопоставление, размещаемая `MapFragment`. Этот метод принимает объект, реализующий `IOnMapReadyCallback` интерфейс. 

Этот интерфейс содержит один метод, `IMapReadyCallback.OnMapReady(MapFragment map)` , будет вызываться, если это возможно для приложения для взаимодействия с `GoogleMap` объекта. В следующем фрагменте кода показано, как можно инициализировать действие Android `MapFragment` и реализовать `IOnMapReadyCallback` интерфейса:
```csharp
public class MapWithMarkersActivity : AppCompatActivity, IOnMapReadyCallback
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SetContentView(Resource.Layout.MapLayout);
    
        var mapFragment = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.map);
        mapFragment.GetMapAsync(this);
    
        // remainder of code omitted
    }
    
    public void OnMapReady(GoogleMap map)
    {
        // Do something with the map, i.e. add markers, move to a specific location, etc.
    }
}
```

### <a name="map-types"></a>Сопоставления типов

Существует пять разных типа карт из API сопоставляет Google:

-  **Обычный** -это тип карты по умолчанию. Он показывает, дороги и важных функций естественным вместе с некоторых искусственные баллы интерес (например, здания и мосты).

-  **Вспомогательные** -карта кода показывает вспомогательную фотография.

-  **Гибридные** — карта кода показывает фотографии вспомогательную, и сопоставляет пути.

-  **Ландшафта** -это в основном отображаются топографические функции с помощью некоторых дорогах.

-  **Нет** -это сопоставление не загружает никаких карточек, оно обрабатывается как пустую сетку.


На следующем рисунке показаны три различных типов карт, из слева направо (обычный, гибридное решение, ландшафта):

[![Три сопоставить снимки экрана: обычный, гибридных и ландшафта](maps-api-images/map-types-sml.png)](maps-api-images/map-types.png#lightbox)

`GoogleMap.MapType` Свойство позволяет задать или изменить, какой тип карты отображается. В следующем фрагменте кода показано, как для отображения карты вспомогательную.

```csharp
public void OnMapReady(GoogleMap map)
{
    map.MapType = GoogleMap.MapTypeHybrid;
}
```


### <a name="a-namegooglemapobject-googlemap-properties"></a><a name="googlemap_object" />Свойства GoogleMap

`GoogleMap` определяет несколько свойств, определяющих функциональные возможности и внешний вид карты. Один из способов настройки начальное состояние `GoogleMap` является передача [GoogleMapOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions) объекта при создании `MapFragment`. В следующем фрагменте кода приведен один пример использования `GoogleMapOptions` объекта при создании `MapFragment`:

```csharp
GoogleMapOptions mapOptions = new GoogleMapOptions()
    .InvokeMapType(GoogleMap.MapTypeSatellite)
    .InvokeZoomControlsEnabled(false)
    .InvokeCompassEnabled(true);

FragmentTransaction fragTx = FragmentManager.BeginTransaction();
mapFragment = MapFragment.NewInstance(mapOptions);
fragTx.Add(Resource.Id.map, mapFragment, "map");
fragTx.Commit();
```

Еще одним способом настроить `GoogleMap` — путем изменения свойства на [UiSettings](https://developers.google.com/android/reference/com/google/android/gms/maps/UiSettings) объекта map. В следующем образце кода показано, как настроить `GoogleMap` отображать элементы управления масштабом и компас:

```csharp
public void OnMapReady(GoogleMap map)
{
    map.UiSettings.ZoomControlsEnabled = true;
    map.UiSettings.CompassEnabled = true;
}
```

## <a name="interacting-with-the-googlemap"></a>Взаимодействие с GoogleMap

Android API для карт предоставляет интерфейсы API, которые позволяют действию изменить точки зрения, добавить маркеры, поместите специальные наложения или рисования геометрических фигур. В этом разделе описывается, как выполнить некоторые из этих задач в Xamarin.Android.

### <a name="changing-the-viewpoint"></a>Изменение точки зрения

Maps смоделированы как плоский плоскости на экране, в зависимости от проекцию Меркатора. На представлении карты — такой *камеры* выглядящие сверху на этой плоскости. Положение камеры можно управлять путем изменения расположения, масштабирования, наклона и отношения. [CameraUpdate](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdate) класс используется для перемещения расположения камеры. `CameraUpdate` не создаются экземпляры объектов непосредственно, вместо этого предоставляет API для карт [CameraUpdateFactory](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdateFactory) класса.

Один раз `CameraUpdate` объект был создан, он передается как параметр либо [GoogleMap.MoveCamera](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#moveCamera(com.google.android.gms.maps.CameraUpdate)) или [GoogleMap.AnimateCamera](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#animateCamera(com.google.android.gms.maps.CameraUpdate)) методы. `MoveCamera` Метод обновляет карты мгновенно при `AnimateCamera` метод предоставляет smooth, анимацию перехода.

В этом фрагменте кода приведен простой пример демонстрирует использование `CameraUpdateFactory` для создания `CameraUpdate` , увеличивают уровень масштаба карты от одного масштаба уровня:

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...

public void OnMapReady(GoogleMap map)
{   
    map.MoveCamera(CameraUpdateFactory.ZoomIn());
}
```

Предоставляет API для карт [CameraPosition](http://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.html) которого будет выполнять статистическую обработку всех возможных значений для положения камеры. Экземпляр этого класса можно указать для [CameraUpdateFactory.NewCameraPosition](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/CameraUpdateFactory#newCameraPosition%28com.google.android.gms.maps.model.CameraPosition%29) метод, который возвратит `CameraUpdate` объекта. Также включает API для карт [CameraPosition.Builder](http://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.Builder.html) класс, предоставляющий fluent API для создания `CameraPosition` объектов.
В следующем фрагменте кода показан пример создания `CameraUpdate` из `CameraPosition` и использовании, чтобы изменить положение камеры в `GoogleMap`:

```csharp
public void OnMapReady(GoogleMap map)
{
    LatLng location = new LatLng(50.897778, 3.013333);
    
    CameraPosition.Builder builder = CameraPosition.InvokeBuilder();
    builder.Target(location);
    builder.Zoom(18);
    builder.Bearing(155);
    builder.Tilt(65);
    
    CameraPosition cameraPosition = builder.Build();
    
    CameraUpdate cameraUpdate = CameraUpdateFactory.NewCameraPosition(cameraPosition);

    map.MoveCamera(cameraUpdate);
}
```

В предыдущем фрагменте кода, представленного в определенное расположение на карте [LatLng](https://developers.google.com/android/reference/com/google/android/gms/maps/model/LatLng) класса. Уровень масштаба установлено до 18, произвольная мера, масштабирования, используемые Google Maps. Отношения является компаса измерения, по часовой стрелке от севера. Свойство наклона управляет угол просмотра и задающий угол 25 градусов из по вертикали. На следующем снимке экрана показан `GoogleMap` после выполнения предыдущего кода:

[![Пример сопоставления Google, показывающий указанное расположение с мозаичный угол обзора](maps-api-images/image06-sml.png)](maps-api-images/image06.png#lightbox)


### <a name="drawing-on-the-map"></a>Рисование на карте

Android API для карт предоставляет API для рисования следующие элементы на карте:

-  **Маркеры** -это специальные значки, которые используются для идентификации в одном месте на карте.

-  **Наложений** -это образ, который может использоваться для идентификации коллекции для расположения или области на карте.

-  **Строки, многоугольники и окружностей** — это API, позволяющие действия, чтобы добавить фигуры на карту.


#### <a name="markers"></a>Markers

Предоставляет API для карт [маркер](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker) класс, который инкапсулирует все данные об одном месте на карте. По умолчанию маркер класс использует стандартный значок, предоставляемые Google Maps. Это можно настроить внешний вид маркера и отвечать на щелчок пользователя.


##### <a name="adding-a-marker"></a>Добавление маркера

Чтобы добавить маркер на карту, создайте новый [MarkerOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/MarkerOptions) объекта, а затем вызвать [AddMarker](http://developer.android.com/reference/com/google/android/gms/maps/GoogleMap.html#addMarker%28com.google.android.gms.maps.model.MarkerOptions%29) метод `GoogleMap` экземпляра. Этот метод будет возвращать [маркер](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker) объекта.

```csharp
public void OnMapReady(GoogleMap map)
{
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");
    
    map.AddMarker(markerOpt1);
}
```

Заголовок маркера будет отображаться в *информационное окно* когда пользователь касается к маркеру. Снимке экрана ниже показано, как выглядит этот маркер:

[![Пример сопоставления Google с помощью маркера и окно сведений для Vimy ребра](maps-api-images/image07-sml.png)](maps-api-images/image07.png#lightbox)


##### <a name="customizing-a-marker"></a>Настройка маркера

Можно настроить значок, используемый маркер путем вызова `MarkerOptions.InvokeIcon` метод при добавлении к маркеру карты.
Этот метод принимает [BitmapDescriptor](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptor) объект, содержащий данные, необходимые для подготовки к просмотру значок. [BitmapDescriptorFactory](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptorFactory) класс предоставляет вспомогательные методы для упрощения создания `BitmapDescriptor`. В следующем списке перечислены некоторые из этих методов:

-   `DefaultMarker(float colour)` &ndash; С помощью Google карты метки по умолчанию, но изменить цвет.

-   `FromAsset(string assetName)` &ndash; Используйте пользовательский значок из указанного файла в папке «ресурсы».

-   `FromBitmap(Bitmap image)` &ndash; Используется указанный рисунок в качестве значка.

-   `FromFile(string fileName)` &ndash; Создайте пользовательский значок из файла по указанному пути.

-   `FromResource(int resourceId)` &ndash; Создание пользовательского значка из указанного ресурса.

В следующем фрагменте кода показан пример создания маркера голубой цветной по умолчанию:

```csharp
public void OnMapReady(GoogleMap map)
{
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");
    
    var bmDescriptor = BitmapDescriptorFactory.DefaultMarker (BitmapDescriptorFactory.HueCyan);
    markerOpt1.InvokeIcon(bmDescriptor);
    
    map.AddMarker(markerOpt1);
}
```

#### <a name="info-windows"></a>Сведения о windows

*Сведения о windows* представляют собой специальные окна, всплывающее окно для отображения сведений для пользователя, когда они коснуться определенного маркера. По умолчанию в окне сведения будут отображаться содержимое заголовка маркер. Если заголовок не был назначен, будет отображаться окно не info. Только одно окно сведения могут отображаться одновременно.

Можно настроить путем реализации информационное окно [GoogleMap.IInfoWindowAdapter](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.InfoWindowAdapter) интерфейс. Существует два важных метода для этого интерфейса:

-  `public View GetInfoWindow(Marker marker)` &ndash; Этот метод вызывается для получения окна пользовательские сведения для маркера. Если он возвращает `null` , отрисовка окна по умолчанию будет использоваться. Если этот метод возвращает представление, этого представления будут размещены внутри окна сведений.

-  `public View GetInfoContents(Marker marker)` &ndash; Этот метод будет вызываться, только если GetInfoWindow возвращает `null` . Этот метод может возвращать `null` значение, если отрисовка по умолчанию содержимое окна информация будет использоваться. В противном случае этот метод должен возвращать представление с содержимое окна сведения.

Информационное окно не динамического представления — вместо этого Android будет преобразовать представление в точечные и вывести их на изображении. Это означает, что информационное окно не может отвечать на любые события касания и жестов, а также он автоматически обновится. Чтобы обновить окно привилегиями сведения, необходимые для вызова метода [GoogleMap.ShowInfoWindow](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker.html#showInfoWindow()) метод.

Ниже показаны некоторые примеры некоторых окон настраиваемые сведения. Изображение в левой части имеет его содержимого, настраивать, а изображение справа имеет свое окно и содержимое, настраивать со скругленными углами.

![Пример маркера windows для Мельбурн, включая значок и заполнение. Правом окне будут скругленные углы.](maps-api-images/marker-infowindows.png)

#### <a name="groundoverlays"></a>GroundOverlays

В отличие от маркеров, которые идентифицируют в определенное расположение на карте, [GroundOverlay](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlay) — это образ, который используется для идентификации коллекции расположения или области на карте.

##### <a name="adding-a-groundoverlay"></a>Добавление GroundOverlay

Добавление на карту наложения нуля сходно с добавлением маркер на карту. Во-первых, [GroundOverlayOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlayOptions) создается объект. Затем этот объект передается в качестве параметра [ `GoogleMap.AddGroundOverlay` ](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addGroundOverlay(com.google.android.gms.maps.model.GroundOverlayOptions)) метод, который будет возвращать `GroundOverlay` объекта. В этом фрагменте кода приведен пример добавления нуля наложение на карту.

```csharp
BitmapDescriptor image = BitmapDescriptorFactory.FromResource(Resource.Drawable.polarbear);
GroundOverlayOptions groundOverlayOptions = new GroundOverlayOptions()
    .Position(position, 150, 200)
    .InvokeImage(image);
GroundOverlay myOverlay = googleMap.AddGroundOverlay(groundOverlayOptions);
```

На следующем снимке экрана показан этого элемента управления overlay на карте:

[![Пример сопоставления с Выводимое изображение содержат полярной](maps-api-images/image09-sml.png)](maps-api-images/image09.png#lightbox)


#### <a name="lines-circles-and-polygons"></a>Линий, кругов и многоугольников

Существует три типа простые геометрические фигуры, которые могут быть добавлены на карту:

-  **Ломаной линии** -это последовательность соединенных отрезков. Его можно пометить путь на карте или создать геометрической фигуры.

-  **Круг** -это будет нарисовать круг на карте.

-  **Многоугольник** -это замкнутой фигуры для пометки области на карте.


##### <a name="polylines"></a>Ломаные линии

Объект [ломаной линии](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Polyline) приведен список последовательных `LatLng` объектов, которые определяют вершины каждого сегмента линии. Создав вначале создается ломаной `PolylineOptions` объекта и добавления в него точки. `PolylineOption` Объект затем передается `GoogleMap` путем вызова метода `AddPolyline` метод.

```csharp
PolylineOption rectOptions = new PolylineOption();
rectOptions.Add(new LatLng(37.35, -122.0));
rectOptions.Add(new LatLng(37.45, -122.0));
rectOptions.Add(new LatLng(37.45, -122.2));
rectOptions.Add(new LatLng(37.35, -122.2));
rectOptions.Add(new LatLng(37.35, -122.0)); // close the polyline - this makes a rectangle.

googleMap.AddPolyline(rectOptions);
```

##### <a name="circles"></a>Круги

Круги создаются путем создания первого экземпляра [CircleOption](https://developers.google.com/android/reference/com/google/android/gms/maps/model/CircleOptions) объекта, который укажет центра и радиус окружности в метрах. Элемент управления circle рисуется на карте, вызвав [GoogleMap.AddCircle](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addCircle(com.google.android.gms.maps.model.CircleOptions)).
В следующем фрагменте кода показано, как нарисовать круг:

```csharp
CircleOptions circleOptions = new CircleOptions ();
circleOptions.InvokeCenter (new LatLng(37.4, -122.1));
circleOptions.InvokeRadius (1000);

googleMap.AddCircle (circleOptions);
```


##### <a name="polygons"></a>Многоугольники

`Polygon`аналогичны `Polyline`s, однако они не открыты завершено. `Polygon`s замкнутым циклом и имеют их внутренней части заполнено.
`Polygon`будет создана в точно так же, как `Polyline`, за исключением [GoogleMap.AddPolygon](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addPolygon(com.google.android.gms.maps.model.PolygonOptions)) вызванного метода.

В отличие от `Polyline`, `Polygon` является объединение открывающего и закрывающего. Многоугольник будет закрыто `AddPolygon` метод путем рисования линии, соединяющей начальную и конечную точки. В следующем фрагменте кода создается прямоугольника с заливкой туда же, как в предыдущем фрагменте кода, в `Polyline` пример.

```csharp
PolygonOptions rectOptions = new PolygonOptions();
rectOptions.Add(new LatLng(37.35, -122.0));
rectOptions.Add(new LatLng(37.45, -122.0));
rectOptions.Add(new LatLng(37.45, -122.2));
rectOptions.Add(new LatLng(37.35, -122.2));
// notice we don't need to close off the polygon

googleMap.AddPolygon(rectOptions);
```


## <a name="responding-to-user-events"></a>Реагирование на события пользователей

Существует три типа действий, которые пользователь может иметь с картой.

-  **Щелкните маркер** -пользователь нажимает для маркера.

-  **Перетащите маркер** -long щелкнул пользователь на mparger

-  **Щелкните окно сведений о** -пользователь нажал на информационное окно.

Каждое из этих событий будут рассматриваться более подробно ниже.


### <a name="marker-click-events"></a>Маркер события щелчка мыши

`MarkerClicked` Событие возникает в том случае, когда пользователь касается для маркера. Это событие принимает `GoogleMap.MarkerClickEventArgs` объект в качестве параметра. Этот класс содержит два свойства:

-  `GoogleMap.MarkerClickEventArgs.Handled` &ndash; Это свойство должно быть присвоено `true` для указания, что обработчик событий потребляет события. Если задано значение `false` то поведение по умолчанию будет выполняться в дополнение к пользовательского поведения обработчика событий.

-  `Marker` &ndash; Это свойство является ссылкой на маркер, который вызвал `MarkerClick` событий.


В этом фрагменте кода показан пример `MarkerClick` это изменится положение камеры в новое расположение на карте:

```csharp
void MapOnMarkerClick(object sender, GoogleMap.MarkerClickEventArgs markerClickEventArgs)
{
    markerClickEventArgs.Handled = true;

    var marker = markerClickEventArgs.Marker;
    if (marker.Id.Equals(gotMauiMarkerId))
    {
        LatLng InMaui = new LatLng(20.72110, -156.44776);
    
        // Move the camera to look at Maui.
        PositionPolarBearGroundOverlay(InMaui);
        googleMap.AnimateCamera(CameraUpdateFactory.NewLatLngZoom(InMaui, 13));
        gotMauiMarkerId = null;
        polarBearMarker.Remove();
        polarBearMarker = null;
    }
    else
    {
        Toast.MakeText(this, $"You clicked on Marker ID {marker.Id}", ToastLength.Short).Show();
    }
}
```


### <a name="marker-drag-events"></a>События перетаскивания маркеров

Это событие возникает, когда пользователь хочет перетащите маркер. По умолчанию маркеры не перетаскиваемые. Маркер можно задать как перетаскиваемой, задав `Marker.Draggable` свойства `true` или вызвав `MarkerOptions.Draggable` метод с `true` как параметр.

Перетащите маркер, пользователь должен сначала долго, щелкните маркер и затем пальцев должны оставаться на карте. Если перетаскивать пальцем пользователя на экране перемещаются маркера. Когда пользователь поднимает палец на экране, маркер будет оставаться на месте.

Ниже перечислены различные события, которые будут созданы для перетаскиваемой маркера.

-   `GoogleMap.MarkerDragStart(object sender, GoogleMap.MarkerDragStartEventArgs e)` &ndash; Это событие возникает, когда пользователь перетаскивает сначала маркера.

-   `GoogleMap.MarkerDrag(object sender, GoogleMap.MarkerDragEventArgs e)` &ndash; Это событие возникает, так как маркер перетаскивается.

-   `GoogleMap.MarkerDragEnd(object sender, GoogleMap.MarkerDragEndEventArgs e)` &ndash; Это событие возникает, когда пользователь закончил Перетаскивание маркера.

Каждый из `EventArgs` содержит одиночное свойство с именем `P0` то есть ссылку на `Marker` объект, перетаскиваемый.


### <a name="info-window-click-events"></a>Щелкните окно событий Info

Одновременно может отображаться только одно окно info. Когда пользователь щелкает информационное окно в сопоставлении, объект-сопоставление будет вызывать `InfoWindowClick` событий. В следующем фрагменте кода показано, как подключить обработчик к событию:

```csharp
public void OnMapReady(GoogleMap map)
{
    map.InfoWindowClick += MapOnInfoWindowClick;
}

private void MapOnInfoWindowClick (object sender, GoogleMap.InfoWindowClickEventArgs e)
{
    Marker myMarker = e.Marker;
    // Do something with marker.
}
```

Помните, что в окне сведений является статический `View` которого отображается в виде изображения на карте. Все мини-приложения, такие как кнопки, флажки и представлениям текста, которые помещаются внутри информационное окно будет пассивным и не может отвечать на любой из их целочисленных пользовательские события.


## <a name="related-links"></a>Связанные ссылки

- [SimpleMapDemo](https://github.com/xamarin/monodroid-samples/tree/master/MapsAndLocationDemo_v3/SimpleMapDemo)
- [Службы Google Play](https://developers.google.com/android/guides/overview)
- [Google сопоставляет v2 Android API](https://developers.google.com/maps/documentation/android-sdk/intro)
- [Сервисы Google Play APK](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en)
- [Получение ключа Google Maps API](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [использует библиотеку](https://developer.android.com/guide/topics/manifest/uses-library-element)
- [использует функция](https://developer.android.com/guide/topics/manifest/uses-feature-element)
