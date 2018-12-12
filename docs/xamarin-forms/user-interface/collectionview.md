---
title: Xamarin.Forms CollectionView
description: CollectionView — это гибкий и эффективный представление для представления списков данных, используя спецификации другой макет.
ms.prod: xamarin
ms.assetid: 2BC9B223-2D5C-4B09-849C-B9D578954557
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2018
ms.openlocfilehash: 46ab8169b31624e3862cf14f477bcd6cf8c8f3a3
ms.sourcegitcommit: 408b78dd6eded4696469e316af7922a5991f2211
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53246312"
---
# <a name="xamarinforms-collectionview"></a>Xamarin.Forms CollectionView

![Предварительный просмотр](~/media/shared/preview.png)

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

`CollectionView` Просмотр для представления списков данных с помощью спецификации другой макет. Оно предоставляет предоставляют более гибкий и эффективной альтернативой для `ListView`. Хотя `CollectionView` и `ListView` интерфейсы API похожи, существуют некоторые важные различия:

- `CollectionView` не поддерживает понятие ячеек. Вместо этого шаблоны данных используются для определения внешнего вида каждого элемента данных в списке.
- `CollectionView` уменьшает область API `ListView`. Многие свойства и события из `ListView` не существуют в `CollectionView`.
- `CollectionView` имеет модель гибкий макет, что позволяет представлять горизонтально или вертикально, в списке или сетке данные.

Объект `CollectionView` потребляется, задав его `ItemsSource` свойство к любой коллекции, реализующей `IEnumerable`и его `ItemTemplate` свойства `DataTemplate` , определяющий каждого внешнего вида элемента списка. Кроме того его `ItemsLayout` свойству должно быть присвоено `ItemsLayout` класса, чтобы определить Спецификация расположения для списка.

> [!IMPORTANT]
> `CollectionView` Предварительная версия в данный момент, а не имеет большую часть его запланированные функциональные возможности. Кроме того API будет меняться по мере завершения реализации.

`CollectionView` доступен в Xamarin.Forms 4.0-pre1. Тем не менее, он является в настоящее время экспериментальным и может использоваться только, добавив следующую строку кода, чтобы ваши `AppDelegate` класс на iOS или для вашей `MainActivity` класс на устройствах Android, перед вызовом метода `Forms.Init`:

```csharp
Forms.SetFlags("CollectionView_Experimental");
```

## <a name="populating-a-collectionview-with-data"></a>Заполнение CollectionView с данными

Объект `CollectionView` заполняется данными, задав его `ItemSource` свойство к любой коллекции, реализующий `IEnumerable`. Элементы могут быть добавлены в XAML путем инициализации `ItemsSource` свойства из массива элементов:

```xaml
<CollectionView>
  <CollectionView.ItemsSource>
    <x:Array Type="{x:Type x:String}">
      <x:String>Baboon</x:String>
      <x:String>Capuchin Monkey</x:String>
      <x:String>Blue Monkey</x:String>
      <x:String>Squirrel Monkey</x:String>
      <x:String>Golden Lion Tamarin</x:String>
      <x:String>Howler Monkey</x:String>
      <x:String>Japanese Macaque</x:String>
    </x:Array>
  </CollectionView.ItemsSource>
</CollectionView>
```

> [!NOTE]
> Обратите внимание, что `x:Array` элемента требуется `Type` атрибут, указывающий, тип элементов в массиве.

Кроме того `CollectionView` могут заполняться с помощью данных с помощью привязки данных для привязки его `ItemsSource` свойства `IEnumerable` коллекции. В XAML это достигается за счет `Binding` расширение разметки:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}" />
```

В этом примере `ItemsSource` свойство данных привязывает к `Monkeys` свойство модели связанное представление, которое возвращает `IList<Monkey>` коллекции. В следующем коде показано в примере `Monkey` класс, который содержит четыре свойства:

```csharp
public class Monkey
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

## <a name="providing-a-view-for-the-empty-state"></a>Благодаря представлению для пустого состояния

Когда `CollectionView` не содержит данных для отображения, соответствующее сообщение может отображаться пользователю. Это достигается путем установки `CollectionView.EmptyView` свойства `object` , будет отображаться, если этот параметр указан коллекции `ItemSource` свойство пусто:

```xaml
<CollectionView ItemsSource="{Binding EmptyMonkeys}">
    <CollectionView.EmptyView>
        <Label Text="No items to display" />
    </CollectionView.EmptyView>
</CollectionView>
```

`EmptyView` Можно присвоить свойство `string`, привязки или представления и поэтому может включать интерактивное содержимое, если это необходимо.

Кроме того `EmptyViewTemplate` свойству может быть присвоено `DataTemplate` , будет использоваться для форматирования `EmptyView`.

## <a name="defining-list-item-appearance"></a>Определение внешнего вида элемента списка

Внешний вид данных для каждого элемента в `CollectionView` может быть определена путем задания `CollectionView.ItemTemplate` свойства `DataTemplate`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="Auto" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
    ...
</CollectionView>
```

В этом примере `DataTemplate` содержит `Grid` с `Image`и два `Label` экземпляров, что все привязки к свойствам объекта `Monkey` объекта.

Дополнительные сведения о шаблонах данных см. в разделе [шаблоны данных](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="specifying-a-layout"></a>Указание макета

По умолчанию `CollectionView` будет отображения элементов в вертикальном списке. Тем не менее любой из нижеследующих спецификаций макета можно использовать:

- Вертикальный список — список один столбец, по мере добавления новых элементов увеличивается по вертикали.
- Горизонтальный список — список одну строку, по мере добавления новых элементов увеличивается по горизонтали.
- Вертикальная сетка — сетка нескольких столбцов, по мере добавления новых элементов увеличивается по вертикали.
- Горизонтальная сетка — сетка нескольких строк, по мере добавления новых элементов увеличивается по горизонтали.

Эти макеты можно задать, присвоив `CollectionView.ItemsLayout` свойства `ItemsLayout` класса, с помощью `ListItemsLayout` класс, предоставляющий структуру списка и `GridItemsLayout` класс, предоставляющий макет сетки.

`ItemsLayout` Класс определяет `Orientation` свойство типа `ItemsLayoutOrientation`. `ItemsLayoutOrientation` Перечисление определяет `Vertical` и `Horizontal` значения членов.

`ListItemsLayout` Класс наследует от `ItemsLayout` класса и определяет статические `VerticalList` и `HorizontalList` членов. Эти элементы можно создать вертикальные или горизонтальные списки, соответственно. Кроме того `ListItemsLayout` может быть создан экземпляр, указав `ItemsLayoutOrientation` член перечисления в качестве аргумента.

`GridItemsLayout` Класс наследует от `ItemsLayout` класса и определяет `Span` свойство типа `int`, который представляет количество столбцов или строк, отображаемых в сетке. Значение по умолчанию `Span` свойства-1, и его значение всегда должно быть больше или равно 1.

### <a name="vertical-list"></a>Вертикальный список

По умолчанию `CollectionView` будет отображения элементов в вертикальном списке макета. Таким образом, нет необходимости задать `ItemsLayout` свойства, используемого для этой структуры:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="Auto" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

Однако для полноты информации `CollectionView` можно задать для отображения элементов в вертикальном списке, задав его `ItemsLayout` статического `ListItemsLayout.VerticalList` член:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="{x:Static ListItemsLayout.VerticalList}">
    ...
</CollectionView>
```

Кроме того, это можно также сделать, задав `ItemsLayout` свойства к экземпляру `ListItemsLayout` класса, указав `Vertical` `ItemsLayoutOrientation` член перечисления в качестве аргумента:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout>
            <x:Arguments>
                <ItemsLayoutOrientation>Vertical</ItemsLayoutOrientation>    
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

Это приводит список один столбец, который увеличивается по вертикали добавляются новые элементы:

[![Макет вертикальный список CollectionView](collectionview-images/vertical-list.png "CollectionView вертикальный список макета")](collectionview-images/vertical-list-large.png#lightbox)

### <a name="horizontal-list"></a>Горизонтальный список

Объект `CollectionView` отображения элементов в списке по горизонтали, задав его `ItemsLayout` свойство статического `ListItemsLayout.HorizontalList` член:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="{x:Static ListItemsLayout.HorizontalList}">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="35" />
                    <RowDefinition Height="35" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="70" />
                    <ColumnDefinition Width="140" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold"
                       LineBreakMode="TailTruncation" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       LineBreakMode="TailTruncation"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

Кроме того, это можно также сделать, задав `ItemsLayout` свойства к экземпляру `ListItemsLayout` класса, указав `Horizontal` `ItemsLayoutOrientation` член перечисления в качестве аргумента:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout>
            <x:Arguments>
                <ItemsLayoutOrientation>Horizontal</ItemsLayoutOrientation>    
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

Это приводит список одной строки, который увеличивается по горизонтали как добавляются новые элементы:

[![Макет горизонтальный список CollectionView](collectionview-images/horizontal-list.png "CollectionView макет горизонтальный список")](collectionview-images/horizontal-list-large.png#lightbox)

### <a name="vertical-grid"></a>Вертикальную сетку

Объект `CollectionView` отображения элементов в вертикальную сетку, задав его `ItemsLayout` свойства `GridItemsLayout` экземпляра которого `Orientation` свойству `Vertical`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Vertical"
                        Span="2" />
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="35" />
                    <RowDefinition Height="35" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="70" />
                    <ColumnDefinition Width="80" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold"
                       LineBreakMode="TailTruncation" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       LineBreakMode="TailTruncation"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

По умолчанию вертикальный `GridItemsLayout` будет отображать элементы в одном столбце. Тем не менее, в этом примере устанавливается `GridItemsLayout.Span` значение 2. Это приводит к таблице два столбца, которая по мере добавления новых элементов увеличивается по вертикали:

[![Макет вертикальную сетку CollectionView](collectionview-images/vertical-grid.png "CollectionView вертикальной сетки макета")](collectionview-images/vertical-grid-large.png#lightbox)

### <a name="horizontal-grid"></a>Горизонтальная сетка

Объект `CollectionView` отображения элементов в виде горизонтальной сетки, задав его `ItemsLayout` свойства `GridItemsLayout` экземпляра которого `Orientation` свойству `Horizontal`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Horizontal"
                        Span="4" />
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="35" />
                    <RowDefinition Height="35" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="70" />
                    <ColumnDefinition Width="140" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold"
                       LineBreakMode="TailTruncation" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       LineBreakMode="TailTruncation"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

По умолчанию горизонтального `GridItemsLayout` будут доступны элементы в одну строку. Тем не менее, в этом примере устанавливается `GridItemsLayout.Span` значение 4. Это приводит к сетке четыре строки по мере добавления новых элементов увеличивается по горизонтали:

[![Макет сетки по горизонтали CollectionView](collectionview-images/horizontal-grid.png "CollectionView горизонтальной сетки макета")](collectionview-images/horizontal-grid-large.png#lightbox)

## <a name="scrolling"></a>Прокрутка

`CollectionView` можно прокрутить запрошенных элементов в представлении с помощью `ScrollTo` метод. Этот метод прокручивает элемент по указанному индексу в представление:

```csharp
_collectionView.ScrollTo(12);
```

В качестве альтернативы можно использовать перегрузку этого метода для прокрутки указанный элемент в представлении:

```csharp
var viewModel = BindingContext as MonkeysViewModel;
var monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
_collectionView.ScrollTo(monkey);
```

Обе перегрузки позволяют дополнительных аргументов, указывать на группу, которую он находится, точную позицию элемента после прокрутки имеет завершенной (запуск, center, end) и следует ли анимировать прокрутки.

## <a name="related-links"></a>Связанные ссылки

- [CollectionView (пример)](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
