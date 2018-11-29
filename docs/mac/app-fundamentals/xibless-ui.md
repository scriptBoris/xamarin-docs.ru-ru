---
title: пользовательского интерфейса.Storyboard/.xib-less в Xamarin.Mac
description: В этой статье описано, как создавать пользовательский интерфейс приложения непосредственно из C# кода без .storyboard файлы xib-файлы и конструктора Interface Builder.
ms.prod: xamarin
ms.assetid: 02310F58-DCF1-4589-9F4A-065DF64FC0E1
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 076c6464359a58c2b36d157d9620673b0644cd4a
ms.sourcegitcommit: 2f6a5c1abf90fbdb0475fd8a3ce6de3cd7c7d575
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52459841"
---
# <a name="storyboardxib-less-user-interface-design-in-xamarinmac"></a>пользовательского интерфейса.Storyboard/.xib-less в Xamarin.Mac

_В этой статье описано, как создавать пользовательский интерфейс приложения непосредственно из C# кода без .storyboard файлы xib-файлы и конструктора Interface Builder._

## <a name="overview"></a>Обзор

При работе с C# и .NET в приложении Xamarin.Mac, имеют доступ к тем же элементы пользовательского интерфейса и средства, работающий в *Objective-C* и *Xcode* does. Как правило при создании приложения Xamarin.Mac, используемого конструктора Interface Builder .storyboard или .xib файлов для создания и обслуживания вы пользовательского интерфейса приложения.

Также имеется возможность создать непосредственно в некоторые или все из пользовательского интерфейса приложения Xamarin.Mac C# кода. В этой статье мы обсудим основные принципы создания пользовательских интерфейсов и элементов пользовательского интерфейса в C# кода.

[![Редактор Visual Studio для Mac кода](xibless-ui-images/intro01.png "Visual Studio для Mac редактора кода")](xibless-ui-images/intro01-large.png#lightbox)

<a name="Switching_a_Window_to_use_Code" />

## <a name="switching-a-window-to-use-code"></a>Переключение окна для использования кода

При создании нового приложения Cocoa для Xamarin.Mac, вы получаете стандартное окно пустым, по умолчанию. В данном окне определяется в **Main.storyboard** (или традиционно **MainWindow.xib**) файл, автоматически включаются в проект. Сюда также входят **ViewController.cs** файл, который управляет представлением главного приложения (или снова традиционно **MainWindow.cs** и **MainWindowController.cs** файла).

Чтобы переключиться в окно Xibless для приложения, сделайте следующее:

1. Откройте приложение, которое вы хотите остановить использование `.storyboard` или xib-файлы для определения пользовательского интерфейса в Visual Studio для Mac.
2. В **панели решения**, щелкните правой кнопкой мыши **Main.storyboard** или **MainWindow.xib** файл и выберите **удалить**: 

    ![Удаление основной раскадровки или окно](xibless-ui-images/switch01.png "основной раскадровки или окно")
3. Из **диалоговое окно, удалите**, нажмите кнопку **удалить** кнопку, чтобы полностью удалить .storyboard или .xib из проекта: 

    ![Подтверждение удаления](xibless-ui-images/switch02.png "подтверждения удаления")

Теперь нам нужно будет изменить **MainWindow.cs** файл для определения макета окна и изменения **ViewController.cs** или **MainWindowController.cs** создаваемого файла экземпляр нашей `MainWindow` класса, так как мы больше не используется в файле .storyboard или .xib.

Современные приложения Xamarin.Mac, для их пользовательского интерфейса не включается автоматически при помощи объектов Storyboard **MainWindow.cs**, **ViewController.cs** или **MainWindowController.cs** файлов. При необходимости, просто добавьте новую пустую C# класс в проект (**добавить** > **новый файл...**   >  **Общие** > **пустой класс**) и назовите его так же, как отсутствующий файл. 


### <a name="defining-the-window-in-code"></a>Определение окна в коде

Далее следует изменить **MainWindow.cs** файл и это должно выглядеть следующим образом:

```csharp
using System;
using Foundation;
using AppKit;
using CoreGraphics;

namespace MacXibless
{
    public partial class MainWindow : NSWindow
    {
        #region Private Variables
        private int NumberOfTimesClicked = 0;
        #endregion

        #region Computed Properties
        public NSButton ClickMeButton { get; set;}
        public NSTextField ClickMeLabel { get ; set;}
        #endregion

        #region Constructors
        public MainWindow (IntPtr handle) : base (handle)
        {
        }

        [Export ("initWithCoder:")]
        public MainWindow (NSCoder coder) : base (coder)
        {
        }

        public MainWindow(CGRect contentRect, NSWindowStyle aStyle, NSBackingStore bufferingType, bool deferCreation): base (contentRect, aStyle,bufferingType,deferCreation) {
            // Define the user interface of the window here
            Title = "Window From Code";

            // Create the content view for the window and make it fill the window
            ContentView = new NSView (Frame);

            // Add UI elements to window
            ClickMeButton = new NSButton (new CGRect (10, Frame.Height-70, 100, 30)){
                AutoresizingMask = NSViewResizingMask.MinYMargin
            };
            ContentView.AddSubview (ClickMeButton);

            ClickMeLabel = new NSTextField (new CGRect (120, Frame.Height - 65, Frame.Width - 130, 20)) {
                BackgroundColor = NSColor.Clear,
                TextColor = NSColor.Black,
                Editable = false,
                Bezeled = false,
                AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin,
                StringValue = "Button has not been clicked yet."
            };
            ContentView.AddSubview (ClickMeLabel);
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Wireup events
            ClickMeButton.Activated += (sender, e) => {
                // Update count
                ClickMeLabel.StringValue = (++NumberOfTimesClicked == 1) ? "Button clicked one time." : string.Format("Button clicked {0} times.",NumberOfTimesClicked);
            };
        }
        #endregion

    }
}
```

Давайте рассмотрим некоторые из ключевых элементов.

Во-первых, мы добавили несколько _вычисляемые свойства_ который будет функционировать как переменные экземпляров (как, если окна был создан в файле .storyboard или .xib):

```csharp
public NSButton ClickMeButton { get; set;}
public NSTextField ClickMeLabel { get ; set;}
```

Это даст нам доступ к элементам пользовательского интерфейса, которые мы собираемся отображать в окне. Так как не производительность заметно окна из файла .storyboard или .xib, необходим способ для создания его экземпляра (как мы увидим позже на `MainWindowController` класса). Вот что делает этот новый метод-конструктор:

```csharp
public MainWindow(CGRect contentRect, NSWindowStyle aStyle, NSBackingStore bufferingType, bool deferCreation): base (contentRect, aStyle,bufferingType,deferCreation) {
    ...
}
```

Это, где мы макет окна и поместите все элементы пользовательского интерфейса, необходимые для создания необходимых пользовательского интерфейса. Перед добавлением все элементы пользовательского интерфейса на окно, он должен _представление содержимого_ будет содержать элементы:

```csharp
ContentView = new NSView (Frame);
```

Это создает представление содержимого, который будет заполнять окна. Теперь мы добавим наш первый элемент пользовательского интерфейса, `NSButton`, в окно:

```csharp
ClickMeButton = new NSButton (new CGRect (10, Frame.Height-70, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
ContentView.AddSubview (ClickMeButton);
```

Первое, что здесь необходимо обратить внимание — что, в отличие от iOS, macOS использует математическую нотацию для определения его система координат окна. Поэтому исходная точка находится в левом нижнем углу окна, с увеличением значения вправо и вперед в правом верхнем углу окна. Когда мы создадим новый `NSButton`, мы учитываем это как на экране мы определяем его положения и размера.

`AutoresizingMask = NSViewResizingMask.MinYMargin` Свойство указывает кнопке, мы должны находиться в том же расположении, в верхней части окна при изменении размера окна по вертикали. Опять же, что это является обязательным, поскольку (0,0) находится в левом нижнем углу окна.

Наконец `ContentView.AddSubview (ClickMeButton)` добавляет метод `NSButton` в представлении содержимого, так что он будет отображаться на экране, когда приложение выполнения и окно.

Далее метка добавится на окно, которое будет отображаться количество раз, `NSButton` был выполнен щелчок: 

```csharp
ClickMeLabel = new NSTextField (new CGRect (120, Frame.Height - 65, Frame.Width - 130, 20)) {
    BackgroundColor = NSColor.Clear,
    TextColor = NSColor.Black,
    Editable = false,
    Bezeled = false,
    AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin,
    StringValue = "Button has not been clicked yet."
};
ContentView.AddSubview (ClickMeLabel);
``` 

Так как macOS не имеет конкретного _метка_ элемент пользовательского интерфейса, мы добавили специально оформленного неизменяемые `NSTextField` в качестве метки. Подобно тому, как кнопка раньше, принимает размер и расположение, в учетную запись (0,0) находится в нижней левой части окна. `AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin` Использует свойство **или** оператор, чтобы объединить два `NSViewResizingMask` функции. Это сделает метки остаются в том же расположении, в верхней части окна, при изменении размера окна по вертикали и сжатия и увеличения ширины при изменении размеров окна по горизонтали.

Опять же `ContentView.AddSubview (ClickMeLabel)` добавляет метод `NSTextField` в представлении содержимого, так что он будет отображаться на экране при его запуске и открытии окна.


### <a name="adjusting-the-window-controller"></a>Настройка контроллер окна

Так как структура `MainWindow` больше не загружается из файла .storyboard или .xib, нам нужно будет внести некоторые изменения в контроллер окна. Изменить **MainWindowController.cs** файл и это должно выглядеть следующим образом:

```csharp
using System;

using Foundation;
using AppKit;
using CoreGraphics;

namespace MacXibless
{
    public partial class MainWindowController : NSWindowController
    {
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }

        [Export ("initWithCoder:")]
        public MainWindowController (NSCoder coder) : base (coder)
        {
        }

        public MainWindowController () : base ("MainWindow")
        {
            // Construct the window from code here
            CGRect contentRect = new CGRect (0, 0, 1000, 500);
            base.Window = new MainWindow(contentRect, (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable), NSBackingStore.Buffered, false);

            // Simulate Awaking from Nib
            Window.AwakeFromNib ();
        }

        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();
        }

        public new MainWindow Window {
            get { return (MainWindow)base.Window; }
        }
    }
}

```

Позвольте рассматриваются ключевые элементы этого изменения.

Во-первых, определим новый экземпляр класса `MainWindow` класса и его назначение контроллер базового окна `Window` свойство:

```csharp
CGRect contentRect = new CGRect (0, 0, 1000, 500);
base.Window = new MainWindow(contentRect, (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable), NSBackingStore.Buffered, false);
```

Мы определяем расположение окна экрана с `CGRect`. Так же, как система координат окна экрана определяет (0,0) в левом нижнем углу. Далее мы определим стиль окна с помощью **или** оператор для объединения двух или более `NSWindowStyle` функции:

```csharp
... (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable) ...
``` 

Следующие `NSWindowStyle` функции доступны:

- **Без рамки** -окно имеет без рамки.
- **Под названием** -окно имеет заголовок окна.
- **Closable** -окно имеет кнопку «Close» и может быть закрыт.
- **Miniaturizable** -окно имеет кнопку Miniaturize и может быть свернута.
- **Можно изменять** -кнопка Изменить размер и быть регулируемого размера окна.
- **Программа** -окно является окном стиля служебной программы (панель).
- **DocModal** -Если окна — это панель, она будет модальное документа вместо модальное системы. 
- **NonactivatingPanel** -Если окна — это панель, она не будут совершаться главного окна.
- **TexturedBackground** -окно имеет текстуры фона.
- **Немасштабированным** -окна не масштабируется.
- **UnifiedTitleAndToolbar** -области заголовка и панель инструментов окна будет присоединен.
- **Hud** -окно будет отображаться как индикации панели.
- **FullScreenWindow** -окне можно ввести в полноэкранном режиме.
- **FullSizeContentView** -представление содержимого окна находится за строку заголовка и область панели инструментов.

Последние два свойства определяют _тип буферизации_ для окна и если рисование окна будет отложено. Дополнительные сведения о `NSWindows`, см. в разделе Apple [введение в Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1) документации.

Наконец, поскольку не производительность заметно окна из файла .storyboard или .xib, нам нужно для имитации его в нашей **MainWindowController.cs** путем вызова windows `AwakeFromNib` метод:

```csharp
Window.AwakeFromNib ();
```

Это позволит вам код для окна точно так же как стандартное окно загружаются из файла .storyboard или .xib.


### <a name="displaying-the-window"></a>Отображение окна

.Storyboard или .xib файла удалена и **MainWindow.cs** и **MainWindowController.cs** файлы изменены, вы будете использовать окна так же, как и любого обычного окна, которая была создана в Interface Builder в Xcode с файлом .xib.

Ниже будет создать новый экземпляр окна и его контроллера и отображение окна на экране:

```csharp
private MainWindowController mainWindowController;
...

mainWindowController = new MainWindowController ();
mainWindowController.Window.MakeKeyAndOrderFront (this);
```

На этом этапе при запуске приложения и нажатии кнопки несколько раз, ниже будет отображаться:

![Запустите пример приложения](xibless-ui-images/run01.png "Запустите пример приложения")


## <a name="adding-a-code-only-window"></a>Добавление только окно кода

Если мы хотим добавить в существующее приложение Xamarin.Mac только код, xibless окно, щелкните правой кнопкой мыши проект в **панели решения** и выберите **добавить** > **новый файл...** . В **новый файл** диалогового окна выберите **Xamarin.Mac** > **окно Cocoa с контроллером**, как показано ниже:

![Добавление нового контроллера окно](xibless-ui-images/add01.png "добавлении нового контроллера окна") 

Как и ранее, мы удалим .storyboard или .xib файл по умолчанию из проекта (в данном случае **SecondWindow.xib**) и выполните действия, описанные в [переключение окна, используемый код](#Switching_a_Window_to_use_Code) раздел выше для охвата Определение окна кода.


## <a name="adding-a-ui-element-to-a-window-in-code"></a>Добавление элемента пользовательского интерфейса в окно, в коде

Окна был создан в коде или загружен из файла .storyboard или .xib, может возникнуть где мы хотим добавить элемент пользовательского интерфейса в окно из кода. Пример:

```csharp
var ClickMeButton = new NSButton (new CGRect (10, 10, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
MyWindow.ContentView.AddSubview (ClickMeButton);
```

Приведенный выше код создает новый `NSButton` и добавляет ее к `MyWindow` экземпляр окна для отображения. По сути любой элемент пользовательского интерфейса, который можно задать в конструктора Interface Builder в файле .storyboard или .xib можно создать в коде и отображается в окне.


## <a name="defining-the-menu-bar-in-code"></a>Определение в строке меню в коде

Из-за ограничений в Xamarin.Mac не рекомендуется создавать приложения Xamarin.Mac меню панели —`NSMenuBar`— в коде, но продолжать использовать **Main.storyboard** или **MainMenu.xib** файл, чтобы определить его. С другой стороны, вы можете добавлять и удалять меню и пунктов меню в C# кода.

Например, изменить **AppDelegate.cs** и создайте `DidFinishLaunching` внешний метод следующим образом:

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    mainWindowController = new MainWindowController ();
    mainWindowController.Window.MakeKeyAndOrderFront (this);

    // Create a Status Bar Menu
    NSStatusBar statusBar = NSStatusBar.SystemStatusBar;

    var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);
    item.Title = "Phrases";
    item.HighlightMode = true;
    item.Menu = new NSMenu ("Phrases");

    var address = new NSMenuItem ("Address");
    address.Activated += (sender, e) => {
        Console.WriteLine("Address Selected");
    };
    item.Menu.AddItem (address);

    var date = new NSMenuItem ("Date");
    date.Activated += (sender, e) => {
        Console.WriteLine("Date Selected");
    };
    item.Menu.AddItem (date);

    var greeting = new NSMenuItem ("Greeting");
    greeting.Activated += (sender, e) => {
        Console.WriteLine("Greetings Selected");
    };
    item.Menu.AddItem (greeting);

    var signature = new NSMenuItem ("Signature");
    signature.Activated += (sender, e) => {
        Console.WriteLine("Signature Selected");
    };
    item.Menu.AddItem (signature);
}
```

Выше создается меню строки состояния из кода и отображается при запуске приложения. Дополнительные сведения о работе с меню, см. в разделе наших [меню](~/mac/user-interface/menu.md) документации.


## <a name="summary"></a>Сводка

В этой статье были предприняты никакие более подробно создание пользовательского интерфейса приложения Xamarin.Mac в C# кода, а не с помощью конструктора Interface Builder с .storyboard или .xib файлов.



## <a name="related-links"></a>Связанные ссылки

- [MacXibless (пример)](https://developer.xamarin.com/samples/mac/MacXibless/)
- [Windows](~/mac/user-interface/window.md)
- [Меню](~/mac/user-interface/menu.md)
- [Рекомендации по созданию пользовательских интерфейсов в macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [Введение в Windows](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/WinPanel/Introduction.html)
