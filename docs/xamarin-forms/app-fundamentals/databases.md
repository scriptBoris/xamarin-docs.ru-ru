---
title: Xamarin.Forms локальной базы данных
description: Xamarin.Forms поддерживает приложения на основе базы данных с помощью ядра СУБД SQLite, что делает возможным для загрузки и сохранения объектов в общем коде. В этой статье описывается, как приложения Xamarin.Forms могут читать и записывать данные для локальной базы данных SQLite, используя для SQLite.Net.
ms.prod: xamarin
ms.assetid: F687B24B-7DF0-4F8E-A21A-A9BB507480EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2018
ms.openlocfilehash: 05c77c4c47841a897d0d1de5c3ba004db524ea2d
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2018
ms.locfileid: "36310144"
---
# <a name="xamarinforms-local-databases"></a>Xamarin.Forms локальной базы данных

_Xamarin.Forms поддерживает приложения на основе базы данных с помощью ядра СУБД SQLite, что делает возможным для загрузки и сохранения объектов в общем коде. В этой статье описывается, как приложения Xamarin.Forms могут читать и записывать данные для локальной базы данных SQLite, используя для SQLite.Net._

## <a name="overview"></a>Обзор

Приложения Xamarin.Forms могут использовать [NuGet переносимой библиотеки Классов для SQLite.NET](https://www.nuget.org/packages/sqlite-net-pcl/) пакета для включения операций базы данных в общий код, ссылаясь на `SQLite` классы, которые поставляются в NuGet. Операции с базой данных можно определить в проекте библиотеки .NET Standard решения Xamarin.Forms.

Сопутствующий [пример приложения](https://github.com/xamarin/xamarin-forms-samples/tree/master/Todo) представляет собой простое приложение списка задач. На следующих снимках экрана показано, как выглядит пример на каждой платформе:

[![Снимки экрана базы данных с Xamarin.Forms](databases-images/todo-list-sml.png "TodoList первой страницы, снимки экрана")](databases-images/todo-list.png#lightbox "TodoList первой страницы, снимки экрана") [ ![ Снимки экрана базы данных с Xamarin.Forms](databases-images/todo-list-sml.png "TodoList первой страницы, снимки экрана")](databases-images/todo-list.png#lightbox "TodoList первой страницы, снимки экрана")

<a name="Using_SQLite_with_PCL" />

## <a name="using-sqlite"></a>С помощью SQLite

Чтобы добавить поддержку SQLite в библиотеку Xamarin.Forms .NET Standard, используйте функцию поиска NuGet для поиска **sqlite-net-pcl** и установите последнюю версию пакета:

![Добавьте пакет NuGet для SQLite.NET PCL](databases-images/vs2017-sqlite-pcl-nuget.png "добавьте пакет NuGet для SQLite.NET PCL")

Существует несколько пакетов NuGet с одинаковыми именами, правильный пакет имеет атрибуты:

- **Созданные:** Крюгер A. франк
- **Идентификатор:** sqlite-net-pcl
- **Ссылку на NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

> [!NOTE]
> Несмотря на название пакета, используйте **sqlite-net-pcl** даже в проектов .NET Standard пакет NuGet.

После добавления ссылки, добавьте свойство, чтобы `App` класс, который возвращает путь к локальному файлу для хранения базы данных:

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

Преимущество предоставление доступа к базе данных, как единственный экземпляр есть, что создается подключение к одной базе данных, сохраняется открытым во время выполнения приложения работает, выполняется таким образом избежать затрат на открытие и закрытие файла базы данных каждый раз, операции с базой данных.

В оставшейся части `TodoItemDatabase` класс содержит SQLite запросы, которые выполняются между различными платформами. Ниже приведен пример запроса кода (Дополнительные сведения о синтаксисе можно найти в [с помощью для SQLite.NET с помощью Xamarin.iOS](~/ios/data-cloud/data/using-sqlite-orm.md).

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
> Преимуществом использования асинхронный интерфейс API для SQLite.Net является базы данных operations, перемещаются в фоновых потоках. Кроме того нет необходимости писать код обработки, так как API берет на себя его повышает уровень параллелизма.

## <a name="summary"></a>Сводка

Xamarin.Forms поддерживает приложения на основе базы данных с помощью ядра СУБД SQLite, что делает возможным для загрузки и сохранения объектов в общем коде.

В этой статье внимание уделяется **доступ к** базой данных SQLite с помощью Xamarin.Forms. Дополнительные сведения о работе с для SQLite.Net сам см [для SQLite.NET в Android](~/android/data-cloud/data-access/using-sqlite-orm.md) или [для SQLite.NET в iOS](~/ios/data-cloud/data/using-sqlite-orm.md) документации.

## <a name="related-links"></a>Связанные ссылки

- [Примере TODO](https://developer.xamarin.com/samples/xamarin-forms/Todo/)
- [Примеры Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)

