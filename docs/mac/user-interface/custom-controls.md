---
title: Создание пользовательских элементов управления в Xamarin.Mac
description: В этом документе описывается создание пользовательских элементов управления в Xamarin.Mac. Показано, как построить пользовательский элемент управления, отслеживания состояния, его интерфейс, реагирует на действия пользователя и использовать элемент управления в приложении.
ms.prod: xamarin
ms.assetid: 004534B1-5AEE-452C-BBBE-8C2673FD49B7
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 7fde60d48c23bc48ce1602a0643a3af8ad492ec6
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50104015"
---
# <a name="creating-custom-controls-in-xamarinmac"></a>Создание пользовательских элементов управления в Xamarin.Mac

При работе с C# и .NET в приложении Xamarin.Mac, у вас есть доступ к тому же пользовательские элементы управления, работающему *Objective-C*, *Swift* и *Xcode* does . Поскольку Xamarin.Mac напрямую интегрируется с Xcode, можно использовать конструктор _Interface Builder_ для создания и поддержания свои пользовательские элементы управления (или при необходимости создать их непосредственно в коде C#).

MacOS предоставляет широкий набор встроенных пользовательских элементов управления, может возникнуть, необходимо создать пользовательский элемент управления для обеспечения функциональности не предоставлен out of box или в соответствии с пользовательскую тему пользовательского интерфейса (например, интерфейс игры).

[![](custom-controls-images/intro01.png "Пример пользовательского элемента управления пользовательского интерфейса")](custom-controls-images/intro01.png#lightbox)

В этой статье мы обсудим основные принципы создания многократно используемых пользовательских пользовательского интерфейса элемента управления в приложении Xamarin.Mac. Настоятельно рекомендуется работать через [Привет, Mac](~/mac/get-started/hello-mac.md) статьи, во-первых, в частности [введение в Xcode и конструкторе Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) и [переменных экземпляров и действий](~/mac/get-started/hello-mac.md#outlets-and-actions) разделы, так как он рассматриваются основные понятия и методы, которые мы будем использовать в этой статье.

Может потребоваться взгляните на [предоставление C# классы / методы Objective-c](~/mac/internals/how-it-works.md) раздел [структуре Xamarin.Mac](~/mac/internals/how-it-works.md) документов, здесь объясняется `Register` и `Export` команды используется для передачи классов C# в службе Objective-C объекты и элементы пользовательского интерфейса.

<a name="Introduction-to-Outline-Views" />

## <a name="introduction-to-custom-controls"></a>Общие сведения о пользовательских элементов управления

Как уже говорилось выше, может возникнуть при необходимости создайте повторно используемый пользовательского элемента управления пользовательского интерфейса предоставляют уникальные функциональные возможности для пользовательского интерфейса приложения Xamarin.Mac или создать пользовательскую тему пользовательского интерфейса (например, интерфейс игры).

В таких ситуациях может легко наследовать от `NSControl` и создать пользовательский инструмент, который можно добавить к пользовательскому Интерфейсу вашего приложения с помощью кода C# или с помощью конструктора Interface Builder. Путем наследования от `NSControl` пользовательского элемента управления автоматически получит все стандартные возможности, к которым имеет встроенного элемента управления интерфейс пользователя (например, `NSButton`).

Если пользовательский элемент управления пользовательского интерфейса просто отображает данные (такие как создание пользовательских диаграмм и графическое средство), можно наследовать от `NSView` вместо `NSControl`.

Независимо от того, какой базовый класс используется основные шаги для создания пользовательского элемента управления одинаков.

В этой статье будет создайте пользовательский компонент перевернуть коммутатора, предоставляющий уникальный тему пользовательского интерфейса и пример построения полнофункциональный пользовательский интерфейс пользовательского элемента управления.

<a name="Building-the-Custom-Control" />

## <a name="building-the-custom-control"></a>Создание пользовательского элемента управления

Так как пользовательский элемент управления, мы создаем будет отвечать на действия пользователя (нажатие кнопки мыши), мы собираемся наследовать от `NSControl`. Таким образом наш пользовательский элемент управления будет автоматически иметь все стандартные возможности, к которым имеет встроенный интерфейс пользовательского элемента управления и отвечать как macOS стандартный элемент управления.

В Visual Studio для Mac откройте проект Xamarin.Mac, который требуется для создания пользовательского интерфейса пользовательского элемента управления для (или создайте новую). Добавьте новый класс и назовите его `NSFlipSwitch`:

[![](custom-controls-images/custom01.png "Добавление нового класса")](custom-controls-images/custom01.png#lightbox)

Далее следует изменить `NSFlipSwitch.cs` класса и это должно выглядеть следующим образом:

```csharp
using Foundation;
using System;
using System.CodeDom.Compiler;
using AppKit;
using CoreGraphics;

namespace MacCustomControl
{
    [Register("NSFlipSwitch")]
    public class NSFlipSwitch : NSControl
    {
        #region Private Variables
        private bool _value = false;
        #endregion

        #region Computed Properties
        public bool Value {
            get { return _value; }
            set {
                // Save value and force a redraw
                _value = value;
                NeedsDisplay = true;
            }
        }
        #endregion

        #region Constructors
        public NSFlipSwitch ()
        {
            // Init
            Initialize();
        }

        public NSFlipSwitch (IntPtr handle) : base (handle)
        {
            // Init
            Initialize();
        }

        [Export ("initWithFrame:")]
        public NSFlipSwitch (CGRect frameRect) : base(frameRect) {
            // Init
            Initialize();
        }

        private void Initialize() {
            this.WantsLayer = true;
            this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
        }
        #endregion

        #region Draw Methods
        public override void DrawRect (CGRect dirtyRect)
        {
            base.DrawRect (dirtyRect);

            // Use Core Graphic routines to draw our UI
            ...

        }
        #endregion

        #region Private Methods
        private void FlipSwitchState() {
            // Update state
            Value = !Value;
        }
        #endregion

    }
}
```

Прежде всего следует отметить о нашем пользовательском классе, в том, что мы наследование `NSControl` и с помощью **зарегистрировать** команду, чтобы предоставить этот класс для Objective-C и Xcode конструктора Interface Builder:

```csharp
[Register("NSFlipSwitch")]
public class NSFlipSwitch : NSControl
```

В следующих разделах мы рассмотрим остальная часть приведенный выше код подробно.

<a name="Tracking-the-Controls-State" />

### <a name="tracking-the-controls-state"></a>Отслеживание состояния элемента управления

Поскольку наш пользовательский элемент управления является переключателем, нам нужно отслеживать состояние включения и отключения переключателя. Мы занимаемся, с помощью следующего кода в `NSFlipSwitch`:

```csharp
private bool _value = false;
...

public bool Value {
    get { return _value; }
    set {
        // Save value and force a redraw
        _value = value;
        NeedsDisplay = true;
    }
}
```

При изменении состояния переключателя, необходим способ для обновления пользовательского интерфейса. Для этого мы принудительно перерисовывает его пользовательского интерфейса с помощью элемента управления `NeedsDisplay = true`.

Если наш элемент управления, которые требуется один Вкл/Выкл состояния (например несколькими состояниями коммутатор с 3 позициях), можно было бы использовать **перечисления** для отслеживания состояния. В нашем примере простого **bool** подойдет.

Мы также добавили вспомогательный метод, чтобы переключить состояние переключения между включения и отключения:

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
}
```

Позже мы расширим этот вспомогательный класс для информирования вызывающего объекта при изменении состояния переключателей.

<a name="Drawing-the-Controls-Interface" />

### <a name="drawing-the-controls-interface"></a>Рисование элемента управления интерфейс

Мы собираемся с помощью графических Core рисования подпрограмм для рисования наш пользовательский элемент управления пользовательского интерфейса во время выполнения. Прежде чем это можно сделать, необходимо включить слои наш элемент управления. Это делается с помощью следующий частный метод:

```csharp
private void Initialize() {
    this.WantsLayer = true;
    this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

Этот метод вызывается из каждого элемента управления конструкторов, чтобы убедиться, что элемент управления настроен надлежащим образом. Пример:

```csharp
public NSFlipSwitch (IntPtr handle) : base (handle)
{
    // Init
    Initialize();
}
```

Далее нам нужно переопределить `DrawRect` метод и добавьте Core графических подпрограммы нарисуйте элемент управления:

```csharp
public override void DrawRect (CGRect dirtyRect)
{
    base.DrawRect (dirtyRect);

    // Use Core Graphic routines to draw our UI
    ...

}
```

Мы будем Настройка визуальное представление для элемента управления при изменении его состояния (например, переход от **на** для **Off**). Изменения состояния в любое время, мы можем использовать `NeedsDisplay = true` принудительно перерисовывает для нового состояния элемента управления.

<a name="Responding-to-User-Input" />

### <a name="responding-to-user-input"></a>Реагировать на действия пользователя

Существует два основных способа, что мы можем добавить входные данные пользователя нашего пользовательского элемента управления: **переопределить подпрограммы обработки мыши** или **распознавателей жестов**. Какой метод, мы используем будет основываться на функциональность, необходимую наш элемент управления.

> [!IMPORTANT]
> Для любого пользовательского элемента управления, создании, следует использовать **переопределить методы** _или_ **распознавателей жестов**, но не оба одновременно времени, как они могут конфликтовать друг с другом.

<a name="Summary" />

#### <a name="handling-user-input-with-override-methods"></a>Обработка введенных пользователем данных с помощью методы переопределения

Объекты, наследующие `NSControl` (или `NSView`) существует несколько переопределить методы для обработки мыши или клавиатуры входных данных. Наш пример элемента управления, мы хотим перевернуть состояние переключения между **на** и **Off** при нажатии на элемент управления с левой кнопки мыши. Мы можем добавить следующее переопределить методы, чтобы `NSFliwSwitch` класс для обработки этого:

```csharp
#region Mouse Handling Methods
// --------------------------------------------------------------------------------
// Handle mouse with Override Methods.
// NOTE: Use either this method or Gesture Recognizers, NOT both!
// --------------------------------------------------------------------------------
public override void MouseDown (NSEvent theEvent)
{
    base.MouseDown (theEvent);

    FlipSwitchState ();
}

public override void MouseDragged (NSEvent theEvent)
{
    base.MouseDragged (theEvent);
}

public override void MouseUp (NSEvent theEvent)
{
    base.MouseUp (theEvent);
}

public override void MouseMoved (NSEvent theEvent)
{
    base.MouseMoved (theEvent);
}
## endregion
```

В приведенном выше коде мы вызываем `FlipSwitchState` (описанный выше) перевернуть On/Off состояние коммутатора в метод `MouseDown` метод. Это также приведет к принудительной перерисовку для отражения текущего состояния элемента управления.

<a name="Handling-User-Input-with-Gesture-Recognizers" />

#### <a name="handling-user-input-with-gesture-recognizers"></a>Обработка введенных пользователем данных с помощью средства распознавания жестов

При необходимости можно использовать средства распознавания жестов для обработки пользователь, взаимодействующий с элементом управления. Удалить переопределения, добавленных выше, измените `Initialize` метода и это должно выглядеть следующим образом:

```csharp
private void Initialize() {
    this.WantsLayer = true;
    this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;

    // --------------------------------------------------------------------------------
    // Handle mouse with Gesture Recognizers.
    // NOTE: Use either this method or the Override Methods, NOT both!
    // --------------------------------------------------------------------------------
    var click = new NSClickGestureRecognizer (() => {
        FlipSwitchState();
    });
    AddGestureRecognizer (click);
}
```

Здесь мы создаем новый `NSClickGestureRecognizer` и вызова наших `FlipSwitchState` метод, чтобы изменить состояние переключателя, когда пользователь щелкает его с левой кнопки мыши. `AddGestureRecognizer (click)` Метод добавляет средства распознавания жестов в элемент управления.

Опять же какой метод, мы используем зависит от мы пытаемся выполнить с помощью наших пользовательского элемента управления. Если требуется низкий уровень доступа для взаимодействия с пользователем, используйте методы переопределения. Если необходимо предварительно определенной функции, например на щелчки мыши, используйте средства распознавания жестов.

<a name="Responding-to-State-Change-Events" />

### <a name="responding-to-state-change-events"></a>Отвечает на события изменения состояния

Когда пользователь изменяет состояние наш пользовательский элемент управления, необходим способ реагировать на изменение состояния в коде (например, действия при нажатии настраиваемой кнопки). 

Для обеспечения данной функциональности, изменить `NSFlipSwitch` класса и добавьте следующий код:

```csharp
#region Events
public event EventHandler ValueChanged;

internal void RaiseValueChanged() {
    if (this.ValueChanged != null)
        this.ValueChanged (this, EventArgs.Empty);

    // Perform any action bound to the control from Interface Builder
    // via an Action.
    if (this.Action !=null) 
        NSApplication.SharedApplication.SendAction (this.Action, this.Target, this);
}
## endregion
```

Далее следует изменить `FlipSwitchState` метода и это должно выглядеть следующим образом:

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
    RaiseValueChanged ();
}
```

Во-первых, мы предоставляем `ValueChanged` событий, можно добавить обработчик в коде C#, чтобы мы может выполнить действие, когда пользователь изменяет состояние коммутатора.

Во-вторых, поскольку наш пользовательский элемент управления наследует от `NSControl`, он автоматически использует **действие** можно назначать в Interface Builder в Xcode. Чтобы вызвать это **действие** при изменении состояния, мы используем следующий код:

```csharp
if (this.Action !=null) 
    NSApplication.SharedApplication.SendAction (this.Action, this.Target, this);
```

Во-первых, мы проверяем Если **действия** был назначен элемент управления. Затем мы вызываем **действие** если он был определен.

<a name="Using-the-Custom-Control" />

## <a name="using-the-custom-control"></a>Использование пользовательского элемента управления

С наш пользовательский элемент управления полностью определена можно либо добавить его пользовательского интерфейса приложения Xamarin.Mac с использованием кода C# или конструктора Interface Builder.

Чтобы добавить элемент управления с помощью конструктора Interface Builder, сначала выполните чистую сборку проекта Xamarin.Mac, а затем дважды щелкните `Main.storyboard` файл, чтобы открыть его в конструктор Interface Builder для редактирования:

[![](custom-controls-images/custom02.png "Изменение раскадровки в Xcode")](custom-controls-images/custom02.png#lightbox)

Затем перетащите `Custom View` проектирования пользовательского интерфейса:

[![](custom-controls-images/custom03.png "Выбор представления из библиотеки")](custom-controls-images/custom03.png#lightbox)

Пользовательское представление по-прежнему выбран, можно переключиться в **инспектор удостоверений** и изменить представление **класс** для `NSFlipSwitch`:

[![](custom-controls-images/custom04.png "Класс представления параметров")](custom-controls-images/custom04.png#lightbox)

Переключиться в режим **вспомогательном редакторе** и создайте **розетки** для пользовательского элемента управления (не забывая привязать его в `ViewControler.h` файл и не `.m` файл):

[![](custom-controls-images/custom05.png "Настройка нового выхода")](custom-controls-images/custom05.png#lightbox)

Сохраните изменения, вернитесь в Visual Studio для Mac и разрешить изменения в синхронизации. Изменить `ViewController.cs` и создайте `ViewDidLoad` внешний метод следующим образом:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Do any additional setup after loading the view.
    OptionTwo.ValueChanged += (sender, e) => {
        // Display the state of the option switch
        Console.WriteLine("Option Two: {0}", OptionTwo.Value);
    };
}
``` 

Здесь мы отвечаем на `ValueChanged` событий, определенным выше на `NSFlipSwitch` класса и записать текущий **значение** при нажатии на элемент управления.

При необходимости может вернуться в конструктор Interface Builder и определить **действие** на элементе управления:

[![](custom-controls-images/custom06.png "Настройка нового действия")](custom-controls-images/custom06.png#lightbox)

Опять же, изменить `ViewController.cs` файл и добавьте следующий метод:

```csharp
partial void OptionTwoFlipped (Foundation.NSObject sender) {
    // Display the state of the option switch
    Console.WriteLine("Option Two: {0}", OptionTwo.Value);
}
```

> [!IMPORTANT]
> Следует использовать либо **событий** или определить **действие** в построителе интерфейса, но не следует использовать оба метода, в то же время или они могут конфликтовать друг с другом.

<a name="Summary" />

## <a name="summary"></a>Сводка

В этой статье были предприняты никакие подробное описание создания многократно используемых пользовательских пользовательского интерфейса элемента управления в приложении Xamarin.Mac. Мы узнали, как рисовать пользовательские элементы управления пользовательского интерфейса, двумя основными способами реагировать на ввод данных пользователем и мыши и способ предоставления нового элемента управления к действиям в Interface Builder в Xcode.

## <a name="related-links"></a>Связанные ссылки

- [MacCustomControl (пример)](https://developer.xamarin.com/samples/mac/MacCustomControl/)
- [Привет, Mac](~/mac/get-started/hello-mac.md)
- [Привязка данных и кодирование типа "ключ — значение"](~/mac/app-fundamentals/databinding.md)
- [Рекомендации по работе с человеческим интерфейсом OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Обработка событий мыши](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/EventOverview/HandlingMouseEvents/HandlingMouseEvents.html)
