---
title: Стандартные элементы управления в Xamarin.Mac
description: В этой статье рассматривается работа с помощью стандартных элементов управления AppKit, такие как кнопки, метки, текстовые поля, флажки и сегментировать элементов управления в приложении Xamarin.Mac. Он описывает, добавив их в интерфейс, с помощью конструктора Interface Builder и работать с ними в коде.
ms.prod: xamarin
ms.assetid: d2593883-d255-431f-9781-75f04d8cecea
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 4e236f2517482665406008d0b86af487e2d799dd
ms.sourcegitcommit: d09391c315336d36496880ef465a72b8974f2ac7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51579886"
---
# <a name="standard-controls-in-xamarinmac"></a>Стандартные элементы управления в Xamarin.Mac

_В этой статье рассматривается работа с помощью стандартных элементов управления AppKit, такие как кнопки, метки, текстовые поля, флажки и сегментировать элементов управления в приложении Xamarin.Mac. Он описывает, добавив их в интерфейс, с помощью конструктора Interface Builder и работать с ними в коде._

При работе с C# и .NET в приложении Xamarin.Mac, у вас есть доступ к тем же AppKit элементов управления, работающий в *Objective-C* и *Xcode* does. Поскольку Xamarin.Mac напрямую интегрируется с Xcode, можно использовать конструктор _Interface Builder_ для создания и обслуживания элементов управления Appkit (или при необходимости создать их непосредственно в коде C#).

Элементы управления AppKit являются элементы пользовательского интерфейса, которые используются для создания пользовательского интерфейса приложения Xamarin.Mac. Они состоят из элементов, таких как кнопки, метки, текстовые поля, флажки и Сегментированные элементы управления и привести к мгновенной действий или видимым результатов при пользователь управляет их.

[![](standard-controls-images/intro01.png "На главном экране пример приложения")](standard-controls-images/intro01.png#lightbox)

В этой статье мы обсудим, что узнаете основы работы с элементами управления AppKit в приложении Xamarin.Mac. Настоятельно рекомендуется работать через [Привет, Mac](~/mac/get-started/hello-mac.md) статьи, во-первых, в частности [введение в Xcode и конструкторе Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) и [переменных экземпляров и действий](~/mac/get-started/hello-mac.md#outlets-and-actions) разделы, так как он рассматриваются основные понятия и методы, которые мы будем использовать в этой статье.

Может потребоваться взгляните на [предоставление C# классы / методы Objective-c](~/mac/internals/how-it-works.md) раздел [структуре Xamarin.Mac](~/mac/internals/how-it-works.md) документов, здесь объясняется `Register` и `Export` команды используется для передачи классов C# в службе Objective-C объекты и элементы пользовательского интерфейса.

<a name="Introduction_to_Controls_and_Views" />

## <a name="introduction-to-controls-and-views"></a>Введение в элементы управления и представлений

macOS (прежнее название Mac OS X) предоставляет стандартный набор элементов управления пользовательского интерфейса с помощью платформы AppKit. Они состоят из элементов, таких как кнопки, метки, текстовые поля, флажки и Сегментированные элементы управления и привести к мгновенной действий или видимым результатов при пользователь управляет их.

Все элементы управления AppKit имеют стандартных, встроенных вид, который подходит для большинства случаев, некоторые укажите альтернативный внешний вид для использования в области фрейма окна или в _эффект «вибрация»_ контекста, например как в боковой панели или в Мини-приложение центр уведомлений.

Apple предлагает следующие рекомендации при работе с элементами управления AppKit:

- Не смешивайте размеры элемента управления в одном представлении.
- В общем случае следует Избегайте, изменение размеров элементов управления по вертикали.
- Используйте системный шрифт и размер правильное текста в элементе управления.
- Используйте пробелы между элементами управления.

Дополнительные сведения см. в разделе pleas [о элементов управления и представлений](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1) раздел Apple [рекомендации по человеческим интерфейсом OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/).

<a name="Using_Controls_in_a_Window_Frame" />

### <a name="using-controls-in-a-window-frame"></a>Использование элементов управления в рамку окна

Существуют подмножество элементов управления AppKit, который содержит стиль отображения, поэтому они могут включать в области фрейма окна. Например см. приложение "Почта" на панели инструментов:

[![](standard-controls-images/mailapp.png "Фрейм окна Mac")](standard-controls-images/mailapp.png#lightbox)

- **Округление текстурой кнопку** - `NSButton` с стилем `NSTexturedRoundedBezelStyle`.
- **Текстурой округленное сегментированных управления** - `NSSegmentedControl` с стилем `NSSegmentStyleTexturedRounded`.
- **Текстурой округленное сегментированных управления** - `NSSegmentedControl` с стилем `NSSegmentStyleSeparated`.
- **Округление текстурой всплывающего меню** - `NSPopUpButton` с стилем `NSTexturedRoundedBezelStyle`.
- **Округление текстурой раскрывающееся меню** - `NSPopUpButton` с стилем `NSTexturedRoundedBezelStyle`.
- **Панель поиска** - `NSSearchField`.

Apple предлагает следующие рекомендации при работе с элементами управления AppKit в рамку окна:

- Не используйте стили рамки окна конкретного элемента управления в тексте окна.
- Не используйте элементы управления окна текст или стили в рамке окна.

Дополнительные сведения см. в разделе pleas [о элементов управления и представлений](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1) раздел Apple [рекомендации по человеческим интерфейсом OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/).

<a name="Creating_a_User_Interface_in_Interface_Builder" />

## <a name="creating-a-user-interface-in-interface-builder"></a>Создание пользовательского интерфейса в построителе интерфейса

При создании нового приложения Cocoa для Xamarin.Mac, вы получаете стандартное окно пустым, по умолчанию. В данном окне определяется в `.storyboard` файл, автоматически включаются в проект. Редактирование своего проекта windows, в **обозревателе решений**, дважды щелкните `Main.storyboard` файла:

[![](standard-controls-images/edit01.png "Выбрав основной раскадровки в обозревателе решений")](standard-controls-images/edit01.png#lightbox)

Он откроется окно конструктора Interface Builder в Xcode:

[![](standard-controls-images/edit02.png "Изменение раскадровки в Xcode")](standard-controls-images/edit02.png#lightbox)

Чтобы создать пользовательский интерфейс, перетащите элементы пользовательского интерфейса (элементы управления AppKit) из **инспектор библиотеки** для **редактор интерфейса** в конструктор Interface Builder. В следующем примере **вертикальной разделенное представление** элемент управления был лекарства от **инспектор библиотеки** и помещаются в окне в **редактор интерфейса**:

[![](standard-controls-images/edit03.png "Выбрав разделенное представление из библиотеки")](standard-controls-images/edit03.png#lightbox)

Дополнительные сведения о создании пользовательского интерфейса в построителе интерфейса, см. в разделе наших [введение в Xcode и конструкторе Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) документации.

<a name="Sizing_and_Positioning" />

### <a name="sizing-and-positioning"></a>Изменения размеров и положения

После элемента управления был включен в пользовательском интерфейсе, используйте **Редактор ограничений** для задания его расположение и размер, введя значения вручную и контролировать использование автоматически помещается элемент управления и размера, когда родительский окно или представление изменения размера:

[![](standard-controls-images/edit04.png "Настройка ограничений")](standard-controls-images/edit04.png#lightbox)

Используйте **Red I-образных указателей** вне **Autoresizing** поле для _палочка_ элемент управления в данное (x, y) место. Пример: 

[![](standard-controls-images/edit05.png "Изменение ограничения")](standard-controls-images/edit05.png#lightbox)

Указывает, что выбранный элемент управления (в **иерархическое представление** & **редактор интерфейса**) будет оставаться правая и верхняя расположение окна или представления при перемещении или изменении размера. 

Другие элементы, свойства элемента управления редактора, например высоту и ширину:

[![](standard-controls-images/edit06.png "Значение высоты")](standard-controls-images/edit06.png#lightbox)

Выравнивание элементов также можно управлять с помощью ограничения **выравнивание редактор**:

[![](standard-controls-images/edit07.png "Редактор выравнивания")](standard-controls-images/edit07.png#lightbox)

> [!IMPORTANT]
> В отличие от iOS где (0,0) находится в верхнем левом углу экрана, в macOS (0,0) находится в левом нижнем углу. Это обусловлено macOS использует математические систему координат с числовые значения, увеличение значения вверх и вправо. Необходимо учитывать это при помещении элементов управления AppKit в пользовательском интерфейсе.

<a name="Setting_a_Custom_Class" />

### <a name="setting-a-custom-class"></a>Пользовательский класс параметров

Бывают случаи, когда работа с элементами управления AppKit, которые будет подкласс и существующего элемента управления и создать собственные пользовательские версии этого класса. Например Определение настраиваемой версии исходного списка:

```csharp
using System;
using AppKit;
using Foundation;

namespace AppKit
{
    [Register("SourceListView")]
    public class SourceListView : NSOutlineView
    {
        #region Computed Properties
        public SourceListDataSource Data {
            get {return (SourceListDataSource)this.DataSource; }
        }
        #endregion

        #region Constructors
        public SourceListView ()
        {

        }

        public SourceListView (IntPtr handle) : base(handle)
        {

        }

        public SourceListView (NSCoder coder) : base(coder)
        {

        }

        public SourceListView (NSObjectFlag t) : base(t)
        {

        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

        }
        #endregion

        #region Public Methods
        public void Initialize() {

            // Initialize this instance
            this.DataSource = new SourceListDataSource (this);
            this.Delegate = new SourceListDelegate (this);

        }

        public void AddItem(SourceListItem item) {
            if (Data != null) {
                Data.Items.Add (item);
            }
        }
        #endregion

        #region Events
        public delegate void ItemSelectedDelegate(SourceListItem item);
        public event ItemSelectedDelegate ItemSelected;

        internal void RaiseItemSelected(SourceListItem item) {
            // Inform caller
            if (this.ItemSelected != null) {
                this.ItemSelected (item);
            }
        }
        #endregion
    }
}
```

Где `[Register("SourceListView")]` инструкция предоставляет `SourceListView` класс Objective-c, поэтому можно использовать в построителе интерфейса. Дополнительные сведения см. в разделе [предоставление C# классы / методы Objective-c](~/mac/internals/how-it-works.md) раздел [структуре Xamarin.Mac](~/mac/internals/how-it-works.md) документ, он объясняет `Register` и `Export` команды, используемые для передачи классов C# в службе Objective-C объекты и элементы пользовательского интерфейса.

Приведенный выше код его можно перетащить элемент управления AppKit, базовый тип, который выполняются, в область конструктора (в примере ниже, **исходного списка**), переключитесь в **инспектор удостоверений** и задайте **пользовательского класса** к имени, который вы предоставляете коду Objective-C (пример `SourceListView`):

[![](standard-controls-images/edit10.png "Задание пользовательского класса в Xcode")](standard-controls-images/edit10.png#lightbox)

<a name="Exposing_Outlets_and_Actions" />

### <a name="exposing-outlets-and-actions"></a>Предоставление переменных экземпляров и действий

Прежде, чем элемент управления AppKit может осуществляться в коде C#, оно должно быть доступно как **розетки** или и **действие**. Чтобы сделать это выберите заданного элемента управления на **иерархия интерфейса** или **редактор интерфейса** и переключиться в режим **Assistant представление** (Убедитесь, что у вас есть `.h`окна выбранного для редактирования):

[![](standard-controls-images/edit11.png "Выбор нужного файла для редактирования")](standard-controls-images/edit11.png#lightbox)

Перетащите из элемента управления AppKit на предоставить `.h` файл, чтобы приступить к созданию **розетки** или **действие**:

[![](standard-controls-images/edit12.png "Перетаскивание для создания розетки или действие")](standard-controls-images/edit12.png#lightbox)

Выберите тип уязвимости, создать и предоставить **розетки** или **действие** **имя**: 

[![](standard-controls-images/edit13.png "Настройка розетки или действие")](standard-controls-images/edit13.png#lightbox)


Дополнительные сведения о работе с **розеток** и **действия**, см. в разделе [переменных экземпляров и действий](~/mac/get-started/hello-mac.md#outlets-and-actions) части нашей [введение в Xcode и интерфейс Построитель](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) документации.

<a name="Synchronizing_Changes_with_Xcode" />

### <a name="synchronizing-changes-with-xcode"></a>Синхронизация изменений с Xcode

При переключении обратно в Visual Studio для Mac с Xcode, любые изменения, внесенные в Xcode, автоматически синхронизируются с проектом Xamarin.Mac.

При выборе `SplitViewController.designer.cs` в **обозревателе решений** вы сможете см. в разделе как вашей **розетки** и **действие** было настроено в коде C#:

[![](standard-controls-images/sync01.png "Синхронизация изменений с Xcode")](standard-controls-images/sync01.png#lightbox)

Обратите внимание, что как в определении `SplitViewController.designer.cs` файла:

```csharp
[Outlet]
AppKit.NSSplitViewItem LeftController { get; set; }

[Outlet]
AppKit.NSSplitViewItem RightController { get; set; }

[Outlet]
AppKit.NSSplitView SplitView { get; set; }
```

Выровнять с определением в `MainWindow.h` файл в Xcode:

```csharp
@interface SplitViewController : NSSplitViewController {
    NSSplitViewItem *_LeftController;
    NSSplitViewItem *_RightController;
    NSSplitView *_SplitView;
}

@property (nonatomic, retain) IBOutlet NSSplitViewItem *LeftController;

@property (nonatomic, retain) IBOutlet NSSplitViewItem *RightController;

@property (nonatomic, retain) IBOutlet NSSplitView *SplitView;
```

Как вы видите, Visual Studio для Mac прослушивает изменения `.h` файл, а затем автоматически синхронизирует их в соответствующие `.designer.cs` файл, чтобы сделать его доступным для приложения. Также можно заметить, `SplitViewController.designer.cs` является разделяемым классом, таким образом, чтобы Visual Studio для Mac не нужно изменять `SplitViewController.cs ` который перезапишет любые изменения, которые мы внесли в класс.

Обычно не нужно будет открыть `SplitViewController.designer.cs` самостоятельно, он был представлен здесь только в целях обучения.

> [!IMPORTANT]
> В большинстве случаев Visual Studio для Mac автоматически видит изменения, внесенные в Xcode и синхронизировать их в проект Xamarin.Mac. В ситуации, если синхронизация не выполняется автоматически, вернитесь в Xcode и еще раз вернитесь в Visual Studio для Mac. Обычно эти действия запускают цикл синхронизации.

<a name="Working_with_Buttons" />

## <a name="working-with-buttons"></a>Работа с кнопками

AppKit предоставляет несколько видов кнопка, которая может использоваться в Дизайн пользовательского интерфейса. Дополнительные сведения см. в разделе [кнопки](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsButtons.html#//apple_ref/doc/uid/20000957-CH48-SW1) раздел Apple [рекомендации по человеческим интерфейсом OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/buttons01.png "Пример различные типы кнопок")](standard-controls-images/buttons01.png#lightbox)

Если кнопка предоставленный через **розетки**, следующий код будет отвечать на него при нажатии:

```csharp
ButtonOutlet.Activated += (sender, e) => {
        FeedbackLabel.StringValue = "Button Outlet Pressed";
};
```

Для кнопок, которые доступны через **действия**, `public partial` метод будет автоматически создана для вас имя, которое вы выбрали в Xcode. Реагировать на **действие**, завершить разделяемого метода в классе, **действия** был определен на. Пример:

```csharp
partial void ButtonAction (Foundation.NSObject sender) {
    // Do something in response to the Action
    FeedbackLabel.StringValue = "Button Action Pressed";
}
```

Для кнопок с состоянием (например **на** и **Off**), состояние можно проверить или задать для `State` проверяет свойство `NSCellStateValue` перечисления. Пример:

```csharp
DisclosureButton.Activated += (sender, e) => {
    LorumIpsum.Hidden = (DisclosureButton.State == NSCellStateValue.On);
};
```

Где `NSCellStateValue` может быть:

- **На** - помещается кнопки или выборе элемента управления (например, возврата типа "флажок").
- **Отключение** - не отправляется кнопку или элемент управления не выбран.
- **Смешанный** -сочетание **на** и **Off** состояний.

<a name="Mark-a-Button-as-Default-and-Set-Key-Equivalent" />

### <a name="mark-a-button-as-default-and-set-key-equivalent"></a>Пометить кнопки, так как по умолчанию и устанавливает эквивалент ключа

Для любой кнопки, добавленный для пользовательского интерфейса, можно пометить эту кнопку, как _по умолчанию_ кнопка, которая будет активироваться, когда пользователь нажимает **Return или ввод** клавиш на клавиатуре. В macOS эта кнопка получит синего цвета фона по умолчанию.

Чтобы задать кнопки по умолчанию, выберите его в Interface Builder в Xcode. Затем в **инспекторе атрибутов**выберите **эквивалент ключа** поле и нажмите клавишу **Return или ввод** ключ:

[![](standard-controls-images/buttons03.png "Изменение ключа эквивалент")](standard-controls-images/buttons03.png#lightbox)

Аналогично можно назначить любой ключевой последовательности, который может использоваться для активации кнопки с помощью клавиатуры вместо мыши. Например нажав ключи команды-C на приведенном выше рисунке.

При запуске приложения и окно с кнопкой является ключом и с фокусом ввода, если пользователь щелкает команду-C, будут активированы действие для кнопки (как-если бы он нажал кнопку).

<a name="Working_with_Checkboxes_and_Radio_Buttons" />

## <a name="working-with-checkboxes-and-radio-buttons"></a>Работа с флажков и переключателей

AppKit предоставляет несколько типов флажков и группы переключателей, можно использовать в Дизайн пользовательского интерфейса. Дополнительные сведения см. в разделе [кнопки](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsButtons.html#//apple_ref/doc/uid/20000957-CH48-SW1) раздел Apple [рекомендации по человеческим интерфейсом OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/buttons02.png "Пример типы доступных флажок")](standard-controls-images/buttons02.png#lightbox)


Флажки и переключатели (реализуются с помощью **розеток**), в состоянии (как **на** и **Off**), состояние можно проверить или задать `State` проверяет свойство `NSCellStateValue` перечисления. Пример:

```csharp
AdjustTime.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Adjust Time: {0}",AdjustTime.State == NSCellStateValue.On);
};
```

Где `NSCellStateValue` может быть:

- **На** - помещается кнопки или выборе элемента управления (например, возврата типа "флажок").
- **Отключение** - не отправляется кнопку или элемент управления не выбран.
- **Смешанный** -сочетание **на** и **Off** состояний.

Чтобы выбрать кнопку в группе переключателей, следует предоставить переключатель, чтобы выбрать в качестве **розетки** и задайте его `State` свойство. Пример.

```csharp
partial void SelectCar (Foundation.NSObject sender) {
    TransportationCar.State = NSCellStateValue.On;
    FeedbackLabel.StringValue = "Car Selected";
}
```

Чтобы получить коллекцию переключателей в качестве группы и автоматически управлять выбранное состояние, создайте новый **действие** и присоединить к ней каждого переключателя в группе:

![](standard-controls-images/buttons04.png "Создание нового действия")

Затем присваивает уникальный `Tag` для каждого переключателя в **инспекторе атрибутов**:

![](standard-controls-images/buttons05.png "Изменение тега кнопку переключателя")

Сохраните изменения и вернуться в Visual Studio для Mac, добавьте код для обработки **действие** всех переключателей присоединяются к:

```csharp
partial void NumberChanged(Foundation.NSObject sender)
{
    var check = sender as NSButton;
    Console.WriteLine("Changed to {0}", check.Tag);
}
```

Можно использовать `Tag` свойство, чтобы увидеть, какой переключатель выбран.

<a name="Working_with_Menu_Controls" />

## <a name="working-with-menu-controls"></a>Работа с элементами управления меню

AppKit предоставляет несколько типов элементов управления меню, который может использоваться в Дизайн пользовательского интерфейса. Дополнительные сведения см. в разделе [элементы управления меню](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlswithMenus.html#//apple_ref/doc/uid/20000957-CH100-SW1) раздел Apple [рекомендации по человеческим интерфейсом OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/menu01.png "Примерами элементов управления меню")](standard-controls-images/menu01.png#lightbox)

<a name="Providing-Menu-Control-Data" />

### <a name="providing-menu-control-data"></a>Предоставление данных управления меню

Элементы управления меню, доступные для macOS можно задать для заполнения раскрывающегося списка, либо из во внутренний список (который может быть предварительно определенные в построителе интерфейса или заполняется через код) или предоставив свои собственные пользовательские внешний источник данных.

<a name="Working-with-Internal-Data" />

#### <a name="working-with-internal-data"></a>Работа с внутренними данными

Кроме определения элементов в конструктор Interface Builder, элементы управления меню (таких как `NSComboBox`), предоставляют полный набор методов, которые дают возможность добавить, изменить или удалить элементы из внутреннего списка, они поддерживают:

- `Add` — Добавляет новый элемент в конец списка.
- `GetItem` — Возвращает элемент по указанному индексу.
- `Insert` — Вставляет новый элемент в списке в указанном расположении.
- `IndexOf` — Возвращает индекс заданного элемента.
- `Remove` — Удаляет заданный элемент из списка.
- `RemoveAll` — Удаляет все элементы из списка.
- `RemoveAt` — Удаляет элемент по указанному индексу.
- `Count` — Возвращает количество элементов в списке.

> [!IMPORTANT]
> Если вы используете источник данных Extern (`UsesDataSource = true`), вызова любого из вышеуказанных методов приведет к возникновению исключения.

<a name="Working-with-an-External-Data-Source" />

#### <a name="working-with-an-external-data-source"></a>Работа с внешнего источника данных

Вместо использования встроенных внутренние данные для предоставления строк для элемента управления меню, можно при необходимости использование внешнего источника данных и указать собственное резервное хранилище для элементов (например, база данных SQLite).

Для работы с внешним источником данных, вы создадите экземпляр источника данных элемента управления меню (`NSComboBoxDataSource` к примеру) и переопределить несколько методов для предоставления необходимых данных:

- `ItemCount` — Возвращает количество элементов в списке.
- `ObjectValueForItem` — Возвращает значение элемента для указанного индекса.
- `IndexOfItem` — Возвращает индекс значения элемента дают.
- `CompletedString` — Возвращает первое совпадающее значение элемента для значения частично типизированного элемента. Этот метод вызывается только в том случае, если автозаполнение включено (`Completes = true`).

См. в разделе [баз данных и ComboBox](~/mac/app-fundamentals/databases.md#Databases-and-ComboBoxes) раздел [работы с базами данных](~/mac/app-fundamentals/databases.md) документа для получения дополнительных сведений.

<a name="Adjusting-the-Lists-Appearance" />

### <a name="adjusting-the-lists-appearance"></a>Настройка внешнего вида списка

Чтобы настроить внешний вид элемента управления меню доступны следующие методы:

- `HasVerticalScroller` Если `true`, элемент управления будет отображать вертикальную полосу прокрутки. 
- `VisibleItems` -Задайте число элементов, отображаемых при открытии элемента управления. Значение по умолчанию — пять (5).
- `IntercellSpacing` -Настроить объем пространства вокруг данного элемента, предоставляя `NSSize` где `Width` задает левое и правое поля и `Height` указывает пространство до и после элемента.
- `ItemHeight` — Указывает высоту каждого элемента в списке.

Для раскрывающегося списка типов `NSPopupButtons`, первый элемент меню содержит заголовок для элемента управления. Пример. 

[![](standard-controls-images/menu02.png "Пример элемента управления меню")](standard-controls-images/menu02.png#lightbox)

Чтобы изменить заголовок, следует предоставить этот элемент как **розетки** и использовать код, аналогичный следующему:

```csharp
DropDownSelected.Title = "Item 1";
```

<a name="Manipulating-the-Selected-Items" />

### <a name="manipulating-the-selected-items"></a>Выбранные элементы управления

Следующие методы и свойства можно управлять выбранные элементы в списке элемента управления меню:

- `SelectItem` -Выбирает элемент по указанному индексу.
- `Select` -Выберите значения заданного элемента.
- `DeselectItem` -Отменяет выделение элемента по указанному индексу.
- `SelectedIndex` — Возвращает индекс выбранного элемента.
- `SelectedValue` — Возвращает значение выбранного элемента.

Используйте `ScrollItemAtIndexToTop` для представления элемента по указанному индексу в верхней части списка и `ScrollItemAtIndexToVisible` выполнить прокрутку в списке, пока не отображается элемент по указанному индексу.

<a name="Responding to Events" />

### <a name="responding-to-events"></a>Реагирование на события

Элементы управления меню предоставляют следующие события, чтобы отвечать на действия пользователя:

- `SelectionChanged` — Вызывается, когда пользователь выбрал значение из списка.
- `SelectionIsChanging` — Вызывается перед новый пользователь выбрал элемент становится активного выделения.
- `WillPopup` Вызывается перед отображением раскрывающемся списке элементов.
- `WillDismiss` Вызывается перед закрытием раскрывающемся списке элементов.

Для `NSComboBox` элементы управления, они включают все же события как `NSTextField`, такие как `Changed` событий, который вызывается каждый раз, когда пользователь редактирует значение текста в поле со списком.

При необходимости, может отвечать определенных внутренних элементов меню данных в конструктор Interface Builder, выбираемый путем присоединения элемента **действие** и использовать код, аналогичный следующему реагировать на **действие** , запущенное пользователем:

```csharp
partial void ItemOne (Foundation.NSObject sender) {
    DropDownSelected.Title = "Item 1";
    FeedbackLabel.StringValue = "Item One Selected";
}
```

Дополнительные сведения о работе с меню и элементов управления меню, см. в разделе наших [меню](~/mac/user-interface/menu.md) и [всплывающее кнопки и перечислены в раскрывающемся списке](~/mac/user-interface/menu.md) документации.

<a name="Working_with_Selection_Controls" />

## <a name="working-with-selection-controls"></a>Работа с элементами управления для выбора

AppKit предоставляет несколько видов элементы управления выбором, который может использоваться в Дизайн пользовательского интерфейса. Дополнительные сведения см. в разделе [элементы управления выбором](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsSelection.html#//apple_ref/doc/uid/20000957-CH49-SW1) раздел Apple [рекомендации по человеческим интерфейсом OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/select01.png "Элементы управления выбором пример")](standard-controls-images/select01.png#lightbox)

Существует два способа для отслеживания, когда элемент управления для выбора имеет взаимодействия с пользователем, обеспечивая его как **действие**. Пример:

```csharp
partial void SegmentButtonPressed (Foundation.NSObject sender) {
    FeedbackLabel.StringValue = string.Format("Button {0} Pressed",SegmentButtons.SelectedSegment);
}
```

Или путем присоединения **делегат** для `Activated` событий. Пример:

```csharp
TickedSlider.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Stepper Value: {0:###}",TickedSlider.IntValue);
};
```

Чтобы задать или прочитать значение элемент управления для выбора, используйте `IntValue` свойство. Пример:

```csharp
FeedbackLabel.StringValue = string.Format("Stepper Value: {0:###}",TickedSlider.IntValue);
```

Специальные элементы управления (например, также цвета и изображения хорошо) имеют определенные свойства для своих типов значений. Пример.

```csharp
ColorWell.Color = NSColor.Red;
ImageWell.Image = NSImage.ImageNamed ("tag.png");

```

`NSDatePicker` Имеет следующие свойства для работы непосредственно с даты и времени:

- **DateValue** -то текущее значение даты и времени, что `NSDate`.
- **Локальный** -из местоположения пользователя как `NSLocal`.
- **TimeInterval** -значения времени как `Double`.
- **Часовой пояс** -часового пояса пользователя как `NSTimeZone`.

<a name="Working_with_Indicator_Controls" />

## <a name="working-with-indicator-controls"></a>Работа с элементами управления индикатора

AppKit предоставляет несколько типов элементов управления индикатор, который может использоваться в Дизайн пользовательского интерфейса. Дополнительные сведения см. в разделе [элементы управления индикатор](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsIndicators.html#//apple_ref/doc/uid/20000957-CH50-SW1) раздел Apple [рекомендации по человеческим интерфейсом OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/level01.png "Примерами элементов управления индикатора")](standard-controls-images/level01.png#lightbox)

Существует два способа для отслеживания, если элемент управления индикатора имеет взаимодействия с пользователем, либо путем предоставления его как **действие** или **розетки** и присоединение **делегат** для `Activated`событий. Пример:

```csharp
LevelIndicator.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Level: {0:###}",LevelIndicator.DoubleValue);
};
```

Чтобы прочитать или задать значение элемента управления индикатор, используйте `DoubleValue` свойство. Пример:

```csharp
FeedbackLabel.StringValue = string.Format("Rating: {0:###}",Rating.DoubleValue);
```

Не определено и асинхронной индикаторы хода выполнения анимации при отображении. Используйте `StartAnimation` метод для запуска анимации, когда они отображаются. Пример:

```csharp
Indeterminate.StartAnimation (this);
AsyncProgress.StartAnimation (this);
```

Вызов `StopAnimation` метод останавливает анимацию.

<a name="Working_with_Text_Controls" />

## <a name="working-with-text-controls"></a>Работа с текстовыми элементами управления

AppKit предоставляет несколько типов текстовых элементов управления, который может использоваться в Дизайн пользовательского интерфейса. Дополнительные сведения см. в разделе [текстовые элементы управления](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsText.html#//apple_ref/doc/uid/20000957-CH51-SW1) раздел Apple [рекомендации по человеческим интерфейсом OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/text01.png "Пример текстовых элементов управления")](standard-controls-images/text01.png#lightbox)

Для текстовых полей (`NSTextField`), можно использовать следующие события для отслеживания взаимодействия с пользователем:

- **Изменить** — срабатывает в любое время, пользователь изменяет значение поля. Например для каждого введенного символа.
- **EditingBegan** -возникает, когда пользователь выбирает поле для редактирования.
- **EditingEnded** — когда пользователь нажимает клавишу ВВОД в поле или покидает данное поле.

Используйте `StringValue` свойство для чтения, или задать значение поля. Пример:

```csharp
FeedbackLabel.StringValue = string.Format("User ID: {0}",UserField.StringValue);
```

Для полей, отображения или редактирования числовые значения, можно использовать `IntValue` свойство. Пример:

```csharp
FeedbackLabel.StringValue = string.Format("Number: {0}",NumberField.IntValue);
```

`NSTextView` Предоставляет полный текст рекомендуемое изменение и отображение области с помощью встроенных форматирование. Как и `NSTextField`, используйте `StringValue` свойство считывать или устанавливать ее значение.

Пример сложного примера работы с представлений текста в приложении Xamarin.Mac, см. в разделе [пример приложения SourceWriter](https://developer.xamarin.com/samples/mac/SourceWriter/). SourceWriter — это простой редактор исходного кода, который предоставляет поддержку для автозавершения и выделения простого синтаксиса.

Код SourceWriter полностью закомментирован, и там, где это возможно, предоставлены ссылки из основных технологий и методов на соответствующую информацию в документации по руководствам для Xamarin.Mac.

<a name="Working_with_Content_Views" />

## <a name="working-with-content-views"></a>Работа с представлениями содержимого

AppKit предоставляет несколько типов представлений содержимого, который может использоваться в Дизайн пользовательского интерфейса. Дополнительные сведения см. в разделе [содержимого представления](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsView.html#//apple_ref/doc/uid/20000957-CH52-SW1) раздел Apple [рекомендации по человеческим интерфейсом OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/).

[![](standard-controls-images/content01.png "Приведен пример представления содержимого")](standard-controls-images/content01.png#lightbox)

<a name="Popovers" />

### <a name="popovers"></a>Popovers

Контекстном — это временные элемент пользовательского интерфейса, который предоставляет функции, которые непосредственно связаны с определенным элемент управления или область на экране. Контекстном поверх окна, содержащего элемент управления или область, которая связана с, и его границу включает стрелку, чтобы указать точку, из которого оно появилось.

Чтобы создать контекстном, сделайте следующее:

1. Откройте `.storyboard` файл окна, которое вы хотите добавить, дважды щелкнув его в контекстном для **обозревателе решений**
2. Перетащите **увидеть контроллер** из **инспектор библиотеки** на **редактор интерфейса**: 

    [![](standard-controls-images/content02.png "Выбрав контроллер представления из библиотеки")](standard-controls-images/content02.png#lightbox)
4. Определить размер и макет **пользовательское представление**: 

    [![](standard-controls-images/content04.png "Изменение макета")](standard-controls-images/content04.png#lightbox)
5. Щелкните и перетащите из источника всплывающего окна на **контроллер представления**: 

    [![](standard-controls-images/content05.png "Перетаскивание для создания объекта перехода")](standard-controls-images/content05.png#lightbox)
6. Выберите **контекстном** во всплывающем меню: 

    [![](standard-controls-images/content06.png "Выбор типа перехода")](standard-controls-images/content06.png#lightbox)
7. Сохранить изменения и вернуться в Visual Studio для Mac синхронизировать с Xcode.

<a name="Tab_Views" />

### <a name="tab-views"></a>Вкладка представления

Вкладка представления состоит из вкладок, (который выглядит аналогично сегментированных управления) в сочетании с набор представлений, которые вызываются _области_. Когда пользователь выбирает новую вкладку, области, подключенный к ней будет отображаться. Каждая область содержит свой собственный набор элементов управления.

При работе с представление вкладки в конструктора Interface Builder, использовать **инспекторе атрибутов** задать число вкладок:

[![](standard-controls-images/content08.png "Редактирование число вкладок")](standard-controls-images/content08.png#lightbox)

Перейдите на вкладку каждой в **иерархия интерфейса** присвоить его **Title** и добавлять элементы пользовательского интерфейса для его **панели**:

[![](standard-controls-images/content09.png "Редактирование вкладки в Xcode")](standard-controls-images/content09.png#lightbox)

<a name="Data_Binding_AppKit_Controls" />

## <a name="data-binding-appkit-controls"></a>Элементы управления AppKit привязки данных

С помощью методов кодирования ключ-значение и привязка данных в приложении Xamarin.Mac, можно значительно сократить объем кода, который необходимо писать и поддерживать для заполнения и работать с элементами пользовательского интерфейса. Вы также можете использовать дальнейшего разделения данных резервного (_модель данных_) из вашего front end Interface пользователя (_Model-View-Controller_), что приведет к проще было обслуживать более гибкие приложения Структура.

Ключ-значение кодирования (KVC) — это механизм для доступа к свойствам объекта косвенно, с помощью ключей (специальный формат строки) для определения свойства вместо обращения к ним через переменные экземпляра или методы доступа (`get/set`). Путем реализации кодирования ключ-значение соответствующих методов доступа в приложении Xamarin.Mac, можно получить доступ к другим функциям macOS, например наблюдения ключ-значение (KVO), привязка данных, Core Data, Cocoa привязок и распространяя применимость сценариев.

Дополнительные сведения см. в разделе [простая привязка данных](~/mac/app-fundamentals/databinding.md#Simple_Data_Binding) части нашей [привязки данных и кодирования ключ-значение](~/mac/app-fundamentals/databinding.md) документации.


<a name="Summary" />

## <a name="summary"></a>Сводка

В этой статье были предприняты никакие подробные принципы работы с стандартные AppKit элементы управления, такие как кнопки, метки, текстовые поля, флажки и Сегментированные элементы управления в приложении Xamarin.Mac. Он рассматривается добавление пользовательского интерфейса в конструктора Interface Builder, написанному для кода с помощью переменных экземпляров и действий и работа с элементами управления AppKit в коде C#.

## <a name="related-links"></a>Связанные ссылки

- [MacControls (пример)](https://developer.xamarin.com/samples/mac/MacControls/)
- [Привет, Mac](~/mac/get-started/hello-mac.md)
- [Windows](~/mac/user-interface/window.md)
- [Привязка данных и кодирование типа "ключ — значение"](~/mac/app-fundamentals/databinding.md)
- [Рекомендации по работе с человеческим интерфейсом OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Об элементах управления и представлений](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1)
