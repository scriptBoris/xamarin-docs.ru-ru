---
title: Интерактивность ListView
description: В этой статье объясняется, как для обеспечения интерактивности ListView Xamarin.Forms, реализовав выбранные параметры, контекст действия и по запросу для обновления.
ms.prod: xamarin
ms.assetid: CD14EB90-B08C-4E8F-A314-DA0EEC76E647
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/14/2018
ms.openlocfilehash: 939df6cfd17de82e28958363cfa51cd199f928cb
ms.sourcegitcommit: 93c9fe61eb2cdfa530960b4253eb85161894c882
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55831695"
---
# <a name="listview-interactivity"></a>Интерактивность ListView

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/interactivity)

[`ListView`](xref:Xamarin.Forms.ListView) поддерживает взаимодействие с данными, которые она представляет.

<a name="selectiontaps" />

## <a name="selection--taps"></a>Выбор & касания

[ `ListView` ](xref:Xamarin.Forms.ListView) Режим выбора управляется заданием [ `ListView.SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode) свойство в значение [ `ListViewSelectionMode` ](xref:Xamarin.Forms.ListViewSelectionMode) перечисления:

- [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) Указывает, что элемент можно выбрать, с выбранным элементом выделена. Это значение по умолчанию.
- [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) Указывает, что элементы не могут быть выбраны.

При касании элемента, два события инициируются:

- [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) активируется, когда элемент выбран.
- [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) возникает при нажатии элемента.

> [!NOTE]
> При касании тот же элемент дважды будет срабатывать два [ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped) события, но будет только вызов одной [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) событий.

Когда [ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode) свойству [ `Single` ](xref:Xamarin.Forms.ListViewSelectionMode.Single), элементы в [ `ListView` ](xref:Xamarin.Forms.ListView) можно выбрать, [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) и [ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped) события порождаются и [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem) свойству будет присвоено значение выбранного элемента.

Когда [ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode) свойству [ `None` ](xref:Xamarin.Forms.ListViewSelectionMode.None), элементы в [ `ListView` ](xref:Xamarin.Forms.ListView) не могут быть выбраны, [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) не будет создаваться событие и [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem) свойство будет оставаться `null`. Тем не менее [ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped) события по-прежнему будет создаваться и полученные элемента будут кратко выделены во время прослушивания.

Если был выбран элемент и [ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode) свойство меняется с [ `Single` ](xref:Xamarin.Forms.ListViewSelectionMode.Single) для [ `None` ](xref:Xamarin.Forms.ListViewSelectionMode.None), [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem) свойству будет присвоено `null` и [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) событие будет запускаться с `null` элемента.

Ниже показаны снимки экрана [ `ListView` ](xref:Xamarin.Forms.ListView) с режимом выделения по умолчанию:

![](interactivity-images/selection-default.png "ListView с выбором включена")

### <a name="disabling-selection"></a>Отключение выделения

Чтобы отключить [ `ListView` ](xref:Xamarin.Forms.ListView) набора [ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode) свойства [ `None` ](xref:Xamarin.Forms.ListViewSelectionMode.None):

```xaml
<ListView ... SelectionMode="None" />
```

```csharp
var listView = new ListView { ... SelectionMode = ListViewSelectionMode.None };
```

<a name="Context_Actions" />

## <a name="context-actions"></a>Контекстные действия

Часто пользователей потребуется выполнить действие на элемент в `ListView`. Например рассмотрим список адресов электронной почты в почтовом приложении. В iOS можно проведите, чтобы удалить сообщение::

![](interactivity-images/context-default.png "ListView с помощью контекстных действий")

Контекстные действия можно реализовать в C# и XAML. Далее вы найдете определенных направляющих для обоих, но сперва давайте взглянем на некоторые ключевые особенности реализации для обоих.

Контекстные действия создаются с помощью `MenuItem`s. События касания для MenuItems вызываются с помощью MenuItem, не ListView. Это отличается от обработки события касания для ячеек, где ListView инициирует событие, а не ячейку. Поскольку ListView порождает событие, соответствующий обработчик событий получает сведения о ключе, например которого выбран или касание элемента.

По умолчанию элемент меню не имеет возможности узнать, какая ячейка, в которой он принадлежит. `CommandParameter` можно найти в `MenuItem` для хранения объектов, таких как объект MenuItem ViewCell. `CommandParameter` можно задать в XAML и C#.

### <a name="c"></a>C#  

Контекстные действия может быть реализован ни в одном `Cell` подкласс (пока она не используется в качестве заголовка группы), создав `MenuItem`s и добавления их в `ContextActions` коллекции для ячейки. У вас есть следующие свойства можно настроить для контекста действия:

* **Текст** &ndash; строка, которая появляется в элементе меню.
* **Нажата** &ndash; события при щелчке элемента.
* **IsDestructive** &ndash; (Дополнительно) Если значение true, элемент отображается по-разному в iOS.

Несколько контекстных действий можно добавить в ячейку, однако должны иметь только один `IsDestructive` присвоено `true`. В следующем коде показано, как будут добавлены контекстные действия `ViewCell`:

```csharp
var moreAction = new MenuItem { Text = "More" };
moreAction.SetBinding (MenuItem.CommandParameterProperty, new Binding ("."));
moreAction.Clicked += async (sender, e) => {
    var mi = ((MenuItem)sender);
    Debug.WriteLine("More Context Action clicked: " + mi.CommandParameter);
};

var deleteAction = new MenuItem { Text = "Delete", IsDestructive = true }; // red background
deleteAction.SetBinding (MenuItem.CommandParameterProperty, new Binding ("."));
deleteAction.Clicked += async (sender, e) => {
    var mi = ((MenuItem)sender);
    Debug.WriteLine("Delete Context Action clicked: " + mi.CommandParameter);
};
// add to the ViewCell's ContextActions property
ContextActions.Add (moreAction);
ContextActions.Add (deleteAction);
```

### <a name="xaml"></a>XAML

`MenuItem`s могут также создаваться в коллекции XAML декларативно. Ниже XAML демонстрирует настраиваемой ячейки с реализованы два контекстных действий:

```xaml
<ListView x:Name="ContextDemoList">
  <ListView.ItemTemplate>
    <DataTemplate>
      <ViewCell>
         <ViewCell.ContextActions>
            <MenuItem Clicked="OnMore" CommandParameter="{Binding .}"
               Text="More" />
            <MenuItem Clicked="OnDelete" CommandParameter="{Binding .}"
               Text="Delete" IsDestructive="True" />
         </ViewCell.ContextActions>
         <StackLayout Padding="15,0">
              <Label Text="{Binding title}" />
         </StackLayout>
      </ViewCell>
    </DataTemplate>
  </ListView.ItemTemplate>
</ListView>
```

Убедитесь в файл с выделенным кодом `Clicked` реализуются методы:

```csharp
public void OnMore (object sender, EventArgs e) {
    var mi = ((MenuItem)sender);
    DisplayAlert("More Context Action", mi.CommandParameter + " more context action", "OK");
}

public void OnDelete (object sender, EventArgs e) {
    var mi = ((MenuItem)sender);
    DisplayAlert("Delete Context Action", mi.CommandParameter + " delete context action", "OK");
}
```

> [!NOTE]
> `NavigationPageRenderer` Для Android имеет переопределяемый `UpdateMenuItemIcon` метод, который может использоваться для загрузки значки из настраиваемого `Drawable`. Это переопределение позволяет использовать изображения SVG в виде значков на `MenuItem` экземпляров на Android.

<a name="Pull_to_Refresh" />

## <a name="pull-to-refresh"></a>Потяните, чтобы обновить

Пользователи привыкли, потянув вниз по списку данных обновит этого списка. [`ListView`](xref:Xamarin.Forms.ListView) поддерживает этот out-of--box. Чтобы включить функцию по запросу для обновления, задайте [ `IsPullToRefreshEnabled` ](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled) для `true`:

```xaml
<ListView ...
          IsPullToRefreshEnabled="true" />
```

Ниже приведен аналогичный код C#:

```csharp
listView.IsPullToRefreshEnabled = true;
```

Счетчик появляется во время обновления, который является черной по умолчанию. Тем не менее, цвет "Счетчик" можно изменить в iOS и Android, задав `RefreshControlColor` свойства [ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<ListView ...
          IsPullToRefreshEnabled="true"
          RefreshControlColor="Red" />
```

Ниже приведен аналогичный код C#:

```csharp
listView.RefreshControlColor = Color.Red;
```

На следующих снимках экрана показано по запросу для обновления, как пользователь извлекает:

![](interactivity-images/refresh-start.png "ListView потяните, чтобы обновить выполняющееся")

Далее на снимках экрана Показывать по запросу для обновления после Пользователь отпустил по запросу, с "Счетчик", который отображается во время [ `ListView` ](xref:Xamarin.Forms.ListView) обновляется:

![](interactivity-images/refresh-in-progress.png "ListView потяните, чтобы обновление завершено")

[`ListView`](xref:Xamarin.Forms.ListView) активируется [ `Refreshing` ](xref:Xamarin.Forms.ListView.Refreshing) событий инициировать обновление и [ `IsRefreshing` ](xref:Xamarin.Forms.ListView.IsRefreshing) свойству будет присвоено `true`. Любой код не требуется, чтобы обновить содержимое `ListView` затем должна выполняться с помощью обработчика событий для `Refreshing` событий, или с помощью метода, выполняемая [ `RefreshCommand` ](xref:Xamarin.Forms.ListView.RefreshCommand). Один раз `ListView` обновляется и `IsRefreshing` свойство должно быть присвоено `false`, или [ `EndRefresh` ](xref:Xamarin.Forms.ListView.EndRefresh) метод должен вызываться, для указания выполнения обновления.

> [!NOTE]
> При определении [ `RefreshCommand` ](xref:Xamarin.Forms.ListView.RefreshCommand), `CanExecute` метод команды можно задать, чтобы включить или отключить команду.

## <a name="related-links"></a>Связанные ссылки

- [ListView интерактивности (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/interactivity)
