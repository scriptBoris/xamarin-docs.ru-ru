---
title: Путем перетаскивания в Xamarin.iOS
description: В этом документе описывается, как реализовать функцию перетаскивания в приложениях Xamarin.iOS с помощью интерфейсов API, представленная в iOS 11. В частности, в нем описывается включение перетаскивания в UITableView.
ms.prod: xamarin
ms.assetid: 0D39C4C3-D169-42F8-B3FA-7F98CF0B6F1F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/05/2017
ms.openlocfilehash: aa93e015a399e733a2bb52f087a1e482bc23a00a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112082"
---
# <a name="drag-and-drop-in-xamarinios"></a>Путем перетаскивания в Xamarin.iOS

_Реализация перетаскивания для iOS 11_

iOS 11 включает в себя перетащите и поместите поддержки для копирования данных между приложениями на устройствах iPad. Пользователи могут выбрать и перетащите все типы содержимого из приложений размещается side-by-side, или путем перетаскивания над значка приложения, который будет активировать приложения, чтобы открыть и разрешить передачу данных для удаления:

![Пример перетаскивания из пользовательского приложения в приложение заметки](drag-and-drop-images/drag-drop-sml.png)

> [!NOTE]
> Перетаскивание доступна только в том же приложении на iPhone.

Рассмотрите возможность использования перетащите операций перетаскивания в любом содержимого можно создать или изменить:

- Текстовые элементы управления поддерживают перетаскивание для всех приложений, рассчитанных iOS 11, не требуется никаких дополнительных действий.
- Представления и таблицы представления коллекций включают улучшения в iOS 11, упростить добавление перетащите и поместите поведение.
- Для поддержки перетаскивания и удалить после дополнительной настройки можно сделать другое представление.

При Добавление операции перетаскивания поддерживают в свои приложения, можно предоставить различные уровни точности содержимого; Например можно предоставить форматированного текста и текстовая версия бюллетеня данные таким образом, принимающее приложение можно выбрать, который подойдет в цели перетаскивания. Можно также для настройки визуализации перетаскивания, а также для перетаскивания нескольких элементов за один раз.

## <a name="drag-and-drop-with-text-controls"></a>Перетаскивание с текстовыми элементами управления

`UITextView` и `UITextField` автоматически поддерживают выделенный текст out, перетаскивание текста содержимого в.

<a name="uitableview" />

## <a name="drag-and-drop-with-uitableview"></a>Перетаскивание с UITableView

`UITableView` предусмотрена встроенная обработка перетаскивание взаимодействия с строк таблицы, требуя лишь несколько методов, чтобы включить поведение по умолчанию.

Состоит из двух интерфейсов:

- `IUITableViewDragDelegate` — Сведения о пакетах перетаскивания будет запущена в табличном представлении.
- `IUITableViewDropDelegate` — Обрабатывает данные, при перетаскивании попытки и завершения.

В [пример DragAndDropTableView](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView/) оба эти два интерфейса реализуются на `UITableViewController` классе, а также делегата и источник данных. Они присвоены `ViewDidLoad` метод:

```csharp
this.TableView.DragDelegate = this;
this.TableView.DropDelegate = this;
```

Минимальный требуемый код для этих двух интерфейсов описан ниже.

### <a name="table-view-drag-delegate"></a>Делегат перетащите представление таблицы

Это единственный метод _требуется_ для поддержки перетаскивания строки из таблицы представления является `GetItemsForBeginningDragSession`. Если пользователь начинает перетаскивание строки, этот метод будет вызываться.

Реализация показана ниже. Он извлекает данные, связанные с перетаскиваемого строки, кодирует его и настраивает `NSItemProvider` определяет, как приложения будет обрабатывать «удалить» часть операции (например, является ли они могут обрабатывать тип данных, `PlainText`, в примере):

```csharp
public UIDragItem[] GetItemsForBeginningDragSession (UITableView tableView,
  IUIDragSession session, NSIndexPath indexPath)
{
  // gets the 'information' to be dragged
  var placeName = model.PlaceNames[indexPath.Row];
  // convert to NSData representation
  var data = NSData.FromString(placeName, NSStringEncoding.UTF8);
  // create an NSItemProvider to describe the data
  var itemProvider = new NSItemProvider();
  itemProvider.RegisterDataRepresentation(UTType.PlainText,
                                NSItemProviderRepresentationVisibility.All,
                                (completion) =>
  {
    completion(data, null);
    return null;
  });
  // wrap in a UIDragItem
  return new UIDragItem[] { new UIDragItem(itemProvider) };
}
```

Существует множество вспомогательных методов для перетаскивания делегата, который можно реализовать для настройки поведения перетаскивания, например для предоставления нескольких представлений данных, которые могут использоваться в целевых приложений (таких как форматированный текст как контейнер в виде обычного текста или вектор и точечный рисунок версии рисунка). Можно также предоставить пользовательские данные представления для использования при перетаскивании в одном приложении.

### <a name="table-view-drop-delegate"></a>Делегат Drop представление таблицы

Методы для делегата, drop, называются при операции перетаскивания, возникающего в табличное представление, или завершения над ним. Необходимые методы определяют, разрешено ли данные удаляемую, и действия, предпринимаемые при завершения:

- `CanHandleDropSession` — Во время перетаскивания в ходе выполнения и потенциально перетащен на приложения, этот метод определяет, разрешено ли перетаскиваемые данные удаляемую.
- `DropSessionDidUpdate` — Перетаскивание во время выполнения, этот метод вызывается для определения, какие действия. Сведения из таблицы представления, перетаскиваемый над, перетащите сеанса и путь возможно индекс можно использовать для определения поведения и визуальную обратную связь, предоставленному пользователю.
- `PerformDrop` — Когда пользователь завершает раскрывающегося (результате поднятия пальцев), этот метод извлекает перетаскиваемые данные и изменяет представление таблицы для добавления данных в новую строку (или строки).

#### <a name="canhandledropsession"></a>CanHandleDropSession

`CanHandleDropSession` Указывает, принимает ли таблицы представления перетаскиваемых данных. В этом фрагменте кода `CanLoadObjects` используется для подтверждения, что это представление таблицы может принять строковых данных.

```csharp
public bool CanHandleDropSession(UITableView tableView, IUIDropSession session)
{
  return session.CanLoadObjects(typeof(NSString));
}
```

#### <a name="dropsessiondidupdate"></a>DropSessionDidUpdate

`DropSessionDidUpdate` Метод вызывается повторно, операция перетаскивания во время выполнения, для предоставления визуальных сигналов пользователю.

В следующем коде `HasActiveDrag` позволяет определить, возникло ли операцию в текущем представлении таблицы. Если Да, для перемещения разрешены только одной строки.
Если перетаскивание из другого источника, будут указаны операции копирования:

```csharp
public UITableViewDropProposal DropSessionDidUpdate(UITableView tableView, IUIDropSession session, NSIndexPath destinationIndexPath)
{
  // The UIDropOperation.Move operation is available only for dragging within a single app.
  if (tableView.HasActiveDrag)
  {
    if (session.Items.Length > 1)
    {
        return new UITableViewDropProposal(UIDropOperation.Cancel);
    } else {
        return new UITableViewDropProposal(UIDropOperation.Move, UITableViewDropIntent.InsertAtDestinationIndexPath);
    }
  } else {
    return new UITableViewDropProposal(UIDropOperation.Copy, UITableViewDropIntent.InsertAtDestinationIndexPath);
  }
}
```

Операция перетаскивания может принимать одно из `Cancel`, `Move`, или `Copy`.

Цель перетаскивания можно вставить новую строку, или добавьте/Добавление данных в существующую строку.

#### <a name="performdrop"></a>PerformDrop

`PerformDrop` Метод вызывается, когда пользователь завершает операцию и изменяет представление и данных исходной таблицы в соответствии с перетаскиваемых данных.

```csharp
public void PerformDrop(UITableView tableView, IUITableViewDropCoordinator coordinator)
{
  NSIndexPath indexPath, destinationIndexPath;
  if (coordinator.DestinationIndexPath != null)
  {
    indexPath = coordinator.DestinationIndexPath;
    destinationIndexPath = indexPath;
  }
  else
  {
    // Get last index path of table view
    var section = tableView.NumberOfSections() - 1;
    var row = tableView.NumberOfRowsInSection(section);
    destinationIndexPath = NSIndexPath.FromRowSection(row, section);
  }
  coordinator.Session.LoadObjects(typeof(NSString), (items) =>
  {
    // Consume drag items
    List<string> stringItems = new List<string>();
    foreach (var i in items)
    {
      var q = NSString.FromHandle(i.Handle);
      stringItems.Add(q.ToString());
    }
    var indexPaths = new List<NSIndexPath>();
    for (var j = 0; j < stringItems.Count; j++)
    {
      var indexPath1 = NSIndexPath.FromRowSection(destinationIndexPath.Row + j, destinationIndexPath.Section);
      model.AddItem(stringItems[j], indexPath1.Row);
      indexPaths.Add(indexPath1);
    }
    tableView.InsertRows(indexPaths.ToArray(), UITableViewRowAnimation.Automatic);
  });
}
```

Асинхронно загружающий объекты больших объемов данных можно добавить дополнительный код.

### <a name="testing-drag-and-drop"></a>Тестирование путем перетаскивания

IPad необходимо использовать для тестирования [пример](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView/).
Откройте пример вместе с другим приложением (например, заметки) и перетащите строк и текст между ними:

![Снимок экрана выполняется операция перетаскивания](drag-and-drop-images/01-sml.png)


## <a name="related-links"></a>Связанные ссылки

- [Перетаскивание рекомендации по работе с человеческим интерфейсом (Apple)](https://developer.apple.com/ios/human-interface-guidelines/interaction/drag-and-drop/)
- [Путем перетаскивания таблицы Просмотр результата](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView/)
- [Перетаскивание коллекции просмотр результата](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropCollectionView)
- [Знакомство с Drag and Drop (WWDC) (видео)](https://developer.apple.com/videos/play/wwdc2017/203/)
- [Путем перетаскивания с помощью коллекции и таблицы представления (WWDC) (видео)](https://developer.apple.com/videos/play/wwdc2017/223/)
