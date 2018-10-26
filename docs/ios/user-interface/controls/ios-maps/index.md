---
title: Карты в Xamarin.iOS
description: В этом документе описывается платформа MapKit iOS и как она используется с помощью Xamarin.iOS. В этом примере рассматривается добавление карты, стиль, панорамирование и масштаб, работать с расположением пользователя, добавление заметок, работать с выносками и наложения и выполнения локального поиска.
ms.prod: xamarin
ms.assetid: 5DD8E56D-51C1-4AFA-B387-79B5734698ED
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: d83470db23b1376d18fa36c52c1daabaf68cfe0b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117763"
---
# <a name="maps-in-xamarinios"></a>Карты в Xamarin.iOS

Maps — это основная возможность во всех современных мобильных операционных системах. iOS поддерживает сопоставления в исходном формате через framework Map Kit. С помощью комплекта Map приложения можно легко добавить интерактивные карты. Эти карты можно настраивать различными способами, например Добавление заметки для обозначения расположений на карту и наложенной графики из произвольные формы. MAP Kit даже имеется встроенная поддержка для отображения текущего расположения устройства.

## <a name="adding-a-map"></a>Добавление схемы

Добавление карты к приложению осуществляется посредством добавления `MKMapView` экземпляра иерархию представлений, как показано ниже:

```csharp
// map is an MKMapView declared as a class variable
map = new MKMapView (UIScreen.MainScreen.Bounds);
View = map;
```

 `MKMapView` является `UIView` подкласс, в котором отображается карта. Простое добавление сопоставления, используя приведенный выше код создает интерактивную карту:

 ![](images/00-map.png "Пример карты")

## <a name="map-style"></a>Стиль карты

 `MKMapView` поддерживает 3 различных стилей карт. Чтобы применить стиль карты, просто установите `MapType` значение из свойства `MKMapType` перечисления:
 ```
map.MapType = MKMapType.Standard; //road map
map.MapType = MKMapType.Satellite;
map.MapType = MKMapType.Hybrid;
 ```
  Следующий снимок экрана: отображение стилей другой карте, доступны:

 ![](images/01-mapstyles.png "На этом снимке экрана отображаются разные карты стили, которые доступны")

## <a name="panning-and-zooming"></a>Панорамирования и масштабирования

 `MKMapView` включает поддержку карты интерактивные функции, такие как:

-  Изменение масштаба с помощью жестов жестом сжатия
-  Панорамирование с помощью жестов pan


Эти функции можно включить или отключить, просто задав `ZoomEnabled` и `ScrollEnabled` свойства `MKMapView` экземпляра, где значение по умолчанию имеет значение true, если для обоих. К примеру Чтобы вывести статической картой, просто задайте соответствующие свойства значение false.

```csharp
map.ZoomEnabled = false;
map.ScrollEnabled = false;
```

## <a name="user-location"></a>Расположение пользователя

В дополнение к взаимодействия с пользователем `MKMapView` также обладает встроенной поддержкой для отображения расположения устройства. Это делается с помощью *расположение Core* framework. Прежде чем можно получить доступ к расположению пользователя, должно предложить пользователю. Чтобы сделать это, создайте экземпляр `CLLocationManager` и вызвать `RequestWhenInUseAuthorization`.

```csharp
CLLocationManager locationManager = new CLLocationManager();
locationManager.RequestWhenInUseAuthorization();
//locationManager.RequestAlwaysAuthorization(); //requests permission for access to location data while running in the background
```

Обратите внимание, что в версиях iOS до 8.0, попытка вызова `RequestWhenInUseAuthorization` приведет к ошибке. Не забудьте проверить версию iOS до выполнения этого вызова, если планируется поддержка более ранних версий.

Доступ к расположению пользователя требует изменений в **Info.plist**. Необходимо задать следующие ключи, связанные с данными о расположении:

- **NSLocationWhenInUseUsageDescription** для доступа к расположению пользователя во время его взаимодействия с приложением.
- **NSLocationAlwaysUsageDescription** для доступа приложения к расположению пользователя в фоновом режиме.

Можно добавить эти ключи, открыв **Info.plist** и выбрав *источника* в нижней части редактора.

После обновления **Info.plist** и запросило у пользователя разрешение на доступ к их расположения, из местоположения пользователя можно показать на карте, задав `ShowsUserLocation` присваивается значение true:

```csharp
map.ShowsUserLocation = true;
```

 ![](images/02-location-alert.png "Предупреждение о доступе к расположение разрешить")
 
## <a name="annotations"></a>Заметки

 `MKMapView` также поддерживает отображение изображения, известные как заметки на карте. Это могут быть пользовательских образов или системный ПИН-кодов различных цветов. Например, на следующем рисунке показан карту с обоих ПИН-код и пользовательский образ:

 ![](images/03-annotations.png "На этом снимке экрана показана карта с обоих ПИН-кода и пользовательского образа")

### <a name="adding-an-annotation"></a>Добавление заметки

Заметки сам состоит из двух частей:

-  `MKAnnotation` Объекта, включая модели данных о заметки, например название и местонахождение заметки.
-  `MKAnnotationView` , Который содержит изображение, отображаемое и при необходимости выноски, которое отображается, когда пользователь касается заметки.


MAP Kit использует шаблон делегирования iOS, чтобы добавить заметки к схеме, где `Delegate` свойство `MKMapView` присваивается экземпляр `MKMapViewDelegate`. Это реализация этот делегат, который отвечает за возврат `MKAnnotationView` для заметки.

Чтобы добавить заметки, сначала заметки добавляется путем вызова `AddAnnotations` на `MKMapView` экземпляр:

```csharp
// add an annotation
map.AddAnnotations (new MKPointAnnotation (){
    Title="MyAnnotation",
    Coordinate = new CLLocationCoordinate2D (42.364260, -71.120824)
});
```

Когда расположение заметки становится видимым на карте, `MKMapView` вызовет его делегат `GetViewForAnnotation` метод для получения `MKAnnotationView` для отображения.

Например, следующий код возвращает предоставляемая системой `MKPinAnnotationView`:

```csharp
string pId = "PinAnnotation";

public override MKAnnotationView GetViewForAnnotation (MKMapView mapView, NSObject annotation)
{
    if (annotation is MKUserLocation)
        return null;

    // create pin annotation view
    MKAnnotationView pinView = (MKPinAnnotationView)mapView.DequeueReusableAnnotation (pId);

    if (pinView == null)
        pinView = new MKPinAnnotationView (annotation, pId);

    ((MKPinAnnotationView)pinView).PinColor = MKPinAnnotationColor.Red;
    pinView.CanShowCallout = true;

    return pinView;
}
```

### <a name="reusing-annotations"></a>Повторное использование заметок

Для экономии памяти, `MKMapView` позволяет представление заметки в пул для повторного использования, аналогично тому, как используются повторно ячеек таблицы. Получение представления заметки из пула выполняется с помощью вызова `DequeueReusableAnnotation`:

```csharp
MKAnnotationView pinView = (MKPinAnnotationView)mapView.DequeueReusableAnnotation (pId);
```

#### <a name="showing-callouts"></a>Отображение выноски

Как упоминалось ранее, заметки при необходимости можно показать выноски. Чтобы отобразить выноски, просто задайте `CanShowCallout` в значение true, если `MKAnnotationView`. Это приводит к заголовок заметки, отображаются заметки шифрованию, как показано:

 ![](images/04-callout.png "Отображение заголовка заметки")

### <a name="customizing-the-callout"></a>Настройка выноски

Также можно настроить выноски представления в левой и правой периферийных устройств, как показано ниже:

```csharp
pinView.RightCalloutAccessoryView = UIButton.FromType (UIButtonType.DetailDisclosure);
pinView.LeftCalloutAccessoryView = new UIImageView(UIImage.FromFile ("monkey.png"));
```

Этот код вызывает следующие выноски:

 ![](images/05-callout-accessories.png "Пример выноски")

Чтобы обрабатывать действия, коснувшись правой периферийных устройств, просто реализовать `CalloutAccessoryControlTapped` метод в `MKMapViewDelegate`:

```csharp
public override void CalloutAccessoryControlTapped (MKMapView mapView, MKAnnotationView view, UIControl control)
{
    ...
}
```

### <a name="overlays"></a>Наложения

Другой способ слой графических элементов на карте, — использовать наложение. Наложений поддерживает рисование графического содержимого, которые масштабируются вместе с карты, так как оно увеличивается. iOS поддерживает несколько типов наложения, включая:

-  Многоугольники - обычно используется для выделения некоторые области на карте.
-  Ломаные линии -, которая происходит при отображении маршрут.
-  Круги - используемым для выделения круговой области карты.


Кроме того можно создать специальные наложения для отображения произвольных геометрических объектов с помощью разделенные, настраиваемый код прорисовки. Например Лепестковая погоды будет хорошим кандидатом для пользовательского элемента управления overlay.

#### <a name="adding-an-overlay"></a>Добавление наложение

Как и заметки, добавление наложение состоит из 2 частей:

-  Создание объекта модели для наложения и добавления его в `MKMapView` .
-  Создание представления для наложения в `MKMapViewDelegate` .


Модель для наложения может быть любой `MKShape` подкласс. Включает Xamarin.iOS `MKShape` подклассы многоугольников, ломаные линии и круги, с помощью `MKPolygon`, `MKPolyline` и `MKCircle` классы соответственно.

Например, следующий код используется для добавления `MKCircle`:

```csharp
var circleOverlay = MKCircle.Circle (mapCenter, 1000);
map.AddOverlay (circleOverlay);
```

Это представление для наложения является `MKOverlayView` экземпляр, возвращаемый `GetViewForOverlay` в `MKMapViewDelegate`. Каждый `MKShape` имеет соответствующий `MKOverlayView` , знает, как отобразить заданной фигуры. Для `MKPolygon` есть `MKPolygonView`. Аналогичным образом `MKPolyline` соответствует `MKPolylineView`, а также для `MKCircle` есть `MKCircleView`.

Например, в следующем коде возвращает `MKCircleView` для `MKCircle`:

```csharp
public override MKOverlayView GetViewForOverlay (MKMapView mapView, NSObject overlay)
{
    var circleOverlay = overlay as MKCircle;
    var circleView = new MKCircleView (circleOverlay);
    circleView.FillColor = UIColor.Blue;
    return circleView;
}
```

Это отображается окружность на карте, как показано:

 ![](images/06-circle-overlay.png "Круг, отображаемых на карте")

## <a name="local-search"></a>Локального поиска

iOS включает локальный поиск API с помощью комплекта Map, позволяющий асинхронный поиск интересующих в конкретном географическом регионе.

Для выполнения поиска в локальной, приложения необходимо выполнить следующие действия:

1.  Создание `MKLocalSearchRequest` объекта.
1.  Создание `MKLocalSearch` объекта из `MKLocalSearchRequest` .
1.  Вызовите `Start` метод `MKLocalSearch` объекта.
1.  Получить `MKLocalSearchResponse` объекта в обратном вызове.


Локального поиска сам интерфейс API предоставляет пользовательский интерфейс отсутствует. Он даже не требует карту для использования. Тем не менее чтобы сделать практического использования локального поиска, приложение необходимо какой-нибудь способ, чтобы указать запрос поиска и отображения результатов. Кроме того, так как результаты, содержащие данные о местоположении, он будет часто смысла для их отображения на карте.

<a name="Adding_a_Local_Search_UI"/>

### <a name="adding-a-local-search-ui"></a>Добавление локального поиска пользовательского интерфейса

Один из способов, чтобы принимать входные данные для поиска — с `UISearchBar`, что предоставляемые `UISearchController` и отображает результаты в таблице.

Следующий код добавляет `UISearchController` (имеет свойство строке поиска) в `ViewDidLoad` метод `MapViewController`:

```csharp
//Creates an instance of a custom View Controller that holds the results
var searchResultsController = new SearchResultsViewController (map);

//Creates a search controller updater
var searchUpdater = new SearchResultsUpdator ();
searchUpdater.UpdateSearchResults += searchResultsController.Search;
            
//add the search controller
searchController = new UISearchController (searchResultsController) {
                SearchResultsUpdater = searchUpdater
            };

//format the search bar
searchController.SearchBar.SizeToFit ();
searchController.SearchBar.SearchBarStyle = UISearchBarStyle.Minimal;
searchController.SearchBar.Placeholder = "Enter a search query";

//the search bar is contained in the navigation bar, so it should be visible
searchController.HidesNavigationBarDuringPresentation = false;

//Ensure the searchResultsController is presented in the current View Controller 
DefinesPresentationContext = true;

//Set the search bar in the navigation bar
NavigationItem.TitleView = searchController.SearchBar;

```csharp
Note that you are responsible for incorporating the search bar object into the user interface. In this example, we assigned it to the TitleView of the navigation bar, but if you do not use a navigation controller in your application you will have to find another place to display it.

In this code snippet, we created another custom view controller – `searchResultsController` –  that displays the search results and then we used this object to create our search controller object. We also created a new search updater, which becomes active when the user interacts with the search bar. It receives notifications about searches with each keystroke and is responsible for updating the UI.
We will take a look at how to implement both the `searchResultsController` and the `searchResultsUpdater` later in this guide.

This results in a search bar displayed over the map as shown below:

 ![](images/07-searchbar.png "A search bar displayed over the map")
 


### Displaying the Search Results

To display search results, we need to create a custom View Controller; normally a `UITableViewController`. As shown above, the `searchResultsController` is passed to the constructor of the `searchController` when it is being created.
The following code is an example of how to create this custom View Controller:

```csharp
public class SearchResultsViewController : UITableViewController
{
    static readonly string mapItemCellId = "mapItemCellId";
    MKMapView map;

    public List<MKMapItem> MapItems { get; set; }

    public SearchResultsViewController (MKMapView map)
    {
        this.map = map;

        MapItems = new List<MKMapItem> ();
    }

    public override nint RowsInSection (UITableView tableView, nint section)
    {
        return MapItems.Count;
    }

    public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
    {
        var cell = tableView.DequeueReusableCell (mapItemCellId);

        if (cell == null)
            cell = new UITableViewCell ();

        cell.TextLabel.Text = MapItems [indexPath.Row].Name;
        return cell;
    }

    public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
    {
        // add item to map
        CLLocationCoordinate2D coord = MapItems [indexPath.Row].Placemark.Location.Coordinate;
        map.AddAnnotations (new MKPointAnnotation () {
            Title = MapItems [indexPath.Row].Name,
            Coordinate = coord
        });

        map.SetCenterCoordinate (coord, true);

        DismissViewController (false, null);
    }

    public void Search (string forSearchString)
    {
        // create search request
        var searchRequest = new MKLocalSearchRequest ();
        searchRequest.NaturalLanguageQuery = forSearchString;
        searchRequest.Region = new MKCoordinateRegion (map.UserLocation.Coordinate, new MKCoordinateSpan (0.25, 0.25));

        // perform search
        var localSearch = new MKLocalSearch (searchRequest);

        localSearch.Start (delegate (MKLocalSearchResponse response, NSError error) {
            if (response != null && error == null) {
                this.MapItems = response.MapItems.ToList ();
                this.TableView.ReloadData ();
            } else {
                Console.WriteLine ("local search error: {0}", error);
            }
        });


    }
}
```

### <a name="updating-the-search-results"></a>Обновление результатов поиска

`SearchResultsUpdater` Действует как посредник между `searchController`в строке поиска и результаты поиска. 

В этом примере нам нужно сначала создать метод поиска в `SearchResultsViewController`. Для этого необходимо создать `MKLocalSearch` объекта и его использования для поиска `MKLocalSearchRequest`, результаты извлекаются в обратный вызов, переданный `Start` метод `MKLocalSearch` объекта. Затем результаты возвращаются в `MKLocalSearchResponse` объект, содержащий массив `MKMapItem` объектов:

```csharp
public void Search (string forSearchString)
{
    // create search request
    var searchRequest = new MKLocalSearchRequest ();
    searchRequest.NaturalLanguageQuery = forSearchString;
    searchRequest.Region = new MKCoordinateRegion (map.UserLocation.Coordinate, new MKCoordinateSpan (0.25, 0.25));

    // perform search
    var localSearch = new MKLocalSearch (searchRequest);

    localSearch.Start (delegate (MKLocalSearchResponse response, NSError error) {
        if (response != null && error == null) {
            this.MapItems = response.MapItems.ToList ();
            this.TableView.ReloadData ();
        } else {
            Console.WriteLine ("local search error: {0}", error);
        }
    });


}
```

Затем в нашей `MapViewController` мы создадим пользовательскую реализацию `UISearchResultsUpdating`, назначенной для `SearchResultsUpdater` свойство наших `searchController` в [Добавление локальный пользовательский Интерфейс поиска](#Adding_a_Local_Search_UI) раздел:

```csharp
public class SearchResultsUpdator : UISearchResultsUpdating
{
    public event Action<string> UpdateSearchResults = delegate {};

    public override void UpdateSearchResultsForSearchController (UISearchController searchController)
    {
        this.UpdateSearchResults (searchController.SearchBar.Text);
    }
}
```

Выше реализации добавляет заметки к схеме при выборе элемента в результатах, как показано ниже:

 ![](images/08-search-results.png "Заметки добавляются на карту при выборе элемента в результатах")
 
> [!IMPORTANT]
> `UISearchController` была реализована в iOS 8. Если вы хотите поддерживать более ранних, чем это устройства, то необходимо будет использовать `UISearchDisplayController`.



## <a name="summary"></a>Сводка

В этой статье исследуется *карты* *Kit* платформа для iOS. Во-первых, он коснулся того, как `MKMapView` класс позволяет интерактивной карты должны быть включены в приложение. Затем показано, как настроить карты с помощью заметки и наложения. Наконец, он проверяет возможности локального поиска, которые были добавлены Map Kit с iOS 6.1, демонстрирующий использование выполнения запросов на основе расположения для точек интерес и добавить их на карту.

## <a name="related-links"></a>Связанные ссылки

- [SearchController](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller)
- [MapDemo (пример)](https://developer.xamarin.com/samples/monotouch/MapDemo)
