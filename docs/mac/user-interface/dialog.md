---
title: Диалоговые окна в Xamarin.Mac
description: В этой статье рассматривается работа с диалоговыми и модальными окнами в приложении Xamarin.Mac. Он описывает создание модальных окон в Xcode и конструкторе Interface builder, работа со стандартными диалоговыми окнами и взаимодействие с этими элементами управления в коде C#.
ms.prod: xamarin
ms.assetid: 55451990-B77B-4D44-B8BB-F874EC503B0C
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 2f28b52b4904b73f97cd9da575e90ef583e443da
ms.sourcegitcommit: 47709db4d115d221e97f18bc8111c95723f6cb9b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2018
ms.locfileid: "40251234"
---
# <a name="dialogs-in-xamarinmac"></a>Диалоговые окна в Xamarin.Mac

При работе с C# и .NET в приложении Xamarin.Mac, у вас есть доступ к одной и той же диалоговые окна и модальное Windows, работающему *Objective-C* и *Xcode* does. Поскольку Xamarin.Mac напрямую интегрируется с Xcode, можно использовать конструктор _Interface Builder_ для создания и поддержания вашей модальное Windows (или при необходимости создать их непосредственно в коде C#).

Диалоговое окно отображается в ответ на действия пользователя и обычно предоставляет как пользователи могут завершить действие. Диалоговое окно требует реакции от пользователя перед закрытием.

Windows можно использовать в немодальное состоянии (например, текстовый редактор, который может иметь одновременно открыто несколько документов) или модальное (например, диалоговое окно экспорта, должно быть отброшено, прежде чем приложение может продолжить).

[![](dialog-images/dialog03.png "Открытое окно")](dialog-images/dialog03.png#lightbox)

В этой статье мы обсудим, что узнаете основы работы с диалоговыми и модальное Windows в приложении Xamarin.Mac. Настоятельно рекомендуется работать через [Привет, Mac](~/mac/get-started/hello-mac.md) статьи, во-первых, в частности [введение в Xcode и конструкторе Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) и [переменных экземпляров и действий](~/mac/get-started/hello-mac.md#outlets-and-actions) разделы, так как он рассматриваются основные понятия и методы, которые мы будем использовать в этой статье.

Может потребоваться взгляните на [предоставление C# классы / методы Objective-c](~/mac/internals/how-it-works.md) раздел [структуре Xamarin.Mac](~/mac/internals/how-it-works.md) документов, здесь объясняется `Register` и `Export` команды используется для передачи классов C# в службе Objective-C объекты и элементы пользовательского интерфейса.

<a name="Introduction_to_Dialogs" />

## <a name="introduction-to-dialogs"></a>Общие сведения о диалоговых окнах

Диалоговое окно отображается в ответ на действия пользователя (например, сохранение в файл), а также предоставляет способ для пользователей, для выполнения этого действия. Диалоговое окно требует реакции от пользователя перед закрытием.

В соответствии с Apple для открытия окна тремя способами:

- **Модальное окно документа** -документа модальное диалоговое окно не позволяет пользователю выполнять что-нибудь еще в пределах заданного документа до его закрытия.
- **Модальное окно приложения** — это модальное диалоговое окно не позволяет пользователю взаимодействовать с приложением, до его закрытия приложения.
- **Немодальный** немодальном диалоговом разрешает пользователю изменять параметры в диалоговом окне при взаимодействии по-прежнему с окном документа.

### <a name="modal-window"></a>Модальное окно

Любой стандарт `NSWindow` может использоваться в качестве настраиваемое диалоговое окно отображается как модальная:

[![](dialog-images/modal01.png "Пример модальные окна")](dialog-images/modal01.png#lightbox)

### <a name="document-modal-dialog-sheets"></a>Листы модального диалогового окна документов

Объект _лист_ является модальным диалоговым окном, присоединенный к окну данного документа, предотвращая взаимодействие с окном, пока они закрыть диалоговое окно. Лист присоединяется к окну, из которого он возникает и в любой момент времени только один лист может быть открыт для окна.

[![](dialog-images/sheet08.png "Лист модальное пример")](dialog-images/sheet08.png#lightbox)

### <a name="preferences-windows"></a>Параметры Windows

Предпочтения окно — немодальное диалоговое окно, который содержит параметры приложения, пользователь редко изменяются. Параметры Windows часто включают в себя панель инструментов, которая позволяет пользователю переключаться между различные группы параметров:

[![](dialog-images/dialog02.png "Пример окна предпочтений")](dialog-images/dialog02.png#lightbox)

### <a name="open-dialog"></a>Откройте диалоговое окно

Откройте диалоговое окно предоставляет пользователям согласованный способ найти и открыть элемент в приложении:

[![](dialog-images/dialog03.png "Откройте диалоговое окно")](dialog-images/dialog03.png#lightbox)


### <a name="print-and-page-setup-dialogs"></a>Печать и диалоговых окнах параметры страницы

macOS предоставляет стандартный печати и страницы установки диалоговых окон, которые приложение может отображать таким образом, пользователи могут иметь согласованный печати в каждое приложение, которые они используют.

Диалоговое окно печати могут отображаться как оба бесплатно с плавающей запятой диалоговое окно:

[![](dialog-images/print01.png "Диалоговое окно печати")](dialog-images/print01.png#lightbox)

Или он может отображаться в виде листа:

[![](dialog-images/print02.png "Печать листа")](dialog-images/print02.png#lightbox)

Диалоговое окно параметров страницы могут отображаться как оба бесплатно с плавающей запятой диалоговое окно:

[![](dialog-images/print03.png "Диалоговое окно настройки печати")](dialog-images/print03.png#lightbox)

Или он может отображаться в виде листа:

[![](dialog-images/print04.png "Лист установки страницы")](dialog-images/print04.png#lightbox)

### <a name="save-dialogs"></a>Сохранить диалоговых окон

Диалоговое окно Сохранить предоставляет пользователям согласованный способ сохранения элемента в приложении. Диалоговое окно сохранения имеет два состояния: **минимальным** (также известный как свернутый):

[![](dialog-images/save01.png "Сохранение диалоговое окно")](dialog-images/save01.png#lightbox)

И **разреженный** состояния:

[![](dialog-images/save02.png "Диалоговое окно сохранения, расширенный объект")](dialog-images/save02.png#lightbox)

**Минимальным** сохранить диалоговое окно может также быть отображен как таблицу:

[![](dialog-images/save03.png "Минимальным сохранить лист")](dialog-images/save03.png#lightbox)

Так же, как **разреженный** сохранить диалоговое окно:

[![](dialog-images/save04.png "Расширенный объект сохранить лист")](dialog-images/save04.png#lightbox)

Дополнительные сведения см. в разделе [диалоговые окна](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowDialogs.html#//apple_ref/doc/uid/20000957-CH43-SW1) раздел Apple [рекомендации по человеческим интерфейсом OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Adding_a_Modal_Window_to_a_Project" />

## <a name="adding-a-modal-window-to-a-project"></a>Добавление в проект модальное окно

Помимо основным окном документа в приложении Xamarin.Mac может потребоваться отображать другие типы окон для пользователя, например адаптируются к предпочтениям или панели инспектора.

Чтобы добавить новое окно, сделайте следующее:

1. В **обозревателе решений**откройте `Main.storyboard` файл для редактирования в Interface Builder в Xcode.
2. Перетащите новый **контроллер представления** в области конструктора:

    [![](dialog-images/new01.png "Выбрав контроллер представления из библиотеки")](dialog-images/new01.png#lightbox)
3. В **инспектор удостоверений**, введите `CustomDialogController` для **имя класса**: 

    [![](dialog-images/new02.png "Задание имени класса")](dialog-images/new02.png#lightbox)
4. Переключитесь в Visual Studio для Mac, разрешить его для синхронизации с Xcode и создать `CustomDialogController.h` файл.
5. Вернитесь в Xcode и спроектировать свой интерфейс: 

    [![](dialog-images/new03.png "Проектирование пользовательского интерфейса в Xcode")](dialog-images/new03.png#lightbox)
6. Создание **модального перехода** из главного окна приложения на новый контроллер представления, перетащив элемент управления в элементе пользовательского интерфейса, который открывается диалоговое окно, в диалоговом окне. Назначить **идентификатор** `ModalSegue`: 

    [![](dialog-images/new06.png "Модального перехода")](dialog-images/new06.png#lightbox)
6. Проводной доступ к любой **действия** и **розеток**: 

    [![](dialog-images/new04.png "Настройка действий")](dialog-images/new04.png#lightbox)
6. Сохранить изменения и вернуться в Visual Studio для Mac синхронизировать с Xcode.

Сделать `CustomDialogController.cs` внешний файл следующим образом:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDialog
{
    public partial class CustomDialogController : NSViewController
    {
        #region Private Variables
        private string _dialogTitle = "Title";
        private string _dialogDescription = "Description";
        private NSViewController _presentor;
        #endregion

        #region Computed Properties
        public string DialogTitle {
            get { return _dialogTitle; }
            set { _dialogTitle = value; }
        }

        public string DialogDescription {
            get { return _dialogDescription; }
            set { _dialogDescription = value; }
        }

        public NSViewController Presentor {
            get { return _presentor; }
            set { _presentor = value; }
        }
        #endregion

        #region Constructors
        public CustomDialogController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            // Set initial title and description
            Title.StringValue = DialogTitle;
            Description.StringValue = DialogDescription;
        }
        #endregion

        #region Private Methods
        private void CloseDialog() {
            Presentor.DismissViewController (this);
        }
        #endregion

        #region Custom Actions
        partial void AcceptDialog (Foundation.NSObject sender) {
            RaiseDialogAccepted();
            CloseDialog();
        }

        partial void CancelDialog (Foundation.NSObject sender) {
            RaiseDialogCanceled();
            CloseDialog();
        }
        #endregion

        #region Events
        public EventHandler DialogAccepted;

        internal void RaiseDialogAccepted() {
            if (this.DialogAccepted != null)
                this.DialogAccepted (this, EventArgs.Empty);
        }

        public EventHandler DialogCanceled;

        internal void RaiseDialogCanceled() {
            if (this.DialogCanceled != null)
                this.DialogCanceled (this, EventArgs.Empty);
        }
        #endregion
    }
}
```

Этот код предоставляет несколько свойств, чтобы задать заголовок и описание диалогового окна и несколько событий реагировать в диалоговом окне отменена или принято.

Далее следует изменить `ViewController.cs` файл, переопределить `PrepareForSegue` метода и это должно выглядеть следующим образом:

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on the segue name
    switch (segue.Identifier) {
    case "ModalSegue":
        var dialog = segue.DestinationController as CustomDialogController;
        dialog.DialogTitle = "MacDialog";
        dialog.DialogDescription = "This is a sample dialog.";
        dialog.DialogAccepted += (s, e) => {
            Console.WriteLine ("Dialog accepted");
            DismissViewController (dialog);
        };
        dialog.Presentor = this;
        break;
    }
}
```

Этот код инициализирует перехода, который мы определили в конструктора Interface Builder для нас диалоговое окно и устанавливает заголовок и описание. Он также обрабатывает выбор, вносимые пользователем в диалоговом окне.

Можно запустить наше приложение и отобразить настраиваемое диалоговое окно:

[![](dialog-images/new05.png "Пример этого окна")](dialog-images/new05.png#lightbox)

Дополнительные сведения об использовании windows в приложении Xamarin.Mac, см. в разделе наших [работа с Windows](~/mac/user-interface/window.md) документации.

<a name="Creating_a_Custom_Sheet" />

## <a name="creating-a-custom-sheet"></a>Создание пользовательских

Объект _лист_ является модальным диалоговым окном, присоединенный к окну данного документа, предотвращая взаимодействие с окном, пока они закрыть диалоговое окно. Лист присоединяется к окну, из которого он возникает и в любой момент времени только один лист может быть открыт для окна. 

Чтобы создать пользовательский лист в Xamarin.Mac, давайте сделайте следующее:

1. В **обозревателе решений**откройте `Main.storyboard` файл для редактирования в Interface Builder в Xcode.
2. Перетащите новый **контроллер представления** в области конструктора:

    [![](dialog-images/new01.png "Выбрав контроллер представления из библиотеки")](dialog-images/new01.png#lightbox)
2. Разработка пользовательского интерфейса.

    [![](dialog-images/sheet01.png "Дизайн пользовательского интерфейса")](dialog-images/sheet01.png#lightbox)
3. Создание **Segue лист** из главное окно на новый контроллер представления: 

    [![](dialog-images/sheet02.png "Выбор типа перехода таблицы стилей")](dialog-images/sheet02.png#lightbox)
4. В **инспектор удостоверений**, назовите контроллер представления **класс** `SheetViewController`: 

    [![](dialog-images/sheet03.png "Задание имени класса")](dialog-images/sheet03.png#lightbox)
5. Определите необходимые **розеток** и **действия**: 

    [![](dialog-images/sheet04.png "Определение необходимых переменных экземпляров и действий")](dialog-images/sheet04.png#lightbox)
6. Сохранить изменения и вернуться в Visual Studio для Mac для синхронизации.

Далее следует изменить `SheetViewController.cs` файл и это должно выглядеть следующим образом:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDialog
{
    public partial class SheetViewController : NSViewController
    {
        #region Private Variables
        private string _userName = "";
        private string _password = "";
        private NSViewController _presentor;
        #endregion

        #region Computed Properties
        public string UserName {
            get { return _userName; }
            set { _userName = value; }
        }

        public string Password {
            get { return _password;}
            set { _password = value;}
        }

        public NSViewController Presentor {
            get { return _presentor; }
            set { _presentor = value; }
        }
        #endregion

        #region Constructors
        public SheetViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            // Set initial values
            NameField.StringValue = UserName;
            PasswordField.StringValue = Password;

            // Wireup events
            NameField.Changed += (sender, e) => {
                UserName = NameField.StringValue;
            };
            PasswordField.Changed += (sender, e) => {
                Password = PasswordField.StringValue;
            };
        }
        #endregion

        #region Private Methods
        private void CloseSheet() {
            Presentor.DismissViewController (this);
        }
        #endregion

        #region Custom Actions
        partial void AcceptSheet (Foundation.NSObject sender) {
            RaiseSheetAccepted();
            CloseSheet();
        }

        partial void CancelSheet (Foundation.NSObject sender) {
            RaiseSheetCanceled();
            CloseSheet();
        }
        #endregion

        #region Events
        public EventHandler SheetAccepted;

        internal void RaiseSheetAccepted() {
            if (this.SheetAccepted != null)
                this.SheetAccepted (this, EventArgs.Empty);
        }

        public EventHandler SheetCanceled;

        internal void RaiseSheetCanceled() {
            if (this.SheetCanceled != null)
                this.SheetCanceled (this, EventArgs.Empty);
        }
        #endregion
    }
}
```

Далее следует изменить `ViewController.cs` файл, изменить `PrepareForSegue` метода и это должно выглядеть следующим образом:

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on the segue name
    switch (segue.Identifier) {
    case "ModalSegue":
        var dialog = segue.DestinationController as CustomDialogController;
        dialog.DialogTitle = "MacDialog";
        dialog.DialogDescription = "This is a sample dialog.";
        dialog.DialogAccepted += (s, e) => {
            Console.WriteLine ("Dialog accepted");
            DismissViewController (dialog);
        };
        dialog.Presentor = this;
        break;
    case "SheetSegue":
        var sheet = segue.DestinationController as SheetViewController;
        sheet.SheetAccepted += (s, e) => {
            Console.WriteLine ("User Name: {0} Password: {1}", sheet.UserName, sheet.Password);
        };
        sheet.Presentor = this;
        break;
    }
}
```

Если запустить наше приложение и откройте страницу, он будет подключен к окна:

[![](dialog-images/sheet08.png "Пример листа")](dialog-images/sheet08.png#lightbox)

<a name="Creating_a_Preferences_Dialog" />

## <a name="creating-a-preferences-dialog"></a>Создание диалоговое окно предпочтений

Прежде чем мы макета представления предпочтений в конструктор Interface Builder, необходимо добавить тип пользовательского segue обработать смену out предпочтений. Добавьте новый класс в проект и назовите его `ReplaceViewSeque `. Измените класс и это должно выглядеть следующим образом:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacWindows
{
    [Register("ReplaceViewSeque")]
    public class ReplaceViewSeque : NSStoryboardSegue
    {
        #region Constructors
        public ReplaceViewSeque() {

        }

        public ReplaceViewSeque (string identifier, NSObject sourceController, NSObject destinationController) : base(identifier,sourceController,destinationController) {

        }

        public ReplaceViewSeque (IntPtr handle) : base(handle) {
        }

        public ReplaceViewSeque (NSObjectFlag x) : base(x) {
        }
        #endregion

        #region Override Methods
        public override void Perform ()
        {
            // Cast the source and destination controllers
            var source = SourceController as NSViewController;
            var destination = DestinationController as NSViewController;

            // Is there a source?
            if (source == null) {
                // No, get the current key window
                var window = NSApplication.SharedApplication.KeyWindow;

                // Swap the controllers
                window.ContentViewController = destination;

                // Release memory
                window.ContentViewController?.RemoveFromParentViewController ();
            } else {
                // Swap the controllers
                source.View.Window.ContentViewController = destination;

                // Release memory
                source.RemoveFromParentViewController ();
            }
        
        }
        #endregion

    }

}
```

С помощью пользовательских переход создан можно добавить новое окно конструктора Interface Builder для обработки наших предпочтения.

Чтобы добавить новое окно, сделайте следующее:

1. В **обозревателе решений**откройте `Main.storyboard` файл для редактирования в Interface Builder в Xcode.
2. Перетащите новый **контроллер окна** в области конструктора:

    [![](dialog-images/pref01.png "Выберите контроллер окна из библиотеки")](dialog-images/pref01.png#lightbox)
3. Расположить окна рядом **меню** конструктор:

    [![](dialog-images/pref02.png "Добавление нового окна")](dialog-images/pref02.png#lightbox)
4. Создайте копии присоединенного контроллера представления, благодаря наличию вкладок в представлении предпочтения:

    [![](dialog-images/pref03.png "Добавление необходимых контроллеров представления")](dialog-images/pref03.png#lightbox)
5. Перетащите новый **инструментов контроллера** из **библиотеки**:

    [![](dialog-images/pref04.png "Выберите контроллер панели инструментов из библиотеки")](dialog-images/pref04.png#lightbox)
6. И поместите его в окне в области конструктора:

    [![](dialog-images/pref05.png "Добавление контроллера в новой панели инструментов")](dialog-images/pref05.png#lightbox)
7. Макет конструктора из панели инструментов:

    [![](dialog-images/pref06.png "Макет панели инструментов")](dialog-images/pref06.png#lightbox)
8. Щелкните и перетащите из каждого **кнопки на панели инструментов** с представлениями, созданную ранее. Выберите **Custom** segue типа:

    [![](dialog-images/pref07.png "Выбор типа перехода")](dialog-images/pref07.png#lightbox)
9. Выберите новый переход и задайте **класс** для `ReplaceViewSegue`:

    [![](dialog-images/pref08.png "Переход-класс параметров")](dialog-images/pref08.png#lightbox)
10. В **конструктор Menubar** в области конструктора в меню приложения выберите **предпочтения...** , щелкните и перетащите в окно "Параметры" для создания **Показать** segue:

    [![](dialog-images/pref09.png "Выбор типа перехода")](dialog-images/pref09.png#lightbox)
11. Сохранить изменения и вернуться в Visual Studio для Mac для синхронизации.

Если мы запустите код и выберите **предпочтения...**  из **меню приложения**, откроется окно:

[![](dialog-images/pref10.png "Пример настройки окна")](dialog-images/pref10.png#lightbox)

Дополнительные сведения о работе с Windows и на панелях инструментов см. в разделе наших [Windows](~/mac/user-interface/window.md) и [панелей инструментов](~/mac/user-interface/toolbar.md) документации.

<a name="Saving-and-Loading-Preferences" />

### <a name="saving-and-loading-preferences"></a>Сохранение и загрузка параметров

В типичных macOS приложения когда пользователь вносит изменения в любую пользовательских параметров приложения, эти изменения сохраняются автоматически. Для обработки таких приложения Xamarin.Mac, проще всего создать один класс для управления всеми персональные настройки пользователя и предоставить к нему во всей системе.

Во-первых, добавьте новый `AppPreferences` класса в проект и наследуют от `NSObject`. Предпочтения будет разработано, чтобы использовать [привязки данных и кодирования ключ-значение](~/mac/app-fundamentals/databinding.md) что сделает процесс создания и обслуживания предпочтение forms гораздо проще. Так как предпочтения будет состоять из небольшое количество простых типов данных, используйте встроенный `NSUserDefaults` для хранения и извлечения значений.

Изменить `AppPreferences.cs` файл и это должно выглядеть следующим образом:

```csharp
using System;
using Foundation;
using AppKit;

namespace SourceWriter
{
    [Register("AppPreferences")]
    public class AppPreferences : NSObject
    {
        #region Computed Properties
        [Export("DefaultLangauge")]
        public int DefaultLanguage {
            get { 
                var value = LoadInt ("DefaultLangauge", 0);
                return value; 
            }
            set {
                WillChangeValue ("DefaultLangauge");
                SaveInt ("DefaultLangauge", value, true);
                DidChangeValue ("DefaultLangauge");
            }
        }

        [Export("SmartLinks")]
        public bool SmartLinks {
            get { return LoadBool ("SmartLinks", true); }
            set {
                WillChangeValue ("SmartLinks");
                SaveBool ("SmartLinks", value, true);
                DidChangeValue ("SmartLinks");
            }
        }

        // Define any other required user preferences in the same fashion
        ...

        [Export("EditorBackgroundColor")]
        public NSColor EditorBackgroundColor {
            get { return LoadColor("EditorBackgroundColor", NSColor.White); }
            set {
                WillChangeValue ("EditorBackgroundColor");
                SaveColor ("EditorBackgroundColor", value, true);
                DidChangeValue ("EditorBackgroundColor");
            }
        }
        #endregion

        #region Constructors
        public AppPreferences ()
        {
        }
        #endregion

        #region Public Methods
        public int LoadInt(string key, int defaultValue) {
            // Attempt to read int
            var number = NSUserDefaults.StandardUserDefaults.IntForKey(key);

            // Take action based on value
            if (number == null) {
                return defaultValue;
            } else {
                return (int)number;
            }
        }
            
        public void SaveInt(string key, int value, bool sync) {
            NSUserDefaults.StandardUserDefaults.SetInt(value, key);
            if (sync) NSUserDefaults.StandardUserDefaults.Synchronize ();
        }

        public bool LoadBool(string key, bool defaultValue) {
            // Attempt to read int
            var value = NSUserDefaults.StandardUserDefaults.BoolForKey(key);

            // Take action based on value
            if (value == null) {
                return defaultValue;
            } else {
                return value;
            }
        }

        public void SaveBool(string key, bool value, bool sync) {
            NSUserDefaults.StandardUserDefaults.SetBool(value, key);
            if (sync) NSUserDefaults.StandardUserDefaults.Synchronize ();
        }

        public string NSColorToHexString(NSColor color, bool withAlpha) {
            //Break color into pieces
            nfloat red=0, green=0, blue=0, alpha=0;
            color.GetRgba (out red, out green, out blue, out alpha);

            // Adjust to byte
            alpha *= 255;
            red *= 255;
            green *= 255;
            blue *= 255;

            //With the alpha value?
            if (withAlpha) {
                return String.Format ("#{0:X2}{1:X2}{2:X2}{3:X2}", (int)alpha, (int)red, (int)green, (int)blue);
            } else {
                return String.Format ("#{0:X2}{1:X2}{2:X2}", (int)red, (int)green, (int)blue);
            }
        }

        public NSColor NSColorFromHexString (string hexValue)
        {
            var colorString = hexValue.Replace ("#", "");
            float red, green, blue, alpha;

            // Convert color based on length
            switch (colorString.Length) {
            case 3 : // #RGB
                red = Convert.ToInt32(string.Format("{0}{0}", colorString.Substring(0, 1)), 16) / 255f;
                green = Convert.ToInt32(string.Format("{0}{0}", colorString.Substring(1, 1)), 16) / 255f;
                blue = Convert.ToInt32(string.Format("{0}{0}", colorString.Substring(2, 1)), 16) / 255f;
                return NSColor.FromRgba(red, green, blue, 1.0f);
            case 6 : // #RRGGBB
                red = Convert.ToInt32(colorString.Substring(0, 2), 16) / 255f;
                green = Convert.ToInt32(colorString.Substring(2, 2), 16) / 255f;
                blue = Convert.ToInt32(colorString.Substring(4, 2), 16) / 255f;
                return NSColor.FromRgba(red, green, blue, 1.0f);
            case 8 : // #AARRGGBB
                alpha = Convert.ToInt32(colorString.Substring(0, 2), 16) / 255f;
                red = Convert.ToInt32(colorString.Substring(2, 2), 16) / 255f;
                green = Convert.ToInt32(colorString.Substring(4, 2), 16) / 255f;
                blue = Convert.ToInt32(colorString.Substring(6, 2), 16) / 255f;
                return NSColor.FromRgba(red, green, blue, alpha);
            default :
                throw new ArgumentOutOfRangeException(string.Format("Invalid color value '{0}'. It should be a hex value of the form #RBG, #RRGGBB or #AARRGGBB", hexValue));
            }
        }

        public NSColor LoadColor(string key, NSColor defaultValue) {

            // Attempt to read color
            var hex = NSUserDefaults.StandardUserDefaults.StringForKey(key);

            // Take action based on value
            if (hex == null) {
                return defaultValue;
            } else {
                return NSColorFromHexString (hex);
            }
        }

        public void SaveColor(string key, NSColor color, bool sync) {
            // Save to default
            NSUserDefaults.StandardUserDefaults.SetString(NSColorToHexString(color,true), key);
            if (sync) NSUserDefaults.StandardUserDefaults.Synchronize ();
        }
        #endregion
    }
}
```

Этот класс содержит несколько вспомогательные процедуры, такие как `SaveInt`, `LoadInt`, `SaveColor`, `LoadColor`, и т.д., чтобы сделать работу с `NSUserDefaults` проще. Кроме того, поскольку `NSUserDefaults` отсутствует встроенный способ обработки `NSColors`, `NSColorToHexString` и `NSColorFromHexString` методы используются для преобразования цветов в веб-шестнадцатеричных строк (`#RRGGBBAA` где `AA` является альфа-прозрачность), которые могут быть легко хранимых и извлекаются.

В `AppDelegate.cs` файл, создать экземпляр **AppPreferences** объект, который будет использоваться на уровне приложения:

```csharp
using AppKit;
using Foundation;
using System.IO;
using System;

namespace SourceWriter
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public int NewWindowNumber { get; set;} = -1;

        public AppPreferences Preferences { get; set; } = new AppPreferences();
        #endregion

        #region Constructors
        public AppDelegate ()
        {
        }
        #endregion
        
        ...
```

<a name="Wiring-Preferences-to-Preference-Views" />

### <a name="wiring-preferences-to-preference-views"></a>Персональные настройки привязки для настройки представления

Подключите класс предпочтений к элементам пользовательского интерфейса на окно предпочтений и представления, созданной ранее. В построителе интерфейса выбрать контроллер представления предпочтений и переключиться в режим **инспектор удостоверений**, создайте пользовательский класс для контроллера: 

[![](dialog-images/prefs12.png "Инспектор удостоверений")](dialog-images/prefs12.png#lightbox)

Переключитесь в Visual Studio для Mac для синхронизации изменений и откройте только что созданный класс для редактирования. Сделайте класс следующий вид:

```csharp
using System;
using Foundation;
using AppKit;

namespace SourceWriter
{
    public partial class EditorPrefsController : NSViewController
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        [Export("Preferences")]
        public AppPreferences Preferences {
            get { return App.Preferences; }
        }
        #endregion

        #region Constructors
        public EditorPrefsController (IntPtr handle) : base (handle)
        {
        }
        #endregion
    }
}
```

Обратите внимание на то, что этот класс сделала здесь две вещи: во-первых, это вспомогательный объект `App` свойство доступ к **AppDelegate** проще. Во-вторых, `Preferences` свойство предоставляет глобальный **AppPreferences** класса для привязки данных с элементами управления интерфейса помещаются в этом представлении.

Затем дважды щелкните файл раскадровки, чтобы повторно открыть его в конструктор Interface Builder (и только что внесенных выше изменений см. в разделе). Перетащите любые элементы управления пользовательского интерфейса, необходимые для создания интерфейса предпочтения в представление. Для каждого элемента управления, переключитесь в **привязки инспектор** и привязать к отдельные свойства **AppPreference** класса:

[![](dialog-images/prefs13.png "Инспектор привязки")](dialog-images/prefs13.png#lightbox)

Повторите описанные выше действия для всех панелей (контроллеров представления) и необходимые свойства предпочтений.

<a name="Applying-Preference-Changes-to-All-Open-Windows" />

### <a name="applying-preference-changes-to-all-open-windows"></a>Применение предпочтений изменения всех открытых Windows

Как уже говорилось выше, в macOS типичные приложения, когда пользователь вносит изменения в любую пользовательских параметров приложения, эти изменения сохраняются автоматически и применить к любой версии windows пользователь может быть открыт в приложении.

Этот процесс происходит плавно и незаметно для пользователей с конечным пользователям, с минимальной необходимостью программирования рабочих позволит тщательное планирование и проектирование установки вашего приложения и windows.

Для окна, в который будут использовать параметры приложения, добавьте следующее свойство вспомогательный его содержимого контроллер представления, доступ к нашей **AppDelegate** проще:

```csharp
#region Application Access
public static AppDelegate App {
    get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
}
#endregion
```

Добавьте класс для настройки содержимого или поведения в зависимости от предпочтений пользователя:

```csharp
public void ConfigureEditor() {

    // General Preferences
    TextEditor.AutomaticLinkDetectionEnabled = App.Preferences.SmartLinks;
    TextEditor.AutomaticQuoteSubstitutionEnabled = App.Preferences.SmartQuotes;
    ...

}
``` 

Вам потребуется вызвать метод конфигурации, при первом открытии окна чтобы убедиться в том, что он соответствует персональные настройки пользователя.

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Configure editor from user preferences
    ConfigureEditor ();
    ...
}
```

Далее следует изменить `AppDelegate.cs` файл и добавьте следующий метод для применения любого предпочтений изменения всех открытых окон:

```csharp
public void UpdateWindowPreferences() {

    // Process all open windows
    for(int n=0; n<NSApplication.SharedApplication.Windows.Length; ++n) {
        var content = NSApplication.SharedApplication.Windows[n].ContentViewController as ViewController;
        if (content != null ) {
            // Reformat all text
            content.ConfigureEditor ();
        }
    }

}
```

Затем добавьте `PreferenceWindowDelegate` класса в проект и это должно выглядеть следующим образом:

```csharp
using System;
using AppKit;
using System.IO;
using Foundation;

namespace SourceWriter
{
    public class PreferenceWidowDelegate : NSWindowDelegate
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        public NSWindow Window { get; set;}
        #endregion

        #region constructors
        public PreferenceWidowDelegate (NSWindow window)
        {
            // Initialize
            this.Window = window;

        }
        #endregion

        #region Override Methods
        public override bool WindowShouldClose (Foundation.NSObject sender)
        {
            
            // Apply any changes to open windows
            App.UpdateWindowPreferences();

            return true;
        }
        #endregion
    }
}
```

В результате изменения предпочтений отправку всех open Windows при закрытии окна предпочтения.

Наконец измените контроллер окна предпочтения, и добавьте созданный ранее делегат:

```csharp
using System;
using Foundation;
using AppKit;

namespace SourceWriter
{
    public partial class PreferenceWindowController : NSWindowController
    {
        #region Constructors
        public PreferenceWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Initialize
            Window.Delegate = new PreferenceWidowDelegate(Window);
            Toolbar.SelectedItemIdentifier = "General";
        }
        #endregion
    }
}
```

Все эти изменения в месте, если пользователь изменяет параметры приложения и закрывает окно предпочтений, изменения будут применяться для всех открытых Windows:

[![](dialog-images/prefs14.png "Пример настройки окна")](dialog-images/prefs14.png#lightbox)

<a name="The_Open_Dialog" />

## <a name="the-open-dialog"></a>Откройте диалоговое окно

Откройте диалоговое окно предоставляет пользователям согласованный способ найти и открыть элемент в приложении. Чтобы отобразить окно открытия в приложении Xamarin.Mac, используйте следующий код:

```csharp
var dlg = NSOpenPanel.OpenPanel;
dlg.CanChooseFiles = true;
dlg.CanChooseDirectories = false;
dlg.AllowedFileTypes = new string[] { "txt", "html", "md", "css" };

if (dlg.RunModal () == 1) {
    // Nab the first file
    var url = dlg.Urls [0];

    if (url != null) {
        var path = url.Path;

        // Create a new window to hold the text
        var newWindowController = new MainWindowController ();
        newWindowController.Window.MakeKeyAndOrderFront (this);

        // Load the text into the window
        var window = newWindowController.Window as MainWindow;
        window.Text = File.ReadAllText(path);
        window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
        window.RepresentedUrl = url;

    }
}
```

В приведенном выше коде открываем в новом окне документа, чтобы отобразить содержимое файла. Вам потребуется заменить этот код с помощью функциональности, необходимой для приложения.

Доступны следующие свойства: при работе с `NSOpenPanel`:

- **CanChooseFiles** — Если `true` пользователь может выбрать файлы.
- **CanChooseDirectories** — Если `true` пользователь может выбрать каталоги.
- **AllowsMultipleSelection** — Если `true` пользователь может выбрать более одного файла за раз.
- **ResolveAliases** — Если `true` выбора и псевдоним, разрешает его путь исходного файла.
- **AllowedFileTypes** -является массивом строк типов файлов, которые пользователь может выбрать либо расширением или _UTI_. Значение по умолчанию — `null`, что позволяет открыть любой файл.

`RunModal ()` Метод отображает диалоговое окно открытия и разрешить пользователю выбирать файлы или каталоги (как указано в свойствах) и возвращает `1` при нажатии кнопки **откройте** кнопки.

Откройте диалоговое окно возвращает выбранные файлы или каталоги пользователя как массив URL-адреса в `URL` свойство.

Если запустить программу и выберите **открыть...**  элемент из **файл** отображается меню, следующие: 

[![](dialog-images/dialog03.png "Открытое окно")](dialog-images/dialog03.png#lightbox)

<a name="The_Print_and_Page_Setup_Dialogs" />

## <a name="the-print-and-page-setup-dialogs"></a>Печать и диалоговых окнах параметры страницы

macOS предоставляет стандартный печати и страницы установки диалоговых окон, которые приложение может отображать таким образом, пользователи могут иметь согласованный печати в каждое приложение, которые они используют.

Приведенный ниже будет показано стандартное диалоговое окно печати:

```csharp
public bool ShowPrintAsSheet { get; set;} = true;
...

[Export ("showPrinter:")]
void ShowDocument (NSObject sender) {
    var dlg = new NSPrintPanel();

    // Display the print dialog as dialog box
    if (ShowPrintAsSheet) {
        dlg.BeginSheet(new NSPrintInfo(),this,this,null,new IntPtr());
    } else {
        if (dlg.RunModalWithPrintInfo(new NSPrintInfo()) == 1) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to print the document here...",
                MessageText = "Print Document",
            };
            alert.RunModal ();
        }
    }
}

```

Если установить значение `ShowPrintAsSheet` свойства `false`, для запуска приложения, так и для отображения диалогового окна печати, будет отображаться следующее:

[![](dialog-images/print01.png "Диалоговое окно печати")](dialog-images/print01.png#lightbox)

Если задать `ShowPrintAsSheet` свойства `true`, для запуска приложения, так и для отображения диалогового окна печати, будет отображаться следующее:

[![](dialog-images/print02.png "Печать листа")](dialog-images/print02.png#lightbox)

Следующий код для отображения диалогового окна страницы макета:

```csharp
[Export ("showLayout:")]
void ShowLayout (NSObject sender) {
    var dlg = new NSPageLayout();

    // Display the print dialog as dialog box
    if (ShowPrintAsSheet) {
        dlg.BeginSheet (new NSPrintInfo (), this);
    } else {
        if (dlg.RunModal () == 1) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to print the document here...",
                MessageText = "Print Document",
            };
            alert.RunModal ();
        }
    }
}
```

Если установить значение `ShowPrintAsSheet` свойства `false`, запустите приложение и отобразить диалоговое окно разметки страницы, будет отображаться следующее:

[![](dialog-images/print03.png "Диалоговое окно настройки печати")](dialog-images/print03.png#lightbox)

Если задать `ShowPrintAsSheet` свойства `true`, запустите приложение и отобразить диалоговое окно разметки страницы, будет отображаться следующее:

[![](dialog-images/print04.png "Лист установки страницы")](dialog-images/print04.png#lightbox)

Дополнительные сведения о работе с печати и страницы диалоговые окна программы установки см. в разделе Apple [NSPrintPanel](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSPrintPanel_Class/index.html#//apple_ref/doc/uid/TP40004092), [NSPageLayout](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSPageLayout_Class/index.html#//apple_ref/doc/uid/TP40004080) и [Общие сведения о печати](http://sdg.mesonet.org/people/brad/XCode3/Documentation/DocSets/com.apple.adc.documentation.AppleSnowLeopard.CoreReference.docset/Contents/Resources/Documents/#documentation/Cocoa/Conceptual/Printing/Printing.html#//apple_ref/doc/uid/10000083-SW1) документация.

<a name="The_Save_Dialog" />

## <a name="the-save-dialog"></a>Сохранить диалоговое окно

Диалоговое окно Сохранить предоставляет пользователям согласованный способ сохранения элемента в приложении.

Приведенный ниже будет показано стандартное диалоговое окно Сохранить следующее:

```csharp
public bool ShowSaveAsSheet { get; set;} = true;
...

[Export("saveDocumentAs:")]
void ShowSaveAs (NSObject sender)
{
    var dlg = new NSSavePanel ();
    dlg.Title = "Save Text File";
    dlg.AllowedFileTypes = new string[] { "txt", "html", "md", "css" };

    if (ShowSaveAsSheet) {
        dlg.BeginSheet(mainWindowController.Window,(result) => {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to save the document here...",
                MessageText = "Save Document",
            };
            alert.RunModal ();
        });
    } else {
        if (dlg.RunModal () == 1) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to save the document here...",
                MessageText = "Save Document",
            };
            alert.RunModal ();
        }
    }

}
```

`AllowedFileTypes` Свойство является массивом строк типов файлов, которые пользователь может выбрать, чтобы сохранить файл как. Тип файла могут задаваться как расширение или _UTI_. Значение по умолчанию — `null`, что позволяет все типы файлов для использования.

Если установить значение `ShowSaveAsSheet` свойства `false`, запустите приложение и выберите **Сохранить как...**  из **файл** меню будет отображаться следующее:

[![](dialog-images/save01.png "Сохранение диалоговое окно")](dialog-images/save01.png#lightbox)

Пользователь может развернуть диалоговое окно:

[![](dialog-images/save02.png "Расширенный объект диалоговое окно сохранения")](dialog-images/save02.png#lightbox)

Если установить значение `ShowSaveAsSheet` свойства `true`, запустите приложение и выберите **Сохранить как...**  из **файл** меню будет отображаться следующее:

[![](dialog-images/save03.png "Сохранение таблицы стилей")](dialog-images/save03.png#lightbox)

Пользователь может развернуть диалоговое окно:

[![](dialog-images/save04.png "Расширенный объект сохранить лист")](dialog-images/save04.png#lightbox)

Для получения дополнительных сведения о работе с диалоговым окном сохранения см. в разделе Apple [NSSavePanel](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSSavePanel_Class/index.html#//apple_ref/doc/uid/TP40004098) документации.

<a name="Summary" />

## <a name="summary"></a>Сводка

В этой статье были предприняты никакие подробные принципы работы с модальным Windows, листов и стандартной системе диалоговые окна в приложении Xamarin.Mac. Мы видели различные типы и варианты использования модальных Windows, листов и диалоговые окна, как создавать и обслуживать модальное Windows и листы в Xcode и конструктор Interface Builder и способы работы с модальным Windows, листы и диалоговые окна в коде C#.

## <a name="related-links"></a>Связанные ссылки

- [MacWindows (пример)](https://developer.xamarin.com/samples/mac/MacWindows/)
- [Привет, Mac](~/mac/get-started/hello-mac.md)
- [Меню](~/mac/user-interface/menu.md)
- [Windows](~/mac/user-interface/window.md)
- [Панели инструментов](~/mac/user-interface/toolbar.md)
- [Рекомендации по работе с человеческим интерфейсом OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Введение в Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
- [Введение в листы](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Sheets/Sheets.html#//apple_ref/doc/uid/10000002i)
- [Общие сведения о печати](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Printing/osxp_aboutprinting/osxp_aboutprt.html)
