---
title: Службы определения местоположения
description: В этом руководстве представлены сетевом расположении в приложения Android и показано, как получить с помощью API Android расположение службы, а также плавким расположение поставщика, доступного с API-интерфейса служб Google место расположения пользователя.
ms.prod: xamarin
ms.assetid: 0008682B-6CEF-0C1D-3200-56ECF58F5D3C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/22/2018
ms.openlocfilehash: b509f6892b27afa053a6ee913826d913d7ad54a8
ms.sourcegitcommit: 4f646dc5c51db975b2936169547d625c78a22b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/25/2018
ms.locfileid: "34546142"
---
# <a name="location-services"></a>Службы определения местоположения

_В этом руководстве представлены сетевом расположении в приложения Android и показано, как получить с помощью API Android расположение службы, а также плавким расположение поставщика, доступного с API-интерфейса служб Google место расположения пользователя._

## <a name="location-services-overview"></a>Общие сведения о службах расположения

Android предоставляет доступ к различным расположение технологий, таких как расположение башня ячейки, Wi-Fi и GPS. Сведения о каждой из технологий расположение абстрагированы через *поставщиков расположения*, позволяя приложениям получать расположения таким же образом, независимо от используемого в поставщика. В этом руководстве описаны поставщика плавким расположения, в состав службы Google Play, которая интеллектуально определяет лучший способ получения сведений о местоположении устройства, на основе доступны какие поставщики и использование устройства. Службы API Android расположение и показано, как взаимодействовать с расположением системы службы с использованием `LocationManager`. Вторая часть руководства более подробно рассматривается в Android API службы расположения с помощью `LocationManager`.
 
Как правило лучше использовать поставщика плавким расположения, возврата старых расположение службы API Android только при необходимости приложений.

## <a name="location-fundamentals"></a>Сведения о расположении

В Android не имеет значения, какие API для работы с данными расположения, некоторые понятия остаются неизменными. В этом разделе представлены поставщиков расположения и разрешения, связанные с расположение.

### <a name="location-providers"></a>Расположение поставщиков

Несколько технологий используются для указания местонахождения пользователя. Оборудование, используемое зависит от типа *поставщик расположения* выбранные задания сбора данных. Android использует три поставщиков расположения:

-   **Поставщик GPS** &ndash; GPS дает наиболее точную местонахождение наиболее электропитанием и лучше всего работает вне. Этот поставщик использует сочетание GPS и интерактивную GPS ([aGPS](http://en.wikipedia.org/wiki/Assisted_GPS)), возвращающий GPS данными, собранными towers сотовой связи.

-   **Сетевой поставщик** &ndash; предоставляет сочетание Wi-Fi и сотовой сети данных, включая aGPS данными, собранными towers ячейки. Он использует меньше энергии, чем поставщик GPS, но возвращает данные о местоположении различной точности.

-   **Пассивный поставщика** &ndash; «комбинированное» параметр с помощью поставщиков, запрашиваемые другие приложения или службы для создания расположения данных в приложении. Это является менее надежным, кроме параметр энергосбережения идеально подходит для приложений, для которых не требуется постоянное расположение обновлений для работы.

Расположение поставщики не всегда доступны. Например может потребоваться использовать GPS для нашего приложения, но GPS может быть отключен в параметрах или устройство может вообще не имеют GPS. Если конкретного поставщика недоступен, выбрав этот поставщик может возвращать `null`.

### <a name="location-permissions"></a>Разрешения расположения

Привязанный к местонахождению приложению доступ к датчики оборудования устройства для получения GPS, Wi-Fi и передачи данных. Управление доступом осуществляется через соответствующие разрешения в Android манифеста приложения.
Существует два разрешения &ndash; в зависимости от требований приложения и собственный API, необходимо разрешить одному:

-   `ACCESS_FINE_LOCATION` &ndash; Позволяет приложению доступ к GPS.
    Требуется для *GPS поставщика* и *пассивный поставщика* параметры (*пассивный поставщика необходимо предоставить разрешение на доступ к данным GPS собранные другим приложением или службой*). Дополнительное разрешение для *поставщика сетевых*.

-   `ACCESS_COARSE_LOCATION` &ndash; Позволяет приложению доступ к расположение сотовой и Wi-Fi. Требуется для *поставщика сетевых* Если `ACCESS_FINE_LOCATION` не задано.

Для приложений, предназначенных для API версии 21 (Android 5.0 без описания операций) или более поздней версии, можно включить `ACCESS_FINE_LOCATION` и по-прежнему выполняться на устройствах, не имеющих оборудования GPS. Если вашему приложению требуется GPS оборудования, следует явным образом добавить `android.hardware.location.gps` `uses-feature` Android манифест элемента. Дополнительные сведения см. в разделе Android [использует компонент](https://developer.android.com/guide/topics/manifest/uses-feature-element.html) ссылка на элемент.

Чтобы задать разрешения, разверните **свойства** папки в **Pad решения** и дважды щелкните **AndroidManifest.xml**. Разрешения будут перечислены на вкладке **требуемые разрешения**:

[![Снимок экрана с параметрами Android манифеста необходимые разрешения](location-images/location-01-xs.png)](location-images/location-01-xs.png#lightbox)

Если задать одно из этих разрешений сообщает Android вашему приложению разрешения пользователя для доступа к поставщикам расположение. Устройства, запустите уровень API 22 (Android 5.1) или ниже, будет запрашивать у пользователя для предоставления этих разрешений, каждый раз, когда приложение установлено. На устройствах под управлением API уровня 23 (Android 6.0) или более поздней версии, приложение должен выполнять проверку на разрешение во время выполнения перед запросом поставщика расположения. 

> [!NOTE]
>Примечание: Параметр `ACCESS_FINE_LOCATION` подразумевает доступ к данным как расположение крупных и нормально. Никогда не нужно установить оба разрешения, только *минимальные* разрешения, требуемые приложению для работы.

В этом фрагменте приведен пример того, как проверить, что приложение имеет разрешение для `ACCESS_FINE_LOCATION` разрешение:

```csharp
 if (ContextCompat.CheckSelfPermission(this, Manifest.Permission.AccessFineLocation) == Permission.Granted)
{
    StartRequestingLocationUpdates();
    isRequestingLocationUpdates = true;
}
else
{
    // The app does not have permission ACCESS_FINE_LOCATION 
}
```

Приложения должен быть нечувствительного к ошибкам сценария, где пользователь не будет предоставлять разрешение (или отозвал разрешения) и иметь способ решения этой проблемы надлежащим образом. См. в разделе [руководство разрешения](~/android/app-fundamentals/permissions.md) Дополнительные сведения о реализации разрешение во время выполнения проверки в Xamarin.Android.


## <a name="using-the-fused-location-provider"></a>С помощью поставщика плавким расположения

Поставщик плавким расположение является предпочтительным для приложений Android для получения обновлений расположения с устройства, так как он эффективно отбирает поставщика расположения во время выполнения для обеспечения наиболее сведения о расположении батареи эффективным образом. Например пользователь проходу вокруг помещении возвращает лучшее расположение считывания с GPS. Если пользователь затем обходит здании, где GPS работает неправильно (если вообще), поставщик плавким расположения может автоматически переключается Wi-Fi, в котором работает лучше здании.
 
API-Интерфейс поставщика плавким расположение предоставляет широкий набор средств для расширения возможностей привязанный к местонахождению приложений, включая зонирование и мониторинг активности. В этом разделе мы собираемся фокус по основам настройки `LocationClient`, создав поставщиков и получение расположения пользователя.

Поставщик плавким расположение является частью [службы Google Play](http://developer.android.com/google/play-services/index.html).
Пакет службы Google Play должно быть установлено и настроено в API-Интерфейс поставщика плавким расположение для работы приложения и устройство должно иметь Google воспроизвести служб APK установлен.

Прежде чем Xamarin.Android приложение может использовать поставщик плавким расположения, его необходимо добавить **Xamarin.GooglePlayServices.Maps** пакета в проект. Кроме того, следующие `using` инструкции должны быть добавлены в любой исходные файлы, которые ссылаются на классы, описанные ниже:

```csharp
using Android.Gms.Common;
using Android.Gms.Location;
```

### <a name="checking-if-google-play-services-is-installed"></a>Проверка, установлены ли службы Google Play

Одной из Xamarin.Android будет аварийного завершения, при попытке использовать поставщик плавким расположения при установке службы Google Play (или истек срок), а затем исключение времени выполнения может произойти.  Если службы Google Play не установлен, затем приложение следует переключиться на Android службы расположения, описанных выше. Если истек срок службы Google Play, приложение может отображать сообщение для пользователя, запрашиваются для обновления установленной версии службы Google Play.

Этот фрагмент является примером как Android действия можно программным образом проверить установленные службы Google Play:

```csharp
bool IsGooglePlayServicesInstalled()
{
    var queryResult = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(this);
    if (queryResult == ConnectionResult.Success)
    {
        Log.Info("MainActivity", "Google Play Services is installed on this device.");
        return true;
    }

    if (GoogleApiAvailability.Instance.IsUserResolvableError(queryResult))
    {
        // Check if there is a way the user can resolve the issue
        var errorString = GoogleApiAvailability.Instance.GetErrorString(queryResult);
        Log.Error("MainActivity", "There is a problem with Google Play Services on this device: {0} - {1}",
                  queryResult, errorString);
                  
        // Alternately, display the error to the user.
    }

    return false;
}
```

### <a name="fusedlocationproviderclient"></a>FusedLocationProviderClient

Для взаимодействия с поставщиком плавким расположения приложения Xamarin.Android необходимо иметь установленный экземпляр `FusedLocationProviderClient`. Этот класс предоставляет необходимые методы, чтобы подписаться на обновления расположения и получения последнем известном расположении устройства.

`OnCreate` Метод действия является подходящее место для получения ссылки на `FusedLocationProviderClient`, как показано в следующем фрагменте кода:

```csharp
public class MainActivity: AppCompatActivity
{
    FusedLocationProviderClient fusedLocationProviderClient;

    protected override void OnCreate(Bundle bundle) 
    {
        fusedLocationProviderClient = LocationServices.GetFusedLocationProviderClient(this);
    }
}
```

### <a name="getting-the-last-known-location"></a>Получение последнем известном расположении

`FusedLocationProviderClient.GetLastLocationAsync()` Метод предоставляет способ для Xamarin.Android приложения, чтобы быстро получить последнем известном расположении устройства с минимального программирования издержки простой, без блокировки.

В этом фрагменте показано использование `GetLastLocationAsync` метода для получения расположения устройства:

```csharp
async Task GetLastLocationFromDevice()
{
    // This method assumes that the necessary run-time permission checks have succeeded.
    getLastLocationButton.SetText(Resource.String.getting_last_location);
    Android.Locations.Location location = await fusedLocationProviderClient.GetLastLocationAsync();

    if (location == null)
    {
        // Seldom happens, but should code that handles this scenario
    }
    else
    {
        // Do something with the location 
        Log.Debug("Sample", "The latitude is " + location.Latitude);
    }
}
```

### <a name="subscribing-to-location-updates"></a>Подписка на расположение обновлений

Приложение Xamarin.Android можно также подписаться на обновления расположения от поставщика плавким расположения с помощью `FusedLocationProviderClient.RequestLocationUpdatesAsync` метода, как показано в этом фрагменте кода:

```csharp
await fusedLocationProviderClient.RequestLocationUpdatesAsync(locationRequest, locationCallback);
```
Этот метод принимает два параметра:

-   **`Android.Gms.Location.LocationRequest`** &ndash; Объект `LocationRequest` объект является, как приложение Xamarin.Android передает параметры о работе поставщика плавким расположения. `LocationRequest` Содержит сведения, такие как частые запросы должны вноситься или важность точное расположение обновления должно быть. Например запрос важным местом приведет к устройству использовать GPS и поэтому больше возможностей при определении положения. Этот фрагмент кода демонстрируется создание `LocationRequest` для расположения с высокой точностью, проверка примерно каждые пять минуты для расположения обновления (но не может быть выполнено до двух минут между запросами). Поставщик плавким расположение будет использовать `LocationRequest` как рекомендации для используемого расположение поставщика при попытке определить местоположение устройства:

    ```csharp
    LocationRequest locationRequest = new LocationRequest()
                                      .SetPriority(LocationRequest.PriorityHighAccuracy)
                                      .SetInterval(60 * 1000 * 5)
                                      .SetFastestInterval(60 * 1000 * 2);
    ```
                                          

-   **`Android.Gms.Location.LocationCallback`** &ndash; Для получения обновлений расположения, приложение Xamarin.Android необходимо подкласс `LocationProvider` абстрактного класса. Этот класс предоставляется два метода, которым может быть вызван поставщиком плавким расположение обновление приложения с сведения о расположении. Это будет иметь более подробно рассмотрены ниже.

Чтобы уведомить приложение Xamarin.Android обновления в расположение, будет вызывать поставщик плавким расположения `LocationCallBack.OnLocationResult(LocationResult result)`. `Android.Gms.Location.LocationResult` Параметр будет содержать сведения о расположении обновления.

Когда поставщик плавким расположения обнаруживает изменение в доступности данных расположения, он вызывает `LocationProvider.OnLocationAvaibility(LocationAvailability
locationAvailability)` метод. Если `LocationAvailability.IsLocationAvailable` возвращает `true`, то можно предположить, сообщаемые результаты расположение устройства `OnLocationResult` как точность и как в актуальном состоянии, необходимые `LocationRequest`. Если `IsLocationAvailable` имеет значение false, результаты не расположение будет вернуть `OnLocationResult`.

Этот фрагмент кода входит пример реализации `LocationCallback` объекта:

```csharp
public class FusedLocationProviderCallback : LocationCallback
{
    readonly MainActivity activity;

    public FusedLocationProviderCallback(MainActivity activity)
    {
        this.activity = activity;
    }

    public override void OnLocationAvailability(LocationAvailability locationAvailability)
    {
        Log.Debug("FusedLocationProviderSample", "IsLocationAvailable: {0}",locationAvailability.IsLocationAvailable);
    }

    public override void OnLocationResult(LocationResult result)
    {
        if (result.Locations.Any())
        {
            var location = result.Locations.First();
            Log.Debug("Sample", "The latitude is :" + location.Latitude);
        }
        else
        {
            // No locations to work with.
        }
    }
}
```

## <a name="using-the-android-location-service-api"></a>С помощью API Android расположение службы

Android расположение службы — это более старые API с помощью сведений о расположении на Android. Данные о местоположении собираются с датчиками оборудования и собранные системной службой, к которому можно получить в приложении с `LocationManager` класса и `ILocationListener`.

Службы расположения наилучшим образом подходит для приложений, которые необходимо выполнить на устройствах, не установлены службы Google Play.

Службы расположения — это специальный тип [службы](http://developer.android.com/guide/components/services.html) управляемых системой. Системная служба взаимодействует с устройства и всегда запущена. Вниманию обновления расположения в приложении, мы подпишется на расположение обновлений из службы расположения системы с помощью `LocationManager` и `RequestLocationUpdates` вызова.

Для получения расположения пользователя с помощью Android расположения службы включает несколько этапов:

1.  Получить ссылку на `LocationManager` службы.
2.  Реализуйте `ILocationListener` интерфейс и дескриптор события при изменении расположения.
3.  Используйте `LocationManager` обновления расположения запрос для указанного поставщика. `ILocationListener` Из предыдущего шага будет использоваться для получения обратных вызовов от `LocationManager`.
4.  Остановитесь расположение обновлений для приложения это больше не подходит для получения обновлений.

### <a name="location-manager"></a>Расположение диспетчера

Можно обратиться к службе расположение system с помощью экземпляра `LocationManager` класса. `LocationManager` является особым классом, позволяют взаимодействовать с расположением системы службы и вызывать методы на нем. Приложение может получить ссылку на `LocationManager` путем вызова `GetSystemService` и передачи в тип службы, как показано ниже:

```csharp
LocationManager locationManager = (LocationManager) GetSystemService(Context.LocationService);
```

`OnCreate` Поместите здесь для получения ссылки на `LocationManager`.
Рекомендуется сохранять `LocationManager` как переменная класса таким образом, можно вызывать в разных точках в жизненный цикл действия.

### <a name="request-location-updates-from-the-locationmanager"></a>Запрос обновления расположения из LocationManager

Если приложение имеет ссылку на `LocationManager`, он должен сообщить `LocationManager` какого рода сведения о расположении, которые необходимы, и как часто эти сведения будут обновляться. Это выполняется вызовом метода `RequestionLocationUpdates` на `LocationManager` объекта и передав некоторые условия для обновлений и обратный вызов, который будет получать обновления расположения. Этот обратный вызов — это тип, который должен быть реализован `ILocationListener` интерфейса (более подробно далее в этом руководстве).

`RequestionLocationUpdates` Метод указывает место в системе службы приложения хотите начать получать обновления на месте. Этот метод позволяет указать поставщика, а также время и расстояния пороговые значения для управления частоту обновления. Например, метод ниже вложенных запросов папку обновляет от поставщика расположения GPS каждые 2 000 миллисекунд, и только в том случае, когда расположение изменяется более чем 1 метр:

```csharp
// For this example, this method is part of a class that implements ILocationListener, described below
locationManager.RequestLocationUpdates(LocationManager.GpsProvider, 2000, 1, this);
```

Приложение должно запросить обновления расположения только так часто, как требуется для приложения для выполнения также. Это позволяет сохранить времени автономной работы и создает для повышения удобства для пользователя.

### <a name="responding-to-updates-from-the-locationmanager"></a>Отвечать на запросы к обновлениям LocationManager

Когда приложение запрашивает обновления из `LocationManager`, он может получать сведения из службы путем реализации [ `ILocationListener` ](https://developer.xamarin.com/api/type/Android.Locations.ILocationListener/) интерфейса. Этот интерфейс предоставляет четыре метода для прослушивания расположение службы и поставщика расположения `OnLocationChanged`. Система будет вызывать `OnLocationChanged` при изменении расположения пользователя достаточно, чтобы считаться изменение расположения согласно критериям, заданным при запросе обновления расположения. 

В следующем коде показано методы в `ILocationListener` интерфейс:

```csharp
public class MainActivity : AppCompatActivity, ILocationListener
{
    TextView latitude;
    TextView longitude;
    
    public void OnLocationChanged (Location location)
    {
        // called when the location has been updated.
    }
    
    public OnProviderDisabled(string locationProvider)
    {
        // called when the user disables the provider
    }
    
    public OnProviderEnabled(string locationProvider)
    {
        // called when the user enables the provider
    }
    
    public OnStatusChanged(string locationProvider, Availability status, Bundle extras)
    {
        // called when the status of the provider changes (there are a variety of reasons for this)
    }
}
```

### <a name="unsubscribing-to-locationmanager-updates"></a>Отмена подписки на LocationManager обновлений

Чтобы сэкономить системные ресурсы, приложение должно отменить подписку на расположение обновлений как можно быстрее. `RemoveUpdates` Указывает метод `LocationManager` для прекращения отправки обновлений приложения.  Например, действие может вызвать `RemoveUpdates` в `OnPause` метод, чтобы мы могли для экономии питания, если приложение не требует обновления расположения во время его действие не находится на экране:

```csharp
protected override void OnPause ()
{
    base.OnPause ();
    locationManager.RemoveUpdates (this);
}
```

Если приложению необходимо получить расположение обновлений в фоновом режиме, нужно создать пользовательскую службу, подписывается на системной расположение службы. Ссылаться на [Backgrounding Android службами](~/android/app-fundamentals/services/index.md) для дополнительных сведений.

### <a name="determining-the-best-location-provider-for-the-locationmanager"></a>Определение расположения услуг LocationManager

Выше приложение задает GPS как поставщик расположения. Тем не менее GPS может оказаться доступен во всех случаях, например если здании или устройства не имеет приемника GPS. Если это так, результат будет `null` возврата для поставщика.

Чтобы получить приложение для работы при GPS недоступен, используется `GetBestProvider` метод запрашивать услуг доступным местам (поддерживаемые устройства и пользователя включен) при запуске приложения. Вместо передачи в конкретного поставщика, чтобы узнать `GetBestProvider` требования для службы — например, точность и power - с [ `Criteria` объекта](https://developer.xamarin.com/api/type/Android.Locations.Criteria/). `GetBestProvider` Возвращает поставщик рекомендации по заданным критериям.

Следующий код показано, как получить лучше всего поставщика и использовать его при запросе обновления расположения:

```csharp
Criteria locationCriteria = new Criteria();   
locationCriteria.Accuracy = Accuracy.Coarse;
locationCriteria.PowerRequirement = Power.Medium;

locationProvider = locationManager.GetBestProvider(locationCriteria, true);

if(locationProvider != null)
{
    locationManager.RequestLocationUpdates (locationProvider, 2000, 1, this);
}
else
{
    Log.Info(tag, "No location providers available");
}
```

> [!NOTE]
>  Если пользователь отключает все поставщики расположение `GetBestProvider` вернет `null`. Чтобы увидеть, как работает этот код на физическом устройстве, не забудьте включить GPS, Wi-Fi и сотовые сети под **параметров Google > расположение > режим** как показано на этом снимке экрана:

[![Параметры режима расположения экрана на телефоне с Android](location-images/location-02.png)](location-images/location-02.png#lightbox)

На следующем снимке экрана показано расположение приложения запущенным с помощью `GetBestProvider`:

[![Приложение GetBestProvider отображения широты, долготы и поставщик](location-images/location-03.png)](location-images/location-03.png#lightbox)

Имейте в виду, что `GetBestProvider` динамически не изменять службу. Однако он определяет, лучше всего поставщика один раз в течение жизненного цикла действия. При изменении состояния поставщик после его задания, требуется дополнительный код в приложение `ILocationListener` методы &ndash; `OnProviderEnabled`, `OnProviderDisabled`, и `OnStatusChanged` &ndash; обрабатывать все возможности, связанные с ключ поставщика.

## <a name="summary"></a>Сводка

В этом руководстве рассматривается получение адреса пользователя, с помощью Android расположение службы и поставщик плавким расположения из API-интерфейса Google расположение службы.


## <a name="related-links"></a>Связанные ссылки

- [Расположение (пример)](https://developer.xamarin.com/samples/Location/)
- [FusedLocationProvider (пример)](https://developer.xamarin.com/samples/FusedLocationProvider/)
- [Службы Google Play](http://developer.android.com/google/play-services/index.html)
- [Класс критериев](https://developer.xamarin.com/api/type/Android.Locations.Criteria/)
- [Класс LocationManager](https://developer.xamarin.com/api/type/Android.Locations.LocationManager/)
- [Класс LocationListener](https://developer.xamarin.com/api/type/Android.Locations.ILocationListener/)
- [LocationClient API](http://developer.android.com/reference/com/google/android/gms/location/LocationClient.html)
- [LocationListener API](http://developer.android.com/reference/com/google/android/gms/location/LocationListener.html)
- [LocationRequest API](https://developer.android.com/reference/com/google/android/gms/location/LocationRequest.html)
