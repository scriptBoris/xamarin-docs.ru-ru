---
title: Страница с вкладками Xamarin.Forms
description: Xamarin.Forms TabbedPage состоит из списка вкладок и большой области сведений, где каждая вкладка загружает содержимое в область сведений. В этой статье демонстрируется использование TabbedPage для перехода по коллекции страниц.
ms.prod: xamarin
ms.assetid: C946057F-C77C-412D-82A0-DAF475A24EF5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 85a6bce8a1021c75064ba06f3a5daf69b7fe3e57
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171382"
---
# <a name="xamarinforms-tabbed-page"></a>Страница с вкладками Xamarin.Forms

_Xamarin.Forms TabbedPage состоит из списка вкладок и большой области сведений, где каждая вкладка загружает содержимое в область сведений. В этой статье демонстрируется использование TabbedPage для перехода по коллекции страниц._

## <a name="overview"></a>Обзор

На следующих снимках экрана показана страница [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) для каждой платформы:

![](tabbed-page-images/tab1.png "Пример TabbedPage")

На следующих снимках экрана показан формат вкладок для каждой платформы:

![](tabbed-page-images/tabbedpage-components.png "Компоненты вкладок TabbedPage")

Макет [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) и его вкладки зависят от платформы:

- В iOS список вкладок отображается в нижней части экрана, а также в области данных сверху. Каждая вкладка содержит изображение значка, которое должно иметь формат PNG с прозрачностью и размер 30x30 для обычного разрешения, размер 60x60 для высокого разрешения и размер 90x90 для разрешения iPhone 6 Plus. При наличии более пяти вкладок появляется вкладка *Дополнительно*, используемая для доступа к дополнительным вкладкам. Дополнительные сведения о загрузке изображений в приложении Xamarin.Forms см. в разделе [Работа с изображениями](~/xamarin-forms/user-interface/images.md). Дополнительные сведения о требованиях к значкам см. в разделе [Создание приложений с вкладками](~/ios/user-interface/controls/creating-tabbed-applications.md).

  > [!NOTE]
  > Обратите внимание, что `TabbedRenderer` для iOS имеет переопределяемый метод `GetIcon`, который может использоваться для загрузки значков вкладок из указанного источника. Это переопределение позволяет использовать изображения SVG в виде значков в `TabbedPage`. Кроме того, можно предоставить варианты значка в выбранном и невыбранном состоянии.

- В Android список вкладок по умолчанию отображается в верхней части экрана, а также в области данных ниже. Однако этот список вкладок можно переместить в нижнюю часть экрана с учетом конкретной платформы. Дополнительные сведения см. в разделе [Настройка цвета и расположения панели инструментов TabbedPage](~/xamarin-forms/platform/platform-specifics/consuming/android.md#tabbedpage-toolbar).

  > [!NOTE]
  > Обратите внимание, что при использовании AppCompat в Android для каждой вкладки отображается значок. Кроме того, `TabbedPageRenderer` для Android AppCompat имеет переопределяемый метод `GetIconDrawable`, который может использоваться для загрузки значков вкладок из настраиваемого `Drawable`. Это переопределение позволяет использовать изображения SVG в виде значков в `TabbedPage` и работает как для верхних, так и для нижних панелей вкладок. Кроме того, можно использовать переопределяемый метод `SetTabIcon` для загрузки значков вкладок из настраиваемого `Drawable` для верхних панелей вкладок.

- В форм-факторах планшетов Windows вкладки видны не всегда, и пользователю требуется провести вниз (или щелкнуть правой кнопкой мыши, если подключена мышь), чтобы просмотреть вкладки в `TabbedPage` (как показано ниже).

![](tabbed-page-images/windows-tabs.png "Вкладки TabbedPage в Windows")

## <a name="creating-a-tabbedpage"></a>Создание TabbedPage

Создать [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) можно двумя способами:

- [Заполните](#Populating_a_TabbedPage_with_a_Page_Collection) [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) коллекцией дочерних объектов [`Page`](xref:Xamarin.Forms.Page), например коллекцией экземпляров [`ContentPage`](xref:Xamarin.Forms.ContentPage).
- [Присвойте](#Populating_a_TabbedPage_with_a_Template) коллекцию свойству [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource), а затем присвойте [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) свойству [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate), чтобы возвратить страницы для объектов в коллекции.

При обоих подходах [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) отображает каждую страницу при выборе пользователем каждой из вкладок.

> [!NOTE]
> Рекомендуется заполнять [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) только экземплярами [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) и [`ContentPage`](xref:Xamarin.Forms.ContentPage). Это позволит обеспечить согласованность пользовательского интерфейса на всех платформах.

<a name="Populating_a_TabbedPage_with_a_Page_Collection" />

### <a name="populating-a-tabbedpage-with-a-page-collection"></a>Заполнение TabbedPage коллекцией страниц

В следующем примере кода XAML показан [`TabbedPage`](xref:Xamarin.Forms.TabbedPage), создаваемый заполнением коллекцией дочерних объектов [`Page`](xref:Xamarin.Forms.Page):

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

В следующем примере кода создается эквивалентный объект [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) на языке C#:

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

[`TabbedPage`](xref:Xamarin.Forms.TabbedPage) заполняется двумя дочерними объектами [`Page`](xref:Xamarin.Forms.Page). Первый дочерний объект является экземпляром [`ContentPage`](xref:Xamarin.Forms.ContentPage), а вторая вкладка — [`NavigationPage`](xref:Xamarin.Forms.NavigationPage), содержащим экземпляр `ContentPage`.

> [!NOTE]
> [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) не поддерживает виртуализацию пользовательского интерфейса. Поэтому, если `TabbedPage` содержит слишком много дочерних элементов, производительность может снижаться.

На следующих снимках экрана изображен экземпляр `TodayPage` [`ContentPage`](xref:Xamarin.Forms.ContentPage), отображаемый на вкладке *Today* (Сегодня):

![](tabbed-page-images/today-page.png "ContentPage в TabbedPage")

При открытии вкладки *Schedule* (Расписание) отображается экземпляр `SchedulePage` [`ContentPage`](xref:Xamarin.Forms.ContentPage), который заключен в экземпляр [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) и показан на следующем снимке экрана:

![](tabbed-page-images/schedule-page.png "NavigationPage в TabbedPage")

Сведения о структуре [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) см. в разделе [Осуществление навигации](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

> [!NOTE]
> Хотя допустимо поместить [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) в [`TabbedPage`](xref:Xamarin.Forms.TabbedPage), мы не рекомендуем помечать `TabbedPage` в `NavigationPage`. Это обусловлено тем, что в iOS `UITabBarController` всегда служит оболочкой для `UINavigationController`. Дополнительные сведения см. в разделе [Комбинированные интерфейсы контроллера представления](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/CombiningViewControllers.html) библиотеки разработчика iOS.

#### <a name="navigation-inside-a-tab"></a>Навигации внутри вкладки

Навигация может осуществляться со второй вкладки посредством вызова метода [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) для свойства [`Navigation`](xref:Xamarin.Forms.VisualElement.Navigation) экземпляра [`ContentPage`](xref:Xamarin.Forms.ContentPage), как показано в следующем примере кода:

```csharp
async void OnUpcomingAppointmentsButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new UpcomingAppointmentsPage ());
}
```

В результате в стек навигации помещается экземпляр `UpcomingAppointmentsPage`, где он становится активной страницей. Эти действия показаны на следующих снимках экрана:

![](tabbed-page-images/navigationpage.png "Навигации внутри вкладки")

Дополнительные сведения об осуществлении навигации с помощью класса [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) см. в статье [Иерархическая навигация](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

<a name="Populating_a_TabbedPage_with_a_Template" />

### <a name="populating-a-tabbedpage-with-a-template"></a>Заполнение TabbedPage с помощью шаблона

В следующем примере кода XAML показан объект [`TabbedPage`](xref:Xamarin.Forms.TabbedPage), сформированный путем присвоения [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) свойству [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) для возврата страниц для объектов в коллекции:

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

Объект [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) заполняется данными путем задания свойства [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) в конструкторе для файла кода программной части:

```csharp
public TabbedPageDemoPage ()
{
  ...
  ItemsSource = MonkeyDataModel.All;
}
```

В следующем примере кода создается эквивалентный объект [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) на языке C#:

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

Каждая вкладка отображает [`ContentPage`](xref:Xamarin.Forms.ContentPage), использующий серии экземпляров [`StackLayout`](xref:Xamarin.Forms.StackLayout) и [`Label`](xref:Xamarin.Forms.Label), чтобы отобразить данные для вкладки. На следующих снимках экрана показано содержимое для вкладки *Tamarin*:

![](tabbed-page-images/tab3.png "Заполнение TabbedPage с помощью шаблона")

Затем при выборе другой вкладки отображается содержимое для этой вкладки.

> [!NOTE]
> [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) не поддерживает виртуализацию пользовательского интерфейса. Поэтому, если `TabbedPage` содержит слишком много дочерних элементов, производительность может снижаться.

Дополнительные сведения о [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) см. в [главе 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) книги Xamarin.Forms Чарльза Петцольда (Charles Petzold).

## <a name="summary"></a>Сводка

В этой статье демонстрируется использование TabbedPage для перехода по коллекции страниц. Xamarin.Forms [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) состоит из списка вкладок и большой области сведений, где каждая вкладка загружает содержимое в область сведений.


## <a name="related-links"></a>Связанные ссылки

- [Виды страниц](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [TabbedPageWithNavigationPage (пример)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPageWithNavigationPage)
- [TabbedPage (пример)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPage/)
- [TabbedPage](xref:Xamarin.Forms.TabbedPage)
