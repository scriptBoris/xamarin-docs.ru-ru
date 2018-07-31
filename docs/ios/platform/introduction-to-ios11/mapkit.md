---
title: Новые возможности в MapKit в iOS 11
description: 'Этом документе описываются новые возможности iOS 11 MapKit: группирование маркеры, компаса кнопки, масштабированного представления и кнопки отслеживания пользователя.'
ms.prod: xamarin
ms.assetid: 304AE5A3-518F-422F-BE24-92D62CE30F34
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/30/2017
ms.openlocfilehash: c060a7bbc8d5968aeaca5f84743cdf22513dfbec
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39350590"
---
# <a name="new-features-in-mapkit-on-ios-11"></a>Новые возможности в MapKit в iOS 11

iOS 11 MapKit добавляет следующие новые функции:

- [Заметка кластеризации](#clustering)
- [Компаса кнопки](#compass)
- [Масштабированного представления](#scale)
- [Кнопка отслеживания пользователя](#user-tracking)

![Схема демонстрирующая кластеризованный маркеры и компаса кнопки](mapkit-images/cyclemap-heading.png)

<a name="clustering" />

## <a name="automatically-grouping-markers-while-zooming"></a>Автоматическое группирование маркеров во время масштабирования

Образец [MapKit образец «Tandm»](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample/) показан способ реализации новой заметки iOS 11 кластеров.

### <a name="1-create-an-mkpointannotation-subclass"></a>1. Создание `MKPointAnnotation` подкласс

Класс заметки точка представляет каждый маркер на карте. Могут быть добавлены по отдельности, используя `MapView.AddAnnotation()` или из массива с помощью `MapView.AddAnnotations()`.

Классы точки заметки не имеют визуального представления, они необходимы только для представления данных, связанное с маркером (что самое важное, `Coordinate` свойство, которое является его широты и долготы на карте) и любые пользовательские свойства:

```csharp
public class Bike : MKPointAnnotation
{
  public BikeType Type { get; set; } = BikeType.Tricycle;
  public Bike(){}
  public Bike(NSNumber lat, NSNumber lgn, NSNumber type)
  {
    Coordinate = new CLLocationCoordinate2D(lat.NFloatValue, lgn.NFloatValue);
    switch(type.NUIntValue) {
      case 0:
        Type = BikeType.Unicycle;
        break;
      case 1:
        Type = BikeType.Tricycle;
        break;
    }
  }
}
```

### <a name="2-create-an-mkmarkerannotationview-subclass-for-single-markers"></a>2. Создание `MKMarkerAnnotationView` подкласса для одного маркеры

Представление маркера заметки — визуальное представление каждого примечания и реализуется с помощью свойств, таких как:

- **MarkerTintColor** — цвет маркера.
- **GlyphText** — отображение текста в маркер.
- **GlyphImage** — задает изображение, отображаемое в маркер.
- **DisplayPriority** — определяет z порядок (наложения поведение) при переполнении карты с маркерами. Используйте один из `Required`, `DefaultHigh`, или `DefaultLow`.

Для поддержки автоматической кластеризации, необходимо также задать:

- **ClusteringIdentifier** — определяет, какие маркеры получить компактными группами. Можно использовать тот же идентификатор для всех маркеров, или использовать разные идентификаторы для управления способом, они группируются вместе.

```csharp
[Register("BikeView")]
public class BikeView : MKMarkerAnnotationView
{
  public static UIColor UnicycleColor = UIColor.FromRGB(254, 122, 36);
  public static UIColor TricycleColor = UIColor.FromRGB(153, 180, 44);
  public override IMKAnnotation Annotation
  {
    get {
      return base.Annotation;
    }
    set {
      base.Annotation = value;

      var bike = value as Bike;
      if (bike != null){
        ClusteringIdentifier = "bike";
        switch(bike.Type){
          case BikeType.Unicycle:
            MarkerTintColor = UnicycleColor;
            GlyphImage = UIImage.FromBundle("Unicycle");
            DisplayPriority = MKFeatureDisplayPriority.DefaultLow;
            break;
          case BikeType.Tricycle:
            MarkerTintColor = TricycleColor;
            GlyphImage = UIImage.FromBundle("Tricycle");
            DisplayPriority = MKFeatureDisplayPriority.DefaultHigh;
            break;
        }
      }
    }
  }
```

### <a name="3-create-an-mkannotationview-to-represent-clusters-of-markers"></a>3. Создание `MKAnnotationView` для представления кластеров маркеров

При представлении заметки, который представляет кластер маркеров _удалось_ быть простого образа, пользователи ожидают, что приложению предоставлять визуальные подсказки о времени были сгруппированы количества маркерами.

[Пример кода](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample/) использует CoreGraphics для подготовки к просмотру число маркеров в кластере, а также представление графа круг долю каждого типа маркера.

Также следует задать:

- **DisplayPriority** — определяет z порядок (наложения поведение) при переполнении карты с маркерами. Используйте один из `Required`, `DefaultHigh`, или `DefaultLow`.
- **CollisionMode** — `Circle` или `Rectangle`.

```csharp
[Register("ClusterView")]
public class ClusterView : MKAnnotationView
{
  public static UIColor ClusterColor = UIColor.FromRGB(202, 150, 38);
  public override IMKAnnotation Annotation
  {
    get {
      return base.Annotation;
    }
    set {
      base.Annotation = value;
      var cluster = MKAnnotationWrapperExtensions.UnwrapClusterAnnotation(value);
      if (cluster != null)
      {
        var renderer = new UIGraphicsImageRenderer(new CGSize(40, 40));
        var count = cluster.MemberAnnotations.Length;
        var unicycleCount = CountBikeType(cluster.MemberAnnotations, BikeType.Unicycle);

        Image = renderer.CreateImage((context) => {
          // Fill full circle with tricycle color
          BikeView.TricycleColor.SetFill();
          UIBezierPath.FromOval(new CGRect(0, 0, 40, 40)).Fill();
          // Fill pie with unicycle color
          BikeView.UnicycleColor.SetFill();
          var piePath = new UIBezierPath();
          piePath.AddArc(new CGPoint(20,20), 20, 0, (nfloat)(Math.PI * 2.0 * unicycleCount / count), true);
          piePath.AddLineTo(new CGPoint(20, 20));
          piePath.ClosePath();
          piePath.Fill();
          // Fill inner circle with white color
          UIColor.White.SetFill();
          UIBezierPath.FromOval(new CGRect(8, 8, 24, 24)).Fill();
          // Finally draw count text vertically and horizontally centered
          var attributes = new UIStringAttributes() {
            ForegroundColor = UIColor.Black,
            Font = UIFont.BoldSystemFontOfSize(20)
          };
          var text = new NSString($"{count}");
          var size = text.GetSizeUsingAttributes(attributes);
          var rect = new CGRect(20 - size.Width / 2, 20 - size.Height / 2, size.Width, size.Height);
          text.DrawString(rect, attributes);
        });
      }
    }
  }
  public ClusterView(){}
  public ClusterView(MKAnnotation annotation, string reuseIdentifier) : base(annotation, reuseIdentifier)
  {
    DisplayPriority = MKFeatureDisplayPriority.DefaultHigh;
    CollisionMode = MKAnnotationViewCollisionMode.Circle;
    // Offset center point to animate better with marker annotations
    CenterOffset = new CoreGraphics.CGPoint(0, -10);
  }
  private nuint CountBikeType(IMKAnnotation[] members, BikeType type) {
    nuint count = 0;
    foreach(Bike member in members){
      if (member.Type == type) ++count;
    }
    return count;
  }
}
```

### <a name="4-register-the-view-classes"></a>4. Регистрация классов представления

Когда создается элемент управления map представление и добавлены к представлению, зарегистрировать типы представлений заметки, чтобы включить автоматическое поведение кластеризации, как карты является одновременное увеличение и уменьшение:

```csharp
MapView.Register(typeof(BikeView), MKMapViewDefault.AnnotationViewReuseIdentifier);
MapView.Register(typeof(ClusterView), MKMapViewDefault.ClusterAnnotationViewReuseIdentifier);
```

### <a name="5-render-the-map"></a>5. Визуализации карты!

При отображении карты маркеры заметок кластеризованный или подготовке к просмотру в зависимости от масштаба. По мере изменения масштаба и из него кластеров анимировать маркеры.

![Симулятор, показывающий кластеризованный маркеров на карте](mapkit-images/cyclemap-sml.png)

Ссылаться на [сопоставляет разделе](~/ios/user-interface/controls/ios-maps/index.md) Дополнительные сведения об отображении данных с помощью MapKit.

<a name="compass" />

## <a name="compass-button"></a>Компаса кнопки

iOS 11 добавляет возможность pop компас уменьшение карты и визуализировать его в другом месте в представлении. См. в разделе [Tandm пример приложения](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample/) пример.

Создание кнопки, выглядит следующим образом (включая динамической анимации при изменении ориентации карты), компас и готовит его к просмотру в другом элементе управления.

![Компаса кнопки на панели навигации](mapkit-images/compass-sml.png)

Приведенный ниже код создает компаса кнопку и отображает его на панели навигации:

```csharp
var compass = MKCompassButton.FromMapView(MapView);
compass.CompassVisibility = MKFeatureVisibility.Visible;
NavigationItem.RightBarButtonItem = new UIBarButtonItem(compass);
MapView.ShowsCompass = false; // so we don't have two compasses!
```

`ShowsCompass` Свойство может использоваться для управления видимостью ориентиры по умолчанию в представлении карты.

<a name="scale" />

## <a name="scale-view"></a>Масштабированного представления

Добавление масштаб в любом месте в представление, используя `MKScaleView.FromMapView()` метод для получения экземпляра для добавления в другом месте в иерархии представлений масштабированного представления.

![Масштабированного представления, выводимое на карте](mapkit-images/scale-sml.png)

```csharp
var scale = MKScaleView.FromMapView(MapView);
scale.LegendAlignment = MKScaleViewAlignment.Trailing;
scale.TranslatesAutoresizingMaskIntoConstraints = false;
View.AddSubview(scale); // constraints omitted for simplicity
MapView.ShowsScale = false; // so we don't have two scale displays!
```

`ShowsScale` Свойство может использоваться для управления видимостью ориентиры по умолчанию в представлении карты.

<a name="user-tracking" />

## <a name="user-tracking-button"></a>Кнопка отслеживания пользователя

Кнопка отслеживания пользователя на карте отображается текущее местоположение пользователя. Используйте `MKUserTrackingButton.FromMapView()` метод для получения экземпляра кнопки, применять форматирование и добавить в другом месте в иерархии представлений.

![Расположение кнопки пользователя, накладывается на карте](mapkit-images/user-location-sml.png)

```csharp
var button = MKUserTrackingButton.FromMapView(MapView);
button.Layer.BackgroundColor = UIColor.FromRGBA(255,255,255,80).CGColor;
button.Layer.BorderColor = UIColor.White.CGColor;
button.Layer.BorderWidth = 1;
button.Layer.CornerRadius = 5;
button.TranslatesAutoresizingMaskIntoConstraints = false;
View.AddSubview(button); // constraints omitted for simplicity
```


## <a name="related-links"></a>Связанные ссылки

- [Пример MapKit «Tandm»](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample/)
- [MKCompassButton](https://developer.apple.com/documentation/mapkit/mkcompassbutton)
- [Новые возможности в MapKit (WWDC) (видео)](https://developer.apple.com/videos/play/wwdc2017/237/)
