---
title: Использование ADO.NET с Android
ms.prod: xamarin
ms.assetid: F6ABCEF1-951E-40D8-9EA9-DD79123C2650
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/08/2018
ms.openlocfilehash: 9e0c1be2e37355242db2fb70857d90127c3b5259
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242216"
---
# <a name="using-adonet-with-android"></a>Использование ADO.NET с Android

Xamarin имеет встроенную поддержку базы данных SQLite, которая доступна на устройствах Android и можно предоставлять через знакомый синтаксис в стиле ADO.NET. С помощью этих API требует написания инструкций SQL, которые обрабатываются SQLite, такие как `CREATE TABLE`, `INSERT` и `SELECT` инструкций.

## <a name="assembly-references"></a>Ссылки на сборки

Использование SQLite с помощью ADO.NET, необходимо добавить доступа `System.Data` и `Mono.Data.Sqlite` ссылки на проект Android, как показано ниже:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin) 

![Справочные материалы по Android в Visual Studio](using-adonet-images/image7.png "ссылается на Android в Visual Studio") 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac) 

![Справочные материалы по Android в Visual Studio для Mac](using-adonet-images/image5.png "ссылается на Android в Visual Studio для Mac") 

-----


Щелкните правой кнопкой мыши **ссылки > Изменение ссылок...**  а затем установите необходимые сборки.

## <a name="about-monodatasqlite"></a>О Mono.Data.Sqlite

Мы будем использовать `Mono.Data.Sqlite.SqliteConnection` класс для создания файла пустую базу данных и затем для создания экземпляра `SqliteCommand` объектов, мы можно использовать для выполнения инструкций SQL в базе данных.

**Создание пустой базы данных** &ndash; вызовите `CreateFile` метод с допустимым (т. е. для записи) путь к файлу. Стоит ли файл уже существует, перед вызовом этого метода, в противном случае поверх старого, в котором будет создан новый (пустой) базы данных и данных в старый файл, будут потеряны.
`Mono.Data.Sqlite.SqliteConnection.CreateFile (dbPath);` `dbPath` Переменной должно определяться в соответствии с правилами, см. выше в этом документе.

**Создание подключения к базе данных** &ndash; после создания файла базы данных SQLite, можно создать объект подключения для доступа к данным. Подключение создается с строка подключения, которая принимает форму `Data Source=file_path`, как показано ниже:

```csharp
var connection = new SqliteConnection ("Data Source=" + dbPath);
connection.Open();
// do stuff
connection.Close();
```

Как упоминалось ранее, соединение никогда не следует использовать повторно в разных потоках. Если вы сомневаетесь, создать подключение при необходимости и закройте его, когда все будет готово; но нельзя делать это более часто требуется слишком.

**Создание и выполнение команды базы данных** &ndash; после соединения мы может выполнять произвольные команды SQL для нее. В коде ниже показан `CREATE TABLE` выполняемой инструкции.

```csharp
using (var command = connection.CreateCommand ()) {
    command.CommandText = "CREATE TABLE [Items] ([_id] int, [Symbol] ntext, [Name] ntext);";
    var rowcount = command.ExecuteNonQuery ();
}
```

При выполнении SQL непосредственно в базе данных следует принять обычные меры не вносить недопустимых запросов, таких как попытка создать таблицу, которая уже существует. Хранить список со структурой вашей базы данных, чтобы не вызвать `SqliteException` например **уже существует в таблице ошибок SQLite [элементов]**.

## <a name="basic-data-access"></a>Основные данные доступа

*DataAccess_Basic* пример кода для этого документа выглядит следующим образом, при работе в Android:

![Пример Android ADO.NET](using-adonet-images/image8.png "пример Android ADO.NET")

В приведенном ниже коде показано, как выполнять простые операции SQLite и отображает результаты в виде текста в главное окно приложения.

Вам потребуется включить эти пространства имен:

```csharp
using System;
using System.IO;
using Mono.Data.Sqlite;
```

В следующем образце кода показано взаимодействие всей базы данных.

1.  Создание файла базы данных
2.  Вставка данных
3.  Запрос данных

Эти операции обычно будет в нескольких местах по всему коду, например можно создать файл базы данных и таблиц, при первом запуске приложения и выполнять операции чтения и записи в отдельных экранов в приложении. В следующем примере были сгруппированы в один метод в этом примере:

```csharp
public static SqliteConnection connection;
public static string DoSomeDataAccess ()
{
    // determine the path for the database file
    string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "adodemo.db3");

    bool exists = File.Exists (dbPath);

    if (!exists) {
        Console.WriteLine("Creating database");
        // Need to create the database before seeding it with some data
        Mono.Data.Sqlite.SqliteConnection.CreateFile (dbPath);
        connection = new SqliteConnection ("Data Source=" + dbPath);

        var commands = new[] {
            "CREATE TABLE [Items] (_id ntext, Symbol ntext);",
            "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('1', 'AAPL')",
            "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('2', 'GOOG')",
            "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('3', 'MSFT')"
        };
        // Open the database connection and create table with data
        connection.Open ();
        foreach (var command in commands) {
            using (var c = connection.CreateCommand ()) {
                c.CommandText = command;
                var rowcount = c.ExecuteNonQuery ();
                Console.WriteLine("\tExecuted " + command);
            }
        }
    } else {
        Console.WriteLine("Database already exists");
        // Open connection to existing database file
        connection = new SqliteConnection ("Data Source=" + dbPath);
        connection.Open ();
    }

    // query the database to prove data was inserted!
    using (var contents = connection.CreateCommand ()) {
        contents.CommandText = "SELECT [_id], [Symbol] from [Items]";
        var r = contents.ExecuteReader ();
        Console.WriteLine("Reading data");
        while (r.Read ())
            Console.WriteLine("\tKey={0}; Value={1}",
                              r ["_id"].ToString (),
                              r ["Symbol"].ToString ());
    }
    connection.Close ();
}

```

## <a name="more-complex-queries"></a>Более сложные запросы

Так как SQLite допускает произвольные команды SQL, чтобы отправлять к данным, можно выполнять любые `CREATE`, `INSERT`, `UPDATE`, `DELETE`, или `SELECT` инструкций, вам нравится. Можно узнать из команды SQL, поддерживаемые SQLite на веб-сайте Sqlite. Инструкции SQL выполняются с помощью одного из трех методов на `SqliteCommand` объекта:

-   **ExecuteNonQuery** &ndash; обычно используется для создания или данных вставить таблицы. Для некоторых операций возвращается число затронутых строк, в противном случае — значение -1.

-   **ExecuteReader** &ndash; при коллекцию строк, которые должны возвращаться как `SqlDataReader`.

-   **ExecuteScalar** &ndash; возвращает одно значение (например статистическое выражение).


### <a name="executenonquery"></a>EXECUTENONQUERY

`INSERT`, `UPDATE`, и `DELETE` инструкции возвращает число затронутых строк. Все другие инструкции SQL возвращает -1.

```csharp
using (var c = connection.CreateCommand ()) {
    c.CommandText = "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('1', 'APPL')";
    var rowcount = c.ExecuteNonQuery (); // rowcount will be 1
}
```

### <a name="executereader"></a>EXECUTEREADER

В следующем показан метод `WHERE` предложение в `SELECT` инструкции.
Так как код является создание полная инструкция SQL она должны уделить особое внимание экранирует зарезервированные символы, такие как кавычки (') вокруг строк.

```csharp
public static string MoreComplexQuery ()
{
    var output = "";
    output += "\nComplex query example: ";
    string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal), "ormdemo.db3");

    connection = new SqliteConnection ("Data Source=" + dbPath);
    connection.Open ();
    using (var contents = connection.CreateCommand ()) {
        contents.CommandText = "SELECT * FROM [Items] WHERE Symbol = 'MSFT'";
        var r = contents.ExecuteReader ();
        output += "\nReading data";
        while (r.Read ())
            output += String.Format ("\n\tKey={0}; Value={1}",
                    r ["_id"].ToString (),
                    r ["Symbol"].ToString ());
    }
    connection.Close ();

    return output;
}
```

Метод `ExecuteReader` возвращает объект `SqliteDataReader`. В дополнение к `Read` метод, показанный в примере другие полезные свойства включают:

-   **RowsAffected** &ndash; количество строк, затронутых запросом.

-   **HasRows** &ndash; ли любые строки были возвращены.


### <a name="executescalar"></a>EXECUTESCALAR

Используйте это для `SELECT` инструкций, которые возвращают единственное значение (например, статистическое выражение).

```csharp
using (var contents = connection.CreateCommand ()) {
    contents.CommandText = "SELECT COUNT(*) FROM [Items] WHERE Symbol <> 'MSFT'";
    var i = contents.ExecuteScalar ();
}
```

`ExecuteScalar` — Возвращаемый тип метода `object` &ndash; следует привести результат в зависимости от запроса к базе данных. Результат может быть целое число от `COUNT` запрос или строку из одного столбца `SELECT` запроса. Обратите внимание, что это отличается от других `Execute` методы, возвращающие объект средства чтения или число затронутых строк.



## <a name="related-links"></a>Связанные ссылки

- [DataAccess Basic (пример)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess Advanced (пример)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Рецепты данных для Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Доступ к данным Xamarin.Forms](~/xamarin-forms/app-fundamentals/databases.md)
