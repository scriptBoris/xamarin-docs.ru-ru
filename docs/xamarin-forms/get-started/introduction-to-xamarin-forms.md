---
title: Введение в Xamarin.Forms
description: В этой статье приводятся основные сведения о наборе средств Xamarin.Forms и начале создания приложений с его помощью.
ms.prod: xamarin
ms.assetid: f619595f-3ee7-439b-a1bc-d13e5106e6e9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/02/2016
ms.openlocfilehash: 95b0744cdd52ac1c3f5d7c62c18139a30400ab04
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38999021"
---
# <a name="an-introduction-to-xamarinforms"></a>Введение в Xamarin.Forms

_Xamarin.Forms — это кроссплатформенный слой абстракции пользовательского интерфейса с собственной поддержкой. Он позволяет легко создавать пользовательские интерфейсы, которые можно использовать в Android, iOS, Windows и на универсальной платформе Windows. Пользовательские интерфейсы отрисовываются с помощью собственных элементов управления целевой платформы, обеспечивая единообразный внешний вид приложений Xamarin.Forms на каждой платформе. В этой статье описан набор средств Xamarin.Forms и создание приложений с его помощью._

<a name="Overview" />

## <a name="overview"></a>Обзор

Xamarin.Forms — это платформа, которая позволяет разработчикам быстро создавать кроссплатформенные пользовательские интерфейсы. Она предоставляет собственный слой абстракции для пользовательского интерфейса, который будет отрисовываться с помощью собственных элементов управления в iOS, Android и на универсальной платформе Windows (UWP). Это означает, что большая часть кода, связанного с пользовательским интерфейсом, может использоваться для разных целевых платформ с сохранением их особенностей оформления.

Xamarin.Forms позволяет быстро разрабатывать прототипы приложений, которые со временем могут развиваться в сложные приложения. Так как приложения Xamarin.Forms являются собственными, у них нет ограничений, характерных для других наборов средств, таких как браузерные песочницы, ограниченные интерфейсы API или низкая производительность. Приложения, написанные с помощью Xamarin.Forms, могут использовать все возможности и интерфейсы API базовой платформы, включая, помимо прочего, CoreMotion, PassKit и StoreKit в iOS, NFC и Сервисы Google Play в Android и Плитки в Windows. Кроме того, можно создавать приложения, одни части пользовательского интерфейса которых основаны на Xamarin.Forms, а другие — на собственном наборе средств пользовательского интерфейса.

По своей архитектуре приложения Xamarin.Forms не отличаются от традиционных кроссплатформенных приложений. Наиболее распространенный подход состоит в использовании [переносимых библиотек](~/cross-platform/app-fundamentals/pcl.md) или [общих проектов](~/cross-platform/app-fundamentals/shared-projects.md) для общего кода и создании приложений для конкретных платформ, в которых используется этот общий код.

Существуют два метода создания пользовательского интерфейса в Xamarin.Forms. Первый метод заключается в создании пользовательского интерфейса исключительно с помощью исходного кода на языке C#. Второй заключается в использовании *XAML*, декларативного языка разметки, который служит для описания пользовательского интерфейса. Дополнительные сведения о XAML см. в статье [Основы XAML](~/xamarin-forms/xaml/xaml-basics/index.md).

В этой статье рассматриваются основы платформы Xamarin.Forms, в том числе следующие темы:

-  [Знакомство с приложением Xamarin.Forms](#Examining_A_Xamarin.Forms_Application).
-  [Использование страниц и элементов управления Xamarin.Forms](#Views_and_Layouts).
-  [Отображение списка данных](#Lists_in_Xamarin.Forms).
-  [Настройка привязки данных](#Data_Binding).
-  [Переход между страницами](#Navigation).
-  [Дальнейшие действия](#Next_Steps).

<a name="Examining_A_Xamarin_Forms_Application" />

### <a name="examining-a-xamarinforms-application"></a>Знакомство с приложением Xamarin.Forms

В Visual Studio для Mac и Visual Studio на основе шаблона приложения Xamarin.Forms по умолчанию создается простейшее возможное решение Xamarin.Forms, которое отображает текст для пользователя. При запуске этого приложения оно должно выглядеть примерно так, как показано на снимках экрана ниже:

[![](introduction-to-xamarin-forms-images/image05-sml.png "Приложение Xamarin.Forms по умолчанию")](introduction-to-xamarin-forms-images/image05.png#lightbox "Приложение Xamarin.Forms по умолчанию")

Каждый экран на снимках экрана соответствует объекту *Page* в Xamarin.Forms. Класс [`Page`](xref:Xamarin.Forms.Page) представляет *действие* в Android, *контроллер представления* в iOS или *страницу* в универсальной платформе Windows (UWP). В представленном примере на снимках экрана выше создается объект [`ContentPage`](xref:Xamarin.Forms.ContentPage), который служит для отображения элемента [`Label`](xref:Xamarin.Forms.Label).

Чтобы код запуска как можно больше использовался повторно, в приложениях Xamarin.Forms есть класс `App`, который отвечает за создание первого отображаемого объекта [`Page`](xref:Xamarin.Forms.Page). Пример класса `App` приведен в следующем коде:

```csharp
public class App : Application
{
  public App ()
  {
    MainPage = new ContentPage {
      Content =  new Label
      {
          Text = "Hello, Forms !",
          VerticalOptions = LayoutOptions.CenterAndExpand,
          HorizontalOptions = LayoutOptions.CenterAndExpand,
      }
      };
  }
}
```

Этот код создает объект [`ContentPage`](xref:Xamarin.Forms.ContentPage), который отображает один элемент [`Label`](xref:Xamarin.Forms.Label), размещаемый в центре страницы как по вертикали, так и по горизонтали.

<a name="Launching_the_Initial_Xamarin_Forms_Page_on_Each_Platform" />

### <a name="launching-the-initial-xamarinforms-page-on-each-platform"></a>Запуск начальной страницы Xamarin.Forms на каждой платформе

Для использования объекта [`Page`](xref:Xamarin.Forms.Page) приложение для каждой платформы должно инициализировать платформу Xamarin.Forms и предоставить экземпляр [`ContentPage`](xref:Xamarin.Forms.ContentPage) при запуске. Способы инициализации зависят от платформы и рассматриваются в следующих разделах.

<a name="Launching_in_iOS" />

#### <a name="ios"></a>iOS

Для запуска начальной страницы Xamarin.Forms в iOS проект платформы включает в себя класс `AppDelegate`, который наследуется от класса `Xamarin.Forms.Platform.iOS.FormsApplicationDelegate`, как показано в следующем примере кода:

```csharp
[Register("AppDelegate")]
public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
{
    public override bool FinishedLaunching(UIApplication app, NSDictionary options)
    {
      global::Xamarin.Forms.Forms.Init ();
      LoadApplication (new App ());
      return base.FinishedLaunching (app, options);
    }
}
```

Переопределение `FinishedLoading` инициализирует платформу Xamarin.Forms, вызывая метод `Init`. В результате реализация Xamarin.Forms для iOS загружается в приложении, прежде чем корневой контроллер представления задается путем вызова метода `LoadApplication`.

<a name="Launching_in_Android" />

#### <a name="android"></a>Android

Для запуска начальной страницы Xamarin.Forms в Android проект платформы включает в себя код, который создает объект `Activity` с атрибутом `MainLauncher`, причем действие наследуется от класса `FormsAppCompatActivity`, как показано в следующем примере кода:

```csharp
namespace HelloXamarinFormsWorld.Android
{
    [Activity(Label = "HelloXamarinFormsWorld", Theme = "@style/MainTheme", MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    public class MainActivity : FormsAppCompatActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            Xamarin.Forms.Forms.Init(this, bundle);
            LoadApplication (new App ());
        }
    }
}
```

Переопределение `OnCreate` инициализирует платформу Xamarin.Forms, вызывая метод `Init`. В результате реализация Xamarin.Forms для Android загружается в приложении перед загрузкой самого приложения Xamarin.Forms.

#### <a name="universal-windows-platform"></a>Универсальная платформа Windows 

В приложениях универсальной платформы Windows (UWP) метод `Init`, который инициализирует платформу Xamarin.Forms, вызывается из класса `App`:

```csharp
Xamarin.Forms.Forms.Init (e);

if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
{
  ...
}
```

В результате в приложении загружается реализация Xamarin.Forms для UWP. Начальная страница Xamarin.Forms запускается классом `MainPage`, как показано в следующем примере кода:

```csharp
public partial class MainPage
{
    public MainPage()
    {
      this.InitializeComponent();
      this.LoadApplication(new HelloXamarinFormsWorld.App());
    }
}
```

Приложение Xamarin.Forms загружается с помощью метода `LoadApplication`.

<a name="Views_and_Layouts" />

### <a name="views-and-layouts"></a>Представления и макеты

Для создания пользовательского интерфейса приложения Xamarin.Forms используются четыре основные группы элементов управления.

1. **Страницы** — страницы Xamarin.Forms представляют экраны в кроссплатформенных мобильных приложениях. Дополнительные сведения о страницах см. в статье [Страницы Xamarin.Forms](~/xamarin-forms/user-interface/controls/pages.md).
1. **Макеты** — макеты Xamarin.Forms представляют собой контейнеры, которые служат для объединения представлений в логические структуры. Дополнительные сведения о макетах см. в статье [Макеты Xamarin.Forms](~/xamarin-forms/user-interface/controls/layouts.md).
1. **Представления** — представления Xamarin.Forms являются элементами управления, отображаемыми в пользовательском интерфейсе, например метки, кнопки и поля ввода текста. Дополнительные сведения о представлениях см. в статье [Представления Xamarin.Forms](~/xamarin-forms/user-interface/controls/views.md).
1. **Ячейки** — ячейки Xamarin.Forms являются специальными объектами, представляющими элементы списка и описывающими способ их отрисовки. Дополнительные сведения о ячейках см. в статье [Ячейки Xamarin.Forms](~/xamarin-forms/user-interface/controls/cells.md).

Во время выполнения каждый элемент управления сопоставляется с собственным аналогом, который и будет отрисовываться.

Элементы управления размещаются внутри макета. Теперь мы рассмотрим класс [`StackLayout`](xref:Xamarin.Forms.StackLayout), который реализует стандартный макет.

<a name="StackLayout" />

#### <a name="stacklayout"></a>StackLayout

Класс [`StackLayout`](xref:Xamarin.Forms.StackLayout) упрощает разработку кроссплатформенных приложений благодаря автоматической компоновке элементов управления на экране независимо от его размера. Дочерние элементы размещаются поочередно (по горизонтали или по вертикали) в порядке добавления. Область, занимаемая макетом `StackLayout`, зависит от значений свойств [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) и [`VerticalOptions`](xref:Xamarin.Forms.View.HorizontalOptions), но по умолчанию макет `StackLayout` будет пытаться использовать весь экран.

Следующий код XAML представляет собой пример использования макета [`StackLayout`](xref:Xamarin.Forms.StackLayout) для размещения трех элементов управления [`Label`](xref:Xamarin.Forms.Label):

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" x:Class="HelloXamarinFormsWorldXaml.StackLayoutExample1" Padding="20">
  <StackLayout Spacing="10">
    <Label Text="Stop" BackgroundColor="Red" Font="20" />
    <Label Text="Slow down" BackgroundColor="Yellow" Font="20" />
    <Label Text="Go" BackgroundColor="Green" Font="20" />
  </StackLayout>
</ContentPage>
```

Эквивалентный код на языке C# показан в следующем примере:

```csharp
public class StackLayoutExample : ContentPage
{
    public StackLayoutExample()
    {
        Padding = new Thickness(20);
        var red = new Label
        {
            Text = "Stop", BackgroundColor = Color.Red, FontSize = 20
        };
        var yellow = new Label
        {
            Text = "Slow down", BackgroundColor = Color.Yellow, FontSize = 20
        };
        var green = new Label
        {
            Text = "Go", BackgroundColor = Color.Green, FontSize = 20
        };

        Content = new StackLayout
        {
            Spacing = 10,
            Children = { red, yellow, green }
        };
    }
}
```

По умолчанию макет [`StackLayout`](xref:Xamarin.Forms.StackLayout) предполагает вертикальную ориентацию, как показано на следующих снимках экрана:

[![](introduction-to-xamarin-forms-images/image09-sml.png "Вертикальный макет StackLayout")](introduction-to-xamarin-forms-images/image09.png#lightbox "Вертикальный макет StackLayout")

Ориентацию макета [`StackLayout`](xref:Xamarin.Forms.StackLayout) можно изменить на горизонтальную, как показано в следующем примере кода XAML:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" x:Class="HelloXamarinFormsWorldXaml.StackLayoutExample2" Padding="20">
  <StackLayout Spacing="10" VerticalOptions="End" Orientation="Horizontal" HorizontalOptions="Start">
    <Label Text="Stop" BackgroundColor="Red" Font="20" />
    <Label Text="Slow down" BackgroundColor="Yellow" Font="20" />
    <Label Text="Go" BackgroundColor="Green" Font="20" />
  </StackLayout>
</ContentPage>
```

Эквивалентный код на языке C# показан в следующем примере:

```csharp
public class StackLayoutExample: ContentPage
{
    public StackLayoutExample()
    {
        // Code that creates labels removed for clarity
        Content = new StackLayout
        {
            Spacing = 10,
            VerticalOptions = LayoutOptions.End,
            Orientation = StackOrientation.Horizontal,
            HorizontalOptions = LayoutOptions.Start,
            Children = { red, yellow, green }
        };
    }
}
```

На следующих снимках экрана показан получившийся макет:

[![](introduction-to-xamarin-forms-images/image10-sml.png "Горизонтальный макет StackLayout")](introduction-to-xamarin-forms-images/image10.png#lightbox "Горизонтальный макет StackLayout")

Размеры элементов управления можно задать с помощью свойств `HeightRequest` и `WidthRequest`, как показано в следующем примере кода XAML:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" x:Class="HelloXamarinFormsWorldXaml.StackLayoutExample3" Padding="20">
  <StackLayout Spacing="10" VerticalOptions="End" Orientation="Horizontal" HorizontalOptions="Start">
    <Label Text="Stop" BackgroundColor="Red" Font="20" WidthRequest="100" />
    <Label Text="Slow down" BackgroundColor="Yellow" Font="20" WidthRequest="100" />
    <Label Text="Go" BackgroundColor="Green" Font="20" WidthRequest="200" />
  </StackLayout>
</ContentPage>
```

Эквивалентный код на языке C# показан в следующем примере:

```csharp
var red = new Label
{
    Text = "Stop", BackgroundColor = Color.Red, FontSize = 20, WidthRequest = 100
};
var yellow = new Label
{
    Text = "Slow down", BackgroundColor = Color.Yellow, FontSize = 20, WidthRequest = 100
};
var green = new Label
{
    Text = "Go", BackgroundColor = Color.Green, FontSize = 20, WidthRequest = 200
};

Content = new StackLayout
{
    Spacing = 10,
    VerticalOptions = LayoutOptions.End,
    Orientation = StackOrientation.Horizontal,
    HorizontalOptions = LayoutOptions.Start,
    Children = { red, yellow, green }
};
```

На следующих снимках экрана показан получившийся макет:

[![](introduction-to-xamarin-forms-images/image11-sml.png "Горизонтальный макет StackLayout с LayoutOptions")](introduction-to-xamarin-forms-images/image11.png#lightbox "Горизонтальный макет StackLayout с LayoutOptions")

Дополнительные сведения о классе [`StackLayout`](xref:Xamarin.Forms.StackLayout) см. в статье [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md).

<a name="Lists_in_Xamarin_Forms" />

## <a name="lists-in-xamarinforms"></a>Списки в Xamarin.Forms

Элемент управления [`ListView`](xref:Xamarin.Forms.ListView) отвечает за отображение коллекции элементов на экране. Каждый элемент `ListView` содержится в отдельной ячейке. По умолчанию элемент управления `ListView` использует встроенный шаблон [`TextCell`](xref:Xamarin.Forms.TextCell) и отображает одну строку текста.

Ниже представлен простой пример кода [`ListView`](xref:Xamarin.Forms.ListView):

```csharp
var listView = new ListView
{
    RowHeight = 40
};
listView.ItemsSource = new string []
{
    "Buy pears", "Buy oranges", "Buy mangos", "Buy apples", "Buy bananas"
};
Content = new StackLayout
{
    VerticalOptions = LayoutOptions.FillAndExpand,
    Children = { listView }
};
```

На следующем снимке экрана показан получившийся элемент управления [`ListView`](xref:Xamarin.Forms.ListView):

 ![](introduction-to-xamarin-forms-images/image13.png "ListView")

Дополнительные сведения об элементе управления [`ListView`](xref:Xamarin.Forms.ListView) см. в статье [ListView](~/xamarin-forms/user-interface/listview/index.md).

<a name="Binding_to_a_Custom_Class" />

### <a name="binding-to-a-custom-class"></a>Привязка к пользовательскому классу

Элемент управления [`ListView`](xref:Xamarin.Forms.ListView) может также применяться для отображения пользовательских объектов с помощью шаблона [`TextCell`](xref:Xamarin.Forms.TextCell) по умолчанию.

Следующий пример кода демонстрирует класс `TodoItem`:

```csharp
public class TodoItem
{
    public string Name { get; set; }
    public bool Done { get; set; }
}
```

Элемент управления [`ListView`](xref:Xamarin.Forms.ListView) можно заполнить, как показано в следующем примере кода:

```csharp
listView.ItemsSource = new TodoItem [] {
    new TodoItem { Name = "Buy pears" },
    new TodoItem { Name = "Buy oranges", Done=true} ,
    new TodoItem { Name = "Buy mangos" },
    new TodoItem { Name = "Buy apples", Done=true },
    new TodoItem { Name = "Buy bananas", Done=true }
};
```

Чтобы указать, какое свойство `TodoItem` должно отображаться элементом управления [`ListView`](xref:Xamarin.Forms.ListView), можно создать привязку, как показано в следующем примере кода:

```csharp
listView.ItemTemplate = new DataTemplate(typeof(TextCell));
listView.ItemTemplate.SetBinding(TextCell.TextProperty, "Name");
```

В этом примере создается привязка, указывающая путь к свойству `TodoItem.Name`. Результат будет таким же, как на снимке экрана выше.

Дополнительные сведения о привязке к пользовательскому классу см. в статье [Источники данных ListView](~/xamarin-forms/user-interface/listview/data-and-databinding.md).

<a name="Selecting_an_Item_in_a_ListView" />

### <a name="selecting-an-item-in-a-listview"></a>Выбор элемента в ListView

Чтобы обеспечить реакцию на прикосновение пользователя к ячейке в списке [`ListView`](xref:Xamarin.Forms.ListView), необходимо обработать событие [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected), как показано в следующем примере кода:

```csharp
listView.ItemSelected += async (sender, e) => {
    await DisplayAlert("Tapped!", e.SelectedItem + " was tapped.", "OK");
};
```

Если метод [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)) содержится в объекте [`NavigationPage`](xref:Xamarin.Forms.NavigationPage), его можно использовать для открытия новой страницы с помощью встроенной функции обратной навигации. Событие [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) может обратиться к объекту, связанному с ячейкой, посредством свойства [`e.SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem), привязать его к новой странице и отобразить новую страницу с помощью метода `PushAsync`, как показано в следующем примере кода:

```csharp
listView.ItemSelected += async (sender, e) => {
    var todoItem = (TodoItem)e.SelectedItem;
    var todoPage = new TodoItemPage(todoItem); // so the new page shows correct data
    await Navigation.PushAsync(todoPage);
};
```

Встроенная обратная навигация реализуется в каждой платформе особым образом. Дополнительные сведения см. в разделе [Переходы](#Navigation).

Дополнительные сведения о выборе элементов [`ListView`](xref:Xamarin.Forms.ListView) см. в статье [Взаимодействие с элементом управления ListView](~/xamarin-forms/user-interface/listview/interactivity.md).

<a name="Customizing_the_appearance_of_a_cell" />

### <a name="customizing-the-appearance-of-a-cell"></a>Настройка внешнего вида ячейки

Чтобы настроить внешний вид ячейки, можно создать подкласс класса [`ViewCell`](xref:Xamarin.Forms.ViewCell) и задать в качестве его типа значение свойства [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) элемента управления [`ListView`](xref:Xamarin.Forms.ListView).

Ячейка, показанная на следующем снимке экрана, состоит из одного элемента управления [`Image`](xref:Xamarin.Forms.Image) и двух элементов управления [`Label`](xref:Xamarin.Forms.Label):

 ![](introduction-to-xamarin-forms-images/image14.png "Настроенный внешний вид ячейки ListView")

Для создания этого пользовательского макета необходимо создать подкласс класса [`ViewCell`](xref:Xamarin.Forms.ViewCell), как показано в следующем примере кода:

```csharp
class EmployeeCell : ViewCell
{
    public EmployeeCell()
    {
        var image = new Image
        {
            HorizontalOptions = LayoutOptions.Start
        };
        image.SetBinding(Image.SourceProperty, new Binding("ImageUri"));
        image.WidthRequest = image.HeightRequest = 40;

        var nameLayout = CreateNameLayout();
        var viewLayout = new StackLayout()
        {
           Orientation = StackOrientation.Horizontal,
           Children = { image, nameLayout }
        };
        View = viewLayout;
    }

    static StackLayout CreateNameLayout()
    {
        var nameLabel = new Label
        {
            HorizontalOptions= LayoutOptions.FillAndExpand
        };
        nameLabel.SetBinding(Label.TextProperty, "DisplayName");

        var twitterLabel = new Label
        {
           HorizontalOptions = LayoutOptions.FillAndExpand,
           Font = Fonts.Twitter
        };
        twitterLabel.SetBinding(Label.TextProperty, "Twitter");

        var nameLayout = new StackLayout()
        {
           HorizontalOptions = LayoutOptions.StartAndExpand,
           Orientation = StackOrientation.Vertical,
           Children = { nameLabel, twitterLabel }
        };
        return nameLayout;
    }
}
```

Этот код выполняет указанные ниже задачи:

-  Добавляет элемент управления [`Image`](xref:Xamarin.Forms.Image) и привязывает его к свойству `ImageUri` объекта `Employee`. Дополнительные сведения о привязке данных см. в разделе [Привязка данных](#Data_Binding).
-  Создает макет [`StackLayout`](xref:Xamarin.Forms.StackLayout) с вертикальной ориентацией для размещения двух элементов управления [`Label`](xref:Xamarin.Forms.Label). Элементы управления `Label` привязываются к свойствам `DisplayName` и `Twitter` объекта `Employee`.
-  Создает макет [`StackLayout`](xref:Xamarin.Forms.StackLayout) для размещения существующего элемента управления [`Image`](xref:Xamarin.Forms.Image) и макета `StackLayout`. Дочерние объекты размещаются по вертикали.

После создания пользовательской ячейки она может использоваться элементом управления [`ListView`](xref:Xamarin.Forms.ListView). Для этого она заключается в шаблон [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), как показано в следующем примере кода:

```csharp
List<Employee> myListOfEmployeeObjects = GetAListOfAllEmployees();
var listView = new ListView
{
    RowHeight = 40
};
listView.ItemsSource = myListOfEmployeeObjects;
listView.ItemTemplate = new DataTemplate(typeof(EmployeeCell));
```

Этот код предоставляет список `List` элементов `Employee` в [`ListView`](xref:Xamarin.Forms.ListView). Каждая ячейка отрисовывается с помощью класса `EmployeeCell`. Элемент управления `ListView` передает объект `Employee` в `EmployeeCell` в качестве [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext).

Дополнительные сведения о настройке внешнего вида ячеек см. в статье [Внешний вид ячеек](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md).

<a name="Using_XAML_to_Create_and_Customize_A_List" />

### <a name="using-xaml-to-create-and-customize-a-list"></a>Создание и настройка списка с помощью XAML

В следующем примере показан код XAML, эквивалентный элементу управления [`ListView`](xref:Xamarin.Forms.ListView) из предыдущего раздела:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:local="clr-namespace:XamarinFormsXamlSample;assembly=XamarinFormsXamlSample"
             xmlns:constants="clr-namespace:XamarinFormsSample;assembly=XamarinFormsXamlSample"
             x:Class="XamarinFormsXamlSample.Views.EmployeeListPage"
             Title="Employee List">
  <ListView x:Name="listView" IsVisible="false" ItemsSource="{x:Static local:App.Employees}" ItemSelected="EmployeeListOnItemSelected">
    <ListView.ItemTemplate>
      <DataTemplate>
        <ViewCell>
          <ViewCell.View>
            <StackLayout Orientation="Horizontal">
              <Image Source="{Binding ImageUri}" WidthRequest="40" HeightRequest="40" />
              <StackLayout Orientation="Vertical" HorizontalOptions="StartAndExpand">
                <Label Text="{Binding DisplayName}" HorizontalOptions="FillAndExpand" />
                <Label Text="{Binding Twitter}" Font="{x:Static constants:Fonts.Twitter}"/>
              </StackLayout>
            </StackLayout>
          </ViewCell.View>
        </ViewCell>
      </DataTemplate>
    </ListView.ItemTemplate>
  </ListView>
</ContentPage>
```

В этом коде XAML определяется объект [`ContentPage`](xref:Xamarin.Forms.ContentPage), содержащий элемент [`ListView`](xref:Xamarin.Forms.ListView). Источник данных для `ListView` задается с помощью атрибута [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource). Макет каждой строки в `ItemsSource` определяется в элементе [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate).

<a name="Data_Binding" />

## <a name="data-binding"></a>Привязка данных

Привязка данных связывает два объекта, которые называются *источником* и *целевым объектом*. *Источник* предоставляет данные. *Целевой* объект будет использовать (и часто отображать) данные из источника. Например, свойство [`Text`](xref:Xamarin.Forms.Label.Text) элемента [`Label`](xref:Xamarin.Forms.Label) (*целевого* объекта) часто связывается с открытым свойством `string` *источника*. На следующей схеме показано отношение привязки:

![](introduction-to-xamarin-forms-images/data-binding.png "Привязка данных")

Главным преимуществом привязки данных является то, что вам больше не нужно беспокоиться о синхронизации данных между представлениями и источником данных. Изменения в *источнике* автоматически передаются в *целевой* объект платформой привязки. При необходимости изменения в целевом объекте также могут передаваться в *источник*.

Установление привязки данных состоит из двух этапов:

- Свойству [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) *целевого* объекта должен быть присвоен *источник*.
- Между *целевым* объектом и *источником* должна быть установлена привязка. В XAML для этого используется расширение разметки [`Binding`](xref:Xamarin.Forms.Xaml.BindingExtension). В C# для этого применяется метод [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)).

Дополнительные сведения о привязке данных см. в статье [Основы привязки данных](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

### <a name="xaml"></a>XAML

Следующий пример кода демонстрирует привязку данных в XAML:

```xaml
<Entry Text="{Binding FirstName}" ... />
```

Создается привязка между свойством [`Entry.Text`](xref:Xamarin.Forms.Entry.Text) и свойством `FirstName` *источника*. Изменения, вносимые в элементе управления `Entry`, будут автоматически применяться к объекту `employeeToDisplay`. Аналогичным образом, при внесении изменений в свойство `employeeToDisplay.FirstName` подсистема привязки Xamarin.Forms будет также обновлять содержимое элемента управления `Entry`. Это называется *двусторонней привязкой*. Чтобы двусторонняя привязка работала, класс модели должен реализовывать интерфейс `INotifyPropertyChanged`.

Хотя свойство [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) класса `EmployeeDetailPage` можно задавать в XAML, в этом случае ему присваивается экземпляр `Employee` в коде программной части:

```csharp
public EmployeeDetailPage(Employee employee)
{
    InitializeComponent();
    this.BindingContext = employee;
}
```

Хотя свойство [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) каждого *целевого* объекта можно задавать по отдельности, это не является обязательным. `BindingContext` — это особое свойство, которое наследуется всеми дочерними объектами. Поэтому когда свойству `BindingContext` объекта [`ContentPage`](xref:Xamarin.Forms.ContentPage) присваивается экземпляр `Employee`, все дочерние объекты `ContentPage` имеют то же значение `BindingContext` и могут привязываться к открытым свойствам объекта `Employee`.

### <a name="c35"></a>C&#35;

Следующий пример кода демонстрирует привязку данных в коде на языке C#:

```csharp
public EmployeeDetailPage(Employee employeeToDisplay)
{
    this.BindingContext = employeeToDisplay;
    var firstName = new Entry()
    {
        HorizontalOptions = LayoutOptions.FillAndExpand
    };
    firstName.SetBinding(Entry.TextProperty, "FirstName");
    ...
}
```

В конструктор [`ContentPage`](xref:Xamarin.Forms.ContentPage) передается экземпляр `Employee`, а свойству [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) присваивается объект, к которому необходимо выполнить привязку. Создается экземпляр элемента управления [`Entry`](xref:Xamarin.Forms.Entry) и привязка между свойством [`Entry.Text`](xref:Xamarin.Forms.Entry.Text) и свойством `FirstName` *источника*. Изменения, вносимые в элементе управления `Entry`, будут автоматически применяться к объекту `employeeToDisplay`. Аналогичным образом, при внесении изменений в свойство `employeeToDisplay.FirstName` подсистема привязки Xamarin.Forms будет также обновлять содержимое элемента управления `Entry`. Это называется *двусторонней привязкой*. Чтобы двусторонняя привязка работала, класс модели должен реализовывать интерфейс `INotifyPropertyChanged`.

Метод `SetBinding` принимает два параметра. В первом параметре указываются сведения о типе привязки. Второй параметр служит для предоставления сведений о привязываемых объектах и способе привязки. В большинстве случаев он представляет собой просто строку, содержащую имя свойства [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext). Для прямой привязки к свойству `BindingContext` используется следующий синтаксис:

```csharp
someLabel.SetBinding(Label.TextProperty, new Binding("."));
```

Синтаксис с точечной нотацией предписывает Xamarin.Forms использовать в качестве источника данных [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) вместо свойства `BindingContext`. Это полезно, если `BindingContext` имеет простой тип, например `string` или `int`.

<a name="INotifyPropertyChanged" />

### <a name="property-change-notification"></a>Уведомление об изменении свойства

По умолчанию *целевой* объект принимает значение *источника* только при создании привязки. Чтобы синхронизировать пользовательский интерфейс с источником данных, необходимо каким-либо образом уведомлять *целевой* объект об изменении *источника*. Для этого служит интерфейс `INotifyPropertyChanged`. Реализация этого интерфейса позволяет уведомлять все элементы управления с привязкой данных об изменении значений базовых свойств.

Объекты, реализующие интерфейс `INotifyPropertyChanged`, должны вызывать событие `PropertyChanged` при изменении значения одного из их свойств, как показано в следующем примере кода:

```csharp
public class MyObject : INotifyPropertyChanged
{
    public event PropertyChangedEventHandler PropertyChanged;

    string _firstName;
    public string FirstName
    {
        get { return _firstName; }
        set
        {
            if (value.Equals(_firstName, StringComparison.Ordinal))
            {
                // Nothing to do - the value hasn't changed;
                return;
            }
            _firstName = value;
            OnPropertyChanged();

        }
    }

    void OnPropertyChanged([CallerMemberName] string propertyName = null)
    {
        var handler = PropertyChanged;
        if (handler != null)
        {
            handler(this, new PropertyChangedEventArgs(propertyName));
        }
    }
}
```

При изменении свойства `MyObject.FirstName` вызывается метод `OnPropertyChanged`, который создает событие `PropertyChanged`. Чтобы избежать вызова ненужных событий, событие `PropertyChanged` не инициируется, если значение свойства не изменяется.

Обратите внимание на то, что в методе `OnPropertyChanged` параметр `propertyName` имеет атрибут `CallerMemberName`. Благодаря этому при вызове метода `OnPropertyChanged` со значением `null` атрибут `CallerMemberName` будет предоставлять имя метода, вызвавшего `OnPropertyChanged`.

<a name="Navigation" />

## <a name="navigation"></a>Навигация

Xamarin.Forms предоставляет ряд различных способов перехода по страницам в зависимости от используемого типа объекта [`Page`](xref:Xamarin.Forms.Page). Для экземпляров [`ContentPage`](xref:Xamarin.Forms.ContentPage) доступны два способа перехода:

- [Иерархическая навигация](#Hierarchical_Navigation)
- [Модальная навигация](#Modal_Navigation)

Классы [`CarouselPage`](xref:Xamarin.Forms.CarouselPage), [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) и [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) предоставляют альтернативные способы перехода. Дополнительные сведения см. в разделе [Переходы](~/xamarin-forms/app-fundamentals/navigation/index.md).

<a name="Hierarchical_Navigation" />

### <a name="hierarchical-navigation"></a>Иерархическая навигация

Класс [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) обеспечивает иерархическую навигацию, при которой пользователь может переходить по страницам вперед и назад по своему желанию. Этот класс реализует навигацию на основе стека объектов [`Page`](xref:Xamarin.Forms.Page) по методу LIFO (последним поступил — первым обслужен).

При иерархической навигации класс [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) используется для перехода по стеку объектов [`ContentPage`](xref:Xamarin.Forms.ContentPage). Для перехода с одной страницы на другую приложение помещает новую страницу в стек навигации, где она становится активной страницей. Для возврата к предыдущей странице приложение выбирает текущую страницу из стека навигации, после чего активной становится верхняя страница в стеке.

Первая страница, добавленная в стек навигации, называется *корневой* страницей приложения, что демонстрируется в следующем примере кода:

```csharp
public App ()
{
    MainPage = new NavigationPage(new EmployeeListPage());
}
```

Для перехода к странице `LoginPage` необходимо вызвать метод [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)) свойства [`Navigation`](xref:Xamarin.Forms.VisualElement.Navigation) текущей страницы, как показано в следующем примере кода:

```csharp
await Navigation.PushAsync(new LoginPage());
```

В результате в стек навигации помещается новый объект `LoginPage`, где он становится активной страницей.

Активная страница может быть извлечена из стека навигации путем нажатия кнопки *Назад* на устройстве, причем это может быть как физическая кнопка, так и кнопка на экране. Чтобы вернуться на предыдущую страницу программным образом, экземпляр `LoginPage` должен вызвать метод [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync), как показано в следующем примере кода:

```csharp
await Navigation.PopAsync();
```

Дополнительные сведения об иерархической навигации см. в статье [Иерархическая навигация](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

<a name="Modal_Navigation" />

### <a name="modal-navigation"></a>Модальная навигация

Xamarin.Forms поддерживает модальные страницы. На модальной странице пользователь должен выполнить отдельную задачу, причем он не может уйти с этой страницы, пока задача не будет выполнена или отменена.

Модальная страница может быть любого из типов [`Page`](xref:Xamarin.Forms.Page), поддерживаемых Xamarin.Forms. Для отображения модальной страницы приложение помещает ее в стек навигации, где она становится активной страницей. Для возврата к предыдущей странице приложение выбирает текущую страницу из стека навигации, после чего активной становится верхняя страница в стеке.

Методы модальной навигации предоставляются свойством [`Navigation`](xref:Xamarin.Forms.VisualElement.Navigation) любых типов, производных от класса [`Page`](xref:Xamarin.Forms.Page). Свойство [`Navigation`](xref:Xamarin.Forms.VisualElement.Navigation) также предоставляет свойство [`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack), из которого могут быть получены модальные страницы в стеке навигации. Однако не существует средств для работы с модальным стеком или перехода к корневой странице модальной навигации. Причина в том, что такие операции поддерживаются не всеми базовыми платформами.

> [!NOTE]
> Экземпляр [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) не требуется для навигации по модальным страницам.

Для модального перехода к странице `LoginPage` необходимо вызвать метод [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync*) свойства [`Navigation`](xref:Xamarin.Forms.VisualElement.Navigation) текущей страницы, как показано в следующем примере кода:

```csharp
await Navigation.PushModalAsync(new LoginPage());
```

В результате в стек навигации помещается экземпляр `LoginPage`, где он становится активной страницей.

Активная страница может быть извлечена из стека навигации путем нажатия кнопки *Назад* на устройстве, причем это может быть как физическая кнопка, так и кнопка на экране. Чтобы вернуться на исходную страницу программным образом, экземпляр `LoginPage` должен вызвать метод [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync), как показано в следующем примере кода:

```csharp
await Navigation.PopModalAsync();
```

В результате экземпляр `LoginPage` удаляется из стека навигации, а активной становится верхняя страница в нем.

Дополнительные сведения о модальной навигации см. в статье [Модальные страницы](~/xamarin-forms/app-fundamentals/navigation/modal.md).

<a name="Next_Steps" />

## <a name="next-steps"></a>Следующие шаги

В этой вводной статье приводятся общие сведения, которые смогут помочь вам приступить к созданию приложений Xamarin.Forms. Далее рекомендуется ознакомиться с перечисленными ниже функциональными возможностями.

- Шаблоны элементов управления дают возможность легко применять темы к страницам приложения и отменять их во время выполнения. Дополнительные сведения см. в статье [Шаблоны элементов управления](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md).
- Шаблоны данных дают возможность настраивать представление данных в поддерживаемых элементах управления. Дополнительные сведения см. в статье [Шаблоны данных](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).
- Общий код может получать доступ к собственным функциональным возможностям посредством класса [`DependencyService`](xref:Xamarin.Forms.DependencyService). Дополнительные сведения см. в статье, посвященной [доступу к собственным функциональным возможностям с помощью DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md).
- Платформа Xamarin.Forms включает в себя простую службу обмена сообщениями для отправки и получения сообщений, которая сокращает потребность в обеспечении взаимодействия между классами. Дополнительные сведения см. в статье, посвященной [публикации и подписке с помощью MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md).
- Каждая страница, макет и элемент управления отрисовываются по-разному на разных платформах с помощью класса `Renderer`, который создает собственный элемент управления, размещает его на экране и реализует поведение, определенное в общем коде. Разработчики могут реализовывать пользовательские классы `Renderer` для настройки внешнего вида или работы элемента управления. Дополнительные сведения см. в статье [Пользовательские отрисовщики](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).
- Эффекты также позволяют настраивать собственные элементы управления на каждой платформе. Эффекты создаются в проектах для конкретных платформ путем создания подклассов элемента управления [`PlatformEffect`](xref:Xamarin.Forms.PlatformEffect`2) и используются путем их присоединения к соответствующему элементу управления Xamarin.Forms. Дополнительные сведения см. в статье [Эффекты](~/xamarin-forms/app-fundamentals/effects/index.md).

Много полезных сведений о платформе Xamarin.Forms можно получить в книге "Creating Mobile Apps with Xamarin.Forms" (Создание мобильных приложений с помощью Xamarin.Forms) Чарльза Петцольда (Charles Petzold). Дополнительные сведения см. на странице [Создание мобильных приложений с помощью Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md).

## <a name="summary"></a>Сводка

В этой статье были приведены основные сведения о наборе средств Xamarin.Forms и начале создания приложений с его помощью. Xamarin.Forms — это кроссплатформенный слой абстракции пользовательского интерфейса с собственной поддержкой. Он позволяет легко создавать пользовательские интерфейсы, которые можно использовать в Android, iOS и на универсальной платформе Windows. Пользовательские интерфейсы отрисовываются с помощью собственных элементов управления целевой платформы, обеспечивая единообразный внешний вид приложений Xamarin.Forms на каждой платформе.


## <a name="related-links"></a>Связанные ссылки

- [Основы XAML](~/xamarin-forms/xaml/xaml-basics/index.md)
- [Справочник по элементам управления](~/xamarin-forms/user-interface/controls/index.md)
- [Пользовательский интерфейс](~/xamarin-forms/user-interface/index.md)
- [Примеры Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Примеры для начала работы](https://developer.xamarin.com/samples/xamarin-forms/GettingStarted/)
- [Xamarin.Forms](xref:Xamarin.Forms)
- [Бесплатное самостоятельное обучение (видео)](https://university.xamarin.com/self-guided)
- [Начало работы с Xamarin.Forms (книга для iOS)](https://developer.xamarin.com/workbooks/xamarin-forms/getting-started/GettingStartedWithXamarinForms-ios.workbook)
