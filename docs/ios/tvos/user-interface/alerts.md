---
title: Работа с оповещениями tvOS в Xamarin
description: В этом документе описывается работа с оповещениями tvOS в Xamarin. В нем описывается, отображая оповещение, добавляя текстовые поля и вспомогательный класс.
ms.prod: xamarin
ms.assetid: F969BB28-FF2C-4A7D-88CA-F8076AD48538
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 13c5ae3fac76ec1ec1a0ade135d5919403066226
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111295"
---
# <a name="working-with-tvos-alerts-in-xamarin"></a>Работа с оповещениями tvOS в Xamarin

_В этой статье рассматривается работа с UIAlertController для отображения предупреждающее сообщение для пользователя в Xamarin.tvOS._

Если требуется привлекать внимание пользователя, tvOS или задать разрешения на выполнение разрушительные действия (например, при удалении файла), могут быть представлены в предупреждающее сообщение с помощью `UIAlertViewController`:

[![](alerts-images/alert01.png "Пример UIAlertViewController")](alerts-images/alert01.png#lightbox)

Если дополнением к отображение сообщения, можно добавить кнопки и текстовые поля на оповещение, чтобы разрешить пользователю реагировать на действия и оставить отзыв.

<a name="About-Alerts" />

## <a name="about-alerts"></a>Об оповещениях

Как уже говорилось, оповещения позволяют привлечет внимание пользователя и сообщить о состоянии приложения или запроса на отзыв. Оповещения должен представить заголовок, они могут при необходимости иметь сообщение и один или несколько кнопок и текстовых полей.

[![](alerts-images/alert04.png "Пример предупреждения")](alerts-images/alert04.png#lightbox)

Apple имеет следующие рекомендации по работе с оповещениями.

- **Использовать оповещения только в случае необходимости** -оповещения нарушать поток пользователя с приложением и прерываний взаимодействие с пользователем и таким образом, следует использовать только для важных ситуаций, таких как уведомления об ошибках, покупки из приложений и разрушительные действия. 
- **Предоставляет полезных** — Если оповещение представляет параметры для пользователя, ваш следует убедиться, что каждый параметр содержит важные сведения и полезные действия для от пользователя выполнить.

<a name="Alert-Titles-and-Messages" />

### <a name="alert-titles-and-messages"></a>Предупреждения об изменении названия и сообщений

Apple имеет следующие рекомендации для представления название и необязательное сообщение предупреждения:

- **Используйте названия Multiword** -в заголовок оповещения следует получить точку ситуации через четко оставаясь простой. Название одного слова редко дает достаточно информации.
- **Используйте описательное названия, не требующих сообщение** — везде, где это возможно, попробуйте сделать оповещения заголовок описательный достаточно, необязательный текст сообщения не требуется. 
- **Преобразовать сообщение короткое, полное предложение** — Если необязательное сообщение необходим, чтобы получить оповещения через точку, сохранить как можно более простым и сделать его полное предложение с правильным регистром и знаков препинания.

<a name="Alert-Buttons" />

### <a name="alert-buttons"></a>Кнопки оповещений

Apple состоит из следующих предложений для добавления кнопок на оповещение.

- **Ограничение на две кнопки** — по возможности ограничить предупреждения, до двух кнопок. Одной кнопки оповещения содержат сведения, но никакие действия. Две кнопки оповещения содержат простой Да/Нет выбора действия.
- **Используйте Succinct, логических названия кнопки** -Simple, лучше всего работают один или два слова кнопку заголовки, которые четко описывать действие кнопки. Дополнительные сведения см. наш [работа с кнопками](~/ios/tvos/user-interface/buttons.md) документации.
- **Четко разрушающее кнопки Марк** — для кнопок, которые выполнять разрушительные действия (например, при удалении файла) место, пометив их с помощью `UIAlertActionStyle.Destructive` стиля.

<a name="Displaying-an-Alert" />

## <a name="displaying-an-alert"></a>Отображение оповещения

Чтобы отобразить оповещения, создайте экземпляр `UIAlertViewController` и настройте его, добавляя действия (кнопки) и выбрав стиль оповещения. Например следующий код отображает предупреждение ОК, Отмена:

```csharp
const string title = "A Short Title is Best";
const string message = "A message should be a short, complete sentence.";
const string acceptButtonTitle = "OK";
const string cancelButtonTitle = "Cancel";
const string deleteButtonTitle = "Delete";
...
        
var alertController = UIAlertController.Create (title, message, UIAlertControllerStyle.Alert);

// Create the action.
var acceptAction = UIAlertAction.Create (acceptButtonTitle, UIAlertActionStyle.Default, _ => 
    Console.WriteLine ("The \"OK/Cancel\" alert's other action occurred.")
);

var cancelAction = UIAlertAction.Create (cancelButtonTitle, UIAlertActionStyle.Cancel, _ => 
    Console.WriteLine ("The \"OK/Cancel\" alert's other action occurred.")
);

// Add the actions.
alertController.AddAction (acceptAction);
alertController.AddAction (cancelAction);
PresentViewController (alertController, true, null);
```

Давайте подробно рассмотрим этот код. Во-первых мы создадим новое предупреждение с указанным заголовком и сообщением:

```csharp
UIAlertController.Create (title, message, UIAlertControllerStyle.Alert)
```

Затем для каждой кнопки, мы должны отображаться в диалоговом окне оповещения Создайте определение название кнопки, его стиль и действие, которое необходимо выполнить при нажатии кнопки действие:

```csharp
UIAlertAction.Create ("Button Title", UIAlertActionStyle.Default, _ => 
    // Do something when the button is pressed
    ...
);
```

`UIAlertActionStyle` Перечисления позволяют задать стиль кнопки как одно из следующих:

- **По умолчанию** -кнопка будет кнопка по умолчанию, выбранные при отображении предупреждения.
- **Отмена** -кнопка является кнопкой "Отмена" для оповещения.
- **Разрушительные** -выделение кнопок как разрушительные действия, например при удалении файла. В настоящее время tvOS уничтожения данных отображается кнопка раскрытия с красным фоном.

`AddAction` Метод добавляет данное действие для `UIAlertViewController` и, наконец `PresentViewController (alertController, true, null)` метод отображает данного предупреждение для пользователя.

<a name="Adding-Text-Fields" />

## <a name="adding-text-fields"></a>Добавление текстовых полей

Помимо добавления действия (кнопки) оповещения, можно добавить текстовые поля на оповещение, чтобы разрешить пользователю заполнить сведения, такие как идентификаторы пользователей и пароли:

[![](alerts-images/alert02.png "Текстовое поле в предупреждении")](alerts-images/alert02.png#lightbox)

Если пользователь выбирает поле, стандартная tvOS клавиатуры будет отображаться, позволяющий вводить значение для поля:

[![](alerts-images/alert03.png "Ввод текста")](alerts-images/alert03.png#lightbox)

Следующий код отображает предупреждения ОК, Отмена с помощью одного текстового поля для ввода значения:

```csharp
UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);
UITextField field = null;

// Add and configure text field
alert.AddTextField ((textField) => {
    // Save the field
    field = textField;

    // Initialize field
    field.Placeholder = placeholder;
    field.Text = text;
    field.AutocorrectionType = UITextAutocorrectionType.No;
    field.KeyboardType = UIKeyboardType.Default;
    field.ReturnKeyType = UIReturnKeyType.Done;
    field.ClearButtonMode = UITextFieldViewMode.WhileEditing;

});

// Add cancel button
alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
    // User canceled, do something
    ...
}));

// Add ok button
alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(actionOK) => {
    // User selected ok, do something
    ...
}));

// Display the alert
controller.PresentViewController(alert,true,null);
```

`AddTextField` Метод добавляет новое текстовое поле оповещение, которое затем можно настроить, задав свойства, такие как заполнитель (текст, отображаемый, когда поле пусто), по умолчанию текстовое значение и тип клавиатуры. Пример:

```csharp
// Initialize field
field.Placeholder = placeholder;
field.Text = text;
field.AutocorrectionType = UITextAutocorrectionType.No;
field.KeyboardType = UIKeyboardType.Default;
field.ReturnKeyType = UIReturnKeyType.Done;
field.ClearButtonMode = UITextFieldViewMode.WhileEditing;
```

Таким образом, мы может действовать от значения текстового поля в более поздней версии, мы также сохранить копию, используя следующий код:

```csharp
UITextField field = null;
...

// Add and configure text field
alert.AddTextField ((textField) => {
    // Save the field
    field = textField;
    ...
});
```

Когда пользователь укажет значение в текстовом поле, мы используем `field` переменной для доступа к это значение.

<a name="Alert-View-Controller-Helper-Class" />

## <a name="alert-view-controller-helper-class"></a>Вспомогательный класс контроллера представления "предупреждения"

Так как отображение простой распространенных типов оповещений с помощью `UIAlertViewController` может привести к совсем немного повторяющегося кода, можно использовать вспомогательный класс для уменьшения объема повторяющихся частей кода. Пример:

```csharp
using System;
using Foundation;
using UIKit;
using System.CodeDom.Compiler;

namespace UIKit
{
    /// <summary>
    /// Alert view controller is a reusable helper class that makes working with <c>UIAlertViewController</c> alerts
    /// easier in a tvOS app.
    /// </summary>
    public class AlertViewController
    {
        #region Static Methods
        public static UIAlertController PresentOKAlert(string title, string description, UIViewController controller) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);

            // Configure the alert
            alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(action) => {}));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }

        public static UIAlertController PresentOKCancelAlert(string title, string description, UIViewController controller, AlertOKCancelDelegate action) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);

            // Add cancel button
            alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
                // Any action?
                if (action!=null) {
                    action(false);
                }
            }));

            // Add ok button
            alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(actionOK) => {
                // Any action?
                if (action!=null) {
                    action(true);
                }
            }));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }

        public static UIAlertController PresentDestructiveAlert(string title, string description, string destructiveAction, UIViewController controller, AlertOKCancelDelegate action) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);

            // Add cancel button
            alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
                // Any action?
                if (action!=null) {
                    action(false);
                }
            }));

            // Add ok button
            alert.AddAction(UIAlertAction.Create(destructiveAction,UIAlertActionStyle.Destructive,(actionOK) => {
                // Any action?
                if (action!=null) {
                    action(true);
                }
            }));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }

        public static UIAlertController PresentTextInputAlert(string title, string description, string placeholder, string text, UIViewController controller, AlertTextInputDelegate action) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);
            UITextField field = null;

            // Add and configure text field
            alert.AddTextField ((textField) => {
                // Save the field
                field = textField;

                // Initialize field
                field.Placeholder = placeholder;
                field.Text = text;
                field.AutocorrectionType = UITextAutocorrectionType.No;
                field.KeyboardType = UIKeyboardType.Default;
                field.ReturnKeyType = UIReturnKeyType.Done;
                field.ClearButtonMode = UITextFieldViewMode.WhileEditing;

            });

            // Add cancel button
            alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
                // Any action?
                if (action!=null) {
                    action(false,"");
                }
            }));

            // Add ok button
            alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(actionOK) => {
                // Any action?
                if (action!=null && field !=null) {
                    action(true, field.Text);
                }
            }));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }
        #endregion

        #region Delegates
        public delegate void AlertOKCancelDelegate(bool OK);
        public delegate void AlertTextInputDelegate(bool OK, string text);
        #endregion
    }
}
```

С помощью этого класса, отображения и простой оповещениями можно сделать следующим образом:

```csharp
#region Custom Actions
partial void DisplayDestructiveAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentDestructiveAlert("A Short Title is Best","The message should be a short, complete sentence.","Delete",this, (ok) => {
        Console.WriteLine("Destructive Alert: The user selected {0}",ok);
    });
}

partial void DisplayOkCancelAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentOKCancelAlert("A Short Title is Best","The message should be a short, complete sentence.",this, (ok) => {
        Console.WriteLine("OK/Cancel Alert: The user selected {0}",ok);
    });
}

partial void DisplaySimpleAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentOKAlert("A Short Title is Best","The message should be a short, complete sentence.",this);
}

partial void DisplayTextInputAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentTextInputAlert("A Short Title is Best","The message should be a short, complete sentence.","placeholder", "", this, (ok, text) => {
        Console.WriteLine("Text Input Alert: The user selected {0} and entered `{1}`",ok,text);
    });
}
#endregion
```


<a name="Summary" />

## <a name="summary"></a>Сводка

В этой статье подробно рассматривается работа с `UIAlertController` для отображения предупреждающее сообщение для пользователя в Xamarin.tvOS. Во-первых он показал, как на экран уведомление о простых и добавление кнопок. Далее рассмотрено добавление текстовых полей на оповещение. Наконец показал, как использовать вспомогательный класс для уменьшения объема повторяющегося кода, необходимый для отображения предупреждения.



## <a name="related-links"></a>Связанные ссылки

- [Примеры tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Человека направляющие интерфейса tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Приложение руководство по программированию для tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
