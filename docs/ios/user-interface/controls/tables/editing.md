---
title: Редактирование в таблицах с помощью Xamarin.iOS
description: В этом документе описывается изменение таблиц в Xamarin.iOS. Здесь рассматриваются проведите по экрану, чтобы удалить, режим правки и вставки строки.
ms.prod: xamarin
ms.assetid: EC197F25-E865-AFA3-E5CF-B33FAB7744A0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 1267de341a88130c18254f414d2fbb1c42595a0c
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50104826"
---
# <a name="editing-tables-with-xamarinios"></a>Редактирование в таблицах с помощью Xamarin.iOS

Функции редактирования таблиц включены путем переопределения методов в `UITableViewSource` подкласс. Самый простой редактирования правило жест прокрутки для удаления, который может быть реализована с помощью переопределения одного метода.
Более сложные изменения (включая перемещение строк) можно сделать с таблицей в режиме редактирования.

## <a name="swipe-to-delete"></a>Проведите по экрану, чтобы удалить

Проведите по экрану, чтобы удалить компонент является естественным жестов в iOS, пользователи ожидают, что. 

 [![](editing-images/image10.png "Пример того, проведите по экрану Delete")](editing-images/image10.png#lightbox)

Существует три метода переопределения, влияющие на жест прокрутки для отображения **удалить** кнопки в ячейке:

-   **CommitEditingStyle** — обнаруживает источник таблицы, если этот метод переопределяется и автоматически включает жест прокрутки для удаления. Реализация этого метода должна вызывать `DeleteRows` на `UITableView` заставить ячеек исчезнуть, а также удаление базовых данных из модели (например, массив, словарь или базы данных). 
-   **CanEditRow** — Если CommitEditingStyle переопределяется, предполагается, что все строки можно было изменять. Если этот метод реализован и возвращает значение false, (для некоторых определенных строк или для всех строк), затем жест прокрутки для удаления будет недоступен в этой ячейке. 
-   **TitleForDeleteConfirmation** : при необходимости указывает текст для **удалить** кнопки. Если этот метод не реализован на кнопке отображается надпись будет «Удалить». 


Эти методы реализованы в `TableSource` класса следующим образом:

```csharp
public override void CommitEditingStyle (UITableView tableView, UITableViewCellEditingStyle editingStyle, Foundation.NSIndexPath indexPath)
{
    switch (editingStyle) {
        case UITableViewCellEditingStyle.Delete:
            // remove the item from the underlying data source
            tableItems.RemoveAt(indexPath.Row);
            // delete the row from the table
            tableView.DeleteRows (new NSIndexPath[] { indexPath }, UITableViewRowAnimation.Fade);
            break;
        case UITableViewCellEditingStyle.None:
            Console.WriteLine ("CommitEditingStyle:None called");
            break;
    }
}
public override bool CanEditRow (UITableView tableView, NSIndexPath indexPath)
{
    return true; // return false if you wish to disable editing for a specific indexPath or for all rows
}
public override string TitleForDeleteConfirmation (UITableView tableView, NSIndexPath indexPath)
{   // Optional - default text is 'Delete'
    return "Trash (" + tableItems[indexPath.Row].SubHeading + ")";
}
```

В этом примере `UITableViewSource` был обновлен для использования `List<TableItem>` (а не строковый массив) как источник данных, так как он поддерживает добавление и удаление элементов из коллекции.


## <a name="edit-mode"></a>Режим правки

Когда таблицы находится в режиме редактирования пользователь видит красный «stop» мини-приложения в каждой строке, где обнаружите кнопку «Удалить», при касании. В таблице также отображается значок 'handle', чтобы указать, что строки может быть передвинута для изменения порядка.
**TableEditMode** Образец реализует эти возможности, как показано.

 [![](editing-images/image11.png "Этот пример TableEditMode реализует эти возможности, как показано")](editing-images/image11.png#lightbox)

Существует несколько различных методов на `UITableViewSource` , влияющие на поведение режима редактирования таблиц:

-   **CanEditRow** — определяет, может редактироваться каждой строки. Возвращает значение false, чтобы предотвратить считывание для удаления и удаления в режиме редактирования. 
-   **CanMoveRow** — возвращаемое значение true для включения маркера перемещения или значение false, чтобы предотвратить перемещение. 
-   **EditingStyleForRow** – когда она находится в режиме редактирования, возвращаемое значение из этого метода, определяет ли в ячейке отобразится значок удаления красный или зеленый значок добавления. Вернуть `UITableViewCellEditingStyle.None` Если строки не должны быть редактируемыми. 
-   **MoveRow** — вызывается, когда строка перемещается, структуры данных можно изменить в соответствии с данными, которое будет отображаться в таблице. 


Для первых трех методов реализуется сравнительно проста — Если вы хотите использовать `indexPath` Чтобы изменить поведение определенных строк, просто жестко закодировать возвращаемые значения для всей таблицы.

```csharp
public override bool CanEditRow (UITableView tableView, NSIndexPath indexPath)
{
    return true; // return false if you wish to disable editing for a specific indexPath or for all rows
}
public override bool CanMoveRow (UITableView tableView, NSIndexPath indexPath)
{
    return true; // return false if you don't allow re-ordering
}
public override UITableViewCellEditingStyle EditingStyleForRow (UITableView tableView, NSIndexPath indexPath)
{
    return UITableViewCellEditingStyle.Delete; // this example doesn't support Insert
}
```

`MoveRow` Реализации более сложной, так как они нужны для изменения структуры данных в соответствии с порядком новый. Так как данные реализуется как `List` приведенный ниже код удаляет элемент данных на ее старое расположение и вставляет его в новом расположении. При хранении данных в таблицу базы данных SQLite, со столбцом «order» (например), этот метод вместо этого необходимо выполнить некоторые операции SQL, чтобы изменить порядок номеров в этом столбце.

```csharp
public override void MoveRow (UITableView tableView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
{
    var item = tableItems[sourceIndexPath.Row];
    var deleteAt = sourceIndexPath.Row;
    var insertAt = destinationIndexPath.Row;
    
    // are we inserting 
    if (destinationIndexPath.Row < sourceIndexPath.Row) {
        // add one to where we delete, because we're increasing the index by inserting
        deleteAt += 1;
    } else {
        // add one to where we insert, because we haven't deleted the original yet
        insertAt += 1;
    }
    tableItems.Insert (insertAt, item);
    tableItems.RemoveAt (deleteAt);
}
```

Наконец, чтобы получить таблицу в режим редактирования, **изменить** кнопка должна вызывать `SetEditing` следующим образом

```csharp
table.SetEditing (true, true);
```

и когда пользователь закончил редактирование, **сделать** кнопку следует отключить режим редактирования:

```csharp
table.SetEditing (false, true);
```


## <a name="row-insertion-editing-style"></a>Стиль редактирования строки вставки

Вставки строки из таблицы является редко пользовательский интерфейс — является основным примером в приложениях стандартный iOS **изменить контакт** экрана. На этом снимке экрана показано, как работает функция вставки строк — в изменить режим, имеется дополнительная строка, (при щелчке) вставляет дополнительных строк в данных. При завершении редактирования, временный **(Добавить новый)** строка удаляется.

 [![](editing-images/image12.png "По завершении редактирования временный добавить новые строки удаляется")](editing-images/image12.png#lightbox)

Существует несколько различных методов на `UITableViewSource` , влияющие на поведение режима редактирования таблиц. Эти методы были реализованы как показано ниже в примере кода:

-   **EditingStyleForRow** — возвращает `UITableViewCellEditingStyle.Delete` для строки, содержащие данные и возвращает `UITableViewCellEditingStyle.Insert` для последней строки (которая будет добавляться в частности, вести себя как кнопки вставки). 
-   **CustomizeMoveTarget** — хотя пользователь перемещается возвращаемое значение из этого метода необязательно ячейки можно переопределить их выбор расположения. Это означает, что может помешать их нормальной «удаление» ячейки в определенных позициях — как показано в примере, блокирует перемещение после любой строке **(Добавить новый)** строки. 
-   **CanMoveRow** — возвращаемое значение true для включения маркера перемещения или значение false, чтобы предотвратить перемещение. В примере последней строки содержится «маркер перемещения» скрыты, так как он предназначен для сервера как только кнопки вставки. 


Также добавим две настраиваемые методы для добавления строки «insert» и удалите его еще раз больше не требуется. Они вызываются из **изменить** и **сделать** кнопки:

-   **WillBeginTableEditing** — когда **изменить** кнопка затронутых просроченных вызовы `SetEditing` для помещения в таблице в режиме редактирования. Это вынуждает метод WillBeginTableEditing, где мы отображаем **(Добавить новый)** строку в конец таблицы в качестве «кнопки "Вставить"». 
-   **DidFinishTableEditing** — при касании "Готово" `SetEditing` вызывается снова, чтобы отключить режим редактирования. Пример кода удаляет **(Добавить новый)** строки в таблице, при редактировании больше не требуется. 


Эти переопределенные методы реализуются в образце файла **TableEditModeAdd/Code/TableSource.cs**:

```csharp
public override UITableViewCellEditingStyle EditingStyleForRow (UITableView tableView, NSIndexPath indexPath)
{
    if (tableView.Editing) {
        if (indexPath.Row == tableView.NumberOfRowsInSection (0) - 1)
            return UITableViewCellEditingStyle.Insert;
        else
            return UITableViewCellEditingStyle.Delete;
    } else // not in editing mode, enable swipe-to-delete for all rows
        return UITableViewCellEditingStyle.Delete;
}
public override NSIndexPath CustomizeMoveTarget (UITableView tableView, NSIndexPath sourceIndexPath, NSIndexPath proposedIndexPath)
{
    var numRows = tableView.NumberOfRowsInSection (0) - 1; // less the (add new) one
    if (proposedIndexPath.Row >= numRows)
        return NSIndexPath.FromRowSection(numRows - 1, 0);
    else
        return proposedIndexPath;
} 
public override bool CanMoveRow (UITableView tableView, NSIndexPath indexPath)
{
    return indexPath.Row < tableView.NumberOfRowsInSection (0) - 1;
}
```

Эти две пользовательские методы используются для добавления и удаления **(Добавить новый)** строки, если режим редактирования таблицы включен или отключен:

```csharp
public void WillBeginTableEditing (UITableView tableView)
{
    tableView.BeginUpdates ();
    // insert the 'ADD NEW' row at the end of table display
    tableView.InsertRows (new NSIndexPath[] { 
            NSIndexPath.FromRowSection (tableView.NumberOfRowsInSection (0), 0) 
        }, UITableViewRowAnimation.Fade);
    // create a new item and add it to our underlying data (it is not intended to be permanent)
    tableItems.Add (new TableItem ("(add new)"));
    tableView.EndUpdates (); // applies the changes
}
public void DidFinishTableEditing (UITableView tableView)
{
    tableView.BeginUpdates ();
    // remove our 'ADD NEW' row from the underlying data
    tableItems.RemoveAt ((int)tableView.NumberOfRowsInSection (0) - 1); // zero based :)
    // remove the row from the table display
    tableView.DeleteRows (new NSIndexPath[] { NSIndexPath.FromRowSection (tableView.NumberOfRowsInSection (0) - 1, 0) }, UITableViewRowAnimation.Fade);
    tableView.EndUpdates (); // applies the changes
}
```

Наконец, этот код создает экземпляр **изменить** и **сделать** кнопки, с помощью лямбда-выражения, включающие или отключающие режим правки, когда они затронут:

```csharp
done = new UIBarButtonItem(UIBarButtonSystemItem.Done, (s,e)=>{
    table.SetEditing (false, true);
    NavigationItem.RightBarButtonItem = edit;
    tableSource.DidFinishTableEditing(table);
});
edit = new UIBarButtonItem(UIBarButtonSystemItem.Edit, (s,e)=>{
    if (table.Editing)
        table.SetEditing (false, true); // if we've half-swiped a row
    tableSource.WillBeginTableEditing(table);
    table.SetEditing (true, true);
    NavigationItem.LeftBarButtonItem = null;
    NavigationItem.RightBarButtonItem = done;
});
```

Этот шаблон пользовательского интерфейса вставки строк не используется очень часто, однако вы также можете использовать `UITableView.BeginUpdates` и `EndUpdates` методы для анимации вставки и удаления ячеек в любой таблице. Правила использования этих методов является возвращение разницу по `RowsInSection` между `BeginUpdates` и `EndUpdates` вызовы должны совпадать на общее количество ячеек, добавлять или удалять с помощью `InsertRows` и `DeleteRows` методы. Если базовый источник данных не изменяется для сопоставления операций вставки, удаления таблицы представления произойдет ошибка.


## <a name="related-links"></a>Связанные ссылки

- [WorkingWithTables (пример)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
