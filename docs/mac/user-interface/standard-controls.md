---
title: Стандартные элементы управления в Xamarin.Mac
description: В этой статье рассматривается работа с стандартных элементов управления AppKit, такие как кнопки, метки, текстовые поля, флажки и сегментируются элементов управления в приложении Xamarin.Mac. Здесь описывается добавление интерфейса с помощью построителя интерфейс и взаимодействие с ними в коде.
ms.prod: xamarin
ms.assetid: d2593883-d255-431f-9781-75f04d8cecea
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: e9d110d0d7a46d431ea6fca50caffe05903ddce2
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793049"
---
# <a name="standard-controls-in-xamarinmac"></a>Стандартные элементы управления в Xamarin.Mac

_В этой статье рассматривается работа с стандартных элементов управления AppKit, такие как кнопки, метки, текстовые поля, флажки и сегментируются элементов управления в приложении Xamarin.Mac. Здесь описывается добавление интерфейса с помощью построителя интерфейс и взаимодействие с ними в коде._

При работе с C# и .NET в приложении Xamarin.Mac, имеется доступ к тому же AppKit элементов управления, работающий в *Objective-C* и *Xcode* does. Поскольку Xamarin.Mac напрямую интегрируется с Xcode, можно использовать в Xcode _интерфейс построителя_ для создания и поддержания Appkit элементов управления (или их при необходимости создать непосредственно в код C#).

Элементы управления AppKit являются элементы пользовательского интерфейса, которые используются для создания пользовательского интерфейса приложения Xamarin.Mac. Они состоят из элементов, таких как кнопки, метки, текстовые поля, флажки и Сегментированный элементов управления и вызвать мгновенной действий или результаты видимым, когда пользователь управляет их.

[![](standard-controls-images/intro01.png "В основном окне пример приложения")](standard-controls-images/intro01.png#lightbox)

В этой статье мы обсудим основы работы с элементами управления AppKit Xamarin.Mac приложения. Настоятельно рекомендуется работать через [Hello, Mac](~/mac/get-started/hello-mac.md) статью, во-первых, в частности [введение в Xcode и интерфейс построителя](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) и [выходов и действия](~/mac/get-started/hello-mac.md#Outlets_and_Actions) разделы, как он охватывает основные принципы и методы, которые будут использоваться в этой статье.

Может потребоваться рассмотреть [предоставление C# классы и методы для Objective-C](~/mac/internals/how-it-works.md) раздел [внутренние компоненты Xamarin.Mac](~/mac/internals/how-it-works.md) документов также объясняется, какие `Register` и `Export` команд используется для передачи до классов C# Objective-C-объекты и элементы пользовательского интерфейса.

<a name="Introduction_to_Controls_and_Views" />

## <a name="introduction-to-controls-and-views"></a>Знакомство с элементами управления и представления

macOS (прежнее название — Mac OS X) предоставляет стандартный набор элементов управления пользовательского интерфейса посредством AppKit Framework. Они состоят из элементов, таких как кнопки, метки, текстовые поля, флажки и Сегментированный элементов управления и вызвать мгновенной действий или результаты видимым, когда пользователь управляет их.

Все элементы управления AppKit имеют стандартных, встроенных вид, который подходит для большинства случаев, некоторые указать альтернативный внешний вид для использования в области рамки окна, либо в _эффект «вибрация»_ контекста, например боковой панели или в Центр уведомлений мини-приложения.

Apple предлагаются следующие рекомендации при работе с элементами управления AppKit.

- Использовать размеры элемента управления в одном представлении.
- Следует Избегайте, изменение размеров элементов управления по вертикали.
- Используйте системный шрифт и размер правильную текста в элементе управления.
- Используйте правильную расстояние между элементами управления.

Дополнительные сведения см. в разделе pleas [о элементы управления и представления](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1) раздел Apple [рекомендациям по интерфейсам OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/).

<a name="Using_Controls_in_a_Window_Frame" />

### <a name="using-controls-in-a-window-frame"></a>Использование элементов управления в рамки окна

Существует подмножество элементов управления AppKit, включать их можно включить в область фрейма окна стиль отображения. Пример см. в разделе инструментов почтовое приложение:

[![](standard-controls-images/mailapp.png "Рамка окна Mac")](standard-controls-images/mailapp.png#lightbox)

- **Округлить текстурой кнопка** - `NSButton` со стилем из `NSTexturedRoundedBezelStyle`.
- **Текстурой округленное сегментированных управления** - `NSSegmentedControl` со стилем из `NSSegmentStyleTexturedRounded`.
- **Текстурой округленное сегментированных управления** - `NSSegmentedControl` со стилем из `NSSegmentStyleSeparated`.
- **Округлить текстурой всплывающего меню** - `NSPopUpButton` со стилем из `NSTexturedRoundedBezelStyle`.
- **Round раскрывающемся меню текстурой** - `NSPopUpButton` со стилем из `NSTexturedRoundedBezelStyle`.
- **Панель поиска** - `NSSearchField`.

Apple предлагаются следующие рекомендации при работе с элементами управления AppKit в рамки окна.

- Не используйте стили рамки окна конкретного элемента управления в тексте окна.
- Не используйте текст окна элементы управления и стили из рамки окна.

Дополнительные сведения см. в разделе pleas [о элементы управления и представления](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1) раздел Apple [рекомендациям по интерфейсам OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/).

<a name="Creating_a_User_Interface_in_Interface_Builder" />

## <a name="creating-a-user-interface-in-interface-builder"></a>Создание пользовательского интерфейса в интерфейс построителя

При создании нового приложения Xamarin.Mac Cocoa, вы получаете стандартное окно пустым, по умолчанию. В данном окне определяется в `.storyboard` файл автоматически включен в проект. Чтобы изменить проект windows в **обозревателе решений**, дважды щелкните `Main.storyboard` файла:

[![](standard-controls-images/edit01.png "При выборе основных раскадровки в обозревателе решений")](standard-controls-images/edit01.png#lightbox)

Откроется окно конструктора в построителе Xcode элемента интерфейса:

[![](standard-controls-images/edit02.png "Редактирование раскадровки в Xcode")](standard-controls-images/edit02.png#lightbox)

Для создания пользовательского интерфейса, перетащите элементы пользовательского интерфейса (элементы управления AppKit) из **инспектора библиотеки** для **интерфейс редактора** в построителе интерфейса. В следующем примере **вертикальной разделенное представление** элемент был лекарство из **инспектора библиотеки** , помещенные в окне в **редактор интерфейса**:

[![](standard-controls-images/edit03.png "При выборе разделенное представление из библиотеки")](standard-controls-images/edit03.png#lightbox)

Дополнительные сведения о создании пользовательского интерфейса в интерфейс построителя см. в разделе нашей [введение в Xcode и интерфейс построителя](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) документации.

<a name="Sizing_and_Positioning" />

### <a name="sizing-and-positioning"></a>Изменения размеров и положения

После элемента управления был включен в пользовательском интерфейсе, используйте **Редактор ограничений** для установки ее расположение и размер, введя значения вручную и контролировать автоматически помещается элемент управления и размера, когда родительского окна или представления При изменении размеров:

[![](standard-controls-images/edit04.png "Установка ограничения")](standard-controls-images/edit04.png#lightbox)

Используйте **красным I балки** вне **Autoresizing** поле для _кнут_ (x, y) данное расположение элемента управления. Пример: 

[![](standard-controls-images/edit05.png "Изменение ограничения")](standard-controls-images/edit05.png#lightbox)

Указывает, что выбранный элемент управления (в **представление иерархии** & **интерфейс редактора**) зависнуть верхнего и правого расположение окна или представления, как его размера или перемещения. 

Другие элементы, свойства элемента управления редактора, например высоту и ширину:

[![](standard-controls-images/edit06.png "Значение высоты")](standard-controls-images/edit06.png#lightbox)

Выравнивание элементов также можно управлять с помощью ограничения **выравнивание редактор**:

[![](standard-controls-images/edit07.png "Редактор выравнивания")](standard-controls-images/edit07.png#lightbox)

> [!IMPORTANT]
> В отличие от операций ввода-вывода где (0,0) является верхний левый угол экрана, в macOS (0,0) находится в левом нижнем углу. Это происходит потому macOS использует математические систему координат с числовые значения, увеличение значения вверх и вправо. Необходимо учитывать это при помещении AppKit элементов управления в пользовательском интерфейсе.

<a name="Setting_a_Custom_Class" />

### <a name="setting-a-custom-class"></a>Пользовательский класс параметров

Существуют ситуации, когда работа с элементами управления AppKit, который будет подкласс и существующего элемента управления и создать собственную настраиваемую версию этого класса. Например определение пользовательской версии исходного списка:

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

Где `[Register("SourceListView")]` инструкция предоставляет `SourceListView` класс для Objective-C, то есть можно использовать в построителе интерфейса. Дополнительные сведения см. в разделе [предоставление C# классы и методы для Objective-C](~/mac/internals/how-it-works.md) раздел [внутренние компоненты Xamarin.Mac](~/mac/internals/how-it-works.md) документе объясняется, какие `Register` и `Export` команды, используемые для передачи до классов C# Objective-C-объекты и элементы пользовательского интерфейса.

С приведенный выше код можно перетащить элемент управления AppKit, базовый тип, который расширении, в область конструктора (в приведенном ниже примере **исходного списка**), перейдите в **инспектора идентификаторов** и задайте **настраиваемый класс** к имени, которые доступны для Objective-C (пример `SourceListView`):

[![](standard-controls-images/edit10.png "Задание пользовательского класса в Xcode")](standard-controls-images/edit10.png#lightbox)

<a name="Exposing_Outlets_and_Actions" />

### <a name="exposing-outlets-and-actions"></a>Предоставление доступа к выходам и действия

Прежде, чем элемент управления AppKit может осуществляться в коде C#, он должен быть предоставлен как либо **розетки** или и **действия**. Делать выбор определенного элемента управления либо **иерархии интерфейсов** или **интерфейс редактора** и переключитесь в **помощника представление** (Убедитесь, что `.h`окна выбранной для редактирования):

[![](standard-controls-images/edit11.png "Выбрав правильный файл для редактирования")](standard-controls-images/edit11.png#lightbox)

Перетащите элемент управления из элемента управления AppKit на предоставить `.h` файл, чтобы приступить к созданию **розетки** или **действия**:

[![](standard-controls-images/edit12.png "Для создания розетки или действие перетаскивания")](standard-controls-images/edit12.png#lightbox)

Выберите тип данных для создания и присвоения **розетки** или **действия** **имя**: 

[![](standard-controls-images/edit13.png "Настройка розетки или действие")](standard-controls-images/edit13.png#lightbox)


Дополнительные сведения о работе с **выходов** и **действия**, см. в разделе [выходов и действия](~/mac/get-started/hello-mac.md#Outlets_and_Actions) часть наших [введение в Xcode и интерфейс Построитель](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) документации.

<a name="Synchronizing_Changes_with_Xcode" />

### <a name="synchronizing-changes-with-xcode"></a>Синхронизация изменений с Xcode

При переключении обратно в Visual Studio для Mac с Xcode, любые изменения, внесенные в Xcode автоматически синхронизироваться с проектом Xamarin.Mac.

При выборе `SplitViewController.designer.cs` в **обозреватель решений** будете иметь возможность просматривать как вашей **розетки** и **действия** был построенная в коде C#:

[![](standard-controls-images/sync01.png "Синхронизация изменений с Xcode")](standard-controls-images/sync01.png#lightbox)

Обратите внимание как определение в `SplitViewController.designer.cs` файла:

```csharp
[Outlet]
AppKit.NSSplitViewItem LeftController { get; set; }

[Outlet]
AppKit.NSSplitViewItem RightController { get; set; }

[Outlet]
AppKit.NSSplitView SplitView { get; set; }
```

Выравнивание с определением в `MainWindow.h` файл в Xcode:

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

Как видите, прослушивает изменения в Visual Studio для Mac `.h` файла, а затем автоматически синхронизирует эти изменения в соответствующие `.designer.cs` файла для показа их в приложение. Можно также заметить, что `SplitViewController.designer.cs` является частичным классом, чтобы Visual Studio для Mac не нужно было изменить `SplitViewController.cs ` которого перезапишет любые изменения, которые мы внесли в класс.

Обычно не требуется открывать `SplitViewController.designer.cs` самостоятельно, она была представленных здесь только в целях обучения.

> [!IMPORTANT]
> В большинстве случаев Visual Studio для Mac автоматически см. любые изменения, внесенные в Xcode и синхронизировать их Xamarin.Mac проекта. В ситуации, если синхронизация не выполняется автоматически, вернитесь в Xcode и еще раз вернитесь в Visual Studio для Mac. Обычно эти действия запускают цикл синхронизации.

<a name="Working_with_Buttons" />

## <a name="working-with-buttons"></a>Работа с кнопками

AppKit предоставляет несколько типов кнопки, которые можно использовать при разработке пользовательского интерфейса. Дополнительные сведения см. в разделе [кнопки](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsButtons.html#//apple_ref/doc/uid/20000957-CH48-SW1) раздел Apple [рекомендациям по интерфейсам OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/buttons01.png "Примером различные типы кнопок")](standard-controls-images/buttons01.png#lightbox)

Если через кнопку **розетки**, следующий код будет отвечать на него нажатие:

```csharp
ButtonOutlet.Activated += (sender, e) => {
        FeedbackLabel.StringValue = "Button Outlet Pressed";
};
```

Для кнопок, которые доступны через **действия**, `public partial` метод будет автоматически создана автоматически с именем, выбранным в Xcode. Ответ на **действие**, завершения разделяемого метода в классе, **действия** был определен на. Пример:

```csharp
partial void ButtonAction (Foundation.NSObject sender) {
    // Do something in response to the Action
    FeedbackLabel.StringValue = "Button Action Pressed";
}
```

Для кнопок с состоянием (как **на** и **Off**), состояние можно проверить или задать для `State` свойства `NSCellStateValue` перечисления. Пример:

```csharp
DisclosureButton.Activated += (sender, e) => {
    LorumIpsum.Hidden = (DisclosureButton.State == NSCellStateValue.On);
};
```

Где `NSCellStateValue` может быть:

- **На** - помещается кнопку или элемент управления выделен (например, проверка флажка).
- **Отключение** - кнопки не занесен в стек или элемент управления не выбран.
- **Смешанный** -сочетание **на** и **Off** состояния.

<a name="Mark-a-Button-as-Default-and-Set-Key-Equivalent" />

### <a name="mark-a-button-as-default-and-set-key-equivalent"></a>Пометить кнопки по умолчанию, а также задать ключа эквивалент

Для любой кнопки, добавления пользовательского интерфейса, можно пометить как кнопки _по умолчанию_ , активируется, когда пользователь нажимает кнопку **возврата или ввод** клавиш на клавиатуре. В macOS эта кнопка получит синего цвета фона по умолчанию.

Чтобы задать кнопки по умолчанию, выберите его в интерфейс построителя в Xcode. Далее в **инспектора атрибут**выберите **эквивалентные ключ** поле и нажмите клавишу **возврата или ввод** ключ:

[![](standard-controls-images/buttons03.png "Изменение ключа эквивалент")](standard-controls-images/buttons03.png#lightbox)

Также можно назначить любое сочетание клавиш, который может использоваться для активации кнопки с помощью клавиатуры вместо мыши. Например путем нажатия клавиш C команды на приведенном выше рисунке.

При запуске приложения окно с кнопкой является ключом и с фокусом ввода, если пользователь щелкает команду C, будут активированы действие для нее (как-если бы он нажал кнопку).

<a name="Working_with_Checkboxes_and_Radio_Buttons" />

## <a name="working-with-checkboxes-and-radio-buttons"></a>Работа с флажки и переключатели

AppKit предоставляет несколько типов флажков и группы переключателей, можно использовать при разработке пользовательского интерфейса. Дополнительные сведения см. в разделе [кнопки](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsButtons.html#//apple_ref/doc/uid/20000957-CH48-SW1) раздел Apple [рекомендациям по интерфейсам OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/buttons02.png "Примером типы доступных флажок")](standard-controls-images/buttons02.png#lightbox)


Флажки и переключатели (через **торговцам**) находятся в состоянии (как **на** и **Off**), состояние можно проверить или задать для `State` свойства `NSCellStateValue` перечисления. Пример:

```csharp
AdjustTime.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Adjust Time: {0}",AdjustTime.State == NSCellStateValue.On);
};
```

Где `NSCellStateValue` может быть:

- **На** - помещается кнопку или элемент управления выделен (например, проверка флажка).
- **Отключение** - кнопки не занесен в стек или элемент управления не выбран.
- **Смешанный** -сочетание **на** и **Off** состояния.

Чтобы выбрать в группе переключателей, предоставляют переключатель, чтобы выбрать в качестве **розетки** и задайте его `State` свойство. Пример.

```csharp
partial void SelectCar (Foundation.NSObject sender) {
    TransportationCar.State = NSCellStateValue.On;
    FeedbackLabel.StringValue = "Car Selected";
}
```

Чтобы получить коллекцию переключателей работать как группы и автоматически обрабатывать выделен, создайте новый **действия** и к ней подключить все кнопки в группе:

![](standard-controls-images/buttons04.png "Создание нового действия")

Затем назначить уникальный `Tag` для каждого переключателя в **инспектора атрибут**:

![](standard-controls-images/buttons05.png "Редактирование тег кнопки переключателей")

Сохранить изменения и вернуться в Visual Studio для Mac, добавьте код для обработки **действия** все переключатели присоединяются к:

```csharp
partial void NumberChanged(Foundation.NSObject sender)
{
    var check = sender as NSButton;
    Console.WriteLine("Changed to {0}", check.Tag);
}
```

Можно использовать `Tag` свойство, чтобы узнать, какой переключатель был выбран.

<a name="Working_with_Menu_Controls" />

## <a name="working-with-menu-controls"></a>Работа с элементами управления меню

AppKit предоставляет несколько типов элементов управления меню, который можно использовать при разработке пользовательского интерфейса. Дополнительные сведения см. в разделе [элементов управления меню](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlswithMenus.html#//apple_ref/doc/uid/20000957-CH100-SW1) раздел Apple [рекомендациям по интерфейсам OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/menu01.png "Примерами элементов управления меню")](standard-controls-images/menu01.png#lightbox)

<a name="Providing-Menu-Control-Data" />

### <a name="providing-menu-control-data"></a>Предоставление данных для элемента управления меню

Элементы управления меню, доступные для macOS можно задать для заполнения раскрывающегося списка, либо из внутреннего списка (который может быть предварительно определенные в построителе интерфейса или заполняется через код), или определив собственные пользовательские внешний источник данных.

<a name="Working-with-Internal-Data" />

#### <a name="working-with-internal-data"></a>Работа с внутренние данные

Кроме определения элементов в построителе интерфейса элементов управления меню (таких как `NSComboBox`), обеспечивают полный набор методов, которые позволяют добавить, изменить или удалить элементы из внутреннего списка, они поддерживают:

- `Add` — Добавляет новый элемент в конец списка.
- `GetItem` — Возвращает элемент по указанному индексу.
- `Insert` -Вставляет новый элемент в списке в заданном месте.
- `IndexOf` — Возвращает индекс указанного элемента.
- `Remove` — Удаляет заданный элемент из списка.
- `RemoveAll` — Удаляет все элементы из списка.
- `RemoveAt` — Удаляет элемент по указанному индексу.
- `Count` — Возвращает количество элементов в списке.

> [!IMPORTANT]
> При использовании с внешним источником данных (`UsesDataSource = true`), любой из указанных способов вызова будет вызвано исключение.

<a name="Working-with-an-External-Data-Source" />

#### <a name="working-with-an-external-data-source"></a>Работа с внешним источником данных

Вместо использования встроенных внутренних данных для предоставления строк для элемента управления меню, можно при необходимости использовать внешний источник данных и указать собственные резервного хранилища для элементов (например, в базу данных SQLite).

Для работы с внешним источником данных, вы создадите экземпляр источника данных элемента управления меню (`NSComboBoxDataSource` например) и переопределить несколько методов для предоставления необходимых данных:

- `ItemCount` — Возвращает количество элементов в списке.
- `ObjectValueForItem` — Возвращает значение элемента по указанному индексу.
- `IndexOfItem` — Возвращает индекс обеспечивает значение элемента.
- `CompletedString` — Возвращает первое совпадающее значение элемента для значения частично типизированного элемента. Этот метод вызывается только в том случае, если включен автозаполнения (`Completes = true`).

См. в разделе [баз данных и поле со списком](~/mac/app-fundamentals/databases.md#Databases-and-ComboBoxes) раздел [работа с базами данных](~/mac/app-fundamentals/databases.md) документа для получения дополнительных сведений.

<a name="Adjusting-the-Lists-Appearance" />

### <a name="adjusting-the-lists-appearance"></a>Настройка внешнего вида списка

Чтобы настроить внешний вид элемента управления меню доступны следующие методы:

- `HasVerticalScroller` -Если `true`, элемент управления будет отображать вертикальную полосу прокрутки. 
- `VisibleItems` -Задайте число элементов, отображаемых при открытии элемента управления. Значение по умолчанию — пять (5).
- `IntercellSpacing` -Настроить объем свободного пространства вокруг данного элемента, предоставляя `NSSize` где `Width` задает левое и правое поля и `Height` указывает пространство до и после элемента.
- `ItemHeight` — Задает высоту каждого элемента в списке.

Раскрывающийся список типов `NSPopupButtons`, первый элемент меню содержит заголовок для элемента управления. Пример. 

[![](standard-controls-images/menu02.png "Пример управления меню")](standard-controls-images/menu02.png#lightbox)

Чтобы изменить заголовок, следует открыть этот элемент в качестве **розетки** и используйте код, аналогичный следующему:

```csharp
DropDownSelected.Title = "Item 1";
```

<a name="Manipulating-the-Selected-Items" />

### <a name="manipulating-the-selected-items"></a>Обработка выбранных элементов

Следующие методы и свойства можно управлять выбранные элементы в списке управления меню:

- `SelectItem` -Выбирает элемент по указанному индексу.
- `Select` -Выбор значения заданного элемента.
- `DeselectItem` -Отменяет выделение элемента по указанному индексу.
- `SelectedIndex` — Возвращает индекс текущего выделенного элемента.
- `SelectedValue` — Возвращает значение выбранного элемента.

Используйте `ScrollItemAtIndexToTop` для представления элемента с указанным индексом в верхней части списка и `ScrollItemAtIndexToVisible` для прокрутки списка, пока не появится элемент по указанному индексу.

<a name="Responding to Events" />

### <a name="responding-to-events"></a>Реагирование на события

Элементы управления меню предоставляют следующие события, чтобы отвечать на действия пользователя.

- `SelectionChanged` — Вызывается, когда пользователь выбрал значение из списка.
- `SelectionIsChanging` — Вызывается перед выделенные становится новый пользователь выбрал элемент.
- `WillPopup` — Вызывается перед отображением элементов раскрывающегося списка.
- `WillDismiss` Вызывается перед закрытием раскрывающемся списке элементов.

Для `NSComboBox` элементы управления, они включают все же события, что `NSTextField`, такие как `Changed` событий, который вызывается каждый раз, когда пользователь изменяет значение текста в поле со списком.

При необходимости можно ответить определенные внутренние элементы меню данных в построителе интерфейс, выбранный при присоединении элемента **действия** и использования следующего кода для реагирования на **действия** , вызванное пользователем:

```csharp
partial void ItemOne (Foundation.NSObject sender) {
    DropDownSelected.Title = "Item 1";
    FeedbackLabel.StringValue = "Item One Selected";
}
```

Дополнительные сведения о работе с меню и элементов управления меню см. в разделе нашей [меню](~/mac/user-interface/menu.md) и [всплывающая кнопка и перечислены в раскрывающемся списке](~/mac/user-interface/menu.md) документации.

<a name="Working_with_Selection_Controls" />

## <a name="working-with-selection-controls"></a>Работа с элементами управления для выбора

AppKit предоставляет несколько типов элементов управления для выбора, можно использовать при разработке пользовательского интерфейса. Дополнительные сведения см. в разделе [управления для выбора](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsSelection.html#//apple_ref/doc/uid/20000957-CH49-SW1) раздел Apple [рекомендациям по интерфейсам OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/select01.png "Примерами элементов управления выбора")](standard-controls-images/select01.png#lightbox)

Существует два способа для отслеживания, когда элемент управления для выбора имеет взаимодействие с пользователем, обеспечивая доступ к его как **действия**. Пример:

```csharp
partial void SegmentButtonPressed (Foundation.NSObject sender) {
    FeedbackLabel.StringValue = string.Format("Button {0} Pressed",SegmentButtons.SelectedSegment);
}
```

Или путем присоединения **делегат** для `Activated` события. Пример:

```csharp
TickedSlider.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Stepper Value: {0:###}",TickedSlider.IntValue);
};
```

Чтобы задать или прочитать значение с выбором, используйте `IntValue` свойство. Пример:

```csharp
FeedbackLabel.StringValue = string.Format("Stepper Value: {0:###}",TickedSlider.IntValue);
```

Специальные элементы управления (например, также цвета и изображения также) иметь свойства, для их типов значений. Пример.

```csharp
CollorWell.Color = NSColor.Red;
ImageWell.Image = NSImage.ImageNamed ("tag.png");

```

`NSDatePicker` Имеет следующие свойства для работы непосредственно с даты и времени:

- **Функция DateValue** -текущее значение даты и времени как `NSDate`.
- **Локальный** -расположения пользователя как `NSLocal`.
- **TimeInterval** -значения времени как `Double`.
- **Часовой пояс** -часовой пояс пользователя как `NSTimeZone`.

<a name="Working_with_Indicator_Controls" />

## <a name="working-with-indicator-controls"></a>Работа с элементами управления индикатора

AppKit предоставляет несколько типов элементов управления индикатор, который можно использовать при разработке пользовательского интерфейса. Дополнительные сведения см. в разделе [индикатор элементы управления](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsIndicators.html#//apple_ref/doc/uid/20000957-CH50-SW1) раздел Apple [рекомендациям по интерфейсам OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/level01.png "Примерами элементов управления индикатора")](standard-controls-images/level01.png#lightbox)

Существует два способа для отслеживания, когда элемент управления индикатором содержит взаимодействие с пользователем, либо путем предоставления доступа к его как **действия** или **розетки** и присоединение **делегат** для `Activated`события. Пример:

```csharp
LevelIndicator.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Level: {0:###}",LevelIndicator.DoubleValue);
};
```

Для чтения или задать значение индикатора элемента управления, используйте `DoubleValue` свойство. Пример:

```csharp
FeedbackLabel.StringValue = string.Format("Rating: {0:###}",Rating.DoubleValue);
```

Не определено и асинхронных индикаторов хода выполнения анимации при отображении. Используйте `StartAnimation` метод для запуска анимации, когда они отображаются. Пример:

```csharp
Indeterminate.StartAnimation (this);
AsyncProgress.StartAnimation (this);
```

Вызов `StopAnimation` метод будет останавливать анимации.

<a name="Working_with_Text_Controls" />

## <a name="working-with-text-controls"></a>Работа с текстовых элементов управления

AppKit предоставляет несколько типов текстовых элементов управления, который можно использовать при разработке пользовательского интерфейса. Дополнительные сведения см. в разделе [текстовых элементов управления](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsText.html#//apple_ref/doc/uid/20000957-CH51-SW1) раздел Apple [рекомендациям по интерфейсам OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/text01.png "Пример текстовых элементов управления")](standard-controls-images/text01.png#lightbox)

Для текстовых полей (`NSTextField`), для отслеживания взаимодействия с пользователем можно использовать следующие события:

- **Изменить** -запускается раз, пользователь изменяет значение поля. Например для каждого введенного символа.
- **EditingBegan** -возникает, когда пользователь выбирает поле для редактирования.
- **EditingEnded** — когда пользователь нажимает клавишу ВВОД в поле или покидает данное поле.

Используйте `StringValue` свойство для чтения, или задать значение поля. Пример:

```csharp
FeedbackLabel.StringValue = string.Format("User ID: {0}",UserField.StringValue);
```

Для полей, отображения или редактирования числовых значений, можно использовать `IntValue` свойства. Пример:

```csharp
FeedbackLabel.StringValue = string.Format("Number: {0}",NumberField.IntValue);
```

`NSTextView` Предоставляет полный текст рекомендуемое изменение и отображения области с форматированием встроенные. Как `NSTextField`, используйте `StringValue` свойство считывать или устанавливать ее значение.

Пример работы с представлениями текста в приложении Xamarin.Mac сложного примера, см. в разделе [SourceWriter пример приложения](https://developer.xamarin.com/samples/mac/SourceWriter/). SourceWriter — это простой редактор исходного кода, который предоставляет поддержку для автозавершения и выделения простого синтаксиса.

Код SourceWriter полностью закомментирован, и там, где это возможно, предоставлены ссылки из основных технологий и методов на соответствующую информацию в документации по руководствам для Xamarin.Mac.

<a name="Working_with_Content_Views" />

## <a name="working-with-content-views"></a>Работа с представлениями содержимого

AppKit предоставляет несколько типов содержимого представлений, который можно использовать при разработке пользовательского интерфейса. Дополнительные сведения см. в разделе [содержимого представления](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsView.html#//apple_ref/doc/uid/20000957-CH52-SW1) раздел Apple [рекомендациям по интерфейсам OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/).

[![](standard-controls-images/content01.png "Пример представления содержимого")](standard-controls-images/content01.png#lightbox)

<a name="Popovers" />

### <a name="popovers"></a>Элементы popover

Popover — это временные элемент пользовательского интерфейса, который предоставляет функции, которые непосредственно связаны с конкретной элемент управления или область на экране. Popover поверх окна, содержащего элемент управления или область, которая связана с, и его границу включает стрелку, чтобы указать точку, из которой она возникла.

Чтобы создать popover, выполните следующее:

1. Откройте `.storyboard` файл окна, которое вы хотите добавить, дважды щелкнув его в popover для **обозревателе решений**
2. Перетащите **просмотра контроллера** из **инспектора библиотеки** на **редактора интерфейса**: 

    [![](standard-controls-images/content02.png "При выборе контроллера представления из библиотеки")](standard-controls-images/content02.png#lightbox)
4. Укажите размер и макет **пользовательское представление**: 

    [![](standard-controls-images/content04.png "Изменение макета")](standard-controls-images/content04.png#lightbox)
5. Удерживая нажатой, перетащите из источника всплывающего окна на **View Controller**: 

    [![](standard-controls-images/content05.png "Для создания segue перетаскивания")](standard-controls-images/content05.png#lightbox)
6. Выберите **Popover** из всплывающего меню: 

    [![](standard-controls-images/content06.png "Segue тип параметра")](standard-controls-images/content06.png#lightbox)
7. Сохранить изменения и вернуться в Visual Studio для Mac для синхронизации с Xcode.

<a name="Tab_Views" />

### <a name="tab-views"></a>Вкладка представления

Вкладка представления состоит из списка вкладка (который выглядит аналогично сегментированных управления) в сочетании с набор представлений, которые вызываются _панелей_. Когда пользователь выбирает новую вкладку, будет отображаться области, связанные с ними. Каждая область содержит собственный набор элементов управления.

При работе с представлением вкладку в построителе интерфейс Xcode следует использовать **инспектора атрибут** для установки количества вкладок:

[![](standard-controls-images/content08.png "Изменение количество вкладок")](standard-controls-images/content08.png#lightbox)

Перейдите на вкладку каждого в **иерархии интерфейса** для задания его **заголовок** и добавления элементов пользовательского интерфейса для его **панели**:

[![](standard-controls-images/content09.png "Редактирование вкладки в Xcode")](standard-controls-images/content09.png#lightbox)

<a name="Data_Binding_AppKit_Controls" />

## <a name="data-binding-appkit-controls"></a>Элементы управления AppKit привязки данных

С помощью методов кодирования ключ-значение и привязка данных в приложении Xamarin.Mac, можно значительно уменьшить объем кода, который необходимо написать и хранить для заполнения и работать с элементами пользовательского интерфейса. Имеется также преимущество дальнейшего разделения данных резервное копирование (_модели данных_) вашего спереди завершения пользовательского интерфейса (_Model-View-Controller_), что приведет к проще поддерживать более гибкие приложения конструктор.

Ключ-значение кодирования (KVC) — это механизм для доступа к свойствам объекта неявно, с помощью ключей (специальный формат строки) для определения свойства вместо обращения к ним через переменные экземпляра или методы доступа (`get/set`). Путем реализации кодирования ключ-значение соответствует методов доступа в приложении Xamarin.Mac, можно получить доступ с другими функциями macOS рассмотрение ключ-значение (KVO), привязка данных, основные данные, Cocoa привязок и применимость сценариев.

Дополнительные сведения см. в разделе [простая привязка данных](~/mac/app-fundamentals/databinding.md#Simple_Data_Binding) часть наших [привязки данных и кодирование ключ-значение](~/mac/app-fundamentals/databinding.md) документации.


<a name="Summary" />

## <a name="summary"></a>Сводка

Подробное описание работы с стандартные AppKit элементы управления, такие как кнопки, метки, текстовые поля, флажки и Сегментированный элементов управления в приложении Xamarin.Mac вступила в этой статье. Он рассматривается добавление пользовательского интерфейса в Xcode интерфейс построителя, представив их код через выходов и действия и работа с элементами управления AppKit в коде C#.

## <a name="related-links"></a>Связанные ссылки

- [MacControls (пример)](https://developer.xamarin.com/samples/mac/MacControls/)
- [Привет, Mac](~/mac/get-started/hello-mac.md)
- [Windows](~/mac/user-interface/window.md)
- [Привязка данных и кодирование типа "ключ — значение"](~/mac/app-fundamentals/databinding.md)
- [Рекомендации по работе с человеческим интерфейсом OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Об элементах управления и представления](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1)
