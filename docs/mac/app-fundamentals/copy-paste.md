---
title: Скопируйте и вставьте в Xamarin.Mac
description: В этой статье рассматривается работа с монтажного стола для предоставления скопируйте и вставьте его в приложении Xamarin.Mac. Показано, как работать со стандартными типами данных, которые могут совместно использоваться несколькими приложениями и как обеспечить поддержку пользовательских данных в приложении.
ms.prod: xamarin
ms.assetid: 7E9C99FB-B7B4-4C48-B20F-84CB48543083
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 728e0264f7da8f3adfef360dd473772dd7e28a11
ms.sourcegitcommit: 47709db4d115d221e97f18bc8111c95723f6cb9b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2018
ms.locfileid: "40251161"
---
# <a name="copy-and-paste-in-xamarinmac"></a>Скопируйте и вставьте в Xamarin.Mac

_В этой статье рассматривается работа с монтажного стола для предоставления скопируйте и вставьте его в приложении Xamarin.Mac. Показано, как работать со стандартными типами данных, которые могут совместно использоваться несколькими приложениями и как обеспечить поддержку пользовательских данных в приложении._

## <a name="overview"></a>Обзор

При работе с C# и .NET в приложении Xamarin.Mac, у вас есть доступ к тем же монтажном столе (копирование и вставка) поддержка, работающий в Objective-C имеет.

В этой статье мы будет рассматривать два основных способа использования монтажного стола в приложении Xamarin.Mac:

1. **Стандартные типы данных** -так, как монтажном столе обычно выполняются операции между двумя несвязанными приложениями, ни одна из приложения определяет типы данных, который поддерживает другой. Полностью использовать потенциал для совместного использования, монтажного стола может содержать несколько представлений данного элемента (с помощью стандартного набора общих типов данных), это выполнять много приложение, чтобы выбрать версию, которая лучше всего подходит для своих потребностей.
2. **Пользовательские данные** — для поддержки копирования и вставки сложных данных в пределах вашей Xamarin.Mac, можно определить тип пользовательских данных, который будет обрабатываться монтажного стола. Например в приложении рисования вектор, позволяет пользователю скопируйте и вставьте сложных фигур, состоящих из нескольких типов данных и точки.

[![Пример запущенного приложения](copy-paste-images/intro01.png "пример запущенного приложения")](copy-paste-images/intro01-large.png#lightbox)

В этой статье мы обсудим, что узнаете основы работы с монтажного стола в приложении Xamarin.Mac для поддержки копирования и вставки. Настоятельно рекомендуется работать через [Привет, Mac](~/mac/get-started/hello-mac.md) статьи, во-первых, в частности [введение в Xcode и конструкторе Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) и [переменных экземпляров и действий](~/mac/get-started/hello-mac.md#outlets-and-actions) разделы, так как он рассматриваются основные понятия и методы, которые мы будем использовать в этой статье.

Может потребоваться взгляните на [предоставление C# классы / методы Objective-c](~/mac/internals/how-it-works.md) раздел [структуре Xamarin.Mac](~/mac/internals/how-it-works.md) документа, он объясняет `Register` и `Export` атрибуты используется для привязывания классов C# к объектам Objective-C и пользовательского интерфейса элементов.

## <a name="getting-started-with-the-pasteboard"></a>Приступая к работе с монтажного стола

Монтажного стола представляет стандартный механизм для обмена данными в данном приложении или между приложениями. Обычно для вставки в приложении Xamarin.Mac используется для обработки копирования и вставки, однако также поддерживается ряд других операций (например, перетащите & Drop и службы приложений).

Поможет вам быстро создать с нуля, мы хотим начать с простого и практические Общие сведения об использовании монтажных столах в приложении Xamarin.Mac. Позже мы предоставим подробное разъяснение принципов монтажного стола и методы, используемые.

В этом примере мы создадим приложение на основе простого документа, которое управляет окном, содержащая представление изображения. Пользователь будет иметь возможность скопировать и вставить образов между документами в приложении и на или из других приложений или несколько окон внутри одного приложения.

### <a name="creating-the-xamarin-project"></a>Создание проекта Xamarin

Во-первых мы собираемся создать новое приложение Xamarin.Mac документа, основанного на что мы будет добавление скопируйте и вставьте поддержка.

Выполните следующие действия:

1. Запустите Visual Studio для Mac и нажмите кнопку **новый проект...**  ссылку.
2. Выберите **Mac** > **приложения** > **приложение Cocoa**, затем нажмите кнопку **Далее** кнопки: 

    [![Создание нового проекта приложения Cocoa](copy-paste-images/sample01.png "Создание нового проекта приложения Cocoa")](copy-paste-images/sample01-large.png#lightbox)
3. Введите `MacCopyPaste` для **имя_проекта** и сохранить все остальные значения по умолчанию. Нажмите кнопку Далее: 

    [![Задание имени проекта](copy-paste-images/sample01a.png "задание имени проекта")](copy-paste-images/sample01a-large.png#lightbox)

4. Нажмите кнопку **создать** кнопки: 

    [![Подтверждение параметров нового проекта](copy-paste-images/sample02.png "подтверждения новых параметров проекта")](copy-paste-images/sample02-large.png#lightbox)

### <a name="add-an-nsdocument"></a>Добавить NSDocument

Далее мы добавим настраиваемый `NSDocument` класс, который будет выступать в качестве хранилища фона для пользовательского интерфейса приложения. Он содержит одно представление изображения и узнать, как скопировать изображение из представления в области вставки и как получить изображение из монтажного стола по умолчанию и отображения его в графическом режиме.

Щелкните правой кнопкой мыши по проекту Xamarin.Mac в **панели решения** и выберите **добавить** > **новый файл...** :

![Добавление в проект NSDocument](copy-paste-images/sample03.png "Добавление в проект NSDocument")

Введите в поле **Имя** значение `ImageDocument` и нажмите кнопку **Новый**. Изменить **ImageDocument.cs** класса и это должно выглядеть следующим образом:

```csharp
using System;
using AppKit;
using Foundation;
using ObjCRuntime;

namespace MacCopyPaste
{
    [Register("ImageDocument")]
    public class ImageDocument : NSDocument
    {
        #region Computed Properties
        public NSImageView ImageView {get; set;}

        public ImageInfo Info { get; set; } = new ImageInfo();

        public bool ImageAvailableOnPasteboard {
            get {
                // Initialize the pasteboard
                NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
                Class [] classArray  = { new Class ("NSImage") };

                // Check to see if an image is on the pasteboard
                return pasteboard.CanReadObjectForClasses (classArray, null);
            }
        }
        #endregion

        #region Constructor
        public ImageDocument ()
        {
        }
        #endregion

        #region Public Methods
        [Export("CopyImage:")]
        public void CopyImage(NSObject sender) {

            // Grab the current image
            var image = ImageView.Image;

            // Anything to process?
            if (image != null) {
                // Get the standard pasteboard
                var pasteboard = NSPasteboard.GeneralPasteboard;

                // Empty the current contents
                pasteboard.ClearContents();

                // Add the current image to the pasteboard
                pasteboard.WriteObjects (new NSImage[] {image});

                // Save the custom data class to the pastebaord
                pasteboard.WriteObjects (new ImageInfo[] { Info });

                // Using a Pasteboard Item
                NSPasteboardItem item = new NSPasteboardItem();
                string[] writableTypes = {"public.text"};

                // Add a data provier to the item
                ImageInfoDataProvider dataProvider = new ImageInfoDataProvider (Info.Name, Info.ImageType);
                var ok = item.SetDataProviderForTypes (dataProvider, writableTypes);

                // Save to pasteboard
                if (ok) {
                    pasteboard.WriteObjects (new NSPasteboardItem[] { item });
                }
            }

        }

        [Export("PasteImage:")]
        public void PasteImage(NSObject sender) {

            // Initialize the pasteboard
            NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
            Class [] classArray  = { new Class ("NSImage") };

            bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
            if (ok) {
                // Read the image off of the pasteboard
                NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
                NSImage image = (NSImage)objectsToPaste[0];

                // Display the new image
                ImageView.Image = image;
            }

            Class [] classArray2 = { new Class ("ImageInfo") };
            ok = pasteboard.CanReadObjectForClasses (classArray2, null);
            if (ok) {
                // Read the image off of the pasteboard
                NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
                ImageInfo info = (ImageInfo)objectsToPaste[0];

            }

        }
        #endregion
    }
}
```

Давайте рассмотрим некоторые программы ниже подробно.

Следующий код предоставляет свойство для проверки существования данных изображения монтажного стола по умолчанию, если образ доступен, `true` в противном случае возвращается `false`:

```csharp
public bool ImageAvailableOnPasteboard {
    get {
        // Initialize the pasteboard
        NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
        Class [] classArray  = { new Class ("NSImage") };

        // Check to see if an image is on the pasteboard
        return pasteboard.CanReadObjectForClasses (classArray, null);
    }
}
```

Следующий код копирует изображение из представления прикрепленного изображения в области вставки:

```csharp
[Export("CopyImage:")]
public void CopyImage(NSObject sender) {

    // Grab the current image
    var image = ImageView.Image;

    // Anything to process?
    if (image != null) {
        // Get the standard pasteboard
        var pasteboard = NSPasteboard.GeneralPasteboard;

        // Empty the current contents
        pasteboard.ClearContents();

        // Add the current image to the pasteboard
        pasteboard.WriteObjects (new NSImage[] {image});

        // Save the custom data class to the pastebaord
        pasteboard.WriteObjects (new ImageInfo[] { Info });

        // Using a Pasteboard Item
        NSPasteboardItem item = new NSPasteboardItem();
        string[] writableTypes = {"public.text"};

        // Add a data provider to the item
        ImageInfoDataProvider dataProvider = new ImageInfoDataProvider (Info.Name, Info.ImageType);
        var ok = item.SetDataProviderForTypes (dataProvider, writableTypes);

        // Save to pasteboard
        if (ok) {
            pasteboard.WriteObjects (new NSPasteboardItem[] { item });
        }
    }

}
```

И следующий код вставляет изображение из монтажного стола по умолчанию и отображает его в представлении прикрепленного изображения (если монтажного стола содержит действительный образ):

```csharp
[Export("PasteImage:")]
public void PasteImage(NSObject sender) {

    // Initialize the pasteboard
    NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
    Class [] classArray  = { new Class ("NSImage") };

    bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
        NSImage image = (NSImage)objectsToPaste[0];

        // Display the new image
        ImageView.Image = image;
    }

    Class [] classArray2 = { new Class ("ImageInfo") };
    ok = pasteboard.CanReadObjectForClasses (classArray2, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
        ImageInfo info = (ImageInfo)objectsToPaste[0]
    }
}
```

Этот документ на месте мы создадим пользовательский интерфейс для приложения Xamarin.Mac.

### <a name="building-the-user-interface"></a>Создание пользовательского интерфейса

Дважды щелкните **Main.storyboard** файл, чтобы открыть его в Xcode. Затем добавьте панель инструментов и изображения хорошо и настройте их следующим образом:

[![Панель инструментов для редактирования](copy-paste-images/sample04.png "редактирование панели инструментов")](copy-paste-images/sample04-large.png#lightbox)

Добавить копию и вставьте **элемент панели инструментов изображения** в левой части панели инструментов. Мы будем использовать их в качестве сочетания клавиш для копирования и вставки из меню "Правка". Добавьте четыре **элементов панели инструментов изображения** в правой части панели инструментов. Мы будем использовать их для заполнения изображения с некоторые образы по умолчанию.

Дополнительные сведения о работе с панелями инструментов см. в разделе наших [панелей инструментов](~/mac/user-interface/toolbar.md) документации.

Теперь давайте также предоставляют следующие переменные экземпляров и действия для наших элементы панели инструментов и изображения:

[![Создание переменных экземпляров и действий](copy-paste-images/sample05.png "Создание переменных экземпляров и действий")](copy-paste-images/sample05-large.png#lightbox)

Дополнительные сведения о работе с помощью переменных экземпляров и действий, см. в разделе [переменных экземпляров и действий](~/mac/get-started/hello-mac.md#outlets-and-actions) части нашей [Привет, Mac](~/mac/get-started/hello-mac.md) документации.

### <a name="enabling-the-user-interface"></a>Включение пользовательского интерфейса

С помощью пользовательского интерфейса создан в Xcode и наш элемент пользовательского интерфейса, реализуются с помощью переменных экземпляров и действий необходимо добавить код, пользовательский Интерфейс. Дважды щелкните **ImageWindow.cs** файл **панели решения** и это должно выглядеть следующим образом:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacCopyPaste
{
    public partial class ImageWindow : NSWindow
    {
        #region Private Variables
        ImageDocument document;
        #endregion

        #region Computed Properties
        [Export ("Document")]
        public ImageDocument Document {
            get {
                return document;
            }
            set {
                WillChangeValue ("Document");
                document = value;
                DidChangeValue ("Document");
            }
        }

        public ViewController ImageViewController {
            get { return ContentViewController as ViewController; }
        }

        public NSImage Image {
            get {
                return ImageViewController.Image;
            }
            set {
                ImageViewController.Image = value;
            }
        }
        #endregion

        #region Constructor
        public ImageWindow (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Create a new document instance
            Document = new ImageDocument ();

            // Attach to image view
            Document.ImageView = ImageViewController.ContentView;
        }
        #endregion

        #region Public Methods
        public void CopyImage (NSObject sender)
        {
            Document.CopyImage (sender);
        }

        public void PasteImage (NSObject sender)
        {
            Document.PasteImage (sender);
        }

        public void ImageOne (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image01.jpg");

            // Set image info
            Document.Info.Name = "city";
            Document.Info.ImageType = "jpg";
        }

        public void ImageTwo (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image02.jpg");

            // Set image info
            Document.Info.Name = "theater";
            Document.Info.ImageType = "jpg";
        }

        public void ImageThree (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image03.jpg");

            // Set image info
            Document.Info.Name = "keyboard";
            Document.Info.ImageType = "jpg";
        }

        public void ImageFour (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image04.jpg");

            // Set image info
            Document.Info.Name = "trees";
            Document.Info.ImageType = "jpg";
        }
        #endregion
    }
}
```

Давайте взглянем на этот код ниже подробно.

Во-первых, мы предоставляем экземпляр `ImageDocument` класс, который мы создали выше:

```csharp
private ImageDocument _document;
...

[Export ("Document")]
public ImageDocument Document {
    get { return _document; }
    set {
        WillChangeValue ("Document");
        _document = value;
        DidChangeValue ("Document");
    }
}
```

С помощью `Export`, `WillChangeValue` и `DidChangeValue`, у нас есть программа установки `Document` свойство, чтобы разрешить для кодирования ключ-значение и привязка данных в Xcode.

Мы также предоставляем образ из образа, также мы добавили к пользовательскому Интерфейсу в Xcode со следующим свойством:

```csharp
public ViewController ImageViewController {
    get { return ContentViewController as ViewController; }
}

public NSImage Image {
    get {
        return ImageViewController.Image;
    }
    set {
        ImageViewController.Image = value;
    }
}
```

Когда загружается и отображается главного окна, мы создаем экземпляр нашей `ImageDocument` класса и присоединить образ пользовательского интерфейса для его следующим кодом:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Create a new document instance
    Document = new ImageDocument ();

    // Attach to image view
    Document.ImageView = ImageViewController.ContentView;
}
```

В ответ на щелчок для копирования и вставки элементов панели инструментов, мы вызываем экземпляр `ImageDocument` класса для выполнения фактических действий:

```csharp
partial void CopyImage (NSObject sender) {
    Document.CopyImage(sender);
}

partial void PasteImage (Foundation.NSObject sender) {
    Document.PasteImage(sender);
}
```

### <a name="enabling-the-file-and-edit-menus"></a>Включение меню «файл» и «Edit

Последнее, что нам нужно сделать это включить **New** элемент меню из **файл** меню (для создания новых экземпляров наше основное окно) и включить **Вырезать**, **копирования**  и **вставить** пункты **изменить** меню.

Чтобы включить **New** меню элемента, изменить **AppDelegate.cs** файл и добавьте следующий код:

```csharp
public int UntitledWindowCount { get; set;} =1;
...

[Export ("newDocument:")]
void NewDocument (NSObject sender) {
    // Get new window
    var storyboard = NSStoryboard.FromName ("Main", null);
    var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

    // Display
    controller.ShowWindow(this);

    // Set the title
    controller.Window.Title = (++UntitledWindowCount == 1) ? "untitled" : string.Format ("untitled {0}", UntitledWindowCount);
}
```

Дополнительные сведения см. в разделе [работа с несколькими Windows](~/mac/user-interface/window.md) части нашей [Windows](~/mac/user-interface/window.md) документации.

Чтобы включить **Вырезать**, **копирования** и **вставить** пункты меню, изменить **AppDelegate.cs** файл и добавьте следующий код:

```csharp
[Export("copy:")]
void CopyImage (NSObject sender)
{
    // Get the main window
    var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;

    // Anything to do?
    if (window == null)
        return;

    // Copy the image to the clipboard
    window.Document.CopyImage (sender);
}

[Export("cut:")]
void CutImage (NSObject sender)
{
    // Get the main window
    var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;

    // Anything to do?
    if (window == null)
        return;

    // Copy the image to the clipboard
    window.Document.CopyImage (sender);

    // Clear the existing image
    window.Image = null;
}

[Export("paste:")]
void PasteImage (NSObject sender)
{
    // Get the main window
    var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;

    // Anything to do?
    if (window == null)
        return;

    // Paste the image from the clipboard
    window.Document.PasteImage (sender);
}
```

Для каждого пункта меню, мы получить окно текущего, верхний, ключа и приведите его к нашей `ImageWindow` класса:

```csharp
var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;
```

Здесь мы вызываем `ImageDocument` экземпляр класса окна для обработки копирования и вставки данных. Пример: 

```csharp
window.Document.CopyImage (sender);
```

Нам нужен только **Вырезать**, **копирования** и **вставить** пункты меню могут быть недоступны, если имеется образ данных монтажного стола по умолчанию или в виде образа также текущее активное окно.

Давайте добавим **EditMenuDelegate.cs** файл в проект Xamarin.Mac и это должно выглядеть следующим образом:

```csharp
using System;
using AppKit;

namespace MacCopyPaste
{
    public class EditMenuDelegate : NSMenuDelegate
    {
        #region Override Methods
        public override void MenuWillHighlightItem (NSMenu menu, NSMenuItem item)
        {
        }

        public override void NeedsUpdate (NSMenu menu)
        {
            // Get list of menu items
            NSMenuItem[] Items = menu.ItemArray ();

            // Get the key window and determine if the required images are available
            var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;
            var hasImage = (window != null) && (window.Image != null);
            var hasImageOnPasteboard = (window != null) && window.Document.ImageAvailableOnPasteboard;

            // Process every item in the menu
            foreach(NSMenuItem item in Items) {
                // Take action based on the menu title
                switch (item.Title) {
                case "Cut":
                case "Copy":
                case "Delete":
                    // Only enable if there is an image in the view
                    item.Enabled = hasImage;
                    break;
                case "Paste":
                    // Only enable if there is an image on the pasteboard
                    item.Enabled = hasImageOnPasteboard;
                    break;
                default:
                    // Only enable the item if it has a sub menu
                    item.Enabled = item.HasSubmenu;
                    break;
                }
            }
        }
        #endregion
    }
}
```

Опять же, мы получить текущий, верхнее окно и использовать его `ImageDocument` экземпляр класса, существует ли данные необходимые изображения. Затем мы используем `MenuWillHighlightItem` метод для включения или отключения каждого элемента на основе этого состояния.

Изменить **AppDelegate.cs** и создайте `DidFinishLaunching` внешний метод следующим образом:
 
```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    // Disable automatic item enabling on the Edit menu
    EditMenu.AutoEnablesItems = false;
    EditMenu.Delegate = new EditMenuDelegate ();
}
```

Во-первых мы отключить автоматическое включение и отключение пунктов меню в меню "Правка". Далее мы присоединяем экземпляр `EditMenuDelegate` класс, который мы создали выше.

Дополнительные сведения см. в разделе наших [меню](~/mac/user-interface/menu.md) документации.

### <a name="testing-the-app"></a>Тестирование приложения

Все мы готовы к тестированию приложения. Построение и запустите приложение и отображается основной интерфейс:

![Запуск приложения](copy-paste-images/run01.png "запуск приложения")

При открытии меню "Правка", обратите внимание, что **Вырезать**, **копирования** и **вставить** отключены, так как нет изображения на рисунке также или вставки по умолчанию:

![Открытие меню "Правка"](copy-paste-images/run02.png "открыв меню \"Правка\"")

Если добавить изображение к образу хорошо и снова откройте меню "Правка", теперь можно включить элементы:

![Отображение элементов меню Edit включены](copy-paste-images/run03.png "отображаются элементы меню Edit включены")

Если скопировать образ и выберите **New** меню "файл", этот образ можно вставить в новом окне:

![Вставить изображение в новое окно](copy-paste-images/run04.png "вставки изображения в новом окне")

В следующих разделах мы рассмотрим более подробно работа с монтажного стола в приложении Xamarin.Mac.

## <a name="about-the-pasteboard"></a>О монтажного стола

В macOS (прежнее название OS X) монтажного стола (`NSPasteboard`) обеспечивают поддержку для нескольких серверных процессов, таких как копирование и вставка, перетаскивание и службы приложений. В следующих разделах мы рассмотрим более подробно рассмотрим некоторые основные понятия монтажном столе.

### <a name="what-is-a-pasteboard"></a>Что такое монтажного стола?

`NSPasteboard` Класс предоставляет стандартный механизм для обмена данными между приложениями, или в приложении. Основная функция монтажного стола используется для обработки операций копирования и вставки:

1. Когда пользователь выбирает элемент в приложении и использует **Вырезать** или **копирования** пункта меню, один или несколько представлений выбранного элемента помещаются монтажного стола.
2. Когда пользователь использует **вставить** пункта меню (в пределах одного приложения или другое), версии данных, он может обрабатывать копируется из монтажного стола и добавляется в приложение.

Менее очевидная монтажном столе использования могут найти перетаскивания, путем перетаскивания и операций со службами приложений:

- Когда пользователь инициирует операцию перетаскивания, перетаскиваемые данные копируются в буфер. Если операция перетаскивания завершается после перетаскивания на другое приложение, приложение копирует данные из монтажного стола.
- Услуги по переводу данные, которые будут преобразованы копируется в буфер запрашивающему приложению. Службы приложения, получает данные из монтажного стола, осуществляет трансляцию, а затем вставляет данные обратно на монтажного стола.

В своей простейшей форме монтажных столах используются для перемещения данных в приложении или в разных приложениях и что существует в специальной глобальной области памяти вне процесса приложения. Хотя концепции монтажных столах являются легко grasps, несколько более сложных параметров должны совпадать. Они будут рассматриваться ниже подробно.

### <a name="named-pasteboards"></a>Именованный монтажных столах

Монтажного стола может быть открытой или закрытой и может использоваться для различных целей в приложении или между несколькими приложениями. macOS предоставляет несколько стандартных монтажных столах, каждый из которых конкретных, четко определенные использования:

- `NSGeneralPboard` -По умолчанию монтажного стола для **Вырезать**, **копирования** и **вставить** операций.
- `NSRulerPboard` — Поддерживает **Вырезать**, **копирования** и **вставить** операции с **линейки**.
- `NSFontPboard` — Поддерживает **Вырезать**, **копирования** и **вставить** операции с `NSFont` объектов.
- `NSFindPboard` -Поддержка конкретного приложения найдите панелей, которые могут совместно использовать искомый текст.
- `NSDragPboard` — Поддерживает **перетаскивание** операций.

В большинстве ситуаций будем использовать один из системы и определены монтажных столах. Однако возможны ситуации, когда необходимо создавать свои собственные монтажных столах. В этих случаях можно использовать `FromName (string name)` метод `NSPasteboard` класса для создания пользовательских вставки с заданным именем.

При необходимости можно вызвать `CreateWithUniqueName` метод `NSPasteboard` класса для создания уникальным именем вставки.

### <a name="pasteboard-items"></a>Монтажном столе элементов

Каждый фрагмент данных, которые приложение записывает чтобы монтажного стола считается _элемента монтажного стола_ и монтажного стола может содержать несколько элементов одновременно. Таким образом приложение можно написать несколько версий копируемых чтобы монтажного стола (например, обычный текст и форматированный текст) и получение приложения данных можно прочитать только те данные, что он может обрабатывать (например, только обычный текст).

### <a name="data-representations-and-uniform-type-identifiers"></a>Представления данных, а также идентификаторы универсальный тип

Монтажном столе операции обычно занимают приложений между двумя (или несколько), которые не имеют сведений о друг с другом или типы данных, что каждый может обрабатывать. Как уже говорилось в предыдущем разделе, полностью использовать потенциал для совместного использования информации, монтажного стола могут содержать несколько представлений данных копирования и вставки.

Каждое представление определяется через универсальный тип идентификатор (UTI), который представляет собой не более чем простая строка, однозначно определяющее тип даты, представляемых (Дополнительные сведения см. в разделе Apple [Обзор идентификаторов универсальный тип ](https://developer.apple.com/library/prerelease/mac/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_intro/understand_utis_intro.html#//apple_ref/doc/uid/TP40001319) документации). 

Если вы создаете пользовательский тип данных (например, графического объекта в векторного рисунка приложения), можно создать собственные UTI для уникальной идентификации ее в копии и операции вставки.

Когда приложение подготавливает для вставки данных, копируемых из монтажного стола, он должен найти представление, которое наилучшим образом соответствует его возможности (если таковое существует). Обычно это будет широчайший доступен тип (например форматированного текста для обработки текстов приложения), возврат к простой формы, доступные как необходимые (обычный текст для простого текстового редактора).

<a name="Promised_Data" />

### <a name="promised-data"></a>Обещанные данные

Вообще говоря необходимо предоставить столько представления данных, копируемых максимально возможное для обеспечения максимальной совместное использование приложений. Тем не менее из-за ограничений времени или памяти, его следует применять для фактической записи каждого типа данных в область вставки.

В этом случае можно разместить первое представление данных монтажного стола и принимающее приложение может запросить другое представление, которое может быть созданный в режиме реального времени непосредственно перед операцией вставки.

Если поместить элемент вставки, вы укажете, что один или несколько доступных другие представления, предоставляются объект, который соответствует `NSPasteboardItemDataProvider` интерфейс. Эти объекты предоставит дополнительные представления по требованию, по запросу в принимающее приложение.

### <a name="change-count"></a>Число изменений

Поддерживает каждого монтажного стола _число изменений_ объявляется времени, с шагом каждого нового владельца. Приложение можно определить, если область вставки содержимое изменилось с момента последнего ее проанализировать путем проверки значения число изменений.

Используйте `ChangeCount` и `ClearContents` методы `NSPasteboard` класс, чтобы изменить число изменений данной монтажного стола.

## <a name="copying-data-to-a-pasteboard"></a>Копирование данных, чтобы монтажного стола

Первый доступ к панели вставки, сняв все существующее содержимое и написания столько представления данных, так как являются необходимыми, чтобы монтажного стола выполнить операцию копирования.

Пример:

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Empty the current contents
pasteboard.ClearContents();

// Add the current image to the pasteboard
pasteboard.WriteObjects (new NSImage[] {image});
```

Как правило вы просто начну чтобы монтажного стола Общие, как было показано в приведенном выше примере. Любой объект, который отправляется `WriteObjects` метод *необходимо* соответствуют `INSPasteboardWriting` интерфейс. Несколько встроенных классов (таких как `NSString`, `NSImage`, `NSURL`, `NSColor`, `NSAttributedString`, и `NSPasteboardItem`) автоматически наследуют этот интерфейс.

Если вы создаете пользовательский класс данных, чтобы монтажного стола должно удовлетворять `INSPasteboardWriting` интерфейс или инкапсулируемый в экземпляре `NSPasteboardItem` класса (см. в разделе [пользовательские типы данных](#Custom_Data_Types) раздел ниже).

## <a name="reading-data-from-a-pasteboard"></a>Чтение данных из панели вставки

Как уже говорилось, полностью использовать потенциал для обмена данными между приложениями, несколькими представлениями скопированные данные могут записываться в буфер. Возлагается принимающему приложению выберите широчайший версии, возможно, для его возможности (если таковое существует).

### <a name="simple-paste-operation"></a>Простой при вставке

Чтение данных из монтажном столе с помощью `ReadObjectsForClasses` метод. Для этого потребуется задать два параметра:

1. Массив `NSObject` на основе типов классов, которые требуется выполнить чтение из монтажного стола. Вы должны заказать это с наиболее требуемый тип данных во-первых, остальные типы в снижения предпочтения.
2. Словарь, содержащий дополнительные ограничения (например, можно ограничить для определенных типов содержимого URL-адрес) или пустой словарь, если нет дополнительных ограничений являются обязательными.

Метод возвращает массив элементов, соответствующих критериям, которые мы передали в и поэтому содержит не более одинаковое количество типов данных, которые запрашиваются. Это также возможно, что ни один из запрошенных типов присутствуют и возвращается пустой массив.

Например, следующий код проверяет, если `NSImage` существует общие вставки и отображает его в изображения хорошо, если это так:

```csharp
[Export("PasteImage:")]
public void PasteImage(NSObject sender) {

    // Initialize the pasteboard
    NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
    Class [] classArray  = { new Class ("NSImage") };

    bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
        NSImage image = (NSImage)objectsToPaste[0];

        // Display the new image
        ImageView.Image = image;
    }

    Class [] classArray2 = { new Class ("ImageInfo") };
    ok = pasteboard.CanReadObjectForClasses (classArray2, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
        ImageInfo info = (ImageInfo)objectsToPaste[0];
            }
}
```

### <a name="requesting-multiple-data-types"></a>Запрашивает несколько типов данных

В зависимости от типа создаваемого приложения Xamarin.Mac, может быть возможность обрабатывать данные, вставляемые несколькими представлениями. В этом случае существует два сценария для извлечения данных из монтажного стола:

1. Сделать один вызов `ReadObjectsForClasses` с указанием массив всех представлений, которые (в порядке предпочтения).
2. Выполнение нескольких вызовов `ReadObjectsForClasses` метод, запрашивая его с другим массивом из типов, каждый раз.

См. в разделе **простой операции вставки** разделе выше, Дополнительные сведения о получении данных от монтажного стола.

### <a name="checking-for-existing-data-types"></a>Проверка существующих типов данных

Бывают случаи, которые может потребоваться проверить наличие монтажного стола представление данных фактически не считывая данные из монтажного стола (таких как включение **вставить** пункта меню только в том случае, если существуют допустимые данные).

Вызовите `CanReadObjectForClasses` метод монтажного стола, содержит ли данный тип.

Например, следующий код определяет, содержит ли общие монтажного стола `NSImage` экземпляр:

```csharp
public bool ImageAvailableOnPasteboard {
    get {
        // Initialize the pasteboard
        NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
        Class [] classArray  = { new Class ("NSImage") };

        // Check to see if an image is on the pasteboard
        return pasteboard.CanReadObjectForClasses (classArray, null);
    }
}
```

### <a name="reading-urls-from-the-pasteboard"></a>Считывание монтажного стола URL-адреса

На основе функции данного приложения Xamarin.Mac, возможно, необходимые для чтения URL-адреса из монтажного стола, но только при соблюдении заданных критериев (например, указание к файлам или URL-адреса к определенному типу данных). В этом случае можно указать дополнительный критерий поиска с помощью второго параметра `CanReadObjectForClasses` или `ReadObjectsForClasses` методы.

<a name="Custom_Data_Types" />

## <a name="custom-data-types"></a>Пользовательские типы данных

Бывают случаи, когда необходимо сохранить свои собственные пользовательские типы в буфер из приложения Xamarin.Mac. Например вектор, рисование приложение, которое позволяет пользователю копировать и вставить графические объекты.

В этом случае необходимо разработать пользовательский класс данных, таким образом, чтобы он наследовал `NSObject` и он соответствует несколько интерфейсов (`INSCoding`, `INSPasteboardWriting` и `INSPasteboardReading`). При необходимости можно использовать `NSPasteboardItem` для инкапсуляции данных, подлежащей копированию или вставки.

Ниже подробно рассматриваются оба варианта.

### <a name="using-a-custom-class"></a>С помощью пользовательского класса

В этом разделе мы расширение простой пример приложения, которое мы создали в начале этого документа и добавив пользовательский класс для отслеживания сведений о образ, который мы копирования и вставки между окнами.

Добавьте новый класс в проект и назовите его **ImageInfo.cs**. Измените файл и это должно выглядеть следующим образом:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacCopyPaste
{
    [Register("ImageInfo")]
    public class ImageInfo : NSObject, INSCoding, INSPasteboardWriting, INSPasteboardReading
    {
        #region Computed Properties
        [Export("name")]
        public string Name { get; set; }

        [Export("imageType")]
        public string ImageType { get; set; }
        #endregion

        #region Constructors
        [Export ("init")]
        public ImageInfo ()
        {
        }
        
        public ImageInfo (IntPtr p) : base (p)
        {
        }

        [Export ("initWithCoder:")]
        public ImageInfo(NSCoder decoder) {

            // Decode data
            NSString name = decoder.DecodeObject("name") as NSString;
            NSString type = decoder.DecodeObject("imageType") as NSString;

            // Save data
            Name = name.ToString();
            ImageType = type.ToString ();
        }
        #endregion

        #region Public Methods
        [Export ("encodeWithCoder:")]
        public void EncodeTo (NSCoder encoder) {

            // Encode data
            encoder.Encode(new NSString(Name),"name");
            encoder.Encode(new NSString(ImageType),"imageType");
        }

        [Export ("writableTypesForPasteboard:")]
        public virtual string[] GetWritableTypesForPasteboard (NSPasteboard pasteboard) {
            string[] writableTypes = {"com.xamarin.image-info", "public.text"};
            return writableTypes;
        }

        [Export ("pasteboardPropertyListForType:")]
        public virtual NSObject GetPasteboardPropertyListForType (string type) {

            // Take action based on the requested type
            switch (type) {
            case "com.xamarin.image-info":
                return NSKeyedArchiver.ArchivedDataWithRootObject(this);
            case "public.text":
                return new NSString(string.Format("{0}.{1}", Name, ImageType));
            }

            // Failure, return null
            return null;
        }

        [Export ("readableTypesForPasteboard:")]
        public static string[] GetReadableTypesForPasteboard (NSPasteboard pasteboard){
            string[] readableTypes = {"com.xamarin.image-info", "public.text"};
            return readableTypes;
        }

        [Export ("readingOptionsForType:pasteboard:")]
        public static NSPasteboardReadingOptions GetReadingOptionsForType (string type, NSPasteboard pasteboard) {

            // Take action based on the requested type
            switch (type) {
            case "com.xamarin.image-info":
                return NSPasteboardReadingOptions.AsKeyedArchive;
            case "public.text":
                return NSPasteboardReadingOptions.AsString;
            }

            // Default to property list
            return NSPasteboardReadingOptions.AsPropertyList;
        }

        [Export ("initWithPasteboardPropertyList:ofType:")]
        public NSObject InitWithPasteboardPropertyList (NSObject propertyList, string type) {

            // Take action based on the requested type
            switch (type) {
            case "com.xamarin.image-info":
                return new ImageInfo();
            case "public.text":
                return new ImageInfo();
            }

            // Failure, return null
            return null;
        }
        #endregion
    }
}
    
```

В следующих разделах мы рассмотрим более подробно этот класс.

#### <a name="inheritance-and-interfaces"></a>Наследование и интерфейсы

Прежде чем пользовательский класс данных может быть записи или чтения из монтажного стола, она должна соответствовать `INSPastebaordWriting` и `INSPasteboardReading` интерфейсов. Кроме того, он должен наследовать `NSObject` и также соответствовать `INSCoding` интерфейса:

```csharp
[Register("ImageInfo")]
public class ImageInfo : NSObject, INSCoding, INSPasteboardWriting, INSPasteboardReading
...
```

Класс также должен быть открыт для Objective-C с помощью `Register` директивы и он должен предоставлять необходимые свойства и методы, с помощью `Export`. Пример:

```csharp
[Export("name")]
public string Name { get; set; }

[Export("imageType")]
public string ImageType { get; set; }
```

Мы предоставляете два поля данных, этот класс будет содержать - имя образа и его тип (jpg, png, и т.д.). 

Дополнительные сведения см. в разделе [предоставление C# классы / методы Objective-c](~/mac/internals/how-it-works.md) раздел [структуре Xamarin.Mac](~/mac/internals/how-it-works.md) документации, в ней объясняется `Register` и `Export` атрибуты используется для привязывания классов C# к объектам Objective-C и пользовательского интерфейса элементов.

#### <a name="constructors"></a>Конструкторы

Два конструктора (должным образом передаваться Objective-C) потребуются для наших пользовательского класса данных таким образом, чтобы его можно считать из монтажного стола:

```csharp
[Export ("init")]
public ImageInfo ()
{
}

[Export ("initWithCoder:")]
public ImageInfo(NSCoder decoder) {

    // Decode data
    NSString name = decoder.DecodeObject("name") as NSString;
    NSString type = decoder.DecodeObject("imageType") as NSString;

    // Save data
    Name = name.ToString();
    ImageType = type.ToString ();
}
```

Во-первых, мы предоставляем _пустой_ конструктора с учетом метод Objective-C по умолчанию `init`.

После этого мы предоставляем `NSCoding` совместимый конструктор, который будет использоваться для создания нового экземпляра объекта от монтажного стола, при вставке в разделе экспортируемое имя `initWithCoder`.

Этот конструктор принимает `NSCoder` (созданный при помощи `NSKeyedArchiver` при записи в буфер), извлекает данные в паре ключ значение и сохраняет его в поля свойств класса данных.

#### <a name="writing-to-the-pasteboard"></a>Запись в буфер

Подчиняясь соглашению `INSPasteboardWriting` интерфейс, нам нужно предоставлять два метода и при необходимости третий метод, таким образом, чтобы класс могут записываться в буфер.

Во-первых мы должны сообщить монтажного стола тип данных, представления, которые могут быть записаны пользовательского класса:

```csharp
[Export ("writableTypesForPasteboard:")]
public virtual string[] GetWritableTypesForPasteboard (NSPasteboard pasteboard) {
    string[] writableTypes = {"com.xamarin.image-info", "public.text"};
    return writableTypes;
}
```

Каждое представление определяется через универсальный тип идентификатор (UTI), который представляет собой не более чем простая строка, однозначно определяющее тип данных, представляемых (Дополнительные сведения см. в разделе Apple [Обзор идентификаторов универсальный тип ](https://developer.apple.com/library/prerelease/mac/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_intro/understand_utis_intro.html#//apple_ref/doc/uid/TP40001319) документации).

Для наших настраиваемого формата, мы создаем нашего собственного UTI: «com.xamarin.image-info» (Обратите внимание, что в обратную нотацию так же, как идентификатор приложения). Наш класс также может записывать стандартную строку в буфер (`public.text`). 

Далее нам нужно создать объект в затребованном формате, которая фактически записывается в буфер:

```csharp
[Export ("pasteboardPropertyListForType:")]
public virtual NSObject GetPasteboardPropertyListForType (string type) {

    // Take action based on the requested type
    switch (type) {
    case "com.xamarin.image-info":
        return NSKeyedArchiver.ArchivedDataWithRootObject(this);
    case "public.text":
        return new NSString(string.Format("{0}.{1}", Name, ImageType));
    }

    // Failure, return null
    return null;
}
```

Для `public.text` типа, мы возвращается простой, в формате `NSString` объекта. Для настраиваемого `com.xamarin.image-info` типа, мы используем `NSKeyedArchiver` и `NSCoder` интерфейс Кодируемая в пользовательских классах данных архив пару ключ значение. Нам потребуется реализовать следующий метод в действительности кодировками:

```csharp
[Export ("encodeWithCoder:")]
public void EncodeTo (NSCoder encoder) {

    // Encode data
    encoder.Encode(new NSString(Name),"name");
    encoder.Encode(new NSString(ImageType),"imageType");
}
```

Пары отдельных ключ/значение записываются в кодировщик и расшифровки с помощью второй конструктор, который мы добавили выше.

Кроме того мы может включать следующий метод для определения параметров при записи данных в буфер:

```csharp
[Export ("writingOptionsForType:pasteboard:"), CompilerGenerated]
public virtual NSPasteboardWritingOptions GetWritingOptionsForType (string type, NSPasteboard pasteboard) {
    return NSPasteboardWritingOptions.WritingPromised;
}
```

Сейчас только `WritingPromised` параметр доступен и должен использоваться, если не были фактически записанных в буфер только обещают заданного типа. Дополнительные сведения см. в разделе [пообещали, что данные](#Promised_Data) предыдущем разделе.

С помощью этих методов в месте можно использовать следующий код для записи нашего пользовательского класса в буфер:

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Empty the current contents
pasteboard.ClearContents();

// Add info to the pasteboard
pasteboard.WriteObjects (new ImageInfo[] { Info });
```

#### <a name="reading-from-the-pasteboard"></a>Чтение из монтажного стола

Подчиняясь соглашению `INSPasteboardReading` интерфейс, нам нужно предоставить три метода, чтобы пользовательский класс данных может быть считано из монтажного стола.

Во-первых мы должны сообщить монтажного стола тип данных, представления, которые пользовательского класса могут быть считаны из буфера обмена:

```csharp
[Export ("readableTypesForPasteboard:")]
public static string[] GetReadableTypesForPasteboard (NSPasteboard pasteboard){
    string[] readableTypes = {"com.xamarin.image-info", "public.text"};
    return readableTypes;
}
```

Опять же, они определяются как простой Uti и имеют те же типы, определенные в **записи в буфер** предыдущем разделе.

Далее нам нужно сообщить монтажного стола _как_ каждого из типов UTI читается следующим образом:

```csharp
[Export ("readingOptionsForType:pasteboard:")]
public static NSPasteboardReadingOptions GetReadingOptionsForType (string type, NSPasteboard pasteboard) {

    // Take action based on the requested type
    switch (type) {
    case "com.xamarin.image-info":
        return NSPasteboardReadingOptions.AsKeyedArchive;
    case "public.text":
        return NSPasteboardReadingOptions.AsString;
    }

    // Default to property list
    return NSPasteboardReadingOptions.AsPropertyList;
}
```

Для `com.xamarin.image-info` типа, мы указываем монтажного стола для декодирования пару ключ значение, которое мы создали с помощью `NSKeyedArchiver` при написании класса в буфер путем вызова `initWithCoder:` конструктор, который мы добавили в класс.

Наконец нам нужно добавить следующий метод для чтения представления данных UTI из монтажного стола:

```csharp
[Export ("initWithPasteboardPropertyList:ofType:")]
public NSObject InitWithPasteboardPropertyList (NSObject propertyList, string type) {

    // Take action based on the requested type
    switch (type) {
    case "public.text":
        return new ImageInfo();
    }

    // Failure, return null
    return null;
}
```

Все эти методы на месте с пользовательский класс данных могут быть считаны из монтажного стола, используя следующий код:

```csharp
// Initialize the pasteboard
NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
var classArrayPtrs = new [] { Class.GetHandle (typeof(ImageInfo)) };
NSArray classArray = NSArray.FromIntPtrs (classArrayPtrs);

// NOTE: Sending messages directly to the base Objective-C API because of this defect:
// https://bugzilla.xamarin.com/show_bug.cgi?id=31760
// Check to see if image info is on the pasteboard
ok = bool_objc_msgSend_IntPtr_IntPtr (pasteboard.Handle, Selector.GetHandle ("canReadObjectForClasses:options:"), classArray.Handle, IntPtr.Zero);

if (ok) {
    // Read the image off of the pasteboard
    NSObject [] objectsToPaste = NSArray.ArrayFromHandle<Foundation.NSObject>(IntPtr_objc_msgSend_IntPtr_IntPtr (pasteboard.Handle, Selector.GetHandle ("readObjectsForClasses:options:"), classArray.Handle, IntPtr.Zero));
    ImageInfo info = (ImageInfo)objectsToPaste[0];
}
```

### <a name="using-a-nspasteboarditem"></a>С помощью NSPasteboardItem

Возможны ситуации, когда вам нужно написать пользовательские элементы в буфер, который не является основанием для создания пользовательского класса или вы хотите предоставить данные в общем формате, только при необходимости. В подобных случаях можно использовать `NSPasteboardItem`.

Объект `NSPasteboardItem` предоставляет детальный контроль над данными, которые записываются в буфер и предназначен для временного доступа — он должен быть удален после его записи в буфер.

#### <a name="writing-data"></a>Запись данных

Для записи пользовательских данных для `NSPasteboardItem` необходимо предоставить пользовательский `NSPasteboardItemDataProvider`. Добавьте новый класс в проект и назовите его **ImageInfoDataProvider.cs**. Измените файл и это должно выглядеть следующим образом:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacCopyPaste
{
    [Register("ImageInfoDataProvider")]
    public class ImageInfoDataProvider : NSPasteboardItemDataProvider
    {
        #region Computed Properties
        public string Name { get; set;}
        public string ImageType { get; set;}
        #endregion

        #region Constructors
        [Export ("init")]
        public ImageInfoDataProvider ()
        {
        }

        public ImageInfoDataProvider (string name, string imageType)
        {
            // Initialize
            this.Name = name;
            this.ImageType = imageType;
        }

        protected ImageInfoDataProvider (NSObjectFlag t){
        }

        protected internal ImageInfoDataProvider (IntPtr handle){

        }
        #endregion

        #region Override Methods
        [Export ("pasteboardFinishedWithDataProvider:")]
        public override void FinishedWithDataProvider (NSPasteboard pasteboard)
        {
            
        }

        [Export ("pasteboard:item:provideDataForType:")]
        public override void ProvideDataForType (NSPasteboard pasteboard, NSPasteboardItem item, string type)
        {

            // Take action based on the type
            switch (type) {
            case "public.text":
                // Encode the data to string 
                item.SetStringForType(string.Format("{0}.{1}", Name, ImageType),type);
                break;
            }

        }
        #endregion
    }
}
```

Как и в пользовательских классах данных, нам нужно использовать `Register` и `Export` директивы для представления этого Objective-C. Класс должен наследоваться от `NSPasteboardItemDataProvider` и должен реализовывать `FinishedWithDataProvider` и `ProvideDataForType` методы.

Используйте `ProvideDataForType` метод для предоставления данных, который будет упакован в `NSPasteboardItem` следующим образом:

```csharp
[Export ("pasteboard:item:provideDataForType:")]
public override void ProvideDataForType (NSPasteboard pasteboard, NSPasteboardItem item, string type)
{

    // Take action based on the type
    switch (type) {
    case "public.text":
        // Encode the data to string 
        item.SetStringForType(string.Format("{0}.{1}", Name, ImageType),type);
        break;
    }

}
```

В этом случае мы хранения два фрагмента информации о наш образ ("имя" и "ImageType") и записи их в простую строку (`public.text`).

Тип записи данных в буфер, используйте следующий код:

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Using a Pasteboard Item
NSPasteboardItem item = new NSPasteboardItem();
string[] writableTypes = {"public.text"};

// Add a data provider to the item
ImageInfoDataProvider dataProvider = new ImageInfoDataProvider (Info.Name, Info.ImageType);
var ok = item.SetDataProviderForTypes (dataProvider, writableTypes);

// Save to pasteboard
if (ok) {
    pasteboard.WriteObjects (new NSPasteboardItem[] { item });
}
```

#### <a name="reading-data"></a>Чтение данных

Для чтения данных от монтажного стола, используйте следующий код:

```csharp
// Initialize the pasteboard
NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
Class [] classArray  = { new Class ("NSImage") };

bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
if (ok) {
    // Read the image off of the pasteboard
    NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
    NSImage image = (NSImage)objectsToPaste[0];

    // Do something with data
    ...
}
            
Class [] classArray2 = { new Class ("ImageInfo") };
ok = pasteboard.CanReadObjectForClasses (classArray2, null);
if (ok) {
    // Read the image off of the pasteboard
    NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
    
    // Do something with data
    ...
}
```

## <a name="summary"></a>Сводка

В этой статье были предприняты никакие подробные принципы работы с монтажного стола в приложении Xamarin.Mac для поддержки копирования и вставки. Во-первых она введена простой пример для получения вы знакомы с операциями стандартный монтажных столах. Затем она заняла подробно рассматривались монтажного стола, а также способы чтения и записи данных из него. Наконец он рассмотрели Применение пользовательского типа данных для поддержки копирования и вставки сложных типов данных в приложении.



## <a name="related-links"></a>Связанные ссылки

- [MacCopyPaste (пример)](https://developer.xamarin.com/samples/mac/MacCopyPaste/)
- [Привет, Mac](~/mac/get-started/hello-mac.md)
- [Руководство по программированию на монтажном столе](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/PasteboardGuide106/Articles/pbGettingStarted.html)
- [macOS рекомендациям по интерфейсам](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
