---
title: Представления структуры в Xamarin.Mac
description: В этой статье рассматривается работа с представлениями структур в приложении Xamarin.Mac. Он описывает, создании и обслуживании представлений структур в Xcode и конструкторе Interface Builder и программным способом работы с ними.
ms.prod: xamarin
ms.assetid: 043248EE-11DA-4E96-83A3-08824A4F2E01
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 7228a22eeae3dfdb354ba3693c277251fd2cd821
ms.sourcegitcommit: 47709db4d115d221e97f18bc8111c95723f6cb9b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2018
ms.locfileid: "40251216"
---
# <a name="outline-views-in-xamarinmac"></a>Представления структуры в Xamarin.Mac

_В этой статье рассматривается работа с представлениями структур в приложении Xamarin.Mac. Он описывает, создании и обслуживании представлений структур в Xcode и конструкторе Interface Builder и программным способом работы с ними._

При работе с C# и .NET в приложении Xamarin.Mac, у вас есть доступ к той же структуры представления, работающему *Objective-C* и *Xcode* does. Поскольку Xamarin.Mac напрямую интегрируется с Xcode, можно использовать конструктор _Interface Builder_ для создания и поддержания вашего представления структуры (или при необходимости создать их непосредственно в коде C#).

Структура — это тип таблицы, который позволяет пользователю развернуть или свернуть строки иерархических данных. Как представление таблицы структура отображаются данные для набора связанных элементов, строк, представляющих отдельные элементы и столбцы, представляющие атрибуты этих элементов. В отличие от представления таблицы не являются элементы в представлении структуры в виде плоского списка, они организованы в иерархии, как файлы и папки на жестком диске.

[![](outline-view-images/populate03.png "Запустите пример приложения")](outline-view-images/populate03.png#lightbox)

В этой статье мы обсудим, что узнаете основы работы с представлениями структур в приложении Xamarin.Mac. Настоятельно рекомендуется работать через [Привет, Mac](~/mac/get-started/hello-mac.md) статьи, во-первых, в частности [введение в Xcode и конструкторе Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) и [переменных экземпляров и действий](~/mac/get-started/hello-mac.md#outlets-and-actions) разделы, так как он рассматриваются основные понятия и методы, которые мы будем использовать в этой статье.

Может потребоваться взгляните на [предоставление C# классы / методы Objective-c](~/mac/internals/how-it-works.md) раздел [структуре Xamarin.Mac](~/mac/internals/how-it-works.md) документов, здесь объясняется `Register` и `Export` команды используется для передачи классов C# в службе Objective-C объекты и элементы пользовательского интерфейса.

<a name="Introduction_to_Outline_Views" />

## <a name="introduction-to-outline-views"></a>Введение в режиме структуры

Структура — это тип таблицы, который позволяет пользователю развернуть или свернуть строки иерархических данных. Как представление таблицы структура отображаются данные для набора связанных элементов, строк, представляющих отдельные элементы и столбцы, представляющие атрибуты этих элементов. В отличие от представления таблицы не являются элементы в представлении структуры в виде плоского списка, они организованы в иерархии, как файлы и папки на жестком диске.

Если элемент в представлении структуры содержит другие элементы, его можно разворачивать и сворачивать пользователем. Расширяемый элемент отображает треугольник, указывающей вправо, когда элемент будет свернут и обращена вниз, если элемент развернут. Щелкнув треугольник вызывает элемент, чтобы развернуть или свернуть.

Представление структуры (`NSOutlineView`) является подклассом представления таблицы (`NSTableView`) и таким образом, наследует большую часть своего поведения от родительского класса. Таким образом многие операции, поддерживаемые таблицы представления, включая выбор строк или столбцов, изменение расположения столбцов, перетащив столбец заголовков и т. д., также поддерживаются структура. Xamarin.Mac управлять этими функциями и приложения можно настроить параметры представления структуры (либо в коде или конструктора Interface Builder), чтобы разрешать или запрещать определенные операции.

Структура не сохраняет данные своими собственными, вместо этого она зависит от источника данных (`NSOutlineViewDataSource`) для предоставления строк и столбцов, необходимых, по мере необходимости.

Режим представления структуры можно настроить, указав подкласс делегата представление структуры (`NSOutlineViewDelegate`) для поддержки управления столбца структуры, введите для выбора функции, выбор строк и редактирования, настраиваемое отслеживание и пользовательские представления для отдельных столбцы и строки.

Так как структура совместно использует большую часть такое поведение и функции с в табличное представление, можно пройти через наши [представления таблиц](~/mac/user-interface/table-view.md) документации, прежде чем продолжить в этой статье.

<a name="Creating_and_Maintaining_Outline_Views_in_Xcode" />

## <a name="creating-and-maintaining-outline-views-in-xcode"></a>Создании и обслуживании представлений структур в Xcode

При создании нового приложения Cocoa для Xamarin.Mac, вы получаете стандартное окно пустым, по умолчанию. В данном окне определяется в `.storyboard` файл, автоматически включаются в проект. Редактирование своего проекта windows, в **обозревателе решений**, дважды щелкните `Main.storyboard` файла:

[![](outline-view-images/edit01.png "Выбрав основной раскадровки")](outline-view-images/edit01.png#lightbox)

Он откроется окно конструктора Interface Builder в Xcode:

[![](outline-view-images/edit02.png "Изменение пользовательского интерфейса в Xcode")](outline-view-images/edit02.png#lightbox)

Тип `outline` в **инспектор библиотеки** поле поиска, чтобы было проще найти элементы управления представления структуры:

[![](outline-view-images/edit03.png "Выбрав структура из библиотеки")](outline-view-images/edit03.png#lightbox)

Перетащите контроллер представления в представлении структуры **редактор интерфейса**, сделайте его заполнения области содержимого контроллера представления и задайте для него значение, где он сжимает и растет вместе с окна в **Редактор ограничений**:

[![](outline-view-images/edit04.png "Изменение ограничения")](outline-view-images/edit04.png#lightbox)

Выберите режим структуры **иерархия интерфейса** и следующие свойства доступны в **инспекторе атрибутов**:

[![](outline-view-images/edit05.png "Инспектор атрибутов")](outline-view-images/edit05.png#lightbox)

- **Столбец структуры** -столбец таблицы, в котором отображается иерархических данных.
- **Столбец структуры автосохранения** — Если `true`, столбец структуры будет автоматически сохраняется и восстанавливается между запусками приложения.
- **Отступ** -значение отступа для столбцов с списке развернутый элемент.
- **Ячейки соответствует отступа** — Если `true`, Марк отступа будут иметь отступ вместе с ячейки.
- **Автоматически сохранять элементы расширен** — Если `true`, свернутого состояния элементов будет автоматически сохраняется и восстанавливается между запусками приложения.
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
> Если необходимо хранить устаревшего приложения Xamarin.Mac, `NSView` на основе структуры представления, которые должны использоваться через `NSCell` на основе представления таблиц. `NSCell` считается прежних версий и могут не поддерживаться в будущем.

Выберите столбец в таблице **иерархия интерфейса** и следующие свойства доступны в **инспекторе атрибутов**:

[![](outline-view-images/edit06.png "Инспектор атрибутов")](outline-view-images/edit06.png#lightbox)

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

[![](outline-view-images/edit07.png "Инспектор атрибутов")](outline-view-images/edit07.png#lightbox)

Ниже приведены все свойства стандартное представление. Имеется также возможность изменения размера строк для этого столбца здесь.

Выберите ячейки представления таблиц (по умолчанию, это `NSTextField`) в **иерархия интерфейса** и следующие свойства доступны в **инспекторе атрибутов**:

[![](outline-view-images/edit08.png "Инспектор атрибутов")](outline-view-images/edit08.png#lightbox)

У вас будет все свойства стандартный текстового поля, чтобы задать здесь. По умолчанию стандартный текстовое поле используется для отображения данных для ячейки в столбце.

Выберите представление ячейки таблицы (`NSTableFieldCell`) в **иерархия интерфейса** и следующие свойства доступны в **инспекторе атрибутов**:

[![](outline-view-images/edit09.png "Инспектор атрибутов")](outline-view-images/edit09.png#lightbox)

Ниже перечислены наиболее важные параметры здесь.

- **Макет** — выберите, каким образом размещаются в ячейках этого столбца.
- **Использует режим единого строки** — Если `true`, ячейки ограничена одной строки.
- **Первый ширины макет среды выполнения** — Если `true`, ячейки предпочтут ширины, установите для него (вручную или автоматически) при его отображается при первом запуске приложения.
- **Действие** -контролирует, когда изменение **действие** отправляется для ячейки.
- **Поведение** -определяет, если ячейка находится и редактировать.
- **Форматированный текст** — Если `true`, ячейка может отображать текст, отформатированный, примененным стилем.
- **Отменить** — Если `true`, ячейки несет ответственность, для его — отменить поведение.

Выберите представление ячейки таблицы (`NSTableFieldCell`) в нижней части столбца таблицы в **иерархия интерфейса**:

[![](outline-view-images/edit11.png "Выбор представления ячейки таблицы")](outline-view-images/edit10.png#lightbox)

Это дает возможность изменить представление ячейки таблицы, используемый как базовый _шаблон_ для всех ячеек, созданных для данного столбца.

<a name="Adding_Actions_and_Outlets" />

### <a name="adding-actions-and-outlets"></a>Добавление действия и источники

Так же как и любые другие Cocoa элемента управления пользовательского интерфейса, нам нужно предоставить нашего представления структуры и его столбцы и ячейки в C# кода с помощью **действия** и **розеток** (с учетом функциональность, необходимую).

Процесс одинаков для любого элемента представления структуры, необходимо предоставить:

1. Переключиться в режим **вспомогательном редакторе** и убедитесь, что `ViewController.h` выбран файл: 

    [![](outline-view-images/edit11.png "Выбор правильного h-файл")](outline-view-images/edit11.png#lightbox)
2. Выберите представление структуры из **иерархия интерфейса**, щелкните и перетащите `ViewController.h` файл.
3. Создание **розетки** для представления структуры, которая называется `ProductOutline`: 

    [![](outline-view-images/edit13.png "Настройка выхода")](outline-view-images/edit13.png#lightbox)
4. Создание **розеток** для столбцов таблицы также называются `ProductColumn` и `DetailsColumn`: 

    [![](outline-view-images/edit14.png "Настройка выхода")](outline-view-images/edit14.png#lightbox)
5. Сохранить изменения и вернуться в Visual Studio для Mac синхронизировать с Xcode.

Далее мы напишем код отображения некоторых данных для структуры при запуске приложения.

<a name="Populating_the_Table_View" />

## <a name="populating-the-outline-view"></a>Заполнение представления структуры

С помощью нашего представления структуры, разработанной в конструктор Interface Builder и предоставляются через **розетки**, Далее нам нужно создать код C# для заполнения.

Во-первых давайте создадим новый `Product` класса, содержащего данные для отдельных строк и группы продуктов sub. В **обозревателе решений**, щелкните правой кнопкой мыши проект и выберите **добавить** > **новый файл...** Выберите **Общие** > **пустой класс**, введите `Product` для **имя** и нажмите кнопку **New** кнопки:

[![](outline-view-images/populate01.png "Создание пустой класс")](outline-view-images/populate01.png#lightbox)

Сделать `Product.cs` внешний файл следующим образом:

```csharp
using System;
using Foundation;
using System.Collections.Generic;

namespace MacOutlines
{
    public class Product : NSObject
    {
        #region Public Variables
        public List<Product> Products = new List<Product>();
        #endregion

        #region Computed Properties
        public string Title { get; set;} = "";
        public string Description { get; set;} = "";
        public bool IsProductGroup {
            get { return (Products.Count > 0); }
        }
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

Далее нам необходимо создать подкласс `NSOutlineDataSource` для предоставления данных для нашей структуры по запросу. В **обозревателе решений**, щелкните правой кнопкой мыши проект и выберите **добавить** > **новый файл...** Выберите **Общие** > **пустой класс**, введите `ProductOutlineDataSource` для **имя** и нажмите кнопку **New** кнопки.

Изменить `ProductTableDataSource.cs` файл и это должно выглядеть следующим образом:

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacOutlines
{
    public class ProductOutlineDataSource : NSOutlineViewDataSource
    {
        #region Public Variables
        public List<Product> Products = new List<Product>();
        #endregion

        #region Constructors
        public ProductOutlineDataSource ()
        {
        }
        #endregion

        #region Override Methods
        public override nint GetChildrenCount (NSOutlineView outlineView, NSObject item)
        {
            if (item == null) {
                return Products.Count;
            } else {
                return ((Product)item).Products.Count;
            }

        }

        public override NSObject GetChild (NSOutlineView outlineView, nint childIndex, NSObject item)
        {
            if (item == null) {
                return Products [childIndex];
            } else {
                return ((Product)item).Products [childIndex];
            }
                
        }

        public override bool ItemExpandable (NSOutlineView outlineView, NSObject item)
        {
            if (item == null) {
                return Products [0].IsProductGroup;
            } else {
                return ((Product)item).IsProductGroup;
            }
        
        }
        #endregion
    }
}
```

Этот класс имеет хранилище для нашего представления структуры элементов и переопределяет `GetChildrenCount` для возврата количества строк в таблице. `GetChild` Возвращает конкретного родительского или дочернего элемента (по запросу представления структуры) и `ItemExpandable` определяет указанный элемент как дочерний или родительский элемент.

Наконец, нам нужно создать подкласс `NSOutlineDelegate` для обеспечения поведения для наших структуры. В **обозревателе решений**, щелкните правой кнопкой мыши проект и выберите **добавить** > **новый файл...** Выберите **Общие** > **пустой класс**, введите `ProductOutlineDelegate` для **имя** и нажмите кнопку **New** кнопки.

Изменить `ProductOutlineDelegate.cs` файл и это должно выглядеть следующим образом:

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacOutlines
{
    public class ProductOutlineDelegate : NSOutlineViewDelegate
    {
        #region Constants 
        private const string CellIdentifier = "ProdCell";
        #endregion

        #region Private Variables
        private ProductOutlineDataSource DataSource;
        #endregion

        #region Constructors
        public ProductOutlineDelegate (ProductOutlineDataSource datasource)
        {
            this.DataSource = datasource;
        }
        #endregion

        #region Override Methods

        public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
            // This pattern allows you reuse existing views when they are no-longer in use.
            // If the returned view is null, you instance up a new view
            // If a non-null view is returned, you modify it enough to reflect the new data
            NSTextField view = (NSTextField)outlineView.MakeView (CellIdentifier, this);
            if (view == null) {
                view = new NSTextField ();
                view.Identifier = CellIdentifier;
                view.BackgroundColor = NSColor.Clear;
                view.Bordered = false;
                view.Selectable = false;
                view.Editable = false;
            }

            // Cast item
            var product = item as Product;

            // Setup view based on the column selected
            switch (tableColumn.Title) {
            case "Product":
                view.StringValue = product.Title;
                break;
            case "Details":
                view.StringValue = product.Description;
                break;
            }

            return view;
        }
        #endregion
    }
}
```

Когда мы создаем экземпляр объекта `ProductOutlineDelegate`, мы передаем в экземпляре `ProductOutlineDataSource` , предоставляющий данные для контура. `GetView` Метод отвечает за возврат представление (данные), чтобы отобразить ячейку для столбца и строки. Если это возможно существующее представление будет использоваться повторно для отображения этой ячейки, в противном случае необходимо создать новое представление.

Для заполнения контура, изменим `MainWindow.cs` и создайте `AwakeFromNib` внешний метод следующим образом:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Create data source and populate
    var DataSource = new ProductOutlineDataSource ();

    var Vegetables = new Product ("Vegetables", "Greens and Other Produce");
    Vegetables.Products.Add (new Product ("Cabbage", "Brassica oleracea - Leaves, axillary buds, stems, flowerheads"));
    Vegetables.Products.Add (new Product ("Turnip", "Brassica rapa - Tubers, leaves"));
    Vegetables.Products.Add (new Product ("Radish", "Raphanus sativus - Roots, leaves, seed pods, seed oil, sprouting"));
    Vegetables.Products.Add (new Product ("Carrot", "Daucus carota - Root tubers"));
    DataSource.Products.Add (Vegetables);

    var Fruits = new Product ("Fruits", "Fruit is a part of a flowering plant that derives from specific tissues of the flower");
    Fruits.Products.Add (new Product ("Grape", "True Berry"));
    Fruits.Products.Add (new Product ("Cucumber", "Pepo"));
    Fruits.Products.Add (new Product ("Orange", "Hesperidium"));
    Fruits.Products.Add (new Product ("Blackberry", "Aggregate fruit"));
    DataSource.Products.Add (Fruits);

    var Meats = new Product ("Meats", "Lean Cuts");
    Meats.Products.Add (new Product ("Beef", "Cow"));
    Meats.Products.Add (new Product ("Pork", "Pig"));
    Meats.Products.Add (new Product ("Veal", "Young Cow"));
    DataSource.Products.Add (Meats);

    // Populate the outline
    ProductOutline.DataSource = DataSource;
    ProductOutline.Delegate = new ProductOutlineDelegate (DataSource);

}
```

Если запустить приложение, отображается следующая информация:

[![](outline-view-images/populate02.png "Свернутое представление")](outline-view-images/populate02.png#lightbox)

Если мы развернуть узел в представлении структуры, он будет выглядеть следующим образом:

[![](outline-view-images/populate03.png "Расширенное представление")](outline-view-images/populate03.png#lightbox)

<a name="Sorting_by_Column" />

## <a name="sorting-by-column"></a>Сортировка по столбцу

Разрешим пользователю сортировать данные в структуре, щелкнув заголовок столбца. Во-первых, дважды щелкните `Main.storyboard` файл, чтобы открыть его для редактирования в конструкторе Interface Builder. Выберите `Product` столбца, введите `Title` для **ключ сортировки**, `compare:` для **селектор** и выберите `Ascending` для **порядок**:

[![](outline-view-images/sort01.png "Установка ключа порядка сортировки")](outline-view-images/sort01.png#lightbox)

Сохранить изменения и вернуться в Visual Studio для Mac синхронизировать с Xcode.

Теперь изменим `ProductOutlineDataSource.cs` файл и добавьте следующие методы:

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
    }
}

public override void SortDescriptorsChanged (NSOutlineView outlineView, NSSortDescriptor[] oldDescriptors)
{
    // Sort the data
    Sort (oldDescriptors [0].Key, oldDescriptors [0].Ascending);
    outlineView.ReloadData ();
}
```

`Sort` Метода позволяют сортировать данные в источнике данных, на основе заданного `Product` поля класса в возрастающем или убывающем порядке. Переопределенный `SortDescriptorsChanged` метод будет вызываться каждый раз, использование щелкает заголовок столбца. Он будет передан **ключ** значение, которое задается в конструкторе Interface Builder и порядок сортировки для этого столбца.

Если запустить приложение и нажмите кнопку в заголовках столбцов, строк будут отсортированы по этому столбцу:

[![](outline-view-images/sort02.png "Пример сортировки выходных данных")](outline-view-images/sort02.png#lightbox)

<a name="Row_Selection" />

## <a name="row-selection"></a>Выбор строк

Если вы хотите разрешить пользователю выбрать одну строку, дважды щелкните `Main.storyboard` файл, чтобы открыть его для редактирования в конструкторе Interface Builder. Выберите режим структуры **иерархия интерфейса** и снимите флажок **несколько** флажок в **инспекторе атрибутов**:

[![](outline-view-images/select01.png "Инспектор атрибутов")](outline-view-images/select01.png#lightbox)

Сохранить изменения и вернуться в Visual Studio для Mac синхронизировать с Xcode.


Далее следует изменить `ProductOutlineDelegate.cs` файл и добавьте следующий метод:

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

Это позволит пользователю выбрать любой отдельной строки в представлении структуры. Вернуть `false` для `ShouldSelectItem` для хотя бы один элемент, который вы не хотите, чтобы пользователь мог выбрать или `false` для каждого элемента, если вы не хотите пользователя, чтобы иметь возможность выбрать какие-либо элементы.

<a name="Multiple_Row_Selection" />

## <a name="multiple-row-selection"></a>Выбор нескольких строк

Если вы хотите разрешить пользователю выбрать несколько строк, дважды щелкните `Main.storyboard` файл, чтобы открыть его для редактирования в конструкторе Interface Builder. Выберите режим структуры **иерархия интерфейса** и проверьте **несколько** флажок в **инспекторе атрибутов**:

[![](outline-view-images/select02.png "Инспектор атрибутов")](outline-view-images/select02.png#lightbox)

Сохранить изменения и вернуться в Visual Studio для Mac синхронизировать с Xcode.


Далее следует изменить `ProductOutlineDelegate.cs` файл и добавьте следующий метод:

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

Это позволит пользователю выбрать любой отдельной строки в представлении структуры. Вернуть `false` для `ShouldSelectRow` для хотя бы один элемент, который вы не хотите, чтобы пользователь мог выбрать или `false` для каждого элемента, если вы не хотите пользователя, чтобы иметь возможность выбрать какие-либо элементы.

<a name="Type_to_Select_Row" />

## <a name="type-to-select-row"></a>Тип, чтобы выбрать строку

Если вы хотите разрешить пользователю вводить символ с помощью выбранного представления структуры и выберите первую строку, содержащий этот символ, дважды щелкните `Main.storyboard` файл, чтобы открыть его для редактирования в конструкторе Interface Builder. Выберите режим структуры **иерархия интерфейса** и проверьте **выберите тип** флажок в **инспекторе атрибутов**:

[![](outline-view-images/type01.png "Редактирование строк типа")](outline-view-images/type01.png#lightbox)

Сохранить изменения и вернуться в Visual Studio для Mac синхронизировать с Xcode.

Теперь изменим `ProductOutlineDelegate.cs` файл и добавьте следующий метод:

```csharp
public override NSObject GetNextTypeSelectMatch (NSOutlineView outlineView, NSObject startItem, NSObject endItem, string searchString)
{
    foreach(Product product in DataSource.Products) {
        if (product.Title.Contains (searchString)) {
            return product;
        }
    }

    // Not found
    return null;
}
```

`GetNextTypeSelectMatch` Альбома заданного `searchString` и возвращает элемент первого `Product` с этой строки в его `Title`.

<a name="Reordering_Columns" />

## <a name="reordering-columns"></a>Изменение порядка столбцов

Если вы хотите разрешить пользователю перетаскивать изменение порядка столбцов в представлении структуры, дважды щелкните `Main.storyboard` файл, чтобы открыть его для редактирования в конструкторе Interface Builder. Выберите режим структуры **иерархия интерфейса** и проверьте **переупорядочивание** флажок в **инспекторе атрибутов**:

[![](outline-view-images/reorder01.png "Инспектор атрибутов")](outline-view-images/reorder01.png#lightbox)

Если мы предоставляем значение **автосохранения** свойство и проверьте **сведения о столбце** поле, любые изменения, вносимые для макета таблицы будут автоматически сохранены для нас и восстановления следующего приложения выполняется.

Сохранить изменения и вернуться в Visual Studio для Mac синхронизировать с Xcode.

Теперь изменим `ProductOutlineDelegate.cs` файл и добавьте следующий метод:

```csharp
public override bool ShouldReorder (NSOutlineView outlineView, nint columnIndex, nint newColumnIndex)
{
    return true;
}
```

`ShouldReorder` Метод должен возвращать `true` любого столбца, он должен иметь возможность быть перетащите переупорядочить в `newColumnIndex`, в противном случае возвращаемое `false`;

Если запустить приложение, можно перетащить заголовки столбцов вокруг переупорядочить столбцы:

[![](outline-view-images/reorder02.png "Пример изменения порядка столбцов")](outline-view-images/reorder02.png#lightbox)

<a name="Editing_Cells" />

## <a name="editing-cells"></a>Редактирование ячеек

Если вы хотите разрешить пользователю изменять значения для данной ячейки, изменить `ProductOutlineDelegate.cs` файлов и изменить `GetViewForItem` метод следующим образом:

```csharp
public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
    // Cast item
    var product = item as Product;

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTextField view = (NSTextField)outlineView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTextField ();
        view.Identifier = tableColumn.Title;
        view.BackgroundColor = NSColor.Clear;
        view.Bordered = false;
        view.Selectable = false;
        view.Editable = !product.IsProductGroup;
    }

    // Tag view
    view.Tag = outlineView.RowForItem (item);

    // Allow for edit
    view.EditingEnded += (sender, e) => {

        // Grab product
        var prod = outlineView.ItemAtRow(view.Tag) as Product;

        // Take action based on type
        switch(view.Identifier) {
        case "Product":
            prod.Title = view.StringValue;
            break;
        case "Details":
            prod.Description = view.StringValue;
            break; 
        }
    };

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.StringValue = product.Title;
        break;
    case "Details":
        view.StringValue = product.Description;
        break;
    }

    return view;
}
```

Теперь Если запустить приложение, пользователь может изменять ячейки в табличном представлении:

[![](outline-view-images/editing01.png "Пример изменения ячейки")](outline-view-images/editing01.png#lightbox)

<a name="Using_Images_in_Outline_Views" />

## <a name="using-images-in-outline-views"></a>Использование изображений в представления структуры

Для включения изображения как часть ячейки в `NSOutlineView`, вам потребуется изменить как данных, возвращаемых методом представления структуры `NSTableViewDelegate's` `GetView` метод, используемый `NSTableCellView` вместо обычного `NSTextField`. Пример:

```csharp
public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
    // Cast item
    var product = item as Product;

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)outlineView.MakeView (tableColumn.Title, this);
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
        view.TextField.Editable = !product.IsProductGroup;
    }

    // Tag view
    view.TextField.Tag = outlineView.RowForItem (item);

    // Allow for edit
    view.TextField.EditingEnded += (sender, e) => {

        // Grab product
        var prod = outlineView.ItemAtRow(view.Tag) as Product;

        // Take action based on type
        switch(view.Identifier) {
        case "Product":
            prod.Title = view.TextField.StringValue;
            break;
        case "Details":
            prod.Description = view.TextField.StringValue;
            break; 
        }
    };

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed (product.IsProductGroup ? "tags.png" : "tag.png");
        view.TextField.StringValue = product.Title;
        break;
    case "Details":
        view.TextField.StringValue = product.Description;
        break;
    }

    return view;
}
```

Дополнительные сведения см. в разделе [с помощью образов с помощью представления структуры](~/mac/app-fundamentals/image.md) части нашей [работе с изображением](~/mac/app-fundamentals/image.md) документации.

<a name="Data_Binding_Outline_Views" />

## <a name="data-binding-outline-views"></a>Представления структуры привязки данных

С помощью методов кодирования ключ-значение и привязка данных в приложении Xamarin.Mac, можно значительно сократить объем кода, который необходимо писать и поддерживать для заполнения и работать с элементами пользовательского интерфейса. Вы также можете использовать дальнейшего разделения данных резервного (_модель данных_) из вашего front end Interface пользователя (_Model-View-Controller_), что приведет к проще было обслуживать более гибкие приложения Структура.

Ключ-значение кодирования (KVC) — это механизм для доступа к свойствам объекта косвенно, с помощью ключей (специальный формат строки) для определения свойства вместо обращения к ним через переменные экземпляра или методы доступа (`get/set`). Путем реализации кодирования ключ-значение соответствующих методов доступа в приложении Xamarin.Mac, можно получить доступ к другим функциям macOS, например наблюдения ключ-значение (KVO), привязка данных, Core Data, Cocoa привязок и распространяя применимость сценариев.

Дополнительные сведения см. в разделе [привязки данных представление структуры](~/mac/app-fundamentals/databinding.md#Outline_View_Data_Binding) части нашей [привязки данных и кодирования ключ-значение](~/mac/app-fundamentals/databinding.md) документации.

<a name="Summary" />

## <a name="summary"></a>Сводка

В этой статье были предприняты никакие подробные принципы работы с представлениями структур в приложении Xamarin.Mac. Мы видели различные типы и использует представления структуры, как создавать и обслуживать представлений структур в Interface Builder в Xcode и способы работы с представлениями структур в коде C#.

## <a name="related-links"></a>Связанные ссылки

- [MacOutlines (пример)](https://developer.xamarin.com/samples/mac/MacOutlines/)
- [MacImages (пример)](https://developer.xamarin.com/samples/mac/MacImages/)
- [Привет, Mac](~/mac/get-started/hello-mac.md)
- [Представления таблиц](~/mac/user-interface/table-view.md)
- [Списки источников](~/mac/user-interface/source-list.md)
- [Привязка данных и кодирование типа "ключ — значение"](~/mac/app-fundamentals/databinding.md)
- [Рекомендации по работе с человеческим интерфейсом OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Введение в режиме структуры](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/OutlineView/OutlineView.html#//apple_ref/doc/uid/10000023i)
- [NSOutlineView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSOutlineView_Class/index.html#//apple_ref/doc/uid/TP40004079)
- [NSOutlineViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSOutlineViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004175)
- [NSOutlineViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSOutlineViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008609)
