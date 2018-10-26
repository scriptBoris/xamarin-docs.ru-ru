---
title: Заметки и наложения в Xamarin.iOS
description: Эта статья содержит пошаговое руководство, в котором показано, как работать с функциями заметки и наложения Map Kit. В этом примере показано добавление карты в приложение, которое отображает заметки и наложения в расположении конференции 2013 развиваться Xamarin.
ms.prod: xamarin
ms.assetid: 1BC4F7FC-AE3C-46D7-A4D3-18E142F55B8E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 445661513b0cf79df99d54ed0bb4b0261dd75c2a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105443"
---
# <a name="annotations-and-overlays-in-xamarinios"></a>Заметки и наложения в Xamarin.iOS

Приложение, которое мы создадим в этом пошаговом руководстве показан ниже:

 [![](ios-maps-walkthrough-images/00-map-overlay.png "Пример приложения MapKit")](ios-maps-walkthrough-images/00-map-overlay.png#lightbox)
 
Вы найдете полный код в [пример пошагового руководства Maps](https://developer.xamarin.com/samples/monotouch/MapsWalkthrough/).

Начнем с создания нового **пустой проект iOS**и присвоить ей соответствующие имя. Мы начнем с добавления кода в контроллер представления для отображения в объекте MapView и создаст новые классы для наших MapDelegate и пользовательских заметок. Для этого выполните следующие действия:

## <a name="viewcontroller"></a>ViewController


1. Добавьте следующие пространства имен для `ViewController`:

    ```csharp
    using MapKit;
    using CoreLocation;
    using UIKit
    using CoreGraphics
    ```

1. Добавить `MKMapView` переменную в класс экземпляра вместе с `MapDelegate` экземпляра. Мы создадим `MapDelegate` чуть ниже:

    ```csharp
    public partial class ViewController : UIViewController
    {
        MKMapView map;
        MapDelegate mapDelegate;
        ...
    ```

1. В свойстве контроллера `LoadView` метод, добавьте `MKMapView` и присвойте ему значение `View` свойства контроллера:

    ```csharp
    public override void LoadView ()
    {
        map = new MKMapView (UIScreen.MainScreen.Bounds);
        View = map;
    }
    ```

    Далее мы добавим код для инициализации сопоставления в "ViewDidLoad'' метод.

1. В `ViewDidLoad` добавьте код, чтобы задать тип карты отображения положения пользователя и разрешить масштабирование и панорамирование:

    ```csharp
    // change map type, show user location and allow zooming and panning
    map.MapType = MKMapType.Standard;
    map.ShowsUserLocation = true;
    map.ZoomEnabled = true;
    map.ScrollEnabled = true;
    
    ```

1. Затем добавьте код, чтобы центрировать карту и задать его регион:

    ```csharp
    double lat = 30.2652233534254;
    double lon = -97.73815460962083;
    CLLocationCoordinate2D mapCenter = new CLLocationCoordinate2D (lat, lon);
    MKCoordinateRegion mapRegion = MKCoordinateRegion.FromDistance (mapCenter, 100, 100);
    map.CenterCoordinate = mapCenter;
    map.Region = mapRegion;
    
    ```

1. Создайте новый экземпляр класса `MapDelegate` и назначьте его `Delegate` из `MKMapView`. Опять же, мы будем implcodeent `MapDelegate` чуть ниже:

    ```csharp
    mapDelegate = new MapDelegate ();
    map.Delegate = mapDelegate;     
    ```

1. Начиная с версии iOS 8 следует запрашивает авторизацию от пользователя для использования их расположения, так что давайте добавим это в нашем примере. Во-первых, определите `CLLocationManager` переменную уровня класса:

    ```csharp
    CLLocationManager locationManager = new CLLocationManager();
    ```

1. В `ViewDidLoad` метод, мы хотим проверить, если на устройстве, выполнение приложения используется iOS 8, и если это мы будет запрашивать авторизацию, когда приложение используется:

    ```csharp
    if (UIDevice.CurrentDevice.CheckSystemVersion(8,0)){
                    locationManager.RequestWhenInUseAuthorization ();
                }
    ```

1. Наконец, нам нужно изменить **Info.plist** файл, чтобы уведомить пользователей о причину запроса их расположение. В **источника** меню **Info.plist**, добавьте следующий раздел:
    
    `NSLocationWhenInUseUsageDescription` 
    
    и строка: 

    `Maps Walkthrough Docs Sample`.


## <a name="conferenceannotationcs--a-class-for-custom-annotations"></a>ConferenceAnnotation.cs-класс для пользовательских заметок


1. Мы будем использовать пользовательский класс для заметки вызывается `ConferenceAnnotation`. Добавьте следующий класс в проект:

    ```csharp
    using System;
    using CoreLocation;
    using MapKit;
    
    namespace MapsWalkthrough
    {
        public class ConferenceAnnotation : MKAnnotation
        {
            string title;
            CLLocationCoordinate2D coord;
    
            public ConferenceAnnotation (string title,
            CLLocationCoordinate2D coord)
            {
                this.title = title;
                this.coord = coord;
            }
    
            public override string Title {
                get {
                    return title;
                }
            }
    
            public override CLLocationCoordinate2D Coordinate {
                get {
                    return coord;
                }
            }
        }
    }   
    ```

## <a name="viewcontroller---adding-the-annotation-and-overlay"></a>ViewController - Добавление заметки и наложения

1. С помощью `ConferenceAnnotation` на месте мы можем добавить его к схеме. Вернитесь в `ViewDidLoad` метод `ViewController`, добавить заметки по оси y центра карты:

    ```csharp
    map.AddAnnotations (new ConferenceAnnotation ("Evolve Conference", mapCenter)); 
    ```

1. Мы также хотим иметь наложение отеля. Добавьте следующий код для создания `MKPolygon` с помощью координат для отеля предоставляются и добавьте его к схеме, вызов `AddOverlay`:

    ```csharp
    // add an overlay of the hotel
    MKPolygon hotelOverlay = MKPolygon.FromCoordinates(
        new CLLocationCoordinate2D[]{
        new CLLocationCoordinate2D(30.2649977168594, -97.73863627705),
        new CLLocationCoordinate2D(30.2648461170005, -97.7381627734755),
        new CLLocationCoordinate2D(30.2648355402574, -97.7381750192576),
        new CLLocationCoordinate2D(30.2647791309417, -97.7379872505988),
        new CLLocationCoordinate2D(30.2654525150319, -97.7377341711021),
        new CLLocationCoordinate2D(30.2654807195004, -97.7377994819399),
        new CLLocationCoordinate2D(30.2655089239607, -97.7377994819399),
        new CLLocationCoordinate2D(30.2656428950368, -97.738346460207),
        new CLLocationCoordinate2D(30.2650364981811, -97.7385709662122),
        new CLLocationCoordinate2D(30.2650470749025, -97.7386199493406)
    });
    
    map.AddOverlay (hotelOverlay);  
    ```
Это завершает код в `ViewDidLoad`. Теперь нам нужно реализовать наши `MapDelegate` класс для создания заметки и наложения представления соответственно.


## <a name="mapdelegate"></a>MapDelegate

1. Создайте класс с именем `MapDelegate` , наследуемый от `MKMapViewDelegate` и включают `annotationId` переменной для использования в качестве идентификатора повторное использование для заметки:

    ```csharp
    class MapDelegate : MKMapViewDelegate
    {
        static string annotationId = "ConferenceAnnotation";
        ...
    }
    ```
    Здесь у нас только есть одно примечание для повторного использования кода не является обязательным, но рекомендуется включить его.

1. Реализуйте `GetViewForAnnotation` метод, чтобы вернуть представление для `ConferenceAnnotation` с помощью **conference.png** изображения, включенные в этом пошаговом руководстве:

    ```csharp
    public override MKAnnotationView GetViewForAnnotation (MKMapView mapView, NSObject annotation)
    {
        MKAnnotationView annotationView = null;
    
        if (annotation is MKUserLocation)
            return null; 
    
        if (annotation is ConferenceAnnotation) {
    
            // show conference annotation
            annotationView = mapView.DequeueReusableAnnotation (annotationId);
    
            if (annotationView == null)
                annotationView = new MKAnnotationView (annotation, annotationId);
        
            annotationView.Image = UIImage.FromFile ("images/conference.png");
            annotationView.CanShowCallout = true;
        } 
    
        return annotationView;
    }
    ```

1. Когда пользователь касается заметку, мы хотим отображать изображение Город Остин. Добавьте следующие переменные `MapDelegate` для изображения и представление для отображения:

    ```csharp
    UIImageView venueView;
    UIImage venueImage;
    ```

1. Далее, чтобы отобразить изображение шифрованию заметки, реализовать `DidSelectAnnotation` метод следующим образом:

    ```csharp
    public override void DidSelectAnnotationView (MKMapView mapView, MKAnnotationView view)
    {
        // show an image view when the conference annotation view is selected
        if (view.Annotation is ConferenceAnnotation) {
    
            venueView = new UIImageView ();
            venueView.ContentMode = UIViewContentMode.ScaleAspectFit;
            venueImage = UIImage.FromFile ("image/venue.png");
            venueView.Image = venueImage;
            view.AddSubview (venueView);
    
            UIView.Animate (0.4, () => {
            venueView.Frame = new CGRect (-75, -75, 200, 200); });
        }
    }
    ```

1. Чтобы скрыть изображение, когда пользователь отменяет выделение заметки, коснувшись везде на карте, реализовать `DidSelectAnnotationView` метод следующим образом:

    ```csharp
    public override void DidDeselectAnnotationView (MKMapView mapView, MKAnnotationView view)
    {
        // remove the image view when the conference annotation is deselected
        if (view.Annotation is ConferenceAnnotation) {
    
            venueView.RemoveFromSuperview ();
            venueView.Dispose ();
            venueView = null;
        }
    }
    ```
    Теперь у нас есть код для заметки на месте. Все, что остается — добавить код для `MapDelegate` Создание представления для элемента управления overlay отеля.

1. Добавьте следующую реализацию `GetViewForOverlay` для `MapDelegate`:

    ```csharp
    public override MKOverlayView GetViewForOverlay (MKMapView mapView, NSObject overlay)
    {
        // return a view for the polygon
        MKPolygon polygon = overlay as MKPolygon;
        MKPolygonView polygonView = new MKPolygonView (polygon);
        polygonView.FillColor = UIColor.Blue;
        polygonView.StrokeColor = UIColor.Red;
        return polygonView;
    }
    ```

Запустите приложение. Теперь у нас есть интерактивную карту с пользовательские примечания и наложение! Щелкните на ней и отображается изображение Остин, как показано ниже:

 [![](ios-maps-walkthrough-images/01-map-image.png "Коснуться заметки и появится изображение Остине")](ios-maps-walkthrough-images/01-map-image.png#lightbox)

## <a name="summary"></a>Сводка

В этой статье мы рассмотрели способы добавления заметки к схеме, а также как добавлять наложение для указанного многоугольника. Мы также продемонстрировали, как добавить поддержку сенсорного ввода для заметки для анимации изображения на карте.


## <a name="related-links"></a>Связанные ссылки

- [Пример пошагового руководства Maps](https://developer.xamarin.com/samples/monotouch/MapsWalkthrough/)
- [Демонстрационный пример карты](https://developer.xamarin.com/samples/monotouch/MapDemo/)
- [iOS Maps](~/ios/user-interface/controls/ios-maps/index.md)
