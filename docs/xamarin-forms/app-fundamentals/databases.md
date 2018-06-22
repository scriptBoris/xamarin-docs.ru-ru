---
title: Xamarin.Forms локальной базы данных
description: Xamarin.Forms поддерживает приложения на основе базы данных, используя компонент database engine SQLite, что делает возможным для загрузки и сохранения объектов в общем коде. В этой статье описывается, как Xamarin.Forms приложения могут читать и записывать данные к локальной базе данных SQLite, с помощью SQLite.Net.
ms.prod: xamarin
ms.assetid: F687B24B-7DF0-4F8E-A21A-A9BB507480EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2018
ms.openlocfilehash: feec4993a0719a083d713e084552b18aead8ee42
ms.sourcegitcommit: eac092f84b603958c761df305f015ff84e0fad44
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36310144"
---
# <a name="xamarinforms-local-databases"></a>Xamarin.Forms локальной базы данных

_Xamarin.Forms поддерживает приложения на основе базы данных, используя компонент database engine SQLite, что делает возможным для загрузки и сохранения объектов в общем коде. В этой статье описывается, как Xamarin.Forms приложения могут читать и записывать данные к локальной базе данных SQLite, с помощью SQLite.Net._

## <a name="overview"></a>Обзор

Xamarin.Forms приложения могут использовать [SQLite.NET PCL NuGet](https://www.nuget.org/packages/sqlite-net-pcl/) пакета для включения операций базы данных в общий код, ссылаясь на `SQLite` классов, поставляемых в NuGet. Операции с базой данных можно определить в проекте библиотеки .NET Standard решения Xamarin.Forms.

Сопутствующий [образец приложения](https://github.com/xamarin/xamarin-forms-samples/tree/master/Todo) это простое приложение Todo list. Следующих снимках экрана показано, как образец отображается на каждой платформе.

[![Снимки экрана примера базы данных Xamarin.Forms](databases-images/todo-list-sml.png "TodoList первой страницы, снимки экрана")](databases-images/todo-list.png#lightbox "TodoList первой страницы, снимки экрана") [ ![ Снимки экрана примера базы данных Xamarin.Forms](databases-images/todo-list-sml.png "TodoList первой страницы, снимки экрана")](databases-images/todo-list.png#lightbox "TodoList первой страницы, снимки экрана")

<a name="Using_SQLite_with_PCL" />

## <a name="using-sqlite"></a>С помощью SQLite

Чтобы добавить поддержку SQLite Xamarin.Forms .NET стандартную библиотеку, используйте функцию поиска NuGet для поиска **sqlite-net-pcl** и установите последнюю версию пакета:

![Добавьте пакет NuGet SQLite.NET PCL](databases-images/vs2017-sqlite-pcl-nuget.png "добавьте пакет NuGet SQLite.NET PCL")

Существует несколько пакетов NuGet с одинаковыми именами, правильный пакет имеет следующие атрибуты:

- **Созданные:** Krueger Михаил A.
- **Идентификатор:** sqlite net переносимую библиотеку классов
- **NuGet ссылку:** [sqlite net переносимую библиотеку классов](https://www.nuget.org/packages/sqlite-net-pcl/)

> [!NOTE]
> Несмотря на это имя пакета, используйте **sqlite-net-pcl** пакет NuGet даже в проектах .NET Standard.

После добавления ссылки Добавление свойства `App` класс, который возвращает путь к локальному файлу для хранения базы данных:

```csharp
static TodoItemDatabase database;

public static TodoItemDatabase Database
{
  get
  {
    if (database == null)
    {
      database = new TodoItemDatabase(
        Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "TodoSQLite.db3"));
    }
    return database;
  }
}
```

`TodoItemDatabase` Конструктор, который принимает путь к файлу базы данных в качестве аргумента, показан ниже:

```csharp
public TodoItemDatabase(string dbPath)
{
  database = new SQLiteAsyncConnection(dbPath);
  database.CreateTableAsync<TodoItem>().Wait();
}
```

Работает преимущество предоставление доступа к базе данных как единственное значение, создается подключение к одной базе данных сохраняется открытым во время выполнения приложения, таким образом избежать затрат на открытие и закрытие файла базы данных каждый раз при операции базы данных производится.

В оставшейся части `TodoItemDatabase` класс содержит SQLite запросы, которые выполняются между различными платформами. Ниже приведен пример запроса (Дополнительные сведения о синтаксисе можно найти в [с помощью SQLite.NET](~/cross-platform/app-fundamentals/index.md) статьи):

```csharp
public Task<List<TodoItem>> GetItemsAsync()
{
  return database.Table<TodoItem>().ToListAsync();
}

public Task<List<TodoItem>> GetItemsNotDoneAsync()
{
  return database.QueryAsync<TodoItem>("SELECT * FROM [TodoItem] WHERE [Done] = 0");
}

public Task<TodoItem> GetItemAsync(int id)
{
  return database.Table<TodoItem>().Where(i => i.ID == id).FirstOrDefaultAsync();
}

public Task<int> SaveItemAsync(TodoItem item)
{
  if (item.ID != 0)
  {
    return database.UpdateAsync(item);
  }
  else {
    return database.InsertAsync(item);
  }
}

public Task<int> DeleteItemAsync(TodoItem item)
{
  return database.DeleteAsync(item);
}
```

> [!NOTE]
> Преимуществом использования асинхронный интерфейс API SQLite.Net является базы данных операций, перемещаются в фоновых потоках. Кроме того нет необходимости для записи дополнительных параллелизма, код обработки, так как он обеспечивает API-интерфейса.

## <a name="summary"></a>Сводка

Xamarin.Forms поддерживает приложения на основе базы данных, используя компонент database engine SQLite, что делает возможным для загрузки и сохранения объектов в общем коде.

В этой статье основное внимание уделено **доступ к** в базу данных SQLite, с помощью Xamarin.Forms. Дополнительные сведения о работе с самой SQLite.Net посвящены [SQLite.NET на Android](~/android/data-cloud/data-access/using-sqlite-orm.md) или [SQLite.NET на iOS](~/ios/data-cloud/data/using-sqlite-orm.md) документации.

## <a name="related-links"></a>Связанные ссылки

- [Образец TODO](https://developer.xamarin.com/samples/xamarin-forms/Todo/)
- [Примеры Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [База данных книги](https://developer.xamarin.com/workbooks/xamarin-forms/application-fundamentals/database/database.workbook)
