---
title: Настройка внешнего вида ListView
description: В этой статье объясняется, как настроить ListViews в приложениях Xamarin.Forms с помощью заголовки, нижние колонтитулы, групп и переменной высоты ячейки.
ms.prod: xamarin
ms.assetid: DC8009B0-4371-4D60-885A-5362FC7EE3E5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 1326a1326b4a88459e4e0a01ef590e770e3a88c0
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997352"
---
# <a name="customizing-listview-appearance"></a>Настройка внешнего вида ListView

`ListView` содержит параметры для управления представлением общий список, в дополнение к основной `ViewCell`s. Возможны следующие значения.

- [**Группирование** ](#Grouping) &ndash; группировать элементы в ListView для упрощения навигации и усовершенствование структуры.
- [**Верхние и нижние колонтитулы** ](#Headers_and_Footers) &ndash; отображения сведений в начале и конце представление, которое прокручивается вместе с другими элементами.
- [**Разделители строк** ](#Row_Separators) &ndash; Отображение или скрытие разделительных линий между элементами.
- [**Переменной высоты строк** ](#Row_Heights) &ndash; по умолчанию все строки имеют одинаковую высоту, но это можно изменить, чтобы разрешить строки с разными высоты для отображения.

<a name="Grouping" />

## <a name="grouping"></a>Группирование
Часто большие наборы данных может оказаться неудобной, при представлении в виде постоянно прокручиваемого списка. Включение группирования можно улучшить взаимодействие с пользователем в таких случаях лучше организации содержимого и активации элементов управления платформы, которые упрощают перемещения по данным.

При активации для группирования `ListView`, добавляется строка заголовка для каждой группы.

Чтобы включить группирования:

- Создайте список списков (список групп, каждая группа, что список элементов).
- Задайте `ListView`в `ItemsSource` для этого списка.
- Задайте `IsGroupingEnabled` значение true.
- Задайте [ `GroupDisplayBinding` ](xref:Xamarin.Forms.ListView.GroupDisplayBinding) для привязки к свойству из групп, который используется в качестве заголовка группы.
- [Необязательно] Задайте [ `GroupShortNameBinding` ](xref:Xamarin.Forms.ListView.GroupShortNameBinding) для привязки к свойству из групп, который используется в качестве краткого имени для группы. Короткое имя используется для списки переходов (столбец справа в iOS).

Начните с создания класса для групп:

```csharp
public class PageTypeGroup : List<PageModel>
    {
        public string Title { get; set; }
        public string ShortName { get; set; } //will be used for jump lists
        public string Subtitle { get; set; }
        private PageTypeGroup(string title, string shortName)
        {
            Title = title;
            ShortName = shortName;
        }

        public static IList<PageTypeGroup> All { private set; get; }
    }
```

В приведенном выше коде `All` является списком, которое будет назначено наших ListView, как источник привязки. `Title` и `ShortName` являются свойствами, которые будут использоваться для заголовков групп.

На этом этапе `All` — пустой список. Добавьте статический конструктор, таким образом, список будет заполнен при запуске программы:

```csharp
static PageTypeGroup()
{
    List<PageTypeGroup> Groups = new List<PageTypeGroup> {
            new PageTypeGroup ("Alfa", "A"){
                new PageModel("Amelia", "Cedar", new switchCellPage(),""),
                new PageModel("Alfie", "Spruce", new switchCellPage(), "grapefruit.jpg"),
                new PageModel("Ava", "Pine", new switchCellPage(), "grapefruit.jpg"),
                new PageModel("Archie", "Maple", new switchCellPage(), "grapefruit.jpg")
            },
            new PageTypeGroup ("Bravo", "B"){
                new PageModel("Brooke", "Lumia", new switchCellPage(),""),
                new PageModel("Bobby", "Xperia", new switchCellPage(), "grapefruit.jpg"),
                new PageModel("Bella", "Desire", new switchCellPage(), "grapefruit.jpg"),
                new PageModel("Ben", "Chocolate", new switchCellPage(), "grapefruit.jpg")
            }
        }
        All = Groups; //set the publicly accessible list
}
```

В приведенном выше коде можно также вызвать `Add` над элементами `groups`, которые являются экземплярами типа `PageTypeGroup`. Это возможно, так как `PageTypeGroup` наследует от `List<PageModel>`. Ниже приведен пример списка шаблон списки, указанным выше.

Ниже приведен XAML для отображения Сгруппированный список.

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="DemoListView.GroupingViewPage"
    <ContentPage.Content>
        <ListView  x:Name="GroupedView"
        GroupDisplayBinding="{Binding Title}"
        GroupShortNameBinding="{Binding ShortName}"
        IsGroupingEnabled="true">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Title}"
                     Detail="{Binding Subtitle}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </ContentPage.Content>
</ContentPage>
```

Эти действия произведут следующий результат:

![](customizing-list-appearance-images/grouping-depth.png "Пример группировки ListView")

Обратите внимание, что у нас есть:

- Задайте `GroupShortNameBinding` для `ShortName` свойство, определенное в нашем классе группы
- Задайте `GroupDisplayBinding` для `Title` свойство, определенное в нашем классе группы
- Задайте `IsGroupingEnabled` значение true
- Изменить `ListView`в `ItemsSource` в сгруппированный список

### <a name="customizing-grouping"></a>Настройка группирования

Если включено группирование в списке, можно настроить и заголовок группы.

Аналогично тому, как `ListView` имеет `ItemTemplate` для определения отображения строк, `ListView` имеет `GroupHeaderTemplate`.

Ниже показан пример настройки заголовка группы в XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="DemoListView.GroupingViewPage">
    <ContentPage.Content>
        <ListView x:Name="GroupedView"
         GroupDisplayBinding="{Binding Title}"
         GroupShortNameBinding="{Binding ShortName}"
         IsGroupingEnabled="true">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Title}"
                    Detail="{Binding Subtitle}"
                    TextColor="#f35e20"
                    DetailColor="#503026" />
                </DataTemplate>
            </ListView.ItemTemplate>
            <!-- Group Header Customization-->
            <ListView.GroupHeaderTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Title}"
                    Detail="{Binding ShortName}"
                    TextColor="#f35e20"
                    DetailColor="#503026" />
                </DataTemplate>
            </ListView.GroupHeaderTemplate>
            <!-- End Group Header Customization -->
        </ListView>
    </ContentPage.Content>
</ContentPage>
```

<a name="Headers_and_Footers" />

## <a name="headers-and-footers"></a>Верхние и нижние колонтитулы
Существует возможность ListView для представления верхний и нижний колонтитул, которые можно прокручивать с элементами списка. Верхний и нижний колонтитул могут представлять собой строки текста или более сложный макет. Обратите внимание, что это отдельно от [разделе группы](#Grouping).

Можно задать `Header` и/или `Footer` в простую строку значение, либо задать их в более сложном макете.
Существуют также `HeaderTemplate` и `FooterTemplate` свойства, позволяющие создавать более сложные макеты для заголовка и нижнего колонтитула, поддерживающих привязку данных.

Чтобы создать простой верхний/нижний колонтитул, просто установите свойства верхний или нижний колонтитул для текста, который будет отображаться. В коде:

```csharp
ListView HeaderList = new ListView() {
    Header = "Header",
    Footer = "Footer"
    };
```

В XAML:

```xaml
<ListView  x:Name="HeaderList"  Header="Header" Footer="Footer"></ListView>
```

![](customizing-list-appearance-images/header-default.png "ListView с верхнего и нижнего колонтитулов")

Чтобы создать настраиваемый верхний и нижний колонтитул, определите представлениях колонтитулы:

```xaml
<ListView.Header>
    <StackLayout Orientation="Horizontal">
        <Label Text="Header"
        TextColor="Olive"
        BackgroundColor="Red" />
    </StackLayout>
</ListView.Header>
<ListView.Footer>
    <StackLayout Orientation="Horizontal">
        <Label Text="Footer"
        TextColor="Gray"
        BackgroundColor="Blue" />
    </StackLayout>
</ListView.Footer>
```

![](customizing-list-appearance-images/header-custom.png "ListView с настраиваемый верхний и нижний колонтитул")

<a name="Row_Separators" />

## <a name="row-separators"></a>Разделители строк
Разделитель линии отображаются между `ListView` элементы по умолчанию в iOS и Android. Если вы хотите скрыть разделительных линий в iOS и Android, задайте `SeparatorVisibility` свойство в ListView. Параметры для `SeparatorVisibility` являются:

* **По умолчанию** -показана строка разделителя в iOS и Android.
* **Нет** -скрывает разделителя на всех платформах.

Видимость по умолчанию:

C#:

```csharp
SepratorDemoListView.SeparatorVisibility = SeparatorVisibility.Default;
```

XAML:

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorVisibility="Default" />
```

![](customizing-list-appearance-images/separator-default.png "ListView с разделителями строк по умолчанию")

NONE:

C#:

```csharp
SepratorDemoListView.SeparatorVisibility = SeparatorVisibility.None;
```

XAML:

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorVisibility="None" />
```

![](customizing-list-appearance-images/separator-none.png "ListView без разделителей строк")

Можно также задать цвет разделительной линии через `SeparatorColor` свойство:

C#:

```csharp
SepratorDemoListView.SeparatorColor = Color.Green;
```

XAML:

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorColor="Green" />
```

![](customizing-list-appearance-images/separator-custom.png "ListView с разделителями строк зеленый")

> [!NOTE]
> При настройке любого из этих свойств в Android после загрузки `ListView` сказывается на больших производительности.

<a name="Row_Heights" />

## <a name="row-heights"></a>Значения высоты строк
По умолчанию все строки в ListView имеют одинаковую высоту. ListView имеет два свойства, которые могут использоваться для изменения этого поведения.

- `HasUnevenRows` &ndash; `true`/`false` значение, строки имеют различные значения высоты, если значение `true`. По умолчанию — `false`.
- `RowHeight` &ndash; Задает высоту каждой строки, если `HasUnevenRows` является `false`.

Можно задать высоту всех строк, задав `RowHeight` свойство `ListView`.

### <a name="custom-fixed-row-height"></a>Пользовательские фиксированную высоту строки

C#:

```csharp
RowHeightDemoListView.RowHeight = 100;
```

XAML:

```xaml
<ListView x:Name="RowHeightDemoListView" RowHeight="100" />
```

![](customizing-list-appearance-images/height-custom.png "ListView с фиксированную высоту строки")


### <a name="uneven-rows"></a>Неравномерное строк

Если вы хотите отдельных строк для получения разной высоты, можно задать `HasUnevenRows` свойства `true`.
Обратите внимание, что высота строк не нужно вручную задать один раз `HasUnevenRows` было присвоено `true`, так как значения высоты будет вычисляться автоматически с Xamarin.Forms.


C#:

```csharp
RowHeightDemoListView.HasUnevenRows = true;
```

XAML:

```xaml
<ListView x:Name="RowHeightDemoListView" HasUnevenRows="true" />
```

![](customizing-list-appearance-images/height-uneven.png "ListView с ячейками разного размера строк")

### <a name="runtime-resizing-of-rows"></a>Среда выполнения изменения размера строк

Отдельные `ListView` строк может изменяться программно во время выполнения, при условии, что `HasUnevenRows` свойству `true`. [ `Cell.ForceUpdateSize` ](xref:Xamarin.Forms.Cell.ForceUpdateSize) Метод обновляет размер ячейки, даже в том случае, если он отсутствует в настоящее время, как показано в следующем примере кода:

```csharp
void OnImageTapped (object sender, EventArgs args)
{
    var image = sender as Image;
    var viewCell = image.Parent.Parent as ViewCell;

    if (image.HeightRequest < 250) {
        image.HeightRequest = image.Height + 100;
        viewCell.ForceUpdateSize ();
    }
}
```

`OnImageTapped` Обработчик событий выполняется в ответ на [ `Image` ](xref:Xamarin.Forms.Image) в ячейке касание и увеличивает размер `Image` отображаться в ячейке, чтобы его легко просмотреть.

![](customizing-list-appearance-images/dynamic-row-resizing.png "ListView с изменением размеров строк среды выполнения")

Обратите внимание, что надежный вероятность снижения производительности, что если эта функция является чрезмерным.



## <a name="related-links"></a>Связанные ссылки

- [Группирование (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/Grouping)
- [Пользовательское представление модуля подготовки отчетов (пример)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithListviewNative/)
- [Динамическое изменение размера строк (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/DynamicUnevenListCells/)
- [заметки о выпуске 1.4](http://forums.xamarin.com/discussion/35451/xamarin-forms-1-4-0-released/)
- [заметки о выпуске версии 1.3](http://forums.xamarin.com/discussion/29934/xamarin-forms-1-3-0-released/)
