---
title: Создание шаблона данных Xamarin.Forms DataTemplate
description: Шаблоны данных могут создаваться встроенный в ResourceDictionary, или из пользовательского типа или соответствующий тип ячейки Xamarin.Forms. В этой статье рассматриваются каждому из них.
ms.prod: xamarin
ms.assetid: CFF4AB5E-9069-461C-84D8-F9F6C38510AB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 63f9bf82bc8e637aced1afa5d5699ac1e8dc3f8c
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994619"
---
# <a name="creating-a-xamarinforms-datatemplate"></a>Создание шаблона данных Xamarin.Forms DataTemplate

_Шаблоны данных могут создаваться встроенный в ResourceDictionary, или из пользовательского типа или соответствующий тип ячейки Xamarin.Forms. В этой статье рассматриваются каждому из них._

Стандартный сценарий использования [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) отображает данные из коллекции объектов в [ `ListView` ](xref:Xamarin.Forms.ListView). Внешний вид данных для каждой ячейки в [ `ListView` ](xref:Xamarin.Forms.ListView) можно управлять, задав [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1) свойства [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate). Существует несколько способов, которые могут использоваться для выполнения этой задачи:

- [Создание встроенных DataTemplate](#inline).
- [Создание шаблона данных DataTemplate с типом](#type).
- [Создание шаблона данных DataTemplate как ресурса](#resource).

Независимо от того, используется метод, результатом является, внешний вид каждой ячейки в [ `ListView` ](xref:Xamarin.Forms.ListView) определяется [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate), как показано на следующем снимке экрана:

![](creating-images/data-template-appearance.png "ListView с помощью DataTemplate")

<a name="inline" />

## <a name="creating-an-inline-datatemplate"></a>Создание встроенных DataTemplate

[ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1) Может быть установлено на встроенный [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate). Встроенный шаблон, который — это помещается в качестве ближайшего дочернего свойства соответствующего элемента управления, следует использовать, если нет необходимости повторно использовать шаблон данных в другом месте. Элементы, указанные в `DataTemplate` определяют внешний вид каждой ячейки, как показано в следующем примере кода XAML:

```xaml
<ListView Margin="0,20,0,0">
    <ListView.ItemsSource>
        <x:Array Type="{x:Type local:Person}">
            <local:Person Name="Steve" Age="21" Location="USA" />
            <local:Person Name="John" Age="37" Location="USA" />
            <local:Person Name="Tom" Age="42" Location="UK" />
            <local:Person Name="Lucas" Age="29" Location="Germany" />
            <local:Person Name="Tariq" Age="39" Location="UK" />
            <local:Person Name="Jane" Age="30" Location="USA" />
        </x:Array>
    </ListView.ItemsSource>
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <Grid>
                    ...
                    <Label Text="{Binding Name}" FontAttributes="Bold" />
                    <Label Grid.Column="1" Text="{Binding Age}" />
                    <Label Grid.Column="2" Text="{Binding Location}" HorizontalTextAlignment="End" />
                </Grid>
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

Дочерним элементом элемента встроенный [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) должна быть, или являются производными от, введите [ `ViewCell` ](xref:Xamarin.Forms.ViewCell). Макет внутри `ViewCell` управляется здесь [ `Grid` ](xref:Xamarin.Forms.Grid). `Grid` Содержит три [ `Label` ](xref:Xamarin.Forms.Label) экземпляров привязать их [ `Text` ](xref:Xamarin.Forms.Label.Text) свойства к соответствующим свойствам каждого `Person` объекта в коллекции.

Эквивалентный код на языке C# показан в следующем примере:

```csharp
public class WithDataTemplatePageCS : ContentPage
{
    public WithDataTemplatePageCS()
    {
        ...
        var people = new List<Person>
        {
            new Person { Name = "Steve", Age = 21, Location = "USA" },
            ...
        };

        var personDataTemplate = new DataTemplate(() =>
        {
            var grid = new Grid();
            ...
            var nameLabel = new Label { FontAttributes = FontAttributes.Bold };
            var ageLabel = new Label();
            var locationLabel = new Label { HorizontalTextAlignment = TextAlignment.End };

            nameLabel.SetBinding(Label.TextProperty, "Name");
            ageLabel.SetBinding(Label.TextProperty, "Age");
            locationLabel.SetBinding(Label.TextProperty, "Location");

            grid.Children.Add(nameLabel);
            grid.Children.Add(ageLabel, 1, 0);
            grid.Children.Add(locationLabel, 2, 0);

            return new ViewCell { View = grid };
        });

        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children = {
                ...
                new ListView { ItemsSource = people, ItemTemplate = personDataTemplate, Margin = new Thickness(0, 20, 0, 0) }
            }
        };
    }
}
```

В C#, встроенную [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) создается с использованием перегрузки конструктора, которая указывает `Func` аргумент.

<a name="type" />

## <a name="creating-a-datatemplate-with-a-type"></a>Создание шаблона данных DataTemplate с типом

[ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1) Свойство также может быть присвоено [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) , созданный из типа ячейки. Преимуществом такого подхода является то, что внешний вид, определяемый тип ячейки могут быть использованы несколько шаблонов данных во всем приложении. В следующем коде XAML показан пример этого подхода:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataTemplates"
             ...>
    <StackLayout Margin="20">
        ...
        <ListView Margin="0,20,0,0">
           <ListView.ItemsSource>
                <x:Array Type="{x:Type local:Person}">
                    <local:Person Name="Steve" Age="21" Location="USA" />
                    ...
                </x:Array>
            </ListView.ItemsSource>
            <ListView.ItemTemplate>
                <DataTemplate>
                    <local:PersonCell />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

Здесь [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1) свойству [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) , созданный из пользовательского типа, который определяет внешний вид ячейки. Пользовательский тип должен быть производным от типа [ `ViewCell` ](xref:Xamarin.Forms.ViewCell), как показано в следующем примере кода:

```xaml
<ViewCell xmlns="http://xamarin.com/schemas/2014/forms"
          xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
          x:Class="DataTemplates.PersonCell">
     <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.5*" />
            <ColumnDefinition Width="0.2*" />
            <ColumnDefinition Width="0.3*" />
        </Grid.ColumnDefinitions>
        <Label Text="{Binding Name}" FontAttributes="Bold" />
        <Label Grid.Column="1" Text="{Binding Age}" />
        <Label Grid.Column="2" Text="{Binding Location}" HorizontalTextAlignment="End" />
    </Grid>
</ViewCell>
```

В рамках [ `ViewCell` ](xref:Xamarin.Forms.ViewCell), макет управляется здесь [ `Grid` ](xref:Xamarin.Forms.Grid). `Grid` Содержит три [ `Label` ](xref:Xamarin.Forms.Label) экземпляров привязать их [ `Text` ](xref:Xamarin.Forms.Label.Text) свойства к соответствующим свойствам каждого `Person` объекта в коллекции.

В следующем примере показан эквивалентный код на C#:

```csharp
public class WithDataTemplatePageFromTypeCS : ContentPage
{
    public WithDataTemplatePageFromTypeCS()
    {
        ...
        var people = new List<Person>
        {
            new Person { Name = "Steve", Age = 21, Location = "USA" },
            ...
        };

        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children = {
                ...
                new ListView { ItemTemplate = new DataTemplate(typeof(PersonCellCS)), ItemsSource = people, Margin = new Thickness(0, 20, 0, 0) }
            }
        };
    }
}
```

В C# [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) создается с использованием перегрузки конструктора, которая задает тип ячейки в качестве аргумента. Тип ячейки должен быть производным от типа [ `ViewCell` ](xref:Xamarin.Forms.ViewCell), как показано в следующем примере кода:

```csharp
public class PersonCellCS : ViewCell
{
    public PersonCellCS()
    {
        var grid = new Grid();
        ...
        var nameLabel = new Label { FontAttributes = FontAttributes.Bold };
        var ageLabel = new Label();
        var locationLabel = new Label { HorizontalTextAlignment = TextAlignment.End };

        nameLabel.SetBinding(Label.TextProperty, "Name");
        ageLabel.SetBinding(Label.TextProperty, "Age");
        locationLabel.SetBinding(Label.TextProperty, "Location");

        grid.Children.Add(nameLabel);
        grid.Children.Add(ageLabel, 1, 0);
        grid.Children.Add(locationLabel, 2, 0);

        View = grid;
    }
}
```

> [!NOTE]
> Обратите внимание, что Xamarin.Forms также включает типов ячеек, которые могут использоваться для отображения данных в [ `ListView` ](xref:Xamarin.Forms.ListView) ячеек. Дополнительные сведения см. в разделе [внешний вид ячейки](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md).

<a name="resource" />

## <a name="creating-a-datatemplate-as-a-resource"></a>Создание шаблона данных DataTemplate как ресурса

Шаблоны данных также могут создаваться как многократно используемые объекты в [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). Это достигается путем предоставления каждое объявление уникальный `x:Key` атрибут, который предоставляет ему описательное ключа в `ResourceDictionary`, как показано в следующем примере кода XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             ...>
    <ContentPage.Resources>
        <ResourceDictionary>
            <DataTemplate x:Key="personTemplate">
                 <ViewCell>
                    <Grid>
                        ...
                    </Grid>
                </ViewCell>
            </DataTemplate>
        </ResourceDictionary>
    </ContentPage.Resources>
    <StackLayout Margin="20">
        ...
        <ListView ItemTemplate="{StaticResource personTemplate}" Margin="0,20,0,0">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type local:Person}">
                    <local:Person Name="Steve" Age="21" Location="USA" />
                    ...
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </StackLayout>
</ContentPage>
```

[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) Назначается [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1) свойства с помощью `StaticResource` расширение разметки. Обратите внимание, что, хотя `DataTemplate` определен на странице [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), его также можно определить на уровне элемента управления или приложения.

В следующем примере кода показана страница эквивалент в C#:

```csharp
public class WithDataTemplatePageCS : ContentPage
{
  public WithDataTemplatePageCS ()
  {
    ...
    var personDataTemplate = new DataTemplate (() => {
      var grid = new Grid ();
      ...
      return new ViewCell { View = grid };
    });

    Resources = new ResourceDictionary ();
    Resources.Add ("personTemplate", personDataTemplate);

    Content = new StackLayout {
      Margin = new Thickness(20),
      Children = {
        ...
        new ListView { ItemTemplate = (DataTemplate)Resources ["personTemplate"], ItemsSource = people };
      }
    };
  }
}
```

[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) Добавляется [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) с помощью [ `Add` ](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)) метод, который указывает `Key` строку, которая используется для Справочник по `DataTemplate` при извлечении.

## <a name="summary"></a>Сводка

Этой статье показано, как создать шаблоны анализа данных, встроенные, из пользовательского типа, или [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). Встроенный шаблон следует использовать, если нет необходимости повторно использовать шаблон данных в другом месте. Кроме того шаблон данных могут использоваться повторно, определив его как пользовательский тип, или как ресурс управления уровня, уровня страницы или уровня приложения.


## <a name="related-links"></a>Связанные ссылки

- [Вид ячейки](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Шаблоны данных (пример)](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplates/)
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)
