---
title: Таблица элементов управления в Xamarin watchOS
description: В этом документе описывается, как использовать элементы управления таблицы watchOS в Xamarin. В нем описывается добавление таблицы, добавление контроллера в строке, создания и заполнения строк, реагирование на касания и многое другое.
ms.prod: xamarin
ms.assetid: 7C14126D-9591-4387-A588-3C4521F11C55
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: cd5e7299874bbfb1b652315a549b9d067d58e9a0
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109397"
---
# <a name="watchos-table-controls-in-xamarin"></a>Таблица элементов управления в Xamarin watchOS

WatchOS `WKInterfaceTable` управления гораздо проще, чем ее аналог операций ввода-вывода, но выполняет ту же роль. Он создает прокручиваемого списка строк, может иметь пользовательские макеты и который отвечает на события касания.

![](table-images/table-list-sml.png "Список наблюдения за таблицы") ![](table-images/table-detail-sml.png)
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="adding-a-table"></a>Добавление таблицы

Перетащите **таблицы** управления сцены. По умолчанию будет выглядеть, как это (отображается макет однострочную неопределенное):

[![](table-images/add-table-sml.png "Добавление таблицы")](table-images/add-table.png#lightbox)

Присвойте имя таблицы в **свойства** через панель **имя** поле, таким образом, чтобы его можно ссылаться в коде.

## <a name="add-a-row-controller"></a>Добавить контроллер строки

Таблица автоматически включает одну строку, представленный контроллер строк, содержащий **группы** элемента управления по умолчанию.

Чтобы задать **класс** контроллера строк, выберите строку в **Структура документа** и введите имя класса в **свойства** панели:

[![](table-images/add-row-controller-sml.png "Ввод имени класса в панели свойств")](table-images/add-row-controller.png#lightbox)

После задания класса для контроллера строки интегрированной среды разработки будет создан соответствующий C# файл в проекте. Перетаскивайте элементы управления (например, метки) в строке и присвойте им имена, поэтому их можно ссылаться в коде.




## <a name="create-and-populate-rows"></a>Создание и заполнение строк

`SetNumberOfRows` создает классы контроллера строку для каждой строки, с помощью `Identifier` для выбора нужного из них. Если вы присвоили контроллере строку пользовательского `Identifier`, изменить **по умолчанию** во фрагменте кода ниже на идентификатор, который вы использовали. `RowController` *Для каждой строки* создается, когда `SetNumberOfRows` вызывается и таблицу, отображенную.

```csharp
myTable.SetNumberOfRows ((nint)rows.Count, "default");
        // loads row controller by identifier
```

> [!IMPORTANT]
> Строки таблицы не являются виртуальными, как в iOS. Попробуйте ограничить количество строк (компания Apple рекомендует меньше 20).

После строки будут созданы, необходимо заполнить каждую из ячеек (как `GetCell` в iOS). Этот фрагмент кода из [WatchTables пример](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/) обновляет метку в каждой строке

```csharp
for (var i = 0; i < rows.Count; i++) {
    var elementRow = (RowController)myTable.GetRowController (i);
    elementRow.myRowLabel.SetText (rows [i]);
}
```

> [!IMPORTANT]
> С помощью `SetNumberOfRows` и затем циклически с помощью `GetRowController` вызывает таблицу целиком, отправляемых в часы. На последующих просмотрах таблицы для добавления или удаления определенных строк используйте `InsertRowsAt` и `RemoveRowsAt` для повышения производительности.


## <a name="respond-to-taps"></a>Ответ на касания

Можно ответить на выбор строк из двух способов:

- реализовать `DidSelectRow` метод в контроллере интерфейс, или
- Создание объекта перехода в раскадровку и реализовать `GetContextForSegue` Если выбор строк, чтобы открыть другой сцены.

### <a name="didselectrow"></a>DidSelectRow

Чтобы программным образом обрабатывать выбор строк, реализовать `DidSelectRow` метод. Чтобы открыть новую сцену, используйте `PushController` и передайте идентификатор сцены и контекст данных для использования:

```csharp
public override void DidSelectRow (WKInterfaceTable table, nint rowIndex)
{
    var rowData = rows [(int)rowIndex];
    Console.WriteLine ("Row selected:" + rowData);
    // if selection should open a new scene
    PushController ("secondInterface", rows[(int)rowIndex]);
}
```

### <a name="getcontextforsegue"></a>GetContextForSegue

Перетащите перехода раскадровки из строки в таблице другой сцену (удерживайте **управления** ключа во время перетаскивания).
Не забудьте выбрать переход и предоставьте ему идентификатор в **свойства** панели (такие как `secondLevel` в примере ниже).

Контроллер интерфейса, реализуйте `GetContextForSegue` метода и возврат контекст данных, который должен быть предоставлен в сцену, представляемый переход.

```csharp
public override NSObject GetContextForSegue (string segueIdentifier, WKInterfaceTable table, nint rowIndex)
{
    if (segueIdentifier == "secondLevel") {
        return new NSString (rows[(int)rowIndex]);
    }
    return null;
}
```

Эти данные передаются в сцену раскадровки целевой объект в его `Awake` метод.

## <a name="multiple-row-types"></a>Несколько типов строк

По умолчанию элемент управления таблицы с типом одну строку, которую можно разработать. Чтобы добавить дополнительные строки «шаблоны» используют **строк** поле **свойства** , чтобы создать дополнительные контроллеры строки:

![](table-images/prototype-rows1.png "Установка числа строк прототипа")

Установка **строк** свойства **3** создаст заполнители дополнительная строка для перетаскивания элементов управления в. Для каждой строки, задайте **класс** имя в **свойства** , чтобы убедиться, создается класс контроллера строки.

![](table-images/prototype-rows2.png "Прототип строк в конструкторе")

Для заполнения таблицы с использованием типов другой строке `SetRowTypes` метод, чтобы задать тип контроллера строки для каждой строки в таблице. Позволяет указать, какой контроллер строк следует использовать для каждой строки идентификаторов строк.

Число элементов в этом массиве должны совпадает с числом строк, которые предполагается, что в таблице:

```csharp
myTable.SetRowTypes (new [] {"type1", "default", "default", "type2", "default"});
```

Заполнение таблицы с несколькими контроллерами строк, потребуется для отслеживания какого типа нужно заполнить пользовательского интерфейса:

```csharp
for (var i = 0; i < rows.Count; i++) {
    if (i == 0) {
        var elementRow = (Type1RowController)myTable.GetRowController (i);
        // populate UI controls
    } else if (i == 3) {
        var elementRow = (Type2RowController)myTable.GetRowController (i);
        // populate UI controls
    } else {
        var elementRow = (DefaultRowController)myTable.GetRowController (i);
        // populate UI controls
    }
}
```


## <a name="vertical-detail-paging"></a>Разбиение на страницы по вертикали детализации

watchOS 3 появилась новая функция для таблиц: возможность прокручивать страницы сведений о связанных с каждой строки без необходимости вернуться назад к таблице и выбрать другую строку. Экраны сведения можно прокручивать, проведение пальцем по экрану вверх и вниз или используя цифровые корона.

![](table-images/table-scroll-sml.png "Пример вертикального разбиение по страницам данных") ![](table-images/table-detail-sml.png)

> [!IMPORTANT]
> Эта функция доступна в настоящее время только путем редактирования в раскадровку Xcode Interface Builder.

Чтобы включить эту функцию, выберите `WKInterfaceTable` на рабочую область конструирования и делений **вертикальной детализации разбиение по страницам** параметр:

![](table-images/vertical-detail-paging-sml.png "При выборе параметра вертикальной детализации разбиение по страницам")

Как [поясненные Apple](https://developer.apple.com/reference/watchkit/wkinterfacetable#1682023) навигации таблицы необходимо использовать переходы для работы средства разбиения по страницам. Переписывать существующий код, использующий `PushController` вместо этого использовать переходами.

<a name="add_row_controller" />

## <a name="appendix-row-controller-code-example"></a>Приложение: Пример кода контроллера строки

При создании контроллера строки в конструкторе, интегрированной среды разработки автоматически создаст два файла кода. Для справки ниже приведен код в созданные файлы.

Первый будет называться для класса, например **RowController.cs**, следующим образом:

```csharp
using System;
using Foundation;

namespace WatchTablesExtension
{
    public partial class RowController : NSObject
    {
        public RowController ()
        {
        }
    }
}
```

Другой **. designer.cs** файл является определение разделяемого класса, содержащий переменные экземпляров и действия, которые создаются в рабочей области конструктора, как показано в примере с одним `WKInterfaceLabel` управления:

```csharp
using Foundation;
using System;
using System.CodeDom.Compiler;
using UIKit;

namespace WatchTables.OnWatchExtension
{
    [Register ("RowController")]
    partial class RowController
    {
        [Outlet]
        [GeneratedCode ("iOS Designer", "1.0")]
        public WatchKit.WKInterfaceLabel MyLabel { get; set; }

        void ReleaseDesignerOutlets ()
        {
            if (MyLabel != null) {
                MyLabel.Dispose ();
                MyLabel = null;
            }
        }
    }
}
```

Переменные экземпляров и действия, здесь же объявляется можно ссылаться в коде — Однако **. designer.cs** файл не следует изменять непосредственно.



## <a name="related-links"></a>Связанные ссылки

- [WatchTables (пример)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/)
- [WatchKitCatalog (пример)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Apple doc таблицы](https://developer.apple.com/reference/watchkit/wkinterfacetable)
