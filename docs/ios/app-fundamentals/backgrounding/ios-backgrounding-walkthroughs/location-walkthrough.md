---
title: Пошаговое руководство. расположения в фоновом режиме в Xamarin.iOS
description: Этот документ содержит пошаговое руководство демонстрирует использование сведений о расположении в backgrounded приложении Xamarin.iOS. Он описывает, необходимые для установки, пользовательский интерфейс и состояний приложения.
ms.prod: xamarin
ms.assetid: F8EEA0FD-5614-47FE-ADAC-80A5BCA6EB5F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 0d6dac02ac82b74c9b0e33f5fff0b82223df1f47
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108682"
---
# <a name="walkthrough---background-location-in-xamarinios"></a>Пошаговое руководство. расположения в фоновом режиме в Xamarin.iOS

В этом примере мы собираемся строить iOS расположение приложения, которое выводит сведения о наших текущее расположение: широты, долготы и другие параметры на экране. Это приложение будет показано, как правильно выполнять обновления расположение, пока приложение находится активный "или" Backgrounded.

В этом пошаговом руководстве описывается какой-либо ключ фоновой обработки понятия, включая зарегистрировать приложение как приложение необходимости фона, приостановка обновления пользовательского интерфейса, когда приложение выполняется в фоновом режиме и работа с `WillEnterBackground` и `WillEnterForeground` `AppDelegate` методы .

## <a name="application-set-up"></a>Настройка приложения


1. Во-первых, создайте новый **iOS > приложение > приложение одного представления (C#)**. Назовите его _расположение_ и убедитесь, что выбраны iPad и iPhone.

1. Расположение приложения определяет как фон необходимости приложение в iOS. Зарегистрировать приложение в качестве расположения приложения путем редактирования **Info.plist** файл проекта.

    В обозревателе решений дважды щелкните **Info.plist** файл, чтобы открыть его и перейдите к нижней части списка. Установите флажок, оба **включить фоновые режимы** и **обновления расположения** флажки.

    В Visual Studio для Mac она будет выглядеть примерно так:

    [![](location-walkthrough-images/image7.png "Установите флажок Включить фоновые режимы и флажки обновлений расположений")](location-walkthrough-images/image7.png#lightbox)

    В Visual Studio **Info.plist** должен быть обновлен вручную, добавив следующую пару "ключ значение":

    ```xml
    <key>UIBackgroundModes</key>
    <array>
      <string>location</string>
    </array>
    ```

1. Теперь, когда приложение зарегистрировано, его можно получить данные о местоположении устройства. В iOS `CLLocationManager` класс используется для доступа к сведения о расположении и может выдать события, передавать обновления расположения.

1. В коде, создайте новый класс с именем `LocationManager` , представляет собой единое место для некоторые экраны и код, чтобы подписаться на обновления расположения. В `LocationManager` класса, создать экземпляр `CLLocationManager` вызывается `LocMgr`:

    ```csharp
    public class LocationManager
    {
        protected CLLocationManager locMgr;

        public LocationManager () {
            this.locMgr = new CLLocationManager();
            this.locMgr.PausesLocationUpdatesAutomatically = false;

            // iOS 8 has additional permissions requirements
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                locMgr.RequestAlwaysAuthorization (); // works in background
                //locMgr.RequestWhenInUseAuthorization (); // only in foreground
            }

            if (UIDevice.CurrentDevice.CheckSystemVersion (9, 0)) {
                locMgr.AllowsBackgroundLocationUpdates = true;
            }
        }

        public CLLocationManager LocMgr {
            get { return this.locMgr; }
        }
    }
    ```

    Приведенный выше код задает несколько свойств и разрешений на [CLLocationManager](https://developer.xamarin.com/api/type/CoreLocation.CLLocationManager/) класса:

    - `PausesLocationUpdatesAutomatically` — Это логическое значение, которое можно задать в зависимости от того, разрешено ли приостановка расположение обновлений системы. На некоторых устройствах по умолчанию используется `true`, что может привести к устройству прекратить получение фонового обновления расположения примерно через 15 минут.
    - `RequestAlwaysAuthorization` — Следует передать этот метод, чтобы дать пользователю приложения параметр, чтобы разрешить доступ в фоновом режиме. `RequestWhenInUseAuthorization` также могут передаваться, если вы хотите дать пользователю возможность разрешить расположение доступны только в том случае, когда приложение находится на переднем плане.
    - `AllowsBackgroundLocationUpdates` — Это логическое свойство, впервые появился в iOS 9, можно задать, чтобы разрешить приложению получать обновления расположения при приостановке.

    > [!IMPORTANT]
    > iOS 8 (и более) также необходима запись в **Info.plist** файл, чтобы показать пользователю как часть запроса авторизации.

1. Добавьте раздел `NSLocationAlwaysUsageDescription` или `NSLocationWhenInUseUsageDescription` со строкой, которая будет отображаться для пользователя в оповещение, которое запрашивает доступ к данным расположения.

1. требует iOS 9, при использовании `AllowsBackgroundLocationUpdates` **Info.plist** включает ключ `UIBackgroundModes` со значением `location`. После выполнения шага 2 в этом пошаговом руководстве, это уже должны были в файле Info.plist.


1. Внутри `LocationManager` создайте метод, вызванный `StartLocationUpdates` следующим кодом. Этот код показывает, как к получению расположение обновлений из `CLLocationManager`:

    ```csharp
    if (CLLocationManager.LocationServicesEnabled) {
        //set the desired accuracy, in meters
        LocMgr.DesiredAccuracy = 1;
        LocMgr.LocationsUpdated += (object sender, CLLocationsUpdatedEventArgs e) =>
        {
            // fire our custom Location Updated event
            LocationUpdated (this, new LocationUpdatedEventArgs (e.Locations [e.Locations.Length - 1]));
        };
        LocMgr.StartUpdatingLocation();
    }
    ```

    Существует несколько важных моментов, происходит в этот метод. Во-первых мы выполнить проверку, чтобы увидеть, если приложение имеет доступ к данным расположения на устройстве. Мы проверить это путем вызова `LocationServicesEnabled` на `CLLocationManager`. Этот метод будет возвращать **false** Если пользователь отклонил доступ приложения к сведения о расположении.

1. Затем укажите частоту диспетчером местоположения для обновления. `CLLocationManager` предоставляет много параметров для фильтрации и настройке данных о местоположении, включая частоту обновлений. В этом примере значение `DesiredAccuracy` для обновления при изменении местоположения, единица измерения. Дополнительные сведения о настройке частоту обновления расположение и другие необходимые настройки, см. [ссылки на класс CLLocationManager](http://developer.apple.com/library/ios/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html) в документации компании Apple.

1. Наконец, вызовите `StartUpdatingLocation` на `CLLocationManager` экземпляра. Это означает, что диспетчером местоположения для получения начального исправление на текущем месте и с которого начинается отправка обновлений

На данный момент диспетчером местоположения создан, настроен со всеми типами данных, мы хотим получать, и определяет исходное расположение. Теперь код должен для подготовки к просмотру данные расположения в пользовательский интерфейс. Это можно сделать с помощью пользовательское событие, которое принимает `CLLocation` в качестве аргумента:

```csharp
// event for the location changing
public event EventHandler<LocationUpdatedEventArgs>LocationUpdated = delegate { };
```

Далее необходимо подписаться на обновления расположения из `CLLocationManager`и инициировать пользовательские `LocationUpdated` событие, когда новые данные о местоположении становится доступной, передав в расположении, что аргумент. Чтобы сделать это, создайте новый класс **LocationUpdateEventArgs.cs**. Этот код доступен в рамках основного приложения и возвращает местоположение устройства, при возникновении события:

```csharp
public class LocationUpdatedEventArgs : EventArgs
{
    CLLocation location;

    public LocationUpdatedEventArgs(CLLocation location)
    {
       this.location = location;
    }

    public CLLocation Location
    {
       get { return location; }
    }
}
```

## <a name="user-interface"></a>Пользовательский интерфейс

1. Используйте конструктор iOS для создания экрана, который будет отображать сведения о расположении. Дважды щелкните **Main.storyboard** файл, чтобы начать.

    На раскадровке перетащите несколько меток на экране, чтобы выступать в качестве местозаполнителей для сведения о расположении. В этом примере существует метки для широты, долготы, высота над уровнем моря, курс и скорости.

    Макет должен выглядеть следующим образом:

    ![](location-walkthrough-images/image8.png "Пример плана пользовательского интерфейса в конструкторе iOS")

1. На панели решения, дважды щелкните `ViewController.cs` файл и изменить его, чтобы создать новый экземпляр LocationManager и вызовите `StartLocationUpdates`на нем.
  Измените код, чтобы выглядеть следующим образом:

    ```csharp
    #region Computed Properties
    public static bool UserInterfaceIdiomIsPhone {
                get { return UIDevice.CurrentDevice.UserInterfaceIdiom == UIUserInterfaceIdiom.Phone; }
            }

    public static LocationManager Manager { get; set;}
    #endregion

    #region Constructors
    public ViewController (IntPtr handle) : base (handle)
    {
    // As soon as the app is done launching, begin generating location updates in the location manager
        Manager = new LocationManager();
        Manager.StartLocationUpdates();
    }

    #endregion
    ```

    Эта команда запустит расположение обновлений при запуске приложения, несмотря на то, что данные не будут отображаться.

1. Теперь, когда Получает расположение обновления, обновление экрана, используя сведения о расположении. Следующий метод получает расположение, из наших `LocationUpdated` событий и отображает их в пользовательском Интерфейсе:

    ```csharp
    #region Public Methods
    public void HandleLocationChanged (object sender, LocationUpdatedEventArgs e)
    {
        // Handle foreground updates
        CLLocation location = e.Location;

        LblAltitude.Text = location.Altitude + " meters";
        LblLongitude.Text = location.Coordinate.Longitude.ToString ();
        LblLatitude.Text = location.Coordinate.Latitude.ToString ();
        LblCourse.Text = location.Course.ToString ();
        LblSpeed.Text = location.Speed.ToString ();

        Console.WriteLine ("foreground updated");
    }
    #endregion
    ```

Мы по-прежнему необходимо подписаться на `LocationUpdated` событие в нашей AppDelegate и вызов нового метода для обновления пользовательского интерфейса. Добавьте следующий код в `ViewDidLoad,` сразу же после `StartLocationUpdates` вызова:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // It is better to handle this with notifications, so that the UI updates
    // resume when the application re-enters the foreground!
    Manager.LocationUpdated += HandleLocationChanged;

}
```


Теперь когда приложение запускается, он должен выглядеть примерно так:

[![](location-walkthrough-images/image5.png "Запустите пример приложения")](location-walkthrough-images/image5.png#lightbox)

## <a name="handling-active-and-background-states"></a>Обработка состояний активный и фона

1. Приложение является вывод обновления расположения, находящегося на переднем плане и active. Чтобы продемонстрировать, что происходит при переходе приложения в фоновом режиме, переопределите `AppDelegate` методы, которые отслеживают приложение изменений состояния, чтобы приложение записывает в консоль, когда он переходит между переднего плана и фона:

    ```csharp
    public override void DidEnterBackground (UIApplication application)
    {
        Console.WriteLine ("App entering background state.");
    }

    public override void WillEnterForeground (UIApplication application)
    {
        Console.WriteLine ("App will enter foreground");
    }
    ```

    Добавьте следующий код в `LocationManager` постоянно печать расположение выходных данных приложения, чтобы проверить сведения о расположении данных по-прежнему доступны в фоновом режиме:

    ```csharp
    public class LocationManager
    {
        public LocationManager ()
        {
        ...
        LocationUpdated += PrintLocation;
        }
        ...

        //This will keep going in the background and the foreground
        public void PrintLocation (object sender, LocationUpdatedEventArgs e) {
        CLLocation location = e.Location;
        Console.WriteLine ("Altitude: " + location.Altitude + " meters");
        Console.WriteLine ("Longitude: " + location.Coordinate.Longitude);
        Console.WriteLine ("Latitude: " + location.Coordinate.Latitude);
        Console.WriteLine ("Course: " + location.Course);
        Console.WriteLine ("Speed: " + location.Speed);
        }
    }
    ```

1. Один важный аспект оставшиеся кодом: попытка обновить пользовательский Интерфейс, когда приложение выполняется в фоновом режиме будет причина iOS завершит его. Когда приложение выходит в фоновом режиме, код должен отменить подписку на расположение обновления и остановить обновление пользовательского интерфейса.

    iOS предоставляет нам уведомления во время работы приложения о переход в другое приложение состояний. В этом случае мы могут подписаться на `ObserveDidEnterBackground` уведомлений.

    В следующем фрагменте кода показано, как использовать уведомления для позволяет определить, когда необходимо остановить обновление элементов пользовательского интерфейса. Это будет отправлена `ViewDidLoad`:

    ```csharp
    UIApplication.Notifications.ObserveDidEnterBackground ((sender, args) => {
        Manager.LocationUpdated -= HandleLocationChanged;
    });
    ```

    Когда приложение выполняется, выходные данные будут выглядеть следующим образом:

    ![](location-walkthrough-images/image6.png "Пример выходных данных расположения, в консоли")

1. Приложение выводит расположение обновления экрана при работе на переднем плане и продолжает печатать данные в окно вывода приложения при работе в фоновом режиме.

Остается только один решены проблемы: экрана начинается обновление элементов пользовательского интерфейса при первой загрузке приложения, но нет возможности узнать, когда приложение повторно вошел переднего плана. Если backgrounded приложение переводится обратно на передний план, не возобновить обновление элементов пользовательского интерфейса.

Чтобы устранить эту проблему, вложите вызов, чтобы начать обновление элементов пользовательского интерфейса внутри еще одно уведомление, которое инициируется, когда приложение переходит в активное состояние:

```csharp
UIApplication.Notifications.ObserveDidBecomeActive ((sender, args) => {
  Manager.LocationUpdated += HandleLocationChanged;
});
```

Теперь пользовательский Интерфейс начнут обновляться при первом запуске приложения, и возобновить обновление любое время приложение возвращается на передний план.

В этом пошаговом руководстве мы создали приложение правильно, в фоновом режиме с поддержкой iOS, которое выводит данные расположение на экране и окне вывода приложения.


## <a name="related-links"></a>Связанные ссылки

- [Расположение (часть 4) (пример)](https://developer.xamarin.com/samples/monotouch/Location/)
- [Ссылку на Framework Core расположение](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CoreLocation_Framework/_index.html)
