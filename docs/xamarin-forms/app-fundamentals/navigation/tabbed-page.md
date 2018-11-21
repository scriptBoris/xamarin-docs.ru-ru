---
title: Страница с вкладками Xamarin.Forms
description: Xamarin.Forms TabbedPage состоит из списка вкладок и область сведений, большего размера, с каждой вкладки, загрузке содержимого в области сведений. В этой статье показано, как использовать TabbedPage для перемещения по коллекции страниц.
ms.prod: xamarin
ms.assetid: C946057F-C77C-412D-82A0-DAF475A24EF5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 85a6bce8a1021c75064ba06f3a5daf69b7fe3e57
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171382"
---
# <a name="xamarinforms-tabbed-page"></a>Страница с вкладками Xamarin.Forms

_Xamarin.Forms TabbedPage состоит из списка вкладок и область сведений, большего размера, с каждой вкладки, загрузке содержимого в области сведений. В этой статье показано, как использовать TabbedPage для перемещения по коллекции страниц._

## <a name="overview"></a>Обзор

Ниже показаны снимки экрана [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) на каждой платформе:

![](tabbed-page-images/tab1.png "Пример TabbedPage")

Далее на снимках экрана сосредоточиться на вкладке Формат на каждой платформе:

![](tabbed-page-images/tabbedpage-components.png "Вкладка TabbedPage компонентов")

Макет [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)и на соответствующих вкладках зависит от платформы:

- В iOS отобразится список вкладок в нижней части экрана и превышает область сведений. На каждой вкладке также есть изображения значка, который должен быть 30 x 30 PNG с прозрачностью для обычного разрешения, 60 x 60 для высокого разрешения и 90 x 90 для iPhone 6 Plus разрешения. При наличии более пяти вкладках *дополнительные* появится вкладка, которая может использоваться для доступа к дополнительные вкладки. Дополнительные сведения о загрузке изображений в приложении Xamarin.Forms, см. в разделе [работа с образами](~/xamarin-forms/user-interface/images.md). Дополнительные сведения о требованиях к значок см. в разделе [создание приложений с вкладками](~/ios/user-interface/controls/creating-tabbed-applications.md).

  > [!NOTE]
  > Обратите внимание, что `TabbedRenderer` для iOS имеет переопределяемый `GetIcon` метод, который может использоваться для загрузки значки вкладок из указанного источника. Это переопределение позволяет использовать изображения SVG в виде значков на `TabbedPage`. Кроме того можно указать выбранном и невыбранном версии значка.

- В Android по умолчанию отображается список вкладок в верхней части экрана, и область сведений будет меньше. Тем не менее можно перемещать список вкладок в нижней части экрана с конкретной платформы. Дополнительные сведения см. в разделе [параметр TabbedPage инструментов размещения и цвет](~/xamarin-forms/platform/platform-specifics/consuming/android.md#tabbedpage-toolbar).

  > [!NOTE]
  > Обратите внимание на то, что при использовании совместимости приложений в Android, каждая вкладка отображается значок. Кроме того `TabbedPageRenderer` для Android AppCompat имеет переопределяемый `GetIconDrawable` метод, который может использоваться для загрузки значки вкладок из настраиваемого `Drawable`. Это переопределение позволяет использовать изображения SVG в виде значков на `TabbedPage`, и работает с обоими сверху и снизу панели вкладки. Кроме того, переопределяемый `SetTabIcon` метод может использоваться для загрузки значки вкладок из настраиваемого `Drawable` для вкладки в верхней панели.

- По Windows tablet форм факторов вкладках не всегда отображаются, и пользователи должны пальцем вниз (или щелкните правой кнопкой мыши, если у них есть подключено) для просмотра на вкладках `TabbedPage` (как показано ниже).

![](tabbed-page-images/windows-tabs.png "TabbedPage вкладок в Windows")

## <a name="creating-a-tabbedpage"></a>Создание TabbedPage

Два подхода можно использовать для создания [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage):

- [Заполнение](#Populating_a_TabbedPage_with_a_Page_Collection) [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) с коллекцию дочерних [ `Page` ](xref:Xamarin.Forms.Page) объектов, таких как коллекция [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) экземпляров.
- [Назначить](#Populating_a_TabbedPage_with_a_Template) коллекции [ `ItemsSource` ](xref:Xamarin.Forms.MultiPage`1.ItemsSource) свойство и назначьте [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) для [ `ItemTemplate` ](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) свойства для возврата страницы объекты в коллекции.

Используя оба способа [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) отобразит каждой страницы, как пользователь выбирает каждой вкладки.

> [!NOTE]
> Рекомендуется [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) должен быть заполнен [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) и [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)только экземпляр. Это позволит обеспечить согласованность пользовательского интерфейса на всех платформах.

<a name="Populating_a_TabbedPage_with_a_Page_Collection" />

### <a name="populating-a-tabbedpage-with-a-page-collection"></a>Заполнение TabbedPage с коллекцией страницы

В следующем примере показан код XAML [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) создаваемых внесения в него коллекцию дочерних [ `Page` ](xref:Xamarin.Forms.Page) объектов:

```xaml
<TabbedPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
            xmlns:local="clr-namespace:TabbedPageWithNavigationPage;assembly=TabbedPageWithNavigationPage"
            x:Class="TabbedPageWithNavigationPage.MainPage">
    <local:TodayPage />
    <NavigationPage Title="Schedule" Icon="schedule.png">
        <x:Arguments>
            <local:SchedulePage />
        </x:Arguments>
    </NavigationPage>
</TabbedPage>
```

В следующем примере кода показан эквивалент [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) в C#:

```csharp
public class MainPageCS : TabbedPage
{
  public MainPageCS ()
  {
    var navigationPage = new NavigationPage (new SchedulePageCS ());
    navigationPage.Icon = "schedule.png";
    navigationPage.Title = "Schedule";

    Children.Add (new TodayPageCS ());
    Children.Add (navigationPage);
  }
}
```

[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) Заполняется с двумя дочерними [ `Page` ](xref:Xamarin.Forms.Page) объектов. Первый дочерний элемент является [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) экземпляра, а вторая вкладка — [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) содержащий `ContentPage` экземпляра.

> [!NOTE]
> [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) Не поддерживает виртуализацию пользовательского интерфейса. Таким образом, если может снизиться производительность `TabbedPage` содержит слишком много элементов-потомков.

Ниже показаны снимки экрана `TodayPage` [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) экземпляр, который отображается на *сегодня* вкладке:

![](tabbed-page-images/today-page.png "ContentPage в TabbedPage")

Выбрав *расписание* вкладке отображает `SchedulePage` [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) экземпляр, который упаковывается в [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) экземпляра и отображается в Следующий снимок экрана:

![](tabbed-page-images/schedule-page.png "NavigationPage в TabbedPage")

Сведения о структуре [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage), см. в разделе [выполнение навигации](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

> [!NOTE]
> Хотя это допустимо для размещения [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) в [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage), мы не рекомендуем поместить `TabbedPage` в `NavigationPage`. Это обусловлено тем, в iOS, `UITabBarController` всегда служит оболочкой для `UINavigationController`. Дополнительные сведения см. в разделе [сочетании интерфейсы контроллера представления](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/CombiningViewControllers.html) в библиотеке разработчика iOS.

#### <a name="navigation-inside-a-tab"></a>Навигации внутри вкладки

Навигации могут выполняться со второй закладки, вызвав [ `PushAsync` ](xref:Xamarin.Forms.NavigationPage.PushAsync*) метод [ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) свойство [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) экземпляра как показано в следующем примере кода:

```csharp
async void OnUpcomingAppointmentsButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new UpcomingAppointmentsPage ());
}
```

В результате в стек навигации помещается экземпляр `UpcomingAppointmentsPage`, где он становится активной страницей. Это показано на следующем снимке экрана:

![](tabbed-page-images/navigationpage.png "Навигации внутри вкладки")

Дополнительные сведения о выполнении обновления с помощью навигации [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) , представлена в разделе [иерархическая навигация](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

<a name="Populating_a_TabbedPage_with_a_Template" />

### <a name="populating-a-tabbedpage-with-a-template"></a>Заполнение TabbedPage с помощью шаблона

В следующем примере показан код XAML [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) создан путем назначения [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) для [ `ItemTemplate` ](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) свойства для возврата страницы объекты в коллекции:

```xaml
<TabbedPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:local="clr-namespace:TabbedPageDemo;assembly=TabbedPageDemo"
            x:Class="TabbedPageDemo.TabbedPageDemoPage">
  <TabbedPage.Resources>
    <ResourceDictionary>
      <local:NonNullToBooleanConverter x:Key="booleanConverter" />
    </ResourceDictionary>
  </TabbedPage.Resources>
  <TabbedPage.ItemTemplate>
    <DataTemplate>
      <ContentPage Title="{Binding Name}" Icon="monkeyicon.png">
        <StackLayout Padding="5, 25">
          <Label Text="{Binding Name}" Font="Bold,Large" HorizontalOptions="Center" />
          <Image Source="{Binding PhotoUrl}" WidthRequest="200" HeightRequest="200" />
          <StackLayout Padding="50, 10">
            <StackLayout Orientation="Horizontal">
              <Label Text="Family:" HorizontalOptions="FillAndExpand" />
              <Label Text="{Binding Family}" Font="Bold,Medium" />
            </StackLayout>
            ...
          </StackLayout>
        </StackLayout>
      </ContentPage>
    </DataTemplate>
  </TabbedPage.ItemTemplate>
</TabbedPage>
```

[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) Заполняется данными, задав [ `ItemsSource` ](xref:Xamarin.Forms.MultiPage`1.ItemsSource) свойства в конструкторе для файла кода:

```csharp
public TabbedPageDemoPage ()
{
  ...
  ItemsSource = MonkeyDataModel.All;
}
```

В следующем примере кода показан эквивалент [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) в C#:

```csharp
public class TabbedPageDemoPageCS : TabbedPage
{
  public TabbedPageDemoPageCS ()
  {
    var booleanConverter = new NonNullToBooleanConverter ();

    ItemTemplate = new DataTemplate (() => {
      var nameLabel = new Label {
        FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label)),
        FontAttributes = FontAttributes.Bold,
        HorizontalOptions = LayoutOptions.Center
      };
      nameLabel.SetBinding (Label.TextProperty, "Name");

      var image = new Image { WidthRequest = 200, HeightRequest = 200 };
      image.SetBinding (Image.SourceProperty, "PhotoUrl");

      var familyLabel = new Label {
        FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
        FontAttributes = FontAttributes.Bold
      };
      familyLabel.SetBinding (Label.TextProperty, "Family");
      ...

      var contentPage = new ContentPage {
        Icon = "monkeyicon.png",
        Content = new StackLayout {
          Padding = new Thickness (5, 25),
          Children = {
            nameLabel,
            image,
            new StackLayout {
              Padding = new Thickness (50, 10),
              Children = {
                new StackLayout {
                  Orientation = StackOrientation.Horizontal,
                  Children = {
                    new Label { Text = "Family:", HorizontalOptions = LayoutOptions.FillAndExpand },
                    familyLabel
                  }
                },
                ...
              }
            }
          }
        }
      };
      contentPage.SetBinding (TitleProperty, "Name");
      return contentPage;
    });
    ItemsSource = MonkeyDataModel.All;
  }
}
```

Каждая вкладка отображает [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) , с помощью серии [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) и [ `Label` ](xref:Xamarin.Forms.Label) экземпляров для отображения данных для вкладки. На следующих снимках экрана показано содержимое для *Tamarin* вкладке:

![](tabbed-page-images/tab3.png "Заполнение TabbedPage с помощью шаблона")

Затем при выборе другой вкладке отображается содержимое для этой вкладки.

> [!NOTE]
> [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) Не поддерживает виртуализацию пользовательского интерфейса. Таким образом, если может снизиться производительность `TabbedPage` содержит слишком много элементов-потомков.

Дополнительные сведения о [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage), см. в разделе [главе 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) книги Xamarin.Forms (Charles Petzold).

## <a name="summary"></a>Сводка

В этой статье было показано, как использовать TabbedPage для перемещения по коллекции страниц. Xamarin.Forms [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) состоит из списка вкладок и область сведений, большего размера, причем каждая вкладка загрузке содержимого в области сведений.


## <a name="related-links"></a>Связанные ссылки

- [Создание страницы](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [TabbedPageWithNavigationPage (пример)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPageWithNavigationPage)
- [TabbedPage (пример)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPage/)
- [TabbedPage](xref:Xamarin.Forms.TabbedPage)
