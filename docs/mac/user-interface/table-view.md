---
title: Представления таблиц в Xamarin.Mac
description: В этой статье рассматривается работа с представлениями таблиц в приложении Xamarin.Mac. Он описывает создание представлений таблиц в Xcode и конструкторе Interface Builder и работать с ними в коде.
ms.prod: xamarin
ms.assetid: 3B55B858-4769-4331-966A-7F53B3B7C720
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 0d89fff81d1d13baa578068bcaef11dd5af00e14
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/11/2018
ms.locfileid: "51527057"
---
# <a name="table-views-in-xamarinmac"></a>Представления таблиц в Xamarin.Mac

_В этой статье рассматривается работа с представлениями таблиц в приложении Xamarin.Mac. Он описывает создание представлений таблиц в Xcode и конструкторе Interface Builder и работать с ними в коде._

При работе с C# и .NET в приложении Xamarin.Mac, у вас есть доступ к той же таблицы представления, работающий в *Objective-C* и *Xcode* does. Поскольку Xamarin.Mac напрямую интегрируется с Xcode, можно использовать конструктор _Interface Builder_ для создания и поддержания представлениях таблицы (или при необходимости создать их непосредственно в коде C#).

В табличное представление отображает данные в табличном формате, содержащий один или несколько столбцов данных в нескольких строках. Зависимости от типа создаваемого представления таблицы, пользователь может сортировать по столбцу, реорганизовать столбцы, добавить столбцы, удалить столбцы или изменить данные, содержащиеся в таблице.

[![](table-view-images/intro01.png "Пример таблицы")](table-view-images/intro01.png#lightbox)

В этой статье мы обсудим, что узнаете основы работы с представлениями таблиц в приложении Xamarin.Mac. Настоятельно рекомендуется работать через [Привет, Mac](~/mac/get-started/hello-mac.md) статьи, во-первых, в частности [введение в Xcode и конструкторе Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) и [переменных экземпляров и действий](~/mac/get-started/hello-mac.md#outlets-and-actions) разделы, так как он рассматриваются основные понятия и методы, которые мы будем использовать в этой статье.

Может потребоваться взгляните на [предоставление C# классы / методы Objective-c](~/mac/internals/how-it-works.md) раздел [структуре Xamarin.Mac](~/mac/internals/how-it-works.md) документов, здесь объясняется `Register` и `Export` команды используется для передачи классов C# в службе Objective-C объекты и элементы пользовательского интерфейса.

<a name="Introduction_to_Table_Views" />

## <a name="introduction-to-table-views"></a>Общие сведения о представлениях таблиц

В табличное представление отображает данные в табличном формате, содержащий один или несколько столбцов данных в нескольких строках. Представления таблиц отображаются внутри представления с прокруткой (`NSScrollView`) начиная с macOS 10.7, вы может использовать любой `NSView` вместо ячеек (`NSCell`) для отображения строк и столбцов. С другой стороны, вы можете продолжать использовать `NSCell` тем не менее, вы будете обычно подкласс `NSTableCellView` и создание настраиваемых строк и столбцов.

Представление таблицы не сохраняет данные своими собственными, вместо этого она зависит от источника данных (`NSTableViewDataSource`) для предоставления строк и столбцов, необходимых, по мере необходимости.

Можно настроить поведение в табличное представление, предоставляя подкласс делегата представления таблицы (`NSTableViewDelegate`) для поддержки управления столбца таблицы, введите для выбора функции, выбор строк и редактирования, настраиваемое отслеживание и настраиваемые представления для отдельных столбцов и строки.

При создании представления таблиц, Apple рекомендует придерживаться указанных ниже:

* Разрешить пользователям выполнять сортировку в таблице, щелкнув заголовки столбцов.
* Создайте заголовки столбцов, которые существительные или короткие субстантивные словосочетания, которые описывают данные, отображаемые в этом столбце.

Дополнительные сведения см. в разделе [содержимого представления](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsView.html#//apple_ref/doc/uid/20000957-CH52-SW1) раздел Apple [рекомендации по человеческим интерфейсом OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/).

<a name="Creating-and-Maintaining-Table-Views-in-Xcode" />

## <a name="creating-and-maintaining-table-views-in-xcode"></a>Создании и обслуживании представлений таблиц в Xcode

При создании нового приложения Cocoa для Xamarin.Mac, вы получаете стандартное окно пустым, по умолчанию. В данном окне определяется в `.storyboard` файл, автоматически включаются в проект. Редактирование своего проекта windows, в **обозревателе решений**, дважды щелкните `Main.storyboard` файла:

[![](table-view-images/edit01.png "Выбрав основной раскадровки")](table-view-images/edit01.png#lightbox)

Он откроется окно конструктора Interface Builder в Xcode:

[![](table-view-images/edit02.png "Изменение пользовательского интерфейса в Xcode")](table-view-images/edit02.png#lightbox)

Тип `table` в **инспектор библиотеки** поле поиска, чтобы было проще найти элементы управления представления таблицы:

[![](table-view-images/edit03.png "Выбрав представление таблицы из библиотеки")](table-view-images/edit03.png#lightbox)

Перетащите в табличное представление контроллера представления в **редактор интерфейса**, сделайте его заполнения области содержимого контроллера представления и задайте для него значение, где он сжимает и растет вместе с окна в **Редактор ограничений**:

[![](table-view-images/edit04.png "Изменение ограничения")](table-view-images/edit04.png#lightbox)

Выберите режим таблицы **иерархия интерфейса** и следующие свойства доступны в **инспекторе атрибутов**:

[![](table-view-images/edit05.png "Инспектор атрибутов")](table-view-images/edit05.png#lightbox)

- **Содержимое режима** -можно использовать либо представления (`NSView`) или ячеек (`NSCell`) для отображения данных в строках и столбцах. Начиная с macOS 10.7, следует использовать представления.
- **Смещает группирование строк** — Если `true`, представления таблицы рисующий сгруппированных ячеек, как если бы они представляют собой перемещаемые.
- **Столбцы** -определяет количество отображаемых столбцов.
- **Заголовки** — Если `true`, столбцы будут иметь заголовки.
- **Изменение порядка** — Если `true`, пользователь сможет перетаскивать изменение порядка столбцов в таблице.
- **Изменение размера** — Если `true`, пользователь сможет перетаскивать заголовки столбцов, чтобы изменять размер столбцов.
- **Установка размеров столбцов** -элементы управления, как автоматическое размер столбцов таблицы.
- **Выделите** -элементы управления, используется тип выделения в таблице, при выборе ячейки.
- **Альтернативные строки** — Если `true`, когда-либо других строка будет иметь другой цвет фона.
- **Горизонтальная сетка** -выбирает тип границы, нарисованной по горизонтали между ячейками.
- **Вертикальную сетку** -выбирает тип границы, нарисованной по вертикали между ячейками.
- **Цвет сетки** -задает цвет границы ячейки.
- **Фон** -задает цвет фона ячейки.
- **Выбор** -позволяют контролировать, как пользователь может выбрать ячейки в таблице, что:
    - **Несколько** — Если `true`, пользователь может выбрать несколько строк и столбцов.
    - **Столбец** — Если `true`, пользователь может выбрать столбцы.
    - **Введите Select** — Если `true`, пользователь может вводить символ, чтобы выбрать строку.
    - **Пустой** — Если `true`, пользователю не обязательно выберите строки или столбца, таблицы позволяет не выбирать вообще.
- **Эта функция** -имя, которое имеет формат таблицы автоматически сохранить в группе.
- **Сведения о столбце** — Если `true`, порядок и ширину столбцов будут сохранены автоматически.
- **Символы обозначения конца строки** — выберите, как ячейки обработки разрывов строк.
- **Усекает последней видимой строкой** — Если `true`, ячейки будут усечены, данные могут не выходить за его границы.

> [!IMPORTANT]
> Если необходимо хранить устаревшего приложения Xamarin.Mac, `NSView` на основе представления таблицы, которые должны использоваться через `NSCell` на основе представления таблиц. `NSCell` считается прежних версий и могут не поддерживаться в будущем.

Выберите столбец в таблице **иерархия интерфейса** и следующие свойства доступны в **инспекторе атрибутов**:

[![](table-view-images/edit06.png "Инспектор атрибутов")](table-view-images/edit06.png#lightbox)

- **Заголовок** -задает заголовок столбца.
- **Выравнивание** -Установка выравнивания текста в ячейках.
- **Название шрифта** -выбирает шрифт для текста в ячейке заголовка.
- **Ключ сортировки** -— ключ, используемый для сортировки данных в столбце. Оставьте пустым, если пользователь не может сортировать этот столбец.
- **Селектор** -является **действие** используется для выполнения сортировки. Оставьте пустым, если пользователь не может сортировать этот столбец.
- **Порядок** -заключается в порядке сортировки для столбцов данных.
- **Изменение размера** -выбирает тип изменения размера для столбца.
- **Редактируемый** — Если `true`, пользователь может изменять ячейки в таблице ячейки в зависимости.
- **Скрытые** — Если `true`, столбец будет скрыт.

Можно также изменить размер столбца, перетаскивая маркер (по вертикали по центру правой стороны столбца) слева или справа его.

Давайте выберите каждый столбец в представлении таблицы и предоставить первый столбец **Title** из `Product` и второе `Details`.

Выберите представление ячейки таблицы (`NSTableViewCell`) в **иерархия интерфейса** и следующие свойства доступны в **инспекторе атрибутов**:

[![](table-view-images/edit07.png "Инспектор атрибутов")](table-view-images/edit07.png#lightbox)

Ниже приведены все свойства стандартное представление. Имеется также возможность изменения размера строк для этого столбца здесь.

Выберите ячейки представления таблиц (по умолчанию, это `NSTextField`) в **иерархия интерфейса** и следующие свойства доступны в **инспекторе атрибутов**:

[![](table-view-images/edit08.png "Инспектор атрибутов")](table-view-images/edit08.png#lightbox)

У вас будет все свойства стандартного текстового поля, чтобы заданное здесь. По умолчанию стандартный текстовое поле используется для отображения данных для ячейки в столбце.

Выберите представление ячейки таблицы (`NSTableFieldCell`) в **иерархия интерфейса** и следующие свойства доступны в **инспекторе атрибутов**:

[![](table-view-images/edit09.png "Инспектор атрибутов")](table-view-images/edit09.png#lightbox)

Ниже перечислены наиболее важные параметры здесь.

- **Макет** — выберите, каким образом размещаются в ячейках этого столбца.
- **Использует режим единого строки** — Если `true`, ячейки ограничена одной строки.
- **Первый ширины макет среды выполнения** — Если `true`, ячейки предпочтут ширины, установите для него (вручную или автоматически) при его отображается при первом запуске приложения.
- **Действие** -контролирует, когда изменение **действие** отправляется для ячейки.
- **Поведение** -определяет, если ячейка находится и редактировать.
- **Форматированный текст** — Если `true`, ячейка может отображать текст, отформатированный, примененным стилем.
- **Отменить** — Если `true`, ячейки несет ответственность, для его — отменить поведение.

Выберите представление ячейки таблицы (`NSTableFieldCell`) в нижней части столбца таблицы в **иерархия интерфейса**:

[![](table-view-images/edit10.png "Выбор представления ячейки таблицы")](table-view-images/edit10.png#lightbox)

Это дает возможность изменить представление ячейки таблицы, используемый как базовый _шаблон_ для всех ячеек, созданных для данного столбца.

<a name="Adding_Actions_and_Outlets" />

### <a name="adding-actions-and-outlets"></a>Добавление действия и источники

Так же как и любые другие Cocoa элемента управления пользовательского интерфейса, нам нужно предоставить нашего представления таблицы и его столбцы и ячейки в C# кода с помощью **действия** и **розеток** (с учетом функциональность, необходимую).

Процесс одинаков для любого элемента таблицы представления, необходимо предоставить:

1. Переключиться в режим **вспомогательном редакторе** и убедитесь, что `ViewController.h` выбран файл: 

    [![](table-view-images/edit11.png "Вспомогательного редактора")](table-view-images/edit11.png#lightbox)
2. Выберите вид таблицы из **иерархия интерфейса**, щелкните и перетащите `ViewController.h` файл.
3. Создание **розетки** для представления таблицы, которая называется `ProductTable`: 

    [![](table-view-images/edit13.png "Настройка выхода")](table-view-images/edit13.png#lightbox)
4. Создание **розеток** для столбцов таблицы также называются `ProductColumn` и `DetailsColumn`: 

    [![](table-view-images/edit14.png "Настройка выхода")](table-view-images/edit14.png#lightbox)
5. Сохранить изменения и вернуться в Visual Studio для Mac синхронизировать с Xcode.

Далее мы напишем код отображения некоторых данных для таблицы при запуске приложения.

<a name="Populating_the_Table_View" />

## <a name="populating-the-table-view"></a>Заполнение таблицы представления

С помощью нашего представления таблицы, созданной в конструктор Interface Builder и предоставляются через **розетки**, Далее нам нужно создать код C# для заполнения.

Во-первых давайте создадим новый `Product` класса, содержащего данные для отдельных строк. В **обозревателе решений**, щелкните правой кнопкой мыши проект и выберите **добавить** > **новый файл...** Выберите **Общие** > **пустой класс**, введите `Product` для **имя** и нажмите кнопку **New** кнопки:

[![](table-view-images/populate01.png "Создание пустой класс")](table-view-images/populate01.png#lightbox)

Сделать `Product.cs` внешний файл следующим образом:

```csharp
using System;

namespace MacTables
{
    public class Product
    {
        #region Computed Propoperties
        public string Title { get; set;} = "";
        public string Description { get; set;} = "";
        #endregion

        #region Constructors
        public Product ()
        {
        }

        public Product (string title, string description)
        {
            this.Title = title;
            this.Description = description;
        }
        #endregion
    }
}

```

Далее нам необходимо создать подкласс `NSTableDataSource` для предоставления данных для нашей таблице по запросу. В **обозревателе решений**, щелкните правой кнопкой мыши проект и выберите **добавить** > **новый файл...** Выберите **Общие** > **пустой класс**, введите `ProductTableDataSource` для **имя** и нажмите кнопку **New** кнопки.

Изменить `ProductTableDataSource.cs` файл и это должно выглядеть следующим образом:

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacTables
{
    public class ProductTableDataSource : NSTableViewDataSource
    {
        #region Public Variables
        public List<Product> Products = new List<Product>();
        #endregion

        #region Constructors
        public ProductTableDataSource ()
        {
        }
        #endregion

        #region Override Methods
        public override nint GetRowCount (NSTableView tableView)
        {
            return Products.Count;
        }
        #endregion
    }
}

```

Этот класс имеет хранилище для нашей таблицы Просмотр элементов и переопределяет `GetRowCount` для возврата количества строк в таблице.

Наконец, нам нужно создать подкласс `NSTableDelegate` для обеспечения поведения для нашей таблице. В **обозревателе решений**, щелкните правой кнопкой мыши проект и выберите **добавить** > **новый файл...** Выберите **Общие** > **пустой класс**, введите `ProductTableDelegate` для **имя** и нажмите кнопку **New** кнопки.

Изменить `ProductTableDelegate.cs` файл и это должно выглядеть следующим образом:

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacTables
{
    public class ProductTableDelegate: NSTableViewDelegate
    {
        #region Constants 
        private const string CellIdentifier = "ProdCell";
        #endregion

        #region Private Variables
        private ProductTableDataSource DataSource;
        #endregion

        #region Constructors
        public ProductTableDelegate (ProductTableDataSource datasource)
        {
            this.DataSource = datasource;
        }
        #endregion

        #region Override Methods
        public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
        {
            // This pattern allows you reuse existing views when they are no-longer in use.
            // If the returned view is null, you instance up a new view
            // If a non-null view is returned, you modify it enough to reflect the new data
            NSTextField view = (NSTextField)tableView.MakeView (CellIdentifier, this);
            if (view == null) {
                view = new NSTextField ();
                view.Identifier = CellIdentifier;
                view.BackgroundColor = NSColor.Clear;
                view.Bordered = false;
                view.Selectable = false;
                view.Editable = false;
            }

            // Setup view based on the column selected
            switch (tableColumn.Title) {
            case "Product":
                view.StringValue = DataSource.Products [(int)row].Title;
                break;
            case "Details":
                view.StringValue = DataSource.Products [(int)row].Description;
                break;
            }

            return view;
        }
        #endregion
    }
}
```

Когда мы создаем экземпляр объекта `ProductTableDelegate`, мы передаем в экземпляре `ProductTableDataSource` , предоставляющий данные для таблицы. `GetViewForItem` Метод отвечает за возврат представление (данные), чтобы отобразить ячейку для столбца и строки. Если это возможно существующее представление будет использоваться повторно для отображения этой ячейки, в противном случае необходимо создать новое представление.

Для заполнения таблицы, изменим `ViewController.cs` и создайте `AwakeFromNib` внешний метод следующим образом:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Create the Product Table Data Source and populate it
    var DataSource = new ProductTableDataSource ();
    DataSource.Products.Add (new Product ("Xamarin.iOS", "Allows you to develop native iOS Applications in C#"));
    DataSource.Products.Add (new Product ("Xamarin.Android", "Allows you to develop native Android Applications in C#"));
    DataSource.Products.Add (new Product ("Xamarin.Mac", "Allows you to develop Mac native Applications in C#"));

    // Populate the Product Table
    ProductTable.DataSource = DataSource;
    ProductTable.Delegate = new ProductTableDelegate (DataSource);
}
```

Если запустить приложение, отображается следующая информация:

[![](table-view-images/populate02.png "Запустите пример приложения")](table-view-images/populate02.png#lightbox)

<a name="Sorting_by_Column" />

## <a name="sorting-by-column"></a>Сортировка по столбцу

Разрешите пользователям выполнять сортировку данных в таблице, щелкнув заголовок столбца. Во-первых, дважды щелкните `Main.storyboard` файл, чтобы открыть его для редактирования в конструкторе Interface Builder. Выберите `Product` столбца, введите `Title` для **ключ сортировки**, `compare:` для **селектор** и выберите `Ascending` для **порядок**:

[![](table-view-images/sort01.png "Установка ключа сортировки")](table-view-images/sort01.png#lightbox)

Выберите `Details` столбца, введите `Description` для **ключ сортировки**, `compare:` для **селектор** и выберите `Ascending` для **порядок**:

[![](table-view-images/sort02.png "Установка ключа сортировки")](table-view-images/sort02.png#lightbox)

Сохранить изменения и вернуться в Visual Studio для Mac синхронизировать с Xcode.

Теперь изменим `ProductTableDataSource.cs` файл и добавьте следующие методы:

```csharp
public void Sort(string key, bool ascending) {

    // Take action based on key
    switch (key) {
    case "Title":
        if (ascending) {
            Products.Sort ((x, y) => x.Title.CompareTo (y.Title));
        } else {
            Products.Sort ((x, y) => -1 * x.Title.CompareTo (y.Title));
        }
        break;
    case "Description":
        if (ascending) {
            Products.Sort ((x, y) => x.Description.CompareTo (y.Description));
        } else {
            Products.Sort ((x, y) => -1 * x.Description.CompareTo (y.Description));
        }
        break;
    }

}

public override void SortDescriptorsChanged (NSTableView tableView, NSSortDescriptor[] oldDescriptors)
{
    // Sort the data
    if (oldDescriptors.Length > 0) {
        // Update sort
        Sort (oldDescriptors [0].Key, oldDescriptors [0].Ascending);
    } else {
        // Grab current descriptors and update sort
        NSSortDescriptor[] tbSort = tableView.SortDescriptors; 
        Sort (tbSort[0].Key, tbSort[0].Ascending); 
    }
            
    // Refresh table
    tableView.ReloadData ();
}
```

`Sort` Метода позволяют сортировать данные в источнике данных, на основе заданного `Product` поля класса в возрастающем или убывающем порядке. Переопределенный `SortDescriptorsChanged` метод будет вызываться каждый раз, использование щелкает заголовок столбца. Он будет передан **ключ** значение, которое задается в конструкторе Interface Builder и порядок сортировки для этого столбца.

Если запустить приложение и нажмите кнопку в заголовках столбцов, строк будут отсортированы по этому столбцу:

[![](table-view-images/sort03.png "Запустите пример приложения")](table-view-images/sort03.png#lightbox)

<a name="Row_Selection" />

## <a name="row-selection"></a>Выбор строк

Если вы хотите разрешить пользователю выбрать одну строку, дважды щелкните `Main.storyboard` файл, чтобы открыть его для редактирования в конструкторе Interface Builder. Выберите режим таблицы **иерархия интерфейса** и снимите флажок **несколько** флажок в **инспекторе атрибутов**:

[![](table-view-images/select01.png "Инспектор атрибутов")](table-view-images/select01.png#lightbox)

Сохранить изменения и вернуться в Visual Studio для Mac синхронизировать с Xcode.


Далее следует изменить `ProductTableDelegate.cs` файл и добавьте следующий метод:

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
    return true;
}
```

Это позволит пользователю выбрать любой отдельной строки в табличном представлении. Вернуть `false` для `ShouldSelectRow` для любой строке, вы не хотите, чтобы пользователь мог выбрать или `false` для каждой строки, если вы не хотите пользователя, чтобы иметь возможность выбрать любой строки.

Представление таблицы (`NSTableView`) содержит следующие методы для работы с Выбор строки:

- `DeselectRow(nint)` -Отменяет выделение заданной строки в таблице.
- `SelectRow(nint,bool)` -Выбор данной строки. Передайте `false` для второго параметра выбрать только одну строку за раз.
- `SelectedRow` — Возвращает текущую строку в таблице.
- `IsRowSelected(nint)` — Возвращает `true` при выборе данной строки.

<a name="Multiple_Row_Selection" />

## <a name="multiple-row-selection"></a>Выбор нескольких строк

Если вы хотите разрешить пользователю выбрать несколько строк, дважды щелкните `Main.storyboard` файл, чтобы открыть его для редактирования в конструкторе Interface Builder. Выберите режим таблицы **иерархия интерфейса** и проверьте **несколько** флажок в **инспекторе атрибутов**:

[![](table-view-images/select02.png "Инспектор атрибутов")](table-view-images/select02.png#lightbox)

Сохранить изменения и вернуться в Visual Studio для Mac синхронизировать с Xcode.


Далее следует изменить `ProductTableDelegate.cs` файл и добавьте следующий метод:

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
    return true;
}
```

Это позволит пользователю выбрать любой отдельной строки в табличном представлении. Вернуть `false` для `ShouldSelectRow` для любой строке, вы не хотите, чтобы пользователь мог выбрать или `false` для каждой строки, если вы не хотите пользователя, чтобы иметь возможность выбрать любой строки.

Представление таблицы (`NSTableView`) содержит следующие методы для работы с Выбор строки:

- `DeselectAll(NSObject)` -Отменяет выбор всех строк в таблице. Используйте `this` для первого параметра для отправки в объект, выполняющий выбора. 
- `DeselectRow(nint)` -Отменяет выделение заданной строки в таблице.
- `SelectAll(NSobject)` -Выбирает все строки в таблице. Используйте `this` для первого параметра для отправки в объект, выполняющий выбора.
- `SelectRow(nint,bool)` -Выбор данной строки. Передайте `false` для второго параметра снимите флажок и выберите только одну строку, передайте `true` расширит область выделения и включить эту строку.
- `SelectRows(NSIndexSet,bool)` -Выбор данного набора строк. Передайте `false` для второго параметра снимите флажок и выберите только эти строки, передайте `true` расширит область выделения и включить эти строки.
- `SelectedRow` — Возвращает текущую строку в таблице.
- `SelectedRows` — Возвращает `NSIndexSet` содержащую индексы выбранных строк.
- `SelectedRowCount` — Возвращает число выбранных строк.
- `IsRowSelected(nint)` — Возвращает `true` при выборе данной строки.

<a name="Type_to_Select_Row" />

## <a name="type-to-select-row"></a>Тип, чтобы выбрать строку

Если вы хотите разрешить пользователю вводить символ с выбрано представление таблицы и выберите первую строку, содержащий этот символ, дважды щелкните `Main.storyboard` файл, чтобы открыть его для редактирования в конструкторе Interface Builder. Выберите режим таблицы **иерархия интерфейса** и проверьте **выберите тип** флажок в **инспекторе атрибутов**:

[![](table-view-images/type01.png "Тип выбора параметра")](table-view-images/type01.png#lightbox)

Сохранить изменения и вернуться в Visual Studio для Mac синхронизировать с Xcode.

Теперь изменим `ProductTableDelegate.cs` файл и добавьте следующий метод:

```csharp
public override nint GetNextTypeSelectMatch (NSTableView tableView, nint startRow, nint endRow, string searchString)
{
    nint row = 0;
    foreach(Product product in DataSource.Products) {
        if (product.Title.Contains(searchString)) return row;

        // Increment row counter
        ++row;
    }

    // If not found select the first row
    return 0;
}
```

`GetNextTypeSelectMatch` Альбома заданного `searchString` и возвращает строку из первого `Product` с этой строки в его `Title`.

Если запустить приложение и введите символ, выбрана строка:

[![](table-view-images/type02.png "Запустите пример приложения")](table-view-images/type02.png#lightbox)

<a name="Reordering_Columns" />

## <a name="reordering-columns"></a>Изменение порядка столбцов

Если вы хотите разрешить пользователю перетаскивать изменение порядка столбцов в табличном представлении, дважды щелкните `Main.storyboard` файл, чтобы открыть его для редактирования в конструкторе Interface Builder. Выберите режим таблицы **иерархия интерфейса** и проверьте **переупорядочивание** флажок в **инспекторе атрибутов**:

[![](table-view-images/reorder01.png "Инспектор атрибутов")](table-view-images/reorder01.png#lightbox)

Если мы предоставляем значение **автосохранения** свойство и проверьте **сведения о столбце** поле, любые изменения, вносимые для макета таблицы будут автоматически сохранены для нас и восстановления следующего приложения выполняется.

Сохранить изменения и вернуться в Visual Studio для Mac синхронизировать с Xcode.

Теперь изменим `ProductTableDelegate.cs` файл и добавьте следующий метод:

```csharp
public override bool ShouldReorder (NSTableView tableView, nint columnIndex, nint newColumnIndex)
{
    return true;
}
```

`ShouldReorder` Метод должен возвращать `true` любого столбца, он должен иметь возможность быть перетащите переупорядочить в `newColumnIndex`, в противном случае возвращаемое `false`;

Если запустить приложение, можно перетащить заголовки столбцов вокруг переупорядочить столбцы:

[![](table-view-images/reorder02.png "Пример переупорядоченные столбцы")](table-view-images/reorder02.png#lightbox)

<a name="Editing_Cells" />

## <a name="editing-cells"></a>Редактирование ячеек

Если вы хотите разрешить пользователю изменять значения для данной ячейки, изменить `ProductTableDelegate.cs` файлов и изменить `GetViewForItem` метод следующим образом:

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{
    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTextField view = (NSTextField)tableView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTextField ();
        view.Identifier = tableColumn.Title;
        view.BackgroundColor = NSColor.Clear;
        view.Bordered = false;
        view.Selectable = false;
        view.Editable = true;

        view.EditingEnded += (sender, e) => {
                    
            // Take action based on type
            switch(view.Identifier) {
            case "Product":
                DataSource.Products [(int)view.Tag].Title = view.StringValue;
                break;
            case "Details":
                DataSource.Products [(int)view.Tag].Description = view.StringValue;
                break; 
            }
        };
    }

    // Tag view
    view.Tag = row;

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.StringValue = DataSource.Products [(int)row].Title;
        break;
    case "Details":
        view.StringValue = DataSource.Products [(int)row].Description;
        break;
    }

    return view;
}
```

Теперь Если запустить приложение, пользователь может изменять ячейки в табличном представлении:

[![](table-view-images/editing01.png "Пример изменения ячейки")](table-view-images/editing01.png#lightbox)

<a name="Using_Images_in_Table_Views" />

## <a name="using-images-in-table-views"></a>Использование изображений в представления таблиц

Для включения изображения как часть ячейки в `NSTableView`, вам потребуется изменить как данных, возвращаемых методом в представлении таблицы `NSTableViewDelegate's` `GetViewForItem` метод, используемый `NSTableCellView` вместо обычного `NSTextField`. Пример:

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)tableView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTableCellView ();
        if (tableColumn.Title == "Product") {
            view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
            view.AddSubview (view.ImageView);
            view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
        } else {
            view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
        }
        view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
        view.AddSubview (view.TextField);
        view.Identifier = tableColumn.Title;
        view.TextField.BackgroundColor = NSColor.Clear;
        view.TextField.Bordered = false;
        view.TextField.Selectable = false;
        view.TextField.Editable = true;

        view.TextField.EditingEnded += (sender, e) => {

            // Take action based on type
            switch(view.Identifier) {
            case "Product":
                DataSource.Products [(int)view.TextField.Tag].Title = view.TextField.StringValue;
                break;
            case "Details":
                DataSource.Products [(int)view.TextField.Tag].Description = view.TextField.StringValue;
                break; 
            }
        };
    }

    // Tag view
    view.TextField.Tag = row;

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed ("tags.png");
        view.TextField.StringValue = DataSource.Products [(int)row].Title;
        break;
    case "Details":
        view.TextField.StringValue = DataSource.Products [(int)row].Description;
        break;
    }

    return view;
}
```

Дополнительные сведения см. в разделе [с помощью образов с помощью представления таблиц](~/mac/app-fundamentals/image.md) части нашей [работе с изображением](~/mac/app-fundamentals/image.md) документации.

<a name="Adding-a-Delete-Button-to-a-Row" />

## <a name="adding-a-delete-button-to-a-row"></a>Добавление кнопки "Удалить" в строку

Зависимости от требований вашего приложения, возможны случаи где необходимо предоставить кнопка действия для каждой строки в таблице. Как пример, давайте Развернем пример таблицы представления, созданную выше, чтобы включить **удалить** кнопки в каждой строке.

Во-первых, измените `Main.storyboard` в конструктора Interface Builder, выберите вид таблицы и увеличить число столбцов для трех (3). Затем измените **Title** нового столбца к `Action`:

[![](table-view-images/delete01.png "Изменение имени столбца")](table-view-images/delete01.png#lightbox)

Сохранить изменения в раскадровку и вернитесь в Visual Studio для Mac синхронизировать изменения.

Далее следует изменить `ViewController.cs` файл и добавьте приведенный ниже открытый метод:

```csharp
public void ReloadTable ()
{
    ProductTable.ReloadData ();
}
```

В этом же файле изменения создания нового делегата представление таблицы внутри класса `ViewDidLoad` метод следующим образом:

```csharp
// Populate the Product Table
ProductTable.DataSource = DataSource;
ProductTable.Delegate = new ProductTableDelegate (this, DataSource);
```

Теперь измените `ProductTableDelegate.cs` файл для включения частного подключения к контроллер представления и для перевод контроллера в качестве параметра при создании нового экземпляра делегата:

```csharp
#region Private Variables
private ProductTableDataSource DataSource;
private ViewController Controller;
#endregion

#region Constructors
public ProductTableDelegate (ViewController controller, ProductTableDataSource datasource)
{
    this.Controller = controller;
    this.DataSource = datasource;
}
#endregion
```

Добавьте новый закрытый метод в класс:

```csharp
private void ConfigureTextField (NSTableCellView view, nint row)
{
    // Add to view
    view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
    view.AddSubview (view.TextField);

    // Configure
    view.TextField.BackgroundColor = NSColor.Clear;
    view.TextField.Bordered = false;
    view.TextField.Selectable = false;
    view.TextField.Editable = true;

    // Wireup events
    view.TextField.EditingEnded += (sender, e) => {

        // Take action based on type
        switch (view.Identifier) {
        case "Product":
            DataSource.Products [(int)view.TextField.Tag].Title = view.TextField.StringValue;
            break;
        case "Details":
            DataSource.Products [(int)view.TextField.Tag].Description = view.TextField.StringValue;
            break;
        }
    };

    // Tag view
    view.TextField.Tag = row;
}
```

Это занимает все представление текста конфигурации, которые были ранее, выполняемых в `GetViewForItem` метод и помещает их в одном месте вызываемой (так как последний столбец таблицы не содержит текстового представления, но кнопка).

Наконец, измените `GetViewForItem` метода и это должно выглядеть следующим образом:

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)tableView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTableCellView ();

        // Configure the view
        view.Identifier = tableColumn.Title;

        // Take action based on title
        switch (tableColumn.Title) {
        case "Product":
            view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
            view.AddSubview (view.ImageView);
            view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
            ConfigureTextField (view, row);
            break;
        case "Details":
            view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
            ConfigureTextField (view, row);
            break;
        case "Action":
            // Create new button
            var button = new NSButton (new CGRect (0, 0, 81, 16));
            button.SetButtonType (NSButtonType.MomentaryPushIn);
            button.Title = "Delete";
            button.Tag = row;

            // Wireup events
            button.Activated += (sender, e) => {
                // Get button and product
                var btn = sender as NSButton;
                var product = DataSource.Products [(int)btn.Tag];

                // Configure alert
                var alert = new NSAlert () {
                    AlertStyle = NSAlertStyle.Informational,
                    InformativeText = $"Are you sure you want to delete {product.Title}? This operation cannot be undone.",
                    MessageText = $"Delete {product.Title}?",
                };
                alert.AddButton ("Cancel");
                alert.AddButton ("Delete");
                alert.BeginSheetForResponse (Controller.View.Window, (result) => {
                    // Should we delete the requested row?
                    if (result == 1001) {
                        // Remove the given row from the dataset
                        DataSource.Products.RemoveAt((int)btn.Tag);
                        Controller.ReloadTable ();
                    }
                });
            };

            // Add to view
            view.AddSubview (button);
            break;
        }

    }

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed ("tag.png");
        view.TextField.StringValue = DataSource.Products [(int)row].Title;
        view.TextField.Tag = row;
        break;
    case "Details":
        view.TextField.StringValue = DataSource.Products [(int)row].Description;
        view.TextField.Tag = row;
        break;
    case "Action":
        foreach (NSView subview in view.Subviews) {
            var btn = subview as NSButton;
            if (btn != null) {
                btn.Tag = row;
            }
        }
        break;
    }

    return view;
}
```

Давайте взглянем на несколько разделов этого кода, более подробно. Во-первых, если новый `NSTableViewCell` является создаваемого действия берется на основе на имени столбца. Для первых двух столбцов (**продукта** и **сведения**), новый `ConfigureTextField` вызывается метод.

Для **действие** столбец, новый `NSButton` создается и добавляется в ячейку как Sub представление:

```csharp
// Create new button
var button = new NSButton (new CGRect (0, 0, 81, 16));
button.SetButtonType (NSButtonType.MomentaryPushIn);
button.Title = "Delete";
button.Tag = row;
...

// Add to view
view.AddSubview (button);
```

Кнопки `Tag` свойство используется для хранения количества строк, который сейчас обрабатывается. Это число будет использоваться позже при запросе строки будет удален в кнопки `Activated` событий:

```csharp
// Wireup events
button.Activated += (sender, e) => {
    // Get button and product
    var btn = sender as NSButton;
    var product = DataSource.Products [(int)btn.Tag];

    // Configure alert
    var alert = new NSAlert () {
        AlertStyle = NSAlertStyle.Informational,
        InformativeText = $"Are you sure you want to delete {product.Title}? This operation cannot be undone.",
        MessageText = $"Delete {product.Title}?",
    };
    alert.AddButton ("Cancel");
    alert.AddButton ("Delete");
    alert.BeginSheetForResponse (Controller.View.Window, (result) => {
        // Should we delete the requested row?
        if (result == 1001) {
            // Remove the given row from the dataset
            DataSource.Products.RemoveAt((int)btn.Tag);
            Controller.ReloadTable ();
        }
    });
};
```

В начале обработчика событий мы получаем, кнопки и продукт, который находится в строке данной таблицы. Затем пользователь подтверждения удаления строки представляется оповещение. Если пользователь решит удалить строку, данная строка удаляется из источника данных, и загружается таблицы:

```csharp
// Remove the given row from the dataset
DataSource.Products.RemoveAt((int)btn.Tag);
Controller.ReloadTable ();
```

Наконец Если ячейка представления таблиц используется повторно вместо создания новых, приведенный ниже код настраивает его на основе столбца обработки:

```csharp
// Setup view based on the column selected
switch (tableColumn.Title) {
case "Product":
    view.ImageView.Image = NSImage.ImageNamed ("tag.png");
    view.TextField.StringValue = DataSource.Products [(int)row].Title;
    view.TextField.Tag = row;
    break;
case "Details":
    view.TextField.StringValue = DataSource.Products [(int)row].Description;
    view.TextField.Tag = row;
    break;
case "Action":
    foreach (NSView subview in view.Subviews) {
        var btn = subview as NSButton;
        if (btn != null) {
            btn.Tag = row;
        }
    }
    break;
}

```

Для **действие** столбец, все вложенные представления проверяются до `NSButton` найден, то он будет `Tag` обновляется свойство, чтобы они указывали на текущей строке.

Благодаря этим изменениям на месте при запуске приложения каждая строка будет иметь **удалить** кнопки:

[![](table-view-images/delete02.png "Представление таблицы с помощью кнопки удаления")](table-view-images/delete02.png#lightbox)

Когда пользователь щелкает **удалить** кнопку, будет отображаться предупреждение запросом на удаление данной строки:

[![](table-view-images/delete03.png "Создает предупреждение удаления строк")](table-view-images/delete03.png#lightbox)

Если пользователь выберет delete, строка будет удалена и перерисовывается таблицы:

[![](table-view-images/delete04.png "После удаления строк таблицы")](table-view-images/delete04.png#lightbox)

<a name="Data_Binding_Table_Views" />

## <a name="data-binding-table-views"></a>Представления таблиц привязки данных

С помощью методов кодирования ключ-значение и привязка данных в приложении Xamarin.Mac, можно значительно сократить объем кода, который необходимо писать и поддерживать для заполнения и работать с элементами пользовательского интерфейса. Вы также можете использовать дальнейшего разделения данных резервного (_модель данных_) из вашего front end Interface пользователя (_Model-View-Controller_), что приведет к проще было обслуживать более гибкие приложения Структура.

Ключ-значение кодирования (KVC) — это механизм для доступа к свойствам объекта косвенно, с помощью ключей (специальный формат строки) для определения свойства вместо обращения к ним через переменные экземпляра или методы доступа (`get/set`). Путем реализации кодирования ключ-значение соответствующих методов доступа в приложении Xamarin.Mac, можно получить доступ к другим функциям macOS, например наблюдения ключ-значение (KVO), привязка данных, Core Data, Cocoa привязок и распространяя применимость сценариев.

Дополнительные сведения см. в разделе [привязка данных представления таблицы](~/mac/app-fundamentals/databinding.md#Table_View_Data_Binding) части нашей [привязки данных и кодирования ключ-значение](~/mac/app-fundamentals/databinding.md) документации.

<a name="Summary" />

## <a name="summary"></a>Сводка

В этой статье были предприняты никакие подробные принципы работы с представлениями таблиц в приложении Xamarin.Mac. Мы видели различные типы и использует представления таблиц, как создать и поддерживать представления таблицы в Interface Builder в Xcode и способы работы с представлениями таблиц в коде C#.

## <a name="related-links"></a>Связанные ссылки

- [MacTables (пример)](https://developer.xamarin.com/samples/mac/MacTables/)
- [MacImages (пример)](https://developer.xamarin.com/samples/mac/MacImages/)
- [Привет, Mac](~/mac/get-started/hello-mac.md)
- [Представления структур](~/mac/user-interface/outline-view.md)
- [Списки источников](~/mac/user-interface/source-list.md)
- [Привязка данных и кодирование типа "ключ — значение"](~/mac/app-fundamentals/databinding.md)
- [Рекомендации по работе с человеческим интерфейсом OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [NSTableView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSTableView_Class/index.html#//apple_ref/doc/uid/TP40004125)
- [NSTableViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSTableViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008622)
- [NSTableViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSTableDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004178)
