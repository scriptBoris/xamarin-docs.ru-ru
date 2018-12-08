---
title: Поиск данных с помощью поиска Azure
description: В этой статье показано, как использовать библиотеку поиска Microsoft Azure для интеграции службы поиска Azure в приложения Xamarin.Forms.
ms.prod: xamarin
ms.assetid: A4AEF233-3672-4174-9DBA-15BEE3030C0B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/05/2016
ms.openlocfilehash: 81e6cbb39a522a471e739e7e9bbb8a0f451a38cd
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53052907"
---
# <a name="searching-data-with-azure-search"></a>Поиск данных с помощью поиска Azure

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureSearch/)

_Поиск Azure — облачная служба, которая предоставляет возможности для отправляемых данных запросов и индексирования. Это устраняет требования к инфраструктуре сложности алгоритма поиска, традиционно ассоциирующиеся с реализацией функции поиска в приложении. В этой статье показано, как использовать библиотеку поиска Microsoft Azure для интеграции службы поиска Azure в приложения Xamarin.Forms._

## <a name="overview"></a>Обзор

Данные хранятся в службе поиска Azure, как индексы и документы. *Индекс* — это хранилище данных, в котором можно выполнять поиск в службе поиска Azure и принципиально подобен классу таблицу базы данных. Объект *документа* представляет собой единое средство для поиска данных в индексе и принципиально подобен классу строку базы данных. При отправке документов и отправке поисковых запросов в службу поиска Azure, запросы выполняются по указанному индексу в службе поиска.

Каждый запрос к службе поиска Azure необходимо указать имя службы и ключ API. Существует два типа ключ API.

- *Ключи администратора* предоставить полный доступ ко всем операциям. Это включает в себя управление службой, создание и удаление индексов и источников данных.
- *Ключи запросов* предоставить доступ только для чтения к индексам и документам и должен использоваться приложениями, которые создают запросы на поиск.

Для выполнения запроса является наиболее распространенных запрос в службу поиска Azure. Существует два типа запросов, который может быть передан.

- Объект *поиска* запрос ищет один или несколько элементов во всех полях, доступных для поиска в индексе. Поисковые запросы создаются с помощью упрощенного синтаксиса или синтаксис запросов Lucene. Дополнительные сведения см. в разделе [простой синтаксис запросов в службе поиска Azure](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search/), и [синтаксис запросов Lucene в службе поиска Azure](/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search/).
- Объект *фильтра* запросов оценивает логическое выражение во всех фильтруемым полям в индексе. Фильтр запросов создаются с использованием подмножества языка фильтра OData. Дополнительные сведения см. в разделе [синтаксис выражений OData для службы поиска Azure](/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search/).

Поисковые запросы и запросы фильтра можно использовать отдельно или совместно. При совместном использовании фильтра запроса сначала применяется ко всему индексу, а затем выполняется запрос поиска по результатам запроса фильтра.

Поиск Azure также поддерживает получение предложений, на основе входных данных для поиска. Дополнительные сведения см. в разделе [предложений запросов](#suggestions).

## <a name="setup"></a>Установка

Процесс интеграции поиска Azure в приложения Xamarin.Forms выглядит следующим образом:

1. Создание службы поиска Azure. Дополнительные сведения см. в разделе [Создание службы поиска Azure с помощью портала Azure](/azure/search/search-create-service-portal/).
1. Удалите Silverlight как целевой платформы из решения Xamarin.Forms переносимой библиотеки классов (PCL). Это можно сделать, изменив профиль PCL любому профилю, который поддерживает кросс платформенной разработки, но не поддерживает Silverlight, например профиль 151 или 92.
1. Добавить [библиотеке поиска](https://www.nuget.org/packages/Microsoft.Azure.Search) пакет NuGet в проект переносимой библиотеки Классов в решение Xamarin.Forms.

После выполнения этих действий, можно использовать API поиска библиотеки Microsoft для управления поиска индексы и источники данных, отправка и управление документами и выполнения запросов.

## <a name="creating-the-azure-search-index"></a>Создание индекса поиска Azure

Схема индекса должен быть определен, соответствующий структуре данных для поиска. Это может быть реализовано на портале Azure или программным путем, используя `SearchServiceClient` класса. Этот класс управляет подключениями к службе поиска Azure и может использоваться для создания индекса. В следующем примере кода показано, как создать экземпляр этого класса:

```csharp
var searchClient =
  new SearchServiceClient(Constants.SearchServiceName, new SearchCredentials(Constants.AdminApiKey));
```

`SearchServiceClient` Перегрузку конструктора принимает имя службы поиска и `SearchCredentials` объект в качестве аргументов, с `SearchCredentials` упаковки объект *ключ администратора* для службы поиска Azure. *Ключ администратора* необходима для создания индекса.

> [!NOTE]
>  Один `SearchServiceClient` экземпляр должен использоваться в приложении во избежание открытия слишком много подключений к службе поиска Azure.

Индекс определяется `Index` объекта, как показано в следующем примере кода:

```csharp
static void CreateSearchIndex()
{
  var index = new Index()
  {
    Name = Constants.Index,
    Fields = new[]
    {
      new Field("id", DataType.String) { IsKey = true, IsRetrievable = true },
      new Field("name", DataType.String) { IsRetrievable = true, IsFilterable = true, IsSortable = true, IsSearchable = true },
      new Field("location", DataType.String) { IsRetrievable = true, IsFilterable = true, IsSortable = true, IsSearchable = true },
      new Field("details", DataType.String) { IsRetrievable = true, IsFilterable = true, IsSearchable = true },
      new Field("imageUrl", DataType.String) { IsRetrievable = true }
    },
    Suggesters = new[]
    {
      new Suggester("nameSuggester", SuggesterSearchMode.AnalyzingInfixMatching, new[] { "name" })
    }
  };

  searchClient.Indexes.Create(index);
}
```

`Index.Name` Свойство должно быть присвоено имя индекса и `Index.Fields` свойство должно быть установлено на массив `Field` объектов. Каждый `Field` экземпляр указывает имя, тип и любые свойства, которые определяют, как используется поле. Эти свойства включают:

- `IsKey` — Указывает, является ли поле ключа индекса. Только одно поле в индексе, типа `DataType.String`, необходимо назначить в качестве ключевого поля.
- `IsFacetable` — Указывает, является ли можно выполнять фасетной навигации для этого поля. Значение по умолчанию — `false`.
- `IsFilterable` — Указывает, может ли поле использоваться в запросах фильтра. Значение по умолчанию — `false`.
- `IsRetrievable` — Указывает, может ли поле быть извлечен в результатах поиска. Значение по умолчанию — `true`.
- `IsSearchable` — Указывает, включены ли поле в полнотекстовом поиске. Значение по умолчанию — `false`.
- `IsSortable` — Указывает, может ли поле использоваться в `OrderBy` выражения. Значение по умолчанию — `false`.

> [!NOTE]
> Изменение индекса после его развертывания включает в себя перестройку и повторную загрузку данных.

`Index` Объекта можно указать `Suggesters` свойство, которое определяет поля в индексе, который будет использоваться для поддержки автозаполнения или предложений запросов поиска. `Suggesters` Свойство должно быть установлено на массив `Suggester` объектами, которые определяют поля, которые используются для построения поиска результаты предложения.

После создания `Index` объект, индекс создается путем вызова `Indexes.Create` на `SearchServiceClient` экземпляра.

> [!NOTE]
> Если создание индекса из приложения, который должен быть продолжает работать, используйте `Indexes.CreateAsync` метод.

Дополнительные сведения см. в разделе [создание индекса службы поиска Azure с помощью пакета SDK для .NET](/azure/search/search-create-index-dotnet/).

## <a name="deleting-the-azure-search-index"></a>Удаление индекса поиска Azure

Индекс можно удалить, вызвав `Indexes.Delete` на `SearchServiceClient` экземпляр:

```csharp
searchClient.Indexes.Delete(Constants.Index);
```

## <a name="uploading-data-to-the-azure-search-index"></a>Передача данных в индекс службы поиска Azure

Определив индекс, данные можно отправить его с помощью одного из двух моделей:

- **Модель опроса** — данные периодически принимаются из Azure Cosmos DB, базы данных SQL Azure, хранилище BLOB-объектов Azure или SQL Server, размещенных на виртуальной машине Azure.
- **Принудительная модель** — программно данные отправляются в индекс. Это модель, применяемые в данной статье.

Объект `SearchIndexClient` для импорта данных в индекс должен будет создан экземпляр. Это можно сделать, вызвав `SearchServiceClient.Indexes.GetClient` метод, как показано в следующем примере кода:

```csharp
static void UploadDataToSearchIndex()
{
  var indexClient = searchClient.Indexes.GetClient(Constants.Index);

  var monkeyList = MonkeyData.Monkeys.Select(m => new
  {
    id = Guid.NewGuid().ToString(),
    name = m.Name,
    location = m.Location,
    details = m.Details,
    imageUrl = m.ImageUrl
  });

  var batch = IndexBatch.New(monkeyList.Select(IndexAction.Upload));
  try
  {
    indexClient.Documents.Index(batch);
  }
  catch (IndexBatchException ex)
  {
    // Sometimes when the Search service is under load, indexing will fail for some
    // documents in the batch. Compensating actions like delaying and retrying should be taken.
    // Here, the failed document keys are logged.
    Console.WriteLine("Failed to index some documents: {0}",
      string.Join(", ", ex.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
  }
}
```

Данные для импорта в индекс входит в состав `IndexBatch` объект, который инкапсулирует коллекцию `IndexAction` объектов. Каждый `IndexAction` экземпляр содержит документ и свойство, определяющее, какое действие следует выполнить в документе службы поиска Azure. В примере выше `IndexAction.Upload` действие указано, что приведет к появлению, вставляемого в индекс, если он новый, документ или заменен, если он уже существует. `IndexBatch` Объект затем передается в индекс с помощью `Documents.Index` метод `SearchIndexClient` объекта. Сведения о других действиях индексирования, см. в разделе [Выбор операций индексирования для использования](/azure/search/search-import-data-dotnet#ii-decide-which-indexing-action-to-use).

> [!NOTE]
> Только 1000 документов могут быть включены в один запрос на индексирование.

Обратите внимание, что в примере выше, `monkeyList` коллекции создается как анонимный объект из коллекции `Monkey` объектов. Это создает данные для `id` поле и разрешает сопоставление регистра Pascal `Monkey` имена свойств в стиль Camel найти имена полей индекса. Кроме того, это сопоставление можно также выполнить путем добавления `[SerializePropertyNamesAsCamelCase]` атрибут `Monkey` класса.

Дополнительные сведения см. в разделе [отправка данных для поиска Azure с помощью пакета SDK для .NET](/azure/search/search-import-data-dotnet/).

## <a name="querying-the-azure-search-index"></a>Запросы к индексу поиска Azure

Объект `SearchIndexClient` необходимо создать экземпляр для запроса индекса. Когда приложение выполняет запросы, рекомендуется следовать принципу предоставления минимальных прав и создайте `SearchIndexClient` напрямую, передав *ключ запроса* как аргумент. Это гарантирует, что пользователи имеют доступ только для чтения к индексам и документам. Этот подход демонстрируется в следующем примере кода:

```csharp
SearchIndexClient indexClient =
  new SearchIndexClient(Constants.SearchServiceName, Constants.Index, new SearchCredentials(Constants.QueryApiKey));
```

`SearchIndexClient` Перегрузку конструктора принимает имя службы поиска, имя индекса и `SearchCredentials` объект в качестве аргументов, с `SearchCredentials` упаковки объект *ключ запроса* для службы поиска Azure.

### <a name="search-queries"></a>Поисковые запросы

Индекс можно запрашивать путем вызова `Documents.SearchAsync` метод `SearchIndexClient` экземпляра, как показано в следующем примере кода:

```csharp
async Task AzureSearch(string text)
{
  Monkeys.Clear();

  var searchResults = await indexClient.Documents.SearchAsync<Monkey>(text);
  foreach (SearchResult<Monkey> result in searchResults.Results)
  {
    Monkeys.Add(new Monkey
    {
      Name = result.Document.Name,
      Location = result.Document.Location,
      Details = result.Document.Details,
      ImageUrl = result.Document.ImageUrl
    });
  }
}
```

`SearchAsync` Метод принимает аргумент текст поиска и необязательного `SearchParameters` объект, который может использоваться для дальнейшего уточнения запроса. Запрос поиска указывается как аргумент текст поиска, хотя запрос фильтра можно задать, присвоив `Filter` свойство `SearchParameters` аргумент. В следующем примере кода показано, что оба типы запросов:

```csharp
var parameters = new SearchParameters
{
  Filter = "location ne 'China' and location ne 'Vietnam'"
};
var searchResults = await indexClient.Documents.SearchAsync<Monkey>(text, parameters);
```

Этот запрос фильтр применяется ко всему индексу и удаляет документы в результатах где `location` поле не равно Китая и не равно Вьетнам. После фильтрации, поисковый запрос выполняется на результаты запроса фильтра.

> [!NOTE]
> Чтобы отфильтровать без поиска, передайте `*` в качестве аргумента поиска текста.

`SearchAsync` Возвращает метод `DocumentSearchResult` , содержащий результаты запроса. Этот объект перечисляется, с каждым `Document` создаваемого в качестве объекта `Monkey` объекта и добавляемый `Monkeys` `ObservableCollection` для отображения. В следующем снимки экрана Показать результаты поиска возвращаются из службы поиска Azure:

![](azure-search-images/search.png "Результаты поиска")

Дополнительные сведения о поиске и фильтрации см. в разделе [Отправка запросов в индекс поиска Azure с помощью пакета SDK для .NET](/azure/search/search-query-dotnet/).

<a name="suggestions" />

### <a name="suggestion-queries"></a>Запросы предложений

Поиск Azure позволяет предложения запроса на основе запроса поиска, путем вызова `Documents.SuggestAsync` метод `SearchIndexClient` экземпляра. Это показано в следующем примере кода:

```csharp
async Task AzureSuggestions(string text)
{
  Suggestions.Clear();

  var parameters = new SuggestParameters()
  {
    UseFuzzyMatching = true,
    HighlightPreTag = "[",
    HighlightPostTag = "]",
    MinimumCoverage = 100,
    Top = 10
  };

  var suggestionResults =
    await indexClient.Documents.SuggestAsync<Monkey>(text, "nameSuggester", parameters);

  foreach (var result in suggestionResults.Results)
  {
    Suggestions.Add(new Monkey
    {
      Name = result.Text,
      Location = result.Document.Location,
      Details = result.Document.Details,
      ImageUrl = result.Document.ImageUrl
    });
  }
}
```

`SuggestAsync` Метод принимает аргумент текст поиска — имя средства подбора для использования (который определен в индекс), и, при необходимости `SuggestParameters` объект, который может использоваться для дальнейшего уточнения запроса. `SuggestParameters` Экземпляра задает следующие свойства:

- `UseFuzzyMatching` — Если задано значение `true`, службы поиска Azure будет находить предложения, даже если замененного или отсутствующего символа в тексте для поиска.
- `HighlightPreTag` — тег, который добавляется в предложение попаданий.
- `HighlightPostTag` — тег, который добавляется в предложение попаданий.
- `MinimumCoverage` — представляет процент индекса, который должен составлять запрос предложений, запрос может быть сообщила об успешном завершении. Значение по умолчанию — 80.
- `Top` — Количество извлекаемых предложений. Он должен быть целым числом от 1 до 100, со значением по умолчанию 5.

Общий эффект — 10 лучших результатов из индекса, которые будут возвращаться с, выделение совпадений и результаты будут включать документы, содержащие аналогично написания условия поиска.

`SuggestAsync` Возвращает метод `DocumentSuggestResult` , содержащий результаты запроса. Этот объект перечисляется, с каждым `Document` создаваемого в качестве объекта `Monkey` объекта и добавляемый `Monkeys` `ObservableCollection` для отображения. На следующих снимках экрана показано предложение результатов, возвращаемых из поиска Azure:

![](azure-search-images/suggest.png "Результаты предложения")

Обратите внимание, что в приложении-примере `SuggestAsync` метод вызывается только в том случае, когда пользователь заканчивает, введя условие поиска. Тем не менее он может также использоваться для поддержки автозаполнения поисковых запросов, выполнив на каждой keypress.

## <a name="summary"></a>Сводка

В этой статье было показано, как использовать библиотеку поиска Microsoft Azure для интеграции службы поиска Azure в приложения Xamarin.Forms. Поиск Azure — облачная служба, которая предоставляет возможности для отправляемых данных запросов и индексирования. Это устраняет требования к инфраструктуре сложности алгоритма поиска, традиционно ассоциирующиеся с реализацией функции поиска в приложении.


## <a name="related-links"></a>Связанные ссылки

- [Служба поиска Azure (пример)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureSearch/)
- [Документация по поиску Azure](/azure/search/)
- [Библиотека поиска Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Search/)
