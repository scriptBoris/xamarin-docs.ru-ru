---
title: Построение macOS современных приложений
description: В этой статье рассматривается несколько советов, функции и методы, разработчик может использовать для выполнения сборки приложения в Xamarin.Mac современных macOS.
ms.prod: xamarin
ms.assetid: F20EE590-246E-40EB-B309-D9D8C090C7F1
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 4eb4ff4a9e4784d816e2cbe8734e0422573cad92
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30787783"
---
# <a name="building-modern-macos-apps"></a>Построение macOS современных приложений

_В этой статье рассматривается несколько советов, функции и методы, разработчик может использовать для выполнения сборки приложения в Xamarin.Mac современных macOS._

<a name="Building-Modern-Looks-with-Modern-Views" />

## <a name="building-modern-looks-with-modern-views"></a>Создание современных выглядит с современной представления

Современный вид будет включать современный внешний окна и панели инструментов например примера приложения, показано ниже:

[![](modern-cocoa-apps-images/content08.png "Примером современный пользовательский Интерфейс приложения Mac")](modern-cocoa-apps-images/content08.png#lightbox)

<a name="Enabling-Full-Sized-Content-Views" />

### <a name="enabling-full-sized-content-views"></a>Включение полного размера содержимого представления

Чтобы добиться этого выглядит в приложении Xamarin.Mac, будут использовать разработчик _полный размер содержимого представления_, то есть содержимое распространяется в областях средства и строка заголовка и будет автоматически размытое по macOS.

Чтобы включить эту функцию в коде, создайте настраиваемый класс `NSWindowController` и сделать его выглядеть следующим образом:

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

Также можно включить эту функцию в построителе интерфейса в Xcode, выбрав окна и проверка **полного размера представления содержимого**:

[![](modern-cocoa-apps-images/content01.png "Изменение основного раскадровки в построителе интерфейса в Xcode")](modern-cocoa-apps-images/content01.png#lightbox)

При использовании полный размер представления содержимого, разработчик может потребоваться смещение содержимое, расположенное под области панели заголовка и средства, чтобы не слайд конкретного содержимого (например, метки), находящихся на них.

Усложнить эту проблему, заголовок и панели инструментов области может иметь Динамическая высота на основе действия, который в настоящее время выполняет пользователь, пользователь установил macOS и/или оборудовании Mac, что приложение выполняется на версию.

В результате просто жесткого кодирования смещение при размещении пользовательского интерфейса невозможно. Разработчику необходимо динамического подхода.

Apple включила [ключ-значение наблюдаемый](~/mac/app-fundamentals/databinding.md#Observing_Value_Changes) `ContentLayoutRect` свойство `NSWindow` класса для получения текущей области содержимого в коде. Разработчик может использовать это значение вручную разместить необходимые элементы при изменении области содержимого.

Лучшим решением является использование автоматический макет и размер классов для размещения элементов пользовательского интерфейса в коде или в интерфейс построителя.

Код, как в следующем примере используется для размещения элементов пользовательского интерфейса с помощью AutoLayout и размер классов в контроллере представления приложения:

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

Этот код создает хранилище для верхнего ограничения, которая будет применяться к метке (`ItemTitle`) чтобы убедиться, что он не пропущен в области заголовка и панель инструментов:

```csharp
public NSLayoutConstraint topConstraint { get; set; }
```

Путем переопределения View Controller `UpdateViewConstraints` метод, мог проверить, если необходимое ограничение уже выполнена и при необходимости создайте его.

Если новое ограничение должен быть построен, `ContentLayoutGuide` свойства окна элемента управления, который должен быть ограничен доступ и привести `NSLayoutGuide`:

```csharp
var contentLayoutGuide = ItemTitle.Window?.ContentLayoutGuide as NSLayoutGuide;
```

Свойство TopAnchor `NSLayoutGuide` доступна и если он доступен, он используется для создания нового ограничения с требуемое смещения и сделана активной для применения нового ограничения:

```csharp
// Assemble constraint and activate it
topConstraint = topAnchor.ConstraintEqualToAnchor (topAnchor, 20);
topConstraint.Active = true;
```

<a name="Enabling-Streamlined-Toolbars" />

### <a name="enabling-streamlined-toolbars"></a>Включение упрощенное панелей инструментов

Обычный macOS окна включает стандартного выходящих строки заголовка в верхней части окна. Если окно также содержит панель инструментов, которое будет отображаться в этой области заголовка:

[![](modern-cocoa-apps-images/content02.png "Стандартная панель инструментов Mac")](modern-cocoa-apps-images/content02.png#lightbox)

Когда исчезнет с помощью упрощенное инструментов области заголовка и перемещает панель инструментов вверх в позицию в строке заголовка, в строках с кнопками, закрыть окно, свертывания и развертывания:

[![](modern-cocoa-apps-images/content03.png "Упрощенная панель инструментов Mac")](modern-cocoa-apps-images/content03.png#lightbox)

Упрощенное панели инструментов включен, переопределив `ViewWillAppear` метод `NSViewController` и делая выглядеть следующим образом:

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Enable streamlined Toolbars
    View.Window.TitleVisibility = NSWindowTitleVisibility.Hidden;
}
```

Этот эффект обычно используется для _коробки приложений_ (одно окно приложения), например карты, календарь, заметки и системными настройками. 

<a name="Using-Accessory-View-Controllers" />

### <a name="using-accessory-view-controllers"></a>С помощью контроллеров представления периферийных устройств

В зависимости от структуры приложения разработчик также может возникнуть в дополнение к строке заголовка области с контроллером представления аксессуар появившемся вправо под областью панель заголовка и инструментов для предоставления контекстной управляет пользователю на основе действий, они являются в настоящее время участвует в:

[![](modern-cocoa-apps-images/content04.png "Пример контроллера представления аксессуар")](modern-cocoa-apps-images/content04.png#lightbox)

Представления аксессуар контроллера будет автоматически приводит к потере четкости и размеры в системе, не привлекая разработчиков.

Чтобы добавить контроллер представления аксессуар, выполните следующие действия:

1. В **обозревателе решений** дважды щелкните файл `Main.storyboard`, чтобы открыть его для редактирования.
2. Перетащите **настраиваемый контроллер представление** в иерархию окна: 

    [![](modern-cocoa-apps-images/content05.png "Добавление нового контроллера пользовательского представления")](modern-cocoa-apps-images/content05.png#lightbox)
3. Макета аксессуаров представление пользовательского интерфейса: 

    [![](modern-cocoa-apps-images/content06.png "Проектирование нового представления")](modern-cocoa-apps-images/content06.png#lightbox)
4. Предоставлять аксессуаров представления в виде **розетки** и любые другие **действия** или **торговцам** для своего пользовательского интерфейса: 

    [![](modern-cocoa-apps-images/content07.png "Добавление необходимых розетки")](modern-cocoa-apps-images/content07.png#lightbox)
5. Сохраните изменения.
6. Вернитесь в Visual Studio для Mac синхронизировать изменения.

Изменить `NSWindowController` и сделать его выглядеть следующим образом:

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

Ключевые аспекты этого кода, где задано представление настраиваемого представления, определенные в построителе интерфейса и в виде **розетки**:

```csharp
accessoryView.View = AccessoryViewGoBar;
```

И `LayoutAttribute` , определяющий _где_ будет отображаться метод:

```csharp
accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
```

Так как теперь macOS полностью локализован, `Left` и `Right` `NSLayoutAttribute` свойства являются устаревшими и должна быть заменена `Leading` и `Trailing`.

<a name="Using-Tabbed-Windows" />

### <a name="using-tabbed-windows"></a>С помощью окон с вкладками

Кроме того система macOS может добавить контроллеров представления аксессуар окно приложения. Например, чтобы создать Windows с вкладками, где несколько окон приложений объединяются в одно окно виртуального:

[![](modern-cocoa-apps-images/content08.png "Пример окна с вкладками Mac")](modern-cocoa-apps-images/content08.png#lightbox)

Как правило разработчику требуется выполнять ограниченный действие использование окон с вкладками в свои приложения Xamarin.Mac, система будет обрабатывать их автоматически, как показано ниже:

- Windows будет автоматически с вкладками, когда `OrderFront` вызывается метод.
- Windows будет автоматически Untabbed при `OrderOut` вызывается метод.
- В коде, по-прежнему считаются «visible» все окна с вкладками Однако не переднее вкладки скрыты системой с помощью CoreGraphics.
- Используйте `TabbingIdentifier` свойство `NSWindow` для группы Windows в вкладок.
- Если это `NSDocument` зависимости приложения, некоторые из этих компонентов будут включены автоматически (например, "плюс", добавляемый на панель вкладок) без вмешательства разработчика.
- Не -`NSDocument` приложений можно включить кнопку «плюс», в группу вкладок, чтобы добавить новый документ, переопределив `GetNewWindowForTab` метод `NSWindowsController`.

Объединяя все фрагменты, `AppDelegate` приложения, необходимо использовать системы на основе Windows с вкладками может выглядеть следующим образом:

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

Где `NewDocumentNumber` свойство продолжает отслеживать число новых документов, создаваемых и `NewDocument` метод создает новый документ и отображает его.

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

Где статический `App` свойство существует сочетание клавиш, чтобы получить `AppDelegate`. `SetDefaultDocumentTitle` Метод задает новый заголовок документов на основе числа новых документов, создаваемых.

Следующий код сообщает macOS приложение предпочтительную вкладках и предоставляет строку, которая позволяет разделить на вкладках Windows приложения:

```csharp
// Prefer Tabbed Windows
Window.TabbingMode = NSWindowTabbingMode.Preferred;
Window.TabbingIdentifier = "Main";
```

И следующий метод переопределения добавляет "плюс", будет создан новый документ, когда пользователь щелкает мышью панель вкладок:

```csharp
public override void GetNewWindowForTab (NSObject sender)
{
    // Ask app to open a new document window
    App.NewDocument (this);
}
```

<a name="Using-Core-Animation" />

### <a name="using-core-animation"></a>С помощью основных анимации

Анимация основных компонентов — механизм визуализации высокой энергопотреблением графики, встроенный в macOS. Основной анимации был оптимизирован для использования преимуществ графического процессора (единица обработки графики) доступны в современных macOS оборудования, в отличие от графических операций на ЦП, который может замедлить работу компьютера под управлением.

`CALayer`, Предоставляемые основной анимации, можно использовать для задач, таких как простой и гибкий прокрутки и анимации. Пользовательский интерфейс приложения должны быть составлены из нескольких представлений и слои, чтобы полностью использовать преимущества основных анимации.

Объект `CALayer` объект предоставляет несколько свойств, которые позволяют разработчикам управлять, чем представлено на экране для пользователя такие как:

- `Content` — Может быть `NSImage` или `CGImage` , предоставляющий содержимое слоя.
- `BackgroundColor` — Задает цвет фона слоя как `CGColor`
- `BorderWidth` — Задает ширину границы.
- `BorderColor` — Задает цвет границы.

Использование основных графических изображений в пользовательском Интерфейсе приложения, необходимо использовать _слой резервного_ представления, которые Apple предполагает, что разработчик всегда следует включить в представление содержимого окна. Таким образом, все дочерние представления автоматически наследуют резервное слой также.

Кроме того, Apple предполагает использование представлений резервного слоя в отличие от добавления нового `CALayer` как уровне, так как система автоматически будет обрабатывать некоторые необходимые параметры (например, предусмотренного дисплеем Retina).

Резервное слоев можно включить, установив `WantsLayer` из `NSView` для `true` или внутри построитель интерфейс Xcode в разделе **инспектора эффекты представление** , установив **слоя анимации Core**:

[![](modern-cocoa-apps-images/content09.png "Инспектор эффекты представления")](modern-cocoa-apps-images/content09.png#lightbox)

<a name="Redrawing-Views-with-Layers" />

#### <a name="redrawing-views-with-layers"></a>Перерисовку представления со слоями

Следующий важный шаг, если с помощью представлений резервного слоя в приложении Xamarin.Mac — параметр `LayerContentsRedrawPolicy` из `NSView` для `OnSetNeedsDisplay` в `NSViewController`. Пример:

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set the content redraw policy
    View.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

Если это свойство не задано разработчиком, перерисовывается представления при каждом изменении ее источника кадра, который не требуется для повышения производительности. Это свойство равным `OnSetNeedsDisplay` разработчик вручную потребуется задать `NeedsDisplay` для `true` для принудительного перерисовка, однако содержимое.

Когда представление помечен как "грязный", система проверяет `WantsUpdateLayer` свойства представления. Если он возвращает `true` то `UpdateLayer` вызывается метод, в противном случае `DrawRect` метод представления вызывается для обновления содержимого этого представления.

Apple имеет следующие рекомендации по обновлению содержимого представления, при необходимости.

- Является предпочтительным Apple с помощью `UpdateLater` над `DrawRect` каждый раз, когда возможно, как оно обеспечивает значительный выигрыш в производительности.
- Используйте тот же `layer.Contents` для элементов пользовательского интерфейса, которые похожи.
- Apple, также является предпочтительным для разработчиков для создания их пользовательского интерфейса с помощью стандартных представлений, таких как `NSTextField`, еще раз когда это возможно.

Для использования `UpdateLayer`, создайте пользовательский класс для `NSView` и делает код более выглядеть следующим образом:

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

Чтобы создать процедуру современных операции перетаскивания для пользователя, разработчику необходимо принимать _или перетащите косяк_ в их приложении операции перетаскивания. Перетащите Flocking — где каждого файла по отдельности или изначально перетаскиваемый элемент отображается как отдельный элемент, который потоки устремляются (группа друг с другом под курсором с числом, равным числу элементов), как пользователь по-прежнему операции перетаскивания.

Если пользователь завершает операцию перетаскивания, отдельные элементы Unflock и вернуться в исходное расположение.

В следующем примере кода обеспечивает перетаскивания или косяк на пользовательское представление:

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

Эффект flocking было достигнуто путем отправки каждого элемента, перетаскиваемого в `BeginDraggingSession` метод `NSView` как отдельный элемент в массиве.

При работе с `NSTableView` или `NSOutlineView`, используйте `PastboardWriterForRow` метод `NSTableViewDataSource` класса для запуска операции перетаскивание:

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

Это позволяет разработчику предоставить отдельному `NSDraggingItem` для каждого элемента в таблице, перетаскиваемое в отличие от старого метода `WriteRowsWith` все строки, запись как одну группу в буфер.

При работе с `NSCollectionViews`, снова использовать `PasteboardWriterForItemAt` метода, в отличие от `WriteItemsAt` начинает метод при перетаскивании.

Разработчик всегда следует располагать большими файлами монтажного стола. Новые macOS Сьерра, _обещания файл_ позволяют разработчику поместить ссылки на заданное файлы монтажного стола, будут выполнены позднее, когда пользователь завершает операцию перетаскивания, с использованием нового `NSFilePromiseProvider` и `NSFilePromiseReceiver` классы.

<a name="Using-Modern-Event-Tracking" />

## <a name="using-modern-event-tracking"></a>С помощью современных события отслеживания

Для элемента пользовательского интерфейса (например, `NSButton`), будет добавлен в область заголовка или панели инструментов, пользователь должен иметь возможность выберите элемент и его вызывать события в обычном режиме (например, отображение всплывающего окна). Тем не менее поскольку элемент в области заголовка или панели инструментов, пользователь должен иметь права, щелкните и перетащите элемент, чтобы переместить окно также.

Для этого в коде, создайте пользовательский класс, для элемента (например, `NSButton`) и переопределить `MouseDown` событие следующим образом:

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

Этот код использует `TrackEventsMatching` метод `NSWindow` присоединяемого элемент пользовательского интерфейса для перехвата `LeftMouseUp` и `LeftMouseDragged` события. Для `LeftMouseUp` событие, элемент пользовательского интерфейса реагирует обычным образом. Для `LeftMouseDragged` события, событие передается `NSWindow` `PerformWindowDrag` метод для перемещения окна на экране.

Вызов `PerformWindowDrag` метод `NSWindow` класс предоставляет следующие преимущества:

- Позволяет для окна переместить, даже если зависание приложения (например, при обработке сложного цикла).
- Переключение пространства будет работать должным образом.
- Панель пробелы будут отображаться в обычном режиме.
- Окно Привязка и выравнивание работать в обычном режиме.

<a name="Using-Modern-Container-View-Controls" />

## <a name="using-modern-container-view-controls"></a>С помощью современных контейнерных элементов управления представления

macOS Сьерра предоставляет множество улучшений современных существующий контейнер представление элементов управления, доступных в предыдущей версии операционной системы.

<a name="Table View Enhancements" />

## <a name="table-view-enhancements"></a>Улучшения представления таблицы

Разработчику следует всегда использовать новый `NSView` на основе версии представления контейнерных элементов управления, таких как `NSTableView`. Пример:

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

Это позволяет для пользовательских действий строки таблицы для присоединения к данной строки в таблице (например, для считывания вправо, чтобы удалить строку). Чтобы включить это поведение, необходимо переопределить `RowActions` метод `NSTableViewDelegate`:

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

Статический `NSTableViewRowAction.FromStyle` используется для создания нового действия строк таблицы следующие стили:

- `Regular` -Выполняет стандартное действие неразрушающее, такие как изменение содержимого строк.
- `Destructive` -Выполняет разрушительных действия, например удаление строки из таблицы. Эти действия будут передаваться с красным фоном.

<a name="Scroll-View-Enhancements" />

## <a name="scroll-view-enhancements"></a>Улучшения представления прокрутки 

При использовании представлений прокрутки (`NSScrollView`) непосредственно или как часть другого элемента управления (например, `NSTableView`), содержимое представления прокрутки можно сдвигать ползунок в области заголовка и панель инструментов в Xamarin.Mac приложения, с помощью современных поиска и представления.

В результате первого элемента в области просмотра прокрутки содержимого могут быть частично закрыты области заголовка и панели инструментов.

Чтобы устранить эту проблему, два новых свойства для добавил Apple `NSScrollView` класса:

- `ContentInsets` -Разработчик может предоставить `NSEdgeInsets` объект, определяющий смещение, которое будет применяться к верхней части представления прокрутки.
- `AutomaticallyAdjustsContentInsets` -Если `true` представление прокрутки автоматически будет обрабатывать `ContentInsets` для разработчиков.

С помощью `ContentInsets` разработчик можно настроить начало прокрутки представления, чтобы разрешить для включения аксессуаров, таких как:

- Индикатор сортировки подобно показанному в приложении "Почта".
- Поле поиска.
- Кнопка обновления или обновления.

<a name="Auto-Layout-and-Localization-in-Modern-Apps" />

## <a name="auto-layout-and-localization-in-modern-apps"></a>Автоматический макет и локализации в современных приложений

В Xcode, разработчик может легко создать приложение международного macOS Apple включила нескольких технологий. Xcode теперь позволяет разработчику для разделения текста пользовательского интерфейса из конструктора пользовательского интерфейса приложения в его файлами раскадровки и предоставляет средства для поддерживать такое разделение, в случае изменения пользовательского интерфейса.

Дополнительные сведения см. в разделе Apple [Интернационализация и руководство по локализации](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html).

<a name="Implementing-Base-Internationalization" />

### <a name="implementing-base-internationalization"></a>Реализация базового международного использования 

Путем реализации интернационализации базы, разработчик может предоставить один файл раскадровки для представления пользовательского интерфейса приложения и отделить все строки пользовательского интерфейса. 

Если разработчик создает исходный файл раскадровки (или файлы), которые определяют пользовательский интерфейс приложения, они будут строиться интернационализации Base (язык говорит разработчик).

Затем разработчик можно экспортировать локализации и строки интернационализации базы (на конструктор пользовательского интерфейса раскадровки), которые могут быть переведены на несколько языков.

Позже можно импортировать эти локализации и Xcode создаст файлы языку строка для данной раскадровки.

<a name="Implementing-Auto-Layout-to-Support-Localization" />

### <a name="implementing-auto-layout-to-support-localization"></a>Реализация автоматический макет для поддержки локализации

Поскольку локализованные версии строки значения могут иметь самые разные размеры и/или направление чтения, разработчик должен использовать автоматический макет позиция и размер пользовательского интерфейса приложения в файл раскадровки.

Apple предложить следующим образом:

- **Удалите ограничения фиксированной ширины** -все представления, основанные на тексте должно быть разрешено изменение размера по их содержимому. Фиксированная ширина представления может обрезать их содержимого в конкретных языков.
- **Использовать встроенный размеры содержимого** — по умолчанию текстового представления будет Автоподбор в соответствии с содержимым. Для текстового представления, не размеры, выберите их в построителе интерфейс Xcode а затем выберите **изменить** > **размер для размещение содержимого**.
- **Применить начальные и конечные атрибуты** — так, как можно изменить направление текста в зависимости от языка пользователя, используйте новый `Leading` и `Trailing` атрибутов ограничения, в отличие от существующих `Right` и `Left` атрибуты. `Leading` и `Trailing` автоматически изменяется в зависимости от направления языков.
- **Представления ПИН-код для представления смежные** -это позволяет представлений для измените положение и размер при изменении представления вокруг них в ответ на языке, выбранном.
- **Не задано Windows минимальный и максимальный размер** -разрешить окна для изменения размера, как на языке, выбранном изменяет их содержимого области.
- **Протестировать изменения макета постоянно** — во время разработки в приложение необходимо протестировать постоянно на разных языках. В разделе Apple [тестирование Your международного приложения](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/TestingYourInternationalApp/TestingYourInternationalApp.html#//apple_ref/doc/uid/10000171i-CH7-SW1) документации для получения дополнительных сведений.
- **Используйте NSStackViews для представления ПИН-код вместе**  -  `NSStackViews` позволяет их содержимое для увеличения предсказуемыми, удерживая клавишу shift и содержимым изменить размер в зависимости от выбранного языка.

<a name="Localizing-in-Xcodes-Interface-Builder" />

### <a name="localizing-in-xcodes-interface-builder"></a>Локализация в построителе интерфейса в Xcode

Apple предоставляет несколько функций в построителе интерфейса в Xcode, разработчик может использовать при разработке или изменении пользовательского интерфейса приложения для поддержки локализации. **Направление текста** раздел **инспектора атрибут** разработчик может предоставить подсказки на методы направление следует использовать и обновления на выбор текстового представления (такие как `NSTextField`):

[![](modern-cocoa-apps-images/content10.png "Параметры направление текста")](modern-cocoa-apps-images/content10.png#lightbox)

Существует три возможных значения для **направление текста**:

- **Естественное** -макет основан на строке, назначенный элементу управления.
- **Слева направо** -макет всегда принудительно слева направо.
- **Справа налево** -всегда принудительно макет справа налево.

Существует два возможных значения **макета**:

- **Слева направо** -макет всегда слева направо.
- **Справа налево** -макет всегда находится справа налево.

Обычно они не должны изменяться, если не требуется особой взаимосвязи.

**Зеркальный** свойство сообщает системе, чтобы отразить свойств конкретного элемента управления (например положение изображения ячейки). Он имеет три возможных значения:

- **Автоматически** -позиция автоматически изменяются в зависимости от направления выбранного языка.
- **Право на слева интерфейса** -позиция будет изменен только в правом на основе налево.
- **Никогда не** -позиция не изменится.

Если разработчик указал **Center**, **ширине** или **полного** выравнивание содержимого текстового представления, они никогда не будет отраженных зависимости от выбранного языка.

Перед macOS Сьерра элементов управления, созданных в коде должен быть зеркальное отображение не автоматически. Разработчик должен был обрабатывать зеркального отображения с помощью кода следующим образом:

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

macOS Сьерра добавляет несколько новых удобных конструкторов (через статический `CreateButton` метод), которые принимают несколько параметров (например, заголовок, изображения и действия) и автоматически будут зеркалами правильно. Пример:

```csharp
var button2 = NSButton.CreateButton (myTitle, myImage, () => {
    // Take action when the button is pressed
    ...
});
```

<a name="Using-System-Appearances" />

## <a name="using-system-appearances"></a>С помощью системных представлений программы

Можно применить новый темной внешний интерфейс, оптимально подходящий для приложений изображение создания, редактирования или презентации и macOS современных приложений:

[![](modern-cocoa-apps-images/content11.png "Пример пользовательского интерфейса окна темной Mac")](modern-cocoa-apps-images/content11.png#lightbox)

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

Статический `GetAppearance` метод `NSAppearance` класс используется для получения именованный внешний из системы (в данном случае `NSAppearance.NameVibrantDark`).

Apple имеет следующие рекомендации по использованию внешний вид системы:

- Именованные цвета, предпочтут жестко запрограммированных значений (таких как `LabelColor` и `SelectedControlColor`).
- Используйте стиль стандартного элемента управления системы, где это возможно.

MacOS приложения, в котором используется представление в системе автоматически будет работать для пользователей, которые включены специальные возможности из окна системных настроек приложения. В результате Apple предполагает, что разработчик должен всегда использовать внешний вид системы в своих приложениях macOS.

<a name="Designing-UIs-with-Storyboards" />

## <a name="designing-uis-with-storyboards"></a>Разработка пользовательских интерфейсов с помощью раскадровки

Раскадровки позволяют разработчику не только конструирования потока отдельные элементы, которые обеспечивают пользовательский интерфейс приложения, но до визуализировать и проектировать пользовательский Интерфейс, так и иерархии указанными элементами.

Контроллеры позволяют разработчику собирает элементы в единицу композиции и абстрактным Segues и удалите типичные «связывающим кода» для перемещения представления иерархии требуется:

[![](modern-cocoa-apps-images/content12.png "Изменение пользовательского интерфейса в построителе интерфейса в Xcode")](modern-cocoa-apps-images/content12.png#lightbox)

Дополнительные сведения см. в разделе нашей [введение в раскадровки](~/mac/platform/storyboards/index.md) документации.

Существует несколько экземпляров, где заданного сцены, определенные в раскадровку будет требуют данных из предыдущего кадра в иерархии представления. Apple имеет следующие рекомендации для передачи сведений между сцены.

- Dependancies данных следует всегда вызывать каскадные действия вниз по иерархии.
- Избегайте структурных dependancies жесткого задания пользовательского интерфейса, так как это ограничивает гибкость пользовательского интерфейса.
- Используйте C# интерфейсы для предоставления dependancies универсальных данных.

Можно переопределить, выступающего в качестве источника Segue контроллер представление `PrepareForSegue` метод и выполните необходимые инициализацию (например, для передачи данных), а затем Segue выполняется отображение целевого контроллера представления. Пример:

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

Дополнительные сведения см. в разделе нашей [Segues](~/mac/platform/storyboards/indepth.md#Segues) документации.

<a name="Propagating-Actions" />

## <a name="propagating-actions"></a>Распространение действий

На основании конструирования macOS приложения, может возникнуть советы обработчик для действия в элемент управления пользовательского интерфейса может находиться в в другом месте в иерархии пользовательского интерфейса. Это обычно происходит из меню и пунктов меню, проживающих в своих собственных сцены, отдельно от остальной части пользовательского интерфейса приложения.

Чтобы решить эту проблему, разработчик можно создать пользовательское действие и передать действие по цепочке сетевого ответчика. Дополнительные сведения см. в разделе нашей [работа с настраиваемые действия окно](~/mac/user-interface/menu.md) документации.

<a name="Modern-Mac-Features" />

## <a name="modern-mac-features"></a>Компоненты современных Mac

Apple несколько функций пользовательского интерфейса входит в состав macOS Сьерра, разработчик может использовать все возможности платформы, Mac, например:

- **NSUserActivity** -это позволяет приложению описывают действия, которое пользователь в настоящее время участвует в. `NSUserActivity` изначально был создан для поддержки передачи, где запущена на одном из устройств пользователя действие может выборка и продолжается на другое устройство. `NSUserActivity` работает так же в macOS как он делает в iOS таким образом, см. наш [Общие сведения о переадресации](~/ios/platform/handoff.md) iOS документации для получения дополнительных сведений.
- **Siri на Mac** -Siri использует текущее действие (`NSUserActivity`) для поддержки контекста пользователя может выдавать команды.
- **Состояние восстановления** — когда пользователь завершает работу приложения на macOS, а потом relaunches его, приложение будет автоматически возвращается в предыдущее состояние. Разработчик может использовать API состояния восстановления для кодирования и восстановить Переходные состояния пользовательского интерфейса, перед отображением пользовательского интерфейса для пользователя. Если приложение находится `NSDocument` зависимости, восстановление состояния обрабатывается автоматически. Чтобы включить восстановление состояния для не -`NSDocument` набор приложений, `Restorable` из `NSWindow` класса `true`.
- **Документы в облаке** -до появления macOS Сьерра приложение было явно согласиться на работе с документами в пользователя iCloud диска. В macOS Сьерра пользователя **Desktop** и **документов** папки могут быть синхронизированы с их iCloud диска автоматически системой. В результате локальные копии документов могут быть удалены для освобождения места на компьютере пользователя. `NSDocument` приложения на основе автоматически будет обрабатывать это изменение. Все остальные типы приложения должны использовать `NSFileCoordinator` для синхронизации чтения и записи документов.

<a name="Summary" />

## <a name="summary"></a>Сводка

В этой статье был проиллюстрирован несколько советов, функции и методы, разработчик может использовать для выполнения сборки приложения в Xamarin.Mac современных macOS.



## <a name="related-links"></a>Связанные ссылки

- [Образцы macOS](https://developer.xamarin.com/samples/mac/)
