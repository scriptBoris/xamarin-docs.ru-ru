---
title: Меню в Xamarin.Mac
description: В этой статье рассматривается работа с меню в приложении Xamarin.Mac. Он описывает создание и поддержание меню и пунктов меню в Xcode и конструкторе Interface Builder и также программным способом работы с ними.
ms.prod: xamarin
ms.assetid: 5D367F8E-3A76-4995-8A89-488530FAD802
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: d12815c92c1f608a5df4b3869fe9a17cb604b47a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109683"
---
# <a name="menus-in-xamarinmac"></a>Меню в Xamarin.Mac

_В этой статье рассматривается работа с меню в приложении Xamarin.Mac. Он описывает создание и поддержание меню и пунктов меню в Xcode и конструкторе Interface Builder и также программным способом работы с ними._

При работе с C# и .NET в приложении Xamarin.Mac, у вас есть доступ к тем же меню Cocoa, что разработчик, работающий в Objective-C и Xcode. Поскольку Xamarin.Mac напрямую интегрируется с Xcode, можно использовать конструктора Interface Builder для создания и поддержки строки меню, меню и пунктов меню (или при необходимости создать их непосредственно в коде C#).

Меню являются неотъемлемой частью приложения Mac взаимодействие с пользователем и обычно отображаются в различных частях пользовательского интерфейса:

- **Меню приложения** -это главного меню, которое отображается в верхней части экрана для каждого приложения Mac.
- **Контекстные меню** -они отображаются, когда пользователь щелкает правой кнопкой мыши или элемент в окне управления щелчков.
- **В строке состояния** -это область в крайней правой части панели меню приложения, который отображается в верхней части экрана (слева часов панели меню) и потом увеличилась до слева, так как элементы добавляются к нему.
- **Закрепить меню** -меню для каждого приложения на панели dock, появляется когда пользователь щелкает правой кнопкой мыши или элемент управления щелкает значок приложения, а также когда пользователь батарейки значок и удерживает кнопку мыши.
- **Кнопки, всплывающее и раскрывающихся списков** -всплывающее кнопка отображается выбранный элемент и выводит список параметров для использования при нажатии пользователем. Выпадающем списке — это разновидность всплывающее кнопки, обычно используется для выбора команды, относящиеся к контексту текущей задачи. Оба могут находиться в любом месте окна.

[![В примере меню](menu-images/intro01.png "в примере меню")](menu-images/intro01-large.png#lightbox)

В этой статье мы обсудим, что узнаете основы работы с меню Cocoa, меню и пунктов меню в приложении Xamarin.Mac. Настоятельно рекомендуется работать через [Привет, Mac](~/mac/get-started/hello-mac.md) статьи, во-первых, в частности [введение в Xcode и конструкторе Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) и [переменных экземпляров и действий](~/mac/get-started/hello-mac.md#outlets-and-actions) разделы, так как он рассматриваются основные понятия и методы, которые мы будем использовать в этой статье.

Может потребоваться взгляните на [предоставление C# классы / методы Objective-c](~/mac/internals/how-it-works.md) раздел [структуре Xamarin.Mac](~/mac/internals/how-it-works.md) документа, он объясняет `Register` и `Export` атрибуты используется для передачи классов C# в службе Objective-C объекты и элементы пользовательского интерфейса.

## <a name="the-applications-menu-bar"></a>Строка меню приложения 

В отличие от приложений, работающих в операционной системе Windows, где каждое окно может иметь свои собственные строки меню, подключенные к ней каждое приложение, работающее в macOS имеется один меню вдоль верхней части экрана, который используется для каждого окна в этом приложении:

[![Строка меню](menu-images/appmenu01.png "строку меню")](menu-images/appmenu01-large.png#lightbox)

Элементы на этой панели меню активируются или деактивируются исходя из текущего контекста или состояние приложения и его пользовательский интерфейс в каждый конкретный момент. Например: Если пользователь выбирает текстового поля, элементы на **изменить** меню будут поступать включена, такие как **копирования** и **Вырезать**.

В соответствии с Apple и по умолчанию все приложения macOS имеют стандартный набор меню и пунктов меню, которые отображаются в строке меню приложения:

- **Меню Apple** -это меню предоставляет доступ к системе расширенные элементы, доступные для пользователя все время, независимо от того, какие приложения. Эти элементы невозможно изменить разработчиком.
- **Меню приложения** -это меню отображает имя приложения полужирным шрифтом и помогает пользователю определить, какое приложение выполняется в данный момент. Он содержит элементы, которые применяются к приложению как единое целое и не данному документу или процесс, например выхода из приложения.
- **Меню "файл"** — использовать элементы для создания, открыть или сохранить документы, приложение работает с. Если приложения не основанные на документе, это меню может быть переименован или удален.
- **Меню "Правка"** -содержит команды, такие как **Вырезать**, **копирования**, и **вставить** которого используются для редактирования или изменения элементов в пользовательском интерфейсе приложения.
- **Меню «Формат»** — Если приложение работает с текстом, это меню содержит команды, чтобы изменить форматирование текста.
- **Меню "Вид"** -содержит команды, влияющие на способ отображения содержимого (Просмотр) в пользовательском интерфейсе приложения.
- **Меню приложения** -это любой меню, которые предназначены специально для вашего приложения (например, меню закладок для веб-браузера). Они должны отображаться между **представление** и **окно** меню в строке.
- **Меню "окно"** -команды для работы с окнами в приложении, а также список текущего открытых окон.
- **Меню "Справка"** -приложения, предоставляющего справки на экране, меню "Справка" должен быть меню справа на панели. 

Дополнительные сведения о строке меню приложения и стандартные меню и пункты меню, см. в разделе Apple [рекомендациям по интерфейсам](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/).

### <a name="the-default-application-menu-bar"></a>Строка меню приложения по умолчанию

Каждый раз при создании нового проекта Xamarin.Mac, вы автоматически получаете и стандартный, строка меню приложения по умолчанию, которая имеет типичных элементов, которые приложение macOS обычно имеет (как уже говорилось в предыдущем разделе). Строка меню приложения по умолчанию определяется в **Main.storyboard** файл (вместе с остальной части пользовательского интерфейса приложения) в проекте в **панели решения**:  

![Выберите основной раскадровки](menu-images/appmenu02.png "выберите основной раскадровки")

Дважды щелкните **Main.storyboard** файл, чтобы открыть его для редактирования в Interface Builder в Xcode и вам представится интерфейса редактора меню:

[![Изменение пользовательского интерфейса в Xcode](menu-images/defaultbar01.png "редактирование пользовательского интерфейса в Xcode")](menu-images/defaultbar01-large.png#lightbox)

Здесь можно щелкнуть элементы например **откройте** пункта меню в **файл** меню редактировать и изменять его свойства в **инспектор атрибутов**:

[![Изменив атрибуты меню](menu-images/defaultbar02.png "редактирования меню атрибутов")](menu-images/defaultbar02-large.png#lightbox)

Мы рассмотрим добавление, изменение и удаление меню и элементов, далее в этой статье. Для теперь мы просто хотите узнать, какие меню и пункты меню будут доступны по умолчанию и как они автоматически доступны в код с помощью набора стандартных переменных экземпляров и действий (Дополнительные сведения см. наш [переменных экземпляров и действий](~/mac/get-started/hello-mac.md#outlets-and-actions) Документация по).

Например, если мы щелкаем **инспектор подключения** для **откройте** пункта меню, мы видим, до автоматического связывания `openDocument:` действия: 

[![Просмотр вложенное действие](menu-images/defaultbar03.png "Просмотр вложенное действие")](menu-images/defaultbar03-large.png#lightbox)

При выборе **первый респондент** в **иерархия интерфейса** и прокрутите вниз содержимое **инспектор подключения**, и вы увидите определение `openDocument:` действие, **откройте** пункта меню подключен к (а также несколько другие действия по умолчанию для приложения и автоматически не отправлены элементы управления):

[![Просмотр всех присоединенных действия](menu-images/defaultbar04.png "Просмотр всех вложенных действий")](menu-images/defaultbar04-large.png#lightbox) 

Почему это важно? В следующем разделе будут видеть, как работают эти действия автоматически определяется с другими Cocoa элементам управления и автоматически включить и отключить пунктов меню, а также, предоставляя встроенные функциональные возможности для элементов.

Позже мы будем использовать эти встроенные действия для включения и отключения элементов из кода и предоставить собственные функции при их выборе.

<a name="Built-In_Menu_Functionality" />

### <a name="built-in-menu-functionality"></a>Функциональные возможности встроенного меню

Если созданное приложение Xamarin.Mac перед добавлением элементов пользовательского интерфейса и кода запуска, видно, что некоторые элементы автоматически привязав и являются для вас (с полностью автоматически встроенные функции), такие как **Quit** элемент **приложения** меню:

![Элемент меню включена](menu-images/appmenu03.png "элемент меню включена")

Хотя другие пункты меню, например **Вырезать**, **копирования**, и **вставить** не являются:

![Отключенных элементов меню](menu-images/appmenu04.png "отключенных элементов меню")

Давайте остановите приложение и дважды щелкните **Main.storyboard** файл **панели решения** чтобы открыть его для редактирования в Xcode конструктора Interface Builder. Затем перетащите **представления текста** из **библиотеки** на контроллер представления окна в **редактор интерфейса**:

[![Выбор представления текста из библиотеки](menu-images/appmenu05.png "выбора представления текста из библиотеки")](menu-images/appmenu05-large.png#lightbox)

В **Редактор ограничений** давайте закрепить представления текста для границы окна и задайте для него где растет и сжимается с окном, щелкнув все четыре red I-образных указателей в верхней части редактора и **добавить 4 ограничения** кнопки:

[![Изменение ограничений](menu-images/appmenu06.png "редактирования ограничений")](menu-images/appmenu06-large.png#lightbox)

Сохраните изменения пользовательского интерфейса и вернуться назад в Visual Studio для Mac для синхронизации изменений с проектом Xamarin.Mac. Теперь запустите приложение, введите текст в представление текста, выберите его и откройте **изменить** меню:

![Элементы меню автоматически включается или отключается](menu-images/appmenu07.png "пунктов меню автоматически включается или отключается")

Обратите внимание, что как **Вырезать**, **копирования**, и **вставить** элементы автоматически включенной и полностью работоспособной, не написав ни строчки кода. 

Что здесь происходит? Помните, встроенной заранее определить действия, которые перейдут в проводных до пунктов меню по умолчанию (поскольку представленные выше), большая часть элементов интерфейса пользователя Cocoa, которые являются частью macOS имеют встроенные обработчики на выполнение определенных действий (таких как `copy:`). Поэтому при их добавлении в окно активным и выборе соответствующего пункта меню или элементы, присоединенные к действия включаются автоматически. Если пользователь выбирает этот элемент меню, функций, встроенных в элемент пользовательского интерфейса вызывается и выполняется, не привлекая разработчиков.

### <a name="enabling-and-disabling-menus-and-items"></a>Включение и отключение меню и элементов

По умолчанию каждый раз при возникновении события пользователя `NSMenu` автоматически включает и отключает каждого отображается меню и меню элементов на основе контекста приложения. Чтобы включить или отключить элемент тремя способами:

- **Включение автоматического меню** -пункт меню доступен в том случае, если `NSMenu` можно найти соответствующий объект, который отвечает на действие, которое элемент является привязав для. Например, представление текста выше оказывали встроенный обработчик, чтобы `copy:` действие.
- **Настраиваемые действия и validateMenuItem:** — для любого элемента меню, привязанный к [окно или представление настраиваемого действия контроллера](#Working-with-Custom-Window-Actions), вы можете добавить `validateMenuItem:` действие и вручную включить или отключить пунктов меню.
- **Включение меню вручную** -пользователь вручную задаст `Enabled` свойства каждого `NSMenuItem` для включения или отключения каждого элемента в меню отдельно.

Чтобы выбрать систему, установите `AutoEnablesItems` свойство `NSMenu`. `true` выполняется автоматически (по умолчанию) и `false` выполняется вручную. 

> [!IMPORTANT]
> Если вы решили использовать вручную меню включения, ни один из меню элементы, даже те, которые контролируются AppKit классов, таких как `NSTextView`, обновляются автоматически. Вы будет нести ответственность за включение и отключение всех элементов вручную в коде.

#### <a name="using-validatemenuitem"></a>С помощью validateMenuItem

Как уже говорилось, для любого элемента меню, привязанный к [окна или пользовательское действие контроллера представления](#Working-with-Custom-Window-Actions), вы можете добавить `validateMenuItem:` действие и вручную включить или отключить пунктов меню.

В следующем примере `Tag` решить, тип элемента меню, который будет включить или отключить, будет использоваться свойство `validateMenuItem:` действия на основе состояния выбранного текста в `NSTextView`. `Tag` Свойства в конструкторе Interface Builder для каждого пункта меню:

![Задание свойства тега](menu-images/validate01.png "свойства тега")

И следующий код, добавляемый контроллер представления:

```csharp
[Action("validateMenuItem:")]
public bool ValidateMenuItem (NSMenuItem item) {

    // Take action based on the menu item type
    // (As specified in its Tag)
    switch (item.Tag) {
    case 1:
        // Wrap menu items should only be available if
        // a range of text is selected
        return (TextEditor.SelectedRange.Length > 0);
    case 2:
        // Quote menu items should only be available if
        // a range is NOT selected.
        return (TextEditor.SelectedRange.Length == 0);
    }

    return true;
}
```

При выполнении этого кода, и текст не выбран в `NSTextView`, пункты меню два переноса по словам отключены (даже если они привязаны к действиям в контроллере представления):

![Отключить отображение элементов](menu-images/validate02.png "отображение отключенных элементов")

Если выбран раздел текста и повторном открытии меню, два wrap пункты меню будут доступны:

![Включить отображение элементов](menu-images/validate03.png "отображение включены элементы")

## <a name="enabling-and-responding-to-menu-items-in-code"></a>Включение и отвечать на запросы к пунктам меню в коде

Как мы уже видели выше, просто добавив определенные элементы пользовательского интерфейса Cocoa для наших Дизайн пользовательского интерфейса (например, текстовое поле), несколько пунктов меню по умолчанию будут включены и функционируют автоматически без необходимости написания кода. Далее давайте рассмотрим добавление нашим собственным кодом C# для нашего проекта Xamarin.Mac для включения пункта меню и предоставляют функциональные возможности, когда пользователь выбирает его.

Например, допустим мы хотим, чтобы пользователь мог использовать **откройте** элемент **файл** меню, чтобы найти нужную папку. Так как мы это должно быть функцией уровня приложения, не ограниченной предоставляют окна или элемента пользовательского интерфейса, мы собираемся добавить код для обработки таких делегату наши приложения.

В **панели решения**, дважды щелкните `AppDelegate.CS` файл, чтобы открыть его для редактирования:

![Выбрав делегата приложения](menu-images/appmenu08.png "выбрав делегата приложения")

Добавьте следующий код ниже `DidFinishLaunching` метод:

```csharp
[Export ("openDocument:")]
void OpenDialog (NSObject sender)
{
    var dlg = NSOpenPanel.OpenPanel;
    dlg.CanChooseFiles = false;
    dlg.CanChooseDirectories = true;

    if (dlg.RunModal () == 1) {
        var alert = new NSAlert () {
            AlertStyle = NSAlertStyle.Informational,
            InformativeText = "At this point we should do something with the folder that the user just selected in the Open File Dialog box...",
            MessageText = "Folder Selected"
        };
        alert.RunModal ();
    }
}
```

Давайте запустите приложение и откройте **файл** меню: 

![Меню "файл"](menu-images/appmenu09.png "меню \"файл\"")

Обратите внимание, что **откройте** пункт меню включен. Если выбрать его, откройте диалоговое окно будет отображаться:

![Окно открытия](menu-images/appmenu10.png "окно открытия")

При нажатии кнопки **откройте** кнопку, отображается наших предупреждающее сообщение:

![Пример диалогового окна сообщения](menu-images/appmenu11.png "пример диалогового окна сообщения")

Строки ключа был `[Export ("openDocument:")]`, он сообщает `NSMenu` , наши **AppDelegate** имеет метод `void OpenDialog (NSObject sender)` , реагирующий на `openDocument:` действие. Если вы не забудете выше, **откройте** пункт меню является автоматически привязав к этому действию по умолчанию в конструкторе Interface Builder:

[![Просмотр вложенных действий](menu-images/defaultbar03.png "просмотра вложенных действий")](menu-images/defaultbar03-large.png#lightbox)

Далее давайте взглянем на создание собственных меню, пункты меню и действия, а также отвечать на запросы к ним в коде.

### <a name="working-with-the-open-recent-menu"></a>Работа с меню открытия последние

По умолчанию **файл** меню содержит **открыть последние** элемент, который следит за последние несколько файлов, которые пользователь открыл с вашим приложением. Если вы создаете `NSDocument` на основе приложения Xamarin.Mac, это меню будет обрабатываться для вас автоматически. Для любого другого типа приложения Xamarin.Mac будет отвечать за управление и вручную отвечать на этот пункт меню.

Чтобы вручную обрабатывать **открыть последние** меню, сначала необходимо будет уведомлять его об, что новый файл было открыто или сохранить, используя следующие:

```csharp
// Add document to the Open Recent menu
NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);
```

Несмотря на то, что приложение не использует `NSDocuments`, по-прежнему использовать `NSDocumentController` для поддержания **открыть последние** меню, отправляя `NSUrl` с расположением файла `NoteNewRecentDocumentURL` метод `SharedDocumentController`.

Далее необходимо переопределить `OpenFile` метод делегата приложения, чтобы открыть любой файл, который пользователь выбирает из **последние документы** меню. Пример:

```csharp
public override bool OpenFile (NSApplication sender, string filename)
{
    // Trap all errors
    try {
        filename = filename.Replace (" ", "%20");
        var url = new NSUrl ("file://"+filename);
        return OpenFile(url);
    } catch {
        return false;
    }
}
```

Вернуть `true` Если файл может быть открыт, в противном случае возвращает `false` и встроенные предупреждение будет отображаться для пользователя, что файл не удалось открыть.

Так как имя файла и путь возвращается из **открыть последние** меню может содержать пробелы, нам нужно правильным образом экранировать этот символ перед созданием `NSUrl` или мы будет показано сообщение об ошибке. Мы делаем, следующим кодом:

```csharp
filename = filename.Replace (" ", "%20");
```

Наконец, мы создаем `NSUrl` , указывает на файл и используйте вспомогательный метод в приложении делегировать откройте новое окно и загрузить файл в его:

```csharp
var url = new NSUrl ("file://"+filename);
return OpenFile(url);
```

Чтобы получить все вместе, давайте рассмотрим пример реализации в **AppDelegate.cs** файла:

```csharp
using AppKit;
using Foundation;
using System.IO;
using System;

namespace MacHyperlink
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public int NewWindowNumber { get; set;} = -1;
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

        public override bool OpenFile (NSApplication sender, string filename)
        {
            // Trap all errors
            try {
                filename = filename.Replace (" ", "%20");
                var url = new NSUrl ("file://"+filename);
                return OpenFile(url);
            } catch {
                return false;
            }
        }
        #endregion

        #region Private Methods
        private bool OpenFile(NSUrl url) {
            var good = false;

            // Trap all errors
            try {
                var path = url.Path;

                // Is the file already open?
                for(int n=0; n<NSApplication.SharedApplication.Windows.Length; ++n) {
                    var content = NSApplication.SharedApplication.Windows[n].ContentViewController as ViewController;
                    if (content != null && path == content.FilePath) {
                        // Bring window to front
                        NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
                        return true;
                    }
                }

                // Get new window
                var storyboard = NSStoryboard.FromName ("Main", null);
                var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

                // Display
                controller.ShowWindow(this);

                // Load the text into the window
                var viewController = controller.Window.ContentViewController as ViewController;
                viewController.Text = File.ReadAllText(path);
                viewController.SetLanguageFromPath(path);
                viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
                viewController.View.Window.RepresentedUrl = url;

                // Add document to the Open Recent menu
                NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);

                // Make as successful
                good = true;
            } catch {
                // Mark as bad file on error
                good = false;
            }

            // Return results
            return good;
        }
        #endregion

        #region actions
        [Export ("openDocument:")]
        void OpenDialog (NSObject sender)
        {
            var dlg = NSOpenPanel.OpenPanel;
            dlg.CanChooseFiles = true;
            dlg.CanChooseDirectories = false;

            if (dlg.RunModal () == 1) {
                // Nab the first file
                var url = dlg.Urls [0];

                if (url != null) {
                    // Open the document in a new window
                    OpenFile (url);
                }
            }
        }
        #endregion
    }
}
```

Зависимости от требований вашего приложения, может потребоваться пользователю открыть тот же файл в несколько окон, в то же время. В нашем приложении-примере если пользователь выбирает файл, который уже открыт (из **последние документы** или **откройте …** элементы меню), окно, содержащее файл переводится на передний план.

Для этого мы использовали в наших вспомогательный метод следующий код:

```csharp
var path = url.Path;

// Is the file already open?
for(int n=0; n<NSApplication.SharedApplication.Windows.Length; ++n) {
    var content = NSApplication.SharedApplication.Windows[n].ContentViewController as ViewController;
    if (content != null && path == content.FilePath) {
        // Bring window to front
        NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
        return true;
    }
}
```

Мы разработали наших `ViewController` класса, содержащего путь к файлу в его `Path` свойство. Далее мы обходим все открытые окна в приложении. Если файл уже открыт в одно из окон, она переводится в начале всех окон, с помощью:

```csharp
NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
```

Если совпадений не найдено, открывается новое окно с загруженный файл указан в **открыть последние** меню:

```csharp
// Get new window
var storyboard = NSStoryboard.FromName ("Main", null);
var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

// Display
controller.ShowWindow(this);

// Load the text into the window
var viewController = controller.Window.ContentViewController as ViewController;
viewController.Text = File.ReadAllText(path);
viewController.SetLanguageFromPath(path);
viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
viewController.View.Window.RepresentedUrl = url;

// Add document to the Open Recent menu
NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);
```

<a name="Working-with-Custom-Window-Actions" />

### <a name="working-with-custom-window-actions"></a>Работа с действиями пользовательского окна

Так же, как встроенный **первый респондент** действия, которые приходят предварительно проводных в стандартных элементов меню, можно создать настраиваемые действия и привязать их к пунктам меню в конструктор Interface Builder.

Во-первых определение пользовательского действия, на одном из контроллеров окна приложения. Пример:

```csharp
[Action("defineKeyword:")]
public void defineKeyword (NSObject sender) {
    // Preform some action when the menu is selected
    Console.WriteLine ("Request to define keyword");
}
```

Далее дважды щелкните файл раскадровки приложения в **панели решения** чтобы открыть его для редактирования в Xcode конструктора Interface Builder. Выберите **первый респондент** под **сцены приложения**, перейдите в **инспектор атрибутов**:

![Инспектор атрибутов](menu-images/action01.png "инспекторе атрибутов")

Нажмите кнопку **+** кнопки в нижней части **инспектор атрибутов** для добавления нового настраиваемого действия:

![Добавив новое действие](menu-images/action02.png "добавив новое действие")

Присвойте ему имя, совпадающее с именем настраиваемое действие, которое вы создали на контроллере окна:

![Изменение имени действия](menu-images/action03.png "редактирования имя действия")

Щелкните и перетащите из пункта меню, чтобы **первый респондент** под **сцены приложения**. В раскрывающемся списке выберите только что созданный объект action (`defineKeyword:` в этом примере):

![Присоединение действие](menu-images/action04.png "присоединение действие")

Сохранить изменения в раскадровку и вернитесь в Visual Studio для Mac синхронизировать изменения. Если вы запустите приложение, пункт меню, который вы подключили настраиваемого действия будет автоматически включить или отключить (с учетом окна с помощью действия, как окно было открыто) и выбора элемента меню запустит действие:

[![Тестирование нового действия](menu-images/action05.png "тестирование нового действия")](menu-images/action05-large.png#lightbox)

<a name="Adding,_Editing_and_Deleting_Menus" />

### <a name="adding-editing-and-deleting-menus"></a>Добавление, редактирование и удаление меню

Как мы уже видели в предыдущих разделах, в состав указанное количество стандартных меню и команды меню, которые автоматически активировать и отвечать на определенные элементы управления пользовательского интерфейса приложения Xamarin.Mac. Также мы видели, как добавить код в наше приложение, которое позволит также отвечать на эти элементы по умолчанию.

В этом разделе мы рассмотрим удаление элементов меню, которые нам не нужен, реорганизация меню и добавление новых меню, пункты меню и действия.

Дважды щелкните **Main.storyboard** файл **панели решения** чтобы открыть его для редактирования:

[![Изменение пользовательского интерфейса в Xcode](menu-images/maint01.png "редактирование пользовательского интерфейса в Xcode")](menu-images/maint01-large.png#lightbox)

Для конкретного приложения Xamarin.Mac не будет использоваться значение по умолчанию **представление** меню, поэтому мы собираемся удалить его. В **иерархия интерфейса** выберите **представление** пункт меню, который входит в строку главного меню:

![Выбрав элемент меню представления](menu-images/maint02.png "выбора элемента меню представления")

Нажмите клавишу delete или клавиши backspace, чтобы удалить меню. Далее мы не собираемся использовать все элементы в **формат** меню и нужно переместить элементы, мы собираемся использовать из вложенного меню. В **иерархия интерфейса** выберите следующие пункты меню:

![Выделение нескольких элементов](menu-images/maint03.png "выделение нескольких элементов")

Перетащите элементы в папке родительского **меню** во вложенном меню, где они в настоящее время:

[![Перетаскивания элементов меню в меню родительского](menu-images/maint04.png "перетаскивания элементов меню в меню \"родительский\"")](menu-images/maint04-large.png#lightbox)

Меню теперь должен выглядеть так:

[![Элементы в новом расположении](menu-images/maint05.png "элементы в новом расположении")](menu-images/maint05-large.png#lightbox)

Далее давайте перетащим **текст** подменю out из **формат** меню и поместите его в строку главного меню между **формат** и **окно** меню:

[![Меню текстового](menu-images/maint06.png "текст меню")](menu-images/maint06-large.png#lightbox)

Вернемся назад в разделе **формат** меню и delete **шрифта** элемент подменю. Затем выберите **формат** меню и переименуйте его «Шрифт»:

[![В меню шрифта](menu-images/maint07.png "меню шрифта")](menu-images/maint07-large.png#lightbox)

Теперь давайте создадим пользовательское меню стандартное фраз, которые автоматически получить добавляется к тексту в представлении текста при их выборе. В поле поиска в нижней части **инспектор библиотеки** типа в «menu». Это упростит поиск и работать со всеми элементами пользовательского интерфейса меню:

![В инспекторе библиотеки](menu-images/maint08.png "в инспекторе библиотеки")

Теперь давайте выполните следующую команду, чтобы создать наш меню:

1. Перетащите **пункта меню** из **инспектор библиотеки** на панель меню между **текст** и **окно** меню: 

    ![Выбрав элемент меню в библиотеке](menu-images/maint10.png "выбора элемента меню в библиотеке")
2. Переименуйте элемент «Фразы»: 

    [![Задание имени меню](menu-images/maint09.png "задание имени меню")](menu-images/maint09-large.png#lightbox)
3. Затем перетащите **меню** из **инспектор библиотеки**: 

    ![Меню из библиотеки](menu-images/maint11.png "меню из библиотеки")
4. Затем удалите **меню** на новом **пункта меню** мы только что создали и измените ее имя на «Фразы»: 

    [![Изменение имени меню](menu-images/maint12.png "изменение имени меню")](menu-images/maint12-large.png#lightbox)
5. Теперь переименуем трех стандартных **пунктов меню** «Address», «Дата» и «Greeting»: 

    [![В меню фраз](menu-images/maint13.png "меню фраз")](menu-images/maint13-large.png#lightbox)
6. Давайте добавим четвертый **пункта меню** , перетащив **пункта меню** из **инспектор библиотеки** и вызов его «Подпись»: 

    [![Изменение имени элемента меню](menu-images/maint14.png "редактирования имя пункта меню")](menu-images/maint14-large.png#lightbox)
7. Сохраните изменения в строке меню.

Теперь давайте создадим набор пользовательских действий, чтобы наши новые элементы меню предоставляются коду C#. В Xcode перейдем к **Assistant** представления:

[![Создание необходимых действиях](menu-images/maint15.png "Создание необходимые действия")](menu-images/maint15-large.png#lightbox)

Давайте, сделайте следующее:

1. Control, перетащите **адрес** пункт меню, чтобы **AppDelegate.h** файла.
2. Коммутатор **подключения** тип **действие**: 

    [![Выбор типа действия](menu-images/maint17.png "Выбор типа действия")](menu-images/maint17-large.png#lightbox)
3. Введите **имя** «phraseAddress» и нажмите клавишу **Connect** кнопку, чтобы создать новое действие: 

    [![Настройка действия](menu-images/maint18.png "Настройка действия")](menu-images/maint18-large.png#lightbox)
4. Повторите приведенные выше действия для **даты**, **приветствия**, и **подпись** пункты меню: 

    [![Выполненные действия](menu-images/maint19.png "выполненные действия")](menu-images/maint19-large.png#lightbox)
5. Сохраните изменения в строке меню.

Далее нам нужно создать выхода нашего представления текста, что мы уточнить его содержимое из кода. Выберите **ViewController.h** файл **вспомогательном редакторе** и создания новой переменной экземпляра вызывается `documentText`:

[![Создание переменной экземпляра](menu-images/maint20.png "Создание переменной экземпляра")](menu-images/maint20-large.png#lightbox)

Вернитесь к Visual Studio для Mac для синхронизации изменений из Xcode. Далее изменить **ViewController.cs** файл и это должно выглядеть следующим образом:

```csharp
using System;

using AppKit;
using Foundation;

namespace MacMenus
{
    public partial class ViewController : NSViewController
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        public override NSObject RepresentedObject {
            get {
                return base.RepresentedObject;
            }
            set {
                base.RepresentedObject = value;
                // Update the view, if already loaded.
            }
        }

        public string Text {
            get { return documentText.Value; }
            set { documentText.Value = value; }
        } 
        #endregion

        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any additional setup after loading the view.
        }

        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            App.textEditor = this;
        }

        public override void ViewWillDisappear ()
        {
            base.ViewDidDisappear ();

            App.textEditor = null;
        }
        #endregion
    }
}
```

Этот класс представляет текст нашего представления текста за пределами `ViewController` класса, но и уведомляет делегата приложения, когда окно Получает или теряет фокус. Теперь редактировать **AppDelegate.cs** файл и это должно выглядеть следующим образом:

```csharp
using AppKit;
using Foundation;
using System;

namespace MacMenus
{
    [Register ("AppDelegate")]
    public partial class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public ViewController textEditor { get; set;} = null;
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

        #region Custom actions
        [Export ("openDocument:")]
        void OpenDialog (NSObject sender)
        {
            var dlg = NSOpenPanel.OpenPanel;
            dlg.CanChooseFiles = false;
            dlg.CanChooseDirectories = true;

            if (dlg.RunModal () == 1) {
                var alert = new NSAlert () {
                    AlertStyle = NSAlertStyle.Informational,
                    InformativeText = "At this point we should do something with the folder that the user just selected in the Open File Dialog box...",
                    MessageText = "Folder Selected"
                };
                alert.RunModal ();
            }
        }

        partial void phrasesAddress (Foundation.NSObject sender) {

            textEditor.Text += "Xamarin HQ\n394 Pacific Ave, 4th Floor\nSan Francisco CA 94111\n\n";
        }

        partial void phrasesDate (Foundation.NSObject sender) {

            textEditor.Text += DateTime.Now.ToString("D");
        }

        partial void phrasesGreeting (Foundation.NSObject sender) {

            textEditor.Text += "Dear Sirs,\n\n";
        }

        partial void phrasesSignature (Foundation.NSObject sender) {

            textEditor.Text += "Sincerely,\n\nKevin Mullins\nXamarin,Inc.\n";
        }
        #endregion
    }
}
```

Здесь мы приняли `AppDelegate` разделяемый класс, чтобы мы могли использовать действия и источники, которые мы определили в конструктор Interface Builder. Мы также предоставляем `textEditor` для отслеживания того, какое окно находится в фокусе.

Следующие методы используются для обработки наших пользовательских меню и пункты меню:

```csharp
partial void phrasesAddress (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += "Xamarin HQ\n394 Pacific Ave, 4th Floor\nSan Francisco CA 94111\n\n";
}

partial void phrasesDate (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += DateTime.Now.ToString("D");
}

partial void phrasesGreeting (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += "Dear Sirs,\n\n";
}

partial void phrasesSignature (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += "Sincerely,\n\nKevin Mullins\nXamarin,Inc.\n";
}
```

Теперь при выполнении приложения, все элементы в **фразу** меню будет активна и добавит в представление текста, при выборе фразу предоставляют:

![Пример приложения, выполняемого](menu-images/maint21.png "пример приложения, выполняемого")

Теперь, когда у нас есть основы работы с меню приложения, работу, давайте взглянем на создание настраиваемых контекстных меню.

### <a name="creating-menus-from-code"></a>Создание меню из кода

Помимо создания меню и пунктов меню с помощью конструктора Interface Builder, возможны ситуации, когда приложения Xamarin.Mac нужно создать, изменить или удалить меню, подменю или пункта меню из кода.

В следующем примере класс создается для хранения сведений о элементы меню и подменю, которые будут динамически создаваться в режиме реального времени:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace AppKit.TextKit.Formatter
{
    public class LanguageFormatCommand : NSObject
    {
        #region Computed Properties
        public string Title { get; set; } = "";
        public string Prefix { get; set; } = "";
        public string Postfix { get; set; } = "";
        public List<LanguageFormatCommand> SubCommands { get; set; } = new List<LanguageFormatCommand>();
        #endregion

        #region Constructors
        public LanguageFormatCommand () {

        }

        public LanguageFormatCommand (string title)
        {
            // Initialize
            this.Title = title;
        }

        public LanguageFormatCommand (string title, string prefix)
        {
            // Initialize
            this.Title = title;
            this.Prefix = prefix;
        }

        public LanguageFormatCommand (string title, string prefix, string postfix)
        {
            // Initialize
            this.Title = title;
            this.Prefix = prefix;
            this.Postfix = postfix;
        }
        #endregion
    }
}
```

#### <a name="adding-menus-and-items"></a>Добавление меню и элементов

С этим классом, который определен, следующая процедура будет анализировать коллекцию `LanguageFormatCommand`объектов и рекурсивно сборки путем их добавления к нижней части существующего меню (созданные в построителе интерфейса), который был передан в новых меню и пункты меню:

```csharp
private void AssembleMenu(NSMenu menu, List<LanguageFormatCommand> commands) {
    NSMenuItem menuItem;

    // Add any formatting commands to the Formatting menu
    foreach (LanguageFormatCommand command in commands) {
        // Add separator or item?
        if (command.Title == "") {
            menuItem = NSMenuItem.SeparatorItem;
        } else {
            menuItem = new NSMenuItem (command.Title);

            // Submenu?
            if (command.SubCommands.Count > 0) {
                // Yes, populate submenu
                menuItem.Submenu = new NSMenu (command.Title);
                AssembleMenu (menuItem.Submenu, command.SubCommands);
            } else {
                // No, add normal menu item
                menuItem.Activated += (sender, e) => {
                    // Apply the command on the selected text
                    TextEditor.PerformFormattingCommand (command);
                };
            }
        }
        menu.AddItem (menuItem);
    }
}
``` 

Для любого `LanguageFormatCommand` объект, имеющий пустое `Title` свойство, эта процедура создает **пункт меню разделителя** (тонкой серой линии) между разделами меню:

```csharp
menuItem = NSMenuItem.SeparatorItem;
```

Если заголовок не указан, создается новый элемент меню с этим заголовком:

```csharp
menuItem = new NSMenuItem (command.Title);
``` 

Если `LanguageFormatCommand` содержит дочерний объект `LanguageFormatCommand` объекты, созданные подменю и `AssembleMenu` метод будет вызываться рекурсивно для построения этого меню:

```csharp
menuItem.Submenu = new NSMenu (command.Title);
AssembleMenu (menuItem.Submenu, command.SubCommands);
```

Для любой новый пункт меню, который не имеет дочерних меню будет добавлен код для обработки выбранного пользователем элемента меню:

```csharp
menuItem.Activated += (sender, e) => {
    // Do something when the menu item is selected
    ...
};
```

#### <a name="testing-the-menu-creation"></a>Тестирование Создание меню

Со всеми приведенный выше код в месте Если следующую подборку `LanguageFormatCommand` объекты были созданы:

```csharp
// Define formatting commands
FormattingCommands.Add(new LanguageFormatCommand("Stong","**","**"));
FormattingCommands.Add(new LanguageFormatCommand("Emphasize","_","_"));
FormattingCommands.Add(new LanguageFormatCommand("Inline Code","`","`"));
FormattingCommands.Add(new LanguageFormatCommand("Code Block","```\n","\n```"));
FormattingCommands.Add(new LanguageFormatCommand("Comment","<!--","-->"));
FormattingCommands.Add (new LanguageFormatCommand ());
FormattingCommands.Add(new LanguageFormatCommand("Unordered List","* "));
FormattingCommands.Add(new LanguageFormatCommand("Ordered List","1. "));
FormattingCommands.Add(new LanguageFormatCommand("Block Quote","> "));
FormattingCommands.Add (new LanguageFormatCommand ());

var Headings = new LanguageFormatCommand ("Headings");
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 1","# "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 2","## "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 3","### "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 4","#### "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 5","##### "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 6","###### "));
FormattingCommands.Add (Headings);

FormattingCommands.Add(new LanguageFormatCommand ());
FormattingCommands.Add(new LanguageFormatCommand("Link","[","]()"));
FormattingCommands.Add(new LanguageFormatCommand("Image","![]("-")"));
FormattingCommands.Add(new LanguageFormatCommand("Image Link","[ ![]("-")](linkimagehere/index.md)"));
```

И передачу коллекции `AssembleMenu` функции (с **формат** меню в качестве базового), будут создаваться следующие динамические меню и пункты меню:

![Новые элементы меню в запущенном приложении](menu-images/dynamic01.png "новые элементы меню в запущенном приложении")

#### <a name="removing-menus-and-items"></a>Удаление меню и элементов

Если вам нужно удалить из пользовательского интерфейса приложения пункт меню или пункта меню, можно использовать `RemoveItemAt` метод `NSMenu` класс ей, начинающийся с нуля основаны на использовании индекс удаляемого элемента.

Например чтобы удалить меню и элементы меню, созданные процедурой выше, можно использовать следующий код:

```csharp
public void UnpopulateFormattingMenu(NSMenu menu) {

    // Remove any additional items
    for (int n = (int)menu.Count - 1; n > 4; --n) {
        menu.RemoveItemAt (n);
    }
}
```

В случае приведенный выше код первые четыре меню элементы создаются в построителе интерфейса Xcode и размещения, доступные в приложении, поэтому они не будут удалены динамически.

<a name="Contextual_Menus" />

## <a name="contextual-menus"></a>Контекстные меню

Контекстные меню появляются, когда пользователь щелкает правой кнопкой мыши или элемент в окне управления щелчков. По умолчанию некоторые элементы пользовательского интерфейса, встроенные в macOS, уже имеют контекстных меню, присоединенными к ним (например, представление «текст»). Однако возможны ситуации, когда мы хотим создать собственные настраиваемые контекстных меню для элемента пользовательского интерфейса, который мы добавили в окно.

Изменим наш **Main.storyboard** в Xcode и добавьте **окно** окно в нашей структуре его **класс** для «NSPanel» в **инспектор удостоверений**, добавьте новый **Assistant** элемент **окно** меню и присоединить ее к нового окна с помощью **Показать Segue**:

[![Выбор типа перехода](menu-images/context01.png "Выбор типа перехода")](menu-images/context01-large.png#lightbox)

Давайте, сделайте следующее:

1. Перетащите **метка** из **инспектор библиотеки** на **панели** окна и задает его текст «Property»: 

    [![Редактирование метки значение](menu-images/context03.png "редактирования метки значение")](menu-images/context03-large.png#lightbox)
2. Затем перетащите **меню** из **инспектор библиотеки** на контроллера представления в иерархию представлений и элементы меню по умолчанию переименования три **документа**, **текста**  и **шрифта**:

    [![Пункты меню требуется](menu-images/context02.png "пункты меню требуется")](menu-images/context02-large.png#lightbox)
3. Теперь элемент управления, перетащите из **метка свойства** на **меню**:

    [![Перетаскивание для создания объекта перехода](menu-images/context04.png "перетаскивание для создания объекта перехода")](menu-images/context04-large.png#lightbox)
4. В появившемся диалоговом окне выберите **меню**: 

    ![Выбор типа перехода](menu-images/context05.png "Выбор типа перехода")
5. Из **инспектор удостоверений**, класс контроллера представления для «PanelViewController» set: 

    [![Класс segue параметров](menu-images/context10.png "segue-класс параметров")](menu-images/context10-large.png#lightbox)
6. Вернитесь в Visual Studio для Mac для синхронизации, а затем вернуться в конструктор Interface Builder.
7. Переключиться в режим **вспомогательном редакторе** и выберите **PanelViewController.h** файла.
8. Создайте действие для **документа** пункта меню вызывается `propertyDocument`: 

    [![Настройка действия](menu-images/context06.png "Настройка действия")](menu-images/context06-large.png#lightbox)
9. Повторите создание действия для оставшихся элементов меню: 

    [![Необходимые действия](menu-images/context07.png "необходимые действия")](menu-images/context07-large.png#lightbox)
10. Наконец, создайте выхода для **метка свойства** вызывается `propertyLabel`: 

    [![Настройка выхода](menu-images/context08.png "Настройка выхода")](menu-images/context08-large.png#lightbox)
11. Сохранить изменения и вернуться в Visual Studio для Mac синхронизировать с Xcode.

Изменить **PanelViewController.cs** файл и добавьте следующий код:

```csharp
partial void propertyDocument (Foundation.NSObject sender) {
    propertyLabel.StringValue = "Document";
}

partial void propertyFont (Foundation.NSObject sender) {
    propertyLabel.StringValue = "Font";
}

partial void propertyText (Foundation.NSObject sender) {
    propertyLabel.StringValue = "Text";
}
```

Теперь Если запустить приложение и щелкните правой кнопкой мыши на свойство метке на панели, мы увидим наш пользовательского контекстного меню. Если выбрать элемент и элемент меню, изменится значение метки:

![Контекстные меню под управлением](menu-images/context09.png "контекстные меню под управлением")

Далее давайте попробуем создать меню панели состояния.

## <a name="status-bar-menus"></a>Меню панели состояния

Меню панели состояние отображения коллекции элементов состояния команды меню, которые обеспечивают взаимодействие с или отзыв для пользователя, например меню или изображение, отражая состояние приложения. Меню строки состояния приложения включении и активации, даже если приложение выполняется в фоновом режиме. В строке состояния всей системы находится в правой части панели меню приложения и является только строки состояния, в настоящее время доступны в macOS.

Изменим наш **AppDelegate.cs** и создайте `DidFinishLaunching` метод выглядят следующим:

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    // Create a status bar menu
    NSStatusBar statusBar = NSStatusBar.SystemStatusBar;

    var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);
    item.Title = "Text";
    item.HighlightMode = true;
    item.Menu = new NSMenu ("Text");

    var address = new NSMenuItem ("Address");
    address.Activated += (sender, e) => {
        PhraseAddress(address);
    };
    item.Menu.AddItem (address);

    var date = new NSMenuItem ("Date");
    date.Activated += (sender, e) => {
        PhraseDate(date);
    };
    item.Menu.AddItem (date);

    var greeting = new NSMenuItem ("Greeting");
    greeting.Activated += (sender, e) => {
        PhraseGreeting(greeting);
    };
    item.Menu.AddItem (greeting);

    var signature = new NSMenuItem ("Signature");
    signature.Activated += (sender, e) => {
        PhraseSignature(signature);
    };
    item.Menu.AddItem (signature);
}
```

`NSStatusBar statusBar = NSStatusBar.SystemStatusBar;` дает нам доступ к строке состояния всей системы. `var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);` Создает новый элемент строки состояния. Здесь мы создается меню и количество элементов меню и вложите меню элемент строки состояния, который мы только что создали. 

Если запустить приложение, отображается новый элемент строки состояния. Выбор элемента из меню изменится текст в представлении текста: 

![В меню панели состояния под управлением](menu-images/statusbar01.png "под управлением меню строки состояния")

Теперь давайте взглянем на создании пользовательских закрепления элементов меню.

## <a name="custom-dock-menus"></a>Меню пользовательских dock

Всплывающего меню отображается для приложения Mac, когда пользователь щелкает правой кнопкой мыши или элемент управления щелкает значок приложения на панели закрепления:

![Пользовательский закрепить меню](menu-images/dock01.png "настраиваемого закрепления меню")

Давайте создадим пользовательский всплывающего меню для нашего приложения следующим образом:

1. В Visual Studio для Mac, щелкните правой кнопкой мыши на проекте приложения и выберите команду **добавить** > **новый файл...** Диалоговое окно создания файла выберите **Xamarin.Mac** > **пустое определение интерфейса**, используйте «DockMenu» для **имя** и нажмите кнопку **New**  кнопку, чтобы создать новый **DockMenu.xib** файла:

    ![Добавление пустое определение интерфейса](menu-images/dock02.png "Добавление пустое определение интерфейса")
2. В **панели решения**, дважды щелкните **DockMenu.xib** файл, чтобы открыть его для редактирования в Xcode. Создайте новый **меню** со следующими элементами: **адрес**, **даты**, **приветствия**, и **подписи** 

    [![Размещение пользовательского интерфейса](menu-images/dock03.png "списочном пользовательского интерфейса")](menu-images/dock03-large.png#lightbox)
3. Далее подключим к нашей существующих действий, которые мы создали для наших пользовательских меню в наших новых пунктов меню [Добавление, редактирование и удаление меню](#Adding,_Editing_and_Deleting_Menus) предыдущем разделе. Переключиться в режим **инспектор подключения** и выберите **первый респондент** в **иерархия интерфейса**. Прокрутите вниз и найдите `phraseAddress:` действие. Перетащите линию от круг на это действие для **адрес** пункта меню:

    [![Перетаскивание пишем действие](menu-images/dock04.png "перетаскивания пишем действие")](menu-images/dock04-large.png#lightbox)
4. Повторите для всех остальных пунктов меню, их присоединения к их соответствующих действий: 

    [![Необходимые действия](menu-images/dock05.png "необходимые действия")](menu-images/dock05-large.png#lightbox)
5. Затем выберите **приложения** в **иерархия интерфейса**. В **инспектор подключения**, перетащите линию от круг `dockMenu` розетки, чтобы меню, мы только что создали:

    [![Перетаскивание установление розетки](menu-images/dock06.png "перетаскивания установление переменной экземпляра")](menu-images/dock06-large.png#lightbox)
6. Сохраните изменения и вернитесь в Visual Studio для Mac синхронизировать с Xcode.
7. Дважды щелкните **Info.plist** файл, чтобы открыть его для редактирования: 

    [![Редактирование файла info.plist](menu-images/dock07.png "Editing the Info.plist file")](menu-images/dock07-large.png#lightbox)
8. Нажмите кнопку **источника** вкладку в нижней части экрана: 

    [![Выбор представления источника](menu-images/dock08.png "Выбор представления источников")](menu-images/dock08-large.png#lightbox)
9. Нажмите кнопку **добавьте новую запись**, нажмите кнопку в форме плюса зеленым, задайте имя свойства в «AppleDockMenu» и значение для «DockMenu» (имя наш новый файл .xib без расширения): 

    [![Добавление элемента DockMenu](menu-images/dock09.png "добавления элемента DockMenu")](menu-images/dock09-large.png#lightbox)

Теперь если мы запустить наше приложение, щелкните правой кнопкой мыши на значок на панели Dock появится наших новых пунктов меню:

![Пример всплывающего меню под управлением](menu-images/dock10.png "пример всплывающего меню под управлением")

Если мы выбираем одну из пользовательских элементов в меню, текст в наши представления текста будет изменен.

<a name="Pop-up_Menus_and_Pull-Down_Lists" />

## <a name="pop-up-button-and-pull-down-lists"></a>Кнопки, всплывающее и раскрывающихся списков

Всплывающая кнопка отображается выбранный элемент и представляет список параметров для использования при нажатии пользователем. Выпадающем списке — это разновидность всплывающее кнопки, обычно используется для выбора команды, относящиеся к контексту текущей задачи. Оба могут находиться в любом месте окна.

Давайте создадим пользовательскую кнопку всплывающего для нашего приложения следующим образом:

1. Изменить **Main.storyboard** файл в Xcode и перетащите **кнопку всплывающего окна** из **инспектор библиотеки** на **панели** мы создали в окно [контекстных меню](#Contextual_Menus) раздел: 

    [![Добавление кнопки всплывающее окно](menu-images/popup01.png "Добавление кнопки всплывающего окна")](menu-images/popup01-large.png#lightbox)
2. Добавить новый элемент меню и задать заголовки элементов в панель: **адрес**, **даты**, **приветствия**, и **подписи** 

    [![Настройка пунктов меню](menu-images/popup02.png "Настройка пунктов меню")](menu-images/popup02-large.png#lightbox)
3. Далее подключим наших новых пунктов меню для существующего действия, которые мы создали для наших пользовательских меню в [Добавление, редактирование и удаление меню](#Adding,_Editing_and_Deleting_Menus) предыдущем разделе. Переключиться в режим **инспектор подключения** и выберите **первый респондент** в **иерархия интерфейса**. Прокрутите вниз и найдите `phraseAddress:` действие. Перетащите линию от круг на это действие для **адрес** пункта меню: 

    [![Перетаскивание пишем действие](menu-images/popup03.png "перетаскивания пишем действие")](menu-images/popup03-large.png#lightbox)
4. Повторите для всех остальных пунктов меню, их присоединения к их соответствующих действий: 

    [![Все необходимые действия](menu-images/popup04.png "все необходимые действия")](menu-images/popup04-large.png#lightbox)
5. Сохраните изменения и вернитесь в Visual Studio для Mac синхронизировать с Xcode.

Теперь Если запустить наше приложение и выбрать элемент из всплывающего окна, будет изменен текст в наши представления текста:

![Пример всплывающего окна под управлением](menu-images/popup05.png "пример запуска всплывающего окна")

Можно создать и работать с помощью раскрывающихся списков в точно так же как всплывающее кнопки. Вместо присоединения существующего действия, можно создать собственные настраиваемые действия так же как это делалось для наших контекстные меню в [контекстных меню](#Contextual_Menus) раздел.

## <a name="summary"></a>Сводка

В этой статье были предприняты никакие подробные принципы работы с меню и пунктов меню в приложении Xamarin.Mac. Сначала мы рассмотрели меню приложения, то мы рассмотрели создание контекстных меню, затем мы рассмотрели состояния панель меню и пользовательских закрепление меню. Наконец мы рассматривали, всплывающих меню и раскрывающихся списков.


## <a name="related-links"></a>Связанные ссылки

- [MacMenus (пример)](https://developer.xamarin.com/samples/mac/MacMenus/)
- [Привет, Mac](~/mac/get-started/hello-mac.md)
- [Рекомендации по работе с человеческим интерфейсом - меню](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/)
- [Общие сведения о меню и раскрывающиеся списки](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/MenuList/MenuList.html)
