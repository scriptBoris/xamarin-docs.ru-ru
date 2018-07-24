---
title: Настройка закрепления карты
description: В этой статье показано, как создать пользовательское средство отрисовки для элемента управления картой, отображающий карту собственного настраиваемого ПИН-код и настраиваемое представление данных ПИН-код на каждой платформе.
ms.prod: xamarin
ms.assetid: C5481D86-80E9-4E3D-9FB6-57B0F93711A6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 351119a8b0089f78d4ce98729a1516c3cd7bae7b
ms.sourcegitcommit: 4c0093ee5d4aeb16c0e6f0c740c4796736971651
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39203089"
---
# <a name="customizing-a-map-pin"></a>Настройка закрепления карты

_В этой статье показано, как создать пользовательское средство отрисовки для элемента управления картой, отображающий карту собственного настраиваемого ПИН-код и настраиваемое представление данных ПИН-код на каждой платформе._

Каждое представление Xamarin.Forms имеет сопутствующий модуль подготовки отчетов для каждой платформы, который создает экземпляр собственного элемента управления. Когда [ `Map` ](xref:Xamarin.Forms.Maps.Map) отображается в приложении Xamarin.Forms в iOS, `MapRenderer` создается экземпляр класса, который в свою очередь создает экземпляр собственного `MKMapView` элемента управления. На платформе Android `MapRenderer` класс создает экземпляр собственного `MapView` элемента управления. В универсальной платформы Windows (UWP), `MapRenderer` класс создает экземпляр собственного `MapControl`. Дополнительные сведения о визуализации и классы собственного элемента управления, которые сопоставляются с элементами управления Xamarin.Forms, см. в разделе [модуль подготовки отчетов базовые классы и собственные элементы управления](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

На следующей схеме показана связь между [ `Map` ](xref:Xamarin.Forms.Maps.Map) и соответствующие собственные элементы управления, которые реализуют его:

![](customized-pin-images/map-classes.png "Связь между элементом управления карты и реализации собственных элементов управления")

Процесс подготовки отчета можно использовать для реализации настроек конкретных платформ путем создания пользовательского средства визуализации для [ `Map` ](xref:Xamarin.Forms.Maps.Map) на каждой платформе. Таким образом процесс выглядит следующим образом:

1. [Создание](#Creating_the_Custom_Map) пользовательской карты Xamarin.Forms.
1. [Использовать](#Consuming_the_Custom_Map) пользовательских карт с помощью Xamarin.Forms.
1. [Создание](#Creating_the_Custom_Renderer_on_each_Platform) пользовательское средство отрисовки карты на каждой платформе.

Каждый элемент теперь обсуждаются в свою очередь, для реализации `CustomMap` модуля подготовки отчетов, отображающий собственного карту с помощью настраиваемых ПИН-код и настраиваемое представление данных ПИН-код на каждой платформе.

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) необходимо инициализировать и настроить перед использованием. Дополнительные сведения см. на веб-сайте [`Maps Control`](~/xamarin-forms/user-interface/map.md).

<a name="Creating_the_Custom_Map" />

## <a name="creating-the-custom-map"></a>Создание пользовательской карты

Элемент управления пользовательской карты могут создаваться путем создания подклассов [ `Map` ](xref:Xamarin.Forms.Maps.Map) класса, как показано в следующем примере кода:

```csharp
public class CustomMap : Map
{
  public List<CustomPin> CustomPins { get; set; }
}
```

`CustomMap` Управления создается в проекте библиотеки .NET Standard и определяет API для пользовательской карты. Пользовательская карта предоставляет `CustomPins` свойство, которое представляет коллекцию `CustomPin` объектов, отображаемых элементом управления собственного карты на каждой платформе. `CustomPin` Класс показан в следующем примере кода:

```csharp
public class CustomPin : Pin
{
  public string Url { get; set; }
}
```

Этот класс определяет `CustomPin` как наследование свойств [ `Pin` ](xref:Xamarin.Forms.Maps.Pin) класса и добавления `Url` свойство.

<a name="Consuming_the_Custom_Map" />

## <a name="consuming-the-custom-map"></a>Использование пользовательской карты

`CustomMap` Управления можно ссылаться в XAML в проекте библиотеки .NET Standard, объявление пространства имен для его расположения и используя префикс пространства имен в элементе управления пользовательской карты. В следующем примере кода показано как `CustomMap` управления могут использоваться страницы XAML:

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer">
    <ContentPage.Content>
        <local:CustomMap x:Name="myMap" MapType="Street"
          WidthRequest="{x:Static local:App.ScreenWidth}"
          HeightRequest="{x:Static local:App.ScreenHeight}" />
    </ContentPage.Content>
</ContentPage>
```

`local` Префикс пространства имен может быть присвоено любое имя. Тем не менее `clr-namespace` и `assembly` значения должны совпадать сведения о пользовательской карты. После объявления пространства имен, префикс используется для ссылки на пользовательской карты.

В следующем примере кода показано как `CustomMap` управления могут использоваться страницей C#:

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

`CustomMap` Экземпляр будет использоваться для отображения собственного карты на каждой платформе. Он имеет [ `MapType` ](xref:Xamarin.Forms.Maps.Map.MapType) свойство задает стиль отображения [ `Map` ](xref:Xamarin.Forms.Maps.Map), с возможными значениями, указанных в [ `MapType` ](xref:Xamarin.Forms.Maps.MapType) перечисления. Для iOS и Android, ширину и высоту карты задается с помощью свойства `App` класс, инициализируются в проекты под конкретные платформы.

Расположение карты и ПИН-кодов, он содержит, инициализируются, как показано в следующем примере кода:

```csharp
public MapPage ()
{
  ...
  var pin = new CustomPin {
    Type = PinType.Place,
    Position = new Position (37.79752, -122.40183),
    Label = "Xamarin San Francisco Office",
    Address = "394 Pacific Ave, San Francisco CA",
    Id = "Xamarin",
    Url = "http://xamarin.com/about/"
  };

  customMap.CustomPins = new List<CustomPin> { pin };
  customMap.Pins.Add (pin);
  customMap.MoveToRegion (MapSpan.FromCenterAndRadius (
    new Position (37.79752, -122.40183), Distance.FromMiles (1.0)));
}
```

Эта инициализация добавляет пользовательский ПИН-код и помещает представление карты с [ `MoveToRegion` ](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) метод, который изменяет положение и уровень масштаба карты, создав [ `MapSpan` ](xref:Xamarin.Forms.Maps.MapSpan) из [ `Position` ](xref:Xamarin.Forms.Maps.Position) и [ `Distance` ](xref:Xamarin.Forms.Maps.Distance).

Пользовательское средство отрисовки теперь могут добавляться в каждый проект приложения для настройки собственного карты элементов управления.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Создание пользовательского средства визуализации на каждой платформе

Процесс создания класса пользовательского средства визуализации выглядит следующим образом:

1. Создать подкласс `MapRenderer` класс, который выполняет визуализацию пользовательской карты.
1. Переопределить `OnElementChanged` метода, который отображает пользовательской карты и написания логики для его настройки. Этот метод вызывается, когда создается соответствующий пользовательской карты Xamarin.Forms.
1. Добавление `ExportRenderer` атрибут класс пользовательского средства визуализации, чтобы указать, что он будет использоваться для отрисовки пользовательской карты Xamarin.Forms. Этот атрибут используется для регистрации пользовательского средства визуализации с помощью Xamarin.Forms.

> [!NOTE]
> Это необязательно для предоставления пользовательского средства визуализации в каждом проекте платформы. Если не зарегистрировано пользовательское средство отрисовки, то будет использоваться модуль подготовки отчетов по умолчанию для базового класса элемента управления.

На следующей схеме показана обязанности каждого проекта в примере приложения, а также связи между ними:

![](customized-pin-images/solution-structure.png "Обязанности проекта CustomMap пользовательский модуль подготовки отчетов")

`CustomMap` Элемент управления отрисовывается классами платформы модуля подготовки отчетов, которые являются производными от `MapRenderer` класс для каждой платформы. Это приводит к каждой `CustomMap` управления, отображаемого с элементами управления платформы, как показано на следующем снимке экрана:

![](customized-pin-images/screenshots.png "CustomMap на каждой платформе")

`MapRenderer` Предоставляет `OnElementChanged` метод, который вызывается при создании пользовательской карты Xamarin.Forms для отрисовки соответствующего собственного элемента управления. Этот метод принимает `ElementChangedEventArgs` параметр, содержащий `OldElement` и `NewElement` свойства. Эти свойства представляют собой элемент Xamarin.Forms, модуль подготовки отчетов *был* присоединен и элемент Xamarin.Forms, модуль подготовки отчетов *является* подключен, соответственно. В примере приложения `OldElement` свойство будет иметь `null` и `NewElement` свойство будет содержать ссылку на `CustomMap` экземпляра.

Переопределенная версия `OnElementChanged` метод в каждом классе платформы модуль подготовки отчетов используется, выполните настройку собственного элемента управления. Типизированную ссылку на собственный элемент управления, используется на платформе может осуществляться через `Control` свойство. Кроме того, можно получить ссылку на элемент управления Xamarin.Forms, который готовится к просмотру с помощью `Element` свойство.

Будьте осторожны при подписке на обработчики событий в `OnElementChanged` метод, как показано в следующем примере кода:

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<Xamarin.Forms.ListView> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null) {
    // Unsubscribe from event handlers
  }

  if (e.NewElement != null) {
    // Configure the native control and subscribe to event handlers
  }
}
```

Собственный элемент управления должен быть настроен и подписывать на обработчики событий только в том случае, если пользовательское средство отрисовки подключен к новому элементу Xamarin.Forms. Аналогичным образом все обработчики событий, которые были созданы подписки следует отменять подписку на только а также при изменении элемента, к которой подключен модуль подготовки отчетов. Этот подход поможет вам создать пользовательское средство отрисовки, не страдает от утечек памяти.

Каждый класс пользовательского средства визуализации снабжен `ExportRenderer` атрибут, который регистрирует модуль подготовки отчетов с помощью Xamarin.Forms. Атрибут принимает два параметра — имя пользовательского элемента управления Xamarin.Forms визуализируемого типа и имя типа пользовательского модуля подготовки отчетов. `assembly` Префикс в атрибут указывает, что атрибут применяется ко всей сборке.

В следующих разделах рассматриваются реализацию каждого класса пользовательского средства визуализации для конкретной платформы.

### <a name="creating-the-custom-renderer-on-ios"></a>Создание пользовательского средства визуализации на iOS

Далее на снимках экрана Показать эту карту, до и после настройки:

![](customized-pin-images/map-layout-ios.png "До и после настройки элемента управления картой")

В iOS ПИН-код вызывается *заметки*, и можно использовать пользовательский образ или ПИН-код системный разных цветов. Заметки можно включить вывод *выноски*, которое отображается в ответ на пользователя, выбирающего заметки. Отображает выноски `Label` и `Address` свойства `Pin` экземпляра, с необязательным слева и справа аксессуаров представлениями. На снимке экрана выше, левым представлением периферийных устройств — это образ monkey прямо аксессуаров представления, — *сведения* кнопки.

В следующем примере кода показано пользовательское средство отрисовки для платформы iOS:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace CustomRenderer.iOS
{
    public class CustomMapRenderer : MapRenderer
    {
        UIView customPinView;
        List<CustomPin> customPins;

        protected override void OnElementChanged(ElementChangedEventArgs<View> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null) {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null) {
                    nativeMap.RemoveAnnotations(nativeMap.Annotations);
                    nativeMap.GetViewForAnnotation = null;
                    nativeMap.CalloutAccessoryControlTapped -= OnCalloutAccessoryControlTapped;
                    nativeMap.DidSelectAnnotationView -= OnDidSelectAnnotationView;
                    nativeMap.DidDeselectAnnotationView -= OnDidDeselectAnnotationView;
                }
            }

            if (e.NewElement != null) {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;
                customPins = formsMap.CustomPins;

                nativeMap.GetViewForAnnotation = GetViewForAnnotation;
                nativeMap.CalloutAccessoryControlTapped += OnCalloutAccessoryControlTapped;
                nativeMap.DidSelectAnnotationView += OnDidSelectAnnotationView;
                nativeMap.DidDeselectAnnotationView += OnDidDeselectAnnotationView;
            }
        }
        ...
    }
}
```

`OnElementChanged` Следующая конфигурация выполняет метод [ `MKMapView` ](https://developer.xamarin.com/api/type/MapKit.MKMapView/) экземпляра, при условии, что пользовательское средство отрисовки подключен к новому элементу Xamarin.Forms:

- [ `GetViewForAnnotation` ](https://developer.xamarin.com/api/property/MapKit.MKMapView.GetViewForAnnotation/) Свойству `GetViewForAnnotation` метод. Этот метод вызывается, когда [расположение заметки становится видимым на карте](#Displaying_the_Annotation)и используется для настройки предварительного заметки для отображения.
- Обработчики событий для `CalloutAccessoryControlTapped`, `DidSelectAnnotationView`, и `DidDeselectAnnotationView` их регистрации. Эти события возникают при пользователь [касается правой периферийного устройства на этой выноске](#Tapping_on_the_Right_Callout_Accessory_View)и когда пользователь [выбирает](#Selecting_the_Annotation) и [отменяет выделение](#Deselecting_the_Annotation) заметки, соответственно. События, отменена, только в том случае, когда элемент модуль подготовки отчетов с подключенным отрисовщиком.

<a name="Displaying_the_Annotation" />

#### <a name="displaying-the-annotation"></a>Отображение заметки

`GetViewForAnnotation` Метод вызывается, когда расположение заметки становится видимым на карте и используется для настройки предварительного заметки для отображения. Заметки состоит из двух частей:

- `MkAnnotation` — включает в себя заголовок, подзаголовок и расположение заметки.
- `MkAnnotationView` — содержит изображение для представления заметки и при необходимости выноски, которое отображается, когда пользователь касается заметки.

`GetViewForAnnotation` Метод принимает `IMKAnnotation` , содержащий заметки данных и возвращает `MKAnnotationView` для отображения на карте и показано в следующем примере кода:

```csharp
protected override MKAnnotationView GetViewForAnnotation(MKMapView mapView, IMKAnnotation annotation)
{
    MKAnnotationView annotationView = null;

    if (annotation is MKUserLocation)
        return null;

    var customPin = GetCustomPin(annotation as MKPointAnnotation);
    if (customPin == null) {
        throw new Exception("Custom pin not found");
    }

    annotationView = mapView.DequeueReusableAnnotation(customPin.Id.ToString());
    if (annotationView == null) {
        annotationView = new CustomMKAnnotationView(annotation, customPin.Id.ToString());
        annotationView.Image = UIImage.FromFile("pin.png");
        annotationView.CalloutOffset = new CGPoint(0, 0);
        annotationView.LeftCalloutAccessoryView = new UIImageView(UIImage.FromFile("monkey.png"));
        annotationView.RightCalloutAccessoryView = UIButton.FromType(UIButtonType.DetailDisclosure);
        ((CustomMKAnnotationView)annotationView).Id = customPin.Id.ToString();
        ((CustomMKAnnotationView)annotationView).Url = customPin.Url;
    }
    annotationView.CanShowCallout = true;

    return annotationView;
}
```

Этот метод гарантирует, что заметка будет отображаться как пользовательский образ, а не как системный ПИН-код и что шифрованию заметки выноска будет отображаться, включает в себя дополнительное содержимое, слева и справа от заголовка заметки и адрес . Это выполняется следующим образом:

1. `GetCustomPin` Метод вызывается для возвращения данных пользовательского ПИН-код для заметки.
1. Для экономии памяти, представление заметки заносится в пул для повторного использования при вызове [ `DequeueReusableAnnotation` ](https://developer.xamarin.com/api/member/MapKit.MKMapView.DequeueReusableAnnotation/(System.String)/).
1. `CustomMKAnnotationView` Класс расширяет `MKAnnotationView` класса `Id` и `Url` свойства, которые соответствуют идентичны свойствам в `CustomPin` экземпляра. Новый экземпляр класса `CustomMKAnnotationView` создания условии, что заметка `null`:
    - `CustomMKAnnotationView.Image` Свойству образ, который будет представлять заметки на карте.
    - `CustomMKAnnotationView.CalloutOffset` Свойству `CGPoint` , указывающий, что будет центрировано по выноски выше заметки.
    - `CustomMKAnnotationView.LeftCalloutAccessoryView` Свойству образ monkey, которое будет отображаться слева от заметки название и адрес.
    - `CustomMKAnnotationView.RightCalloutAccessoryView` Свойству *сведения* кнопки, которое будет отображаться справа от заголовка заметки и адрес.
    - `CustomMKAnnotationView.Id` Свойству `CustomPin.Id` свойства, возвращенное `GetCustomPin` метод. Это позволяет заметки веб-API, чтобы он включал [выноски можно выполнить дальнейшую настройку](#Selecting_the_Annotation)при необходимости.
    - `CustomMKAnnotationView.Url` Свойству `CustomPin.Url` свойства, возвращенное `GetCustomPin` метод. URL-адрес будет открыт при пользователь [нажимает кнопку в представлении аксессуаров правой выноски](#Tapping_on_the_Right_Callout_Accessory_View).
1. [ `MKAnnotationView.CanShowCallout` ](https://developer.xamarin.com/api/property/MapKit.MKAnnotationView.CanShowCallout/) Свойству `true` так что выноски отображается при нажатии заметки.
1. Заметка возвращается для отображения на карте.

<a name="Selecting_the_Annotation" />

#### <a name="selecting-the-annotation"></a>Выбрав заметки

Когда пользователь касается заметку, `DidSelectAnnotationView` вызывает событие, которые в свою очередь выполняет `OnDidSelectAnnotationView` метод:

```csharp
void OnDidSelectAnnotationView (object sender, MKAnnotationViewEventArgs e)
{
  var customView = e.View as CustomMKAnnotationView;
  customPinView = new UIView ();

  if (customView.Id == "Xamarin") {
    customPinView.Frame = new CGRect (0, 0, 200, 84);
    var image = new UIImageView (new CGRect (0, 0, 200, 84));
    image.Image = UIImage.FromFile ("xamarin.png");
    customPinView.AddSubview (image);
    customPinView.Center = new CGPoint (0, -(e.View.Frame.Height + 75));
    e.View.AddSubview (customPinView);
  }
}
```

Этот метод расширяет существующие выноски (который содержит представления слева и справа периферийных устройств), добавляя `UIView` экземпляр, содержащий изображение логотипа Xamarin, при условии, что выбранный аннотация содержит его `Id` свойство присвоено `Xamarin`. Это позволяет выполнять сценарии, где для другие примечания могут отображаться различные выноски. `UIView` Экземпляра будет отображаться по центру над существующей выноски.

<a name="Tapping_on_the_Right_Callout_Accessory_View" />

#### <a name="tapping-on-the-right-callout-accessory-view"></a>При нажатии кнопки в представлении правой выноски периферийных устройств

Когда пользователь нажимает на *сведения* кнопку в представлении аксессуаров правой выноски `CalloutAccessoryControlTapped` вызывает событие, которые в свою очередь выполняет `OnCalloutAccessoryControlTapped` метод:

```csharp
void OnCalloutAccessoryControlTapped (object sender, MKMapViewAccessoryTappedEventArgs e)
{
  var customView = e.View as CustomMKAnnotationView;
  if (!string.IsNullOrWhiteSpace (customView.Url)) {
    UIApplication.SharedApplication.OpenUrl (new Foundation.NSUrl (customView.Url));
  }
}
```

Этот метод открывает веб-браузер и переходит к адресу, сохраненному в `CustomMKAnnotationView.Url` свойство. Обратите внимание, что адрес был определен при создании `CustomPin` коллекции в проекте библиотеки .NET Standard.

<a name="Deselecting_the_Annotation" />

#### <a name="deselecting-the-annotation"></a>Отмена выбора заметки

Если заметка отображается и пользователь нажимает на карте, `DidDeselectAnnotationView` вызывает событие, которые в свою очередь выполняет `OnDidDeselectAnnotationView` метод:

```csharp
void OnDidDeselectAnnotationView (object sender, MKAnnotationViewEventArgs e)
{
  if (!e.View.Selected) {
    customPinView.RemoveFromSuperview ();
    customPinView.Dispose ();
    customPinView = null;
  }
}
```

Этот метод гарантирует, что при существующих выноски не выбран, в расширенных части выноски (изображение логотипа Xamarin) также прекращает отображение и его ресурсы будут освобождены.

Дополнительные сведения о настройке `MKMapView` экземпляра, см. в разделе [iOS Maps](~/ios/user-interface/controls/ios-maps/index.md).

### <a name="creating-the-custom-renderer-on-android"></a>Создание пользовательского средства визуализации на устройстве Android

Далее на снимках экрана Показать эту карту, до и после настройки:

![](customized-pin-images/map-layout-android.png "До и после настройки элемента управления картой")

В Android ПИН-код вызывается *маркер*, и может быть пользовательский образ или системный маркер разных цветов. Может показывать маркеры *информационное окно*, которое отображается в ответ на пользователя, коснувшись к маркеру. Отображает информационное окно `Label` и `Address` свойства `Pin` экземпляром и можно было настроить для включения другого содержимого. Тем не менее только одно окно информация может отображаться за один раз.

В следующем примере кода показано пользовательское средство отрисовки для платформы Android:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace CustomRenderer.Droid
{
    public class CustomMapRenderer : MapRenderer, GoogleMap.IInfoWindowAdapter
    {
        List<CustomPin> customPins;

        public CustomMapRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(Xamarin.Forms.Platform.Android.ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                NativeMap.InfoWindowClick -= OnInfoWindowClick;
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                customPins = formsMap.CustomPins;
                Control.GetMapAsync(this);
            }
        }

        protected override void OnMapReady(GoogleMap map)
        {
            base.OnMapReady(map);

            NativeMap.InfoWindowClick += OnInfoWindowClick;
            NativeMap.SetInfoWindowAdapter(this);
        }
        ...
    }
}
```

При условии, что пользовательское средство отрисовки подключен к новому элементу Xamarin.Forms, `OnElementChanged` вызовы методов `MapView.GetMapAsync` метод, который возвращает базовый `GoogleMap` , привязывается к представлению. Один раз `GoogleMap` экземпляр доступен, `OnMapReady` переопределение будет вызываться. Этот метод регистрирует обработчик событий для `InfoWindowClick` событий, который срабатывает при [окно сведений о нажатии](#Clicking_on_the_Info_Window)и не отменена только в том случае, если элемента с подключенным отрисовщиком. `OnMapReady` Также переопределить вызовы `SetInfoWindowAdapter` метод, чтобы указать, что `CustomMapRenderer` экземпляр класса будет предоставлять методы для настройки информационное окно.

`CustomMapRenderer` Класс реализует `GoogleMap.IInfoWindowAdapter` интерфейс [настроить информационное окно](#Customizing_the_Info_Window). Этот интерфейс указывает, что должны быть реализованы следующие методы:

- `public Android.Views.View GetInfoWindow(Marker marker)` — Этот метод вызывается для возврата окна пользовательские сведения для маркера. Если он возвращает `null`, отрисовка окна по умолчанию будет использоваться. Если он возвращает `View`, затем, `View` будет помещены в рамке окна сведений.
- `public Android.Views.View GetInfoContents(Marker marker)` — Этот метод вызывается для возврата `View` содержимым окна сведения и будет вызываться только если `GetInfoWindow` возвращает метод `null`. Если он возвращает `null`, будет использоваться по умолчанию отрисовку содержимого окна сведений.

В приложении-примере настроено только содержимое окна сведений и поэтому `GetInfoWindow` возвращает метод `null` соответствующим образом.

#### <a name="customizing-the-marker"></a>Настройка маркера

Значок, используемый для представления маркера можно настроить путем вызова `MarkerOptions.SetIcon` метод. Это можно сделать путем переопределения `CreateMarker` метод, который вызывается для каждого `Pin` добавляется к схеме:

```csharp
protected override MarkerOptions CreateMarker(Pin pin)
{
    var marker = new MarkerOptions();
    marker.SetPosition(new LatLng(pin.Position.Latitude, pin.Position.Longitude));
    marker.SetTitle(pin.Label);
    marker.SetSnippet(pin.Address);
    marker.SetIcon(BitmapDescriptorFactory.FromResource(Resource.Drawable.pin));
    return marker;
}
```

Этот метод создает новую `MarkerOption` экземпляра для каждого `Pin` экземпляр. После задания позиции, меток и адрес маркера, его значок, заданный параметром `SetIcon` метод. Этот метод принимает `BitmapDescriptor` объект, содержащий данные, необходимые для подготовки к просмотру, значка с `BitmapDescriptorFactory` класс, предоставляющий вспомогательные методы для упрощения создания `BitmapDescriptor`.

Дополнительные сведения об использовании `BitmapDescriptorFactory` класс для настройки маркер, см. в разделе [Настройка маркера](~/android/platform/maps-and-location/maps/maps-api.md).

<a name="Customizing_the_Info_Window" />

#### <a name="customizing-the-info-window"></a>Настройка окна сведений

При касании к маркеру `GetInfoContents` выполняется метод, при условии, что `GetInfoWindow` возвращает метод `null`. В следующем коде показано в примере `GetInfoContents` метод:

```csharp
public Android.Views.View GetInfoContents (Marker marker)
{
  var inflater = Android.App.Application.Context.GetSystemService (Context.LayoutInflaterService) as Android.Views.LayoutInflater;
  if (inflater != null) {
    Android.Views.View view;

    var customPin = GetCustomPin (marker);
    if (customPin == null) {
      throw new Exception ("Custom pin not found");
    }

    if (customPin.Id.ToString() == "Xamarin") {
      view = inflater.Inflate (Resource.Layout.XamarinMapInfoWindow, null);
    } else {
      view = inflater.Inflate (Resource.Layout.MapInfoWindow, null);
    }

    var infoTitle = view.FindViewById<TextView> (Resource.Id.InfoWindowTitle);
    var infoSubtitle = view.FindViewById<TextView> (Resource.Id.InfoWindowSubtitle);

    if (infoTitle != null) {
      infoTitle.Text = marker.Title;
    }
    if (infoSubtitle != null) {
      infoSubtitle.Text = marker.Snippet;
    }

    return view;
  }
  return null;
}
```

Этот метод возвращает `View` информационное окно с содержимым. Это выполняется следующим образом:

- Объект `LayoutInflater` извлекается экземпляр. Это используется для создания XML-файлом макета в его соответствующий `View`.
- `GetCustomPin` Метод вызывается для возвращения данных пользовательского ПИН-код для окна сведений.
- `XamarinMapInfoWindow` Макета увеличивается в том случае, если `CustomPin.Id` равно `Xamarin`. В противном случае `MapInfoWindow` расширяется на макет. Это позволяет использовать сценарии, где макетов окон измененными данными может быть отображена для различных меток.
- `InfoWindowTitle` И `InfoWindowSubtitle` ресурсы извлекаются из увеличенную макет и их `Text` свойствам присваиваются соответствующие данные из `Marker` экземпляра, при условии, что ресурсы не находятся в `null`.
- `View` Возвращается экземпляр для отображения на карте.

> [!NOTE]
> Информационное окно не является активной `View`. Вместо этого будет преобразовать Android `View` задается статическое растрового изображения и вывести их в виде изображения. Это означает, что при информационное окно может отвечать на событие щелчка, он не может отвечать на любые события касания и жесты, и отдельные элементы управления в окне «сведения» не может отвечать на свои собственные события щелчка мыши.

<a name="Clicking_on_the_Info_Window" />

#### <a name="clicking-on-the-info-window"></a>Щелкнув информационное окно

Когда пользователь щелкает в окне «сведения» `InfoWindowClick` вызывает событие, которые в свою очередь выполняет `OnInfoWindowClick` метод:

```csharp
void OnInfoWindowClick (object sender, GoogleMap.InfoWindowClickEventArgs e)
{
  var customPin = GetCustomPin (e.Marker);
  if (customPin == null) {
    throw new Exception ("Custom pin not found");
  }

  if (!string.IsNullOrWhiteSpace (customPin.Url)) {
    var url = Android.Net.Uri.Parse (customPin.Url);
    var intent = new Intent (Intent.ActionView, url);
    intent.AddFlags (ActivityFlags.NewTask);
    Android.App.Application.Context.StartActivity (intent);
  }
}
```

Этот метод открывает веб-браузер и переходит к адресу, сохраненному в `Url` из извлеченного `CustomPin` для экземпляра `Marker`. Обратите внимание, что адрес был определен при создании `CustomPin` коллекции в проекте библиотеки .NET Standard.

Дополнительные сведения о настройке `MapView` экземпляра, см. в разделе [API для карт](~/android/platform/maps-and-location/maps/maps-api.md).

### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Создание пользовательского средства визуализации на платформе универсальных Windows

Далее на снимках экрана Показать эту карту, до и после настройки:

![](customized-pin-images/map-layout-uwp.png "До и после настройки элемента управления картой")

Для универсальной платформы Windows вызывается ПИН-код *значок схемы*, и может быть пользовательский образ или образ по умолчанию, определенная системой. Значок схемы можно показать `UserControl`, которое отображается в ответ на пользователя, нажав на значок карты. `UserControl` Можно отобразить любое содержимое, включая `Label` и `Address` свойства `Pin` экземпляра.

В следующем примере кода показано пользовательское средство отрисовки универсальной платформы Windows:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace CustomRenderer.UWP
{
    public class CustomMapRenderer : MapRenderer
    {
        MapControl nativeMap;
        List<CustomPin> customPins;
        XamarinMapOverlay mapOverlay;
        bool xamarinOverlayShown = false;

        protected override void OnElementChanged(ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                nativeMap.MapElementClick -= OnMapElementClick;
                nativeMap.Children.Clear();
                mapOverlay = null;
                nativeMap = null;
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                nativeMap = Control as MapControl;
                customPins = formsMap.CustomPins;

                nativeMap.Children.Clear();
                nativeMap.MapElementClick += OnMapElementClick;

                foreach (var pin in customPins)
                {
                    var snPosition = new BasicGeoposition { Latitude = pin.Pin.Position.Latitude, Longitude = pin.Pin.Position.Longitude };
                    var snPoint = new Geopoint(snPosition);

                    var mapIcon = new MapIcon();
                    mapIcon.Image = RandomAccessStreamReference.CreateFromUri(new Uri("ms-appx:///pin.png"));
                    mapIcon.CollisionBehaviorDesired = MapElementCollisionBehavior.RemainVisible;
                    mapIcon.Location = snPoint;
                    mapIcon.NormalizedAnchorPoint = new Windows.Foundation.Point(0.5, 1.0);

                    nativeMap.MapElements.Add(mapIcon);                    
                }
            }
        }
        ...
    }
}
```

`OnElementChanged` Метод выполняет следующие операции, при условии, что пользовательское средство отрисовки подключен к новому элементу Xamarin.Forms:

- Он очищает `MapControl.Children` коллекции для удаления всех существующих элементов интерфейса пользователя из сопоставления, прежде чем регистрировать обработчики для событий `MapElementClick` событий. Это событие возникает, когда пользователь щелкает или касается его `MapElement` на `MapControl`и не отменена только в том случае, если элемента с подключенным отрисовщиком.
- Каждый ПИН-кода в `customPins` коллекция отображается в неправильном географическом расположении на карте, следующим образом:
  - Расположение для ПИН-код создается как `Geopoint` экземпляра.
  - Объект `MapIcon` создается экземпляр для представления ПИН-код.
  - Изображение, используемое для представления `MapIcon` задается путем присвоения `MapIcon.Image` свойство. Тем не менее значок карты изображения не всегда гарантированно будет показано, как может быть скрыт другими элементами на карте. Таким образом, карты значка `CollisionBehaviorDesired` свойству `MapElementCollisionBehavior.RemainVisible`, чтобы убедиться, что он остается видимой.
  - Расположение `MapIcon` задается путем присвоения `MapIcon.Location` свойство.
  - `MapIcon.NormalizedAnchorPoint` Свойству присваивается Приблизительное расположение указателя на изображении. Если это свойство сохраняет значение по умолчанию (0,0), который представляет в верхний левый угол изображения, изменения в уровень масштаба карты может привести изображения, указывающие в другое расположение.
  - `MapIcon` Добавляется экземпляр `MapControl.MapElements` коллекции. Это приводит к значок карты, отображаемая на `MapControl`.

> [!NOTE]
> При использовании одного образа для нескольких значков карты, `RandomAccessStreamReference` экземпляр должен быть объявлен на уровне страницы или приложения для повышения производительности.

#### <a name="displaying-the-usercontrol"></a>Отображение пользовательского элемента управления

При касании значок карты `OnMapElementClick` выполнения метода. В следующем примере кода показан этот метод.

```csharp
private void OnMapElementClick(MapControl sender, MapElementClickEventArgs args)
{
    var mapIcon = args.MapElements.FirstOrDefault(x => x is MapIcon) as MapIcon;
    if (mapIcon != null)
    {
        if (!xamarinOverlayShown)
        {
            var customPin = GetCustomPin(mapIcon.Location.Position);
            if (customPin == null)
            {
                throw new Exception("Custom pin not found");
            }

            if (customPin.Id.ToString() == "Xamarin")
            {
                if (mapOverlay == null)
                {
                    mapOverlay = new XamarinMapOverlay(customPin);
                }

                var snPosition = new BasicGeoposition { Latitude = customPin.Pin.Position.Latitude, Longitude = customPin.Pin.Position.Longitude };
                var snPoint = new Geopoint(snPosition);

                nativeMap.Children.Add(mapOverlay);
                MapControl.SetLocation(mapOverlay, snPoint);
                MapControl.SetNormalizedAnchorPoint(mapOverlay, new Windows.Foundation.Point(0.5, 1.0));
                xamarinOverlayShown = true;
            }
        }
        else
        {
            nativeMap.Children.Remove(mapOverlay);
            xamarinOverlayShown = false;
        }
    }
}
```

Этот метод создает `UserControl` экземпляр, который отображает сведения о ПИН-код. Это выполняется следующим образом:

- `MapIcon` Извлекается экземпляр.
- `GetCustomPin` Метод вызывается для возвращения данных пользовательского ПИН-код, который будет отображаться.
- Объект `XamarinMapOverlay` для отображения данных пользовательского ПИН-кода создается экземпляр. Этот класс представляет пользовательский элемент управления.
- Географическое расположение, в которой отображается `XamarinMapOverlay` экземпляра `MapControl` создается как `Geopoint` экземпляра.
- `XamarinMapOverlay` Добавляется экземпляр `MapControl.Children` коллекции. Эта коллекция содержит элементы пользовательского интерфейса XAML, которые будут отображаться на карте.
- Географическое расположение `XamarinMapOverlay` экземпляра на карте устанавливается путем вызова `SetLocation` метод.
- Относительное расположение на `XamarinMapOverlay` экземпляра, на котором соответствующее в указанное расположение, устанавливается путем вызова `SetNormalizedAnchorPoint` метод. Таким образом, изменения в уровень масштаба карты результата в `XamarinMapOverlay` экземпляр всегда отображается в правильном расположении.

Кроме того, если сведения о ПИН-код уже отображается на карте, коснувшись на карте удаляет `XamarinMapOverlay` экземпляра из `MapControl.Children` коллекции.

#### <a name="tapping-on-the-information-button"></a>Коснувшись кнопки «сведения»

Когда пользователь нажимает на *сведения* кнопку `XamarinMapOverlay` пользовательский элемент управления, `Tapped` вызывает событие, которые в свою очередь выполняет `OnInfoButtonTapped` метод:

```csharp
private async void OnInfoButtonTapped(object sender, TappedRoutedEventArgs e)
{
    await Launcher.LaunchUriAsync(new Uri(customPin.Url));
}
```

Этот метод открывает веб-браузер и переходит к адресу, сохраненному в `Url` свойство `CustomPin` экземпляра. Обратите внимание, что адрес был определен при создании `CustomPin` коллекции в проекте библиотеки .NET Standard.

Дополнительные сведения о настройке `MapControl` экземпляра, см. в разделе [карты и общие сведения о расположении](https://msdn.microsoft.com/library/windows/apps/mt219699.aspx) на сайте MSDN.

## <a name="summary"></a>Сводка

В этой статье было показано, как создать пользовательское средство отрисовки для `Map` элемента управления, что позволяет разработчикам переопределять способ отрисовки собственного по умолчанию с параметрами, свои собственные платформы. Xamarin.Forms.Maps предоставляет кросс платформенных абстракцию для отображения карт, которые используют собственные карты, возможности API-интерфейсы на каждой платформе, чтобы получить быстрое и знакомых карту для пользователей.


## <a name="related-links"></a>Связанные ссылки

- [Элемент управления Maps](~/xamarin-forms/user-interface/map.md)
- [iOS Maps](~/ios/user-interface/controls/ios-maps/index.md)
- [API карт](~/android/platform/maps-and-location/maps/maps-api.md)
- [Настраиваемые ПИН-кода (пример)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/pin/)
