---
title: Страница Xamarin.Forms основной подробности
description: Xamarin.Forms MasterDetailPage — это страница, который управляет две связанные страницы данных — Главная страница, которая представляет элементы и страницу сведений, которая представляет сведения об элементах на главной странице. В этой статье объясняется, как использовать MasterDetailPage и перейти между страницами его сведения.
ms.prod: xamarin
ms.assetid: 119945E3-58B8-4630-A3D2-8B561529D53B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 46fa32fc8203b32378f4a4fbe07cb8c9f8dbb854
ms.sourcegitcommit: 7a89735aed9ddf89c855fd33928915d72da40c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36209210"
---
# <a name="xamarinforms-master-detail-page"></a>Страница Xamarin.Forms основной подробности

_Xamarin.Forms MasterDetailPage — это страница, который управляет две связанные страницы данных — Главная страница, которая представляет элементы и страницу сведений, которая представляет сведения об элементах на главной странице. В этой статье объясняется, как использовать MasterDetailPage и перейти между страницами его сведения._

## <a name="overview"></a>Обзор

Главная страница обычно отображает список элементов, как показано на следующем снимке экрана:

[![](master-detail-page-images/masterpage-components.png "Компоненты основной страницы")](master-detail-page-images/masterpage-components-large.png#lightbox "компоненты основной страницы")

Расположение списка элементов идентична на каждой платформе, и выбрав один из элементов, можно перейти к соответствующей странице сведений. Кроме того Главная страница также включает панель навигации, который содержит кнопку, которую можно использовать для перехода к странице сведения об активном:

- На iOS на панели навигации присутствует в верхней части страницы и содержит кнопки, которая переходит к странице сведений. Кроме того сведения об активном страницу можно перейти, проведя главной страницы слева.
- На Android на панели навигации в верхней части страницы и отображает заголовок, значок и кнопку, который осуществляет переход к странице сведений. Значок определяется в `[Activity]` атрибут, который оформляет `MainActivity` класса в проекте специфический для платформы Android. Кроме того, сведения об активном страницу можно перейти, проведя главной страницы влево, коснувшись страница сведений в правой части экрана и, нажав *обратно* , расположенную в нижней части экрана.
- На панели навигации на универсальной платформы Windows (UWP), присутствует в верхней части страницы и имеет кнопки перехода к странице сведений.

Подробные данные отображаются страницы, соответствующий элемент выбран в образце страницы и показаны основные компоненты страницу с подробными сведениями в следующих снимках экрана.

![](master-detail-page-images/detailpage-components.png "Компоненты страницы сведений")

Страница сведений содержит панель навигации, содержимое которого зависят от платформы.

- На iOS, на панели навигации присутствует в верхней части страницы отображается заголовок и имеет кнопку, которая возвращает для главной страницы, при условии, что экземпляр страницы сведений упаковывается в [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) экземпляра. Кроме того главной страницы можно вернуть в путем считывания страницы сведений справа.
- На Android панель навигации в верхней части страницы и отображает заголовок, значок и кнопку, которая возвращает на главную страницу. Значок определяется в `[Activity]` атрибут, который оформляет `MainActivity` класса в проекте специфический для платформы Android.
- На UWP на панели навигации в верхней части страницы и отображается заголовок и имеет кнопку, которая возвращает на главную страницу.

### <a name="navigation-behavior"></a>Поведения навигации

Это происходит взаимодействие переходов между основной страницы и страницы сведений зависит от используемой платформы:

- На странице сведений iOS *слайды* справа как слайды главной страницы из слева и левая часть сведений по-прежнему отображается страница.
- В Android подробные сведения и образец страницы являются *накладывается* друг от друга.
- На UWP, подробные сведения и образец страницы являются *местами*.

Аналогичное поведение будет наблюдаться в альбомном режиме, за исключением того, главной страницы в iOS и Android имеет аналогичные ширину как Главная страница в книжной ориентации, чтобы более подробно страницы будут отображаться.

Дополнительные сведения об управлении поведения навигации в разделе [управление поведение при отображении страницы сведений](#Controlling_the_Detail_Page_Display_Behavior).

## <a name="creating-a-masterdetailpage"></a>Создание MasterDetailPage

Объект [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) содержит [ `Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/) и [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/) свойства, которые имеют тип [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/), которые используются для получения и задания страниц главных и подчиненных соответственно.

> [!IMPORTANT]
> Объект [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) предназначен для корневую страницу и использовать его как на дочернюю страницу в другие типы страниц может привести к непредвиденным и несогласованное поведение. Кроме того, рекомендуется на главную страницу [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) всегда должен быть [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) экземпляра и что страницу подробных сведений о заполняются только с [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/), [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/), и `ContentPage` экземпляры. Это поможет обеспечить целостное взаимодействие с пользователем на всех платформах.

В следующем примере показан код XAML [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) , которая устанавливает [ `Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/) и [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/) свойства:

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

В следующем примере кода показан соответствующий [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) созданных в C#:

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

[ `MasterDetailPage.Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/) Свойству [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) экземпляра. [ `MasterDetailPage.Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/) Свойству [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) содержащий `ContentPage` экземпляра.

### <a name="creating-the-master-page"></a>Создание главной страницы

В следующем примере кода XAML показано объявление `MasterPage` объекта, который осуществляется с помощью [ `MasterDetailPage.Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/) свойства:

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

Страница состоит из [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) , заполняется данными в XAML, установив его [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemsSource/) свойства в виде массива `MasterPageItem` экземпляров. Каждый `MasterPageItem` определяет `Title`, `IconSource`, и `TargetType` свойства.

Объект [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) назначается [ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemTemplate/) свойство для отображения `MasterPageItem`. `DataTemplate` Содержит [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) , включающая в себя [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) и [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/). [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) Отображает `IconSource` значение свойства и [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) отображает `Title` значение свойства, для каждого `MasterPageItem`.

На странице его [ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Title/) и [ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Icon/) набором свойств. Значок будет отображаться на странице сведений при условии, что страница сведений имеет заголовок. Оно должно быть включено в iOS, заключив экземпляр страницы сведений в [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) экземпляра.

> [!NOTE]
> [ `MasterDetailPage.Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/) Страницы должен быть его [ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Title/) свойство или возникнет исключение.

В следующем примере кода показан эквивалентный страницы, созданные на языке C#:

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

Следующих снимках экрана показано главной страницы для каждой платформы.

![](master-detail-page-images/masterpage.png "Пример страницы основных")

### <a name="creating-and-displaying-the-detail-page"></a>Создание и отображение страницу с подробными сведениями

`MasterPage` Содержит экземпляр `ListView` свойство, которое обеспечивает доступ к его [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) экземпляра, чтобы `MainPage` [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) можно зарегистрировать экземпляр обработчик событий для обработки [ `ItemSelected` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemSelected/) событий. Это позволяет `MainPage` для установки [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/) свойства страницы, которую представляет выбранный `ListView` элемента. В следующем примере кода показан обработчик событий:

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

- Он извлекает [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.SelectedItem/) из [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) экземпляром и если он не `null`, задает страницу с подробными сведениями на новый экземпляр типа страницы, хранящиеся в `TargetType`свойство `MasterPageItem`. Тип страницы упаковывается в [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) экземпляра, чтобы убедиться, что значок для ссылок на [ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Icon/) свойство `MasterPage` отображается на странице сведений в iOS.
- Элемент, выбранный в [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) равно `null` чтобы убедиться, что ни один из `ListView` элементы будут выбираться при очередном `MasterPage` представлены.
- Страницы сведений о представляется пользователю, задав [ `MasterDetailPage.IsPresented` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.IsPresented/) свойства `false`. Это свойство определяет, выводится ли страница основной или подробности. Должно быть задано `true` для отображения главной страницы, а в `false` отображение страницы «подробности».

В следующих снимках экрана показано `ContactPage` странице сведений отображается после выбран на главной странице:

![](master-detail-page-images/detailpage.png "Пример страницы сведений")

<a name="Controlling_the_Detail_Page_Display_Behavior" />

### <a name="controlling-the-detail-page-display-behavior"></a>Управление поведением отображения страницы сведений

Как [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) управляет страницами главных и подчиненных зависит от того, работает ли приложение на телефон или планшетный ПК, ориентации устройства, а значение [ `MasterBehavior` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.MasterBehavior/) свойство. Это свойство определяет, как будет отображаться страницу с подробными сведениями. Возможными значениями являются:

- **По умолчанию** — страницы отображаются с использованием платформы по умолчанию.
- **Popover** — страницу подробных сведений о охватывает или частично рассматриваются главной страницы.
- **Разбиение** — слева отображается главной страницы и страницы сведений находится справа.
- **SplitOnLandscape** — С разделенным экраном используется в том случае, когда устройство находится в альбомной ориентации.
- **SplitOnPortrait** — С разделенным экраном используется в том случае, когда устройство находится в книжной ориентации.

В следующем примере кода XAML показано, как задать [ `MasterBehavior` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.MasterBehavior/) свойство [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/):

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  x:Class="MasterDetailPageNavigation.MainPage"
                  MasterBehavior="Popover">
  ...
</MasterDetailPage>
```

В следующем примере кода показан соответствующий [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) созданных в C#:

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

Тем не менее значение [ `MasterBehavior` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.MasterBehavior/) свойство влияет только на приложения, запущенные на планшетных ПК или на рабочем столе. Приложения, работающие на телефонах, всегда иметь *Popover* поведение.

## <a name="summary"></a>Сводка

В этой статье показано, как использовать [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) и переходить между его страницы данных. Xamarin.Forms `MasterDetailPage` — страница, которая управляет двумя страницами связанных данных, — Главная страница, которая представляет элементы и страницу сведений, которая представляет сведения об элементах на главной странице.


## <a name="related-links"></a>Связанные ссылки

- [Создание страницы](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [MasterDetailPage (пример)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/MasterDetailPage/)
- [MasterDetailPage](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)
