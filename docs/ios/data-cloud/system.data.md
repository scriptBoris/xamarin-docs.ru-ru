---
title: System.Data в Xamarin.iOS
description: В этом документе описывается использование System.Data и Mono.Data.Sqlite.dll для доступа к данным SQLite в приложении Xamarin.iOS.
ms.prod: xamarin
ms.assetid: F10C0C57-7BDE-A3F3-B011-9839949D15C8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.openlocfilehash: 4e9b782cf266a96f30c79eaf139ef88332e02dca
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119870"
---
# <a name="systemdata-in-xamarinios"></a>System.Data в Xamarin.iOS

Xamarin.iOS 8.10 добавлена поддержка [System.Data](xref:System.Data), в том числе `Mono.Data.Sqlite.dll` поставщика ADO.NET. Поддержка включает в себя добавление следующих [сборки](~/cross-platform/internals/available-assemblies.md):

-  `System.Data.dll`
-  `System.Data.Service.Client.dll`
-  `System.Transactions.dll`
-  `Mono.Data.Tds.dll`
-  `Mono.Data.Sqlite.dll`

<a name="Example" />

## <a name="example"></a>Пример

Следующая программа создает базу данных в `Documents/mydb.db3`, и если базы данных не существует, ранее он заполняется с образцами данных. Затем запрашивается базы данных, с помощью выходных данных, записанных `stderr`.

### <a name="add-references"></a>Добавление ссылок

Во-первых, щелкните правой кнопкой мыши **ссылки** узел и выберите **изменить ссылки...**  выберите `System.Data` и `Mono.Data.Sqlite`:

[![](system.data-images/edit-references-sml.png "Добавление новой ссылки")](system.data-images/edit-references.png#lightbox)

### <a name="sample-code"></a>Пример кода

Ниже приведен простой пример. Создание таблицы и вставка строк с применением внедренные команды SQL:

```csharp
using System;
using System.Data;
using System.IO;
using Mono.Data.Sqlite;

class Demo {
    static void Main (string [] args)
    {
        var connection = GetConnection ();
        using (var cmd = connection.CreateCommand ()) {
            connection.Open ();
            cmd.CommandText = "SELECT * FROM People";
            using (var reader = cmd.ExecuteReader ()) {
                while (reader.Read ()) {
                    Console.Error.Write ("(Row ");
                    Write (reader, 0);
                    for (nint i = 1; i < reader.FieldCount; ++i) {
                        Console.Error.Write(" ");
                        Write (reader, i);
                    }
                    Console.Error.WriteLine(")");
                }
            }
            connection.Close ();
        }
    }

    static SqliteConnection GetConnection()
    {
        var documents = Environment.GetFolderPath (
                Environment.SpecialFolder.Personal);
        string db = Path.Combine (documents, "mydb.db3");
        bool exists = File.Exists (db);
        if (!exists)
            SqliteConnection.CreateFile (db);
        var conn = new SqliteConnection("Data Source=" + db);
        if (!exists) {
            var commands = new[] {
            "CREATE TABLE People (PersonID INTEGER NOT NULL, FirstName ntext, LastName ntext)",
            // WARNING: never insert user-entered data with embedded parameter values
            "INSERT INTO People (PersonID, FirstName, LastName) VALUES (1, 'First', 'Last')",
            "INSERT INTO People (PersonID, FirstName, LastName) VALUES (2, 'Dewey', 'Cheatem')",
            "INSERT INTO People (PersonID, FirstName, LastName) VALUES (3, 'And', 'How')",
            };
            conn.Open ();
            foreach (var cmd in commands) {
                using (var c = conn.CreateCommand()) {
                    c.CommandText = cmd;
                    c.CommandType = CommandType.Text;
                    c.ExecuteNonQuery ();
                }
            }
            conn.Close ();
        }
        return conn;
    }

    static void Write(SqliteDataReader reader, int index)
    {
        Console.Error.Write("({0} '{1}')",
                reader.GetName(index),
                reader [index]);
    }
}
```

> [!IMPORTANT]
> Как упоминалось в приведенном выше примере, не рекомендуется, чтобы внедрять строки в командах SQL, так как он делает код уязвимым для [путем внедрения кода SQL](http://en.wikipedia.org/wiki/SQL_injection).


### <a name="using-command-parameters"></a>С помощью параметров команды

Приведенный ниже показано использование параметров команды вставляемый введенный пользователем текст безопасно в базе данных (даже если текст содержит специальные символы SQL, такие как апостроф один):

```csharp
// user input from Textbox control
var fname = fnameTextbox.Text;
var lname = lnameTextbox.Text;
// use command parameters to safely insert into database
using (var addCmd = conn.CreateCommand ()) {
    addCmd.CommandText = "INSERT INTO [People] (PersonID, FirstName, LastName) VALUES (@COL1, @COL2, @COL3)";
    addCmd.CommandType = System.Data.CommandType.Text;
    addCmd.AddParameterWithValue ("@COL1", 1);
    addCmd.AddParameterWithValue ("@COL2", fname);
    addCmd.AddParameterWithValue ("@COL3", lname);
    addCmd.ExecuteNonQuery ();
}
```

<a name="Missing_Functionality" />

## <a name="missing-functionality"></a>Отсутствующие функции

Оба **System.Data** и **Mono.Data.Sqlite** отсутствуют некоторые функциональные возможности.

<a name="System.Data" />

### <a name="systemdata"></a>System.Data

Функциональные возможности, отсутствующие на **System.Data.dll** состоит из:

-  Все, что требование [System.CodeDom](xref:System.CodeDom) (например)  [System.Data.TypedDataSetGenerator](xref:System.Data.TypedDataSetGenerator) )
-  XML-ФАЙЛ конфигурации файл поддержки (например)  [System.Data.Common.DbProviderConfigurationHandler](xref:System.Data.Common.DbProviderConfigurationHandler) )
-   [System.Data.Common.DbProviderFactories](xref:System.Data.Common.DbProviderFactories) (зависит от поддержки файла конфигурации XML)
-   [System.Data.OleDb](xref:System.Data.OleDb)
-   [System.Data.Odbc](xref:System.Data.Odbc)
-  `System.EnterpriseServices.dll` Зависимость была *удалены* из `System.Data.dll` , что может привести к удалению [SqlConnection.EnlistDistributedTransaction(ITransaction)](xref:System.Data.SqlClient.SqlConnection.EnlistDistributedTransaction*) метод.


<a name="Mono.Data.Sqlite" />

### <a name="monodatasqlite"></a>Mono.Data.Sqlite

В то же время **Mono.Data.Sqlite.dll** обязанность без изменения исходного кода, но вместо этого может должна быть размещена ряд *среды выполнения* проблемы с момента `Mono.Data.Sqlite.dll` привязывает SQLite 3.5. iOS 8, в то же время поставляется с SQLite 3.8.5. Достаточно сказать между двумя версиями изменились некоторые принципы.

Более старые версии iOS поставляются со следующими версиями SQLite:

- **iOS 7** -версии 3.7.13.
- **iOS 6** -версии 3.7.13.
- **iOS 5** -версии 3.7.7.
- **операций ввода-вывода 4** -версии 3.6.22.

Наиболее распространенных проблем могут быть связаны с запрос для схемы базы данных, например определение во время выполнения, в которой столбцы существуют на данной таблице, такие как `Mono.Data.Sqlite.SqliteConnection.GetSchema` (переопределение [DbConnection.GetSchema](xref:System.Data.Common.DbConnection.GetSchema) и `Mono.Data.Sqlite.SqliteDataReader.GetSchemaTable` (переопределение [DbDataReader.GetSchemaTable](xref:System.Data.Common.DbDataReader.GetSchemaTable). Короче говоря, кажется, что-либо с помощью [DataTable](xref:System.Data.DataTable) не будет работать.

<a name="Data_Binding" />

## <a name="data-binding"></a>Привязка данных

В настоящее время не поддерживается привязка данных.

