---
title: Отображение оповещений в Xamarin.iOS
description: В этом документе описывается, как на отображение оповещений в Xamarin.iOS с помощью UIAlertController API, появившихся в iOS 8.
ms.prod: xamarin
ms.assetid: 61C671E9-3757-4052-86E4-28640025A34A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 788e62b30dbf533df059b0c3805e04ecf7b857aa
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39241344"
---
# <a name="displaying-alerts-in-xamarinios"></a>Отображение оповещений в Xamarin.iOS

Начиная с iOS 8, UIAlertController имеет завершенной замененного UIActionSheet и UIAlertView оба из которых уже устарели.

В отличие от классов, замененные, которые являются подклассами класса UIView, UIAlertController является подклассом UIViewController.

Используйте `UIAlertControllerStyle` для указания типа оповещений для отображения. Ниже приведены эти типы оповещений.

- **UIAlertControllerStyleActionSheet**
    * До iOS 8 это было бы UIActionSheet
- **UIAlertControllerStyleAlert**
    * До iOS 8 это было бы UIAlertView 

Существует три необходимые действия, выполняемые при создании контроллер оповещения:

- Создайте и настройте оповещение со следующими характеристиками:
    * заголовок
    * сообщение
    * preferredStyle
    
- (Необязательно) Добавление текстового поля
- Добавьте необходимые действия
- Представления контроллера представления

Самый простой такое оповещение содержит одну кнопку, как показано на следующем снимке экрана:

 ![Оповещение с одной кнопки](alerts-images/alert1.png)

В коде для отображения простое предупреждение выглядит следующим образом:

```csharp
okayButton.TouchUpInside += (sender, e) => {

    //Create Alert
    var okAlertController = UIAlertController.Create ("Title", "The message", UIAlertControllerStyle.Alert);

    //Add Action
    okAlertController.AddAction (UIAlertAction.Create ("OK", UIAlertActionStyle.Default, null));

    // Present Alert
    PresentViewController (okAlertController, true, null);
};
```

Отображение оповещения с несколькими действиями, выполняется таким же образом, но добавить два действия. Например на следующем рисунке показан оповещение с двумя кнопками:

 ![ Оповещение с двумя кнопками](alerts-images/alert2.png)

```csharp
okayCancelButton.TouchUpInside += ((sender, e) => {

    //Create Alert
    var okCancelAlertController = UIAlertController.Create("Alert Title", "Choose from two buttons", UIAlertControllerStyle.Alert);

    //Add Actions
    okCancelAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, alert => Console.WriteLine ("Okay was clicked")));
    okCancelAlertController.AddAction(UIAlertAction.Create("Cancel", UIAlertActionStyle.Cancel, alert => Console.WriteLine ("Cancel was clicked")));

    //Present Alert
    PresentViewController(okCancelAlertController, true, null);
});
```

Оповещения можно также отобразить лист действия, как на снимке экрана ниже:

 ![Предупреждение таблицы стилей](alerts-images/alert3.png)

Оповещение можно с помощью добавления кнопок `AddAction` метод:

```csharp
actionSheetButton.TouchUpInside += ((sender, e) => {

    // Create a new Alert Controller
    UIAlertController actionSheetAlert = UIAlertController.Create("Action Sheet", "Select an item from below", UIAlertControllerStyle.ActionSheet);

    // Add Actions
    actionSheetAlert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default, (action) => Console.WriteLine ("Item One pressed.")));

    actionSheetAlert.AddAction(UIAlertAction.Create("custom button 1",UIAlertActionStyle.Default, (action) => Console.WriteLine ("Item Two pressed.")));

    actionSheetAlert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Default, (action) => Console.WriteLine ("Item Three pressed.")));

    actionSheetAlert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel, (action) => Console.WriteLine ("Cancel button pressed.")));

    // Required for iPad - You must specify a source for the Action Sheet since it is
    // displayed as a popover
    UIPopoverPresentationController presentationPopover = actionSheetAlert.PopoverPresentationController;
    if (presentationPopover!=null) {
        presentationPopover.SourceView = this.View;
        presentationPopover.PermittedArrowDirections = UIPopoverArrowDirection.Up;
    }

    // Display the alert
    this.PresentViewController(actionSheetAlert,true,null);
});
```

## <a name="related-links"></a>Связанные ссылки

- [Элементы управления (пример)](https://developer.xamarin.com/samples/Controls/)
- [Контроллер оповещения](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller)
