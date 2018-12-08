---
title: Проверка подлинности пользователей с базой данных документов Azure Cosmos DB
description: В этой статье объясняется, как объединить управление доступом с помощью Azure Cosmos DB секционированных коллекций, таким образом, чтобы пользователь имеет доступ только к своих собственных документов в приложении Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 11ED4A4C-0F05-40B2-AB06-5A0F2188EF3D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: 1efd0fdfdc3302afaf35aacffdf30d3a84f59351
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53055677"
---
# <a name="authenticating-users-with-an-azure-cosmos-db-document-database"></a>Проверка подлинности пользователей с базой данных документов Azure Cosmos DB

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoDocumentDBAuth/)

_Базы данных документов Azure Cosmos DB поддерживает секционированные коллекции, которые могут охватывать несколько серверов и секций, поддерживая неограниченный объем хранилища и пропускной способности. В этой статье объясняется, как объединить управление доступом с секционированным коллекциям, таким образом, чтобы пользователь имеет доступ только к своих собственных документов в приложении Xamarin.Forms._

## <a name="overview"></a>Обзор

Ключ секции должен быть указан при создании секционированной коллекции и документы с одинаковым ключом секции будут храниться в одной секции. Таким образом указав удостоверение пользователя в качестве ключа секции приведет к секционированной коллекции, в которой будут храниться только документы, для этого пользователя. Это также гарантирует, что база данных документов Azure Cosmos DB масштабируется в соответствии с числом пользователей и увеличить элементов.

Должен быть предоставлен доступ к любой коллекции, и модель управления доступом SQL API определяет два типа конструкций доступа:

- **Главные ключи** предоставляют полный административный доступ ко всем ресурсам в учетной записи Cosmos DB, а также создаются при создании учетной записи Cosmos DB.
- **Маркеры ресурсов** моделируют связь между пользователем базы данных и разрешений, пользователь имеет для определенного ресурса Cosmos DB, например в коллекции или документа.

Предоставление главный ключ открывает учетную запись Cosmos DB, уязвимой для вредоносного или небрежного использования. Однако маркеры ресурсов Azure Cosmos DB предоставляет безопасный механизм для возможности для чтения, записи и удаление отдельных ресурсов на учетную запись Azure Cosmos DB в соответствии с предоставленных разрешений клиента.

Типичный подход для запросов, создания и доставки маркеры ресурсов для мобильных приложений — использовать брокер маркера ресурса. В примере ниже показан общий обзор того, как пример приложения использует брокер маркера ресурса для управления доступом к данным документа, базы данных:

![](authentication-images/documentdb-authentication.png "Процесс проверки подлинности базы данных документа")

Брокер маркера ресурса — это служба веб-API среднего уровня, размещенного в службе приложений Azure, который обрабатывает главный ключ учетной записи Cosmos DB. Пример приложения использует брокера токена ресурса для управления доступом к данным документа, базы данных следующим образом:

1. При входе в систему в приложении Xamarin.Forms обращается к службе приложений Azure, чтобы инициировать поток проверки подлинности.
1. Служба приложений Azure выполняет проверки подлинности OAuth с Facebook. После завершения потока проверки подлинности в приложении Xamarin.Forms получает маркер доступа.
1. Приложение Xamarin.Forms использует маркер доступа для запроса маркера ресурсов из брокера токена ресурса.
1. Брокер маркера ресурса использует маркер доступа для запроса удостоверение пользователя от Facebook. Удостоверение пользователя затем используется для запроса маркера ресурсов из Cosmos DB, который используется для предоставления доступа на чтение и запись секционированную коллекцию прошедшего проверку подлинности пользователя.
1. Приложение Xamarin.Forms использует маркер ресурса для прямого доступа к ресурсам Cosmos DB с разрешениями, определенными этим маркером.

> [!NOTE]
> По истечении срока действия маркера ресурса последующие документов запросов к базе данных будут получать 401 создается исключение. На этом этапе приложений Xamarin.Forms следует повторно установить удостоверение и запросить новый маркер ресурса.

Дополнительные сведения о секционировании Cosmos DB см. в разделе [секционирование и масштабирование в Azure Cosmos DB](/azure/cosmos-db/partition-data/). Дополнительные сведения о контроле доступа Cosmos DB см. в разделе [защита доступа к данным Cosmos DB](/azure/cosmos-db/secure-access-to-data/) и [управление доступом в SQL API](/rest/api/documentdb/access-control-on-documentdb-resources/).

## <a name="setup"></a>Установка

Процесс интеграции брокера токена ресурса в приложение Xamarin.Forms выглядит следующим образом:

1. Создайте учетную запись Cosmos DB, который будет использовать управление доступом. Дополнительные сведения см. в разделе [Cosmos DB конфигурации](#cosmosdb_configuration).
1. Создайте службу приложений Azure для размещения брокера токена ресурса. Дополнительные сведения см. в разделе [конфигурации службы приложений Azure](#app_service_configuration).
1. Создание приложения Facebook для проверки подлинности. Дополнительные сведения см. в разделе [конфигурацию приложения Facebook](#facebook_configuration).
1. Настройте службу приложений Azure, чтобы выполнять простые варианты проверки подлинности с помощью Facebook. Дополнительные сведения см. в разделе [конфигурации проверки подлинности службы приложений Azure](#app_service_authentication_configuration).
1. Настройка примера приложения Xamarin.Forms для взаимодействия со службой приложений Azure и Cosmos DB. Дополнительные сведения см. в разделе [конфигурации приложения Xamarin.Forms](#forms_application_configuration).

<a name="cosmosdb_configuration" />

### <a name="azure-cosmos-db-configuration"></a>Конфигурация Azure Cosmos DB

Процесс создания учетной записи Cosmos DB, который будет использовать управление доступом выглядит следующим образом:

1. Создайте учетную запись Cosmos DB. Дополнительные сведения см. в разделе [создать учетную запись Azure Cosmos DB](/azure/cosmos-db/sql-api-dotnetcore-get-started#step-1-create-an-azure-cosmos-db-account).
1. В учетной записи Cosmos DB, создать новую коллекцию с именем `UserItems`, указав ключ раздела `/userid`.

<a name="app_service_configuration" />

### <a name="azure-app-service-configuration"></a>Конфигурация службы приложений Azure

Процесс размещения брокера токена ресурса в службе приложений Azure выглядит следующим образом:

1. На портале Azure создайте новое веб-приложение службы приложений. Дополнительные сведения см. в разделе [Создание веб-приложения в среде службы приложений](/azure/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase/).
1. На портале Azure откройте колонку параметров приложения для веб-приложения и добавьте следующие параметры:
    - `accountUrl` — Это значение должно быть URL-адрес учетной записи Cosmos DB из колонки "ключи" учетной записи Cosmos DB.
    - `accountKey` — Это значение должно быть Cosmos DB главного ключа (первичный или вторичный) в колонке "ключи" учетной записи Cosmos DB.
    - `databaseId` — значение должно быть имя базы данных Cosmos DB.
    - `collectionId` — значение должно быть имя коллекции Cosmos DB (в этом случае `UserItems`).
    - `hostUrl` — Это значение должно быть URL-адрес веб-приложения в колонке обзора учетной записи службы приложение.

    Следующем снимке экрана показана эта конфигурация:

    [![](authentication-images/azure-web-app-settings.png "Параметры веб-приложения службы приложений")](authentication-images/azure-web-app-settings-large.png#lightbox "параметры веб-приложения службы приложений")

1. Публикация решения брокер маркера ресурса веб-приложения службы приложений Azure.

<a name="facebook_configuration" />

### <a name="facebook-app-configuration"></a>Конфигурация приложения Facebook

Процесс создания приложения Facebook для проверки подлинности выглядит следующим образом:

1. Создание приложения Facebook. Дополнительные сведения см. в разделе [регистрация и настройка приложения](https://developers.facebook.com/docs/apps/register) в центре разработчиков Facebook.
1. Добавление имени для входа Facebook продукта в приложение. Дополнительные сведения см. в разделе [Добавление имени для входа Facebook для приложения или веб-сайт](https://developers.facebook.com/docs/facebook-login) в центре разработчиков Facebook.
1. Настройка имени для входа Facebook следующим образом:
  - Включение имени входа клиента OAuth.
  - Включение имени входа OAuth Web.
  - Набор допустимых OAuth URI перенаправления, который URI веб-приложения службы приложений, с помощью `/.auth/login/facebook/callback` добавляется.

  Следующем снимке экрана показана эта конфигурация:

  ![](authentication-images/facebook-oauth-settings.png "Параметры входа в Facebook OAuth")

Дополнительные сведения см. в разделе [зарегистрировать приложение в Facebook](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-nameregister-aregister-your-application-with-facebook).

<a name="app_service_authentication_configuration" />

### <a name="azure-app-service-authentication-configuration"></a>Настройка проверки подлинности службы приложений Azure

В процессе настройки службы приложений, простые варианты проверки подлинности выглядит следующим образом:

1. На портале Azure перейдите к веб-приложения службы приложений.
1. На портале Azure откройте проверки подлинности / авторизации колонке и выполните следующие действия:
  - Проверка подлинности службы приложений должно быть включено.
  - Действие, выполняемое, когда запрос не прошел проверку подлинности должно быть присвоено **входа с помощью Facebook**.

  Следующем снимке экрана показана эта конфигурация:

  [![](authentication-images/app-service-authentication-settings.png "Параметры проверки подлинности для веб-приложения службы приложений")](authentication-images/app-service-authentication-settings-large.png#lightbox "параметры проверки подлинности для веб-приложения службы приложений")

Веб-приложения службы приложений также должны быть настроены для взаимодействия с приложением Facebook, чтобы включить поток проверки подлинности. Это можно сделать, выбрав поставщика удостоверений Facebook и введя **идентификатор приложения** и **секрет приложения** значения из настроек приложения Facebook в центре разработчиков Facebook. Дополнительные сведения см. в разделе [информации от Facebook, добавьте в приложение](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-namesecrets-aadd-facebook-information-to-your-application).

<a name="forms_application_configuration" />

### <a name="xamarinforms-application-configuration"></a>Конфигурация приложения Xamarin.Forms

В процессе настройки образца приложения Xamarin.Forms выглядит следующим образом:

1. Откройте решение Xamarin.Forms.
1. Откройте `Constants.cs` и обновите значения из следующих констант:
  - `EndpointUri` — Это значение должно быть URL-адрес учетной записи Cosmos DB из колонки "ключи" учетной записи Cosmos DB.
  - `DatabaseName` — значение должно быть имя базы данных документов.
  - `CollectionName` — значение должно быть имя базы данных коллекции документов (в этом случае `UserItems`).
  - `ResourceTokenBrokerUrl` — Это значение должно быть URL-адрес веб-приложения брокера токена ресурса в колонке обзора учетной записи службы приложение.

## <a name="initiating-login"></a>Инициализация имени входа

Пример приложения запускает процесс входа в систему с помощью Xamarin.Auth для перенаправления URL-адрес поставщика удостоверений, браузер, как показано в следующем примере кода:

```csharp
var auth = new Xamarin.Auth.WebRedirectAuthenticator(
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/facebook"),
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/done"));
```

В результате проверки подлинности OAuth инициировать между службой приложений Azure и Facebook, которая отображает страницу входа в Facebook:

![](authentication-images/login.png "Имя входа Facebook")

Имя входа можно отменить, нажав клавишу **отменить** кнопки на iOS или нажав **обратно** кнопку на устройстве Android, в этом случае пользователь остается без проверки подлинности и пользовательский интерфейс поставщика удостоверений удалить с экрана.

Дополнительные сведения о Xamarin.Auth, см. в разделе [проверки подлинности пользователей с помощью поставщика удостоверений](~/xamarin-forms/data-cloud/authentication/oauth.md).

## <a name="obtaining-a-resource-token"></a>Получение маркера ресурсов

После успешной проверки подлинности `WebRedirectAuthenticator.Completed` вызывает событие. В следующем примере кода демонстрируется обработка этого события:

```csharp
auth.Completed += async (sender, e) =>
{
  if (e.IsAuthenticated && e.Account.Properties.ContainsKey("token"))
  {
    var easyAuthResponseJson = JsonConvert.DeserializeObject<JObject>(e.Account.Properties["token"]);
    var easyAuthToken = easyAuthResponseJson.GetValue("authenticationToken").ToString();

    // Call the ResourceBroker to get the resource token
    using (var httpClient = new HttpClient())
    {
      httpClient.DefaultRequestHeaders.Add("x-zumo-auth", easyAuthToken);
      var response = await httpClient.GetAsync(Constants.ResourceTokenBrokerUrl + "/api/resourcetoken/");
      var jsonString = await response.Content.ReadAsStringAsync();
      var tokenJson = JsonConvert.DeserializeObject<JObject>(jsonString);
      resourceToken = tokenJson.GetValue("token").ToString();
      UserId = tokenJson.GetValue("userid").ToString();

      if (!string.IsNullOrWhiteSpace(resourceToken))
      {
        client = new DocumentClient(new Uri(Constants.EndpointUri), resourceToken);
        ...
      }
      ...
    }
  }
};
```

Результатом успешного прохождения проверки подлинности является маркер доступа, который доступен `AuthenticatorCompletedEventArgs.Account` свойство. Маркер доступа извлекаются и используются в запросе GET для брокера токена ресурса `resourcetoken` API.

`resourcetoken` API использует маркер доступа для запроса удостоверение пользователя от Facebook, который в свою очередь, используется для запроса маркера ресурсов из Cosmos DB. Если документ допустимое разрешение уже существует для пользователя в базе данных документов, он извлекается и возвращается документ JSON, содержащий маркер ресурса приложения Xamarin.Forms. Если документ допустимое разрешение не существует для пользователя, пользователя и разрешение создается в базе данных документов, и маркер ресурса извлекается из документа разрешение и возвращается в приложение Xamarin.Forms в документе JSON.

> [!NOTE]
> Пользователя базы данных документов — это ресурс, связанный с базой данных документа, и каждая база данных может содержать ноль или несколько пользователей. Разрешения на базу данных документов — это ресурс, связанный с пользователем базы данных документов, и каждый пользователь может содержать ноль или несколько разрешений. Ресурс разрешения предоставляет доступ к маркера безопасности, который требуется при попытке доступа к ресурсу, например документу.

Если `resourcetoken` API успешного завершения, он отправит код состояния HTTP 200 (ОК) в ответе, а также документ JSON, содержащий маркер ресурса. Следующие данные JSON показывает типичный успешный ответ сообщение:

```csharp
{
  "id": "John Smithpermission",
  "token": "type=resource&ver=1&sig=zx6k2zzxqktzvuzuku4b7y==;a74aukk99qtwk8v5rxfrfz7ay7zzqfkbfkremrwtaapvavw2mrvia4umbi/7iiwkrrq+buqqrzkaq4pp15y6bki1u//zf7p9x/aefbvqvq3tjjqiffurfx+vexa1xarxkkv9rbua9ypfzr47xpp5vmxuvzbekkwq6txme0xxxbjhzaxbkvzaji+iru3xqjp05amvq1r1q2k+qrarurhmjzah/ha0evixazkve2xk1zu9u/jpyf1xrwbkxqpzebvqwma+hyyaazemr6qx9uz9be==;",
  "expires": 4035948,
  "userid": "John Smith"
}
```

`WebRedirectAuthenticator.Completed` Обработчик событий считывает ответ от `resourcetoken` API и извлекает маркер ресурса и идентификатор пользователя. Маркер ресурса затем передается в качестве аргумента `DocumentClient` конструктор, который инкапсулирует конечную точку, учетные данные и политики подключения, используемой для доступа к Cosmos DB и используется для настройки и выполнения запросов к Cosmos DB. Маркер ресурса отправляется с каждым запросом для прямого доступа к ресурсу и указывает, что предоставлен доступ на чтение и запись секционированную коллекцию прошедшего проверку подлинности пользователей.

## <a name="retrieving-documents"></a>Получение документов

Получение документов, относящихся только пользователям, прошедшим проверку достигается путем создания запроса документа, который включает идентификатор пользователя в качестве ключа секции и демонстрируется в следующем примере кода:

```csharp
var query = client.CreateDocumentQuery<TodoItem>(collectionLink,
                        new FeedOptions
                        {
                          MaxItemCount = -1,
                          PartitionKey = new PartitionKey(UserId)
                        })
          .Where(item => !item.Id.Contains("permission"))
          .AsDocumentQuery();
while (query.HasMoreResults)
{
  Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
}
```

Запрос асинхронно извлекает все документы, принадлежащие авторизованного пользователя из указанной коллекции и помещает их в `List<TodoItem>` коллекции для отображения.

`CreateDocumentQuery<T>` Указывает метод `Uri` аргумент, который представляет коллекцию, которую следует использовать в запросах для документов, и `FeedOptions` объекта. `FeedOptions` Объект указывает, что неограниченное количество элементов может возвращаться запрос и идентификатор пользователя в качестве ключа секции. Это гарантирует, что в результате возвращаются только те документы, которые в секционированную коллекцию пользователя.

> [!NOTE]
> Обратите внимание, что разрешение документы, которые создаются с брокера токена ресурса, хранятся в той же коллекцией документов, что документы, созданные в приложении Xamarin.Forms. Таким образом, запрос документа содержит `Where` предложение, которое применяет предикат фильтрации в запросе к коллекции документов. Это предложение гарантирует, что разрешение документов не возвращаются из коллекции документов.

Дополнительные сведения о извлечения документов из коллекции документов, см. в разделе [извлечение документа коллекции документов](~/xamarin-forms/data-cloud/cosmosdb/consuming.md#document_query).

## <a name="inserting-documents"></a>Вставка документов

Перед вставкой документа в коллекции документов, `TodoItem.UserId` следует обновить свойство со значением, используемого в качестве ключа секции, как показано в следующем примере кода:

```csharp
item.UserId = UserId;
await client.CreateDocumentAsync(collectionLink, item);
```

Это гарантирует, что документ будет вставлен в секционированную коллекцию пользователя.

Дополнительные сведения о вставке документа в коллекции документов см. в разделе [Вставка документа в коллекции документов](~/xamarin-forms/data-cloud/cosmosdb/consuming.md#inserting_document).

## <a name="deleting-documents"></a>Удаление документов

Значение ключа секции должен быть указан при удалении документа из секционированных коллекций, как показано в следующем примере кода:

```csharp
await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id),
                 new RequestOptions
                 {
                   PartitionKey = new PartitionKey(UserId)
                 });
```

Это гарантирует, что Cosmos DB знает, что секционированные коллекции для удаления из документа.

Дополнительные сведения об удалении документа из коллекции документов, см. в разделе [удаление документа из коллекции документов](~/xamarin-forms/data-cloud/cosmosdb/consuming.md#deleting_document).

## <a name="summary"></a>Сводка

В этой статье было рассмотрено, как объединить управление доступом с секционированным коллекциям, таким образом, чтобы пользователь имеет доступ только к своими собственными документами базы данных документа в приложении Xamarin.Forms. Указав удостоверение пользователя в качестве ключа секции гарантирует, что секционированной коллекции только для хранения документов, для этого пользователя.


## <a name="related-links"></a>Связанные ссылки

- [TODO Azure Cosmos DB Auth (пример)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoDocumentDBAuth/)
- [Использование базы данных документов Azure Cosmos DB](~/xamarin-forms/data-cloud/cosmosdb/consuming.md)
- [Защита доступа к данным Azure Cosmos DB](/azure/cosmos-db/secure-access-to-data/)
- [Управление доступом в SQL API](/rest/api/documentdb/access-control-on-documentdb-resources/).
- [Секционирование и масштабирование в Azure Cosmos DB](/azure/cosmos-db/partition-data/)
- [Клиентская библиотека Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [API Azure Cosmos DB](https://msdn.microsoft.com/library/azure/dn948556.aspx)
