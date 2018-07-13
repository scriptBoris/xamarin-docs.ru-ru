---
title: Выделение региона на карте
description: В этой статье объясняется, как добавить наложения многоугольника на карту, для выделения области на карте. Многоугольники являются замкнутой фигуры и контактируют заполнено.
ms.prod: xamarin
ms.assetid: E79EB2CF-8DD6-44A8-B47D-5F0A94FB0A63
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 0a11e9c25922531727ad2fee3bbed9c8d4e2b80c
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998138"
---
# <a name="highlighting-a-region-on-a-map"></a>Выделение региона на карте

_В этой статье описываются способы добавления наложения многоугольника на карту, для выделения области на карте. Многоугольники являются замкнутой фигуры и контактируют заполнено._

## <a name="overview"></a>Обзор

Наложение — это многоуровневая изображение на карте. Наложений поддерживает рисование графического содержимого, которые масштабируются вместе с карты, так как оно увеличивается. На следующих снимках экрана показано результат сложения наложении многоугольника на карту:

![](polygon-map-overlay-images/screenshots.png)

Когда [ `Map` ](xref:Xamarin.Forms.Maps.Map) элемент управления отрисовывается в приложении Xamarin.Forms в iOS `MapRenderer` создается экземпляр класса, который в свою очередь создает экземпляр собственного `MKMapView` элемента управления. На платформе Android `MapRenderer` класс создает экземпляр собственного `MapView` элемента управления. В универсальной платформы Windows (UWP), `MapRenderer` класс создает экземпляр собственного `MapControl`. Процесс подготовки отчета можно задействуем преимущества реализации настроек карты конкретных платформ путем создания пользовательского средства визуализации для `Map` на каждой платформе. Таким образом процесс выглядит следующим образом:

1. [Создание](#Creating_the_Custom_Map) пользовательской карты Xamarin.Forms.
1. [Использовать](#Consuming_the_Custom_Map) пользовательских карт с помощью Xamarin.Forms.
1. [Настройка](#Customizing_the_Map) карты, создав пользовательское средство отрисовки карты на каждой платформе.

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) необходимо инициализировать и настроить перед использованием. Дополнительные сведения см. на веб-сайте [`Maps Control`](~/xamarin-forms/user-interface/map.md).

Сведения о настройке карты с помощью пользовательского средства визуализации, см. в разделе [Настройка закрепления карты](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>Создание пользовательской карты

Создать подкласс [ `Map` ](xref:Xamarin.Forms.Maps.Map) класс, который добавляет `ShapeCoordinates` свойство:

```csharp
public class CustomMap : Map
{
  public List<Position> ShapeCoordinates { get; set; }

  public CustomMap ()
  {
    ShapeCoordinates = new List<Position> ();
  }
}
```

`ShapeCoordinates` Свойство будет хранить набор координат, определяющих области, чтобы быть выделенным.

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
    customMap.ShapeCoordinates.Add (new Position (37.797513, -122.402058));
    customMap.ShapeCoordinates.Add (new Position (37.798433, -122.402256));
    customMap.ShapeCoordinates.Add (new Position (37.798582, -122.401071));
    customMap.ShapeCoordinates.Add (new Position (37.797658, -122.400888));

    customMap.MoveToRegion (MapSpan.FromCenterAndRadius (new Position (37.79752, -122.40183), Distance.FromMiles (0.1)));
  }
}
```

Эта инициализация указывает ряд координаты широты и долготы, для определения области элемента карты, чтобы быть выделенным. Затем оно располагает представление карты с [ `MoveToRegion` ](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) метод, который изменяет положение и уровень масштаба карты, создав [ `MapSpan` ](xref:Xamarin.Forms.Maps.MapSpan) из [ `Position` ](xref:Xamarin.Forms.Maps.Position) и [ `Distance` ](xref:Xamarin.Forms.Maps.Distance).

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>Настройка карты

Пользовательское средство отрисовки теперь должны добавляться в каждый проект приложения для добавления многоугольника наложение на карту.

#### <a name="creating-the-custom-renderer-on-ios"></a>Создание пользовательского средства визуализации на iOS

Создать подкласс `MapRenderer` класса и переопределять его `OnElementChanged` метод для добавления многоугольника наложения:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.iOS
{
    public class CustomMapRenderer : MapRenderer
    {
        MKPolygonRenderer polygonRenderer;

        protected override void OnElementChanged(ElementChangedEventArgs<View> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null) {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null) {
                    nativeMap.RemoveOverlays(nativeMap.Overlays);
                    nativeMap.OverlayRenderer = null;
                    polygonRenderer = null;
                }
            }

            if (e.NewElement != null) {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;

                nativeMap.OverlayRenderer = GetOverlayRenderer;

                CLLocationCoordinate2D[] coords = new CLLocationCoordinate2D[formsMap.ShapeCoordinates.Count];

                int index = 0;
                foreach (var position in formsMap.ShapeCoordinates)
                {
                    coords[index] = new CLLocationCoordinate2D(position.Latitude, position.Longitude);
                    index++;
                }

                var blockOverlay = MKPolygon.FromCoordinates(coords);
                nativeMap.AddOverlay(blockOverlay);
            }
        }
        ...
    }
}

```

Этот метод выполняет следующую конфигурацию, при условии, что пользовательское средство отрисовки подключен к новому элементу Xamarin.Forms.

- `MKMapView.OverlayRenderer` Свойству соответствующий делегат.
- Коллекция координаты широты и долготы, извлекаются из `CustomMap.ShapeCoordinates` свойство и хранятся в виде массива `CLLocationCoordinate2D` экземпляров.
- Многоугольник создается путем вызова статического `MKPolygon.FromCoordinates` метод, который задает широту и долготу каждой точки.
- Многоугольник добавляется на карту путем вызова `MKMapView.AddOverlay` метод. Этот метод автоматически закрывает многоугольника, нарисовав линию, соединяющую начальную и конечную точки.

Затем запустите `GetOverlayRenderer` метод для настройки оформления наложения:

```csharp
public class CustomMapRenderer : MapRenderer
{
  MKPolygonRenderer polygonRenderer;
  ...

  MKOverlayRenderer GetOverlayRenderer(MKMapView mapView, IMKOverlay overlayWrapper)
  {
      if (polygonRenderer == null && !Equals(overlayWrapper, null)) {
          var overlay = Runtime.GetNSObject(overlayWrapper.Handle) as IMKOverlay;
          polygonRenderer = new MKPolygonRenderer(overlay as MKPolygon) {
              FillColor = UIColor.Red,
              StrokeColor = UIColor.Blue,
              Alpha = 0.4f,
              LineWidth = 9
          };
      }
      return polygonRenderer;
  }
}
```

#### <a name="creating-the-custom-renderer-on-android"></a>Создание пользовательского средства визуализации на устройстве Android

Создать подкласс `MapRenderer` класса и переопределять его `OnElementChanged` и `OnMapReady` методы для добавления многоугольника наложения:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.Droid
{
    public class CustomMapRenderer : MapRenderer
    {
        List<Position> shapeCoordinates;

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
                shapeCoordinates = formsMap.ShapeCoordinates;
                Control.GetMapAsync(this);
            }
        }

        protected override void OnMapReady(Android.Gms.Maps.GoogleMap map)
        {
            base.OnMapReady(map);

            var polygonOptions = new PolygonOptions();
            polygonOptions.InvokeFillColor(0x66FF0000);
            polygonOptions.InvokeStrokeColor(0x660000FF);
            polygonOptions.InvokeStrokeWidth(30.0f);

            foreach (var position in shapeCoordinates)
            {
                polygonOptions.Add(new LatLng(position.Latitude, position.Longitude));
            }
            NativeMap.AddPolygon(polygonOptions);
        }
    }
}
```

`OnElementChanged` Метод получает коллекцию координаты широты и долготы из `CustomMap.ShapeCoordinates` свойство и сохраняет их в переменную-член. Затем он вызывает `MapView.GetMapAsync` метод, который возвращает базовый `GoogleMap` привязывается к представлению, при условии, что пользовательское средство отрисовки подключен к новому элементу Xamarin.Forms. Один раз `GoogleMap` экземпляр доступен, `OnMapReady` вызывается метод, где многоугольника создается путем создания экземпляра `PolygonOptions` , указывающий широту и долготу каждой точки. Многоугольник затем добавляется к схеме, вызвав `NativeMap.AddPolygon` метод. Этот метод автоматически закрывает многоугольника, нарисовав линию, соединяющую начальную и конечную точки.

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Создание пользовательского средства визуализации на платформе универсальных Windows

Создать подкласс `MapRenderer` класса и переопределять его `OnElementChanged` метод для добавления многоугольника наложения:

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
                foreach (var position in formsMap.ShapeCoordinates)
                {
                    coordinates.Add(new BasicGeoposition() { Latitude = position.Latitude, Longitude = position.Longitude });
                }

                var polygon = new MapPolygon();
                polygon.FillColor = Windows.UI.Color.FromArgb(128, 255, 0, 0);
                polygon.StrokeColor = Windows.UI.Color.FromArgb(128, 0, 0, 255);
                polygon.StrokeThickness = 5;
                polygon.Path = new Geopath(coordinates);
                nativeMap.MapElements.Add(polygon);
            }
        }
    }
}
```

Этот метод выполняет следующие операции, при условии, что пользовательское средство отрисовки подключен к новому элементу Xamarin.Forms:

- Коллекция координаты широты и долготы, извлекаются из `CustomMap.ShapeCoordinates` свойство и преобразовано в `List` из `BasicGeoposition` координаты.
- Многоугольник создается путем создания экземпляра `MapPolygon` объекта. `MapPolygon` Класс используется для отображения фигуры нескольких точек на карте, задав его `Path` свойства `Geopath` , содержащий координаты фигуры.
- Многоугольника отрисовывается на карте, добавив его `MapControl.MapElements` коллекции. Обратите внимание на то, что многоугольника автоматически закрывается, нарисовав линию, соединяющую начальную и конечную точки.

## <a name="summary"></a>Сводка

В этой статье описываются способы добавления наложения многоугольника на карту, чтобы выделить области карты. Многоугольники являются замкнутой фигуры и контактируют заполнено.


## <a name="related-links"></a>Связанные ссылки

- [Многоугольника карты наложения (пример)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/polygon/)
- [Настройка закрепления карты](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](xref:Xamarin.Forms.Maps)
