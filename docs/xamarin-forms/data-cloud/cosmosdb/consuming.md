---
title: Использование базы данных документов Azure Cosmos DB
description: В этой статье объясняется, как использовать клиентской библиотеке .NET Standard для Azure Cosmos DB для интеграции базы данных документов Azure Cosmos DB в приложение Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 7C0605D9-9B7F-4002-9B60-2B5DAA3EA30C
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: 7ea7a9bb66e039289e8ce09e77c223c98f90e4c1
ms.sourcegitcommit: 6be6374664cd96a7d924c2e0c37aeec4adf8be13
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617635"
---
# <a name="consuming-an-azure-cosmos-db-document-database"></a>Использование базы данных документов Azure Cosmos DB

_База данных документов Azure Cosmos DB является базой данных NoSQL, которая предоставляет высокоскоростной доступ к документам JSON, предоставляющие доступ к службе быстрый, высокой степенью доступности и базы данных для приложений, которым требуется эффективное масштабирование и глобальная репликация. В этой статье объясняется, как использовать клиентской библиотеке .NET Standard для Azure Cosmos DB для интеграции базы данных документов Azure Cosmos DB в приложение Xamarin.Forms._

> [!VIDEO https://youtube.com/embed/BoVH12igmbg]

**Microsoft Azure Cosmos DB, по [Xamarin University](https://university.xamarin.com/)**

Учетной записи базы данных документов Azure Cosmos DB можно подготовить с помощью подписки Azure. Каждая учетная запись базы данных может иметь ноль или несколько баз данных. Базу данных документов в Azure Cosmos DB — это логический контейнер для коллекции документов и пользователей.

База данных документов Azure Cosmos DB может содержать ноль или более коллекциями документов. Каждая коллекция документов может иметь разный уровень производительности, позволяя более высокая пропускная способность для часто используемым коллекциям и меньше пропускной способности для редко используемых коллекций.

Каждой коллекции документов состоит из нуля или более документов JSON. Документов в коллекции без схемы и поэтому не обязательно совместно использовать одинаковую структуру, а также поля. Когда документы добавляются к коллекции документов, Cosmos DB автоматически индексирует их и они становятся доступными для запроса.

В целях разработки базы данных документов также могут быть использованы через эмулятор. С помощью эмулятора, приложения могут и тестировать локально, не создавая подписку Azure и каких-либо затрат. Дополнительные сведения об эмуляторе см. в разделе [локальной разработке с помощью эмулятора Azure Cosmos DB](/azure/cosmos-db/local-emulator/).

Эта статья и прилагаемая пример приложения демонстрирует приложение списка задач, где задачи будут храниться в базе данных документов Azure Cosmos DB. Дополнительные сведения о примере приложения см. в разделе [Обзор выборки](~/xamarin-forms/data-cloud/walkthrough.md).

Дополнительные сведения об Azure Cosmos DB см. в разделе [документацию по Azure Cosmos DB](/azure/cosmos-db/).

## <a name="setup"></a>Установка

Процесс интеграции базы данных документов Azure Cosmos DB в приложении Xamarin.Forms выглядит следующим образом:

1. Создайте учетную запись Cosmos DB. Дополнительные сведения см. в разделе [создать учетную запись Azure Cosmos DB](/azure/cosmos-db/sql-api-dotnetcore-get-started#step-1-create-an-azure-cosmos-db-account).
1. Добавить [клиентская библиотека .NET Standard по Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core) пакет NuGet для платформы проектов в решение Xamarin.Forms.
1. Добавить `using` директивы для `Microsoft.Azure.Documents`, `Microsoft.Azure.Documents.Client`, и `Microsoft.Azure.Documents.Linq` пространства имен для классов, которые будут обращаться к учетной записи Cosmos DB.

После выполнения этих действий, клиентская библиотека .NET Standard для Azure Cosmos DB можно использовать для настройки и выполнения запросов к базе данных документов.

> [!NOTE]
> Клиентская библиотека .NET Standard для Azure Cosmos DB можно установить только в проектах платформы, а не в проекте переносимой библиотеки классов (PCL). Таким образом пример приложения — общий доступ проекта (SAP) чтобы избежать дублирования кода. Тем не менее `DependencyService` класс может использоваться в проекте переносимой библиотеки Классов для вызова Azure Cosmos DB .NET Standard код клиентской библиотеки, содержащихся в проекты под конкретные платформы.

## <a name="consuming-the-azure-cosmos-db-account"></a>Использование учетной записи Azure Cosmos DB

`DocumentClient` Тип инкапсулирует конечную точку, учетные данные и политику подключения, используемый для доступа к учетной записи Azure Cosmos DB и используется для настройки и выполнения запросов к учетной записи. В следующем примере кода показано, как создать экземпляр этого класса:

```csharp
DocumentClient client = new DocumentClient(new Uri(Constants.EndpointUri), Constants.PrimaryKey);
```

Uri и первичный ключ необходимы для `DocumentClient` конструктор. Их можно получить на портале Azure. Дополнительные сведения см. в разделе [подключиться к учетной записи Azure Cosmos DB](/azure/cosmos-db/sql-api-dotnetcore-get-started#Connect).

### <a name="creating-a-database"></a>Создание базы данных

База данных документов — это логический контейнер для коллекции документов и пользователей, а также можно создавать на портале Azure или программным путем, используя `DocumentClient.CreateDatabaseIfNotExistsAsync` метод:

```csharp
public async Task CreateDatabase(string databaseName)
{
  ...
  await client.CreateDatabaseIfNotExistsAsync(new Database
  {
    Id = databaseName
  });
  ...
}
```

`CreateDatabaseIfNotExistsAsync` Указывает метод `Database` объекта в качестве аргумента, с помощью `Database` объекта, указав имя базы данных, как его `Id` свойство. `CreateDatabaseIfNotExistsAsync` Метод создает базу данных, если он не существует, или возвращает базу данных в том случае, если он уже существует. Тем не менее, пример приложения игнорирует все данные, возвращаемые по `CreateDatabaseIfNotExistsAsync` метод.

> [!NOTE]
> `CreateDatabaseIfNotExistsAsync` Возвращает метод `Task<ResourceResponse<Database>>` объекта и код состояния ответа можно проверить для определения базы данных был создан или был возвращен существующей базы данных.

### <a name="creating-a-document-collection"></a>Создание коллекции документов

Коллекция документов — это контейнер для документов JSON, а также можно создавать на портале Azure или программным путем, используя `DocumentClient.CreateDocumentCollectionIfNotExistsAsync` метод:

```csharp
public async Task CreateDocumentCollection(string databaseName, string collectionName)
{
  ...
  // Create collection with 400 RU/s
  await client.CreateDocumentCollectionIfNotExistsAsync(
    UriFactory.CreateDatabaseUri(databaseName),
    new DocumentCollection
    {
      Id = collectionName
    },
    new RequestOptions
    {
      OfferThroughput = 400
    });
  ...
}
```

`CreateDocumentCollectionIfNotExistsAsync` Метод требует два принудительного аргумента — имя базы данных, согласно `Uri`и `DocumentCollection` объекта. `DocumentCollection` Представляет коллекцию документов, имя которого указано с `Id` свойство. `CreateDocumentCollectionIfNotExistsAsync` Метод создает коллекцию документов, если он не существует, или возвращает коллекцию документов, если он уже существует. Тем не менее, пример приложения игнорирует все данные, возвращаемые по `CreateDocumentCollectionIfNotExistsAsync` метод.

> [!NOTE]
> `CreateDocumentCollectionIfNotExistsAsync` Возвращает метод `Task<ResourceResponse<DocumentCollection>>` объекта и код состояния ответа можно проверить для определения была создана коллекция документов, или существующую коллекцию документ был возвращен.

При необходимости `CreateDocumentCollectionIfNotExistsAsync` также может указать метод `RequestOptions` объекта, который инкапсулирует параметры, которые могут быть указаны для запросов, выданных для учетной записи Cosmos DB. `RequestOptions.OfferThroughput` Свойство используется для определения уровня производительности коллекции документов, и в образце приложения, имеет значение 400 единиц запросов в секунду. Это значение должно быть увеличивается или уменьшается в зависимости от того, будет иметь часто и редко доступ к коллекции.

> [!IMPORTANT]
> Обратите внимание, что `CreateDocumentCollectionIfNotExistsAsync` метод создаст новую коллекцию с зарезервированной пропускной способности, которой связаны ценовые.

<a name="document_query" />

### <a name="retrieving-document-collection-documents"></a>Извлечение документа коллекции документов

Содержимое коллекции документов можно получить, создав и выполнив запрос к документу. Запрос документа создается с `DocumentClient.CreateDocumentQuery` метод:

```csharp
public async Task<List<TodoItem>> GetTodoItemsAsync()
{
  ...
  var query = client.CreateDocumentQuery<TodoItem>(collectionLink)
            .AsDocumentQuery();
  while (query.HasMoreResults)
  {
    Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
  }
  ...
}
```

Этот запрос асинхронно извлекает все документы из указанной коллекции и размещает документы в `List<TodoItem>` коллекции для отображения.

`CreateDocumentQuery<T>` Указывает метод `Uri` аргумент, который представляет коллекцию, которую следует использовать в запросах для документов. В этом примере `collectionLink` переменная является полем уровня класса, которое указывает `Uri` , представляющий коллекцию документов для извлечения документов из:

```csharp
Uri collectionLink = UriFactory.CreateDocumentCollectionUri(Constants.DatabaseName, Constants.CollectionName);
```

`CreateDocumentQuery<T>` Метод создает запрос, который выполняется синхронно и возвращает `IQueryable<T>` объекта. Тем не менее `AsDocumentQuery` метод преобразует `IQueryable<T>` объект `IDocumentQuery<T>` объект, который может выполняться асинхронно. Асинхронный запрос выполняется с `IDocumentQuery<T>.ExecuteNextAsync` метод, который возвращает следующую страницу результатов из базы данных документов с `IDocumentQuery<T>.HasMoreResults` свойство, указывающее, есть ли дополнительные результатов, возвращаемых из запроса.

Документы могут быть отфильтрованы на стороне сервера, включая `Where` предложение в запросе, который применяет предикат фильтрации в запросе к коллекции документов:

```csharp
var query = client.CreateDocumentQuery<TodoItem>(collectionLink)
          .Where(f => f.Done != true)
          .AsDocumentQuery();
```

Этот запрос извлекает все документы из коллекции, `Done` равно `false`.

<a name="inserting_document" />

### <a name="inserting-a-document-into-a-document-collection"></a>Вставка документа в коллекции документов

Содержимое JSON определяемой пользователем и документов могут быть вставлены в коллекцию документов с `DocumentClient.CreateDocumentAsync` метод:

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.CreateDocumentAsync(collectionLink, item);
  ...
}
```

`CreateDocumentAsync` Указывает метод `Uri` аргумент, который представляет документ должен быть вставлен, коллекции и `object` аргумент, представляющий документ для вставки.

### <a name="replacing-a-document-in-a-document-collection"></a>Замена документа в коллекции документов

Можно заменить документы в коллекции документов с `DocumentClient.ReplaceDocumentAsync` метод:

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, item.Id), item);
  ...
}
```

`ReplaceDocumentAsync` Указывает метод `Uri` аргумент, который представляет документ в коллекции, которую необходимо заменить, и `object` аргумент, который представляет данные, обновленный документ.

<a name="deleting_document" />

### <a name="deleting-a-document-from-a-document-collection"></a>Удаление документа из коллекции документов

Документ можно удалить из коллекции документов с `DocumentClient.DeleteDocumentAsync` метод:

```csharp
public async Task DeleteTodoItemAsync(string id)
{
  ...
  await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id));
  ...
}
```

`DeleteDocumentAsync` Указывает метод `Uri` аргумент, который представляет документ в коллекции, которые должны быть удалены.

### <a name="deleting-a-document-collection"></a>Удаление коллекции документов

Коллекция документов могут быть удалены из базы данных с `DocumentClient.DeleteDocumentCollectionAsync` метод:

```csharp
await client.DeleteDocumentCollectionAsync(collectionLink);
```

`DeleteDocumentCollectionAsync` Указывает метод `Uri` аргумент, который представляет коллекцию документов, удаляются. Обратите внимание на то, что вызов этого метода приведет к удалению документов, хранящихся в коллекции.

### <a name="deleting-a-database"></a>Удаление базы данных

Базы данных могут быть удалены из учетной записи базы данных Cosmos DB с `DocumentClient.DeleteDatabaesAsync` метод:

```csharp
await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(Constants.DatabaseName));
```

`DeleteDatabaseAsync` Указывает метод `Uri` аргумент, который представляет базу данных для удаления. Обратите внимание на то, что вызов этого метода приведет также к удалению коллекции документов, хранящихся в базе данных и документов, хранящихся в коллекции документов.

## <a name="summary"></a>Сводка

В этой статье было рассмотрено, как использовать клиентской библиотеке .NET Standard для Azure Cosmos DB для интеграции базы данных документов Azure Cosmos DB в приложении Xamarin.Forms. База данных документов Azure Cosmos DB является базой данных NoSQL, которая предоставляет высокоскоростной доступ к документам JSON, предоставляющие доступ к службе быстрый, высокой степенью доступности и базы данных для приложений, которым требуется эффективное масштабирование и глобальная репликация.


## <a name="related-links"></a>Связанные ссылки

- [TODO Azure Cosmos DB (пример)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoDocumentDB/)
- [Документация по Azure Cosmos DB](/azure/cosmos-db/)
- [Клиентская библиотека Azure Cosmos DB .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [API Azure Cosmos DB](https://docs.microsoft.com/dotnet/api/overview/azure/cosmosdb/client?view=azure-dotnet)
