---
title: Использование веб-службы Windows Communication Foundation (WCF)
description: В этой статье показано, как использовать службу WCF Simple Object Access Protocol (SOAP) из приложения Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 5696FF04-EF21-4B7A-8C8B-26DE28B5C0AD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 7e8acc6e8aaf8b8e0e8cec7d5d0f3e28cf60073a
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53055599"
---
# <a name="consuming-a-windows-communication-foundation-wcf-web-service"></a>Использование веб-службы Windows Communication Foundation (WCF)

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoWCF/)

_WCF — это унифицированная платформа корпорации Microsoft для построения сервисноориентированных приложений. Он позволяет разработчикам создавать безопасные, надежные, с поддержкой транзакций с возможностью взаимодействия распределенных приложений. В этой статье показано, как использовать службу WCF Simple Object Access Protocol (SOAP) из приложения Xamarin.Forms._

WCF описывает службу, с различными разные контракты, в том числе следующие:

- **Контракты данных** — определение структуры данных, которые формируют основу для содержимого сообщения.
- **Контракты сообщений** — создания сообщений из контракты данных.
- **Контракты сбоя** — разрешить пользовательских ошибок SOAP указать.
- **Контракты службы** — определить операции, которые поддерживают службы и сообщения, необходимые для взаимодействия с каждой операцией. Они также определяют любое поведение пользовательской ошибки, которое может быть связан с операциями с каждой службы.

Существуют различия между ASP.NET Web Services (ASMX) и WCF, но важно понимать, что WCF поддерживает те же возможности, которые предоставляет ASMX-сообщений SOAP по протоколу HTTP. Дополнительные сведения об использовании службы ASMX, см. в разделе [использование ASP.NET Web Services (ASMX)](~/xamarin-forms/data-cloud/consuming/asmx.md).

В общем случае платформы Xamarin поддерживает то же подмножество WCF, которая поставляется вместе со средой выполнения Silverlight в стороне клиента. Это включает в себя наиболее распространенные кодировку и протокол реализации WCF — транспорта кодировкой текста сообщений SOAP по протоколу HTTP с помощью протокола `BasicHttpBinding` класса. Кроме того поддержка WCF требует применения средств доступно только в среде Windows для создания прокси-сервера.

Инструкции по настройке службы WCF можно найти в файле readme, сопровождающий пример приложения. Тем не менее при выполнении примера приложения его будет подключаться к службе WCF, размещенной в Xamarin, которая предоставляет доступ только для чтения к данным, как показано на следующем снимке экрана:

![](wcf-images/portal.png "Пример приложения")

> [!NOTE]
> В iOS 9 и более поздней версии приложение Transport Security (ATS) обеспечивает безопасных соединений между Интернет-ресурсов (например, server серверной части приложения) и приложения, тем самым предотвращая случайное раскрытие конфиденциальной информации. Поскольку ATS включена по умолчанию в приложениях, созданных для iOS 9, все подключения будут применяться требования к безопасности ATS. Если соединения не удовлетворяют этим требованиям, они вызовут сбой с исключением.
> Если невозможно использовать из могут быть присоединены ATS `HTTPS` протокола и безопасный обмен данными для Интернет-ресурсов. Это достигается путем обновления приложения **Info.plist** файла. Дополнительные сведения см. в разделе [безопасность транспорта приложения](~/ios/app-fundamentals/ats.md).

## <a name="consuming-the-web-service"></a>Использование веб-службы

Служба WCF предоставляет следующие операции:

|Операция|Описание|Параметры|
|--- |--- |--- |
|GetTodoItems|Получение списка элементов задач|
|CreateTodoItem|Создать новый элемент списка дел|XML-сериализации TodoItem|
|EditTodoItem|Обновление элемента задачи|XML-сериализации TodoItem|
|DeleteTodoItem|Удаление элемента задачи|XML-сериализации TodoItem|

Дополнительные сведения о модели данных, используемых в приложении, см. в разделе [моделирования данных](~/xamarin-forms/data-cloud/walkthrough.md).

> [!NOTE]
> Пример приложения использует службу WCF, размещенной в Xamarin, предоставляющий доступ только для чтения к веб-службы. Таким образом операции, создание, обновление и удаление данных, которые не изменится на данных, использованных в приложении. Тем не менее, внедряемое версию службы ASMX доступна в **TodoWCFService** папку в соответствующем приложении-примере. Этот ведущий версию разрешение службы WCF на полный создания, обновления, чтения и удаления доступ к данным.

Объект *прокси* должен быть создан для использования службы WCF, что позволяет приложению для подключения к службе. Прокси создается с помощью много метаданных службы, определяющий методы и связанные службы конфигурации. Эти метаданные предоставляются в виде документа языка описания служб (WSDL), который создается веб-службой. Прокси-сервер может быть построен с помощью Microsoft WCF Web Service Reference Provider в Visual Studio 2017 для добавления ссылки на службу веб-службы в библиотеку .NET Standard. Вместо создания прокси с помощью Microsoft WCF Web Service Reference Provider в Visual Studio 2017 является использование ServiceModel Metadata Utility Tool (svcutil.exe). Дополнительные сведения см. в разделе [ServiceModel Metadata Utility Tool (Svcutil.exe)](/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe/).

Созданный прокси-классы предоставляют методы для использования веб-служб, использующих шаблон проектирования модели асинхронного программирования (APM). В этом случае асинхронная операция реализуется как два метода с именем *BeginOperationName* и *EndOperationName*, который начинают и завершают асинхронную операцию.

*BeginOperationName* метод начинает асинхронную операцию и возвращает объект, реализующий `IAsyncResult` интерфейс. После вызова метода *BeginOperationName*, приложение может продолжить выполнение инструкций в вызывающем потоке, пока асинхронная операция выполняется в потоке пула потоков.

Для каждого вызова *BeginOperationName*, приложение также должно вызвать *EndOperationName* для получения результатов операции. Возвращаемое значение *EndOperationName* имеет тот же тип, возвращаемый методом синхронной веб-службы. Например `EndGetTodoItems` метод возвращает коллекцию `TodoItem` экземпляров. *EndOperationName* также включает `IAsyncResult` параметр, который задается экземпляр, возвращаемый соответствующим вызовом метода *BeginOperationName* метод.

Библиотека параллельных задач (TPL) можно упростить процесс потребляет пару методов begin и end APM инкапсуляция асинхронных операций в том же `Task` объекта. Такая инкапсуляция предоставляется несколько перегрузок `TaskFactory.FromAsync` метод.

Дополнительные сведения о APM см. в разделе [модели асинхронного программирования](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) и [TPL и традиционное .NET Framework асинхронное программирование](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) на сайте MSDN.

### <a name="creating-the-todoserviceclient-object"></a>Создание объекта TodoServiceClient

Созданный класс прокси предоставляет `TodoServiceClient` класс, который используется для взаимодействия со службой WCF по протоколу HTTP. Он предоставляет функциональные возможности для вызова методов веб-службы, асинхронных операций из URI определения экземпляра службы. Дополнительные сведения об асинхронных операциях см. в разделе [обзор поддержки асинхронного](~/cross-platform/platform/async.md).

`TodoServiceClient` Экземпляр объявлен на уровне класса, что объект существует до тех пор, пока приложение должно использовать службу WCF, как показано в следующем примере кода:

```csharp
public class SoapService : ISoapService
{
  ITodoService todoService;
  ...

  public SoapService ()
  {
    todoService = new TodoServiceClient (
      new BasicHttpBinding (),
      new EndpointAddress (Constants.SoapUrl));
  }
  ...
}
```

`TodoServiceClient` Экземпляр настроен с привязки сведения и адрес конечной точки. Привязка используется для указания транспорта, кодировки и протокола, требуемых для приложений и служб для взаимодействия друг с другом. `BasicHttpBinding` Указывает, что по транспортному протоколу HTTP будет отправляться кодировке текста сообщений SOAP. Задание адреса конечной точки позволяет приложению подключаться к различным экземплярам службы WCF, при условии, что несколько экземпляров, опубликованных.

Дополнительные сведения о настройке ссылки на службу, см. в разделе [Настройка ссылки на службу](~/cross-platform/data-cloud/web-services/index.md#wcf).

### <a name="creating-data-transfer-objects"></a>Создание объектов передачи данных

В примере приложения используется `TodoItem` для модели данных. Для хранения `TodoItem` элемента в веб-службы, оно сначала должно быть преобразовано в прокси, созданный `TodoItem` типа. Это достигается путем `ToWCFServiceTodoItem` метод, как показано в следующем примере кода:

```csharp
TodoWCFService.TodoItem ToWCFServiceTodoItem (TodoItem item)
{
  return new TodoWCFService.TodoItem {
    ID = item.ID,
    Name = item.Name,
    Notes = item.Notes,
    Done = item.Done
  };
}
```

Этот метод просто создает новый `TodoWCFService.TodoItem` экземпляра и задает каждое свойство в свойстве идентичные из `TodoItem` экземпляра.

Аналогичным образом, при получении данных из веб-службы, оно должно быть преобразовано из прокси, созданный `TodoItem` тип `TodoItem` экземпляра. Это осуществляется с помощью `FromWCFServiceTodoItem` метод, как показано в следующем примере кода:

```csharp
static TodoItem FromWCFServiceTodoItem (TodoWCFService.TodoItem item)
{
  return new TodoItem {
    ID = item.ID,
    Name = item.Name,
    Notes = item.Notes,
    Done = item.Done
  };
}

```

Этот метод просто получает данные из прокси, созданный `TodoItem` введите и задает его в только что созданный `TodoItem` экземпляра.

### <a name="retrieving-data"></a>Получение данных

`TodoServiceClient.BeginGetTodoItems` И `TodoServiceClient.EndGetTodoItems` методы используются для вызова `GetTodoItems` операция, предоставляемая веб-службой. Эти асинхронные методы, инкапсулируются в `Task` объекта, как показано в следующем примере кода:

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync <ObservableCollection<TodoWCFService.TodoItem>> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);

  foreach (var item in todoItems) {
    Items.Add (FromWCFServiceTodoItem (item));
  }
  ...
}
```

`Task.Factory.FromAsync` Метод создает `Task` , выполняющего `TodoServiceClient.EndGetTodoItems` метод один раз `TodoServiceClient.BeginGetTodoItems` завершения метода с `null` параметр, указывающий, что данные не передается в `BeginGetTodoItems` делегировать. Наконец, значение `TaskCreationOptions` перечисление указывает, что следует использовать поведение по умолчанию для создания и выполнения задач.

`TodoServiceClient.EndGetTodoItems` Возвращает `ObservableCollection` из `TodoWCFService.TodoItem` экземпляров, которые затем преобразуются `List` из `TodoItem` экземпляров для отображения.

### <a name="creating-data"></a>Создание данных

`TodoServiceClient.BeginCreateTodoItem` И `TodoServiceClient.EndCreateTodoItem` методы используются для вызова `CreateTodoItem` операция, предоставляемая веб-службой. Эти асинхронные методы, инкапсулируются в `Task` объекта, как показано в следующем примере кода:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToWCFServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginCreateTodoItem,
    todoService.EndCreateTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

`Task.Factory.FromAsync` Метод создает `Task` , выполняющего `TodoServiceClient.EndCreateTodoItem` метод один раз `TodoServiceClient.BeginCreateTodoItem` завершения метода с `todoItem` данным, которое было передано в параметр `BeginCreateTodoItem` делегат для указания `TodoItem` создаваемого веб-службой. Наконец, значение `TaskCreationOptions` перечисление указывает, что следует использовать поведение по умолчанию для создания и выполнения задач.

Служба вызывает исключение web `FaultException` при невозможности создать `TodoItem`, который обрабатывается приложением.

### <a name="updating-data"></a>Обновление данных

`TodoServiceClient.BeginEditTodoItem` И `TodoServiceClient.EndEditTodoItem` методы используются для вызова `EditTodoItem` операция, предоставляемая веб-службой. Эти асинхронные методы, инкапсулируются в `Task` объекта, как показано в следующем примере кода:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToWCFServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginEditTodoItem,
    todoService.EndEditTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

`Task.Factory.FromAsync` Метод создает `Task` , выполняющего `TodoServiceClient.EndEditTodoItem` метод один раз `TodoServiceClient.BeginCreateTodoItem` завершения метода с `todoItem` данным, которое было передано в параметр `BeginEditTodoItem` делегат для указания `TodoItem` обновления веб-службой. Наконец, значение `TaskCreationOptions` перечисление указывает, что следует использовать поведение по умолчанию для создания и выполнения задач.

Служба вызывает исключение web `FaultException` при невозможности найти или обновить `TodoItem`, который обрабатывается приложением.

### <a name="deleting-data"></a>Удаление данных

`TodoServiceClient.BeginDeleteTodoItem` И `TodoServiceClient.EndDeleteTodoItem` методы используются для вызова `DeleteTodoItem` операция, предоставляемая веб-службой. Эти асинхронные методы, инкапсулируются в `Task` объекта, как показано в следующем примере кода:

```csharp
public async Task DeleteTodoItemAsync (string id)
{
  ...
  await Task.Factory.FromAsync (
    todoService.BeginDeleteTodoItem,
    todoService.EndDeleteTodoItem,
    id,
    TaskCreationOptions.None);
  ...
}
```

`Task.Factory.FromAsync` Метод создает `Task` , выполняющего `TodoServiceClient.EndDeleteTodoItem` метод один раз `TodoServiceClient.BeginDeleteTodoItem` завершения метода с `id` данным, которое было передано в параметр `BeginDeleteTodoItem` делегат для указания `TodoItem` должны быть удалены с веб-службы. Наконец, значение `TaskCreationOptions` перечисление указывает, что следует использовать поведение по умолчанию для создания и выполнения задач.

Служба вызывает исключение web `FaultException` если его не удается найти или удалить `TodoItem`, который обрабатывается приложением.

## <a name="summary"></a>Сводка

В этой статье показано, как использовать службу WCF SOAP из приложения Xamarin.Forms. В общем случае платформы Xamarin поддерживает то же подмножество WCF, которая поставляется вместе со средой выполнения Silverlight в стороне клиента. Это включает в себя наиболее распространенные кодировку и протокол реализации WCF — транспорта кодировкой текста сообщений SOAP по протоколу HTTP с помощью протокола `BasicHttpBinding` класса. Кроме того поддержка WCF требует применения средств доступно только в среде Windows для создания прокси-сервера.


## <a name="related-links"></a>Связанные ссылки

- [TodoWCF (пример)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoWCF/)
- [IAsyncResult](https://msdn.microsoft.com/library/system.iasyncresult(v=vs.110).aspx)
