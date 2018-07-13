---
title: Выделение круговой области на карте
description: В этой статье объясняется, как добавить циклическая наложение на карту, чтобы выделить круговой области карты. IOS и Android предлагает API-интерфейсы для добавления циклическая наложение на карту, в универсальной платформы Windows наложения обрабатывается как многоугольник.
ms.prod: xamarin
ms.assetid: 6FF8BD15-074E-4E6A-9522-F9E2BE32EF12
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 3064296d4c78a3342fb27afc971c37a029987e5e
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998561"
---
# <a name="highlighting-a-circular-area-on-a-map"></a>Выделение круговой области на карте

_В этой статье объясняется, как добавить циклическая наложение на карту, чтобы выделить круговой области карты._

## <a name="overview"></a>Обзор

Наложение — это многоуровневая изображение на карте. Наложений поддерживает рисование графического содержимого, которые масштабируются вместе с карты, так как оно увеличивается. На следующих снимках экрана показано результат сложения циклическая наложение на карту:

![](circle-map-overlay-images/screenshots.png)

Когда [ `Map` ](xref:Xamarin.Forms.Maps.Map) элемент управления отрисовывается в приложении Xamarin.Forms в iOS `MapRenderer` создается экземпляр класса, который в свою очередь создает экземпляр собственного `MKMapView` элемента управления. На платформе Android `MapRenderer` класс создает экземпляр собственного `MapView` элемента управления. В универсальной платформы Windows (UWP), `MapRenderer` класс создает экземпляр собственного `MapControl`. Процесс подготовки отчета можно задействуем преимущества реализации настроек карты конкретных платформ путем создания пользовательского средства визуализации для `Map` на каждой платформе. Таким образом процесс выглядит следующим образом:

1. [Создание](#Creating_the_Custom_Map) пользовательской карты Xamarin.Forms.
1. [Использовать](#Consuming_the_Custom_Map) пользовательских карт с помощью Xamarin.Forms.
1. [Настройка](#Customizing_the_Map) карты, создав пользовательское средство отрисовки карты на каждой платформе.

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) необходимо инициализировать и настроить перед использованием. Дополнительные сведения см. в разделе [`Maps Control`](~/xamarin-forms/user-interface/map.md)

Сведения о настройке карты с помощью пользовательского средства визуализации, см. в разделе [Настройка закрепления карты](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>Создание пользовательской карты

Создание `CustomCircle` классом, имеющим `Position` и `Radius` свойства:

```csharp
public class CustomCircle
{
  public Position Position { get; set; }
  public double Radius { get; set; }
}
```

Затем создайте подкласс [ `Map` ](xref:Xamarin.Forms.Maps.Map) класс, который добавляет свойство типа `CustomCircle`:

```csharp
public class CustomMap : Map
{
  public CustomCircle Circle { get; set; }
}
```

<a name="Consuming_the_Custom_Map" />

### <a name="consuming-the-custom-map"></a>Использование пользовательской карты

Использовать `CustomMap` элемента управления, объявив его экземпляр в экземпляр страницы XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MapOverlay;assembly=MapOverlay"
             x:Class="MapOverlay.MapPage">
    <ContentPage.Content>
        <local:CustomMap x:Name="customMap" MapType="Street" WidthRequest="{x:Static local:App.ScreenWidth}" HeightRequest="{x:Static local:App.ScreenHeight}" />
    </ContentPage.Content>
</ContentPage>
```

В качестве альтернативы использовать `CustomMap` элемента управления, объявив его экземпляр в экземпляр страницы C#:

```csharp
public class MapPageCS : ContentPage
{
    public MapPageCS ()
    {
        var customMap = new CustomMap {
            MapType = MapType.Street,
            WidthRequest = App.ScreenWidth,
            HeightRequest = App.ScreenHeight
        };
        ...
        Content = customMap;
    }
}
```

Инициализировать `CustomMap` управления:

```csharp
public partial class MapPage : ContentPage
{
  public MapPage ()
  {
    ...
    var pin = new Pin {
      Type = PinType.Place,
      Position = new Position (37.79752, -122.40183),
      Label = "Xamarin San Francisco Office",
      Address = "394 Pacific Ave, San Francisco CA"
    };

    var position = new Position (37.79752, -122.40183);
    customMap.Circle = new CustomCircle {
      Position = position,
      Radius = 1000
    };

    customMap.Pins.Add (pin);
    customMap.MoveToRegion (MapSpan.FromCenterAndRadius (position, Distance.FromMiles (1.0)));
  }
}
```

Эта инициализация добавляет [ `Pin` ](xref:Xamarin.Forms.Maps.Pin) и `CustomCircle` экземпляров для пользовательской карты и помещает представление карты с [ `MoveToRegion` ](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) метод, который изменяет положение и масштаб Уровень сопоставления, создав [ `MapSpan` ](xref:Xamarin.Forms.Maps.MapSpan) из [ `Position` ](xref:Xamarin.Forms.Maps.Position) и [ `Distance` ](xref:Xamarin.Forms.Maps.Distance).

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>Настройка карты

Пользовательское средство отрисовки теперь должны добавляться в каждый проект приложения для добавления циклическая наложение на карту.

#### <a name="creating-the-custom-renderer-on-ios"></a>Создание пользовательского средства визуализации на iOS

Создать подкласс `MapRenderer` класса и переопределять его `OnElementChanged` метод, чтобы добавить циклическая наложения:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.iOS
{
    public class CustomMapRenderer : MapRenderer
    {
        MKCircleRenderer circleRenderer;

        protected override void OnElementChanged(ElementChangedEventArgs<View> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null) {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null) {
                    nativeMap.RemoveOverlays(nativeMap.Overlays);
                    nativeMap.OverlayRenderer = null;
                    circleRenderer = null;
                }
            }

            if (e.NewElement != null) {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;
                var circle = formsMap.Circle;

                nativeMap.OverlayRenderer = GetOverlayRenderer;

                var circleOverlay = MKCircle.Circle(new CoreLocation.CLLocationCoordinate2D(circle.Position.Latitude, circle.Position.Longitude), circle.Radius);
                nativeMap.AddOverlay(circleOverlay);
            }
        }
        ...
    }
}

```

Этот метод выполняет следующую конфигурацию, при условии, что пользовательское средство отрисовки подключен к новому элементу Xamarin.Forms.

- `MKMapView.OverlayRenderer` Свойству соответствующий делегат.
- Элемент управления circle создается путем установки статического `MKCircle` , указывающий центр окружности и радиус окружности в метрах.
- Элемент управления circle добавляется на карту путем вызова `MKMapView.AddOverlay` метод.

Затем запустите `GetOverlayRenderer` метод для настройки оформления наложения:

```csharp
public class CustomMapRenderer : MapRenderer
{
  MKCircleRenderer circleRenderer;
  ...

  MKOverlayRenderer GetOverlayRenderer(MKMapView mapView, IMKOverlay overlayWrapper)
  {
      if (circleRenderer == null && !Equals(overlayWrapper, null)) {
          var overlay = Runtime.GetNSObject(overlayWrapper.Handle) as IMKOverlay;
          circleRenderer = new MKCircleRenderer(overlay as MKCircle) {
              FillColor = UIColor.Red,
              Alpha = 0.4f
          };
      }
      return circleRenderer;
  }
}
```

#### <a name="creating-the-custom-renderer-on-android"></a>Создание пользовательского средства визуализации на устройстве Android

Создать подкласс `MapRenderer` класса и переопределять его `OnElementChanged` и `OnMapReady` методы для добавления циклическая наложения:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.Droid
{
    public class CustomMapRenderer : MapRenderer
    {
        CustomCircle circle;

        public CustomMapRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(Xamarin.Forms.Platform.Android.ElementChangedEventArgs<Xamarin.Forms.Maps.Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                circle = formsMap.Circle;
                Control.GetMapAsync(this);
            }
        }

        protected override void OnMapReady(Android.Gms.Maps.GoogleMap map)
        {
            base.OnMapReady(map);

            var circleOptions = new CircleOptions();
            circleOptions.InvokeCenter(new LatLng(circle.Position.Latitude, circle.Position.Longitude));
            circleOptions.InvokeRadius(circle.Radius);
            circleOptions.InvokeFillColor(0X66FF0000);
            circleOptions.InvokeStrokeColor(0X66FF0000);
            circleOptions.InvokeStrokeWidth(0);

            NativeMap.AddCircle(circleOptions);
        }
    }
}
```

`OnElementChanged` Вызовы методов `MapView.GetMapAsync` метод, который возвращает базовый `GoogleMap` привязывается к представлению, при условии, что пользовательское средство отрисовки подключен к новому элементу Xamarin.Forms. Один раз `GoogleMap` экземпляр доступен, `OnMapReady` вызывается метод, где элемент управления circle создается путем создания экземпляра `CircleOptions` , указывающий центр окружности и радиус окружности в метрах. Элемент управления circle добавляется карты, вызвав `NativeMap.AddCircle` метод.

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Создание пользовательского средства визуализации на платформе универсальных Windows

Создать подкласс `MapRenderer` класса и переопределять его `OnElementChanged` метод, чтобы добавить циклическая наложения:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.UWP
{
    public class CustomMapRenderer : MapRenderer
    {
        const int EarthRadiusInMeteres = 6371000;

        protected override void OnElementChanged(ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
            }
            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MapControl;
                var circle = formsMap.Circle;

                var coordinates = new List<BasicGeoposition>();
                var positions = GenerateCircleCoordinates(circle.Position, circle.Radius);
                foreach (var position in positions)
                {
                    coordinates.Add(new BasicGeoposition { Latitude = position.Latitude, Longitude = position.Longitude });
                }

                var polygon = new MapPolygon();
                polygon.FillColor = Windows.UI.Color.FromArgb(128, 255, 0, 0);
                polygon.StrokeColor = Windows.UI.Color.FromArgb(128, 255, 0, 0);
                polygon.StrokeThickness = 5;
                polygon.Path = new Geopath(coordinates);
                nativeMap.MapElements.Add(polygon);
            }
        }
        // GenerateCircleCoordinates helper method (below)
    }
}
```

Этот метод выполняет следующие операции, при условии, что пользовательское средство отрисовки подключен к новому элементу Xamarin.Forms:

- Круг положение и radius извлекаются из `CustomMap.Circle` свойство и передается `GenerateCircleCoordinates` метод, который создает широту и долготу координат для периметра окружности. Ниже приведен код для этот вспомогательный метод.
- Координаты периметра окружности преобразуются в `List` из `BasicGeoposition` координаты.
- Элемент управления circle создается путем создания экземпляра `MapPolygon` объекта. `MapPolygon` Класс используется для отображения фигуры нескольких точек на карте, задав его `Path` свойства `Geopath` , содержащий координаты фигуры.
- Многоугольника отрисовывается на карте, добавив его `MapControl.MapElements` коллекции.


```
List<Position> GenerateCircleCoordinates(Position position, double radius)
{
    double latitude = position.Latitude.ToRadians();
    double longitude = position.Longitude.ToRadians();
    double distance = radius / EarthRadiusInMeteres;
    var positions = new List<Position>();

    for (int angle = 0; angle <=360; angle++)
    {
        double angleInRadians = ((double)angle).ToRadians();
        double latitudeInRadians = Math.Asin(Math.Sin(latitude) * Math.Cos(distance) + Math.Cos(latitude) * Math.Sin(distance) * Math.Cos(angleInRadians));
        double longitudeInRadians = longitude + Math.Atan2(Math.Sin(angleInRadians) * Math.Sin(distance) * Math.Cos(latitude), Math.Cos(distance) - Math.Sin(latitude) * Math.Sin(latitudeInRadians));

        var pos = new Position(latitudeInRadians.ToDegrees(), longitudeInRadians.ToDegrees());
        positions.Add(pos);
    }

    return positions;
}
```

## <a name="summary"></a>Сводка

В этой статье описываются способы добавления циклическая наложение на карту, чтобы выделить круговой области карты.


## <a name="related-links"></a>Связанные ссылки

- [Циклическая Ovlerlay карты (пример)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/circle/)
- [Настройка закрепления карты](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](xref:Xamarin.Forms.Maps)
