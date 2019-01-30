---
title: Настройка вида таблицы в Xamarin.iOS
description: В этом документе описывается настройка вида таблицы в Xamarin.iOS. В нем описывается стили ячеек, "Стандартные", разделители ячейки и ячейки пользовательские макеты.
ms.prod: xamarin
ms.assetid: 8A83DE38-0028-CB61-66F9-0FB9DE552286
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: e1e86918d29e12d2f34dd3008b8c1d8e47471c24
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233556"
---
# <a name="customizing-a-tables-appearance-in-xamarinios"></a>Настройка вида таблицы в Xamarin.iOS

Чтобы изменить внешний вид таблицы проще всего использовать стиль другую ячейку. Можно изменить стиль ячейки, который используется при создании каждой ячейки в `UITableViewSource` `GetCell` метод.

## <a name="cell-styles"></a>Стили ячеек

Существует четыре встроенных стилей:

-  **По умолчанию** — поддерживает `UIImageView`.
-  **Подзаголовок** — поддерживает `UIImageView` и подзаголовок.
-  **Значение1** — справа выровненные подзаголовок поддерживает `UIImageView`.
-  **Value2** — заголовок по правому краю и подзаголовок по левому краю (но не изображения).


Этих снимках экрана показано, как выглядит каждого стиля:

 [![](customizing-table-appearance-images/image7.png "Этих снимках экрана показано, как выглядит каждого стиля")](customizing-table-appearance-images/image7.png#lightbox)

Образец **CellDefaultTable** содержит код для создания этих экранов. Стиль ячейки, задается в `UITableViewCell` конструктор, следующим образом:

```csharp
cell = new UITableViewCell (UITableViewCellStyle.Default, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Subtitle, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Value1, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Value2, cellIdentifier);
```

[Поддерживаемые свойства](xref:UIKit.UITableViewCell) ячейки, которую затем можно задать стиль:

```csharp
cell.TextLabel.Text = tableItems[indexPath.Row].Heading;
cell.DetailTextLabel.Text = tableItems[indexPath.Row].SubHeading;
cell.ImageView.Image = UIImage.FromFile("Images/" + tableItems[indexPath.Row].ImageName); // don't use for Value2
```

## <a name="accessories"></a>Стандартные

Ячейки может иметь следующие стандартные, добавлен справа от представления:

-   **Флажок** — может использоваться для указания выбором в таблице.
-   **DetailButton** — реагирует на касание независимо от остальной части ячейки, позволяя ресурсам выполнить другую функцию, которая касается ячейки сам (например, открытие контекстного меню или новое окно, которое не является частью `UINavigationController` стека).
-   **DisclosureIndicator** — обычно используется, чтобы указать, что касается ячейки будет открыть другое представление.
-   **DetailDisclosureButton** — сочетание `DetailButton` и `DisclosureIndicator`.


Это, как они выглядят:

 [![](customizing-table-appearance-images/image8.png "Пример \"Стандартные\"")](customizing-table-appearance-images/image8.png#lightbox)

Для отображения одного из этих компонентов, можно задать `Accessory` свойство в `GetCell` метод:

```csharp
cell.Accessory = UITableViewCellAccessory.Checkmark;
//cell.Accessory = UITableViewCellAccessory.DisclosureIndicator;
//cell.Accessory = UITableViewCellAccessory.DetailDisclosureButton; // implement AccessoryButtonTapped
//cell.Accessory = UITableViewCellAccessory.None; // to clear the accessory
```

Когда `DetailButton` или `DetailDisclosureButton` отображаются, необходимо также переопределить `AccessoryButtonTapped` для выполнения некоторых операций, когда он был затронут.

```csharp
public override void AccessoryButtonTapped (UITableView tableView, NSIndexPath indexPath)
{
    UIAlertController okAlertController = UIAlertController.Create ("DetailDisclosureButton Touched", tableItems[indexPath.Row].Heading, UIAlertControllerStyle.Alert);
    okAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
    owner.PresentViewController (okAlertController, true, null);

    tableView.DeselectRow (indexPath, true);
}
```

Образец **CellAccessoryTable** показан пример использования "Стандартные".

## <a name="cell-separators"></a>Разделители ячейки

Ячейка разделителями являются ячеек таблицы, используемый для разделения таблицы. В таблице свойств.

```csharp
TableView.SeparatorColor = UIColor.Blue;
TableView.SeparatorStyle = UITableViewCellSeparatorStyle.DoubleLineEtched;
```

Можно также добавить эффект размытия или естественность для разделителя:

```csharp
// blur effect
TableView.SeparatorEffect =
    UIBlurEffect.FromStyle(UIBlurEffectStyle.Dark);

//vibrancy effect
var effect = UIBlurEffect.FromStyle(UIBlurEffectStyle.Light);
TableView.SeparatorEffect = UIVibrancyEffect.FromBlurEffect(effect);
```

Разделитель может также иметь отступ:

```csharp
TableView.SeparatorInset.InsetRect(new CGRect(4, 4, 150, 2));
```

## <a name="creating-custom-cell-layouts"></a>Создание пользовательской ячейки макетов

Чтобы изменить визуальный стиль таблицы, необходимо предоставить пользовательские ячейки для отображения. Пользовательской ячейки могут иметь различные цвета и управления разметки.

В примере CellCustomTable реализуется `UITableViewCell` подкласс, который определяет пользовательский макет элемента `UILabel`s и `UIImage` различные шрифты и цвета. Итоговый ячейки будет выглядеть следующим образом:

 [![](customizing-table-appearance-images/image9.png "Макеты пользовательских ячеек")](customizing-table-appearance-images/image9.png#lightbox)

Пользовательский класс ячейки с состоит из только три метода:

-   **Конструктор** — создает элементы управления пользовательского интерфейса и задает свойства пользовательский стиль (например) начертание шрифта, размера и цвета).
-   **UpdateCell** — метод, который `UITableView.GetCell` использовать для задания свойств в ячейке.
-   **LayoutSubviews** — задать расположение элементов управления пользовательского интерфейса. В примере каждая ячейка имеет тот же макет, но более сложные ячейки (особенно с различными размерами) может потребоваться позиций другой макет в зависимости от содержимого, отображаемого.


Полный образец кода в **CellCustomTable > CustomVegeCell.cs** ниже:

```csharp
public class CustomVegeCell : UITableViewCell  {
    UILabel headingLabel, subheadingLabel;
    UIImageView imageView;
    public CustomVegeCell (NSString cellId) : base (UITableViewCellStyle.Default, cellId)
    {
        SelectionStyle = UITableViewCellSelectionStyle.Gray;
        ContentView.BackgroundColor = UIColor.FromRGB (218, 255, 127);
        imageView = new UIImageView();
        headingLabel = new UILabel () {
            Font = UIFont.FromName("Cochin-BoldItalic", 22f),
            TextColor = UIColor.FromRGB (127, 51, 0),
            BackgroundColor = UIColor.Clear
        };
        subheadingLabel = new UILabel () {
            Font = UIFont.FromName("AmericanTypewriter", 12f),
            TextColor = UIColor.FromRGB (38, 127, 0),
            TextAlignment = UITextAlignment.Center,
            BackgroundColor = UIColor.Clear
        };
        ContentView.AddSubviews(new UIView[] {headingLabel, subheadingLabel, imageView});

    }
    public void UpdateCell (string caption, string subtitle, UIImage image)
    {
        imageView.Image = image;
        headingLabel.Text = caption;
        subheadingLabel.Text = subtitle;
    }
    public override void LayoutSubviews ()
    {
        base.LayoutSubviews ();
        imageView.Frame = new CGRect (ContentView.Bounds.Width - 63, 5, 33, 33);
        headingLabel.Frame = new CGRect (5, 4, ContentView.Bounds.Width - 63, 25);
        subheadingLabel.Frame = new CGRect (100, 18, 100, 20);
    }
}
```

`GetCell` Метод `UITableViewSource` должно быть изменено для создания пользовательской ячейки:

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (cellIdentifier) as CustomVegeCell;
    if (cell == null)
        cell = new CustomVegeCell (cellIdentifier);
    cell.UpdateCell (tableItems[indexPath.Row].Heading
            , tableItems[indexPath.Row].SubHeading
            , UIImage.FromFile ("Images/" + tableItems[indexPath.Row].ImageName) );
    return cell;
}
```



## <a name="related-links"></a>Связанные ссылки

- [WorkingWithTables (пример)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
