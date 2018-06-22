---
title: Модуль подготовки отчетов базовых классов и собственные элементы управления
description: Каждый элемент управления Xamarin.Forms имеет сопутствующий модуль подготовки отчетов для каждой платформы, который создает экземпляр собственного элемента управления. В этой статье перечислены модуль подготовки отчетов и классы собственного элемента управления, которые реализуют каждого Xamarin.Forms страницы, макет, представления и ячейки.
ms.prod: xamarin
ms.assetid: A8909AE3-ED0E-4D24-BF96-B49E732E3B93
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/15/2016
ms.openlocfilehash: 513b9b3738b9481444cdad10daa9b11a8441c9dd
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/27/2018
ms.locfileid: "32019689"
---
# <a name="renderer-base-classes-and-native-controls"></a>Модуль подготовки отчетов базовых классов и собственные элементы управления

_Каждый элемент управления Xamarin.Forms имеет сопутствующий модуль подготовки отчетов для каждой платформы, который создает экземпляр собственного элемента управления. В этой статье перечислены модуль подготовки отчетов и классы собственного элемента управления, которые реализуют каждого Xamarin.Forms страницы, макет, представления и ячейки._

За исключением элемента `MapRenderer` платформой модулей подготовки отчетов класса, можно найти в следующих пространств имен:

- **iOS** – Xamarin.Forms.Platform.iOS
- **Android** — Xamarin.Forms.Platform.Android
- **Android (AppCompat)** – Xamarin.Forms.Platform.Android.AppCompat
- **Универсальная платформа Windows (UWP)** – Xamarin.Forms.Platform.UWP

`MapRenderer` Класс можно найти в следующих пространств имен:

- **iOS** – Xamarin.Forms.Maps.iOS
- **Android** — Xamarin.Forms.Maps.Android
- **Универсальная платформа Windows (UWP)** – Xamarin.Forms.Maps.UWP

## <a name="pages"></a>Число страниц

В следующей таблице перечислены средства визуализации и классы собственного элемента управления, которые реализуют каждого Xamarin.Forms [страницы](~/xamarin-forms/user-interface/controls/pages.md) типа:

|Страница|Модуль подготовки отчетов|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)|[PageRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/contentpage.md)|UIViewController|Группе ViewGroup||FrameworkElement|
|[`MasterDetailPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)|PhoneMasterDetailRenderer (iOS — телефон), TabletMasterDetailPageRenderer (iOS — планшете), MasterDetailRenderer (Android), MasterDetailPageRenderer (Android AppCompat), MasterDetailPageRenderer (UWP)|UIViewController (телефон), UISplitViewController (планшетный компьютер)|DrawerLayout (версия 4)|DrawerLayout (версия 4)|FrameworkElement (пользовательский элемент управления)|
|[`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)|NavigationRenderer (iOS и Android), NavigationPageRenderer (Android AppCompat), NavigationPageRenderer (UWP)|UIToolbar|Группе ViewGroup|Группе ViewGroup|FrameworkElement (пользовательский элемент управления)|
|[`TabbedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)|TabbedRenderer (iOS и Android), TabbedPageRenderer (Android AppCompat), TabbedPageRenderer (UWP)|UIView|ViewPager|ViewPager|FrameworkElement (Pivot)|
|[`TemplatedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedPage/)|PageRenderer|UIViewController|Группе ViewGroup||FrameworkElement|
|[`CarouselPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/)|CarouselPageRenderer|UIScrollView|ViewPager|ViewPager|FrameworkElement (FlipView)|

## <a name="layouts"></a>Макеты

В следующей таблице перечислены средства визуализации и классы собственного элемента управления, которые реализуют каждого Xamarin.Forms [макета](~/xamarin-forms/user-interface/controls/layouts.md) типа:

|Макет|Модуль подготовки отчетов|iOS|Android|UWP|
|--- |--- |--- |--- |--- |
|[`ContentPresenter`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/)|ViewRenderer|UIView|Просмотр|FrameworkElement|
|[`ContentView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/)|ViewRenderer|UIView|Просмотр|FrameworkElement|
|[`Frame`](https://developer.xamarin.com/api/type/Xamarin.Forms.Frame/)|FrameRenderer|UIView|Группе ViewGroup|Border|
|[`ScrollView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/)|ScrollViewRenderer|UIScrollView|ScrollView|ScrollViewer|
|[`TemplatedView`](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedView/)|ViewRenderer|UIView|Просмотр|FrameworkElement|
|[`AbsoluteLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/)|ViewRenderer|UIView|Просмотр|FrameworkElement|
|[`Grid`](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/)|ViewRenderer|UIView|Просмотр|FrameworkElement|
|[`RelativeLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/)|ViewRenderer|UIView|Просмотр|FrameworkElement|
|[`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)|ViewRenderer|UIView|Просмотр|FrameworkElement|

## <a name="views"></a>Представления

В следующей таблице перечислены средства визуализации и классы собственного элемента управления, которые реализуют каждого Xamarin.Forms [представление](~/xamarin-forms/user-interface/controls/views.md) типа:

|Представления|Модуль подготовки отчетов|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ActivityIndicator`](https://developer.xamarin.com/api/type/Xamarin.Forms.ActivityIndicator/)|ActivityIndicatorRenderer|UIActivityIndicator|ProgressBar||ProgressBar|
|[`BoxView`](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/)|BoxRenderer (iOS и Android), BoxViewRenderer (UWP)|UIView|Группе ViewGroup||Прямоугольник|
|[`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)|ButtonRenderer|UIButton|Кнопка|AppCompatButton|Кнопка|
|[`CarouselView`](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselView/)|CarouselViewRenderer|UIScrollView|RecyclerView||FlipView|
|[`DatePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/)|DatePickerRenderer|UITextField|EditText||DatePicker|
|[`Editor`](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/)|EditorRenderer|UITextView|EditText||TextBox|
|[`Entry`](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)|[EntryRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/entry.md)|UITextField|EditText||TextBox|
|[`Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)|ImageRenderer|UIImageView|ImageView||Изображение|
|[`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)|LabelRenderer|UILabel|TextView||TextBlock|
|[`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)|[ListViewRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md)|UITableView|ListView||ListView|
|[`Map`](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/)|[MapRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)|MKMapView|MapView||MapControl|
|[`Picker`](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)|PickerRenderer|UITextField|EditText|EditText|ComboBox|
|[`ProgressBar`](https://developer.xamarin.com/api/type/Xamarin.Forms.ProgressBar/)|ProgressBarRenderer|UIProgressView|ProgressBar||ProgressBar|
|[`SearchBar`](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/)|SearchBarRenderer|UISearchBar|SearchView||AutoSuggestBox|
|[`Slider`](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/)|SliderRenderer|UISlider|SeekBar||Slider|
|[`Stepper`](https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/)|StepperRenderer|UIStepper|LinearLayout||Элемент управления|
|[`Switch`](https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/)|SwitchRenderer|UISwitch|Параметр|SwitchCompat|ToggleSwitch|
|[`TableView`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/)|TableViewRenderer|UITableView|ListView||ListView|
|[`TimePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/)|TimePickerRenderer|UITextField|EditText||TimePicker|
|[`WebView`](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/)|WebViewRenderer|UIWebView|Веб-представление||Веб-представление|

## <a name="cells"></a>Ячейки

В следующей таблице перечислены средства визуализации и классы собственного элемента управления, которые реализуют каждого Xamarin.Forms [ячейки](~/xamarin-forms/user-interface/controls/cells.md) типа:

|Ячейки|Модуль подготовки отчетов|iOS|Android|UWP|
|--- |--- |--- |--- |--- |
|[`EntryCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.EntryCell/)|EntryCellRenderer|UITableViewCell с UITextField|LinearLayout с TextView и EditText|DataTemplate с текстовым полем|
|[`SwitchCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.SwitchCell/)|SwitchCellRenderer|UITableViewCell с UISwitch|Параметр|DataTemplate с сетку, содержащую TextBlock и ToggleSwitch|
|[`TextCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/)|TextCellRenderer|UITableViewCell|LinearLayout с двумя TextViews|DataTemplate с StackPanel, содержащий два TextBlocks|
|[`ImageCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/)|ImageCellRenderer|UITableViewCell с UIImage|LinearLayout с двумя TextViews и ImageView|DataTemplate с сетку, содержащую два TextBlocks и изображения|
|[`ViewCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/)|[ViewCellRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)|UITableViewCell|Просмотр|DataTemplate с ContentPresenter|

## <a name="summary"></a>Сводка

В этой статье перечислил модуль подготовки отчетов и классы собственного элемента управления, которые реализуют каждого Xamarin.Forms страницы, макет, представления и ячейки. Каждый элемент управления Xamarin.Forms имеет сопутствующий модуль подготовки отчетов для каждой платформы, который создает экземпляр собственного элемента управления.

## <a name="related-links"></a>Связанные ссылки

- [Пользовательские модули подготовки отчетов (университет Xamarin видео)](https://developer.xamarin.com/videos/cross-platform/xamarinforms-custom-renderers/)
