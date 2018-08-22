---
title: Службы определения местоположения
description: В этом руководстве представлены сетевом расположении в приложениях Android и показано, как получить из местоположения пользователя с помощью API Android расположение службы, а также доступные поставщика склеенную расположения с помощью API служб расположения Google.
ms.prod: xamarin
ms.assetid: 0008682B-6CEF-0C1D-3200-56ECF58F5D3C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/22/2018
ms.openlocfilehash: 2cd49441ec9ba15cd7fd2647fa74b39b32ea8acd
ms.sourcegitcommit: e64c3c10d6a36b3b031d6d4dbff7af74ab2b7f21
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2018
ms.locfileid: "40251273"
---
# <a name="location-services"></a>Службы определения местоположения

_В этом руководстве представлены сетевом расположении в приложениях Android и показано, как получить из местоположения пользователя с помощью API Android расположение службы, а также доступные поставщика склеенную расположения с помощью API служб расположения Google._

## <a name="location-services-overview"></a>Общие сведения о службах расположения

Android предоставляет доступ к различным расположение технологий, таких как расположение tower ячейки, Wi-Fi и GPS. Сведения о каждой из технологий расположение абстрагируются через *поставщики услуг размещения*, что позволяет приложениям получать расположения таким же образом, независимо от того, поставщик, который используется. Это руководство описывает поставщика склеенную расположения, часть службы Google Play, который интеллектуально определяет лучший способ получить сведения о расположении устройств, на основе доступны какие поставщики и использование устройства. API службы Android расположение и показано, как взаимодействовать с расположением системы службы с помощью `LocationManager`. Во второй части руководства описано, как использовать API службы Android расположение `LocationManager`.
 
Как правило приложения должны отдавать предпочтение использованию поставщика склеенную расположения, откат старых расположение службы API Android только при необходимости.

## <a name="location-fundamentals"></a>Основные сведения о расположении

В Android, независимо от того, какие API для работы с данными расположения некоторые понятия остаются неизменными. В этом разделе представлены поставщики услуг размещения и разрешения, связанные с местоположением.

### <a name="location-providers"></a>Поставщики услуг размещения

Несколько технологий используются внутренне для точного определения расположения пользователя. Оборудование, используемое зависит от типа *поставщик расположения* выбранных для задания сбора данных. Android использует три поставщики услуг размещения:

-   **Поставщик GPS** &ndash; GPS дает наиболее точную местонахождение, использует наибольшими возможностями и работает лучше всего лета. Этот поставщик использует сочетание GPS и службе технической GPS ([aGPS](http://en.wikipedia.org/wiki/Assisted_GPS)), возвращающий данные GPS, собранные towers сотовой связи.

-   **Сетевой поставщик** &ndash; предоставляет сочетание Wi-Fi и сотовой сети данные, включая aGPS данные, собранные towers ячейки. Он использует меньше энергии, чем поставщик GPS, но возвращает данные о местоположении различной точности.

-   **Пассивный поставщика** &ndash; «оказией» параметр, с помощью поставщиков, запрашиваемые другими приложениями или службами для создания данных расположения в приложении. Это является менее надежным, кроме параметр энергосбережения идеально подходит для приложений, которые не требуют обновления постоянное расположение для работы.

Поставщики услуг размещения не всегда доступны. Например мы может потребоваться использовать GPS для нашего приложения, но GPS может быть отключен в параметрах или устройство может вообще не иметь GPS. Если недоступен конкретного поставщика, при выборе этого поставщика может вернуть `null`.

### <a name="location-permissions"></a>Разрешения расположения

Привязанный к местонахождению приложению доступ к датчики оборудования устройства для получения GPS, Wi-Fi и передачи данных. Управление доступом осуществляется через соответствующие разрешения в манифест приложения Android.
Существуют два разрешения &ndash; в зависимости от требований вашего приложения и выбор API, необходимо разрешить одному:

-   `ACCESS_FINE_LOCATION` &ndash; Позволяет приложению доступ к GPS.
    Требуется для *GPS поставщика* и *пассивный поставщика* параметры (*пассивный поставщика необходимо предоставить разрешение на доступ к данным GPS, собранные другим приложением или службой*). Дополнительное разрешение для *поставщика сетевых*.

-   `ACCESS_COARSE_LOCATION` &ndash; Позволяет приложению доступ к расположению сотовой и Wi-Fi. Требуется для *поставщика сетевых* Если `ACCESS_FINE_LOCATION` не задано.

Для приложений, предназначенных для API версии 21 (Android 5.0 Lollipop) или более поздней версии, вы можете включить `ACCESS_FINE_LOCATION` и по-прежнему выполняться на устройствах, у которых нет оборудования GPS. Если вашему приложению требуется оборудования GPS, следует явным образом добавить `android.hardware.location.gps` `uses-feature` элемента в манифесте Android. Дополнительные сведения см. в разделе Android [использует функцию](https://developer.android.com/guide/topics/manifest/uses-feature-element.html) ссылка на элемент.

Чтобы задать разрешения, разверните **свойства** папку в **панели решения** и дважды щелкните **AndroidManifest.xml**. Разрешения будут перечислены в разделе **необходимые разрешения**:

[![Снимок экрана: параметры Android Manifest необходимые разрешения](location-images/location-01-xs.png)](location-images/location-01-xs.png#lightbox)

При настройке любого из этих разрешений сообщает Android, что приложению требуется разрешение от пользователя, чтобы получить доступ к поставщикам расположение. Устройства, запустите уровень API 22 (Android 5.1) или ниже, будет запрашивать у пользователя, чтобы предоставить эти разрешения каждый раз, когда приложение установлено. На устройствах под управлением API уровня 23 (Android 6.0) или более поздней версии, приложение необходимо выполнить проверку разрешений во время выполнения перед запросом поставщика расположения. 

> [!NOTE]
>Примечание: Параметр `ACCESS_FINE_LOCATION` подразумевает доступ к данным как грубое и нормально расположение. Не следует необходимо задать оба разрешения, только *минимальным* разрешения, которые необходимы приложению для работы.

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

Приложения должно быть нечувствительного к ошибкам сценария, где пользователь не будет предоставлять разрешение (или отозвал разрешения) и иметь возможность корректно решения этой проблемы. См. в разделе [руководстве по разрешениям](~/android/app-fundamentals/permissions.md) Дополнительные сведения о реализации разрешений времени выполнения проверяет в Xamarin.Android.


## <a name="using-the-fused-location-provider"></a>С помощью поставщика склеенную расположения

Поставщик расположения склеенную является более предпочтительным для приложений Android для получения обновлений расположения с устройства, так как он будет эффективно выбирать поставщика расположения во время выполнения для предоставления рекомендации сведения о расположении батареи эффективным образом. Например пользователь прогулке по Туризм получает наилучшее расположение считывания с GPS. Если пользователь подробно здании, где GPS работает плохо (если вообще), поставщик склеенную расположения может автоматически переключиться на Wi-Fi, который работает лучше, улице.
 
API поставщика склеенную расположение предоставляет широкий набор средств для расширения возможностей привязанный к местонахождению приложений, включая географическое зонирование и мониторинг активности. В этом разделе мы собираемся сосредоточиться на основные сведения о настройке `LocationClient`, создав поставщиков и получение из местоположения пользователя.

Поставщик расположения склеенную является частью [сервисы Google Play](http://developer.android.com/google/play-services/index.html).
Сервисы Google Play пакет должен быть установлен и настроен должным образом в приложении для поставщика склеенную расположения API для работы, и на устройстве должен быть Google Play Services APK установлен.

Прежде чем Xamarin.Android приложение может использовать поставщик склеенную расположения, он должен добавить **Xamarin.GooglePlayServices.Maps** пакет в проект. Кроме того, следующие `using` инструкции должны добавляться к все файлы исходного кода, которые ссылаются на классы, описанные ниже:

```csharp
using Android.Gms.Common;
using Android.Gms.Location;
```

### <a name="checking-if-google-play-services-is-installed"></a>Проверка наличия служб Google Play

Возникала будет Xamarin.Android, аварийном завершении, при попытке использовать поставщик склеенную расположения, если сервисы Google Play не установлен (или устарели), а затем исключение времени выполнения.  Если сервисы Google Play не установлен, затем приложение должно возвращаться к Android службы расположения, описанных выше. Если сервисы Google Play является устаревшим, приложение может отобразить сообщение для пользователя, задавая им обновить установленную версию служб Google Play.

Этот фрагмент кода является примером как действие Android позволяет программно проверять, установлен ли сервисы Google Play:

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

Для взаимодействия с поставщиком склеенную расположения, приложения Xamarin.Android должны иметь экземпляр `FusedLocationProviderClient`. Этот класс предоставляет необходимые методы подписаться на обновления расположения и для получения расположения указывается последние известные устройства.

`OnCreate` Метод действия является подходящее место для получения ссылки `FusedLocationProviderClient`, как показано в следующем фрагменте кода:

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

### <a name="getting-the-last-known-location"></a>Получение последнего известного расположения

`FusedLocationProviderClient.GetLastLocationAsync()` Метод предоставляет способ простой и без блокировки для приложения Xamarin.Android, чтобы быстро получить последнем известном расположении устройства с минимальным изменением кода издержки.

В этом фрагменте показано, как использовать `GetLastLocationAsync` метод для получения расположения указывается устройства:

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

### <a name="subscribing-to-location-updates"></a>Подписка на расположение обновления

Приложение Xamarin.Android можно также подписаться на обновления расположения от поставщика склеенную расположения с помощью `FusedLocationProviderClient.RequestLocationUpdatesAsync` метод, как показано в этом фрагменте кода:

```csharp
await fusedLocationProviderClient.RequestLocationUpdatesAsync(locationRequest, locationCallback);
```
Этот метод принимает два параметра:

-   **`Android.Gms.Location.LocationRequest`** &ndash; Объект `LocationRequest` объект является, как приложения Xamarin.Android передает параметры о работе поставщика склеенную расположения. `LocationRequest` Содержит сведения, такие как частые запросы должны выполняться или важность точное расположение обновления должно быть. Например запрос важным местом вызовет использовать GPS и следовательно больше возможностей при определении расположения на устройстве. В этом фрагменте кода показано, как создать `LocationRequest` для местоположения с высокой точностью, проверка приблизительно каждые пять минуты в течение расположение обновления (но не раньше, чем двух минут между запросами). Поставщик склеенную расположение будет использовать `LocationRequest` как рекомендации, для которого поставщик расположения для использования при попытке определить расположение устройства:

    ```csharp
    LocationRequest locationRequest = new LocationRequest()
                                      .SetPriority(LocationRequest.PriorityHighAccuracy)
                                      .SetInterval(60 * 1000 * 5)
                                      .SetFastestInterval(60 * 1000 * 2);
    ```
                                          

-   **`Android.Gms.Location.LocationCallback`** &ndash; Для получения обновлений расположения, приложения Xamarin.Android необходимо подкласс `LocationProvider` абстрактного класса. Этот класс предоставляется два метода, которые может быть вызвана поставщиком склеенную расположение обновление приложения, используя сведения о расположении. Это будет рассматриваться более подробно ниже.

Чтобы уведомить приложение Xamarin.Android расположение обновления, будет вызывать поставщик склеенную расположения `LocationCallBack.OnLocationResult(LocationResult result)`. `Android.Gms.Location.LocationResult` Параметр будет содержать сведения о расположении обновления.

Когда поставщик склеенную расположения обнаруживает изменение в доступности данных расположения, он вызывает `LocationProvider.OnLocationAvaibility(LocationAvailability
locationAvailability)` метод. Если `LocationAvailability.IsLocationAvailable` возвращает `true`, то можно предположить, что полученные расположения устройства сообщаемое `OnLocationResult` являются точным и чтобы обновлять согласно требованиям `LocationRequest`. Если `IsLocationAvailable` имеет значение false, то результаты не расположение будет вернуть `OnLocationResult`.

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

## <a name="using-the-android-location-service-api"></a>С помощью API службы Android расположение

Служба Android расположение — старый API за использование сведений о расположении в Android. Данные о местоположении собираются с датчики оборудования и собранные системной службой, к которому можно получить в приложении с помощью `LocationManager` класс и `ILocationListener`.

Расположение службы лучше всего подходит для приложений, которые необходимо выполнить на устройствах, у которых не установлены службы Google Play.

Служба расположений — это специальный тип [службы](http://developer.android.com/guide/components/services.html) управляет система. Системная служба взаимодействует с устройства и всегда работает. Открытий расположение обновлений в наше приложение, мы, чтобы подписаться на расположение обновлений из службы расположения системы с помощью `LocationManager` и `RequestLocationUpdates` вызова.

Для получения из местоположения пользователя с помощью Android расположение службы включает несколько этапов:

1.  Получить ссылку на `LocationManager` службы.
2.  Реализуйте `ILocationListener` интерфейс и дескриптор события при изменении расположения.
3.  Используйте `LocationManager` для обновления расположение запроса для указанного поставщика. `ILocationListener` Из предыдущего шага будет использоваться для получения обратных вызовов из `LocationManager`.
4.  Остановите расположение обновления, когда приложение стоит больше не получать обновления.

### <a name="location-manager"></a>Диспетчером местоположения

Можно обратиться к службе расположение системы с помощью экземпляра `LocationManager` класса. `LocationManager` — Это особый класс, который позволяет взаимодействовать с расположением системы службы и вызвать методы на нем. Приложение может получить ссылку на `LocationManager` путем вызова `GetSystemService` и передавая тип службы, как показано ниже:

```csharp
LocationManager locationManager = (LocationManager) GetSystemService(Context.LocationService);
```

`OnCreate` хорошо подходит для получения ссылки на `LocationManager`.
Рекомендуется сохранить `LocationManager` как переменную класса таким образом, мы можем вызвать в различных точках жизненный цикл действия.

### <a name="request-location-updates-from-the-locationmanager"></a>Расположение обновления запроса из LocationManager

Как только приложение получает ссылку на `LocationManager`, она должна сообщить об `LocationManager` какого рода сведения о расположении, являются обязательными, и как часто эти сведения будут обновляться. Это делается путем вызова `RequestionLocationUpdates` на `LocationManager` объекта и передачи Далее указаны некоторые критерии для обновления и обратный вызов, который будет получать обновления расположения. Этот обратный вызов — это тип, который должен быть реализован `ILocationListener` интерфейс (более подробно далее в этом руководстве).

`RequestionLocationUpdates` Метод указывает место в системе службы, что хотите начать получать обновления расположения приложения. Этот метод позволяет указать поставщика, а также пороговые значения времени и расстояние для управления частотой обновления. Например, указанный ниже метод ниже расположение запросы обновления от поставщика расположения GPS каждые 2000 миллисекунд, и только в том случае, когда расположение изменяется более чем 1 метр:

```csharp
// For this example, this method is part of a class that implements ILocationListener, described below
locationManager.RequestLocationUpdates(LocationManager.GpsProvider, 2000, 1, this);
```

Приложение должно запросить расположение обновлений только так часто, как это требуется для приложения лучше работает. Эта команда сохраняет батареи и создает большее удобство работы пользователя.

### <a name="responding-to-updates-from-the-locationmanager"></a>Реагирование на обновления из LocationManager

Когда приложение запрашивает обновления из `LocationManager`, он может получать сведения из службы путем реализации [ `ILocationListener` ](https://developer.xamarin.com/api/type/Android.Locations.ILocationListener/) интерфейс. Этот интерфейс предоставляет четыре метода для прослушивания расположение службы и поставщика расположения `OnLocationChanged`. Система будет вызывать `OnLocationChanged` при изменении местоположения пользователя достаточно, чтобы считаться изменения расположения в соответствии с критериям, заданным при запросе обновления расположения. 

В следующем коде показано методы `ILocationListener` интерфейса:

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

Чтобы сэкономить системные ресурсы, приложения следует отменить подписку на обновления расположение как можно скорее. `RemoveUpdates` Метод указывает `LocationManager` прекращение отправки обновлений в наше приложение.  Например, действие может вызвать `RemoveUpdates` в `OnPause` метод, чтобы мы могли для сохранения питания, если приложения не требуются расположение обновления, хотя его действие не находится на экране:

```csharp
protected override void OnPause ()
{
    base.OnPause ();
    locationManager.RemoveUpdates (this);
}
```

Если приложению для получения обновлений расположения в фоновом режиме, необходимо создать пользовательскую службу, подписывается на системной службы расположения. Ссылаться на [фоновой обработки с помощью службы Android](~/android/app-fundamentals/services/index.md) для дополнительных сведений.

### <a name="determining-the-best-location-provider-for-the-locationmanager"></a>Определение расположения услуг LocationManager

Выше приложение задает GPS в качестве поставщика расположения. Тем не менее GPS может оказаться доступны во всех случаях, например, если устройство улице или не имеет GPS-приемника. Если это так, возвращается `null` возвращаемого значения для поставщика.

Чтобы получить приложение для работы с GPS недоступна, используйте `GetBestProvider` метод, чтобы запросить рекомендации поставщика доступные расположения (поддерживаемые устройства и пользователя с поддержкой) при запуске приложения. Вместо передачи в конкретного поставщика, чтобы узнать `GetBestProvider` требования для службы — например, точность и power - с [ `Criteria` объект](https://developer.xamarin.com/api/type/Android.Locations.Criteria/). `GetBestProvider` Возвращает поставщик рекомендации по заданным критериям.

Ниже показано, как получить наилучшим образом поставщик, и использовать его при запросе обновления расположения:

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
>  Если пользователь отключил все поставщики услуг размещения, `GetBestProvider` вернет `null`. Чтобы увидеть, как этот код работает на реальном устройстве, не забудьте включить GPS, Wi-Fi и сети мобильной связи в разделе **параметров Google > расположение > режим** как показано на следующем снимке экрана:

[![Параметры режима расположения экрана на телефоне с Android](location-images/location-02.png)](location-images/location-02.png#lightbox)

На следующем снимке экрана показано расположение приложения запущенного с помощью `GetBestProvider`:

[![Приложение GetBestProvider отображения широты, долготы и поставщика](location-images/location-03.png)](location-images/location-03.png#lightbox)

Имейте в виду, что `GetBestProvider` динамически не изменять службу. Однако он определяет лучше всего доступного поставщика, один раз во время их жизненный цикл. При изменении состояния поставщика после его задания, приложению потребуется дополнительный код в `ILocationListener` методы &ndash; `OnProviderEnabled`, `OnProviderDisabled`, и `OnStatusChanged` &ndash; обрабатывать все возможности, связанные с ключ поставщика.

## <a name="summary"></a>Сводка

В этом руководстве рассматривались, получение из местоположения пользователя с помощью Android службы расположения и поставщика склеенную расположения из интерфейса API служб расположения Google.


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
