---
title: Оповещения в Xamarin.Mac
description: В этой статье рассматривается работа с оповещениями в приложении Xamarin.Mac. Он описывает создание и отображение оповещений с C# кода и отвечать на действия пользователя.
ms.prod: xamarin
ms.assetid: F1DB93A1-7549-4540-AD5E-D7605CCD8435
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 8f84b688998251db52c8c2be71949e1a2e665dc0
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103963"
---
# <a name="alerts-in-xamarinmac"></a>Оповещения в Xamarin.Mac

_В этой статье рассматривается работа с оповещениями в приложении Xamarin.Mac. Он описывает создание и отображение оповещений с C# кода и отвечать на действия пользователя._

При работе с C# и .NET в приложении Xamarin.Mac, у вас есть доступ к же предупреждений, которые разработчик, работающий с *Objective-C* и *Xcode* does. 

Предупреждение — это особый тип диалогового окна, которое появляется при возникновении серьезной проблемы (например, ошибка) или как предупреждение (например, Подготовка к удалению файла). Поскольку диалоговое окно предупреждения, также требуется ответ от пользователя перед закрытием.

[![](alert-images/alert06.png "Пример предупреждения")](alert-images/alert06.png#lightbox)

В этой статье мы обсудим, что узнаете основы работы с оповещениями в приложении Xamarin.Mac. 

<a name="Introduction_to_Alerts" />

## <a name="introduction-to-alerts"></a>Введение в оповещения

Предупреждение — это особый тип диалогового окна, которое появляется при возникновении серьезной проблемы (например, ошибка) или как предупреждение (например, Подготовка к удалению файла). Поскольку оповещения мешать работе пользователя, так как они должны быть отброшено, прежде чем пользователь может продолжить свою задачу, исключить, отображая оповещение в том случае, если это абсолютно необходимо.

Apple предлагает следующие рекомендации:

- Не используйте оповещения только для предоставления пользователям информации.
- Не отображать оповещение для общего, отмену действия. Даже если такая ситуация может привести к потере данных.
- Если ситуация является достойным оповещение, избегайте с помощью любой элемент пользовательского интерфейса или метода, чтобы отобразить ее.
- Значок предупреждения следует использовать с осторожностью.
- Описывают ситуации ясно и четко предупредительное сообщение.
- Действие, которое вы описываете появлению оповещения должно соответствовать имени кнопки по умолчанию.

Дополнительные сведения см. в разделе [оповещения](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAlerts.html#//apple_ref/doc/uid/20000957-CH44-SW1) раздел Apple [рекомендации по человеческим интерфейсом OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Anatomy_of_an_Alert" />

## <a name="anatomy-of-an-alert"></a>Анатомия оповещение

Как уже говорилось, оповещения должно отображаться для пользователя приложения при возникновении серьезной проблемы или как предупреждение к потере данных (например, закрыть записи несохраненный файл). В Xamarin.Mac, создается оповещение в C# кода, например:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Critical,
    InformativeText = "We need to save the document here...",
    MessageText = "Save Document",
};
alert.RunModal ();
```

Приведенный выше код отображает предупреждение с значок приложения, наложенные на значок предупреждения "," Заголовок "," Предупреждение "и" один **ОК** кнопки:

[![](alert-images/alert01.png "Оповещение с \"ОК\"")](alert-images/alert01.png#lightbox)

Apple предоставляет несколько свойств, которые можно использовать для настройки оповещения:

- **AlertStyle** определяет тип оповещение как одно из следующих:
    - **Предупреждение** — используется, чтобы предупредить пользователя текущего или приближающийся событие, которое не является критически важным. Это стиль по умолчанию.
    - **Информационное** — используется, чтобы предупредить пользователя о текущей или предстоящих событий. В настоящее время нет никаких видимых различий между **предупреждение** и **информационное сообщение**
    - **Критические** — используется, чтобы предупредить пользователя о серьезных последствий приближающийся события (например, при удалении файла). Оповещение этого типа следует использовать с осторожностью.
- **MessageText** -это основного сообщения или заголовок оповещения и должен быстро определить ситуации для пользователя.
- **InformativeText** — это текст предупреждения, где следует четко определить ситуации и функциональны помогают пользователю.
- **Значок** -разрешает пользовательский значок, отображаемый для пользователя.
- **HelpAnchor** & **ShowsHelp** -позволяет предупреждение, чтобы быть интегрированы в приложение HelpBook и вывести справку для оповещения.
- **Кнопки** -по умолчанию, предупреждение имеет только **ОК** кнопки, но **кнопки** коллекции можно добавить дополнительные варианты, при необходимости.
- **ShowsSuppressionButton** — Если `true` отображает элемент управления checkbox, пользователь может использовать для подавления данного предупреждения, последующие вхождения, какое событие вызвало его.
- **AccessoryView** -позволяет присоединить другой вложенное представление с оповещением для предоставления дополнительных сведений, таких как добавление **текстовое поле** для ввода данных. Если задать новый **AccessoryView** или изменить существующий, необходимо вызвать `Layout()` метод для настройки макета видимым оповещения.

<a name="Displaying_an_Alert" />

## <a name="displaying-an-alert"></a>Отображение оповещения

Существует два разных способа, что оповещение может быть отображается, свободные или электронную таблицу. Следующий код отображает оповещение как свободные:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.RunModal ();
```
Если этот код выполняется, отображается следующая информация:

[![](alert-images/alert02.png "Простое предупреждение")](alert-images/alert02.png#lightbox)

Следующий код отображает то же оповещение в виде листа:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.BeginSheet (this);
```

Если этот код выполняется, ниже будет отображаться:

[![](alert-images/alert03.png "Оповещение в милливаттах в лист")](alert-images/alert03.png#lightbox)


<a name="Working_with_Alert_Buttons" />

## <a name="working-with-alert-buttons"></a>Работа с кнопками, предупреждения

По умолчанию отображается оповещение только **ОК** кнопки. Тем не менее, вы не ограничены, можно создавать дополнительные кнопки путем добавления их к **кнопки** коллекции. Следующий код создает свободные предупреждение с **ОК**, **отменить** и **возможно** кнопки:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
var result = alert.RunModal ();
```

Самый первый добавлена кнопка будет _кнопку по умолчанию_ , будут активированы, если пользователь нажмет клавишу ВВОД. Возвращаемое значение будет целое число, представляющее, какую кнопку нажал пользователь. В нашем случае возвращаются следующие значения:

- **ОК** — 1000.
- **Отмена** - 1001.
- **Может быть** - 1002.

Если выполнить код, ниже будет отображаться:

[![](alert-images/alert04.png "Оповещение с тремя параметрами кнопки")](alert-images/alert04.png#lightbox)

Ниже приведен код для то же самое предупреждение, как таблицу:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.BeginSheetForResponse (this, (result) => {
    Console.WriteLine ("Alert Result: {0}", result);
});
```
Если этот код выполняется, ниже будет отображаться:

[![](alert-images/alert05.png "Три кнопки предупреждения, отображаемого как лист")](alert-images/alert05.png#lightbox)

> [!IMPORTANT]
> Никогда не следует добавлять более трех кнопок на оповещение.

<a name="Showing_the_Suppress_Button" />

## <a name="showing-the-suppress-button"></a>Отображение кнопки подавлять

Если предупреждение `ShowSuppressButton` свойство `true`, предупреждение отображается элемент управления checkbox, пользователь может использовать для подавления данного предупреждения, последующие вхождения, какое событие вызвало его. Следующий код отображает свободные оповещение с кнопкой подавлять:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
var result = alert.RunModal ();
Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
```

Если значение `alert.SuppressionButton.State` является `NSCellStateValue.On`, пользователь извлек флажок Suppress, иначе они не имеют.

Если код выполняется, ниже будет отображаться:

[![](alert-images/alert06.png "Оповещение с кнопкой подавлять")](alert-images/alert06.png#lightbox)

Ниже приведен код для то же самое предупреждение, как таблицу:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
alert.BeginSheetForResponse (this, (result) => {
    Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
});
```

Если этот код выполняется, ниже будет отображаться:

[![](alert-images/alert07.png "Отображать оповещение с кнопкой подавлять как лист")](alert-images/alert07.png#lightbox)

<a name="Adding_a_Custom_SubView" />

## <a name="adding-a-custom-subview"></a>Добавление пользовательских вложенное представление

Оповещения имеют `AccessoryView` свойство, которое может использоваться для дальнейшей оповещение, а добавлены такие вещи, как **текстовое поле** для ввода данных пользователем. Следующий код создает свободные оповещение с полем ввода добавлен текст:

```csharp
var input = new NSTextField (new CGRect (0, 0, 300, 20));

var alert = new NSAlert () {
AlertStyle = NSAlertStyle.Informational,
InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
alert.AccessoryView = input;
alert.Layout ();
var result = alert.RunModal ();
Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
```

Строки ключей являются `var input = new NSTextField (new CGRect (0, 0, 300, 20));` которого создает новую **текстовое поле** , мы добавим оповещения. `alert.AccessoryView = input;` какие вкладывает **текстовое поле** оповещения и вызов `Layout()` метод, который необходим для изменения размера предупреждения, помещается в новый вложенное представление.

Если выполнить код, ниже будет отображаться:

[![](alert-images/alert08.png "Если выполнить код, ниже отображается")](alert-images/alert08.png#lightbox)

Вот то же самое предупреждение, как таблицу:

```csharp
var input = new NSTextField (new CGRect (0, 0, 300, 20));

var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
alert.AccessoryView = input;
alert.Layout ();
alert.BeginSheetForResponse (this, (result) => {
    Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
});
```

Если выполнить этот код, ниже будет отображаться:

[![](alert-images/alert09.png "Оповещение с помощью пользовательского представления")](alert-images/alert09.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>Сводка

В этой статье были предприняты никакие подробные принципы работы с оповещениями в приложении Xamarin.Mac. Мы видели различные типы и использует предупреждений, как создать и настроить оповещения и способы работы с оповещениями в C# кода.

## <a name="related-links"></a>Связанные ссылки

- [MacWindows (пример)](https://developer.xamarin.com/samples/mac/MacWindows/)
- [Привет, Mac](~/mac/get-started/hello-mac.md)
- [Работа с Windows](~/mac/user-interface/window.md)
- [Рекомендации по работе с человеческим интерфейсом OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Введение в Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
- [NSAlert](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSAlert_Class/index.html#//apple_ref/doc/uid/TP40004001)
