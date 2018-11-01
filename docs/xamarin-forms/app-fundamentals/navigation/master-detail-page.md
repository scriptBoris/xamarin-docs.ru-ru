---
title: Xamarin.Forms страницы
description: Xamarin.Forms MasterDetailPage — это страница, которая управляет два связанных страниц данных – главной страницы, которая представляет элементы и страницу сведений, в которой представлены сведения об элементах на главной странице. В этой статье объясняется, как использовать MasterDetailPage и переходить между страницами, его данных.
ms.prod: xamarin
ms.assetid: 119945E3-58B8-4630-A3D2-8B561529D53B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 25f6cf341fcf47d5dc5320f73855bb2a4e29a9e8
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675527"
---
# <a name="xamarinforms-master-detail-page"></a>Xamarin.Forms страницы

_Xamarin.Forms MasterDetailPage — это страница, которая управляет два связанных страниц данных – главной страницы, которая представляет элементы и страницу сведений, в которой представлены сведения об элементах на главной странице. В этой статье объясняется, как использовать MasterDetailPage и переходить между страницами, его данных._

## <a name="overview"></a>Обзор

Главная страница обычно отображает список элементов, как показано на следующем снимке экрана:

[![](master-detail-page-images/masterpage-components.png "Компоненты страницы master")](master-detail-page-images/masterpage-components-large.png#lightbox "компоненты образца страницы")

Расположение списка элементов идентична на каждой платформе, и выбрав один из элементов будет перейти к соответствующей странице сведений. Кроме того Главная страница также содержит панель навигации, содержащий кнопку, которая может использоваться для перехода к странице сведения об активном:

- В iOS на панели навигации находится в верхней части страницы и имеет кнопку, которая переходит к странице сведений. Кроме того сведения об активном страницы можно перейти, проводя пальцем главной страницы слева.
- На Android на панели навигации в верхней части страницы и отображает заголовок, значок и кнопку, которая выполняет переход на страницу сведений о. Значок определяется в `[Activity]` атрибут, который оформляет `MainActivity` класс в проекте специфические для платформы Android. Кроме того, сведения об активном страницы можно перейти, проводя пальцем главной страницы слева, коснувшись странице сведений в правой части экрана и можно просмотреть, выбрав *обратно* расположенную в нижней части экрана.
- В универсальной платформы Windows (UWP), на панели навигации присутствует в верхней части страницы и имеет кнопку, которая переходит к странице сведений.

Подробных данных отображает страницы, который соответствует элементу выбранной на главной странице, и на следующих снимках экрана показаны основные компоненты на страницу сведений о:

![](master-detail-page-images/detailpage-components.png "Компоненты страницы сведений")

Страница сведений содержит панель навигации, содержимое которой представлено зависят от платформы:

- В iOS, на панели навигации находится в верхней части страницы отображает заголовок и имеет кнопку, которая возвращает на главную страницу, при условии, что экземпляр страницы сведений упаковывается в [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) экземпляра. Кроме того главной страницы могут быть возвращены для прокручивая страницу сведений справа.
- В Android панель навигации присутствует в верхней части страницы и отображает заголовок, значок и кнопку, которая возвращает на главную страницу. Значок определяется в `[Activity]` атрибут, который оформляет `MainActivity` класс в проекте специфические для платформы Android.
- В универсальной платформы Windows на панели навигации находится в верхней части страницы и отображает заголовок и имеет кнопку, которая возвращает на главную страницу.

### <a name="navigation-behavior"></a>Поведения навигации

Поведение навигацию между основными и подробными страниц не зависит от платформы:

- В iOS, на страницу сведений о *слайды* справа как слайды главной страницы из левого и левую часть подробные данные по-прежнему отображается страница.
- В Android, подробные сведения и образец страницы являются *накладывается* друг от друга.
- На UWP, подробные сведения и образец страницы являются *местами*.

Аналогичное поведение будет наблюдаться в альбомном режиме, за исключением того, что главная страница в iOS и Android имеет аналогичные ширины как Главная страница в книжной ориентации, поэтому большая часть страницы сведений будет отображаться.

Сведения об управлении поведение навигации, см. в разделе [управление поведение при отображении страницы сведений](#Controlling_the_Detail_Page_Display_Behavior).

## <a name="creating-a-masterdetailpage"></a>Создание MasterDetailPage

Объект [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) содержит [ `Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) и [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) свойства, которые имеют тип [ `Page` ](xref:Xamarin.Forms.Page), которые используются для получения и задания основные и подробные страницы соответственно.

> [!IMPORTANT]
> Объект [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) должен быть корневой страницы и использовать его как на дочернюю страницу в другие типы страниц может привести к непредвиденным и несогласованное поведение. Кроме того, рекомендуется на главную страницу [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) всегда должно быть [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) экземпляра и что на страницу сведений о заполняются только с [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage), [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage), и `ContentPage` экземпляров. Это позволит обеспечить согласованность пользовательского интерфейса на всех платформах.

В следующем примере показан код XAML [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) , задает [ `Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) и [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) свойства:

```xaml
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  xmlns:local="clr-namespace:MasterDetailPageNavigation;assembly=MasterDetailPageNavigation"
                  x:Class="MasterDetailPageNavigation.MainPage">
    <MasterDetailPage.Master>
        <local:MasterPage x:Name="masterPage" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <NavigationPage>
            <x:Arguments>
                <local:ContactsPage />
            </x:Arguments>
        </NavigationPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>
```

В следующем примере кода показан эквивалент [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) в C#:

```csharp
public class MainPageCS : MasterDetailPage
{
    MasterPageCS masterPage;

    public MainPageCS ()
    {
        masterPage = new MasterPageCS ();
        Master = masterPage;
        Detail = new NavigationPage (new ContactsPageCS ());
        ...
    }
    ...
}
```

[ `MasterDetailPage.Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) Свойству [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) экземпляра. [ `MasterDetailPage.Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) Свойству [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) содержащий `ContentPage` экземпляра.

### <a name="creating-the-master-page"></a>Создание главной страницы

В следующем примере кода XAML показано объявление `MasterPage` объект, который указан через [ `MasterDetailPage.Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) свойство:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="using:MasterDetailPageNavigation"
             x:Class="MasterDetailPageNavigation.MasterPage"
             Padding="0,40,0,0"
             Icon="hamburger.png"
             Title="Personal Organiser">
    <StackLayout>
        <ListView x:Name="listView" x:FieldModifier="public">
           <ListView.ItemsSource>
                <x:Array Type="{x:Type local:MasterPageItem}">
                    <local:MasterPageItem Title="Contacts" IconSource="contacts.png" TargetType="{x:Type local:ContactsPage}" />
                    <local:MasterPageItem Title="TodoList" IconSource="todo.png" TargetType="{x:Type local:TodoListPage}" />
                    <local:MasterPageItem Title="Reminders" IconSource="reminders.png" TargetType="{x:Type local:ReminderPage}" />
                </x:Array>
            </ListView.ItemsSource>
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <Grid Padding="5,10">
                            <Grid.ColumnDefinitions>
                                <ColumnDefinition Width="30"/>
                                <ColumnDefinition Width="*" />
                            </Grid.ColumnDefinitions>
                            <Image Source="{Binding IconSource}" />
                            <Label Grid.Column="1" Text="{Binding Title}" />
                        </Grid>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

Страница состоит из [ `ListView` ](xref:Xamarin.Forms.ListView) , заполняется данными в XAML, задав его [ `ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource) свойство к массиву `MasterPageItem` экземпляров. Каждый `MasterPageItem` определяет `Title`, `IconSource`, и `TargetType` свойства.

Объект [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) назначается [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) свойства, чтобы вывести все `MasterPageItem`. `DataTemplate` Содержит [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) , состоящий из [ `Image` ](xref:Xamarin.Forms.Image) и [ `Label` ](xref:Xamarin.Forms.Label). [ `Image` ](xref:Xamarin.Forms.Image) Отображает `IconSource` значение свойства и [ `Label` ](xref:Xamarin.Forms.Label) отображает `Title` значение свойства, для каждого `MasterPageItem`.

На странице имеется его [ `Title` ](xref:Xamarin.Forms.Page.Title) и [ `Icon` ](xref:Xamarin.Forms.Page.Icon) заданными свойствами. Значок будет отображаться на странице сведений, при условии, что на страницу сведений о имеет заголовок. Оно должно быть включено в iOS, заключая экземпляр страницы сведений в [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) экземпляра.

> [!NOTE]
> [ `MasterDetailPage.Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) Страницы должен быть его [ `Title` ](xref:Xamarin.Forms.Page.Title) свойству значение, или возникнет исключение.

В следующем примере кода показан эквивалентный страницы, созданной в C#:

```csharp
public class MasterPageCS : ContentPage
{
  public ListView ListView { get { return listView; } }

  ListView listView;

  public MasterPageCS ()
  {
    var masterPageItems = new List<MasterPageItem> ();
    masterPageItems.Add (new MasterPageItem {
      Title = "Contacts",
      IconSource = "contacts.png",
      TargetType = typeof(ContactsPageCS)
    });
    masterPageItems.Add (new MasterPageItem {
      Title = "TodoList",
      IconSource = "todo.png",
      TargetType = typeof(TodoListPageCS)
    });
    masterPageItems.Add (new MasterPageItem {
      Title = "Reminders",
      IconSource = "reminders.png",
      TargetType = typeof(ReminderPageCS)
    });

    listView = new ListView {
      ItemsSource = masterPageItems,
      ItemTemplate = new DataTemplate (() => {
        var grid = new Grid { Padding = new Thickness(5, 10) };
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(30) });
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Star });

        var image = new Image();
        image.SetBinding(Image.SourceProperty, "IconSource");
        var label = new Label { VerticalOptions = LayoutOptions.FillAndExpand };
        label.SetBinding(Label.TextProperty, "Title");

        grid.Children.Add(image);
        grid.Children.Add(label, 1, 0);

        return new ViewCell { View = grid };
      }),
      SeparatorVisibility = SeparatorVisibility.None
    };

    Icon = "hamburger.png";
    Title = "Personal Organiser";
    Content = new StackLayout
    {
      Children = { listView }
    };
  }
}
```

На следующих снимках экрана показано главной страницы на каждой платформе:

![](master-detail-page-images/masterpage.png "Пример страницы основных")

### <a name="creating-and-displaying-the-detail-page"></a>Создание и отображение на страницу сведений о

`MasterPage` Экземпляр содержит `ListView` свойства, предоставляющего его [ `ListView` ](xref:Xamarin.Forms.ListView) экземпляр таким образом, чтобы `MainPage` [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) можно зарегистрировать экземпляр обработчик событий для обработки [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) событий. Это позволяет `MainPage` для установки [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) свойство на страницу, представляющий выбранный `ListView` элемента. В следующем примере кода показан обработчик событий:

```csharp
public partial class MainPage : MasterDetailPage
{
    public MainPage ()
    {
        ...
        masterPage.listView.ItemSelected += OnItemSelected;
    }

    void OnItemSelected (object sender, SelectedItemChangedEventArgs e)
    {
        var item = e.SelectedItem as MasterPageItem;
        if (item != null) {
            Detail = new NavigationPage ((Page)Activator.CreateInstance (item.TargetType));
            masterPage.listView.SelectedItem = null;
            IsPresented = false;
        }
    }
}
```

`OnItemSelected` Метод выполняет следующие действия:

- Он извлекает [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem) из [ `ListView` ](xref:Xamarin.Forms.ListView) экземпляром и если он не `null`, присваивается новый экземпляр типа страницы, хранящиеся в настраницусведенийо`TargetType`свойство `MasterPageItem`. Тип страницы упаковывается в [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) экземпляра, чтобы убедиться, что значок для ссылок на [ `Icon` ](xref:Xamarin.Forms.Page.Icon) свойство `MasterPage` отображается на странице сведений в iOS.
- Элемент, выбранный в [ `ListView` ](xref:Xamarin.Forms.ListView) присваивается `null` чтобы убедиться, что ни один из `ListView` элементы будут выбраны при очередном `MasterPage` представлены.
- На страницу сведений о будет отображаться для пользователя, задав [ `MasterDetailPage.IsPresented` ](xref:Xamarin.Forms.MasterDetailPage.IsPresented) свойства `false`. Это свойство контролирует, представлен ли страница основной или подробности. Оно должно быть присвоено `true` для отображения главной страницы, а в `false` на страницу детализации.

Ниже показаны снимки экрана `ContactPage` странице сведений отображается после выбран на главной странице:

![](master-detail-page-images/detailpage.png "Пример страницы сведений")

<a name="Controlling_the_Detail_Page_Display_Behavior" />

### <a name="controlling-the-detail-page-display-behavior"></a>Управление поведением отображением страницы сведений

Как [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) управляет основными и подробными страницы зависит от того, работает ли приложение на телефоне или планшете, ориентации устройства, а для параметра [ `MasterBehavior` ](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) свойство. Это свойство определяет, как будет отображаться на странице детализации. Возможными значениями являются:

- **По умолчанию** — страницы отображаются с помощью платформы по умолчанию.
- **Контекстном** — на страницу сведений о охватывает или частично рассматриваются главной страницы.
- **Разбиение** — Главная страница отображается в левой части и страницы сведений находится справа.
- **SplitOnLandscape** — С разделенным экраном используется в том случае, когда устройство находится в альбомной ориентации.
- **SplitOnPortrait** — С разделенным экраном используется в том случае, когда устройство находится в книжной ориентации.

В следующем примере кода XAML показано, как задать [ `MasterBehavior` ](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) свойство [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage):

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  x:Class="MasterDetailPageNavigation.MainPage"
                  MasterBehavior="Popover">
  ...
</MasterDetailPage>
```

В следующем примере кода показан эквивалент [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) в C#:

```csharp
public class MainPageCS : MasterDetailPage
{
    MasterPageCS masterPage;

    public MainPageCS ()
    {
        MasterBehavior = MasterBehavior.Popover;
        ...
    }
}
```

Тем не менее значение [ `MasterBehavior` ](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) свойство влияет только на приложения, работающие на планшетных ПК или на рабочем столе. Приложения, выполняющиеся на телефонах, всегда имеют *контекстном* поведение.

## <a name="summary"></a>Сводка

В этой статье было показано, как использовать [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) и переходить между страницами, его данных. Xamarin.Forms `MasterDetailPage` — это страница, которая управляет связанных данных — две страницы, главной страницы, которая представляет элементы и страницу сведений, в которой представлены сведения об элементах на главной странице.


## <a name="related-links"></a>Связанные ссылки

- [Создание страницы](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [MasterDetailPage (пример)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/MasterDetailPage/)
- [MasterDetailPage](xref:Xamarin.Forms.MasterDetailPage)
