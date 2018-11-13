---
title: Ячейки Xamarin.Forms
description: Ячейки Xamarin.Forms могут добавляться к ListView и TableViews. В этой статье перечислены ячеек, включенных в Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 77DA0C89-35D6-4C09-A072-3ADE53FD56CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 34e28d8532c232549c6f45e549787885a9155684
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/11/2018
ms.locfileid: "51526862"
---
# <a name="xamarinforms-cells"></a>Ячейки Xamarin.Forms

_Ячейки Xamarin.Forms могут добавляться к ListView и TableViews._

Объект *ячейки* специализированного элемента, используемого для элементов в таблице в нем описано, как должны отображаться каждый элемент в списке. [ `Cell` ](xref:Xamarin.Forms.Cell) Класс является производным от [ `Element` ](xref:Xamarin.Forms.Element), из которого [ `VisualElement` ](xref:Xamarin.Forms.Element) также является производным. Ячейки сама по себе не визуальный элемент; Вместо этого это шаблон для создания визуального элемента.

`Cell` используется исключительно с [ `ListView` ](views.md#listView) и [ `TableView` ](views.md#tableView) элементов управления. Чтобы узнать, как использовать и настраивать ячеек, обратитесь к [ `ListView` ](~/xamarin-forms/user-interface/listview/index.md) и [ `TableView` ](~/xamarin-forms/user-interface/tableview.md) документации.

## <a name="cells"></a>Ячейки

Xamarin.Forms поддерживает следующие типы ячейки:

<a name="textCell" />

### <a name="textcell"></a>TextCell

|     |     |
| --- | --- |
| Объект [ `TextCell` ](xref:Xamarin.Forms.TextCell) отображает один или два текстовые строки. Задайте [ `Text` ](xref:Xamarin.Forms.TextCell.Text) свойство и, возможно, [ `Detail` ](xref:Xamarin.Forms.TextCell.Detail) свойство в эти текстовые строки.<br /><br />[Документация по API](xref:Xamarin.Forms.TextCell) / [руководство](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#TextCell) | [![Пример TextCell](cells-images/TextCell.png "пример TextCell")](cells-images/TextCell-Large.png#lightbox "TextCell пример")<br />[Код C# для этой страницы](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TextCellDemoPage.cs) / [страницы XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TextCellDemoPage.xaml) |
|     |     |

### <a name="imagecell"></a>ImageCell

|     |     |
| --- | --- |
| [ `ImageCell` ](xref:Xamarin.Forms.ImageCell) Отображает те же сведения, что [ `TextCell` ](#textCell) , но включает в себя Битовая карта, заданное с помощью [ `Source` ](xref:Xamarin.Forms.Image.Source) свойство.<br /><br />[Документация по API](xref:Xamarin.Forms.ImageCell) / [руководство](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#ImageCell) | [![Пример ImageCell](cells-images/ImageCell.png "пример ImageCell")](cells-images/ImageCell-Large.png#lightbox "ImageCell пример")<br />[Код C# для этой страницы](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageCellDemoPage.cs) / [страницы XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageCellDemoPage.xaml) |
|     |     |

### <a name="switchcell"></a>SwitchCell

|     |     |
| --- | --- |
| [ `SwitchCell` ](xref:Xamarin.Forms.SwitchCell) Содержит текст, набор с [ `Text`"](xref:Xamarin.Forms.SwitchCell.Text) свойство и переключатель изначально задаются с двоичным [ `On` ](xref:Xamarin.Forms.SwitchCell.On) свойство. Обрабатывать [ `OnChanged` ](xref:Xamarin.Forms.SwitchCell.OnChanged) когда уведомления о событии `On` изменения свойств.<br /><br />[Документация по API](xref:Xamarin.Forms.SwitchCell) / [руководство](~/xamarin-forms/user-interface/tableview.md#switchcell) | [![Пример SwitchCell](cells-images/SwitchCell.png "пример SwitchCell")](cells-images/SwitchCell-Large.png#lightbox "SwitchCell пример")<br />[Код C# для этой страницы](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchCellDemoPage.cs) / [страницы XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchCellDemoPage.xaml) |
|     |     |

### <a name="entrycell"></a>EntryCell

|     |     |
| --- | --- |
| [ `EntryCell` ](xref:Xamarin.Forms.EntryCell) Определяет [ `Label` ](xref:Xamarin.Forms.EntryCell.Label) свойства, которое определяет ячейку и строку ввода и редактирования текста в [ `Text` ](xref:Xamarin.Forms.EntryCell.Text) свойство. Обрабатывать [ `Completed` ](xref:Xamarin.Forms.EntryCell.Completed) событий, чтобы получать уведомления, когда пользователь завершит ввод текста.<br /><br />[Документация по API](xref:Xamarin.Forms.EntryCell) / [руководство](~/xamarin-forms/user-interface/tableview.md#entrycell) | [![Пример EntryCell](cells-images/EntryCell.png "пример EntryCell")](cells-images/EntryCell-Large.png#lightbox "EntryCell пример")<br />[Код C# для этой страницы](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryCellDemoPage.cs) / [страницы XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryCellDemoPage.xaml) |
|     |     |


## <a name="related-links"></a>Связанные ссылки

- [Введение в Xamarin.Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Пример Xamarin.Forms FormsGallery](https://developer.xamarin.com/samples/xamarin-forms/FormsGallery/)
- [Примеры Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Документация по API Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
