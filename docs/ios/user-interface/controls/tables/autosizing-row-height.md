---
title: Автоматическое изменение высоты строки в Xamarin.iOS
description: В этом документе описывается добавление для приложений Xamarin.iOS представление строк таблицы, высота которого изменения на основе содержимого. В нем описывается макет ячейки в конструкторе iOS и включить автоматическое изменение размеров высоты.
ms.prod: xamarin
ms.assetid: CE45A385-D40A-482A-90A0-E8382C2BFFB9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: e4446abc73817eb0672cd10a69ff6f738de0c1e1
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116476"
---
# <a name="auto-sizing-row-height-in-xamarinios"></a>Автоматическое изменение высоты строки в Xamarin.iOS

Начиная с iOS 8, Apple добавлена возможность создания представления таблицы (`UITableView`), может автоматически увеличиваться и уменьшаться высота данной строки, в соответствии с размером его содержимого, используя автоматический макет, размеры и ограничения.

iOS 11 была добавлена возможность для строк, чтобы автоматически развернуть. Заголовки, нижние колонтитулы и ячейки можно теперь будет автоматически подбираться зависимости от их содержимого. Тем не менее если таблица создается в конструкторе, iOS и конструктора Interface Builder, или если он исправил высоты строк необходимо вручную включить self изменение размера ячеек, как описано в этом руководстве.

## <a name="cell-layout-in-the-ios-designer"></a>Макет ячейки в конструкторе iOS

Откройте раскадровку для представления таблицы, что требуется, чтобы автоматически строки-resize в iOS Designer, выберите ячейки *прототип* и макет ячейки. Пример:

[![](autosizing-row-height-images/table01.png "Проект прототипа ячейки")](autosizing-row-height-images/table01.png#lightbox)

Для каждого элемента в прототипе добавьте ограничений для поддержания элементы в правильном положении по мере изменения размера представления таблицы для поворота и iOS разных размеров экрана устройства. Например, закрепление `Title` сверху, слева и справа от ячейки *представление содержимого*:

[![](autosizing-row-height-images/table02.png "Закрепление заголовка сверху, слева и справа от представления содержимого ячеек")](autosizing-row-height-images/table02.png#lightbox)

В случае в нашем примере таблицы небольшую `Label` (в разделе `Title`) это поле, можно сжать и увеличиваться для увеличения или уменьшения высоты строки. Чтобы этого добиться, добавьте следующие ограничения, чтобы закрепить левой, правой, верхней и нижней метки:

[![](autosizing-row-height-images/table03.png "Эти ограничения, чтобы закрепить левой, правой, верхней и нижней метки")](autosizing-row-height-images/table03.png#lightbox)

Теперь, когда мы полностью иметь ограничения элементов ячейки, нам нужно уточнить, какой элемент должен быть растянут. Чтобы сделать это, установите **содержимого приоритет надо тебя обнять** и **содержимого приоритет сопротивление сжатия** в **макета** части панели свойств:

[![](autosizing-row-height-images/table03a.png "В разделе макета панели свойств")](autosizing-row-height-images/table03a.png#lightbox)

Для элемента, который требуется развернуть, чтобы иметь значение **нижней** надо тебя обнять приоритет и **нижней** значение приоритета сопротивление сжатия.

Далее, нам нужно выбрать ячейку прототип и присвойте ей уникальное **идентификатор**:

[![](autosizing-row-height-images/table04.png "Предоставляя уникальный идентификатор ячейки прототипа")](autosizing-row-height-images/table04.png#lightbox)

В случае нашего примера `GrowCell`. Мы будем использовать это значение позже, когда мы заполнения таблицы.

> [!IMPORTANT]
> Если таблица содержит более одного типа ячейки (**прототип**), необходимо убедиться, каждый тип имеет собственный уникальный `Identifier` для автоматического изменения размеров строк для работы.

Для каждого элемента прототипа наших ячейки, назначить **имя** в предоставлении доступа к C# кода. Пример:

[![](autosizing-row-height-images/table05.png "Назначить имя, чтобы предоставить доступ к C# кода")](autosizing-row-height-images/table05.png#lightbox)

Затем добавьте пользовательский класс для `UITableViewController`, `UITableView` и `UITableCell` (прототип). Пример: 

[![](autosizing-row-height-images/table06.png "Добавление пользовательского класса UITableViewController, UITableView и UITableCell")](autosizing-row-height-images/table06.png#lightbox)

Наконец, чтобы убедиться, что все ожидаются содержимое отображается в нашей метки, установите **строки** свойства `0`:

[![](autosizing-row-height-images/table06.png "Свойство строки, значение 0")](autosizing-row-height-images/table06a.png#lightbox)

С помощью пользовательского интерфейса определен добавим код, автоподбора размера высоты строки.

## <a name="enabling-auto-resizing-height"></a>Включить автоматическое изменение размеров высоты

В любой источник данных нашего представления таблицы (`UITableViewDatasource`) или источника (`UITableViewSource`), когда мы вывода из очереди ячейки, нам нужно использовать `Identifier` , определенный в конструкторе. Пример:

```csharp
public string CellID {
    get { return "GrowCell"; }
}
...

public override UITableViewCell GetCell (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (CellID, indexPath) as GrowRowTableCell;
    var item = Items [indexPath.Row];

    // Setup
    cell.Image = UIImage.FromFile(item.ImageName);
    cell.Title = item.Title;
    cell.Description = item.Description;

    return cell;
}
```

По умолчанию представления таблицы будет устанавливаться для автоматическое изменение размеров высоты строки. Чтобы обеспечить это, `RowHeight` свойство должно быть присвоено `UITableView.AutomaticDimension`. Нам также нужно установить `EstimatedRowHeight` свойство в наших `UITableViewController`. Пример:

```csharp
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Initialize table
    TableView.DataSource = new GrowRowTableDataSource(this);
    TableView.Delegate = new GrowRowTableDelegate (this);
    TableView.RowHeight = UITableView.AutomaticDimension;
    TableView.EstimatedRowHeight = 40f;
    TableView.ReloadData ();
}
```

Эта оценка не должен быть точным, просто грубой оценки того, средняя высота каждой строки в представлении таблицы.

Этот код в месте, при запуске приложения, каждая строка будет сжать и расширяться в зависимости от высоты последняя метка в прототипе ячейки. Пример:

[![](autosizing-row-height-images/table07.png "Запустите образец таблицы")](autosizing-row-height-images/table07.png#lightbox)


## <a name="related-links"></a>Связанные ссылки

- [GrowRowTable (пример)](https://developer.xamarin.com/samples/monotouch/GrowRowTable/)
