---
title: Выделение маршрута на карте
description: В этой статье содержатся сведения о добавлении наложения в виде ломаной линии на карту. Наложение в виде ломаной линии — это ряд соединенных линейных сегментов, которые обычно используются для отображения маршрута на карте или создания любой необходимой фигуры.
ms.prod: xamarin
ms.assetid: FBFDC715-1654-4188-82A0-FC522548BCFF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 786f050495d4682b719178f2723c482929544678
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998726"
---
# <a name="highlighting-a-route-on-a-map"></a>Выделение маршрута на карте

_В этой статье содержатся сведения о добавлении наложения в виде ломаной линии на карту. Наложение в виде ломаной линии — это ряд соединенных линейных сегментов, которые обычно используются для отображения маршрута на карте или создания любой необходимой фигуры._

## <a name="overview"></a>Обзор

Наложение — это многослойное изображение на карте. Наложения позволяют рисовать графическое содержимое, которое масштабируется вместе с картой. На следующих снимках экрана показан результат добавления наложения в виде ломаной линии на карту.

![](polyline-map-overlay-images/screenshots.png)

Когда элемент управления [`Map`](xref:Xamarin.Forms.Maps.Map) отображается в приложении Xamarin.Forms, в iOS создается экземпляр класса `MapRenderer`, который, в свою очередь, создает собственный элемент управления `MKMapView`. На платформе Android класс `MapRenderer` создает собственный элемент управления `MapView`. На универсальной платформе Windows (UWP) класс `MapRenderer` создает собственный элемент управления `MapControl`. Процесс отрисовки можно использовать для реализации настроек карты для конкретных платформ путем создания пользовательского отрисовщика для `Map` на каждой платформе. Этот процесс выглядит следующим образом:

1. [Создание](#Creating_the_Custom_Map) пользовательской карты Xamarin.Forms.
1. [Использование](#Consuming_the_Custom_Map) пользовательской карты в Xamarin.Forms.
1. [Настройка](#Customizing_the_Map) карты путем создания пользовательского отрисовщика для нее на каждой платформе.

> [!NOTE]
> Перед использованием необходимо инициализировать и настроить [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps). Дополнительные сведения см. на веб-сайте [`Maps Control`](~/xamarin-forms/user-interface/map.md).

Сведения о настройке карты с помощью пользовательского отрисовщика см. в разделе [Настройка закрепления карты](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>Создание пользовательской карты

Создайте подкласс класса [`Map`](xref:Xamarin.Forms.Maps.Map), в котором добавлено свойство `RouteCoordinates`:

```csharp
public class CustomMap : Map
{
  public List<Position> RouteCoordinates { get; set; }

  public CustomMap ()
  {
    RouteCoordinates = new List<Position> ();
  }
}
```

В свойстве `RouteCoordinates` будет храниться коллекция координат, определяющих выделяемый маршрут.

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
    customMap.RouteCoordinates.Add (new Position (37.785559, -122.396728));
    customMap.RouteCoordinates.Add (new Position (37.780624, -122.390541));
    customMap.RouteCoordinates.Add (new Position (37.777113, -122.394983));
    customMap.RouteCoordinates.Add (new Position (37.776831, -122.394627));

    customMap.MoveToRegion (MapSpan.FromCenterAndRadius (new Position (37.79752, -122.40183), Distance.FromMiles (1.0)));
  }
}
```

При инициализации задается ряд координат широты и долготы, определяющих выделяемый на карте маршрут. Затем представление карты позиционируется с помощью метода [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*). Он изменяет положение и масштаб карты, создавая объект [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) на основе [`Position`](xref:Xamarin.Forms.Maps.Position) и [`Distance`](xref:Xamarin.Forms.Maps.Distance).

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>Настройка карты

Теперь в каждый проект приложения необходимо добавить пользовательский отрисовщик для добавления наложения в виде ломаной линии на карту.

#### <a name="creating-the-custom-renderer-on-ios"></a>Создание пользовательского отрисовщика в iOS

Создайте подкласс класса `MapRenderer` и переопределите его метод `OnElementChanged` для добавления наложения в виде ломаной линии:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.iOS
{
    public class CustomMapRenderer : MapRenderer
    {
        MKPolylineRenderer polylineRenderer;

        protected override void OnElementChanged(ElementChangedEventArgs<View> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null) {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null) {
                    nativeMap.RemoveOverlays(nativeMap.Overlays);
                    nativeMap.OverlayRenderer = null;
                    polylineRenderer = null;
                }
            }

            if (e.NewElement != null) {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;
                nativeMap.OverlayRenderer = GetOverlayRenderer;

                CLLocationCoordinate2D[] coords = new CLLocationCoordinate2D[formsMap.RouteCoordinates.Count];
                int index = 0;
                foreach (var position in formsMap.RouteCoordinates)
                {
                    coords[index] = new CLLocationCoordinate2D(position.Latitude, position.Longitude);
                    index++;
                }

                var routeOverlay = MKPolyline.FromCoordinates(coords);
                nativeMap.AddOverlay(routeOverlay);
            }
        }
        ...
    }
}

```

Этот метод выполняет указанные ниже действия, если пользовательский отрисовщик подключен к новому элементу Xamarin.Forms.

- Свойству `MKMapView.OverlayRenderer` присваивается соответствующий делегат.
- Коллекция координат широты и долготы извлекается из свойства `CustomMap.RouteCoordinates` и сохраняется как массив экземпляров `CLLocationCoordinate2D`.
- Ломаная линия создается путем вызова статического метода `MKPolyline.FromCoordinates`, который задает широту и долготу каждой точки.
- Ломаная линия добавляется на карту путем вызова метода `MKMapView.AddOverlay`.

Затем реализуйте метод `GetOverlayRenderer` для настройки отрисовки наложения:

```csharp
public class CustomMapRenderer : MapRenderer
{
  MKPolylineRenderer polylineRenderer;
  ...

  MKOverlayRenderer GetOverlayRenderer(MKMapView mapView, IMKOverlay overlayWrapper)
  {
      if (polylineRenderer == null && !Equals(overlayWrapper, null)) {
          var overlay = Runtime.GetNSObject(overlayWrapper.Handle) as IMKOverlay;
          polylineRenderer = new MKPolylineRenderer(overlay as MKPolyline) {
              FillColor = UIColor.Blue,
              StrokeColor = UIColor.Red,
              LineWidth = 3,
              Alpha = 0.4f
          };
      }
      return polylineRenderer;
  }
}
```

#### <a name="creating-the-custom-renderer-on-android"></a>Создание пользовательского отрисовщика в Android

Создайте подкласс класса `MapRenderer` и переопределите его методы `OnElementChanged` и `OnMapReady` для добавления наложения в виде ломаной линии:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.Droid
{
    public class CustomMapRenderer : MapRenderer
    {
        List<Position> routeCoordinates;

        public CustomMapRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(Xamarin.Forms.Platform.Android.ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                routeCoordinates = formsMap.RouteCoordinates;
                Control.GetMapAsync(this);
            }
        }

        protected override void OnMapReady(Android.Gms.Maps.GoogleMap map)
        {
            base.OnMapReady(map);

            var polylineOptions = new PolylineOptions();
            polylineOptions.InvokeColor(0x66FF0000);

            foreach (var position in routeCoordinates)
            {
                polylineOptions.Add(new LatLng(position.Latitude, position.Longitude));
            }

            NativeMap.AddPolyline(polylineOptions);
        }
    }
}
```

Метод `OnElementChanged` извлекает коллекцию координат широты и долготы из свойства `CustomMap.RouteCoordinates` и сохраняет ее в переменной-члене. Затем он вызывает метод `MapView.GetMapAsync`, который получает базовую карту `GoogleMap`, связанную с представлением, если пользовательский отрисовщик подключен к новому элементу Xamarin.Forms. Когда экземпляр `GoogleMap` становится доступен, вызывается метод `OnMapReady`, который создает ломаную линию путем создания экземпляра `PolylineOptions`, определяющего широту и долготу каждой точки. После этого ломаная линия добавляется на карту путем вызова метода `NativeMap.AddPolyline`.

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Создание пользовательского отрисовщика на универсальной платформе Windows

Создайте подкласс класса `MapRenderer` и переопределите его метод `OnElementChanged` для добавления наложения в виде ломаной линии:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.UWP
{
    public class CustomMapRenderer : MapRenderer
    {
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

                var coordinates = new List<BasicGeoposition>();
                foreach (var position in formsMap.RouteCoordinates)
                {
                    coordinates.Add(new BasicGeoposition() { Latitude = position.Latitude, Longitude = position.Longitude });
                }

                var polyline = new MapPolyline();
                polyline.StrokeColor = Windows.UI.Color.FromArgb(128, 255, 0, 0);
                polyline.StrokeThickness = 5;
                polyline.Path = new Geopath(coordinates);
                nativeMap.MapElements.Add(polyline);
            }
        }
    }
}
```

Этот метод выполняет указанные ниже операции, если пользовательский отрисовщик подключен к новому элементу Xamarin.Forms.

- Коллекция координат широты и долготы извлекается из свойства `CustomMap.RouteCoordinates` и преобразуется в список `List` координат `BasicGeoposition`.
- Ломаная линия создается путем создания объекта `MapPolyline`. Класс `MapPolygon` используется для отображения линии на карте. Для этого его свойству `Path` присваивается объект `Geopath`, содержащий координаты линии.
- Ломаная линия отображается на карте путем ее добавления в коллекцию `MapControl.MapElements`.

## <a name="summary"></a>Сводка

В этой статье было описано, как добавить наложение в виде ломаной линии на карту для отображения маршрута на карте или создания любой необходимой фигуры.


## <a name="related-links"></a>Связанные ссылки

- [Наложение на карте в виде ломаной линии (пример)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/polyline/)
- [Настройка закрепления карты](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](xref:Xamarin.Forms.Maps)
