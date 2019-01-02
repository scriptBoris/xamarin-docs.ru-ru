---
title: Выделение региона на карте
description: В этой статье содержатся сведения о добавлении многоугольного наложения на карту для выделения на ней региона. Многоугольники являются замкнутыми фигурами с заполненными внутренними частями.
ms.prod: xamarin
ms.assetid: E79EB2CF-8DD6-44A8-B47D-5F0A94FB0A63
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 9859df6856f118dcfbc6bb4553b10882295866b2
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53057261"
---
# <a name="highlighting-a-region-on-a-map"></a>Выделение региона на карте

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/polygon/)

_В этой статье содержатся сведения о добавлении многоугольного наложения на карту для выделения на ней региона. Многоугольники являются замкнутыми фигурами с заполненными внутренними частями._

## <a name="overview"></a>Обзор

Наложение — это многослойное изображение на карте. Наложения позволяют рисовать графическое содержимое, которое масштабируется вместе с картой. На следующих снимках экрана показан результат добавления многоугольного наложения на карту:

![](polygon-map-overlay-images/screenshots.png)

Когда элемент управления [`Map`](xref:Xamarin.Forms.Maps.Map) отображается в приложении Xamarin.Forms, в iOS создается экземпляр класса `MapRenderer`, который, в свою очередь, создает собственный элемент управления `MKMapView`. На платформе Android класс `MapRenderer` создает собственный элемент управления `MapView`. На универсальной платформе Windows (UWP) класс `MapRenderer` создает собственный элемент управления `MapControl`. Процесс отрисовки можно использовать для реализации настроек карты для конкретных платформ путем создания пользовательского отрисовщика для `Map` на каждой платформе. Этот процесс выглядит следующим образом:

1. [Создание](#Creating_the_Custom_Map) пользовательской карты Xamarin.Forms.
1. [использование](#Consuming_the_Custom_Map) настраиваемой карты в Xamarin.Forms;
1. [настройка](#Customizing_the_Map) карты путем создания настраиваемого отрисовщика для нее на каждой платформе.

> [!NOTE]
> Перед использованием необходимо инициализировать и настроить [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps). Дополнительные сведения см. на веб-сайте [`Maps Control`](~/xamarin-forms/user-interface/map.md).

Сведения о настройке карты с помощью пользовательского отрисовщика см. в разделе [Настройка закрепления карты](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>Создание пользовательской карты

Создайте подкласс класса [`Map`](xref:Xamarin.Forms.Maps.Map), в котором добавлено свойство `ShapeCoordinates`:

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

В свойстве `ShapeCoordinates` будет храниться коллекция координат, определяющих выделяемый регион.

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
    customMap.ShapeCoordinates.Add (new Position (37.797513, -122.402058));
    customMap.ShapeCoordinates.Add (new Position (37.798433, -122.402256));
    customMap.ShapeCoordinates.Add (new Position (37.798582, -122.401071));
    customMap.ShapeCoordinates.Add (new Position (37.797658, -122.400888));

    customMap.MoveToRegion (MapSpan.FromCenterAndRadius (new Position (37.79752, -122.40183), Distance.FromMiles (0.1)));
  }
}
```

При инициализации задается ряд координат широты и долготы, определяющих выделяемый на карте регион. Затем представление карты позиционируется с помощью метода [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*). Он изменяет положение и масштаб карты, создавая объект [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) на основе [`Position`](xref:Xamarin.Forms.Maps.Position) и [`Distance`](xref:Xamarin.Forms.Maps.Distance).

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>Настройка карты

Теперь в каждый проект приложения необходимо добавить настраиваемый отрисовщик для добавления многоугольного наложения на карту.

#### <a name="creating-the-custom-renderer-on-ios"></a>Создание пользовательского отрисовщика в iOS

Создайте подкласс класса `MapRenderer` и переопределите его метод `OnElementChanged` для добавления многоугольного наложения:

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

Этот метод выполняет указанные ниже действия, если пользовательский отрисовщик подключен к новому элементу Xamarin.Forms.

- Свойству `MKMapView.OverlayRenderer` присваивается соответствующий делегат.
- Коллекция координат широты и долготы извлекается из свойства `CustomMap.ShapeCoordinates` и сохраняется как массив экземпляров `CLLocationCoordinate2D`.
- Многоугольник создается путем вызова статического метода `MKPolygon.FromCoordinates`, который задает широту и долготу каждой точки.
- Многоугольник добавляется на карту путем вызова метода `MKMapView.AddOverlay`. Этот метод автоматически замыкает многоугольник, соединяя первую и последнюю точки линией.

Затем реализуйте метод `GetOverlayRenderer` для настройки отрисовки наложения:

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

#### <a name="creating-the-custom-renderer-on-android"></a>Создание пользовательского отрисовщика в Android

Создайте подкласс класса `MapRenderer` и переопределите его методы `OnElementChanged` и `OnMapReady` для добавления многоугольного наложения:

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

Метод `OnElementChanged` извлекает коллекцию координат широты и долготы из свойства `CustomMap.ShapeCoordinates` и сохраняет ее в переменной-члене. Затем он вызывает метод `MapView.GetMapAsync`, который получает базовую карту `GoogleMap`, связанную с представлением, если пользовательский отрисовщик подключен к новому элементу Xamarin.Forms. Когда экземпляр `GoogleMap` становится доступен, вызывается метод `OnMapReady`, который создает многоугольник путем создания экземпляра `PolygonOptions`, определяющего широту и долготу каждой точки. После этого многоугольник добавляется на карту путем вызова метода `NativeMap.AddPolygon`. Этот метод автоматически замыкает многоугольник, соединяя первую и последнюю точки линией.

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Создание пользовательского отрисовщика на универсальной платформе Windows

Создайте подкласс класса `MapRenderer` и переопределите его метод `OnElementChanged` для добавления многоугольного наложения:

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

Этот метод выполняет указанные ниже операции, если пользовательский отрисовщик подключен к новому элементу Xamarin.Forms.

- Коллекция координат широты и долготы извлекается из свойства `CustomMap.ShapeCoordinates` и преобразуется в список `List` координат `BasicGeoposition`.
- Многоугольник создается путем создания объекта `MapPolygon`. Класс `MapPolygon` используется для отображения многоугольной фигуры на карте. Для этого его свойству `Path` присваивается объект `Geopath`, содержащий координаты фигуры.
- Многоугольник отображается на карте путем его добавления в коллекцию `MapControl.MapElements`. Имейте в виду, что многоугольник замыкается автоматически путем соединения первой и последней точек линией.

## <a name="summary"></a>Сводка

В этой статье были представлены сведения о добавлении многоугольного наложения на карту для выделения на ней региона. Многоугольники являются замкнутыми фигурами с заполненными внутренними частями.


## <a name="related-links"></a>Связанные ссылки

- [Многоугольное наложение на карте (пример)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/polygon/)
- [Настройка закрепления карты](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](xref:Xamarin.Forms.Maps)
