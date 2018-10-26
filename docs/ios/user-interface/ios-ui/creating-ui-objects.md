---
title: Создание объектов пользовательского интерфейса в Xamarin.iOS
description: Этот документ предоставляет обзор того, как создать пользовательский интерфейс в Xamarin.iOS. В нем описывается конструктор, iOS и конструктора Interface Builder Xcode C#и раскадровки.
ms.prod: xamarin
ms.assetid: 4D6B136C-744A-4936-8655-A77E62BA7A60
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: a4cf63b84d0686bc28b02b18a6266908251bdf6f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121923"
---
# <a name="creating-user-interface-objects-in-xamarinios"></a>Создание объектов пользовательского интерфейса в Xamarin.iOS

Apple группы связанных части функциональных возможностей в «платформы», которые приравниваются к пространствам имен Xamarin.iOS. `UIKit` — Это пространство имен, которое включает все элементы интерфейса пользователя для iOS.

Каждый раз, когда ваш код должен ссылаться на элемент управления интерфейса пользователя, например метки или кнопки, не забудьте включить следующее оператор using:

```csharp
using UIKit;
```

Все элементы управления, описанных в этой главе находятся в пространстве имен UIKit, и каждое имя класса элемента управления пользователя имеет `UI` префикс.

Вы можете изменить элементы управления пользовательского интерфейса и макеты тремя способами:

-  **[Конструктор iOS Xamarin](~/ios/user-interface/designer/index.md)**  — конструктора встроенных использование Xamarin для разработки экранов. Дважды щелкните storyboard "или" файлы XIB для редактирования с помощью встроенных конструктора.
-  **Конструктор Interface Builder в Xcode** — перетаскивайте элементы управления в макеты экрана с помощью конструктора Interface Builder. Откройте раскадровку или xib-файла в Xcode щелкните правой кнопкой мыши файл в **панели решения** и выбрав **открыть с помощью > конструктора Interface Builder Xcode**.
-  **С помощью C#**  — элементы управления можно также программно создан с помощью кода и добавить иерархию представлений.

Новые файлы раскадровки и XIB можно добавить, щелкнув правой кнопкой мыши на проект iOS и выбрав **Добавить > новый файл...** .

Какой бы метод вы используете, свойства элемента управления и события можно по-прежнему управлять C# в логике приложения.

## <a name="using-xamarin-ios-designer"></a>С помощью Xamarin iOS Designer

Чтобы приступить к созданию пользовательского интерфейса в конструкторе iOS, дважды щелкните файл раскадровки. Элементы управления можно перетащить в область конструктора из **элементов** как показано ниже:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

 [![](creating-ui-objects-images/image2b.png "Панель элементов")](creating-ui-objects-images/image2b.png#lightbox)
 
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

 [![](creating-ui-objects-images/image2b-vs.png "Панель элементов - Visual Stuio")](creating-ui-objects-images/image2b.png#lightbox)
 
-----

При выборе элемента управления в рабочей области конструирования **панели свойств** отобразятся атрибуты для этого элемента управления. **Мини-приложения > удостоверение > имя** поле, которое заполняется на следующем снимке экрана, используется в качестве *розетки* имя. Это как можно сослаться на элемент управления в C#:

 [![](creating-ui-objects-images/image3b.png "Панель свойств мини-приложения")](creating-ui-objects-images/image3b.png#lightbox)

Подробнее об использовании конструктора iOS, см. в разделе [введение в конструктор iOS](~/ios/user-interface/designer/introduction.md) руководства.

## <a name="using-xcode-interface-builder"></a>С помощью построителя интерфейса Xcode

Если вы не знакомы с помощью конструктора Interface Builder, см. раздел Apple [Interface Builder](https://developer.apple.com/xcode/interface-builder/) документов.

Чтобы открыть раскадровку в Xcode, щелкните правой кнопкой мыши для доступа к контекстное меню для файла раскадровки и выберите Открыть с помощью **конструктора Interface Builder Xcode**:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

 [![](creating-ui-objects-images/imagexcode.png "Контекстное меню раскадровки - Xcode")](creating-ui-objects-images/imagexcode.png#lightbox)
 
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](creating-ui-objects-images/imagexcode-vs.png "Контекстное меню раскадровки - Xcode")](creating-ui-objects-images/imagexcode-vs.png#lightbox)

-----

Элементы управления можно перетащить в область конструктора из **Object Library** показано ниже:

 [![](creating-ui-objects-images/image5a.png "Библиотека объектов Xcode")](creating-ui-objects-images/image5a.png#lightbox)

При разработке пользовательского интерфейса с помощью конструктора Interface Builder, необходимо создать **розетки** для каждого элемента управления, который необходимо сослаться в C#. Это делается, включив **вспомогательном редакторе** в центре **редактор** кнопки на кнопке панели инструментов Xcode:

 [![](creating-ui-objects-images/image6a.png "Кнопка \"Редактор помощника\"")](creating-ui-objects-images/image6a.png#lightbox)

Щелкните объект интерфейса пользователя; затем **перетащите элемент управления** в h-файл. Чтобы ** управления перетащите **, удерживайте нажатой клавишу затем нажмите и удерживайте на объект интерфейса пользователя, которые вы создаете переменную экземпляра (или действия). Удерживайте нажатой клавишу во время перетаскивания в файле заголовка. Готово, перетащив ниже `@interface` определения. С заголовка вставить розетки или переменной экземпляра коллекции, должна появиться синяя линия, как показано на следующем снимке экрана.

При освобождении нажмите кнопку, вам будет предложено указать имя переменной экземпляра, который будет использоваться для создания C# свойство, которое можно ссылаться в коде:

 [![](creating-ui-objects-images/image8a.png "Создание переменной экземпляра")](creating-ui-objects-images/image8a.png#lightbox)

Дополнительные сведения о том, как конструктора Interface Builder интегрируется с Visual Studio для Mac, см. [создание кода для Xib](~/ios/internals/xib-code-generation.md#generated) документа.

##  <a name="using-c"></a>С помощьюC#

Если принято решение для программного создания объекта интерфейса пользователя с помощью C# (в представлении или контроллер представления, например), выполните следующие действия:

-  Объявите поле уровня класса для объекта пользовательского интерфейса. Создание самого элемента управления, в `ViewDidLoad` для примера. Объект можно ссылаться на протяжении всего жизненного цикла методы контроллера представления (например)
`ViewWillAppear`).
-  Создание `CGRect` , определяющий фрейм элемента управления (его координаты X и Y на экране, а также его ширина и высота). Необходимо убедиться, что у вас есть `using CoreGraphics` директив для этого.
-  Вызовите конструктор для создания и назначения элемента управления.
-  Задайте свойства или обработчики событий.
-  Вызовите `Add()` для добавления элемента управления в иерархию представлений.

Ниже приведен простой пример создания `UILabel` в контроллер представления с помощью C#:

```csharp
UILabel label1;
public override void ViewDidLoad () {
    base.ViewDidLoad ();
    var frame = new CGRect(10, 10, 300, 30);
    label1 = new UILabel(frame);
    label1.Text = "New Label";
    View.Add (label1);
}
```

<a name="partial_classes" />

## <a name="using-c-and-storyboards"></a>С помощью C# и раскадровки

При добавлении контроллеров представлений в рабочую область конструирования, два соответствующих C# файлы создаются в проекте. В этом примере `ControlsViewController.cs` и `ControlsViewController.designer.cs` будут созданы автоматически:

 [![](creating-ui-objects-images/image9b.png "Разделяемый класс ViewController")](creating-ui-objects-images/image9b.png#lightbox)

`MainViewController.cs` Файл предназначен для *кода*. Именно здесь `View` методы жизненного цикла, такие как `ViewDidLoad` и `ViewWillAppear` реализуются и где можно добавить собственные свойства, поля и методы.

`ControlsViewController.designer.cs` — Созданный код, содержащий разделяемый класс. Когда имя элемента управления на проектирование surface в Visual Studio для Mac, или создайте розетки или действие в Xcode, соответствующего свойства или разделяемого метода, добавляется файл конструктора (designer.cs). В приведенном ниже коде показан пример кода, который создается две кнопки и текстового представления, где одну из кнопок также имеет `TouchUpInside` событий.

Эти элементы разделяемого класса, включите код, чтобы ссылаться на элементы управления и реагирования на действия, которые объявлены в области конструктора:

```csharp
[Register ("ControlsViewController")]
    partial class ControlsViewController
    {
        [Outlet]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        UIKit.UIButton Button1 { get; set; }

        [Outlet]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        UIKit.UIButton Button2 { get; set; }

        [Outlet]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        UIKit.UITextField textfield1 { get; set; }

        [Action ("button2_TouchUpInside:")]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        partial void button2_TouchUpInside (UIButton sender);

        void ReleaseDesignerOutlets ()
        {
            if (Button1 != null) {
                Button1.Dispose ();
                Button1 = null;
            }
            if (Button2 != null) {
                Button2.Dispose ();
                Button2 = null;
            }
            if (textfield1 != null) {
                textfield1.Dispose ();
                textfield1 = null;
            }
        }
    }
}
```

`designer.cs` Файл не следует изменять вручную — для его синхронизация с помощью раскадровки отвечает интегрированной среды разработки (Visual Studio для Mac или Visual Studio).

При добавлении объектов пользовательского интерфейса программными средствами `View` или `ViewController`, создать экземпляр и самостоятельно управлять ссылок на объекты, и таким образом файл конструктора не является обязательным.



## <a name="related-links"></a>Связанные ссылки

- [Элементы управления (пример)](https://developer.xamarin.com/samples/Controls/)
