---
title: С помощью ADO.NET с Android
ms.prod: xamarin
ms.assetid: F6ABCEF1-951E-40D8-9EA9-DD79123C2650
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/08/2018
ms.openlocfilehash: 29e81afdf2c46cdefc68e2c2fae4e6e47999a346
ms.sourcegitcommit: 797597d902330652195931dec9ac3e0cc00792c5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/20/2018
ms.locfileid: "31646785"
---
# <a name="using-adonet-with-android"></a>С помощью ADO.NET с Android

Xamarin имеет встроенную поддержку для базы данных SQLite, доступный на Android и может предоставляться через знакомый синтаксис ADO.NET. С помощью этих API требует написания инструкций SQL, которые обрабатываются SQLite, такие как `CREATE TABLE`, `INSERT` и `SELECT` инструкции.

## <a name="assembly-references"></a>Ссылки на сборки

Использование доступа SQLite через ADO.NET, необходимо добавить `System.Data` и `Mono.Data.Sqlite` ссылки на проект Android, как показано ниже:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin) 

![Android ссылки в Visual Studio](using-adonet-images/image7.png "ссылается на Android в Visual Studio") 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac) 

![Android ссылки в Visual Studio для Mac](using-adonet-images/image5.png "ссылается на Android в Visual Studio для Mac") 

-----


Щелкните правой кнопкой мыши **References > Изменить ссылки...**  а затем установите необходимые сборки.

## <a name="about-monodatasqlite"></a>О Mono.Data.Sqlite

Мы будем использовать `Mono.Data.Sqlite.SqliteConnection` класс, чтобы создать пустую базу данных и затем для создания экземпляра `SqliteCommand` объектов, мы можно использовать для выполнения инструкций SQL в базе данных.

**Создание пустой базы данных** &ndash; вызовите `CreateFile` метод с недопустимым (т. е. для записи) путь к файлу. Стоит ли перед вызовом этого метода уже существует файл, иначе будут созданы новые базы данных (пусто) поверх старого и данные в существующем файле будут потеряны.
`Mono.Data.Sqlite.SqliteConnection.CreateFile (dbPath);` `dbPath` Переменной должно определяться в соответствии с правилами, описанных ранее в этом документе.

**Создание подключения к базе данных** &ndash; после создания файла базы данных SQLite, можно создать объект подключения для доступа к данным. Подключение создается со строкой соединения, которая принимает форму `Data Source=file_path`, как показано ниже:

```csharp
var connection = new SqliteConnection ("Data Source=" + dbPath);
connection.Open();
// do stuff
connection.Close();
```

Как упоминалось ранее, соединение никогда не следует использовать повторно в разных потоках. Если вы сомневаетесь, создания соединения при необходимости и закрыть его, после завершения; Однако следует учитывать это более часто требуется слишком.

**Создание и выполнение команды базы данных** &ndash; после подключения можно выполнить произвольные команды SQL для нее. Ниже показан код `CREATE TABLE` выполняемой инструкции.

```csharp
using (var command = connection.CreateCommand ()) {
    command.CommandText = "CREATE TABLE [Items] ([_id] int, [Symbol] ntext, [Name] ntext);";
    var rowcount = command.ExecuteNonQuery ();
}
```

При выполнении SQL непосредственно с базой данных, необходимо выполнить обычные меры предосторожности не делать недопустимых запросов, например Попытка создать таблицу, которая уже существует. Хранить список структуры базы данных, чтобы не вызвать `SqliteException` например **уже существует в таблице ошибок SQLite [элементы]**.

## <a name="basic-data-access"></a>Доступ к данным, основные

*DataAccess_Basic* образцы кода для этого документа выглядит следующим образом, при работе на Android:

![Пример Android ADO.NET](using-adonet-images/image8.png "пример Android ADO.NET")

В следующем примере кода показано, как выполнять простые операции SQLite и показывает результаты в виде текста в главном окне приложения.

Необходимо иметь для включения этих пространств имен:

```csharp
using System;
using System.IO;
using Mono.Data.Sqlite;
```

В следующем образце кода показано взаимодействие всей базы данных.

1.  Создание файла базы данных
2.  Вставка данных
3.  Запрос данных

Эти операции обычно отобразятся в нескольких местах по всему коду, например можно создать файл базы данных и таблиц, при первом запуске приложения и выполнять операции чтения и записи в отдельных экранов в приложении. В следующем примере были сгруппированы в один метод в этом примере:

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

Поскольку SQLite допускает произвольные команды SQL, которую следует выполнить для данных, можно выполнять любые `CREATE`, `INSERT`, `UPDATE`, `DELETE`, или `SELECT` инструкций, вам нравится. Вы найдете сведения о поддерживаемых SQLite на веб-сайте Sqlite команды SQL. Инструкции SQL выполняются с помощью одного из трех методов на `SqliteCommand` объекта:

-   **ExecuteNonQuery** &ndash; обычно используется для создания или данных вставить таблицы. Для некоторых операций возвращается число обработанных строк, в противном случае — значение -1.

-   **ExecuteReader** &ndash; использовавшейся коллекции строк, которые должны возвращаться как `SqlDataReader`.

-   **ExecuteScalar** &ndash; возвращает одно значение (например агрегатную функцию).


### <a name="executenonquery"></a>EXECUTENONQUERY

`INSERT`, `UPDATE`, и `DELETE` инструкции возвращает количество затронутых строк. Все инструкции SQL возвращает -1.

```csharp
using (var c = connection.CreateCommand ()) {
    c.CommandText = "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('1', 'APPL')";
    var rowcount = c.ExecuteNonQuery (); // rowcount will be 1
}
```

### <a name="executereader"></a>EXECUTEREADER

В следующем показан метод `WHERE` предложения в `SELECT` инструкции.
Так как код, дополнительно полную инструкцию SQL его должны уделить особое внимание escape-зарезервированные символы, такие как кавычки (') вокруг строки.

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

Метод `ExecuteReader` возвращает объект `SqliteDataReader`. В дополнение к `Read` метода, который показан в примере другие полезные свойства включают:

-   **RowsAffected** &ndash; количество строк, затронутых запросом.

-   **HasRows** &ndash; ли все строки были возвращены.


### <a name="executescalar"></a>EXECUTESCALAR

Используйте его для `SELECT` инструкций, которые возвращают одиночное значение (например, статистическое).

```csharp
using (var contents = connection.CreateCommand ()) {
    contents.CommandText = "SELECT COUNT(*) FROM [Items] WHERE Symbol <> 'MSFT'";
    var i = contents.ExecuteScalar ();
}
```

`ExecuteScalar` Имеет возвращаемый тип метода `object` &ndash; следует приведения результата в зависимости от запроса к базе данных. Результат может быть целое число от `COUNT` запрос или строку из одного столбца `SELECT` запроса. Обратите внимание, что это отличается от других `Execute` методы, возвращающие объект считывания или показатель числа обработанных строк.



## <a name="related-links"></a>Связанные ссылки

- [DataAccess Basic (пример)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess Advanced (пример)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Рецепты данных для Android](https://developer.xamarin.com/recipes/android/data/)
- [Доступ к данным Xamarin.Forms](~/xamarin-forms/app-fundamentals/databases.md)
