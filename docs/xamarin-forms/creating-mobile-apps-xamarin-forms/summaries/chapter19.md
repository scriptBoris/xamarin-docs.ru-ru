---
title: Сводка главе 19. Представления коллекций
description: 'Создание мобильных приложений с помощью Xamarin.Forms: Сводка главе 19. Представления коллекций'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 0AEC3A5C-586E-4D0F-9895-67E99A053A79
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 795478805b582b956ee491bdfecd84485c1bc30e
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53059448"
---
# <a name="summary-of-chapter-19-collection-views"></a>Сводка главе 19. Представления коллекций

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)

> [!NOTE] 
> Заметки на этой странице указывать области, где различаются Xamarin.Forms материал, представленный в книге.

Xamarin.Forms определяет три представления, которые обслуживают коллекции и отображают их элементов:

- [`Picker`](xref:Xamarin.Forms.Picker) — относительно короткий список элементов строковых, пользователь может выбрать один
- [`ListView`](xref:Xamarin.Forms.ListView) часто представляет собой длинный список элементов, обычно того же типа и форматирование, также позволяя пользователю выбрать один
- [`TableView`](xref:Xamarin.Forms.TableView) — Это коллекция *ячеек* (как правило, различные типы и внешний вид) для отображения данных или управление ввод данных пользователем

Довольно часто для использования приложениями MVVM `ListView` для отображения можно выбрать коллекцию объектов.

## <a name="program-options-with-picker"></a>Параметры для программы с помощью средства выбора

[ `Picker` ](xref:Xamarin.Forms.Picker) Является хорошим выбором при необходимости разрешить пользователю выбор из списка относительно короткий `string` элементов.

### <a name="the-picker-and-event-handling"></a>Средство выбора и обработка событий

[ **PickerDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerDemo) образце показано, как использовать XAML для установки `Picker` [ `Title` ](xref:Xamarin.Forms.Picker.Title) свойство и добавьте `string` элементы [ `Items` ](xref:Xamarin.Forms.Picker.Items) коллекции. Когда пользователь выбирает `Picker`, она отображает элементы в `Items` коллекции в виде зависят от платформы.

[ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged) Событие указывает, когда пользователь выбрал элемент. Отсчитываемый от нуля [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) свойство затем указывает выбранный элемент. Если элемент не выбран, `SelectedIndex` равно &ndash;1.

Можно также использовать `SelectedIndex` для инициализации выбранного элемента, но оно должно быть задано позднее `Items` заполнения коллекции. В XAML, это означает, что вы воспользуетесь property-элемент для задания `SelectedIndex`.

### <a name="data-binding-the-picker"></a>Средство выбора привязки данных

`SelectedIndex` Поддержку свойства может быть привязано, но `Items` — нет, поэтому использование привязки данных с помощью `Picker` сложно. Одним из решений является использование `Picker` в сочетании с [ `ObjectToIndexConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ObjectToIndexConverter.cs) подобное показанному в [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) библиотеки. [ **PickerBinding** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerBinding) показано, как это работает.

> [!NOTE] 
> Xamarin.Forms `Picker` теперь включает `ItemsSource` и `SelectedItem` свойства, которые поддерживают привязку данных. См. в разделе [выбора](~/xamarin-forms/user-interface/picker/index.md).

## <a name="rendering-data-with-listview"></a>Подготовка к просмотру данных с помощью ListView

[ `ListView` ](xref:Xamarin.Forms.ListView) Является единственным классом, который является производным от [ `ItemsView<TVisual>` ](xref:Xamarin.Forms.ItemsView`1) от которого он наследуется [ `ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource) и [ `ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) свойства.

`ItemsSource` имеет тип `IEnumerable` , но это `null` по умолчанию и, необходимо явно инициализировать или (обычно) в коллекции через привязку данных. Элементы в этой коллекции может быть любого типа.

`ListView` Определяет [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem) свойству, либо присвоить один из элементов в `ItemsSource` коллекции или `null` Если элемент не выбран. `ListView` активируется [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) событие, когда элемент выбран.

### <a name="collections-and-selections"></a>Коллекции и предварительно выбранными параметрами

[ **ListViewList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewList) пример заливки `ListView` с 17 `Color` значения в `List<Color>` коллекции. Элементы выделяются, но по умолчанию они отображаются с их непривлекательной `ToString` представления. Несколько примеров в этой главе показано, как для исправления, которые отображают и сделать ее как привлекательной, при необходимости.

### <a name="the-row-separator"></a>Разделитель строк

В iOS и Android отображает тонкой отделяет строки. Вы можете управлять этим с [ `SeparatorVisibility` ](xref:Xamarin.Forms.ListView.SeparatorVisibility) и [ `SeparatorColor` ](xref:Xamarin.Forms.ListView.SeparatorColor) свойства. `SeparatorVisibility` свойство имеет тип [ `SeparatorVisibility` ](xref:Xamarin.Forms.SeparatorVisibility), перечисление с двумя членами:

- [`Default`](xref:Xamarin.Forms.SeparatorVisibility.Default), значение по умолчанию
- [`None`](xref:Xamarin.Forms.SeparatorVisibility.None)

### <a name="data-binding-the-selected-item"></a>Выбранный элемент привязки данных

`SelectedItem` Резервируется привязываемые свойства, поэтому он может быть источника или целевого объекта привязки данных. По умолчанию `BindingMode` является `OneWayToSource`, но обычно он является целевым объектом Двухсторонняя привязка, особенно в сценариях MVVM. [ **ListViewArray** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewArray) в нем демонстрируется этот тип привязки.

### <a name="the-observablecollection-difference"></a>Разница ObservableCollection

[ **ListViewLogger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewLogger) пример наборов `ItemsSource` свойство `ListView` для `List<DateTime>` коллекции, а затем постепенно добавляет новый `DateTime` в коллекцию Каждый второй, с помощью таймера.

Тем не менее `ListView` не обновится автоматически сам поскольку `List<T>` коллекции не имеет механизм уведомлений, чтобы указать, при добавлении или удален из коллекции элементов.

Гораздо лучше класс для использования в таких случаях является [ `ObservableCollection<T>` ](xref:System.Collections.ObjectModel.ObservableCollection`1) определенные в `System.Collections.ObjectModel` пространства имен. Этот класс реализует [ `INotifyCollectionChanged` ](xref:System.Collections.Specialized.INotifyCollectionChanged) интерфейса и, следовательно, активируется [ `CollectionChanged` ](xref:System.Collections.ObjectModel.ObservableCollection`1.CollectionChanged) событие при добавлении или удален из коллекции, или когда они заменены или перемещении в пределах элементов Коллекция. Когда `ListView` внутренне обнаруживает, что класс, реализующий `INotifyCollectionChanged` было присвоено его `ItemsSource` свойство, он присоединяет обработчик к `CollectionChanged` событий и обновляет отображаемые данные при изменении коллекции.

[ **ObservableLogger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ObservableLogger) в нем демонстрируется использование `ObservableCollection`.

### <a name="templates-and-cells"></a>Шаблоны и ячеек

По умолчанию `ListView` отображаются элементы в коллекции, с помощью каждого элемента `ToString` метод. Более эффективный подход заключается в определении шаблона для отображения элементов.

Чтобы поэкспериментировать с помощью этой функции, можно использовать [ `NamedColor` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) в класс [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) библиотеки. Этот класс определяет статическое `All` свойство типа `IList<NamedColor>` , содержащий 141 `NamedColor` объектов, соответствующий открытые поля `Color` структуры.

[ **NaiveNamedColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/NaiveNamedColorList) пример наборов `ItemsSource` из `ListView` к этому `NamedColor.All` свойство, но только имена полной `NamedColor` объекты отображается.

`ListView` требуется шаблон для отображения этих элементов. В коде, можно задать [ `ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) свойство, определенное `ItemsView<TVisual>` для [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) с помощью [ `DataTemplate` конструктор](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type)) , ссылается на, производный от [ `Cell` ](xref:Xamarin.Forms.Cell) класса. `Cell` имеет пять производные:

- [`TextCell`](xref:Xamarin.Forms.TextCell) &mdash; содержит два `Label` представлений (с концептуальной точки зрения)
- [`ImageCell`](xref:Xamarin.Forms.ImageCell) &mdash; Добавляет `Image` представления `TextCell`
- [`EntryCell`](xref:Xamarin.Forms.EntryCell) &mdash; содержит `Entry` просмотреть с помощью `Label`
- [`SwitchCell`](xref:Xamarin.Forms.SwitchCell) &mdash; содержит `Switch` с `Label`
- [`ViewCell`](xref:Xamarin.Forms.ViewCell) &mdash; может быть любым `View` (скорее всего, с помощью дочерних элементов)

Затем вызовите [ `SetValue` ](xref:Xamarin.Forms.DataTemplate.SetValue(Xamarin.Forms.BindableProperty,System.Object)) и [ `SetBinding` ](xref:Xamarin.Forms.DataTemplate.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) на `DataTemplate` объект для связывания значения с `Cell` свойства, или чтобы задать привязки данных на `Cell` Свойства ссылки на свойства элементов в `ItemsSource` коллекции. Это показано в [ **TextCellListCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListCode) образца.

Так как каждый элемент отображается с `ListView`небольшой визуальное дерево строится на основе шаблона и привязки данных, устанавливаются между элементом и свойства элементов в визуальном дереве. Представление этого процесса можно получить, установив обработчики для [ `ItemAppearing` ](xref:Xamarin.Forms.ListView.ItemAppearing) и [ `ItemDisappearing` ](xref:Xamarin.Forms.ListView.ItemDisappearing) события `ListView`, или с помощью альтернативы [ `DataTemplate`конструктор](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Func{System.Object})) , использующий функцию, которая вызывается каждый раз, необходимо создать визуальное дерево элемента.

[ **TextCellListXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListXaml) показывает идентичных по функциональности программы полностью в XAML. Объект `DataTemplate` имеет значение тега `ItemTemplate` свойство `ListView`, а затем `TextCell` присваивается `DataTemplate`. Привязки для свойств элементов в коллекции, задаются непосредственно в [ `Text` ](xref:Xamarin.Forms.TextCell.Text) и [ `Detail` ](xref:Xamarin.Forms.TextCell.Detail) свойства `TextCell`.

### <a name="custom-cells"></a>Пользовательские ячейки

В XAML можно задать [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) для `DataTemplate` , а затем определите пользовательского визуального дерева как [ `View` ](xref:Xamarin.Forms.ViewCell.View) свойство `ViewCell`. (`View` является свойством содержимого из `ViewCell` поэтому `ViewCell.View` теги не являются обязательными.) [ **CustomNamedColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CustomNamedColorList) этот метод продемонстрирован в примере:

[![Тройной снимок экрана: список цветов с именем пользовательского](images/ch19fg11-small.png "список цветов с именем пользовательского")](images/ch19fg11-large.png#lightbox "список цветов с именем Custom")

Получение размера сразу для всех платформ может быть непростой задачей. [ `RowHeight` ](xref:Xamarin.Forms.ListView.RowHeight) Свойство полезно, но в некоторых случаях вы захотите прибегнуть к [ `HasUnevenRows` ](xref:Xamarin.Forms.ListView.HasUnevenRows) свойство, которое является менее эффективным, но заставляет `ListView` для изменения размера строки. Для iOS и Android Чтобы получить правильное изменение размеров необходимо использовать одно из этих двух свойств.

### <a name="grouping-the-listview-items"></a>Группирование элементов ListView

`ListView` поддерживает группирование элементов и перехода между этими группами. `ItemsSource` Свойство должно иметь значение в коллекцию коллекций: объект, `ItemsSource` присваивается должен реализовать `IEnumerable`, и каждый элемент в коллекции необходимо также реализовать `IEnumerable`. Каждая группа должна содержать два свойства: текстовое описание группы и трехбуквенное сокращение.

[ `NamedColorGroup` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColorGroup.cs) В класс [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) library создает семь групп `NamedColor` объектов. [ **ColorGroupList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorGroupList) примере показано, как использовать эти группы с помощью [ `IsGroupingEnabled` ](xref:Xamarin.Forms.ListView.IsGroupingEnabled) свойство `ListView` присвоено `true`и [ `GroupDisplayBinding` ](xref:Xamarin.Forms.ListView.GroupDisplayBinding) и [ `GroupShortNameBinding` ](xref:Xamarin.Forms.ListView.GroupShortNameBinding) свойства, привязаны к свойствам в каждой группе.

### <a name="custom-group-headers"></a>Заголовки пользовательских групп

Существует возможность создания пользовательских заголовков `ListView` групп, заменив `GroupDisplayBinding` свойство с [ `GroupHeaderTemplate` ](xref:Xamarin.Forms.ListView.GroupHeaderTemplate) определение шаблона для заголовков.

### <a name="listview-and-interactivity"></a>ListView и интерактивность

Как правило, приложение получит взаимодействие пользователя с `ListView` путем присоединения обработчика к `ItemSelected` или [ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped) событий, или установив привязку данных на `SelectedItem` свойство. Но некоторые типы ячейки (`EntryCell` и `SwitchCell`) разрешение взаимодействия с пользователем, а также возможно, чтобы создать пользовательские ячейки, которые сами взаимодействия с пользователем. [ **InteractiveListView** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/InteractiveListView) создает 100 экземпляров [ `ColorViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) и позволяет пользователю изменять каждый цвет, с помощью модулей электронной инструментальной `Slider` элементов. Программа также используется [ `ColorToContrastColorConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorToContrastColorConverter.cs) в [ **Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit).

## <a name="listview-and-mvvm"></a>ListView и MVVM

`ListView` играет важную роль в сценариях MVVM. Каждый раз, когда `IEnumerable` коллекции в ViewModel, часто он привязан к `ListView`. Кроме того, элементы в коллекции часто реализовать `INotifyPropertyChanged` для привязки свойства в шаблоне.

### <a name="a-collection-of-viewmodels"></a>Коллекция ViewModels

Для его изучения, [ **SchoolOfFineArts** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) library создает несколько классов, на основе [XML-файла данных](http://xamarin.github.io/xamarin-forms-book-samples/SchoolOfFineArt/students.xml) и картинки с изображениями вымышленной учащихся в этом вымышленной школе.

[ `Student` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/Student.cs) Класс является производным от [ `ViewModelBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/ViewModelBase.cs). [ `StudentBody` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/StudentBody.cs) Класс — это коллекция `Student` объектов и также является производным от `ViewModelBase`. [ `SchoolViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/SchoolViewModel.cs) Загружает XML-файле и собирает все объекты.

[ **StudentList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/StudentList) программа использует `ImageCell` для отображения учащихся и соответствующих образов в `ListView`:

[![Тройной снимок экрана: список учащихся](images/ch19fg18-small.png "список учащихся")](images/ch19fg18-large.png#lightbox "список учащихся")

[ **ListViewHeader** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewHeader) пример добавляет [ `Header` ](xref:Xamarin.Forms.ListView.Header) свойство, но выводится только в Android.

### <a name="selection-and-the-binding-context"></a>Выбор и контекст привязки

[ **SelectedStudentDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/SelectedStudentDetail) программировать привязывает `BindingContext` из `StackLayout` для `SelectedItem` свойство `ListView`. Это позволяет отобразить подробные сведения о выбранного учащегося.

### <a name="context-menus"></a>Контекстные меню

Ячейки можно определить контекстное меню, которое реализуется в виде конкретной платформы. Чтобы создать это меню, добавьте [ `MenuItem` ](xref:Xamarin.Forms.MenuItem) объектов [ `ContextActions` ](xref:Xamarin.Forms.Cell.ContextActions) свойство `Cell`.

`MenuItem` Определяет пять свойств:

- [`Text`](xref:Xamarin.Forms.MenuItem.Text) типа `string`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) типа `FileImageSource`
- [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive) типа `bool`
- [`Command`](xref:Xamarin.Forms.MenuItem.Command) типа `ICommand`
- [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) типа `object`

`Command` И `CommandParameter` свойства подразумевает, что ViewModel для каждого элемента содержит методы для выполнения команд меню. В сценариях без MVVM `MenuItem` также определяет [ `Clicked` ](xref:Xamarin.Forms.MenuItem.Clicked) событий.

[ **CellContextMenu** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CellContextMenu) этот метод продемонстрирован. `Command` Свойства каждого `MenuItem` привязано к свойству типа `ICommand` в `Student` класса. Задайте `IsDestructive` свойства `true` для `MenuItem` , удаляет или удаление выбранного объекта.

### <a name="varying-the-visuals"></a>Изменение визуальных элементов

Иногда может потребоваться небольшими вариациями в визуальных элементах элементов `ListView` на основе свойства. Например, когда Стьюдента баллов среднее падает ниже 2.0, [ **ColorCodedStudents** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorCodedStudents) пример отображает имя этого учащегося красным цветом.
Это достигается путем использования преобразователя значений привязки [ `ThresholdToObjectConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ThresholdToObjectConverter.cs)в [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) библиотеки.

### <a name="refreshing-the-content"></a>Обновление содержимого

`ListView` Поддерживает жест раскрывающемся списке для обновления его данных. Необходимо установить программу [ `IsPullToRefresh` ](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled) свойства `true` соответствующим образом. `ListView` Реагирует на жест раскрывающемся списке, задав его [ `IsRefreshing` ](xref:Xamarin.Forms.ListView.IsRefreshing) свойства `true`и формируя [ `Refreshing` ](xref:Xamarin.Forms.ListView.Refreshing) событий и (для сценариев MVVM) вызова `Execute` метод его [ `RefreshCommand` ](xref:Xamarin.Forms.ListView.RefreshCommand) свойство.

Код обработки `Refresh` событий или `RefreshCommand` возможно обновление данных, отображаемых объектом `ListView` и задает `IsRefreshing` обратно `false`.

[ **RssFeed** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/RssFeed) образце показано использование [ `RssFeedViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/RssFeed/RssFeed/RssFeed/RssFeedViewModel.cs) , реализующий `RefreshCommand` и `IsRefreshing` свойства для привязки данных.

## <a name="the-tableview-and-its-intents"></a>TableView и его намерений

Хотя `ListView` обычно отображается несколько экземпляров одного типа, [ `TableView` ](xref:Xamarin.Forms.TableView) обычно сосредоточена на предоставление пользовательского интерфейса для нескольких свойств различных типов. Каждый элемент связан с собственным [ `Cell` ](xref:Xamarin.Forms.Cell) класс, производный от для отображения свойства или предоставление пользовательского интерфейса к нему.

### <a name="properties-and-hierarchies"></a>Свойства и иерархий

`TableView` определяет только четыре свойства:

- [`Intent`](xref:Xamarin.Forms.TableView.Intent) типа [ `TableIntent` ](xref:Xamarin.Forms.TableIntent), перечисление
- [`Root`](xref:Xamarin.Forms.TableView.Root) типа [ `TableRoot` ](xref:Xamarin.Forms.TableRoot), свойство содержимого `TableView`
- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight) типа `int`
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) типа `bool`

`TableIntent` Перечисления указывает, как вы планируете использовать `TableView`:

- [`Data`](xref:Xamarin.Forms.TableIntent.Data)
- [`Form`](xref:Xamarin.Forms.TableIntent.Form)
- [`Settings`](xref:Xamarin.Forms.TableIntent.Settings)
- [`Menu`](xref:Xamarin.Forms.TableIntent.Menu)

Эти элементы также предложить некоторые варианты применения `TableView`.

В определении таблицы участвуют несколько других классов.

- [`TableSectionBase`](xref:Xamarin.Forms.TableSectionBase) — Это абстрактный класс, производный от `BindableObject` и определяет [ `Title` ](xref:Xamarin.Forms.TableSectionBase.Title) свойство

- [`TableSectionBase<T>`](xref:Xamarin.Forms.TableSectionBase`1) — Это абстрактный класс, производный от `TableSectionBase` и реализует `IList<T>` и `INotifyCollectionChanged`

- [`TableSection`](xref:Xamarin.Forms.TableSection) является производным от `TableSectionBase<Cell>`

- [`TableRoot`](xref:Xamarin.Forms.TableRoot) является производным от `TableSectionBase<TableSection>`

Короче говоря `TableView` имеет `Root` свойство, которое задано для `TableRoot` объект, который представляет собой коллекцию из `TableSection` объектов, каждый из которых является коллекцией `Cell` объектов. Таблица содержит несколько разделов, а каждый раздел содержит несколько ячеек. Сама таблица может иметь название, и каждый раздел может иметь название. Несмотря на то что `TableView` использует `Cell` производные, при этом не делает использование `DataTemplate`.

### <a name="a-prosaic-form"></a>Прозаическом формы

[ **EntryForm** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/EntryForm) образец определяет [ `PersonalInformation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) модели представления, экземпляр которого становится `BindingContext` из `TableView`. Каждый `Cell` класс, производный от в его `TableSection` может иметь привязки к свойствам `PersonalInformation` класса.

### <a name="custom-cells"></a>Пользовательские ячейки

[ **ConditionalCells** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalCells) расширяет образец **EntryForm**. [ `ProgrammerInformation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) Класс содержит логическое свойство, которое определяет возможность применения двух дополнительных свойств. Для этих двух дополнительных свойств, программа использует пользовательский `PickerCell` на основе [PickerCell.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml) и [PickerCell.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml.cs) в [  **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) библиотеки.

Несмотря на то что `IsEnabled` двух `PickerCell` привязаны к логическое свойство в `ProgrammerInformation`, этот метод не работает, который запрашивает в приведенном ниже примере.

### <a name="conditional-sections"></a>Условные разделы

[ **ConditionalSection** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalSection) пример помещает два элемента, которые являются условными, связанных с выделением логическое элемента в отдельном `TableSection`. Этот раздел из удаляет файл с выделенным кодом `TableView` или добавляет его обратно на основе логического свойства.

### <a name="a-tableview-menu"></a>Меню TableView

Другим использованием `TableView` — это меню. [ **Команды MenuCommand** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/MenuCommands) образце показано меню, в котором вы можете переместить немного `BoxView` по экрану.



## <a name="related-links"></a>Связанные ссылки

- [Глава 19 полнотекстового поиска (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch19-Apr2016.pdf)
- [Примеры главе 19](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)
- [Средство выбора](~/xamarin-forms/user-interface/picker/index.md)
- [ListView](~/xamarin-forms/user-interface/listview/index.md)
- [TableView](~/xamarin-forms/user-interface/tableview.md)
