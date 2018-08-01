---
title: Меню в Xamarin.Mac
description: В этой статье рассматривается работа с меню в приложении Xamarin.Mac. Здесь описывается создание и обслуживание меню и элементов меню в Xcode и интерфейс построителя и работа с ними программным способом.
ms.prod: xamarin
ms.assetid: 5D367F8E-3A76-4995-8A89-488530FAD802
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: cb89d1df60bafe14dcc989666f0eeb5d757e4017
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792925"
---
# <a name="menus-in-xamarinmac"></a>Меню в Xamarin.Mac

_В этой статье рассматривается работа с меню в приложении Xamarin.Mac. Здесь описывается создание и обслуживание меню и элементов меню в Xcode и интерфейс построителя и работа с ними программным способом._

При работе с C# и .NET в приложении Xamarin.Mac, имеется доступ к меню Cocoa же, как это делает разработчик, работающий в Objective-C и Xcode. Поскольку Xamarin.Mac напрямую интегрируется с Xcode, можно использовать построитель интерфейс Xcode создавать и обрабатывать строки меню, меню и команды меню (или при необходимости их непосредственно в коде C#).

Меню являются неотъемлемой частью приложения Mac взаимодействие с пользователем и обычно отображаются в пользовательском интерфейсе различных частей:

- **Строка меню приложения** -это главного меню, которое отображается в верхней части экрана для каждого приложения Mac.
- **Контекстные меню** -они отображаются, когда пользователь щелкает правой кнопкой мыши или элемент в окне управления щелчками.
- **В строке состояния** -это область в крайней правой части панели меню приложения, отображается в верхней части экрана (слева часов панели меню) и увеличения слева, как к нему добавляются элементы.
- **Закрепить меню** -появляется меню для каждого приложения в дока, когда пользователь щелкает правой кнопкой мыши или управления щелкает значок приложения, или когда пользователь батарейки значок и удерживает кнопку мыши.
- **Всплывающая кнопка и раскрывающихся списков** -всплывающая кнопка отображается выбранный элемент и выводит список параметров для выбора при щелчке пользователем. Выпадающем списке является типом всплывающих кнопки, обычно используется для выбора команд, определенных в контексте текущей задачи. Оба могут находиться в любом месте окна.

[![Пример меню](menu-images/intro01.png "пример меню")](menu-images/intro01-large.png#lightbox)

В этой статье мы обсудим основы работы с Cocoa строки меню, меню и элементов меню в приложении Xamarin.Mac. Настоятельно рекомендуется работать через [Hello, Mac](~/mac/get-started/hello-mac.md) статью, во-первых, в частности [введение в Xcode и интерфейс построителя](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) и [выходов и действия](~/mac/get-started/hello-mac.md#Outlets_and_Actions) разделы, как он охватывает основные принципы и методы, которые будут использоваться в этой статье.

Может потребоваться рассмотреть [предоставление C# классы и методы для Objective-C](~/mac/internals/how-it-works.md) раздел [внутренние компоненты Xamarin.Mac](~/mac/internals/how-it-works.md) документов также объясняется, какие `Register` и `Export` атрибуты используется для передачи до классов C# Objective-C-объекты и элементы пользовательского интерфейса.

## <a name="the-applications-menu-bar"></a>Строка меню приложения 

В отличие от приложений, работающих в операционной системе Windows, где каждое окно может иметь собственную строку меню, подключенных к нему все приложения, запущенного на macOS имеется один меню вдоль верхней части экрана, который используется для каждого окна в приложении:

[![Строка меню](menu-images/appmenu01.png "строка меню")](menu-images/appmenu01-large.png#lightbox)

Элементы в этой строке меню активируется или деактивируется на основе текущего контекста или состояние приложения и его пользовательский интерфейс в любой момент. Например: Если пользователь выбирает текстового поля, элементы на **изменить** меню будет получен включен, такие как **копирования** и **Вырезать**.

В соответствии с Apple и по умолчанию все приложения macOS имеют стандартный набор меню и пунктов меню, которые отображаются в строке меню приложения:

- **Меню Apple** -это меню предоставляет доступ к системе расширенные элементы, которые доступны пользователю в любое время, независимо от того, какое приложение выполняется. Эти элементы нельзя изменить разработчиком.
- **Меню приложения** -это меню отображает имя приложения полужирным шрифтом и помогает пользователю определить, какое приложение выполняется в данный момент. Он содержит элементы, которые применяются к приложению как единое целое и не данного документа или процесса, таких как выход из приложения.
- **Меню "файл"** - элементы используются для создания, открыть или сохранить документы, приложение будет работать с. Если приложение не выполняется на основе документа, в этом меню можно будет переименован или удален.
- **В меню Правка** -содержит команды, такие как **Вырезать**, **копирования**, и **вставить** которого используются для изменения или изменять элементы в интерфейсе пользователя приложения.
- **Меню «Формат»** — Если приложение работает с текстом, это меню содержит команды, чтобы изменить форматирование текста.
- **Меню "Вид"** -содержит команды, которые влияют на способ отображения содержимого (Просмотр) в пользовательском интерфейсе приложения.
- **Меню приложения** -это любой меню, характерные для приложения (например, меню закладки для веб-браузера). Они должны отображаться между **представление** и **окна** меню в строке.
- **Меню "окно"** -содержит команды для работы с windows в приложении, а также список текущих открытых окон.
- **Меню "Справка"** -Если ваше приложение предоставляет справку на экране, в меню "Справка" должно быть меню справа на панели. 

Дополнительные сведения о меню приложения и стандартные меню и пунктов меню см. в разделе Apple [рекомендациям по интерфейсам](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/).

### <a name="the-default-application-menu-bar"></a>В меню приложения по умолчанию

Каждый раз при создании нового проекта Xamarin.Mac, вы автоматически получаете Standard Edition, строка меню приложения по умолчанию, имеющий типичных элементов, которые обычно имеет приложения macOS (как описано в предыдущем разделе). Строка меню приложения по умолчанию, определенные в **Main.storyboard** файла (вместе с остальными пользовательского интерфейса приложения) в проекте в **Pad решение**:  

![Выберите основной раскадровки](menu-images/appmenu02.png "выберите основной раскадровки")

Дважды щелкните **Main.storyboard** файл, чтобы открыть его для редактирования в построитель интерфейса в Xcode и будет выведен интерфейс редактора меню:

[![Изменение пользовательского интерфейса в Xcode](menu-images/defaultbar01.png "изменения пользовательского интерфейса в Xcode")](menu-images/defaultbar01-large.png#lightbox)

Здесь можно щелкнуть элементы таких как **откройте** пункта меню в **файл** меню и изменить или настроить его свойства в **атрибуты инспектора**:

[![Изменение атрибутов меню](menu-images/defaultbar02.png "редактирования меню атрибутов")](menu-images/defaultbar02-large.png#lightbox)

Вы узнаете Добавление, изменение и удаление меню и элементов далее в этой статье. Для теперь мы просто хотим видеть, какие меню и команды меню доступны по умолчанию и как они были автоматически подвержены воздействию кода через набор стандартных выходов и действия (Дополнительные сведения см. наш [выходов и действия](~/mac/get-started/hello-mac.md#Outlets_and_Actions) документация).

Например, если щелкнуть **инспектора подключения** для **откройте** пункт меню, мы видим, автоматически проводной до `openDocument:` действия: 

[![Просмотр вложенное действие](menu-images/defaultbar03.png "Просмотр вложенное действие")](menu-images/defaultbar03-large.png#lightbox)

При выборе **первый респондент** в **иерархии интерфейса** и прокрутите вниз **инспектора подключения**, и вы увидите определение `openDocument:` действие, **откройте** пункта меню присоединяется к (а также некоторые другие действия по умолчанию для приложения и автоматически не соединены до элементов управления):

[![Просмотр всех присоединенных действия](menu-images/defaultbar04.png "Просмотр всех вложенных действий")](menu-images/defaultbar04-large.png#lightbox) 

Почему это важно? В следующем разделе будет увидеть, как работают эти действия автоматически определяются с другими элементами Cocoa пользовательского интерфейса автоматически Включение и отключение пунктов меню, а также, предоставляют встроенные функциональные возможности для элементов.

Далее мы будем работать с этих встроенных действий для включения и отключения элементов из кода и предоставить собственную функциональность, когда они были выбраны.

<a name="Built-In_Menu_Functionality" />

### <a name="built-in-menu-functionality"></a>Функциональные возможности встроенного меню

Если выполнения вновь созданного приложения Xamarin.Mac перед добавлением все элементы пользовательского интерфейса или код, видно, что некоторые элементы автоматически проводного доступа и включен автоматически (с полной функциональностью автоматически встроенные), такие как **Quit** элемент **приложения** меню:

![Элемент меню включен](menu-images/appmenu03.png "элемент меню включен")

Хотя другие пункты меню, например **Вырезать**, **копирования**, и **вставить** не являются:

![Отключить пунктов меню](menu-images/appmenu04.png "отключена пунктов меню")

Давайте остановить приложение и дважды щелкните **Main.storyboard** файла в **Pad решения** чтобы открыть его для редактирования в Xcode, интерфейс построителя. Затем перетащите **представления текста** из **библиотеки** на контроллер представление окна в **редактор интерфейса**:

[![При выборе представления текста из библиотеки](menu-images/appmenu05.png "при выборе представления текста из библиотеки")](menu-images/appmenu05-large.png#lightbox)

В **Редактор ограничений** давайте закрепить текстовое представление края окна и задать для него где расширяется и сжимается с окном, щелкнув все четыре красный I балки в верхней части редактора и **добавьте ограничения 4** кнопки:

[![Изменение ограничений](menu-images/appmenu06.png "редактирование ограничений")](menu-images/appmenu06-large.png#lightbox)

Сохранить изменения пользовательского интерфейса и Visual Studio для Mac для синхронизации изменений с проектом Xamarin.Mac переключиться обратно. Теперь запустите приложение, введите текст в текстовое представление, выберите его и откройте **изменить** меню:

![Пункты меню будут автоматически Включение или отключение](menu-images/appmenu07.png "пункты меню будут автоматически отключен")

Обратите внимание как **Вырезать**, **копирования**, и **вставить** элементов, автоматически включена и полностью функциональны, все это не требует написания ни единой строки кода. 

Что здесь происходит? Следует помнить, встроенные заранее определить действия, которые поставляются проводной до пунктов меню по умолчанию (представленный выше), большую часть Cocoa элементы пользовательского интерфейса, которые являются частью macOS имеют встроенные обработчики на выполнение определенных действий (таких как `copy:`). Поэтому при их добавления в окно активно и выборе соответствующего пункта меню или элементы, присоединенные к данному действию включаются автоматически. Если пользователь выбирает соответствующий элемент меню, функций, встроенных в элемент пользовательского интерфейса вызывается и выполняется без вмешательства разработчика.

### <a name="enabling-and-disabling-menus-and-items"></a>Включение и отключение меню и элементов

По умолчанию каждый раз при возникновении события пользователя `NSMenu` автоматически включает и отключает всех раскрытых и меню элементов на основе контекста приложения. Чтобы включить или отключить элемент тремя способами:

- **Включение автоматического меню** -пункт меню доступен в том случае, если `NSMenu` можно найти соответствующий объект, который реагирует на действие, которое элемент является проводной доступ к. Например, текстовое представление выше, имеющих встроенные обработчик `copy:` действие.
- **Настраиваемые действия и validateMenuItem:** — для любого элемента меню, к которому привязан [окна или представления настраиваемое действие контроллера](#Working-with-Custom-Window-Actions), можно добавить `validateMenuItem:` действие вручную включите или отключите пункты меню.
- **Включение ручной меню** -вручную задать `Enabled` каждого экземпляра `NSMenuItem` для включения или отключения каждый элемент меню по отдельности.

Чтобы выбрать систему, установите `AutoEnablesItems` свойство `NSMenu`. `true` автоматический (по умолчанию) и `false` выполняется вручную. 

> [!IMPORTANT]
> Если вы решили использовать включение вручную меню, ни одна из меню элементы, даже те, которые контролируются AppKit классов, таких как `NSTextView`, обновляются автоматически. Вы принимаете обязательство оплаты для включения и отключения всех элементов вручную в коде.

#### <a name="using-validatemenuitem"></a>С помощью validateMenuItem

Как упоминалось выше, для любого элемента меню, к которому привязан [окна или пользовательское действие контроллера представления](#Working-with-Custom-Window-Actions), можно добавить `validateMenuItem:` действие вручную включите или отключите пункты меню.

В следующем примере `Tag` решить тип пункт меню, который будет включить или отключить, будет использоваться свойство `validateMenuItem:` действия на основе состояния выбранного текста в `NSTextView`. `Tag` Свойства в построителе интерфейс для каждого пункта меню:

![Тег свойства](menu-images/validate01.png "параметр Tag-свойство")

И добавлена к представлению контроллеру следующий код:

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

При выполнении этого кода, и нет выделенного текста в `NSTextView`, пункты меню два wrap отключены (даже если они соединены с действий в контроллере представление):

![Отключить отображение элементов](menu-images/validate02.png "отображение отключенных объектов")

Если выбран раздел текста и повторно открыть меню, два wrap пункты меню будут доступны:

![Включить отображение элементов](menu-images/validate03.png "отображение включены элементы")

## <a name="enabling-and-responding-to-menu-items-in-code"></a>Включение и отвечать на запросы к пунктам меню в коде

Как было показано выше, просто добавив определенные элементы пользовательского интерфейса Cocoa нашей разработки пользовательского интерфейса (например, текстовое поле), некоторые элементы меню по умолчанию будут включены и функционируют автоматически, без необходимости написания кода. Далее рассмотрим добавление нашего Xamarin.Mac проекта для включения пункта меню и предоставляют функциональные возможности, когда пользователь выбирает его собственный код на C#.

Например, позволяют скажем, мы хотим, чтобы пользователь имел возможность использовать **откройте** элемента в **файл** меню для выбора папки. Поскольку мы хотим, это может быть функция уровня приложения и не ограничиваясь окна предоставьте или элемент пользовательского интерфейса, мы собираемся добавить код для обработки этого делегата наши приложения.

В **Pad решения**, дважды щелкните `AppDelegate.CS` файл, чтобы открыть его для редактирования:

![При выборе делегат приложения](menu-images/appmenu08.png "при выборе делегат приложения")

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

Обратите внимание, что **откройте** теперь включен элемент меню. Если мы выбираем ее, откройте диалоговое окно будет отображаться:

![Откройте диалоговое окно](menu-images/appmenu10.png "откройте диалоговое окно")

Если щелкнуть **откройте** кнопку, отображается нашей предупреждающее сообщение:

![Пример диалогового окна сообщения](menu-images/appmenu11.png "пример диалогового окна сообщения")

Строки ключа был `[Export ("openDocument:")]`, он сообщает `NSMenu` , наши **AppDelegate** имеет метод `void OpenDialog (NSObject sender)` , отвечающей на запросы `openDocument:` действие. Если необходимо передать из выше, **откройте** пункт меню является автоматически проводной до этого действия по умолчанию в построителе интерфейса:

[![Просмотр вложенных действий,](menu-images/defaultbar03.png "Просмотр вложенных действий")](menu-images/defaultbar03-large.png#lightbox)

Далее рассмотрим создание собственных меню, пункты меню и действий и отвечать на запросы к ним в коде.

### <a name="working-with-the-open-recent-menu"></a>Работа с меню открытия последних

По умолчанию **файл** меню содержит **открыть последние** элемента, который отслеживает последний несколько файлов, которые пользователь открыл вместе с приложением. Если вы создаете `NSDocument` Xamarin.Mac приложения, это меню будет обрабатываться для вас автоматически. Для приложения Xamarin.Mac любого другого типа будет отвечать за управление и отвечать на этот пункт меню вручную.

Чтобы вручную обрабатывать **открыть последние** меню, сначала необходимо будет сообщить ее, новый файл открыло или сохранить, используя следующие:

```csharp
// Add document to the Open Recent menu
NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);
```

Несмотря на то, что приложение не использует `NSDocuments`, по-прежнему использовать `NSDocumentController` для поддержания **открыть последние** меню, отправляя `NSUrl` с расположением файла `NoteNewRecentDocumentURL` метод `SharedDocumentController`.

Затем необходимо переопределить `OpenFile` метод делегата приложение можно открыть любой файл, который пользователь выбирает из **последние документы** меню. Пример:

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

Так как имя и путь к файлу, возвращенные **открыть последние** меню может содержать пробелы, необходимо правильно экранировать этот символ перед созданием `NSUrl` или мы появится сообщение об ошибке. Для этого с помощью следующего кода:

```csharp
filename = filename.Replace (" ", "%20");
```

Наконец, мы создадим `NSUrl` , указывает на файл и использование вспомогательного метода в приложении делегировать откройте новое окно и загрузка файла:

```csharp
var url = new NSUrl ("file://"+filename);
return OpenFile(url);
```

Для извлечения все вместе, давайте рассмотрим пример реализации в **AppDelegate.cs** файла:

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

В зависимости от требований приложения может потребоваться пользователю открыть тот же файл в несколько окон, в то же время. В нашем примере приложении, если пользователь выбирает файл, который уже открыт (либо из **последние документы** или **открыть...** элементы меню), окна, содержащего файл переводится на передний план.

Для этого используется следующий код в нашем вспомогательный метод:

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

Мы разработали нашей `ViewController` класса, содержащего путь к файлу в его `Path` свойство. Далее мы переберите все открытые окна в приложении. Если файл уже открыт в окне, она переводится поверх всех окон, с помощью:

```csharp
NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
```

Если совпадение не найдено, открывается новое окно с загруженного файла и указывается в файле **открыть последние** меню:

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

Так же, как встроенные **первый респондент** действия, которые поставляются предварительно проводной для стандартных элементов меню, можно создать пользовательские действия и привязать их к пунктам меню в интерфейс построителя.

Во-первых определите настраиваемого действия на одном из контроллеров окна приложения. Пример:

```csharp
[Action("defineKeyword:")]
public void defineKeyword (NSObject sender) {
    // Preform some action when the menu is selected
    Console.WriteLine ("Request to define keyword");
}
```

Затем дважды щелкните файл раскадровки приложения в **Pad решения** чтобы открыть его для редактирования в Xcode, интерфейс построителя. Выберите **первый респондент** под **экрана приложения**, затем переключитесь в **атрибуты инспектора**:

![Атрибуты инспектора](menu-images/action01.png "инспектора атрибуты")

Нажмите кнопку **+** кнопки в нижней части **атрибуты инспектора** для добавления нового настраиваемого действия:

![Добавление нового действия](menu-images/action02.png "Добавление нового действия")

Присвойте ему имя, совпадающее с именем настраиваемого действия, созданные на контроллере окна:

![Изменение имени действия](menu-images/action03.png "редактирование имя действия")

Удерживая нажатой, перетащите из пункта меню, чтобы **первый респондент** под **экрана приложения**. Из всплывающего списка, выберите только что созданный новый действие (`defineKeyword:` в этом примере):

![Присоединение действие](menu-images/action04.png "присоединение действие")

Сохранить изменения в раскадровку и вернуться в Visual Studio для Mac синхронизировать изменения. При запуске приложения, к которому подключение, настраиваемое действие для элемента меню будет автоматически включить или отключить (с учетом окна с действием, не откроется) и выбора элемента меню запустит действие:

[![Тестирование нового действия](menu-images/action05.png "тестирование нового действия")](menu-images/action05-large.png#lightbox)

<a name="Adding,_Editing_and_Deleting_Menus" />

### <a name="adding-editing-and-deleting-menus"></a>Добавление, изменение и удаление меню

Как было показано в предыдущих разделах, приложение Xamarin.Mac поставляется с существующие число меню по умолчанию и их элементов, определенных элементов управления пользовательского интерфейса будет автоматически активировать и отвечать на. Также мы увидели, как добавить код для нашего приложения, который также включает и реагировать на эти элементы по умолчанию.

В этом разделе мы рассмотрим удаление пункты меню, которые нам не нужен, реорганизация меню и добавление новых меню, пункты меню и действий.

Дважды щелкните **Main.storyboard** файла в **Pad решения** чтобы открыть его для редактирования:

[![Изменение пользовательского интерфейса в Xcode](menu-images/maint01.png "изменения пользовательского интерфейса в Xcode")](menu-images/maint01-large.png#lightbox)

Для нашего конкретного приложения Xamarin.Mac не будет использоваться значение по умолчанию **представление** меню, поэтому нам необходимо удалить его. В **иерархии интерфейса** выберите **представление** пункт меню, который является частью главного меню:

![При выборе пункта меню Просмотр](menu-images/maint02.png "выбора элемента меню представления")

Нажмите клавишу delete или стирание назад, чтобы удалить данное меню. Далее мы не будем использовать все элементы в **формат** меню и нам требуется переместить элементы, мы собираемся использовать из вложенного меню. В **иерархии интерфейса** выберите следующие пункты меню:

![Выделите несколько элементов](menu-images/maint03.png "выделения нескольких элементов")

Перетащите элементы под родительским элементом **меню** во вложенном меню, где они в настоящее время являются:

[![Перетаскивания элементов меню в его родительском меню](menu-images/maint04.png "перетаскивания элементов меню в его родительском меню")](menu-images/maint04-large.png#lightbox)

Меню должен выглядеть так:

[![Элементы в новом расположении](menu-images/maint05.png "элементов в новое расположение")](menu-images/maint05-large.png#lightbox)

Далее давайте перетащим **текст** подменю out из раздела **формат** меню и поместите его в главном меню между **формат** и **окна** меню:

[![Меню текстового](menu-images/maint06.png "текста меню")](menu-images/maint06-large.png#lightbox)

Давайте рассмотрим обратно в **формат** меню и delete **шрифта** элемент вложенного меню. Затем выберите **формат** меню и переименуйте его в «Шрифт»:

[![Меню «Шрифт»](menu-images/maint07.png "шрифт меню")](menu-images/maint07-large.png#lightbox)

Теперь попробуем создать пользовательское меню стандартное фраз, которые автоматически получить добавляется к тексту в представлении текста, при их выборе. В поле поиска в нижней части **инспектора библиотеки** типа «меню». Это облегчит поиск и работать со всеми меню элементов пользовательского интерфейса:

![Библиотека инспектора](menu-images/maint08.png "инспектора библиотеки")

Теперь давайте выполните следующую команду, чтобы создать нашей меню:

1. Перетащите **пункта меню** из **инспектора библиотеки** в строке меню между **текст** и **окна** меню: 

    ![При выборе нового пункта меню в библиотеке](menu-images/maint10.png "при выборе нового пункта меню в библиотеке")
2. Переименование элемента «Фразы»: 

    [![Выберите имя параметра](menu-images/maint09.png "меню имя параметра")](menu-images/maint09-large.png#lightbox)
3. Затем перетащите **меню** из **инспектора библиотеки**: 

    ![При выборе в меню из библиотеки](menu-images/maint11.png "меню из библиотеки")
4. Затем удалите **меню** на новом **пункта меню** мы только что созданного и измените ее имя на «Фразы»: 

    [![Изменение имени меню](menu-images/maint12.png "редактирование имя меню")](menu-images/maint12-large.png#lightbox)
5. Теперь переименуем трех стандартных **пунктов меню** «Адрес», «Дата» и «Приветствие»: 

    [![В меню фраз](menu-images/maint13.png "фраз меню")](menu-images/maint13-large.png#lightbox)
6. Давайте добавим четвертый **пункта меню** путем перетаскивания **пункта меню** из **инспектора библиотеки** и вызов его «Подпись»: 

    [![Имя элемента меню редактирования](menu-images/maint14.png "редактирование имя пункта меню")](menu-images/maint14-large.png#lightbox)
7. Сохраните изменения в строку меню.

Теперь давайте создадим набор пользовательских действий, чтобы наши новые элементы меню предоставляются кода на языке C#. В Xcode давайте переключение **помощника** представления:

[![Создание необходимых действий](menu-images/maint15.png "Создание необходимые действия")](menu-images/maint15-large.png#lightbox)

Давайте выполните следующие действия.

1. Перетащите элемент управления из **адрес** пункт меню, чтобы **AppDelegate.h** файла.
2. Коммутатор **подключения** тип **действия**: 

    [![Выберите тип действия](menu-images/maint17.png "выберите тип действия")](menu-images/maint17-large.png#lightbox)
3. Введите **имя** «phraseAddress» и нажмите клавишу **Connect** кнопку, чтобы создать новое действие: 

    [![Настройка действия](menu-images/maint18.png "Настройка действия")](menu-images/maint18-large.png#lightbox)
4. Повторите описанные выше шаги для **даты**, **Greeting**, и **подписи** пункты меню: 

    [![Выполненные действия](menu-images/maint19.png "выполненные действия")](menu-images/maint19-large.png#lightbox)
5. Сохраните изменения в строку меню.

Далее нам нужно создать выхода для наших представления текста, что мы можно уточнить его содержимое из кода. Выберите **ViewController.h** файла в **помощника редактор** и создайте новый розетки вызывается `documentText`:

[![Создание выхода](menu-images/maint20.png "Создание выхода")](menu-images/maint20-large.png#lightbox)

Вернитесь в Visual Studio для Mac синхронизировать изменения из Xcode. Далее изменить **ViewController.cs** файла и сделать его выглядеть следующим образом:

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

Это предоставляет текст, наши представления текста за пределами `ViewController` класса, но и уведомляет делегат приложения, когда окно Получает или теряет фокус. Теперь измените **AppDelegate.cs** файла и сделать его выглядеть следующим образом:

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

Здесь мы внесли `AppDelegate` разделяемый класс, чтобы мы могли использовать действия и выходов, созданных в построителе интерфейса. Мы также предоставляем `textEditor` для отслеживания окно, которое в настоящее время находится в фокусе.

Следующие методы используются для обработки наших пользовательских меню и элементов меню:

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

Теперь если мы запустим наше приложение, все элементы в **фразу** меню будет активна и добавит фраза обеспечивает для представления текста, при выборе:

![Пример выполнения приложения](menu-images/maint21.png "пример выполнения приложения")

Теперь, когда у нас есть основы работы с меню приложения вниз, давайте взглянем на создание пользовательского контекстного меню.

### <a name="creating-menus-from-code"></a>Создание меню из кода

Помимо создания меню и команды меню с помощью построителя интерфейса в Xcode, возможны ситуации, когда приложение Xamarin.Mac нужно создать, изменить или удалить меню, подменю или элемент меню из кода.

В следующем примере создается класс, для хранения сведений о элементы меню и подменю, которые динамически создаются на лету:

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

С этим классом, который определен, следующая процедура выполняет синтаксический анализ коллекцию `LanguageFormatCommand`объектов и рекурсивно сборки путем их добавления в нижней части существующего меню, (созданные в построителе интерфейса), которая была передана в новое меню и команды меню:

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

Для любого `LanguageFormatCommand` объект, имеющий пустое `Title` свойство, эта процедура создает **пункт меню разделителя** (тонкая серую линию) между разделами меню:

```csharp
menuItem = NSMenuItem.SeparatorItem;
```

Если заголовок, создается новый пункт меню с этим заголовком:

```csharp
menuItem = new NSMenuItem (command.Title);
``` 

Если `LanguageFormatCommand` содержит дочерний объект `LanguageFormatCommand` объекты, созданные подменю и `AssembleMenu` рекурсивно вызывается для построения этого меню является метод:

```csharp
menuItem.Submenu = new NSMenu (command.Title);
AssembleMenu (menuItem.Submenu, command.SubCommands);
```

Для любого нового пункта меню, у которого нет дочерних меню будет добавлен код для обработки выбранного пользователем:

```csharp
menuItem.Activated += (sender, e) => {
    // Do something when the menu item is selected
    ...
};
```

#### <a name="testing-the-menu-creation"></a>Тестирование меню создания

Со всеми приведенный выше код на месте Если следующая коллекция `LanguageFormatCommand` объекты были созданы:

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

И что передан коллекции `AssembleMenu` функции (с **формат** меню задать в качестве базового), будут создаваться следующие динамического меню и пункты меню:

![Новые пункты меню в запущенном приложении](menu-images/dynamic01.png "новые пункты меню в запущенном приложении")

#### <a name="removing-menus-and-items"></a>Удаление меню и элементов

Если необходимо удалить из пользовательского интерфейса приложения пункт меню или пункта меню, можно использовать `RemoveItemAt` метод `NSMenu` просто ей ноль основан класс индекс удаляемого элемента.

Например чтобы удалить меню и элементов меню, созданный в процедуре выше, можно использовать следующий код:

```csharp
public void UnpopulateFormattingMenu(NSMenu menu) {

    // Remove any additional items
    for (int n = (int)menu.Count - 1; n > 4; --n) {
        menu.RemoveItemAt (n);
    }
}
```

В случае приведенный выше код первые четыре меню элементов создаются в интерфейс построителя и размещения, доступные в приложении, в Xcode, они не удаляются динамически.

<a name="Contextual_Menus" />

## <a name="contextual-menus"></a>Контекстные меню

Контекстные меню отображаются, когда пользователь щелкает правой кнопкой мыши или элемент в окне управления щелчками. По умолчанию некоторые элементы пользовательского интерфейса, уже встроены в macOS имеют контекстные меню, присоединенными к ним (например, представление «текст»). Однако возможны ситуации, когда требуется создать собственный пользовательский контекстных меню для элемента пользовательского интерфейса, мы добавили в окно.

Изменим наши **Main.storyboard** в Xcode и добавьте **окна** окна для наших разработки установить его **класса** для «NSPanel» в **инспектора удостоверения**, добавьте новый **помощника** элемент **окна** меню и подключите его к нового окна с помощью **Показать Перейти**:

[![Установка типа segue](menu-images/context01.png "segue тип параметра")](menu-images/context01-large.png#lightbox)

Давайте выполните следующие действия.

1. Перетащите **метка** из **инспектора библиотеки** на **панель** окна и задать его текст в окне «Свойства»: 

    [![Изменение метки значение](menu-images/context03.png "Редактирование значения метки")](menu-images/context03-large.png#lightbox)
2. Затем перетащите **меню** из **инспектора библиотеки** на контроллер представление в иерархии представления и элементы меню по умолчанию переименования три **документа**, **текста**  и **шрифта**:

    [![Элементы меню необходимые](menu-images/context02.png "необходимые меню")](menu-images/context02-large.png#lightbox)
3. Теперь перетаскивание из **подпись свойства** на **меню**:

    [![Перетаскивание, чтобы создать segue](menu-images/context04.png "перетаскивание Создание segue")](menu-images/context04-large.png#lightbox)
4. В появившемся диалоговом окне выберите **меню**: 

    ![Установка типа segue](menu-images/context05.png "segue тип параметра")
5. Из **инспектора удостоверения**, задать класс контроллера представления «PanelViewController»: 

    [![Класс segue параметров](menu-images/context10.png "segue класс параметров")](menu-images/context10-large.png#lightbox)
6. Вернитесь в Visual Studio для Mac для синхронизации, а затем вернитесь в интерфейс построитель.
7. Переключитесь в **помощника редактор** и выберите **PanelViewController.h** файла.
8. Создание действия для **документа** пункта меню вызывается `propertyDocument`: 

    [![Настройка действия](menu-images/context06.png "Настройка действия")](menu-images/context06-large.png#lightbox)
9. Повторите создание действия для остальных пунктов меню. 

    [![Необходимые действия](menu-images/context07.png "необходимые действия")](menu-images/context07-large.png#lightbox)
10. Наконец, создайте выхода для **подпись свойства** вызывается `propertyLabel`: 

    [![Настройка выхода](menu-images/context08.png "Настройка на выходе")](menu-images/context08-large.png#lightbox)
11. Сохранить изменения и вернуться в Visual Studio для Mac для синхронизации с Xcode.

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

Теперь если мы запустите приложение и щелкните правой кнопкой мыши на метку свойства на панели, мы рассмотрим наших пользовательских контекстного меню. Если выбрать и элементов в меню, изменится значение метки:

![Контекстные меню под управлением](menu-images/context09.png "под управлением контекстные меню")

Далее рассмотрим создание меню строки состояния.

## <a name="status-bar-menus"></a>Меню панели состояния

Меню панели состояния отображения коллекцию пунктов меню состояния, которые обеспечивают взаимодействие с или отзывы для пользователя, такие как меню или изображение, отражая состояние приложения. Меню строки состояния приложения включено и активных, даже если приложение выполняется в фоновом режиме. В строке состояния всей системы находится в правой части панели меню приложения и является только строки состояния, в настоящее время доступны в macOS.

Изменим наши **AppDelegate.cs** и создайте `DidFinishLaunching` внешний метод следующим образом:

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

`NSStatusBar statusBar = NSStatusBar.SystemStatusBar;` дает нам доступа к строке состояния всей системы. `var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);` Создает новый элемент строки состояния. Здесь мы создается меню и количество элементов меню и присоединить меню элемент строки состояния, который мы только что создали. 

Если мы запустим приложение, отображается новый элемент строки состояния. При выборе элемента в меню будет изменить текст в представлении текста: 

![В меню панели состояния под управлением](menu-images/statusbar01.png "под управлением меню строки состояния")

Теперь давайте рассмотрим создание пользовательских закрепление элементов меню.

## <a name="custom-dock-menus"></a>Закрепление пользовательского меню

Всплывающего меню отображается для приложения Mac можно, если пользователь щелкает правой кнопкой мыши или значок приложения в дока щелчками управления:

![Меню Закрепить пользовательский](menu-images/dock01.png "пользовательский закрепить меню")

Давайте создадим пользовательского всплывающего меню для нашего приложения следующим образом:

1. В Visual Studio для Mac, щелкните правой кнопкой мыши проект и выберите пункт приложения **добавить** > **новый файл...** В диалоговом окне создания файла выберите **Xamarin.Mac** > **пустой определение интерфейса**, используйте «DockMenu» для **имя** и нажмите кнопку **New**  кнопку, чтобы создать новый **DockMenu.xib** файла:

    ![Добавление пустое определение интерфейса](menu-images/dock02.png "Добавление пустое определение интерфейса")
2. В **Pad решения**, дважды щелкните **DockMenu.xib** файл, чтобы открыть его для редактирования в Xcode. Создайте новый **меню** со следующими элементами: **адрес**, **даты**, **Greeting**, и **подписи** 

    [![Размещение пользовательского интерфейса](menu-images/dock03.png "с макетом пользовательского интерфейса")](menu-images/dock03-large.png#lightbox)
3. Затем подключим нашей существующего действия, которые мы создали для наших пользовательских меню в нашем новые пункты меню [Добавление, изменение и удаление меню](#Adding,_Editing_and_Deleting_Menus) выше. Переключитесь в **инспектора подключения** и выберите **первый респондент** в **иерархии интерфейса**. Прокрутите вниз и найдите `phraseAddress:` действие. Перетащите линию из круг на это действие для **адрес** элемента меню:

    [![Перетаскивая на установление связи действие](menu-images/dock04.png "установление связи действие перетаскивания")](menu-images/dock04-large.png#lightbox)
4. Повторите для всех остальных пунктов меню, присоединение их к их соответствующие действия: 

    [![Необходимые действия](menu-images/dock05.png "необходимые действия")](menu-images/dock05-large.png#lightbox)
5. Затем выберите **приложения** в **иерархии интерфейса**. В **инспектора подключения**, перетащите линию из круг `dockMenu` розетки в только что созданный меню:

    [![Перетаскивание установление розетки](menu-images/dock06.png "перетаскивания установление выхода")](menu-images/dock06-large.png#lightbox)
6. Сохранить изменения и вернуться в Visual Studio для Mac для синхронизации с Xcode.
7. Дважды щелкните **Info.plist** файл, чтобы открыть его для редактирования: 

    [![Редактирование файла info.plist](menu-images/dock07.png "Editing the Info.plist file")](menu-images/dock07-large.png#lightbox)
8. Нажмите кнопку **источника** вкладку в нижней части экрана: 

    [![Выбор представления источника](menu-images/dock08.png "Выбор представления источника")](menu-images/dock08-large.png#lightbox)
9. Нажмите кнопку **добавьте новую запись**выберите зеленым, а также кнопки, задайте имя свойства для «AppleDockMenu» и значение для «DockMenu» (имя наш новый .xib файл без расширения): 

    [![Добавление элемента DockMenu](menu-images/dock09.png "Добавление элемента DockMenu")](menu-images/dock09-large.png#lightbox)

Теперь если мы запуска нашего приложения, щелкните правой кнопкой мыши значок в дока наши новые пункты меню будет отображаться:

![Пример всплывающего меню под управлением](menu-images/dock10.png "пример всплывающего меню под управлением")

Если один из пользовательских элементов, выберите в меню, текст в нашем текстового представления будут изменены.

<a name="Pop-up_Menus_and_Pull-Down_Lists" />

## <a name="pop-up-button-and-pull-down-lists"></a>Всплывающая кнопка и раскрывающихся списков

Всплывающая кнопка отображается выбранный элемент и выводит список параметров для выбора при щелчке пользователем. Выпадающем списке является типом всплывающих кнопки, обычно используется для выбора команд, определенных в контексте текущей задачи. Оба могут находиться в любом месте окна.

Давайте создадим настраиваемую кнопку всплывающих для нашего приложения следующим образом:

1. Изменить **Main.storyboard** файл в Xcode и перетащите **контекстного меню кнопки** из **инспектора библиотеки** на **панель** мы создали в окно [контекстные меню](#Contextual_Menus) раздела: 

    [![Добавление контекстного меню кнопки](menu-images/popup01.png "добавления кнопки всплывающего окна")](menu-images/popup01-large.png#lightbox)
2. Добавление нового элемента меню и задать заголовки элементов в контекстное меню для: **адрес**, **даты**, **Greeting**, и **подписи** 

    [![Настройка элементов меню](menu-images/popup02.png "настройку элементов меню")](menu-images/popup02-large.png#lightbox)
3. Затем подключим наши новые пункты меню для существующего действия, которые мы создали для наших пользовательских меню в [Добавление, изменение и удаление меню](#Adding,_Editing_and_Deleting_Menus) выше. Переключитесь в **инспектора подключения** и выберите **первый респондент** в **иерархии интерфейса**. Прокрутите вниз и найдите `phraseAddress:` действие. Перетащите линию из круг на это действие для **адрес** элемента меню: 

    [![Перетаскивая на установление связи действие](menu-images/popup03.png "установление связи действие перетаскивания")](menu-images/popup03-large.png#lightbox)
4. Повторите для всех остальных пунктов меню, присоединение их к их соответствующие действия: 

    [![Все необходимые действия](menu-images/popup04.png "все необходимые действия")](menu-images/popup04-large.png#lightbox)
5. Сохранить изменения и вернуться в Visual Studio для Mac для синхронизации с Xcode.

Теперь если мы запуска нашего приложения и выберите элемент из всплывающего окна, будет изменяться текст в нашем представления текста:

![Пример всплывающего окна под управлением](menu-images/popup05.png "пример всплывающего окна под управлением")

Можно создать и работать с раскрывающихся списков в точно так же, как всплывающие кнопки. Вместо присоединения существующего действия, можно создать собственные настраиваемые действия так же, как мы сделали для наших контекстные меню в [контекстные меню](#Contextual_Menus) раздела.

## <a name="summary"></a>Сводка

Подробное описание работы с меню и элементов меню в приложении Xamarin.Mac вступила в этой статье. Сначала мы рассмотрели меню приложения мы рассмотрели создание контекстного меню, Далее мы рассмотрели меню панели состояния и меню Закрепить пользовательский. Наконец мы узнали всплывающих меню и раскрывающихся списков.


## <a name="related-links"></a>Связанные ссылки

- [MacMenus (пример)](https://developer.xamarin.com/samples/mac/MacMenus/)
- [Привет, Mac](~/mac/get-started/hello-mac.md)
- [Рекомендации по пользовательского интерфейса - меню](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/)
- [Введение в меню приложения и всплывающие списки](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/MenuList/MenuList.html)
