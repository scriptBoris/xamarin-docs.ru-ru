---
title: С помощью ADO.NET с помощью Xamarin.iOS
description: В этом документе описывается, как использовать ADO.NET как метод для доступа к SQLite в приложении Xamarin.iOS. В нем описывается, ссылки на сборки, Mono.Data.Sqlite и BasicDataAccess образца.
ms.prod: xamarin
ms.assetid: 79078A4D-2D24-44F3-9543-B50418A7A000
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 9314e1b69df4a5965dfd045d0b4ca3e44f1b9de6
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122300"
---
# <a name="using-adonet-with-xamarinios"></a>С помощью ADO.NET с помощью Xamarin.iOS

Xamarin имеет встроенную поддержку базу данных SQLite, которая доступна на устройствах iOS, доступной с помощью знакомого синтаксиса в стиле ADO.NET. С помощью этих API требует написания инструкций SQL, которые обрабатываются SQLite, такие как `CREATE TABLE`, `INSERT` и `SELECT` инструкций.

## <a name="assembly-references"></a>Ссылки на сборки

Использование SQLite с помощью ADO.NET, необходимо добавить доступа `System.Data` и `Mono.Data.Sqlite` ссылки на проект iOS, как показано ниже (для примеров в Visual Studio для Mac и Visual Studio):

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

 ![](using-adonet-images/image4.png "Ссылки на сборки в Visual Studio для Mac")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  ![](using-adonet-images/image6.png "Ссылки на сборки в Visual Studio")

-----

Щелкните правой кнопкой мыши **ссылки > Изменение ссылок...**  а затем установите необходимые сборки.

## <a name="about-monodatasqlite"></a>О Mono.Data.Sqlite

Мы будем использовать `Mono.Data.Sqlite.SqliteConnection` класс для создания файла пустую базу данных и затем для создания экземпляра `SqliteCommand` объектов, мы можно использовать для выполнения инструкций SQL в базе данных.


1. **Создание пустой базы данных** -вызов `CreateFile` метод с допустимым (т. е. для записи) путь к файлу. Стоит ли файл уже существует, перед вызовом этого метода, в противном случае поверх старого, в котором будет создан новый (пустой) базы данных и данные в старом файле будут потеряны:

    `Mono.Data.Sqlite.SqliteConnection.CreateFile (dbPath);`

    > [!NOTE]
    > `dbPath` Переменной должно определяться в соответствии с правилами, см. выше в этом документе.

2. **Создание подключения к базе данных** — после создания файла базы данных SQLite можно создать объект подключения для доступа к данным. Подключение создается с строка подключения, которая принимает форму `Data Source=file_path`, как показано ниже:

    ```csharp
    var connection = new SqliteConnection ("Data Source=" + dbPath);
    connection.Open();
    // do stuff
    connection.Close();
    ```

    Как упоминалось ранее, соединение никогда не следует использовать повторно в разных потоках. Если вы сомневаетесь, создать подключение при необходимости и закройте его, когда все будет готово; но нельзя делать это более часто требуется слишком.
    
3. **Создание и выполнение команды базы данных** — когда у нас есть подключения, мы может выполнять произвольные команды SQL для нее. В приведенном ниже коде показано выполнение инструкции CREATE TABLE.

    ```csharp
    using (var command = connection.CreateCommand ()) {
        command.CommandText = "CREATE TABLE [Items] ([_id] int, [Symbol] ntext, [Name] ntext);";
        var rowcount = command.ExecuteNonQuery ();
    }
    ```

При выполнении SQL непосредственно в базе данных следует принять обычные меры не вносить недопустимых запросов, таких как попытка создать таблицу, которая уже существует. Хранить список со структурой вашей базы данных, таким образом, чтобы не вызвать SqliteException, такие как «ошибка SQLite таблицы [элементов] уже существует».

## <a name="basic-data-access"></a>Основные данные доступа

*DataAccess_Basic* пример кода для этого документа выглядит следующим образом, при работе на iOS:

 ![](using-adonet-images/image9.png "Пример ADO.NET iOS")

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

Так как SQLite допускает произвольные команды SQL, чтобы отправлять к данным, можно выполнять независимо от СОЗДАНИЯ, вставки, обновления, удаления или, но следует инструкции SELECT. Можно узнать из команды SQL, поддерживаемые SQLite на веб-сайте Sqlite. Инструкции SQL выполняются с помощью одного из трех методов на объекте SqliteCommand:

-  **ExecuteNonQuery** — обычно используются для создания или данных вставить таблицы. Для некоторых операций возвращается число затронутых строк, в противном случае — значение -1.
-  **ExecuteReader** — используется при коллекцию строк, которые должны возвращаться как `SqlDataReader` .
-  **ExecuteScalar** — возвращает одно значение (например статистическое выражение).


### <a name="executenonquery"></a>EXECUTENONQUERY

Инструкции INSERT, UPDATE и DELETE возвращает число затронутых строк. Все другие инструкции SQL возвращает -1.

```csharp
using (var c = connection.CreateCommand ()) {
    c.CommandText = "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('1', 'APPL')";
    var rowcount = c.ExecuteNonQuery (); // rowcount will be 1
}
```

### <a name="executereader"></a>EXECUTEREADER

В следующем методе показано предложение WHERE в инструкции SELECT. Так как код является создание полная инструкция SQL она должны уделить особое внимание экранирует зарезервированные символы, такие как кавычки (') вокруг строк.

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

Метод ExecuteReader возвращает объект SqliteDataReader. Помимо метода Read, показано в примере другие полезные свойства включают:

-  **RowsAffected** — количество строк, затронутых запросом.
-  **HasRows** определяет, когда все строки были возвращены.


### <a name="executescalar"></a>EXECUTESCALAR

Используйте это для инструкций SELECT, которые возвращают единственное значение (например, статистическое выражение).

```csharp
using (var contents = connection.CreateCommand ()) {
    contents.CommandText = "SELECT COUNT(*) FROM [Items] WHERE Symbol <> 'MSFT'";
    var i = contents.ExecuteScalar ();
}
```

`ExecuteScalar` — Возвращаемый тип метода `object` – следует привести результат в зависимости от запроса к базе данных. Результат может быть целое число от запроса число или строку из одного столбца запроса SELECT. Обратите внимание, что это отличается от других методов Execute, которые возвращают объект средства чтения или число затронутых строк.


## <a name="related-links"></a>Связанные ссылки

- [DataAccess Basic (пример)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess Advanced (пример)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS рецепты данных](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Доступ к данным Xamarin.Forms](~/xamarin-forms/app-fundamentals/databases.md)
