---
title: Выделение маршрута на карте
description: В этой статье описывается добавление наложения ломаной линии на карту. Наложение ломаной линии — это последовательность соединенных отрезков, которые обычно используются для отображения маршрута на карте или формируют любой фигуры, который требуется.
ms.prod: xamarin
ms.assetid: FBFDC715-1654-4188-82A0-FC522548BCFF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 786f050495d4682b719178f2723c482929544678
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998726"
---
# <a name="highlighting-a-route-on-a-map"></a>Выделение маршрута на карте

_В этой статье описывается добавление наложения ломаной линии на карту. Наложение ломаной линии — это последовательность соединенных отрезков, которые обычно используются для отображения маршрута на карте или формируют любой фигуры, который требуется._

## <a name="overview"></a>Обзор

Наложение — это многоуровневая изображение на карте. Наложений поддерживает рисование графического содержимого, которые масштабируются вместе с карты, так как оно увеличивается. На следующих снимках экрана показано результат сложения наложении ломаной линии на карту:

![](polyline-map-overlay-images/screenshots.png)

Когда [ `Map` ](xref:Xamarin.Forms.Maps.Map) элемент управления отрисовывается в приложении Xamarin.Forms в iOS `MapRenderer` создается экземпляр класса, который в свою очередь создает экземпляр собственного `MKMapView` элемента управления. На платформе Android `MapRenderer` класс создает экземпляр собственного `MapView` элемента управления. В универсальной платформы Windows (UWP), `MapRenderer` класс создает экземпляр собственного `MapControl`. Процесс подготовки отчета можно задействуем преимущества реализации настроек карты конкретных платформ путем создания пользовательского средства визуализации для `Map` на каждой платформе. Таким образом процесс выглядит следующим образом:

1. [Создание](#Creating_the_Custom_Map) пользовательской карты Xamarin.Forms.
1. [Использовать](#Consuming_the_Custom_Map) пользовательских карт с помощью Xamarin.Forms.
1. [Настройка](#Customizing_the_Map) карты, создав пользовательское средство отрисовки карты на каждой платформе.

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) необходимо инициализировать и настроить перед использованием. Дополнительные сведения см. на веб-сайте [`Maps Control`](~/xamarin-forms/user-interface/map.md).

Сведения о настройке карты с помощью пользовательского средства визуализации, см. в разделе [Настройка закрепления карты](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>Создание пользовательской карты

Создать подкласс [ `Map` ](xref:Xamarin.Forms.Maps.Map) класс, который добавляет `RouteCoordinates` свойство:

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

`RouteCoordinates` Свойство будет хранить коллекцию координат, определяющих маршрут к выделению.

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
    customMap.RouteCoordinates.Add (new Position (37.785559, -122.396728));
    customMap.RouteCoordinates.Add (new Position (37.780624, -122.390541));
    customMap.RouteCoordinates.Add (new Position (37.777113, -122.394983));
    customMap.RouteCoordinates.Add (new Position (37.776831, -122.394627));

    customMap.MoveToRegion (MapSpan.FromCenterAndRadius (new Position (37.79752, -122.40183), Distance.FromMiles (1.0)));
  }
}
```

Эта инициализация указывает ряд координаты широты и долготы, для определения маршрута на карте, чтобы быть выделенным. Затем оно располагает представление карты с [ `MoveToRegion` ](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) метод, который изменяет положение и уровень масштаба карты, создав [ `MapSpan` ](xref:Xamarin.Forms.Maps.MapSpan) из [ `Position` ](xref:Xamarin.Forms.Maps.Position) и [ `Distance` ](xref:Xamarin.Forms.Maps.Distance).

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>Настройка карты

Пользовательское средство отрисовки теперь должны добавляться в каждый проект приложения для добавления в оверлей ломаной линии на карту.

#### <a name="creating-the-custom-renderer-on-ios"></a>Создание пользовательского средства визуализации на iOS

Создать подкласс `MapRenderer` класса и переопределять его `OnElementChanged` метод для добавления в оверлей ломаной линии:

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

Этот метод выполняет следующую конфигурацию, при условии, что пользовательское средство отрисовки подключен к новому элементу Xamarin.Forms.

- `MKMapView.OverlayRenderer` Свойству соответствующий делегат.
- Коллекция координаты широты и долготы, извлекаются из `CustomMap.RouteCoordinates` свойство и хранятся в виде массива `CLLocationCoordinate2D` экземпляров.
- Ломаной линии создается путем вызова статического `MKPolyline.FromCoordinates` метод, который задает широту и долготу каждой точки.
- Ломаной линии добавляется на карту путем вызова `MKMapView.AddOverlay` метод.

Затем запустите `GetOverlayRenderer` метод для настройки оформления наложения:

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

#### <a name="creating-the-custom-renderer-on-android"></a>Создание пользовательского средства визуализации на устройстве Android

Создать подкласс `MapRenderer` класса и переопределять его `OnElementChanged` и `OnMapReady` методы для добавления в оверлей ломаной линии:

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

`OnElementChanged` Метод получает коллекцию координаты широты и долготы из `CustomMap.RouteCoordinates` свойство и сохраняет их в переменную-член. Затем он вызывает `MapView.GetMapAsync` метод, который возвращает базовый `GoogleMap` привязывается к представлению, при условии, что пользовательское средство отрисовки подключен к новому элементу Xamarin.Forms. Один раз `GoogleMap` экземпляр доступен, `OnMapReady` вызывается метод, где ломаной линии создается путем создания экземпляра `PolylineOptions` , указывающий широту и долготу каждой точки. Ломаной линии затем добавляется к схеме, вызвав `NativeMap.AddPolyline` метод.

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Создание пользовательского средства визуализации на платформе универсальных Windows

Создать подкласс `MapRenderer` класса и переопределять его `OnElementChanged` метод для добавления в оверлей ломаной линии:

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

Этот метод выполняет следующие операции, при условии, что пользовательское средство отрисовки подключен к новому элементу Xamarin.Forms:

- Коллекция координаты широты и долготы, извлекаются из `CustomMap.RouteCoordinates` свойство и преобразовано в `List` из `BasicGeoposition` координаты.
- Ломаной линии создается путем создания экземпляра `MapPolyline` объекта. `MapPolygon` Класс используется для отображения линии на карте, задав его `Path` свойства `Geopath` , содержащий координаты линии.
- К просмотру ломаной линии на карте, добавив его `MapControl.MapElements` коллекции.

## <a name="summary"></a>Сводка

В этой статье описываются способы добавления наложения ломаной линии на карту для отображения маршрута на карте или формируют любой фигуры, который требуется.


## <a name="related-links"></a>Связанные ссылки

- [Ovlerlay ломаной линии карты (пример)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/polyline/)
- [Настройка закрепления карты](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](xref:Xamarin.Forms.Maps)
