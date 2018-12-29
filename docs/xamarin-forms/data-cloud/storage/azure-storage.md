---
title: Хранение и доступ к данным в службе хранилища Azure
description: Хранилище Azure — это масштабируемая облачная система хранения, которая может использоваться для хранения структурированных и неструктурированных данных. В этой статье объясняется, как с помощью Xamarin.Forms сохранять текст и двоичные данные в хранилище Azure и способах доступа к данным.
ms.prod: xamarin
ms.assetid: 5B10D37B-839B-4CD0-9C65-91014A93F3EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/28/2018
ms.openlocfilehash: 4ecffa0902d186b659e7df07dbcf17053e29c818
ms.sourcegitcommit: f890b5ec9b7c2702875070859e1a8cbf6e870e46
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/28/2018
ms.locfileid: "53814015"
---
# <a name="storing-and-accessing-data-in-azure-storage"></a>Хранение и доступ к данным в службе хранилища Azure

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureStorage/)

_Хранилище Azure — это масштабируемая облачная система хранения, которая может использоваться для хранения структурированных и неструктурированных данных. В этой статье показано, как с помощью Xamarin.Forms сохранять текст и двоичные данные в хранилище Azure и способах доступа к данным._

Служба хранилища Azure предоставляет четыре службы хранения:

- Хранилище BLOB-объектов. Большой двоичный объект может быть текст или двоичные данные, такие как резервные копии виртуальных машин, файлов мультимедиа и документы.
- Хранилище таблиц — это хранилище ключей атрибутов NoSQL.
- Хранилище очередей — службу обмена сообщениями для обработки рабочего процесса и обмен данными между облачными службами.
- Хранилище файлов предоставляет общее хранилище, с помощью протокола SMB.

Существует два типа учетных записей хранения:

- Учетные записи хранения общего назначения предоставляет доступ к службам хранилища Azure из одной учетной записи.
- Учетную запись хранения BLOB-объектов — специализированные записи хранения для хранения больших двоичных объектов. Этот тип учетной записи рекомендуется в том случае, если вам требуется только для хранения данных больших двоичных объектов.

Эта статья и прилагаемая пример приложения демонстрирует отправки файлов в хранилище BLOB-объектов, а также их скачивании изображение и текст. Кроме того он также демонстрирует получение списка файлов из хранилища BLOB-объектов и удаление файлов.

Дополнительные сведения о службе хранилища Azure см. в разделе [введение в хранилище](https://azure.microsoft.com/documentation/articles/storage-introduction/).

## <a name="introduction-to-blob-storage"></a>Введение в хранилище BLOB-объектов

Хранилище BLOB-объектов состоит из трех компонентов, которые показаны на следующей схеме:

![](azure-storage-images/blob-storage.png "Концепции хранилища больших двоичных объектов")

Весь доступ к хранилищу Azure осуществляется через учетную запись хранения. Учетная запись хранения может содержать неограниченное количество контейнеров, а контейнер может хранить неограниченное количество больших двоичных объектов, пределах емкости учетной записи хранения.

Большой двоичный объект — это файл любого типа и размера. Служба хранилища Azure поддерживает три типа другой BLOB-объекта:

- Блочные BLOB-объекты оптимизированы для потоковой передачи и хранения облачных объектов и хорошо подходят для хранения резервных копий, файлы мультимедиа, документы и т.д. Блочные BLOB-объекты могут быть размером до 195 ГБ.
- Добавить BLOB-объекты аналогичны блочных BLOB-объектов, но оптимизированы для операций, таких как ведение журнала добавления. Добавление больших двоичных объектов может быть размером до 195 ГБ.
- Страничных BLOB-объектов, оптимизированных для операций часто чтения и записи, обычно используются для хранения виртуальных машин и их дисков. Страничных BLOB-объектов может быть размером до 1 ТБ.

> [!NOTE]
> Обратите внимание, что учетные записи хранения BLOB-объектов поддерживают блочные и добавочные BLOB-объектов, но не страничных BLOB-объектов.

Большой двоичный объект отправляется в службу хранилища Azure и загружаются из службы хранилища Azure, как поток байтов. Таким образом файлы необходимо преобразовать в поток байт до передачи и преобразованный обратно в их исходное представление после загрузки.

Каждый объект, который хранится в службе хранилища Azure имеет уникальный URL-адрес. Имя учетной записи хранения образует поддомен этого адреса и сочетание поддомена и доменное имя формы *конечной точки* учетной записи хранения. Например, если имя вашей учетной записи хранения *mystorageaccount*, конечная точка BLOB-объектов по умолчанию для учетной записи хранения является `https://mystorageaccount.blob.core.windows.net`.

URL-адрес для доступа к объекту в учетной записи хранения строится путем добавления местоположения объекта в учетной записи хранения к конечной точке. Например, адрес большого двоичного объекта будет иметь формат `https://mystorageaccount.blob.core.windows.net/mycontainer/myblob`.

## <a name="setup"></a>Установка

Интеграция учетной записи хранения Azure в приложения Xamarin.Forms процесс выглядит следующим образом:

1. Создайте учетную запись хранения. Дополнительные сведения см. в разделе [создать учетную запись хранилища](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account).
1. Добавить [клиентская библиотека хранилища Azure](https://www.nuget.org/packages/WindowsAzure.Storage/) в приложение Xamarin.Forms.
1. Настройка строки подключения к хранилищу. Дополнительные сведения см. в разделе [подключение к службе хранилища Azure](#connecting).
1. Добавить `using` директивы для `Microsoft.WindowsAzure.Storage` и `Microsoft.WindowsAzure.Storage.Blob` пространства имен для классов, которые будут обращаться к службе хранилища Azure.

<a name="connecting" />

## <a name="connecting-to-azure-storage"></a>Подключение к службе хранилища Azure

Каждый запрос к ресурсам учетной записи хранения необходимо пройти проверку подлинности. Хотя большие двоичные объекты можно настроить для поддержки анонимной проверки подлинности, существует два основных подхода, которые приложение может использовать для проверки подлинности с учетной записью хранения:

- Общий ключ. Этот подход использует службу хранилища Azure учетной записи имя и ключ учетной записи доступ к службам хранилища. Учетной записи хранения присваивается два закрытых ключей при создании, который может использоваться для проверки подлинности общего ключа.
- Подписанные URL-адреса. Это маркер, который можно добавить к URL-адрес, который разрешает делегированный доступ к ресурсу хранилища, с разрешениями, оно указывает, в течение периода времени, это значение.

Строки подключения можно указать, включают сведения о проверке подлинности, необходимые для доступа к ресурсам хранилища Azure из приложения. Кроме того можно настроить строку подключения для подключения к эмулятору хранилища Azure из Visual Studio.

> [!NOTE]
> Хранилище Azure поддерживает HTTP и HTTPS в строке подключения. Тем не менее рекомендуется использовать HTTPS.

### <a name="connecting-to-the-azure-storage-emulator"></a>Подключение к эмулятору хранилища Azure

Эмулятор хранения Azure предоставляет локальную среду, эмулирующую BLOB-объектов Azure, служб очередей и таблиц для целей разработки.

Следующая строка подключения должна использоваться для подключения к эмулятору хранилища Azure:

```csharp
UseDevelopmentStorage=true
```

Дополнительные сведения об эмуляторе хранения Azure см. в разделе [использование эмулятора хранилища Azure для разработки и тестирования](https://azure.microsoft.com/documentation/articles/storage-use-emulator/).

### <a name="connecting-to-azure-storage-using-a-shared-key"></a>Подключение к службе хранилища Azure с помощью общего ключа

Следует использовать следующий формат строки подключения для подключения к хранилищу Azure с помощью общего ключа:

```csharp
DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey
```

`myAccountName` необходимо заменить имя учетной записи хранения и `myAccountKey` следует заменить на один из двух ключей доступа учетной записи.

> [!NOTE]
> При использовании общего ключа проверки подлинности, имя учетной записи и ключ учетной записи будет распространяться для каждого, кто использует ваши приложения, который будет предоставлять доступ на чтение и запись в учетную запись хранения. Таким образом используется проверка подлинности shared key исключительно для тестирования и никогда не распространяйте ключи другим пользователям.

### <a name="connecting-to-azure-storage-using-a-shared-access-signature"></a>Подключение к службе хранилища Azure с помощью подписи общего доступа

Следует использовать следующий формат строки подключения для подключения к хранилищу Azure с помощью подписанного URL-адреса:

`BlobEndpoint=myBlobEndpoint;SharedAccessSignature=mySharedAccessSignature`

`myBlobEndpoint` следует заменить на URL-адрес конечной точки большого двоичного объекта, и `mySharedAccessSignature` следует заменить на SAS. SAS предоставляет протокол, конечную точку службы и учетные данные для доступа к ресурсу.

> [!NOTE]
> Для производственных приложений рекомендуется использовать проверку подлинности SAS. Тем не менее в рабочем приложении подписанный URL-адрес должен быть извлечен из серверной части службы по запросу, а не, упакованном с приложением.

Дополнительные сведения о подписях общего доступа см. в разделе [использование подписи (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).

## <a name="creating-a-container"></a>Создание контейнера

`GetContainer` Метод позволяет получить ссылку на именованный контейнер, который затем используется для получения больших двоичных объектов из контейнера или добавить в контейнер больших двоичных объектов. В следующем коде показано в примере `GetContainer` метод:

```csharp
static CloudBlobContainer GetContainer(ContainerType containerType)
{
  var account = CloudStorageAccount.Parse(Constants.StorageConnection);
  var client = account.CreateCloudBlobClient();
  return client.GetContainerReference(containerType.ToString().ToLower());
}
```

`CloudStorageAccount.Parse` Метод анализирует строку подключения и возвращает `CloudStorageAccount` экземпляр, который представляет учетную запись хранения. Объект `CloudBlobClient` затем создается экземпляр, который используется для получения контейнеров и больших двоичных объектов, `CreateCloudBlobClient` метод. `GetContainerReference` Метод получает указанный контейнер как `CloudBlobContainer` экземпляра, прежде чем он возвращается вызывающему методу. В этом примере имя контейнера — `ContainerType` значение перечисления, преобразованное в строку в нижнем регистре.

> [!NOTE]
> Имена контейнеров должны быть строчными и должно начинаться с буквы или цифры. Кроме того они могут содержать только буквы, цифры и дефисы и должно находиться в диапазоне от 3 до 63 символов.

`GetContainer` Метод вызывается следующим образом:

```csharp
var container = GetContainer(containerType);
```

`CloudBlobContainer` Экземпляр может затем использоваться для создания контейнера, если он еще не существует:

```csharp
await container.CreateIfNotExistsAsync();
```

По умолчанию только что созданный контейнер является закрытым. Это означает, что ключ доступа к хранилищу должно быть указано для получения больших двоичных объектов из контейнера. Сведения о том, как большие двоичные объекты в открытый контейнер, см. в разделе [создать контейнер](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#create-a-container).

## <a name="uploading-data-to-a-container"></a>При отправке данных

`UploadFileAsync` Метод используется для передачи потока байтов данных в хранилище BLOB-объектов и показано в следующем примере кода:

```csharp
public static async Task<string> UploadFileAsync(ContainerType containerType, Stream stream)
{
  var container = GetContainer(containerType);
  await container.CreateIfNotExistsAsync();

  var name = Guid.NewGuid().ToString();
  var fileBlob = container.GetBlockBlobReference(name);
  await fileBlob.UploadFromStreamAsync(stream);

  return name;
}
```

После получения ссылки на контейнер, метод создает контейнер, если он еще не существует. Новый `Guid` создается в качестве имени уникальный BLOB-объектов, и ссылку на большой двоичный объект блока извлекается в виде `CloudBlockBlob` экземпляра. Поток данных передается в большой двоичный объект с помощью `UploadFromStreamAsync` метод, который создает большой двоичный объект, если он еще не существует, или заменяет его, если он существует.

Прежде чем файл можно отправить с помощью этого метода хранилище BLOB-объектов, оно сначала должно быть преобразовано в поток байтов. Это показано в следующем примере кода:

```csharp
var byteData = Encoding.UTF8.GetBytes(text);
uploadedFilename = await AzureStorage.UploadFileAsync(ContainerType.Text, new MemoryStream(byteData));
```

`text` Данных преобразуется в массив байтов, который затем помещается за поток, который передается `UploadFileAsync` метод.

## <a name="downloading-data-from-a-container"></a>Загрузка данных из контейнера

`GetFileAsync` Метод используется для загрузки данных большого двоичного объекта из хранилища Azure и показано в следующем примере кода:

```csharp
public static async Task<byte[]> GetFileAsync(ContainerType containerType, string name)
{
  var container = GetContainer(containerType);

  var blob = container.GetBlobReference(name);
  if (await blob.ExistsAsync())
  {
    await blob.FetchAttributesAsync();
    byte[] blobBytes = new byte[blob.Properties.Length];

    await blob.DownloadToByteArrayAsync(blobBytes, 0);
    return blobBytes;
  }
  return null;
}
```

После получения ссылки на контейнер, этот метод извлекает ссылку на BLOB-объектов для сохраненных данных. Если существует большой двоичный объект, его свойства извлекаются путем `FetchAttributesAsync` метод. Создается массив байтов данных о правильных размерах и большой двоичный объект загружается как массив байтов, возвращаемый вызывающему методу.

После загрузки байтовые данные больших двоичных объектов, оно должно быть преобразовано в исходное представление. Это показано в следующем примере кода:

```csharp
var byteData = await AzureStorage.GetFileAsync(ContainerType.Text, uploadedFilename);
string text = Encoding.UTF8.GetString(byteData);
```

Массив байтов, извлекается из хранилища Azure по `GetFileAsync` строка в кодировке метод, прежде чем оно преобразуется обратно в UTF8.

## <a name="listing-data-in-a-container"></a>Список данных в контейнере

`GetFilesListAsync` Метод используется для получения списка больших двоичных объектов, хранящиеся в контейнере и показано в следующем примере кода:

```csharp
public static async Task<IList<string>> GetFilesListAsync(ContainerType containerType)
{
  var container = GetContainer(containerType);

  var allBlobsList = new List<string>();
  BlobContinuationToken token = null;

  do
  {
    var result = await container.ListBlobsSegmentedAsync(token);
    if (result.Results.Count() > 0)
    {
      var blobs = result.Results.Cast<CloudBlockBlob>().Select(b => b.Name);
      allBlobsList.AddRange(blobs);
    }
    token = result.ContinuationToken;
  } while (token != null);

  return allBlobsList;
}
```

После получения ссылки на контейнер, данный метод использует контейнера `ListBlobsSegmentedAsync` метод для извлечения ссылки на большие двоичные объекты в контейнере. Результаты, возвращенные `ListBlobsSegmentedAsync` метод перечисляются хотя `BlobContinuationToken` экземпляр не `null`. Каждый большой двоичный объект преобразовывается из возвращаемого `IListBlobItem` для `CloudBlockBlob` в порядке access `Name` свойство большого двоичного объекта, прежде чем это значение добавляется к `allBlobsList` коллекции. Один раз `BlobContinuationToken` экземпляр `null`вернул последнее имя BLOB-объектов и выполнения выходит из цикла.

## <a name="deleting-data-from-a-container"></a>При удалении данных из контейнера

`DeleteFileAsync` Метод используется для удаления большого двоичного объекта из контейнера и показано в следующем примере кода:

```csharp
public static async Task<bool> DeleteFileAsync(ContainerType containerType, string name)
{
  var container = GetContainer(containerType);
  var blob = container.GetBlobReference(name);
  return await blob.DeleteIfExistsAsync();
}
```

После получения ссылки на контейнер, метод возвращает ссылку на BLOB-объектов для указанного большого двоичного объекта. Большой двоичный объект удаляется с `DeleteIfExistsAsync` метод.

## <a name="related-links"></a>Связанные ссылки

- [Служба хранилища Azure (пример)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureStorage/)
- [Общие сведения о хранилище](https://azure.microsoft.com/documentation/articles/storage-introduction/)
- [Использование хранилища BLOB-объектов из Xamarin](https://azure.microsoft.com/documentation/articles/storage-xamarin-blob-storage/)
- [С помощью подписи коллективного доступа (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)
- [Windows Azure хранилища (NuGet)](https://www.nuget.org/packages/WindowsAzure.Storage/)
