---
title: Заполнение таблицы с данными в Xamarin.iOS
description: В этом документе описывается, как для заполнения таблицы с данными в приложении Xamarin.iOS. В нем описывается UITableViewSource, повторное использование ячеек, добавление индекса и верхние и нижние колонтитулы.
ms.prod: xamarin
ms.assetid: 6FE64DDF-1029-EB9B-6EEC-1C7DFDFDF3AF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 859afcf6ab9f3acfb56104fa68683ba28d913ce4
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117145"
---
# <a name="populating-a-table-with-data-in-xamarinios"></a>Заполнение таблицы с данными в Xamarin.iOS

Для добавления строк в `UITableView` необходимо реализовать `UITableViewSource` подклассов и переопределение, вызывает методы, которые Просмотр таблицы для заполнения сам.

В этом руководстве описывается:

- Создание подкласса UITableViewSource
- Повторное использование ячеек
- Добавление индекса
- Добавление верхних и нижних колонтитулов


<a name="Subclassing_UITableViewSource" />

## <a name="subclassing-uitableviewsource"></a>Создание подкласса UITableViewSource

Объект `UITableViewSource` подкласс назначается в каждый `UITableView`. Представление таблицы запрашивает класс источника, чтобы определить, как для своей отрисовки (для примера, сколько строк являются обязательными и высоту каждой строки, если они отличаются от настроек по умолчанию). Самое главное источник предоставляет каждое представление ячейки, заполненную данными.

Существует только два обязательных методы, необходимые для создания отображения данных таблицы.

-   **RowsInSection** — возвращаемое [ `nint` ](http://developer.xamarin.com/guides/cross-platform/macios/nativetypes/) число общее число строк данных в таблице будет отображаться.
-   **GetCell** — возвращаемое `UITableCellView` заполняется данными для соответствующего индекса строки, передаваемые методу.


Пример файла BasicTable **TableSource.cs** имеет простейшая реализация `UITableViewSource`. Вы увидите в приведенном ниже фрагменте кода, что он принимает массив строк для отображения в таблице и возвращает стиль ячейки по умолчанию, содержащий каждой строки:

```csharp
public class TableSource : UITableViewSource {

        string[] TableItems;
        string CellIdentifier = "TableCell";

        public TableSource (string[] items)
        {
            TableItems = items;
        }

        public override nint RowsInSection (UITableView tableview, nint section)
        {
            return TableItems.Length;
        }

        public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
        {
            UITableViewCell cell = tableView.DequeueReusableCell (CellIdentifier);
            string item = TableItems[indexPath.Row];

            //---- if there are no cells to reuse, create a new one
            if (cell == null)
            { cell = new UITableViewCell (UITableViewCellStyle.Default, CellIdentifier); }

            cell.TextLabel.Text = item;

            return cell;
        }
}
```

Объект `UITableViewSource` можно использовать любую структуру данных, от простой строковый массив (как показано в этом примере) <> списка или другой коллекции. Реализация `UITableViewSource` методы изолирует таблицы из базовой структуры данных.

Чтобы использовать этот подкласс, создать массив строк для создания источника, а затем присвоить экземпляр `UITableView`:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    table = new UITableView(View.Bounds); // defaults to Plain style
    string[] tableItems = new string[] {"Vegetables","Fruits","Flower Buds","Legumes","Bulbs","Tubers"};
    table.Source = new TableSource(tableItems);
    Add (table);
}
```

Результирующая таблица выглядит следующим образом:

 [![](populating-a-table-with-data-images/image3.png "Образец таблицы под управлением")](populating-a-table-with-data-images/image3.png#lightbox)

Большинство таблиц разрешает пользователю touch строки, чтобы выбрать ее и выполнения других действий (например, воспроизведение песни, или вызов контакт или отображения другого экрана). Для этого существует несколько вещей, которые нам нужно сделать. Во-первых давайте создадим AlertController для отображения сообщения, когда пользователь щелкните строку, добавив следующую команду, чтобы `RowSelected` метод:

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    UIAlertController okAlertController = UIAlertController.Create ("Row Selected", tableItems[indexPath.Row], UIAlertControllerStyle.Alert);
    okAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
    ...

    tableView.DeselectRow (indexPath, true);
}
```

Создайте экземпляр нашего контроллера представления:

```csharp
HomeScreen owner;
```
Добавьте конструктор в класс UITableViewSource, который принимает контроллер представления в качестве параметра и сохраняет его в поле:

```csharp
public TableSource (string[] items, HomeScreen owner)
{
    ...
    this.owner = owner;

}
```
Измените метод ViewDidLoad, где создается класс UITableViewSource для передачи `this` ссылки:

```csharp
table.Source = new TableSource(tableItems, this);
```
Наконец, обратно в ваш `RowSelected` мы вызываем метод `PresentViewController` кэшированных поля:

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    ...
    owner.PresentViewController (okAlertController, true, null);

    ...
}
```


Теперь пользователь могут соприкасаться строки и появится предупреждение:



 [![](populating-a-table-with-data-images/image4.png "Строки выбранного оповещения")](populating-a-table-with-data-images/image4.png#lightbox)


## <a name="cell-reuse"></a>Повторное использование ячеек

В этом примере имеется только шесть элементов, поэтому не существует, повторное использование ячеек не требуется. При отображении сотни или тысячи строк, однако было бы к повышенному расходу памяти для создания сотен или тысяч `UITableViewCell` объектов, когда только некоторые помещаются на экране одновременно.

Чтобы избежать такой ситуации, когда ячейка пропадает с экрана, на котором его представления помещается в очередь для повторного использования. Во время прокрутки, таблице вызывает `GetCell` запрашивать новые представления для отображения — повторное использование существующей ячейкой, (который не отображается в настоящий момент) просто вызовите `DequeueReusableCell` метод. Если ячейка доступна для повторного использования, которые будут возвращены, в противном случае возвращается значение null, и ваш код должен создать новый экземпляр ячейки.

Этот фрагмент кода из примера показан шаблон:

```csharp
// request a recycled cell to save memory
UITableViewCell cell = tableView.DequeueReusableCell (cellIdentifier);
// if there are no cells to reuse, create a new one
if (cell == null)
    cell = new UITableViewCell (UITableViewCellStyle.Default, cellIdentifier);
```

`cellIdentifier` Фактически создает отдельные очереди для различных типов ячейки. В этом примере, что все ячейки выглядят же жестко закодированное чтобы только один идентификатор используется. При наличии различных типов ячейки они должен иметь строку другой идентификатор при создании экземпляров и по запросу из очереди повторного использования.

### <a name="cell-reuse-in-ios-6"></a>Повторное использование ячеек в iOS 6 +

iOS 6 добавлены ячейки повторное использование шаблон, аналогичный один Знакомство с представлениями коллекции. Несмотря на то, что существующий шаблон повторного использования, приведенный выше по-прежнему поддерживается для обратной совместимости, этот шаблон является предпочтительным, так как он избавляет от необходимости проверку значений null в ячейке.

С помощью новой модели приложение регистрирует класс ячейки или xib для использования с помощью вызова `RegisterClassForCellReuse` или `RegisterNibForCellReuse` в конструктор контроллера. Затем, когда вывод ячейки в `GetCell` простым вызовом метода `DequeueReusableCell` передав идентификатор регистрации класс ячейки или xib и пути индекса.

Например следующий код регистрирует пользовательский класс ячейки в UITableViewController:

```csharp
public class MyTableViewController : UITableViewController
{
  static NSString MyCellId = new NSString ("MyCellId");

  public MyTableViewController ()
  {
    TableView.RegisterClassForCellReuse (typeof(MyCell), MyCellId);
  }
  ...
}
```

С помощью класса MyCell регистрации, ячейки может быть выведен из очереди в `GetCell` метод `UITableViewSource` без необходимости дополнительной пустоту, как показано ниже:

```csharp
class MyTableSource : UITableViewSource
{
  public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
  {
    // if cell is not available in reuse pool, iOS will create one automatically
    // no need to do null check and create cell manually
    var cell = (MyCell) tableView.DequeueReusableCell (MyCellId, indexPath);

    // do whatever you need to with cell, such as assigning properties, etc.

    return cell;
  }
}
```

Следует помнить, что при использовании нового шаблона повторного использования с пользовательский класс ячейки, вам необходимо реализовать конструктор, принимающий `IntPtr`, как показано в следующем фрагменте, в противном случае Objective-C будет невозможно создать экземпляр класса ячейки:

```csharp
public class MyCell : UITableViewCell
{
  public MyCell (IntPtr p):base(p)
  {
  }
  ...
}
```

Вы можете увидеть примеры в разделах описано выше в **BasicTable** образец, связанные с этой статьей.

<a name="Adding_an_Index" />

## <a name="adding-an-index"></a>Добавление индекса

Индекс помогает пользователям прокручивать длинных списках, обычно представлены в алфавитном порядке несмотря на то, что можно индексировать по критериям, независимо от необходимости. **BasicTableIndex** пример будет загружен из файла, чтобы продемонстрировать индекс гораздо более длинный список элементов. Каждый элемент в индексе соответствует «раздел» таблицы.

 [![](populating-a-table-with-data-images/image5.png "Отображение")](populating-a-table-with-data-images/image5.png#lightbox)

Для поддержки «sections», данные для таблицы необходимо группировать, чтобы в образце BasicTableIndex создается `Dictionary<>` из массива строк, используя первую букву каждого элемента в качестве ключа словаря:

```csharp
indexedTableItems = new Dictionary<string, List<string>>();
foreach (var t in items) {
    if (indexedTableItems.ContainsKey (t[0].ToString ())) {
        indexedTableItems[t[0].ToString ()].Add(t);
    } else {
        indexedTableItems.Add (t[0].ToString (), new List<string>() {t});
    }
}
keys = indexedTableItems.Keys.ToArray ();
```

`UITableViewSource` Подкласс должна следующие методы добавлены или изменены для использования `Dictionary<>` :

-   **NumberOfSections** — этот метод является необязательным, по умолчанию в таблице предполагается один раздел. При отображении индекса этот метод должен возвращать число элементов в индексе (например, 26 Если индекс содержит все буквы английского алфавита).
-   **RowsInSection** — возвращает количество строк в конкретном разделе.
-   **SectionIndexTitles** — возвращает массив строк, которые будут использоваться для отображения индекса. Пример кода возвращает массив букв.


Обновленные методы в образце файла **BasicTableIndex/TableSource.cs** выглядеть следующим образом:

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    return keys.Length;
}
public override nint RowsInSection (UITableView tableview, nint section)
{
    return indexedTableItems[keys[section]].Count;
}
public override string[] SectionIndexTitles (UITableView tableView)
{
    return keys;
}
```

Индексы обычно используются только с простой таблицы стилей.


<a name="Adding_Headers_and_Footers" />

## <a name="adding-headers-and-footers"></a>Добавление верхних и нижних колонтитулов

Верхние и нижние колонтитулы позволяют визуально сгруппировать строки в таблице. Необходимые структуры данных очень сходно с добавлением индекса — `Dictionary<>` работает действительно хорошо. Вместо использования алфавита, чтобы группировать ячейки, в этом примере будет группировать овощи botanical типом.
Вывод выглядит следующим образом.

 [![](populating-a-table-with-data-images/image6.png "Пример верхние и нижние колонтитулы")](populating-a-table-with-data-images/image6.png#lightbox)

Чтобы отобразить верхние и нижние колонтитулы `UITableViewSource` подкласс требует эти дополнительные методы:

-   **TitleForHeader** — возвращает текст, который используется в качестве заголовка
-   **TitleForFooter** — возвращает текст для использования в качестве нижнего колонтитула.


Обновленные методы в образце файла **BasicTableHeaderFooter/Code/TableSource.cs** выглядеть следующим образом:

```csharp
public override string TitleForHeader (UITableView tableView, nint section)
{
    return keys[section];
}
public override string TitleForFooter (UITableView tableView, nint section)
{
    return indexedTableItems[keys[section]].Count + " items";
}
```

Можно настроить внешний вид заголовка и нижнего колонтитула с представлением объекта, использование `GetViewForHeader` и `GetViewForFooter` метод переопределяет на `UITableViewSource`.


## <a name="related-links"></a>Связанные ссылки

- [WorkingWithTables (пример)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
