---
title: Использование для SQLite.NET с Android
description: Библиотека PCL NuGet для SQLite.NET механизм простые данные доступа для приложений Xamarin.Android.
ms.prod: xamarin
ms.assetid: 3447B7EE-A320-489E-AF02-E5721097760A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/18/2018
ms.openlocfilehash: 741e18d84c25bb4479480949a271a5845e99daa1
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118465"
---
# <a name="using-sqlitenet-with-android"></a>Использование для SQLite.NET с Android

Для SQLite.NET библиотеки, в которой Xamarin рекомендует является очень простой ORM, которая позволяет легко сохранять и извлекать объекты в локальной базе данных SQLite на устройстве Android. ORM — это реляционное сопоставление объекта &ndash; API, который позволяет сохранять и извлекать «объекты» из базы данных без написания инструкций SQL.

Чтобы включить для SQLite.NET библиотеки в приложении Xamarin, добавьте следующий пакет NuGet в проект:

- **Имя пакета:** sqlite-net-pcl
- **Автор:** Крюгер A. франк
- **Идентификатор:** sqlite-net-pcl
- **URL-адрес:** [nuget.org/packages/sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

[![Пакет NuGet для SQLite.NET](using-sqlite-orm-images/image1a-sml.png "пакет NuGet для SQLite.NET")](using-sqlite-orm-images/image1a.png#lightbox)

> [!TIP]
> Существует несколько различных пакетов SQLite — не забудьте выбрать оптимальный вариант, (он может быть лучших результатов поиска).

При наличии доступных библиотек для SQLite.NET, выполните следующие три действия, чтобы использовать его для доступа к базе данных.

1.  **Добавить с помощью инструкции** &ndash; добавьте следующую инструкцию, чтобы C# файлов, где требуется доступ к данным:

    ```csharp
    using SQLite;
    ```

2.  **Создайте пустую базу данных** &ndash; ссылку на базу данных можно создать, передав в конструктор класса SQLiteConnection путь к файлу. Необходимо проверить, существует ли уже файл &ndash; она будет создана автоматически при необходимости, в противном случае будет открыть существующий файл базы данных. `dbPath` Переменной должно определяться в соответствии с правилами, см. выше в этом документе:

    ```csharp
    var db = new SQLiteConnection (dbPath);
    ```

3.  **Сохранить данные** &ndash; выполняются после создания объекта SQLiteConnection команд базы данных путем вызова его методов, например CreateTable и Insert следующим образом:

    ```csharp
    db.CreateTable<Stock> ();
    db.Insert (newStock); // after creating the newStock object
    ```

4.  **Получить данные** &ndash; для извлечения объекта (или список объектов) используйте следующий синтаксис:

    ```csharp
    var stock = db.Get<Stock>(5); // primary key id of 5
    var stockList = db.Table<Stock>();
    ```

## <a name="basic-data-access-sample"></a>Доступ к образцу данных

*DataAccess_Basic* пример кода для этого документа выглядит следующим образом, при выполнении на устройствах Android. В коде показано, как выполнять простые операции для SQLite.NET и отображает результаты в виде текста в главное окно приложения.


**Android**

![Пример Android для SQLite.NET](using-sqlite-orm-images/image3.png "пример Android для SQLite.NET")

В следующем образце кода показано взаимодействие всей базы данных с помощью библиотеки для SQLite.NET для инкапсуляции базового доступа к базе данных.
Отобразится:

1.  Создание файла базы данных

2.  Вставка данных путем создания объектов и сохранять их

3.  Запрос данных

Вам потребуется включить эти пространства имен:

```csharp
using SQLite; // from the github SQLite.cs class
```

Последний требует, что вы добавили SQLite в проект. Обратите внимание, что в таблице базы данных SQLite определяется путем добавления атрибутов к классу ( `Stock` класс) вместо того, чтобы команда CREATE TABLE.

```csharp
[Table("Items")]
public class Stock {
    [PrimaryKey, AutoIncrement, Column("_id")]
    public int Id { get; set; }
    [MaxLength(8)]
    public string Symbol { get; set; }
}
public static void DoSomeDataAccess () {
       Console.WriteLine ("Creating database, if it doesn't already exist");
   string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "ormdemo.db3");
   var db = new SQLiteConnection (dbPath);
   db.CreateTable<Stock> ();
   if (db.Table<Stock> ().Count() == 0) {
        // only insert the data if it doesn't already exist
        var newStock = new Stock ();
        newStock.Symbol = "AAPL";
        db.Insert (newStock);
        newStock = new Stock ();
        newStock.Symbol = "GOOG";
        db.Insert (newStock);
        newStock = new Stock ();
        newStock.Symbol = "MSFT";
        db.Insert (newStock);
    }
    Console.WriteLine("Reading data");
    var table = db.Table<Stock> ();
    foreach (var s in table) {
        Console.WriteLine (s.Id + " " + s.Symbol);
    }
}
```

С помощью `[Table]` атрибута без указания параметра имени таблицы приведет к базовых таблиц базы данных имеет имя, совпадающее с именем класса (в данном случае «Stock»). Имя существующей таблицы важно, если написать SQL-запросы непосредственно к базе данных, а не использовать методы доступа к данным ORM. Аналогичным образом `[Column("_id")]` атрибут является необязательным и если значение отсутствует столбец добавляется в таблицу с тем же именем, как свойство в классе.

## <a name="sqlite-attributes"></a>Атрибуты SQLite

Общие атрибуты, которые можно применять к классам для управления, как они хранятся в основной базе данных:

-   **[PrimaryKey]**  &ndash; Этот атрибут может применяться к целочисленное свойство для первичного ключа базовой таблицы. Составные первичные ключи не поддерживаются.

-   **[AutoIncrement]**  &ndash; Этот атрибут приводит значение целочисленного свойства быть с автоматическим приращением для каждого нового объекта, вставляемого в базе данных

-   **[Column(name)]**  &ndash; Указав необязательный `name` переопределит значение по умолчанию имя базового столбца базы данных (который является таким же, как свойства).

-   **[Table(name)]**  &ndash; Помечает класс как возможность хранить в базовой таблице SQLite. Необязательное имя параметра переопределит значение по умолчанию имя базовой таблицы базы данных (который совпадает с именем класса).

-   **[MaxLength(value)]**  &ndash; Ограничить длину свойства текста, при попытке вставки баз данных. Использование кода это следует проверить перед вставкой объекта, так как этот атрибут «проверяется только» при вставке базы данных или попытке выполнить операцию обновления.

-   **[Игнорировать]**  &ndash; Вызывает для SQLite.NET игнорировать это свойство.
    Это особенно полезно для свойства, которые имеют тип, не могут храниться в базе данных или свойства, что модель коллекций, которые не удалось устранить автоматически быть SQLite.

-   **[Unique]**  &ndash; Обеспечивает уникальность значений в столбце базовой базы данных.


Большинство этих атрибутов являются необязательными, SQLite будет использовать значения по умолчанию для имен таблиц и столбцов. Таким образом, чтобы запросы выделения и удаления может выполняться эффективно данных всегда следует указывать целочисленного первичного ключа.

## <a name="more-complex-queries"></a>Более сложные запросы

Следующие методы в `SQLiteConnection` может использоваться для выполнения других операций с данными:

-   **Вставить** &ndash; добавляет новый объект в базу данных.

-   **Получить&lt;T&gt;**  &ndash; пытается получить объект, используя первичный ключ.

-   **Таблица&lt;T&gt;**  &ndash; возвращает все объекты в таблице.

-   **Удалить** &ndash; удаляет объект, используя его первичный ключ.

-   **Запрос&lt;T&gt;**  &ndash; выполнения SQL-запрос, возвращающий несколько строк (как объекты).

-   **Выполнение** &ndash; используйте этот метод (и не `Query`) Если не предполагается, что строки обратно из SQL (например, инструкции INSERT, UPDATE и DELETE).


### <a name="getting-an-object-by-the-primary-key"></a>Получение объекта по первичному ключу

Для SQLite.Net предоставляет метод Get для извлечения отдельного объекта, на основе его первичного ключа.

```csharp
var existingItem = db.Get<Stock>(3);
```

### <a name="selecting-an-object-using-linq"></a>При выборе объекта с помощью Linq

Методы, которые возвращают коллекции поддерживают `IEnumerable<T>` , можно использовать Linq для запроса или Сортировка содержимого таблицы. Ниже приведен пример использования Linq для фильтрации всех записей, которые начинаются с буквы «A»:

```csharp
var apple = from s in db.Table<Stock>()
    where s.Symbol.StartsWith ("A")
    select s;
Console.WriteLine ("-> " + apple.FirstOrDefault ().Symbol);
```

### <a name="selecting-an-object-using-sql"></a>При выборе объекта с помощью SQL

Несмотря на то, что для SQLite.Net можно предоставить на базе объектов доступа к данным, иногда может потребоваться выполнить более сложный запрос, чем позволяет Linq (или может потребоваться повышения производительности). С помощью метода запроса, можно использовать команды SQL, как показано ниже:

```csharp
var stocksStartingWithA = db.Query<Stock>("SELECT * FROM Items WHERE Symbol = ?", "A");
foreach (var s in stocksStartingWithA) {
    Console.WriteLine ("a " + s.Symbol);
}
```

> [!NOTE]
> При написании инструкций SQL непосредственно вы создаете зависимость на имена таблиц и столбцов базы данных, которые были созданы из классов и их атрибуты. Если изменить эти имена в коде необходимо обновить все самостоятельно написанное инструкции SQL.

### <a name="deleting-an-object"></a>Удаление объекта

Первичный ключ используется для удаления строки, как показано ниже:

```csharp
var rowcount = db.Delete<Stock>(someStock.Id); // Id is the primary key
```

Вы можете проверить `rowcount` для подтверждения того, сколько строк затронуто (в этом случае удаляются).

## <a name="using-sqlitenet-with-multiple-threads"></a>Использование нескольких потоков для SQLite.NET

SQLite поддерживает три разных потоков режима: *одним потоком*, *многопоточную*, и *сериализованных*. Если вы хотите получить доступ к базе данных из нескольких потоков без каких-либо ограничений, можно настроить SQLite для использования **сериализованных** threading режим. Очень важно установить этот режим на раннем этапе приложение (например, в начале `OnCreate` метод).

Чтобы изменить режим работы с потоками, вызовите `SqliteConnection.SetConfig`. Например, эта строка кода настраивает SQLite для **сериализованных** режиме: 

```csharp
SqliteConnection.SetConfig(SQLiteConfig.Serialized);
```

Android версию SQLite имеет ограничение, необходимо выполнить несколько дополнительных действий. Если вызов `SqliteConnection.SetConfig` создает исключение, связанное с SQLite, такие как `library used incorrectly`, используйте следующий обходной путь:

1.  Ссылка на собственный **libsqlite.so** библиотеки, чтобы `sqlite3_shutdown` и `sqlite3_initialize` интерфейсы API становятся доступными для приложения:

    ```csharp
    [DllImport("libsqlite.so")]
    internal static extern int sqlite3_shutdown();

    [DllImport("libsqlite.so")]
    internal static extern int sqlite3_initialize();
    ```


2.  В самом начале `OnCreate` метод, добавьте следующий код для завершения работы SQLite, настройте его для **сериализованных** режиме и повторная инициализация SQLite:

    ```csharp
    sqlite3_shutdown();
    SqliteConnection.SetConfig(SQLiteConfig.Serialized);
    sqlite3_initialize();
    ```

Этот способ также подходит для `Mono.Data.Sqlite` библиотеки. Дополнительные сведения о SQLite и многопоточность, см. в разделе [SQLite и нескольких потоков](https://www.sqlite.org/threadsafe.html). 

## <a name="related-links"></a>Связанные ссылки

- [DataAccess Basic (пример)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess Advanced (пример)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Доступ к данным Xamarin.Forms](~/xamarin-forms/app-fundamentals/databases.md)
