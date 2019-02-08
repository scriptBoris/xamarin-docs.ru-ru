---
title: Настройка внешнего вида ячейки ListView
description: В этой статье рассматриваются параметры для представления данных в приложениях Xamarin.Forms, а использование преимуществ удобства элемента управления ListView.
ms.prod: xamarin
ms.assetid: FD45CB91-1A8F-46FB-B432-6BC20492E456
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/07/2016
ms.openlocfilehash: 1e57764dacf7ae0599df46f13bd4f5505c4cc4a2
ms.sourcegitcommit: 93c9fe61eb2cdfa530960b4253eb85161894c882
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55831785"
---
# <a name="customizing-listview-cell-appearance"></a>Настройка внешнего вида ячейки ListView

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/CustomCells)

[`ListView`](xref:Xamarin.Forms.ListView) Представляет прокручиваемые списки, которые можно настраивать с помощью объекта `ViewCell`s. `ViewCells` можно использовать для отображения текста и изображений, указывающее состояние true или false и при получении входных данных пользователя.

<a name="Built_in_Cells" />

## <a name="built-in-cells"></a>Встроенные в ячейках
Xamarin.Forms поставляется с встроенной ячеек, которые работают для многих приложений простой:

- **TextCell** &ndash; для отображения текста
- **ImageCell** &ndash; для отображения изображения с текстом.

Две дополнительные ячейки, [ `SwitchCell` ](~/xamarin-forms/user-interface/tableview.md#switchcell) и [ `EntryCell` ](~/xamarin-forms/user-interface/tableview.md#entrycell) доступны, но не часто используются в соответствии с `ListView`. См. в разделе [ `TableView` ](~/xamarin-forms/user-interface/tableview.md) Дополнительные сведения об этих ячеек.

<a name="TextCell" />

### <a name="textcell"></a>TextCell

[`TextCell`](xref:Xamarin.Forms.TextCell) представляет собой ячейку для отображения текста, при необходимости с вторая строка обозначается как текст сведений.

TextCells подготавливаются к просмотру как собственные элементы управления во время выполнения, поэтому производительность является очень высокой по сравнению с пользовательской `ViewCell`. TextCells являются настраиваемыми, позволяя задавать:

- `Text` &ndash; текст, отображаемый в первой строке крупным шрифтом.
- `Detail` &ndash; текст, который отображается под первой строке, мелким шрифтом.
- `TextColor` &ndash; цвет текста.
- `DetailColor` &ndash; цвет текста детализации

![](customizing-cell-appearance-images/text-cell-default.png "Пример TextCell по умолчанию")

![](customizing-cell-appearance-images/text-cell-custom.png "Пример настраиваемого TextCell")

<a name="ImageCell" />

### <a name="imagecell"></a>ImageCell

[`ImageCell`](xref:Xamarin.Forms.ImageCell), такие как `TextCell`, можно использовать для отображения текста и текст дополнительный сведений, и он предлагает превосходную производительность процессов с помощью собственных элементов управления для каждой платформы. `ImageCell` отличается от `TextCell` тем, что показывает изображения слева от текста.

`ImageCell` полезно, когда необходимо отобразить список данных с помощью вида, такие как список контактов или фильмах. ImageCells являются настраиваемыми, позволяя задавать:

- `Text` &ndash; текст, который отображается в первой строке, крупным шрифтом
- `Detail` &ndash; текст, который отображается под первой строке, меньший размер шрифта
- `TextColor` &ndash; цвет текста
- `DetailColor` &ndash; цвет текста детализации
- `ImageSource` &ndash; изображение, отображаемое рядом с текстом

![](customizing-cell-appearance-images/image-cell-default.png "Пример ImageCell по умолчанию")

![](customizing-cell-appearance-images/image-cell-custom.png "Пример настраиваемого ImageCell")

<a name="customcells" />

## <a name="custom-cells"></a>Пользовательские ячейки
Если встроенные ячеек не предоставляет необходимые макета, пользовательские ячейки реализации требуется макет. Например можно представить ячейки с двух меток, которые имеют равный вес. Объект `TextCell` будет недостаточно поскольку `TextCell` содержит одну метку, меньший по размеру. Большая часть настройки ячейку добавьте дополнительные данные только для чтения (например, дополнительные метки, изображения или другие сведения для отображения).

Все пользовательские ячейки должен быть производным от [ `ViewCell` ](xref:Xamarin.Forms.ViewCell), тот же базовый класс, что все ячейки, встроенные типы использования.

В Xamarin.Forms 2 появился новый [поведение кэширования](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy) на `ListView` элемента управления, который можно задать для повышения быстродействия для некоторых типов пользовательских ячеек.

Ниже приведен пример пользовательского ячейки:

![](customizing-cell-appearance-images/custom-cell.png "Пример пользовательской ячейки")

### <a name="xaml"></a>XAML
XAML для создания макета выше приведен ниже:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="demoListView.ImageCellPage">
    <ContentPage.Content>
        <ListView  x:Name="listView">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <StackLayout BackgroundColor="#eee"
                        Orientation="Vertical">
                            <StackLayout Orientation="Horizontal">
                                <Image Source="{Binding image}" />
                                <Label Text="{Binding title}"
                                TextColor="#f35e20" />
                                <Label Text="{Binding subtitle}"
                                HorizontalOptions="EndAndExpand"
                                TextColor="#503026" />
                            </StackLayout>
                        </StackLayout>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </ContentPage.Content>
</ContentPage>
```

Делает гораздо выше XAML. Давайте разобьем ее:

- Пользовательской ячейки вложен в `DataTemplate`, который находится внутри `ListView.ItemTemplate`. Это же процессе, что с помощью любую другую ячейку.
- `ViewCell` — Тип пользовательской ячейки. Дочерним элементом элемента `DataTemplate` элемент должен быть или являются производными от типа `ViewCell`.
- Обратите внимание, что его в `ViewCell`, макет находится под управлением `StackLayout`. Этот макет позволяет настроить цвет фона. Обратите внимание, что любое свойство `StackLayout` то есть привязки может привязать внутри пользовательских ячейки, несмотря на то, что этот процесс не показан здесь.
- Внутри `ViewCell`, макет может управляться любой макет Xamarin.Forms. 

### <a name="cnum"></a>C&num;

Указание пользовательских ячейки в C# — чуть более подробным, чем эквивалент XAML. Например:

Во-первых, определите пользовательский класс ячейки, с помощью `ViewCell` как базовый класс:

```csharp
public class CustomCell : ViewCell
    {
        public CustomCell()
        {
            //instantiate each of our views
            var image = new Image ();
            StackLayout cellWrapper = new StackLayout ();
            StackLayout horizontalLayout = new StackLayout ();
            Label left = new Label ();
            Label right = new Label ();

            //set bindings
            left.SetBinding (Label.TextProperty, "title");
            right.SetBinding (Label.TextProperty, "subtitle");
            image.SetBinding (Image.SourceProperty, "image");

            //Set properties for desired design
            cellWrapper.BackgroundColor = Color.FromHex ("#eee");
            horizontalLayout.Orientation = StackOrientation.Horizontal;
            right.HorizontalOptions = LayoutOptions.EndAndExpand;
            left.TextColor = Color.FromHex ("#f35e20");
            right.TextColor = Color.FromHex ("503026");

            //add views to the view hierarchy
            horizontalLayout.Children.Add (image);
            horizontalLayout.Children.Add (left);
            horizontalLayout.Children.Add (right);
            cellWrapper.Children.Add (horizontalLayout);
            View = cellWrapper;
        }
    }
```

В конструкторе для страницы с `ListView`, задайте ListView `ItemTemplate` свойство в новый `DataTemplate`:

```csharp
public partial class ImageCellPage : ContentPage
    {
        public ImageCellPage ()
        {
            InitializeComponent ();
            listView.ItemTemplate = new DataTemplate (typeof(CustomCell));
        }
    }
```

Обратите внимание, что конструктор `DataTemplate` принимает значение типа. Оператор typeof возвращает тип среды CLR для `CustomCell`.

<a name="binding-context-changes" />

### <a name="binding-context-changes"></a>Изменения контекста привязки

При привязке к ячейки пользовательского типа [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) экземпляров, элементы управления пользовательского интерфейса, отображающие `BindableProperty` значения следует использовать [ `OnBindingContextChanged` ](xref:Xamarin.Forms.Cell.OnBindingContextChanged) переопределение, чтобы задать данные для отображения в Каждая ячейка, а не конструктор ячейки, как показано в следующем примере кода:

```csharp
public class CustomCell : ViewCell
{
    Label nameLabel, ageLabel, locationLabel;

    public static readonly BindableProperty NameProperty =
        BindableProperty.Create ("Name", typeof(string), typeof(CustomCell), "Name");
    public static readonly BindableProperty AgeProperty =
        BindableProperty.Create ("Age", typeof(int), typeof(CustomCell), 0);
    public static readonly BindableProperty LocationProperty =
        BindableProperty.Create ("Location", typeof(string), typeof(CustomCell), "Location");

    public string Name {
        get { return(string)GetValue (NameProperty); }
        set { SetValue (NameProperty, value); }
    }

    public int Age {
        get { return(int)GetValue (AgeProperty); }
        set { SetValue (AgeProperty, value); }
    }

    public string Location {
        get { return(string)GetValue (LocationProperty); }
        set { SetValue (LocationProperty, value); }
    }
    ...

    protected override void OnBindingContextChanged ()
    {
        base.OnBindingContextChanged ();

        if (BindingContext != null) {
            nameLabel.Text = Name;
            ageLabel.Text = Age.ToString ();
            locationLabel.Text = Location;
        }
    }
}
```

[ `OnBindingContextChanged` ](xref:Xamarin.Forms.Cell.OnBindingContextChanged) Переопределение будет выполнен при вызове [ `BindingContextChanged` ](xref:Xamarin.Forms.BindableObject.BindingContextChanged) вызывает событие, в ответ на значение [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) изменение свойства. Таким образом, когда `BindingContext` изменяется, элементы управления пользовательского интерфейса, отображающие [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) значения следует задать свои данные. Обратите внимание, что `BindingContext` должны проверяться `null` значение, как это может быть переведена с Xamarin.Forms для сборки мусора, что в свою очередь приведет к `OnBindingContextChanged` переопределить вызова.

Кроме того, можно привязать элементы управления пользовательского интерфейса к [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) экземпляров, чтобы отобразить их значения, которые устраняет необходимость в Переопределите `OnBindingContextChanged` метод.

> [!NOTE]
> При переопределении метода `OnBindingContextChanged`, убедитесь, что базовый класс `OnBindingContextChanged` метод вызывается, чтобы зарегистрированные делегаты получили `BindingContextChanged` событий.

В XAML привязка пользовательского типа ячейки к данным может осуществляться как показано в следующем примере кода:

```xaml
<ListView x:Name="listView">
    <ListView.ItemTemplate>
        <DataTemplate>
            <local:CustomCell Name="{Binding Name}" Age="{Binding Age}" Location="{Binding Location}" />
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

Этот код привязывает `Name`, `Age`, и `Location` свойства связывания в `CustomCell` экземпляра, `Name`, `Age`, и `Location` свойства каждого объекта в базовой коллекции.

В следующем примере кода показан эквивалентный привязки на языке C#:

```csharp
var customCell = new DataTemplate (typeof(CustomCell));
customCell.SetBinding (CustomCell.NameProperty, "Name");
customCell.SetBinding (CustomCell.AgeProperty, "Age");
customCell.SetBinding (CustomCell.LocationProperty, "Location");

var listView = new ListView {
    ItemsSource = people,
    ItemTemplate = customCell
};
```

В iOS и Android Если [ `ListView` ](xref:Xamarin.Forms.ListView) перезапускается элементов и пользовательской ячейки использует пользовательское средство отрисовки, пользовательское средство отрисовки должен правильно реализовывать уведомления об изменении свойства. При повторно ячеек используются значения свойств изменится при обновлении контекста привязки, доступные ячейки, с помощью `PropertyChanged` вызванных событий. Дополнительные сведения см. в разделе [Настройка ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md). Дополнительные сведения о перезапуске ячейки, см. в разделе [стратегии кэширования](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy).

## <a name="related-links"></a>Связанные ссылки

- [Встроенные в ячейках (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/BuiltInCells)
- [Пользовательские ячейки (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/CustomCells)
- [Привязка контекст изменен (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/BindingContextChanged)
