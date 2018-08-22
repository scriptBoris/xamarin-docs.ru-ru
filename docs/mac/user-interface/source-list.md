---
title: Списки-источники в Xamarin.Mac
description: В этой статье рассматривается работа с списки-источники в приложении Xamarin.Mac. Он описывает создание и поддержание списки-источники в Xcode и конструкторе Interface Builder и работать с ними в коде C#.
ms.prod: xamarin
ms.assetid: 651A3649-5AA8-4133-94D6-4873D99F7FCC
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: de51395192ab009f5c7fa338a4414ba553610b8c
ms.sourcegitcommit: 47709db4d115d221e97f18bc8111c95723f6cb9b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2018
ms.locfileid: "40251251"
---
# <a name="source-lists-in-xamarinmac"></a>Списки-источники в Xamarin.Mac

_В этой статье рассматривается работа с списки-источники в приложении Xamarin.Mac. Он описывает создание и поддержание списки-источники в Xcode и конструкторе Interface Builder и работать с ними в коде C#._

При работе с C# и .NET в приложении Xamarin.Mac, у вас есть доступ к тому же списков источников, работающему *Objective-C* и *Xcode* does. Поскольку Xamarin.Mac напрямую интегрируется с Xcode, можно использовать конструктор _Interface Builder_ для создания и обслуживания списков источников (или при необходимости создать их непосредственно в коде C#).

Список источника — это специальный тип представления структуры, используемый для отображения исходного действия, например на боковой панели в Finder или iTunes.

[![](source-list-images/source05.png "Пример списка источника")](source-list-images/source05.png#lightbox)

В этой статье мы обсудим, что узнаете основы работы со списками источников в приложении Xamarin.Mac. Настоятельно рекомендуется работать через [Привет, Mac](~/mac/get-started/hello-mac.md) статьи, во-первых, в частности [введение в Xcode и конструкторе Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) и [переменных экземпляров и действий](~/mac/get-started/hello-mac.md#outlets-and-actions) разделы, так как он рассматриваются основные понятия и методы, которые мы будем использовать в этой статье.

Может потребоваться взгляните на [предоставление C# классы / методы Objective-c](~/mac/internals/how-it-works.md) раздел [структуре Xamarin.Mac](~/mac/internals/how-it-works.md) документов, здесь объясняется `Register` и `Export` команды используется для передачи классов C# в службе Objective-C объекты и элементы пользовательского интерфейса.

<a name="Introduction_to_Outline_Views" />

## <a name="introduction-to-source-lists"></a>Общие сведения о списках источника

Как уже говорилось, список источников — это специальная структура используется для отображения исходного действия, например на боковой панели в Finder или iTunes. Список источника — это тип таблицы, который позволяет пользователю развернуть или свернуть строки иерархических данных. В отличие от представления таблицы не являются элементов исходного списка в виде плоского списка, они организованы в иерархии, как файлы и папки на жестком диске. Если элемент в списке источник содержит другие элементы, его можно разворачивать и сворачивать пользователем.

Список источников является представлением специально оформленного структуры (`NSOutlineView`), который сам является подклассом представления таблицы (`NSTableView`) и таким образом, наследует большую часть своего поведения от родительского класса. Таким образом многие операции, поддерживаемые представления структуры, также поддерживаются исходного списка. Xamarin.Mac управлять этими функциями и приложения можно настроить параметры исходного списка (либо в коде или конструктора Interface Builder), чтобы разрешать или запрещать определенные операции.

Список источников не сохраняет данные своими собственными, вместо этого она зависит от источника данных (`NSOutlineViewDataSource`) для предоставления строк и столбцов, необходимых, по мере необходимости.

Можно настроить поведение список источников, предоставляя подкласс делегата представление структуры (`NSOutlineViewDelegate`) для поддержки типа структуры для выбора функции, элемента выбора и редактирования, настраиваемое отслеживание и настраиваемые представления для отдельных элементов.

Так как список источников совместно использует большую часть такое поведение и функции с в табличное представление и структура, можно пройти через наши [представления таблиц](~/mac/user-interface/table-view.md) и [представления структуры](~/mac/user-interface/outline-view.md) документации, прежде чем продолжить в этой статье.

<a name="Working_with_Source_Lists" />

## <a name="working-with-source-lists"></a>Работа со списками источника

Список источника — это специальный тип представления структуры, используемый для отображения исходного действия, например на боковой панели в Finder или iTunes. В отличие от представления структуры прежде чем мы определим наш список источников в конструктор Interface Builder, давайте создадим резервного классы в Xamarin.Mac.

Во-первых давайте создадим новый `SourceListItem` класса для хранения данных исходного списка. В **обозревателе решений**, щелкните правой кнопкой мыши проект и выберите **добавить** > **новый файл...** Выберите **Общие** > **пустой класс**, введите `SourceListItem` для **имя** и нажмите кнопку **New** кнопки:

[![](source-list-images/source01.png "Добавление пустой класс")](source-list-images/source01.png#lightbox)

Сделать `SourceListItem.cs` внешний файл следующим образом: 

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using AppKit;
using Foundation;

namespace MacOutlines
{
    public class SourceListItem: NSObject, IEnumerator, IEnumerable
    {
        #region Private Properties
        private string _title;
        private NSImage _icon;
        private string _tag;
        private List<SourceListItem> _items = new List<SourceListItem> ();
        #endregion

        #region Computed Properties
        public string Title {
            get { return _title; }
            set { _title = value; }
        }

        public NSImage Icon {
            get { return _icon; }
            set { _icon = value; }
        }

        public string Tag {
            get { return _tag; }
            set { _tag=value; }
        }
        #endregion

        #region Indexer
        public SourceListItem this[int index]
        {
            get
            {
                return _items[index];
            }

            set
            {
                _items[index] = value;
            }
        }

        public int Count {
            get { return _items.Count; }
        }

        public bool HasChildren {
            get { return (Count > 0); }
        }
        #endregion

        #region Enumerable Routines
        private int _position = -1;

        public IEnumerator GetEnumerator()
        {
            _position = -1;
            return (IEnumerator)this;
        }

        public bool MoveNext()
        {
            _position++;
            return (_position < _items.Count);
        }

        public void Reset()
        {_position = -1;}

        public object Current
        {
            get 
            { 
                try 
                {
                    return _items[_position];
                }

                catch (IndexOutOfRangeException)
                {
                    throw new InvalidOperationException();
                }
            }
        }
        #endregion

        #region Constructors
        public SourceListItem ()
        {
        }

        public SourceListItem (string title)
        {
            // Initialize
            this._title = title;
        }

        public SourceListItem (string title, string icon)
        {
            // Initialize
            this._title = title;
            this._icon = NSImage.ImageNamed (icon);
        }

        public SourceListItem (string title, string icon, ClickedDelegate clicked)
        {
            // Initialize
            this._title = title;
            this._icon = NSImage.ImageNamed (icon);
            this.Clicked = clicked;
        }

        public SourceListItem (string title, NSImage icon)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
        }

        public SourceListItem (string title, NSImage icon, ClickedDelegate clicked)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
            this.Clicked = clicked;
        }

        public SourceListItem (string title, NSImage icon, string tag)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
            this._tag = tag;
        }

        public SourceListItem (string title, NSImage icon, string tag, ClickedDelegate clicked)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
            this._tag = tag;
            this.Clicked = clicked;
        }
        #endregion

        #region Public Methods
        public void AddItem(SourceListItem item) {
            _items.Add (item);
        }

        public void AddItem(string title) {
            _items.Add (new SourceListItem (title));
        }

        public void AddItem(string title, string icon) {
            _items.Add (new SourceListItem (title, icon));
        }

        public void AddItem(string title, string icon, ClickedDelegate clicked) {
            _items.Add (new SourceListItem (title, icon, clicked));
        }

        public void AddItem(string title, NSImage icon) {
            _items.Add (new SourceListItem (title, icon));
        }

        public void AddItem(string title, NSImage icon, ClickedDelegate clicked) {
            _items.Add (new SourceListItem (title, icon, clicked));
        }

        public void AddItem(string title, NSImage icon, string tag) {
            _items.Add (new SourceListItem (title, icon, tag));
        }

        public void AddItem(string title, NSImage icon, string tag, ClickedDelegate clicked) {
            _items.Add (new SourceListItem (title, icon, tag, clicked));
        }

        public void Insert(int n, SourceListItem item) {
            _items.Insert (n, item);
        }

        public void RemoveItem(SourceListItem item) {
            _items.Remove (item);
        }

        public void RemoveItem(int n) {
            _items.RemoveAt (n);
        }

        public void Clear() {
            _items.Clear ();
        }
        #endregion

        #region Events
        public delegate void ClickedDelegate();
        public event ClickedDelegate Clicked;

        internal void RaiseClickedEvent() {
            // Inform caller
            if (this.Clicked != null)
                this.Clicked ();
        }
        #endregion
    }
}
```

В **обозревателе решений**, щелкните правой кнопкой мыши проект и выберите **добавить** > **новый файл...** Выберите **Общие** > **пустой класс**, введите `SourceListDataSource` для **имя** и нажмите кнопку **New** кнопки. Сделать `SourceListDataSource.cs` внешний файл следующим образом:

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using AppKit;
using Foundation;

namespace MacOutlines
{
    public class SourceListDataSource : NSOutlineViewDataSource
    {
        #region Private Variables
        private SourceListView _controller;
        #endregion

        #region Public Variables
        public List<SourceListItem> Items = new List<SourceListItem>();
        #endregion

        #region Constructors
        public SourceListDataSource (SourceListView controller)
        {
            // Initialize
            this._controller = controller;
        }
        #endregion

        #region Override Properties
        public override nint GetChildrenCount (NSOutlineView outlineView, Foundation.NSObject item)
        {
            if (item == null) {
                return Items.Count;
            } else {
                return ((SourceListItem)item).Count;
            }
        }

        public override bool ItemExpandable (NSOutlineView outlineView, Foundation.NSObject item)
        {
            return ((SourceListItem)item).HasChildren;
        }

        public override NSObject GetChild (NSOutlineView outlineView, nint childIndex, Foundation.NSObject item)
        {
            if (item == null) {
                return Items [(int)childIndex];
            } else {
                return ((SourceListItem)item) [(int)childIndex]; 
            }
        }

        public override NSObject GetObjectValue (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item)
        {
            return new NSString (((SourceListItem)item).Title);
        }
        #endregion

        #region Internal Methods
        internal SourceListItem ItemForRow(int row) {
            int index = 0;

            // Look at each group
            foreach (SourceListItem item in Items) {
                // Is the row inside this group?
                if (row >= index && row <= (index + item.Count)) {
                    return item [row - index - 1];
                }

                // Move index
                index += item.Count + 1;
            }

            // Not found 
            return null;
        }
        #endregion
    }
}
```

Это позволит обеспечить данные для наших исходного списка.

В **обозревателе решений**, щелкните правой кнопкой мыши проект и выберите **добавить** > **новый файл...** Выберите **Общие** > **пустой класс**, введите `SourceListDelegate` для **имя** и нажмите кнопку **New** кнопки. Сделать `SourceListDelegate.cs` внешний файл следующим образом:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacOutlines
{
    public class SourceListDelegate : NSOutlineViewDelegate
    {
        #region Private variables
        private SourceListView _controller;
        #endregion

        #region Constructors
        public SourceListDelegate (SourceListView controller)
        {
            // Initialize
            this._controller = controller;
        }
        #endregion

        #region Override Methods
        public override bool ShouldEditTableColumn (NSOutlineView outlineView, NSTableColumn tableColumn, Foundation.NSObject item)
        {
            return false;
        }

        public override NSCell GetCell (NSOutlineView outlineView, NSTableColumn tableColumn, Foundation.NSObject item)
        {
            nint row = outlineView.RowForItem (item);
            return tableColumn.DataCellForRow (row);
        }

        public override bool IsGroupItem (NSOutlineView outlineView, Foundation.NSObject item)
        {
            return ((SourceListItem)item).HasChildren;
        }

        public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item)
        {
            NSTableCellView view = null;

            // Is this a group item?
            if (((SourceListItem)item).HasChildren) {
                view = (NSTableCellView)outlineView.MakeView ("HeaderCell", this);
            } else {
                view = (NSTableCellView)outlineView.MakeView ("DataCell", this);
                view.ImageView.Image = ((SourceListItem)item).Icon;
            }

            // Initialize view
            view.TextField.StringValue = ((SourceListItem)item).Title;

            // Return new view
            return view;
        }

        public override bool ShouldSelectItem (NSOutlineView outlineView, Foundation.NSObject item)
        {
            return (outlineView.GetParent (item) != null);
        }

        public override void SelectionDidChange (NSNotification notification)
        {
            NSIndexSet selectedIndexes = _controller.SelectedRows;

            // More than one item selected?
            if (selectedIndexes.Count > 1) {
                // Not handling this case
            } else {
                // Grab the item
                var item = _controller.Data.ItemForRow ((int)selectedIndexes.FirstIndex);

                // Was an item found?
                if (item != null) {
                    // Fire the clicked event for the item
                    item.RaiseClickedEvent ();

                    // Inform caller of selection
                    _controller.RaiseItemSelected (item);
                }
            }
        }
        #endregion
    }
}
```

Это позволит обеспечить поведение наш список источников.

Наконец, в **обозревателе решений**, щелкните правой кнопкой мыши проект и выберите **добавить** > **новый файл...** Выберите **Общие** > **пустой класс**, введите `SourceListView` для **имя** и нажмите кнопку **New** кнопки. Сделать `SourceListView.cs` внешний файл следующим образом:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacOutlines
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

Это создает подкласс настраиваемые, многократно используемые `NSOutlineView` (`SourceListView`), можно использовать для диска исходного списка в любом приложении Xamarin.Mac, корпорация Microsoft.

<a name="Creating_and_Maintaining_Source_Lists_in_Xcode" />

## <a name="creating-and-maintaining-source-lists-in-xcode"></a>Создание и обслуживание списки-источники в Xcode

Теперь давайте создадим наш список источников, в конструктор Interface Builder. Дважды щелкните `Main.storyboard` файл, чтобы открыть его для редактирования в конструкторе Interface Builder и перетащите разделенное представление из **инспектор библиотеки**, добавьте его на контроллер представления и настройте его для изменения размера с помощью представления в **Редактор ограничений** :

[![](source-list-images/source00.png "Изменение ограничения")](source-list-images/source00.png#lightbox)

Затем перетащите список из источника **инспектор библиотеки**, добавьте его в левую часть разделенного представления и настройте его для изменения размера с помощью представления в **Редактор ограничений**:

[![](source-list-images/source02.png "Изменение ограничения")](source-list-images/source02.png#lightbox)

Затем переключиться в режим **представления удостоверений**, выберите в списке источников и измените его на **класс** для `SourceListView`:

[![](source-list-images/source03.png "Задание имени класса")](source-list-images/source03.png#lightbox)

Наконец, создайте **розетки** для наших исходного списка, которая называется `SourceList` в `ViewController.h` файла:

[![](source-list-images/source04.png "Настройка выхода")](source-list-images/source04.png#lightbox)

Сохранить изменения и вернуться в Visual Studio для Mac синхронизировать с Xcode.

<a name="Populating the Source List" />

## <a name="populating-the-source-list"></a>Заполнение исходного списка

Изменим то `RotationWindow.cs` файл в Visual Studio для Mac и добавьте его в `AwakeFromNib` метод выглядят следующим:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Populate source list
    SourceList.Initialize ();

    var library = new SourceListItem ("Library");
    library.AddItem ("Venues", "house.png", () => {
        Console.WriteLine("Venue Selected");
    });
    library.AddItem ("Singers", "group.png");
    library.AddItem ("Genre", "cards.png");
    library.AddItem ("Publishers", "box.png");
    library.AddItem ("Artist", "person.png");
    library.AddItem ("Music", "album.png");
    SourceList.AddItem (library);

    // Add Rotation 
    var rotation = new SourceListItem ("Rotation"); 
    rotation.AddItem ("View Rotation", "redo.png");
    SourceList.AddItem (rotation);

    // Add Kiosks
    var kiosks = new SourceListItem ("Kiosks");
    kiosks.AddItem ("Sign-in Station 1", "imac");
    kiosks.AddItem ("Sign-in Station 2", "ipad");
    SourceList.AddItem (kiosks);

    // Display side list
    SourceList.ReloadData ();
    SourceList.ExpandItem (null, true);

}
```

`Initialize ()` Метод должен вызываться с нашей исходного списка **розетки** _перед_ все элементы, добавляются к нему. Для каждой группы элементов родительского элемента и затем добавить вложенные элементы элемента группы. Каждая группа добавляется в коллекцию исходного списка `SourceList.AddItem (...)`. Две последние строки загрузки данных для исходного списка и разворачивание всех групп:

```csharp
// Display side list
SourceList.ReloadData ();
SourceList.ExpandItem (null, true);
```

Наконец, измените `AppDelegate.cs` и создайте `DidFinishLaunching` внешний метод следующим образом:

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    mainWindowController = new MainWindowController ();
    mainWindowController.Window.MakeKeyAndOrderFront (this);

    var rotation = new RotationWindowController ();
    rotation.Window.MakeKeyAndOrderFront (this);
}
```

Если запустить наше приложение, ниже будет отображаться:

[![](source-list-images/source05.png "Запустите пример приложения")](source-list-images/source05.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>Сводка

В этой статье были предприняты никакие подробные принципы работы со списками источников в приложении Xamarin.Mac. Мы узнали, как создавать и обслуживать списков источников в Interface Builder в Xcode и способы работы со списками источников в коде C#.

## <a name="related-links"></a>Связанные ссылки

- [MacOutlines (пример)](https://developer.xamarin.com/samples/mac/MacOutlines/)
- [Привет, Mac](~/mac/get-started/hello-mac.md)
- [Представления таблиц](~/mac/user-interface/table-view.md)
- [Представления структур](~/mac/user-interface/outline-view.md)
- [Рекомендации по работе с человеческим интерфейсом OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Введение в режиме структуры](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/OutlineView/OutlineView.html#//apple_ref/doc/uid/10000023i)
- [NSOutlineView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSOutlineView_Class/index.html#//apple_ref/doc/uid/TP40004079)
- [NSOutlineViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSOutlineViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004175)
- [NSOutlineViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSOutlineViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008609)
