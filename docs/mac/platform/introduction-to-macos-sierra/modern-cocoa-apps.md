---
title: Создание современных приложений macOS
description: В этой статье рассматривается несколько советов, возможности и методы, разработчик может использовать для создания приложения на основе современных macOS в Xamarin.Mac.
ms.prod: xamarin
ms.assetid: F20EE590-246E-40EB-B309-D9D8C090C7F1
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 53bfc9f147b6cf369b8f5ce8d1257dbaf6b0f807
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113525"
---
# <a name="building-modern-macos-apps"></a>Создание современных приложений macOS

_В этой статье рассматривается несколько советов, возможности и методы, разработчик может использовать для создания приложения на основе современных macOS в Xamarin.Mac._

<a name="Building-Modern-Looks-with-Modern-Views" />

## <a name="building-modern-looks-with-modern-views"></a>Создание современных выглядит с помощью современных представлений

Современный интерфейс будет включать более современных вид окна и панели инструментов такие как пример приложения, показано ниже:

[![](modern-cocoa-apps-images/content08.png "Пример из современного пользовательского интерфейса приложения Mac")](modern-cocoa-apps-images/content08.png#lightbox)

<a name="Enabling-Full-Sized-Content-Views" />

### <a name="enabling-full-sized-content-views"></a>Включение полного размера содержимого представления

Чтобы добиться этого выглядит в приложении Xamarin.Mac, разработчику потребуется использовать _полный размер содержимого представления_, то есть содержимое распространяется в области заголовка окна и средства и будет автоматически размыто с macOS.

Чтобы включить эту функцию в коде, создайте пользовательский класс для `NSWindowController` и это должно выглядеть следующим образом:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainWindowController : NSWindowController
    {
        #region Constructor
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Set window to use Full Size Content View
            Window.StyleMask = NSWindowStyle.FullSizeContentView;
        }
        #endregion
    }
}
```

Также можно включить эту функцию в Interface Builder в Xcode, выбрав окна и проверка **полного размера представления содержимого**:

[![](modern-cocoa-apps-images/content01.png "Изменение основной раскадровки в Interface Builder в Xcode")](modern-cocoa-apps-images/content01.png#lightbox)

При использовании полный размер представления содержимого, разработчик может потребоваться смещение содержимое под области панель заголовка и средства, чтобы определенное содержимое (например, метки) не слайд в них.

Усложняет эту проблему, области заголовка и панели инструментов можно иметь динамические высоту на основе действия, в которой он выполняется, версия macOS пользователь установил и/или оборудования Mac, на котором приложение выполняется на.

Таким образом просто жесткого кодирования смещение, при создании макетов пользовательский интерфейс не будет работать. Разработчику необходимо динамического подхода.

Включает в себя Apple [ключ-значение наблюдаемый](~/mac/app-fundamentals/databinding.md#Observing_Value_Changes) `ContentLayoutRect` свойство `NSWindow` класса для получения текущей области содержимого в коде. Разработчик может использовать это значение, чтобы вручную изменить расположение необходимых элементов при изменении области содержимого.

Лучшее решение — использовать автоматический макет и классы размера для размещения элементов пользовательского интерфейса в код или конструктор Interface Builder.

Код, как в следующем примере, может использоваться для размещения элементов пользовательского интерфейса, с помощью Автомакет и размер классов в контроллер представления приложения:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacModern
{
    public partial class ViewController : NSViewController
    {
        #region Computed Properties
        public NSLayoutConstraint topConstraint { get; set; }
        #endregion

        ...

        #region Override Methods
        public override void UpdateViewConstraints ()
        {
            // Has the constraint already been set?
            if (topConstraint == null) {
                // Get the top anchor point
                var contentLayoutGuide = ItemTitle.Window?.ContentLayoutGuide as NSLayoutGuide;
                var topAnchor = contentLayoutGuide.TopAnchor;

                // Found?
                if (topAnchor != null) {
                    // Assemble constraint and activate it
                    topConstraint = topAnchor.ConstraintEqualToAnchor (topAnchor, 20);
                    topConstraint.Active = true;
                }
            }

            base.UpdateViewConstraints ();
        }
        #endregion
    }
}
```

Этот код создает хранилище для верхнего ограничения, которые применяются к метке (`ItemTitle`) чтобы убедиться, что он не сдвигаться в области заголовка и панели инструментов:

```csharp
public NSLayoutConstraint topConstraint { get; set; }
```

Путем переопределения контроллер представления `UpdateViewConstraints` метод, разработчик может проверить, если необходимые ограничения уже собран и создать его при необходимости.

Если новое ограничение должен быть построен, `ContentLayoutGuide` свойства окна, элемент управления, который необходимо ограничить доступ и приведен к типу `NSLayoutGuide`:

```csharp
var contentLayoutGuide = ItemTitle.Window?.ContentLayoutGuide as NSLayoutGuide;
```

Свойство TopAnchor `NSLayoutGuide` доступна и если он доступен, он используется для создания нового ограничения с требуемой суммой смещения и новое ограничение он станет активным, чтобы применить его:

```csharp
// Assemble constraint and activate it
topConstraint = topAnchor.ConstraintEqualToAnchor (topAnchor, 20);
topConstraint.Active = true;
```

<a name="Enabling-Streamlined-Toolbars" />

### <a name="enabling-streamlined-toolbars"></a>Включение упрощенный панелей инструментов

Обычный macOS окна включает стандартным строки заголовка в запускается верхнего края окна. Если окно также включает панель инструментов, он будет отображаться в этой области заголовка:

[![](modern-cocoa-apps-images/content02.png "Стандартная панель инструментов Mac")](modern-cocoa-apps-images/content02.png#lightbox)

Когда исчезнет с помощью упрощенной панели инструментов, области заголовка и панели инструментов Перемещение вверх в позицию в строке заголовка, в строки с кнопками окна закрыть, свернуть и развернуть:

[![](modern-cocoa-apps-images/content03.png "Упрощенная панель инструментов Mac")](modern-cocoa-apps-images/content03.png#lightbox)

Упрощенный панели инструментов включен, переопределив `ViewWillAppear` метод `NSViewController` и делая это должно выглядеть следующим образом:

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Enable streamlined Toolbars
    View.Window.TitleVisibility = NSWindowTitleVisibility.Hidden;
}
```

Этот эффект обычно используется для _Shoebox приложений_ (одно окно приложения), такие как карты, календаря, заметок и системные настройки. 

<a name="Using-Accessory-View-Controllers" />

### <a name="using-accessory-view-controllers"></a>С помощью контроллеров представлений периферийных устройств

В зависимости от структуры приложения разработчик может потребоваться дополнить строку заголовка, область с контроллером представления периферийного устройства, отображается справа под областью панель заголовка и инструментов для предоставления контекстной элементы управления на основе действия пользователя они являются настоящее время участвует в:

[![](modern-cocoa-apps-images/content04.png "Пример контроллера представления периферийных устройств")](modern-cocoa-apps-images/content04.png#lightbox)

Контроллер представления периферийного устройства будут автоматически размыть и размер в системе, не привлекая разработчиков.

Чтобы добавить контроллер представления периферийных устройств, сделайте следующее:

1. В **обозревателе решений** дважды щелкните файл `Main.storyboard`, чтобы открыть его для редактирования.
2. Перетащите **контроллер представления Custom** в иерархию окна: 

    [![](modern-cocoa-apps-images/content05.png "Добавление нового контроллера представления Custom")](modern-cocoa-apps-images/content05.png#lightbox)
3. Макета аксессуаров представление пользовательского интерфейса: 

    [![](modern-cocoa-apps-images/content06.png "Разработка нового представления")](modern-cocoa-apps-images/content06.png#lightbox)
4. Предоставлять аксессуаров представлении в качестве **розетки** и любые другие **действия** или **розеток** для его пользовательского интерфейса: 

    [![](modern-cocoa-apps-images/content07.png "Добавление необходимых переменной экземпляра")](modern-cocoa-apps-images/content07.png#lightbox)
5. Сохраните изменения.
6. Вернитесь к Visual Studio для Mac синхронизировать изменения.

Изменить `NSWindowController` и это должно выглядеть следующим образом:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainWindowController : NSWindowController
    {
        #region Constructor
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Create a title bar accessory view controller and attach
            // the view created in Interface Builder
            var accessoryView = new NSTitlebarAccessoryViewController ();
            accessoryView.View = AccessoryViewGoBar;

            // Set the location and attach the accessory view to the
            // titlebar to be displayed
            accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
            Window.AddTitlebarAccessoryViewController (accessoryView);

        }
        #endregion
    }
}
```

Основные преимущества этот код, где представление имеет значение для настраиваемого представления, определенные в конструктор Interface Builder и в виде **розетки**:

```csharp
accessoryView.View = AccessoryViewGoBar;
```

И `LayoutAttribute` , определяющий _где_ периферийного устройства будет отображаться:

```csharp
accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
```

Так как macOS теперь полностью локализованной, `Left` и `Right` `NSLayoutAttribute` свойства, которые являются устаревшими и следует заменить на `Leading` и `Trailing`.

<a name="Using-Tabbed-Windows" />

### <a name="using-tabbed-windows"></a>С помощью Windows с вкладками

Кроме того система macOS может добавить контроллеры представления периферийного устройства окна приложения. Например, чтобы создать Windows с вкладками, где некоторые из приложения Windows объединяются в одно окно виртуального:

[![](modern-cocoa-apps-images/content08.png "Пример окна с вкладками Mac")](modern-cocoa-apps-images/content08.png#lightbox)

Обычно разработчику потребуется предпринять действия ограниченного использования Windows с вкладками в свои приложения Xamarin.Mac, система будет обрабатывать их автоматически, как показано ниже:

- Windows будет автоматически с вкладками, когда `OrderFront` вызывается метод.
- Windows будет автоматически Untabbed при `OrderOut` вызывается метод.
- В коде, по-прежнему считаются «visible» всех окон с вкладками Однако не переднее вкладки скрыты по систему, используя CoreGraphics.
- Используйте `TabbingIdentifier` свойство `NSWindow` для группы Windows друг с другом по вкладкам.
- Если это `NSDocument` приложения на основе, несколькими из этих возможностей будет включена автоматически (например, знак "плюс", добавляемый на панель вкладок) без вмешательства разработчика.
- Отличным от`NSDocument` на основе приложений можно включить кнопку «плюс» в группе вкладок, чтобы добавить новый документ, переопределив `GetNewWindowForTab` метод `NSWindowsController`.

Объединяя все элементы, `AppDelegate` приложения, необходимо использовать Windows с вкладками на основе системы может выглядеть следующим образом:

```csharp
using AppKit;
using Foundation;

namespace MacModern
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public int NewDocumentNumber { get; set; } = 0;
        #endregion

        #region Constructors
        public AppDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }
        #endregion

        #region Custom Actions
        [Export ("newDocument:")]
        public void NewDocument (NSObject sender)
        {
            // Get new window
            var storyboard = NSStoryboard.FromName ("Main", null);
            var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

            // Display
            controller.ShowWindow (this);
        } 
        #endregion
    }
}
```

Где `NewDocumentNumber` свойство следит за количество новых документов, создаваемых и `NewDocument` метод создает новый документ и отображает его.

`NSWindowController` Затем может выглядеть следующим образом:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainWindowController : NSWindowController
    {
        #region Application Access
        /// <summary>
        /// A helper shortcut to the app delegate.
        /// </summary>
        /// <value>The app.</value>
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructor
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Public Methods
        public void SetDefaultDocumentTitle ()
        {
            // Is this the first document?
            if (App.NewDocumentNumber == 0) {
                // Yes, set title and increment
                Window.Title = "Untitled";
                ++App.NewDocumentNumber;
            } else {
                // No, show title and count
                Window.Title = $"Untitled {App.NewDocumentNumber++}";
            }
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Prefer Tabbed Windows
            Window.TabbingMode = NSWindowTabbingMode.Preferred;
            Window.TabbingIdentifier = "Main";

            // Set default window title
            SetDefaultDocumentTitle ();

            // Set window to use Full Size Content View
            // Window.StyleMask = NSWindowStyle.FullSizeContentView;

            // Create a title bar accessory view controller and attach
            // the view created in Interface Builder
            var accessoryView = new NSTitlebarAccessoryViewController ();
            accessoryView.View = AccessoryViewGoBar;

            // Set the location and attach the accessory view to the
            // titlebar to be displayed
            accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
            Window.AddTitlebarAccessoryViewController (accessoryView);

        }

        public override void GetNewWindowForTab (NSObject sender)
        {
            // Ask app to open a new document window
            App.NewDocument (this);
        }
        #endregion
    }
}
```

Где статический `App` свойство существует сочетание клавиш для получения `AppDelegate`. `SetDefaultDocumentTitle` Метод задает новый заголовок документов на основе числа новых документов, создаваемых.

Следующий код, сообщает macOS, приложения которых предпочтительно использовать вкладки и предоставляет строку, которая позволяет приложения Windows, которому требуется выполнить группировку по вкладкам:

```csharp
// Prefer Tabbed Windows
Window.TabbingMode = NSWindowTabbingMode.Preferred;
Window.TabbingIdentifier = "Main";
```

И следующий метод переопределения добавляет "плюс" на панели вкладки, при этом будет создан новый документ при щелчке пользователем:

```csharp
public override void GetNewWindowForTab (NSObject sender)
{
    // Ask app to open a new document window
    App.NewDocument (this);
}
```

<a name="Using-Core-Animation" />

### <a name="using-core-animation"></a>С помощью базовой анимации

Анимация Core является механизм визуализации мощного графики, который встроен в macOS. Чтобы воспользоваться преимуществами GPU (графическом процессоре), доступного в оборудовании современных macOS в отличие от операций графики на использование ЦП, которое может замедлить работу машины был оптимизирован базовой анимации.

`CALayer`, Предоставляемые Core Animation, можно использовать для задач, таких как Быстрая и плавная прокрутка и анимации. Пользовательский интерфейс приложения должен состоять из нескольких представлений и слоев, чтобы полностью воспользоваться преимуществами базовой анимации.

Объект `CALayer` объект предоставляет несколько свойств, которые позволяют разработчикам управлять, чем представлено на экране, чтобы пользователь например:

- `Content` — Может быть `NSImage` или `CGImage` , предоставляющий содержимое слоя.
- `BackgroundColor` — Задает цвет фона слой как `CGColor`
- `BorderWidth` — Задает ширину границы.
- `BorderColor` — Задает цвет границы.

Чтобы использовать двухмерной графики в пользовательском Интерфейсе приложения, его необходимо использовать _слой резервного_ представления, которые Apple предполагает, что разработчик всегда следует включить в представление содержимого окна. Таким образом, все дочерние представления автоматически наследуют резервного слой также.

Кроме того, Apple предлагает с помощью слоя резервного представлений в отличие от добавления нового `CALayer` как подуровень так, как система автоматически будет обрабатывать некоторые необходимые параметры (например, предусмотренные стандартом дисплей Retina).

Слой резервного можно включить, задав `WantsLayer` из `NSView` для `true` или внутри конструктора Interface Builder в разделе **инспектор эффектов представления** путем проверки **слоя анимации Core**:

[![](modern-cocoa-apps-images/content09.png "Инспектор эффектов представления")](modern-cocoa-apps-images/content09.png#lightbox)

<a name="Redrawing-Views-with-Layers" />

#### <a name="redrawing-views-with-layers"></a>Перерисовка представлений с помощью слоев

Еще один важный шаг, когда использование слоя резервного представлений в приложении Xamarin.Mac является установка `LayerContentsRedrawPolicy` из `NSView` для `OnSetNeedsDisplay` в `NSViewController`. Пример:

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set the content redraw policy
    View.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

Если это свойство не задано разработчиком, представление будет перерисовку при каждом изменении источника кадра, который не требуется для повышения производительности. С помощью этого свойства задано `OnSetNeedsDisplay` разработчик вручную будет устанавливать `NeedsDisplay` для `true` для принудительного содержимое для повторной отрисовки поверхности, тем не менее.

Когда представление помечен как "грязные", система проверяет `WantsUpdateLayer` свойства представления. Если он возвращает `true` то `UpdateLayer` вызывается метод, в противном случае `DrawRect` представления был вызван для обновления представления содержимого.

Apple имеет следующие рекомендации для обновления содержимого представления, при необходимости.

- Является предпочтительным для Apple с помощью `UpdateLater` через `DrawRect` каждый раз, когда возможно, так как он обеспечивает значительный выигрыш в производительности.
- Используйте тот же `layer.Contents` для элементов пользовательского интерфейса, которые выглядят.
- Apple, также является предпочтительным для разработчиков для составления их пользовательского интерфейса, такие как использование стандартных представлений `NSTextField`, еще раз когда это возможно.

Чтобы использовать `UpdateLayer`, создайте пользовательский класс для `NSView` и делает код более выглядеть следующим образом:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainView : NSView
    {
        #region Computed Properties
        public override bool WantsLayer {
            get { return true; }
        }

        public override bool WantsUpdateLayer {
            get { return true; }
        }
        #endregion

        #region Constructor
        public MainView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void DrawRect (CoreGraphics.CGRect dirtyRect)
        {
            base.DrawRect (dirtyRect);

        }

        public override void UpdateLayer ()
        {
            base.UpdateLayer ();

            // Draw view 
            Layer.BackgroundColor = NSColor.Red.CGColor;
        }
        #endregion
    }
}
```

<a name="Using-Modern-Drag-and-Drop" />

## <a name="using-modern-drag-and-drop"></a>С помощью современных перетаскивания

Чтобы представить современный интерфейс перетаскивания для пользователя, следует принять разработчику _перетаскивания или косяк_ в их приложении операции перетаскивания. Перетащите Flocking —, где каждый отдельный файл или изначально перетаскиваемый элемент отображается как отдельный элемент, который потоки устремляются (группа друг с другом под курсором с числом, равным числу элементов), как пользователь продолжает операцию перетаскивания.

Если пользователь завершает операцию перетаскивания, отдельным элементам Unflock и вернуть исходное расположение.

В следующем примере кода включает перетаскивания или косяк на представления:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainView : NSView, INSDraggingSource, INSDraggingDestination
    {
        #region Constructor
        public MainView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void MouseDragged (NSEvent theEvent)
        {
            // Create group of string to be dragged
            var string1 = new NSDraggingItem ((NSString)"Item 1");
            var string2 = new NSDraggingItem ((NSString)"Item 2");
            var string3 = new NSDraggingItem ((NSString)"Item 3");

            // Drag a cluster of items
            BeginDraggingSession (new [] { string1, string2, string3 }, theEvent, this);
        }
        #endregion
    }
}
```

Flocking эффект достигалось путем отправки каждого элемента, перетаскиваемого в `BeginDraggingSession` метод `NSView` как отдельный элемент в массиве.

При работе с `NSTableView` или `NSOutlineView`, использовать `PastboardWriterForRow` метод `NSTableViewDataSource` класса для запуска операции перетаскивание:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public class ContentsTableDataSource: NSTableViewDataSource
    {
        #region Constructors
        public ContentsTableDataSource ()
        {
        }
        #endregion

        #region Override Methods
        public override INSPasteboardWriting GetPasteboardWriterForRow (NSTableView tableView, nint row)
        {
            // Return required pasteboard writer
            ...
            
            // Pasteboard writer failed
            return null;
        }
        #endregion
    }
}
```

Это позволяет разработчику предоставить отдельному `NSDraggingItem` для каждого элемента в таблице, перетаскиваемого в отличие от старого метода `WriteRowsWith` , записать все строки как одну группу в буфер.

При работе с `NSCollectionViews`, снова использовать `PasteboardWriterForItemAt` метод, в отличие от `WriteItemsAt` метод при перетаскивании начинается.

Разработчик всегда следует располагать большими файлами монтажного стола. Знакомы с macOS Sierra, _обещания файл_ позволяют разработчику для размещения ссылки на заданное файлы монтажного стола, позже быть выполнено, когда пользователь завершает операцию перетаскивания с помощью нового `NSFilePromiseProvider` и `NSFilePromiseReceiver` классы.

<a name="Using-Modern-Event-Tracking" />

## <a name="using-modern-event-tracking"></a>Использование современных событий отслеживания

Для элемента пользовательского интерфейса (такие как `NSButton`), добавленный в заголовок или панели инструментов области, пользователь должен иметь возможность щелкните элемент, который будет активировать событие в обычном режиме (например, отображение всплывающего окна). Тем не менее так как элемент находится в области заголовка или панели инструментов, пользователь должен иметь возможность щелкните и перетащите элемент для перемещения окна также.

Чтобы сделать это в коде, создать пользовательский класс для элемента (такие как `NSButton`) и переопределить `MouseDown` событие следующим образом:

```csharp
public override void MouseDown (NSEvent theEvent)
{
    var shouldCallSuper = false;

    Window.TrackEventsMatching (NSEventMask.LeftMouseUp, 2000, NSRunLoop.NSRunLoopEventTracking, (NSEvent evt, ref bool stop) => {
        // Handle event as normal
        stop = true;
        shouldCallSuper = true;
    });

    Window.TrackEventsMatching(NSEventMask.LeftMouseDragged, 2000, NSRunLoop.NSRunLoopEventTracking, (NSEvent evt, ref bool stop) => {
        // Pass drag event to window
        stop = true;
        Window.PerformWindowDrag (evt);
    });

    // Call super to handle mousedown
    if (shouldCallSuper) {
        base.MouseDown (theEvent);
    }
}
```

Этот код использует `TrackEventsMatching` метод `NSWindow` присоединяемого элемента пользовательского интерфейса для перехвата `LeftMouseUp` и `LeftMouseDragged` события. Для `LeftMouseUp` событие, элемент пользовательского интерфейса, которая реагирует в обычном режиме. Для `LeftMouseDragged` событий, событие передается `NSWindow`в `PerformWindowDrag` метод для перемещения окна на экране.

Вызов `PerformWindowDrag` метод `NSWindow` класс предоставляет следующие преимущества:

- Позволяет для окна переместить, даже если зависание приложения (например, при обработке глубокого цикла).
- Переключение пространства будет работать должным образом.
- Панель пробелы будут отображаться в обычном режиме.
- Привязка окна и выравнивание работать в обычном режиме.

<a name="Using-Modern-Container-View-Controls" />

## <a name="using-modern-container-view-controls"></a>С помощью современных контейнерных элементов управления представления

macOS Sierra позволяет улучшить многих современных существующий контейнер представления элементов управления, доступных в предыдущей версии операционной системы.

<a name="Table View Enhancements" />

## <a name="table-view-enhancements"></a>Улучшения представления таблицы

Разработчику следует всегда использовать новый `NSView` на основе версии контейнерных элементов управления представления, такие как `NSTableView`. Пример:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public class ContentsTableDelegate : NSTableViewDelegate
    {
        #region Constructors
        public ContentsTableDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
        {
            // Build new view
            var view = new NSView ();
            ...

            // Return the view representing the item to display
            return view;
        }
        #endregion
    }
}
```

Это позволяет для выполнения пользовательских действий строки таблицы должны быть присоединены к заданной строки в таблице (например, проведение пальцем по экрану вправо, чтобы удалить строку). Чтобы включить это поведение, переопределите `RowActions` метод `NSTableViewDelegate`:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public class ContentsTableDelegate : NSTableViewDelegate
    {
        #region Constructors
        public ContentsTableDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
        {
            // Build new view
            var view = new NSView ();
            ...

            // Return the view representing the item to display
            return view;
        }

        public override NSTableViewRowAction [] RowActions (NSTableView tableView, nint row, NSTableRowActionEdge edge)
        {
            // Take action based on the edge
            if (edge == NSTableRowActionEdge.Trailing) {
                // Create row actions
                var editAction = NSTableViewRowAction.FromStyle (NSTableViewRowActionStyle.Regular, "Edit", (action, rowNum) => {
                    // Handle row being edited
                    ...
                });

                var deleteAction = NSTableViewRowAction.FromStyle (NSTableViewRowActionStyle.Destructive, "Delete", (action, rowNum) => {
                    // Handle row being deleted
                    ...
                });

                // Return actions
                return new [] { editAction, deleteAction };
            } else {
                // No matching actions
                return null;
            }
        }
        #endregion
    }
}
```

Статический `NSTableViewRowAction.FromStyle` используется для создания нового действия строки таблицы следующие стили:

- `Regular` — Выполняет стандартного действия без уничтожения данных, например редактирование содержимое строки.
- `Destructive` — Выполняет разрушительные действия, например удаление строки из таблицы. Эти действия будут передаваться с красным фоном.

<a name="Scroll-View-Enhancements" />

## <a name="scroll-view-enhancements"></a>Улучшения представления прокрутки 

При использовании представления прокрутки (`NSScrollView`) непосредственно или как часть другого элемента управления (такие как `NSTableView`), содержимое представления прокрутки можно сдвигать ползунок в области заголовка и панели инструментов в приложения Xamarin.Mac с помощью современных поиска и представления.

Таким образом первый элемент в области содержимого представления могут быть частично закрыты области заголовка и панели инструментов.

Чтобы исправить эту проблему, Apple добавлено два новых свойства для `NSScrollView` класса:

- `ContentInsets` -Позволяет разработчику предоставить `NSEdgeInsets` объект, определяющий смещение, которое будет применяться к верхней части представления.
- `AutomaticallyAdjustsContentInsets` Если `true` представление прокрутки автоматически обработает `ContentInsets` для разработчиков.

С помощью `ContentInsets` разработчика можно настроить начало прокрутки представления, чтобы разрешить для включения "Стандартные", такие как:

- Индикатор сортировки, показанное в приложение "Почта".
- Поле поиска.
- Кнопка обновления или обновления.

<a name="Auto-Layout-and-Localization-in-Modern-Apps" />

## <a name="auto-layout-and-localization-in-modern-apps"></a>Автоматический макет и локализации в современных приложениях

Apple включила несколько технологий в Xcode, позволяют разработчикам легко создавать приложения интернационализированных macOS. Xcode теперь позволяет разработчику для разделения текста пользовательского интерфейса, начиная с проектирования пользовательского интерфейса приложения в его файлы раскадровки и предоставляет средства для поддержания такое разделение, если он изменяется.

Дополнительные сведения см. в разделе Apple [интернационализации и руководство по локализации](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html).

<a name="Implementing-Base-Internationalization" />

### <a name="implementing-base-internationalization"></a>Реализация базового интернационализации 

Путем реализации базового интернационализации, разработчик может предоставить единый файл раскадровки для представления пользовательского интерфейса приложения и отделить все строки пользовательского интерфейса. 

Когда разработчик создает исходный файл раскадровки (или файлы), которые определяют пользовательский интерфейс приложения, они будут собраны в интернационализации Base (язык, который говорит разработчик).

Затем разработчик можно экспортировать, локализации и интернационализации базовых строк (в структуры пользовательского интерфейса раскадровки), которые могут быть переведены на несколько языков.

Позднее можно импортировать эти локализации и Xcode создаст файлы строки, зависящей от языка для раскадровки.

<a name="Implementing-Auto-Layout-to-Support-Localization" />

### <a name="implementing-auto-layout-to-support-localization"></a>Реализация автоматический макет для поддержки локализации

Поскольку локализованные версии строкового значения могут иметь самые разные размеры и/или направление чтения, разработчик должен использовать автоматический макет позиция и размер пользовательского интерфейса приложения в файл раскадровки.

Apple предложить следующим образом:

- **Удалите ограничения фиксированной ширины** -все текстовые представления должны для изменения размера на основе их содержимого. Фиксированная ширина представление может обрезать их содержимое на определенных языках.
- **Использовать внутренние размеры содержимого** — по умолчанию текстового представления будет Авторазмер в соответствии с содержимым. Для текстового представления, не размеры, выберите их в конструктора Interface Builder, затем выберите **изменить** > **размер на размещение содержимого**.
- **Применить начальные и конечные атрибуты** — так, как можно изменить направление текста на языке пользователя, используйте новый `Leading` и `Trailing` атрибутов ограничения, в отличие от существующего `Right` и `Left` атрибуты. `Leading` и `Trailing` автоматически настроит зависимости от направления языков.
- **ПИН-код представлений для представления рядом** -это позволяет представлений для положение и размер представлений о их изменении в ответ на языке, выбранном.
- **Не устанавливайте Windows минимум и/или максимальные размеры** -Разрешить Windows, чтобы изменить размер, как на языке, выбранном изменяет размер области их содержимого.
- **Проверить изменения макета постоянно** — во время разработки в приложении следует постоянно тестировать на разных языках. См. в разделе Apple [Testing Your международного приложения](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/TestingYourInternationalApp/TestingYourInternationalApp.html#//apple_ref/doc/uid/10000171i-CH7-SW1) документации для получения дополнительных сведений.
- **Использовать NSStackViews для ПИН-код представления вместе**  -  `NSStackViews` позволяет их содержимое, shift и увеличение предсказуемыми и содержимое, изменить размер в зависимости от выбранного языка.

<a name="Localizing-in-Xcodes-Interface-Builder" />

### <a name="localizing-in-xcodes-interface-builder"></a>Локализация в Interface Builder в Xcode

Apple предоставляет несколько функций конструктора Interface Builder, разработчик может использовать при разработке или изменении пользовательского интерфейса приложения для поддержки локализации. **Направление текста** раздел **инспекторе атрибутов** разработчик может предоставить подсказки как направление следует использовать и обновить на select текстового представления (такие как `NSTextField`):

[![](modern-cocoa-apps-images/content10.png "Параметры направление текста")](modern-cocoa-apps-images/content10.png#lightbox)

Существует три возможных значения для **направление текста**:

- **Естественное** -макет на основе строки для элемента управления.
- **Слева направо** -макет всегда вынужден слева направо.
- **Справа налево** -всегда принудительно макет справа налево.

Существует два возможных значения для **макета**:

- **Слева направо** -макет всегда слева направо.
- **Справа налево** -всегда является макет справа налево.

Обычно они не должен изменяться Если конкретное направление не требуется.

**Зеркальный** свойство сообщает системе перевернуть свойства определенного элемента управления (например, положение изображения ячейки). Он имеет три возможных значения:

- **Автоматически** -позиция автоматически изменяется в зависимости от направления на языке, выбранном.
- **В интерфейсе для слева вправо** -положение изменяются только в право слева на основе языков.
- **Никогда не** -позиция никогда не изменится.

Если разработчик указал **Center**, **ширине** или **полный** выравнивания содержимого текстового представления, они никогда не будет должно быть перевернуто зависимости от выбранного языка.

Прежде чем macOS Sierra элементов управления, созданных в коде будет нельзя применять зеркалирование автоматически. Разработчик должен был использовать код, аналогичный следующему для обработки зеркального отображения:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Setting a button's mirroring based on the layout direction
    var button = new NSButton ();
    if (button.UserInterfaceLayoutDirection == NSUserInterfaceLayoutDirection.LeftToRight) {
        button.Alignment = NSTextAlignment.Right;
        button.ImagePosition = NSCellImagePosition.ImageLeft;
    } else {
        button.Alignment = NSTextAlignment.Left;
        button.ImagePosition = NSCellImagePosition.ImageRight;
    }
}
```

Где `Alignment` и `ImagePosition` задаются на основе `UserInterfaceLayoutDirection` элемента управления.

macOS Sierra добавляет несколько новых удобных конструкторов (через статический `CreateButton` метод), которые принимают несколько параметров (например, заголовок, изображение и действия) и автоматически отразит правильно. Пример:

```csharp
var button2 = NSButton.CreateButton (myTitle, myImage, () => {
    // Take action when the button is pressed
    ...
});
```

<a name="Using-System-Appearances" />

## <a name="using-system-appearances"></a>С помощью внешний вид системы

Приложения для современных macOS можно внедрять новый темно-внешний интерфейс, который хорошо подходит для приложений образ создания, редактирования или презентации:

[![](modern-cocoa-apps-images/content11.png "Пример пользовательского интерфейса окна темно-Mac")](modern-cocoa-apps-images/content11.png#lightbox)

Это можно сделать путем добавления одной строки кода, прежде чем будет предоставлен окна. Пример:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacModern
{
    public partial class ViewController : NSViewController
    {
        ...
    
        #region Override Methods
        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            // Apply the Dark Interface Appearance
            View.Window.Appearance = NSAppearance.GetAppearance (NSAppearance.NameVibrantDark);

            ...
        }
        #endregion
    }
}
```

Статический `GetAppearance` метод `NSAppearance` класс используется для получения именованных внешний от системы (в данном случае `NSAppearance.NameVibrantDark`).

Apple имеет следующие рекомендации по использованию системы внешний вид:

- Использовать именованные цвета вместо жестко заданных значений (таких как `LabelColor` и `SelectedControlColor`).
- Используйте стиль стандартного элемента управления системы, где это возможно.

Приложение macOS, которое использует внешний системы автоматически будет работать для пользователей, которые включили специальные возможности в приложении системные настройки. В результате Apple предполагает, что разработчику следует всегда использовать внешний вид системы в своих приложениях на macOS.

<a name="Designing-UIs-with-Storyboards" />

## <a name="designing-uis-with-storyboards"></a>Разработка пользовательских интерфейсов с помощью Storyboard

Раскадровки позволяют разработчику не только для разработки, что поток отдельных элементов интерфейса пользователя приложения, но до визуализации и разработать пользовательский Интерфейс, так и для иерархии заданные элементы.

Контроллеры позволяют разработчику собирает элементы в единицу композиции и абстрактным Segues и удалить типичный «связывающим кода» для перемещения по иерархии представления требуется:

[![](modern-cocoa-apps-images/content12.png "Изменение пользовательского интерфейса конструктора Interface Builder")](modern-cocoa-apps-images/content12.png#lightbox)

Дополнительные сведения см. в разделе наших [введение в раскадровки](~/mac/platform/storyboards/index.md) документации.

Существует несколько экземпляров, где заданного сцены, определенные в раскадровке потребует данные из предыдущего кадра в иерархию представлений. Apple имеет следующие рекомендации для передачи данных между сценами:

- Dependancies данных следует всегда вызывать каскадные действия перемещается вниз по иерархии.
- Избегайте жесткого задания структурные dependancies пользовательского интерфейса, как это ограничивает гибкость пользовательского интерфейса.
- Используйте C# интерфейсы для предоставления dependancies универсальных данных.

Контроллер представления, выступающего в качестве источника переход, можно переопределить `PrepareForSegue` метод и выполните инициализацию необходимы (например, передача данных), прежде чем переход выполняется для отображения целевой контроллер представления. Пример:

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on Segue ID
    switch (segue.Identifier) {
    case "MyNamedSegue":
        // Prepare for the segue to happen
        ...
        break;
    }
}
```

Дополнительные сведения см. в разделе наших [Segues](~/mac/platform/storyboards/indepth.md#Segues) документации.

<a name="Propagating-Actions" />

## <a name="propagating-actions"></a>Распространение действий

Основываясь на дизайн приложения macOS, может существовать раз, когда обработчик рекомендации для действия в элемент управления пользовательского интерфейса может быть в другом месте в иерархии пользовательского интерфейса. Это обычно происходит, меню и пунктов меню, live в свои собственные сцены, отдельно от остальной части пользовательского интерфейса приложения.

Чтобы решить эту проблему, разработчик можно создать настраиваемое действие и передать действия вверх по цепочке респондент. Дополнительные сведения см. в разделе наших [работа с окно Custom Actions](~/mac/user-interface/menu.md) документации.

<a name="Modern-Mac-Features" />

## <a name="modern-mac-features"></a>Возможности современных Mac

Apple включает в себя несколько функций пользовательского интерфейса в macOS Sierra, разработчик может использовать все преимущества на платформе Mac, такие как:

- **NSUserActivity** -это позволяет приложению для описания действия, которое в настоящий момент участвует пользователь. `NSUserActivity` изначально был создан для поддержки передачи, где действие запуска на одном из устройств пользователя может отгрузку и продолжение на другом устройстве. `NSUserActivity` Таким образом, работает соответствующим образом в macOS, так как он делает в iOS см. статью наших [введение переходной](~/ios/platform/handoff.md) iOS документации для получения дополнительных сведений.
- **Siri на Mac** -Siri использует текущее действие (`NSUserActivity`) для поддержки контекста команды, пользователь может выдавать.
- **Состояние восстановления** — когда пользователь завершает работу приложения в Mac OS, а потом перезапуска его, приложение будет автоматически возвращается в предыдущее состояние. Разработчик может использовать API восстановления состояния для кодирования и восстановить Переходные состояния пользовательского интерфейса перед отображением пользовательского интерфейса для пользователя. Если приложение является `NSDocument` зависимости, восстановление состояния обрабатывается автоматически. Чтобы включить восстановление состояния для отличных`NSDocument` зависимости приложения задать `Restorable` из `NSWindow` класс `true`.
- **Документы в облаке** -до macOS Sierra, приложению должны были явно согласиться на работе с документами в пользователя iCloud Drive. В macOS Sierra пользователь в **Desktop** и **документов** папки могут быть синхронизированы с их iCloud Drive автоматически системой. В результате локальные копии документов могут быть удалены, чтобы освободить место на компьютере пользователя. `NSDocument` на основе приложений автоматически обработает это изменение. Все другие типы приложений потребуется использовать `NSFileCoordinator` синхронизировать чтения и записи документов.

<a name="Summary" />

## <a name="summary"></a>Сводка

В этой статье подробно рассматривается несколько советов, возможности и методы, разработчик может использовать для создания приложения на основе современных macOS в Xamarin.Mac.



## <a name="related-links"></a>Связанные ссылки

- [Примеры macOS](https://developer.xamarin.com/samples/mac/)
