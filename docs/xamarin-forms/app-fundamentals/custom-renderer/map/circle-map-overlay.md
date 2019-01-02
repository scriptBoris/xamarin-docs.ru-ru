---
title: Выделение круговой области на карте
description: В этой статье содержатся сведения о добавлении кругового наложения на карту для выделения на ней круговой области. В iOS и Android имеются интерфейсы API для добавления кругового наложения на карту, а в UWP наложение отрисовывается как многоугольник.
ms.prod: xamarin
ms.assetid: 6FF8BD15-074E-4E6A-9522-F9E2BE32EF12
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: ed671063aad55cb6c4730494f024a2ec2a016de1
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53051935"
---
# <a name="highlighting-a-circular-area-on-a-map"></a>Выделение круговой области на карте

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/circle/)

_В этой статье содержатся сведения о добавлении кругового наложения на карту для выделения на ней круговой области._

## <a name="overview"></a>Обзор

Наложение — это многослойное изображение на карте. Наложения позволяют рисовать графическое содержимое, которое масштабируется вместе с картой. На следующих снимках экрана показан результат добавления кругового наложения на карту.

![](circle-map-overlay-images/screenshots.png)

Когда элемент управления [`Map`](xref:Xamarin.Forms.Maps.Map) отображается в приложении Xamarin.Forms, в iOS создается экземпляр класса `MapRenderer`, который, в свою очередь, создает собственный элемент управления `MKMapView`. На платформе Android класс `MapRenderer` создает собственный элемент управления `MapView`. На универсальной платформе Windows (UWP) класс `MapRenderer` создает собственный элемент управления `MapControl`. Процесс отрисовки можно использовать для реализации настроек карты для конкретных платформ путем создания пользовательского отрисовщика для `Map` на каждой платформе. Этот процесс выглядит следующим образом:

1. [Создание](#Creating_the_Custom_Map) пользовательской карты Xamarin.Forms.
1. [использование](#Consuming_the_Custom_Map) настраиваемой карты в Xamarin.Forms;
1. [настройка](#Customizing_the_Map) карты путем создания настраиваемого отрисовщика для нее на каждой платформе.

> [!NOTE]
> Перед использованием необходимо инициализировать и настроить [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps). Дополнительные сведения см. в разделе [`Maps Control`](~/xamarin-forms/user-interface/map.md).

Сведения о настройке карты с помощью пользовательского отрисовщика см. в разделе [Настройка закрепления карты](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>Создание пользовательской карты

Создайте класс `CustomCircle` со свойствами `Position` и `Radius`:

```csharp
public class CustomCircle
{
  public Position Position { get; set; }
  public double Radius { get; set; }
}
```

Затем создайте подкласс класса [`Map`](xref:Xamarin.Forms.Maps.Map), в котором добавлено свойство типа `CustomCircle`:

```csharp
public class CustomMap : Map
{
  public CustomCircle Circle { get; set; }
}
```

<a name="Consuming_the_Custom_Map" />

### <a name="consuming-the-custom-map"></a>Использование пользовательской карты

Для использования элемента управления `CustomMap` объявите его экземпляр в экземпляре страницы XAML:

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

Вы также можете использовать элемент управления `CustomMap`, объявив его экземпляр в экземпляре страницы C#:

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

Инициализируйте элемент управления `CustomMap` требуемым образом:

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

При инициализации экземпляры [`Pin`](xref:Xamarin.Forms.Maps.Pin) и `CustomCircle` добавляются на пользовательскую карту, а затем представление карты позиционируется с помощью метода [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*). Положение и масштаб карты изменяются путем создания объекта [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) на основе [`Position`](xref:Xamarin.Forms.Maps.Position) и [`Distance`](xref:Xamarin.Forms.Maps.Distance).

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>Настройка карты

Теперь в каждый проект приложения необходимо добавить пользовательский отрисовщик для добавления кругового наложения на карту.

#### <a name="creating-the-custom-renderer-on-ios"></a>Создание пользовательского отрисовщика в iOS

Создайте подкласс класса `MapRenderer` и переопределите его метод `OnElementChanged` для добавления кругового наложения:

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

Этот метод выполняет указанные ниже действия, если пользовательский отрисовщик подключен к новому элементу Xamarin.Forms:

- Свойству `MKMapView.OverlayRenderer` присваивается соответствующий делегат.
- Создается круг путем задания статического объекта `MKCircle`, который определяет центр круга и радиус в метрах.
- Круг добавляется на карту путем вызова метода `MKMapView.AddOverlay`.

Затем реализуйте метод `GetOverlayRenderer` для настройки отрисовки наложения:

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

#### <a name="creating-the-custom-renderer-on-android"></a>Создание пользовательского отрисовщика в Android

Создайте подкласс класса `MapRenderer` и переопределите его методы `OnElementChanged` и `OnMapReady` для добавления кругового наложения:

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

Метод `OnElementChanged` вызывает метод `MapView.GetMapAsync`, который получает базовую карту `GoogleMap`, связанную с представлением, если пользовательский отрисовщик подключен к новому элементу Xamarin.Forms. Когда экземпляр `GoogleMap` становится доступен, вызывается метод `OnMapReady`, который создает круг путем создания объекта `CircleOptions`, определяющего центр круга и его радиус в метрах. Затем круг добавляется на карту путем вызова метода `NativeMap.AddCircle`.

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Создание пользовательского отрисовщика на универсальной платформе Windows

Создайте подкласс класса `MapRenderer` и переопределите его метод `OnElementChanged` для добавления кругового наложения:

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

Этот метод выполняет указанные ниже операции, если пользовательский отрисовщик подключен к новому элементу Xamarin.Forms:

- Положение и радиус круга извлекаются из свойства `CustomMap.Circle` и передаются в метод `GenerateCircleCoordinates`, который создает координаты широты и долготы для окружности. Код этого вспомогательного метода показан ниже.
- Координаты окружности преобразуются в список `List` координат `BasicGeoposition`.
- Круг создается путем создания объекта `MapPolygon`. Класс `MapPolygon` используется для отображения многоугольной фигуры на карте. Для этого его свойству `Path` присваивается объект `Geopath`, содержащий координаты фигуры.
- Многоугольник отображается на карте путем его добавления в коллекцию `MapControl.MapElements`.


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

В этой статье были представлены сведения о добавлении кругового наложения на карту для выделения на ней круговой области.


## <a name="related-links"></a>Связанные ссылки

- [Круговое наложение на карте (пример)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/circle/)
- [Настройка закрепления карты](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](xref:Xamarin.Forms.Maps)
