---
title: Интерактивность ListView
description: В этой статье объясняется, как для обеспечения интерактивности ListView Xamarin.Forms, реализовав выбранные параметры, контекст действия и по запросу для обновления.
ms.prod: xamarin
ms.assetid: CD14EB90-B08C-4E8F-A314-DA0EEC76E647
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/13/2018
ms.openlocfilehash: 77a48e36f33fc690306f5e590f9f4f3064fe1ddf
ms.sourcegitcommit: 4c0093ee5d4aeb16c0e6f0c740c4796736971651
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39202946"
---
# <a name="listview-interactivity"></a>Интерактивность ListView

ListView поддерживает взаимодействия с данными, которые она представляет, используя следующие подходы:

- [**Выбор & касания** ](#selectiontaps) &ndash; реагирования на касания и выбранные параметры/deselections элементов. Включить или отключить выделение строки (включено по умолчанию).
- [**Контекстные действия** ](#Context_Actions) &ndash; предоставляют функциональные возможности каждого элемента, например, проведите по экрану для удаления.
- [**Чтобы обновить по запросу** ](#Pull_to_Refresh) &ndash; реализовать идиому по запросу для обновления, которая пользователи привыкли из собственного опыта.

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
Пользователи привыкли, потянув вниз по списку данных обновит этого списка. `ListView` поддерживает этот out-of--box. Чтобы включить функцию по запросу для обновления, задайте `IsPullToRefreshEnabled` значение true:

```csharp
listView.IsPullToRefreshEnabled = true;
```

Извлекает по запросу для обновления имени пользователя:

![](interactivity-images/refresh-start.png "ListView потяните, чтобы обновить выполняющееся")

По запросу для обновления имени пользователя выпустила по запросу. Это, что пользователь видит при обновлении списка: ![ ] (interactivity-images/refresh-in-progress.png "ListView потяните, чтобы обновить полный")

ListView предоставляет несколько событий, которые позволяют реагировать на события по запросу для обновления.

-  `RefreshCommand` Будет вызываться и `Refreshing` событие. `IsRefreshing` будет присвоено `true`.
-  Следует выполнять код необходим для обновления содержимого представления "list", либо команду или события.
-  При обновлении завершения, вызовите `EndRefresh` или задать `IsRefreshing` для `false` определить представление списка, в котором все будет готово.

`CanExecute` Учитывается свойство, которое позволяет способ управления ли команда по запросу для обновления должен быть включен.



## <a name="related-links"></a>Связанные ссылки

- [ListView интерактивности (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/interactivity)
- [заметки о выпуске 1.4](http://forums.xamarin.com/discussion/35451/xamarin-forms-1-4-0-released/)
- [заметки о выпуске версии 1.3](http://forums.xamarin.com/discussion/29934/xamarin-forms-1-3-0-released/)
