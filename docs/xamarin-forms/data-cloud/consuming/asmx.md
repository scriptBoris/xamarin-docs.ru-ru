---
title: Использование службы веб-ASP.NET (ASMX)
description: В этой статье показано, как использовать службу ASMX SOAP из приложения Xamarin.Forms.
ms.prod: xamarin
ms.assetid: D5533964-5528-4D35-9C2B-FAFB632472AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 1fa2fee36619a2a443d84b861b2473a1f616ed0e
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53055145"
---
# <a name="consuming-an-aspnet-web-service-asmx"></a>Использование службы веб-ASP.NET (ASMX)

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoASMX/)

_ASMX предоставляет возможность создавать веб-служб, которые отправляют сообщения, используя Simple Object Access Protocol (SOAP). SOAP — это протокол независимо от платформы и независимый от языка, для создания и вызова веб-служб. Потребителям службы ASMX не нужно ничего знать об используемой платформе, объектной модели или язык программирования, используемый для реализации службы. Они должны понять, как отправлять и получать сообщения SOAP. В этой статье показано, как использовать службу ASMX SOAP из приложения Xamarin.Forms._

Сообщение SOAP — это документ XML, содержащий следующие элементы:

- Корневой элемент с именем *конверт* , идентифицирующий XML-документа, как сообщение SOAP.
- Необязательный *заголовок* элемент, содержащий сведения о приложении, такие как данные проверки подлинности. Если *заголовок* присутствует элемент он должен быть первый дочерний элемент элемента *конверт* элемент.
- Обязательный *текст* элемент, содержащий сообщение SOAP, предназначенное для получателя.
- Необязательный *сбоя* элемент, который используется для указания сообщения об ошибках. Если *сбоя* элемент присутствует, он должен быть дочерним элементом элемента *текст* элемент.

SOAP может работать много транспортных протоколов, включая HTTP, SMTP, TCP и UDP. Тем не менее службы ASMX могут работать только по протоколу HTTP. Платформа Xamarin поддерживает стандартные реализации SOAP 1.1 по протоколу HTTP, и он также поддерживает многие из стандартных конфигураций службы ASMX.

Инструкции по настройке службы ASMX можно найти в файле readme, сопровождающий пример приложения. Тем не менее при запуске примера приложения, он будет подключаться к служба ASMX, размещенный в Xamarin, которая предоставляет доступ только для чтения к данным, как показано на следующем снимке экрана:

![](asmx-images/portal.png "Пример приложения")

> [!NOTE]
> В iOS 9 и более поздней версии приложение Transport Security (ATS) обеспечивает безопасных соединений между Интернет-ресурсов (например, server серверной части приложения) и приложения, тем самым предотвращая случайное раскрытие конфиденциальной информации. Поскольку ATS включена по умолчанию в приложениях, созданных для iOS 9, все подключения будут применяться требования к безопасности ATS. Если соединения не удовлетворяют этим требованиям, они вызовут сбой с исключением.
> Если невозможно использовать из могут быть присоединены ATS `HTTPS` протокола и безопасный обмен данными для Интернет-ресурсов. Это достигается путем обновления приложения **Info.plist** файла. Дополнительные сведения см. в разделе [безопасность транспорта приложения](~/ios/app-fundamentals/ats.md).

## <a name="consuming-the-web-service"></a>Использование веб-службы

Служба ASMX предоставляет следующие операции:

|Операция|Описание|Параметры|
|--- |--- |--- |
|GetTodoItems|Получение списка элементов задач|
|CreateTodoItem|Создать новый элемент списка дел|XML-сериализации TodoItem|
|EditTodoItem|Обновление элемента задачи|XML-сериализации TodoItem|
|DeleteTodoItem|Удаление элемента задачи|XML-сериализации TodoItem|

Дополнительные сведения о модели данных, используемых в приложении, см. в разделе [моделирования данных](~/xamarin-forms/data-cloud/walkthrough.md).

> [!NOTE]
> Пример приложения использует службу ASMX, размещенный в Xamarin, предоставляющий доступ только для чтения к веб-службы. Таким образом операции, создание, обновление и удаление данных, которые не изменится на данных, использованных в приложении. Тем не менее, внедряемое версию службы ASMX доступна в **TodoASMXService** папку в соответствующем приложении-примере. Этот ведущий версию разрешение службы ASMX на полный создания, обновления, чтения и удаления доступ к данным.

Объект *прокси* должен быть создан для использования службы ASMX, что позволяет приложению для подключения к службе. Прокси создается с помощью много метаданных службы, определяющий методы и связанные службы конфигурации. Эти метаданные предоставляются в виде документа языка описания служб (WSDL), который создается веб-службой. Прокси-сервер создается путем добавления веб-ссылки для веб-службы в проекты под конкретные платформы.

Созданный прокси-классы предоставляют методы для использования веб-службы, использующих шаблон проектирования модели асинхронного программирования (APM). В этом шаблоне асинхронную операцию реализуется в виде двух методов с именем *BeginOperationName* и *EndOperationName*, который начинают и завершают асинхронную операцию.

*BeginOperationName* метод начинает асинхронную операцию и возвращает объект, реализующий `IAsyncResult` интерфейс. После вызова метода *BeginOperationName*, приложение может продолжить выполнение инструкций в вызывающем потоке, пока асинхронная операция выполняется в потоке пула потоков.

Для каждого вызова *BeginOperationName*, приложение также должно вызвать *EndOperationName* для получения результатов операции. Возвращаемое значение *EndOperationName* имеет тот же тип, возвращаемый методом синхронной веб-службы. Например `EndGetTodoItems` метод возвращает коллекцию `TodoItem` экземпляров. *EndOperationName* также включает `IAsyncResult` параметр, который задается экземпляр, возвращаемый соответствующим вызовом метода *BeginOperationName* метод.

Библиотека параллельных задач (TPL) можно упростить процесс потребляет пару методов begin и end APM инкапсуляция асинхронных операций в том же `Task` объекта. Такая инкапсуляция предоставляется несколько перегрузок `TaskFactory.FromAsync` метод.

Дополнительные сведения о APM см. в разделе [модели асинхронного программирования](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) и [TPL и традиционное .NET Framework асинхронное программирование](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) на сайте MSDN.

### <a name="creating-the-todoservice-object"></a>Создание объекта TodoService

Созданный класс прокси предоставляет `TodoService` класс, который используется для взаимодействия со службой ASMX по протоколу HTTP. Он предоставляет функциональные возможности для вызова методов веб-службы, асинхронных операций из URI определения экземпляра службы. Дополнительные сведения об асинхронных операциях см. в разделе [обзор поддержки асинхронного](~/cross-platform/platform/async.md).

`TodoService` Экземпляр объявлен на уровне класса, что объект существует до тех пор, пока приложение должно использовать службу ASMX, как показано в следующем примере кода:

```csharp
public class SoapService : ISoapService
{
  ASMXService.TodoService asmxService;
  ...

  public SoapService ()
  {
    asmxService = new ASMXService.TodoService (Constants.SoapUrl);
  }
  ...
}
```

`TodoService` Конструктор принимает необязательный строковый параметр, который указывает URL-адрес экземпляра службы ASMX. Это позволяет приложению для подключения к различным экземплярам службы ASMX, при условии, что несколько опубликованных экземпляров.

### <a name="creating-data-transfer-objects"></a>Создание объектов передачи данных

В примере приложения используется `TodoItem` для модели данных. Для хранения `TodoItem` элемента в веб-службы, оно сначала должно быть преобразовано в прокси, созданный `TodoItem` типа. Это достигается путем `ToASMXServiceTodoItem` метод, как показано в следующем примере кода:

```csharp
ASMXService.TodoItem ToASMXServiceTodoItem (TodoItem item)
{
  return new ASMXService.TodoItem {
    ID = item.ID,
    Name = item.Name,
    Notes = item.Notes,
    Done = item.Done
  };
}
```

Этот метод просто создает новый `ASMService.TodoItem` экземпляра и задает каждое свойство в свойстве идентичные из `TodoItem` экземпляра.

Аналогичным образом, при получении данных из веб-службы, оно должно быть преобразовано из прокси, созданный `TodoItem` тип `TodoItem` экземпляра. Это осуществляется с помощью `FromASMXServiceTodoItem` метод, как показано в следующем примере кода:

```csharp
static TodoItem FromASMXServiceTodoItem (ASMXService.TodoItem item)
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

`TodoService.BeginGetTodoItems` И `TodoService.EndGetTodoItems` методы используются для вызова `GetTodoItems` операция, предоставляемая веб-службой. Эти асинхронные методы, инкапсулируются в `Task` объекта, как показано в следующем примере кода:

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync<ASMXService.TodoItem[]> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);

  foreach (var item in todoItems) {
    Items.Add (FromASMXServiceTodoItem (item));
  }
  ...
}
```

`Task.Factory.FromAsync` Метод создает `Task` , выполняющего `TodoService.EndGetTodoItems` метод один раз `TodoService.BeginGetTodoItems` завершения метода с `null` параметр, указывающий, что данные не передается в `BeginGetTodoItems` делегировать. Наконец, значение `TaskCreationOptions` перечисление указывает, что следует использовать поведение по умолчанию для создания и выполнения задач.

`TodoService.EndGetTodoItems` Метод возвращает массив `ASMXService.TodoItem` экземпляров, которые затем преобразуются `List` из `TodoItem` экземпляров для отображения.

### <a name="creating-data"></a>Создание данных

`TodoService.BeginCreateTodoItem` И `TodoService.EndCreateTodoItem` методы используются для вызова `CreateTodoItem` операция, предоставляемая веб-службой. Эти асинхронные методы, инкапсулируются в `Task` объекта, как показано в следующем примере кода:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToASMXServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginCreateTodoItem,
    todoService.EndCreateTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

`Task.Factory.FromAsync` Метод создает `Task` , выполняющего `TodoService.EndCreateTodoItem` метод один раз `TodoService.BeginCreateTodoItem` завершения метода с `todoItem` данным, которое было передано в параметр `BeginCreateTodoItem` делегат для указания `TodoItem` создаваемого веб-службой. Наконец, значение `TaskCreationOptions` перечисление указывает, что следует использовать поведение по умолчанию для создания и выполнения задач.

Служба вызывает исключение web `SoapException` при невозможности создать `TodoItem`, который обрабатывается приложением.

### <a name="updating-data"></a>Обновление данных

`TodoService.BeginEditTodoItem` И `TodoService.EndEditTodoItem` методы используются для вызова `EditTodoItem` операция, предоставляемая веб-службой. Эти асинхронные методы, инкапсулируются в `Task` объекта, как показано в следующем примере кода:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToASMXServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginEditTodoItem,
    todoService.EndEditTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

`Task.Factory.FromAsync` Метод создает `Task` , выполняющего `TodoService.EndEditTodoItem` метод один раз `TodoService.BeginCreateTodoItem` завершения метода с `todoItem` данным, которое было передано в параметр `BeginEditTodoItem` делегат для указания `TodoItem` обновления веб-службой. Наконец, значение `TaskCreationOptions` перечисление указывает, что следует использовать поведение по умолчанию для создания и выполнения задач.

Служба вызывает исключение web `SoapException` при невозможности найти или обновить `TodoItem`, который обрабатывается приложением.

### <a name="deleting-data"></a>Удаление данных

`TodoService.BeginDeleteTodoItem` И `TodoService.EndDeleteTodoItem` методы используются для вызова `DeleteTodoItem` операция, предоставляемая веб-службой. Эти асинхронные методы, инкапсулируются в `Task` объекта, как показано в следующем примере кода:

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

`Task.Factory.FromAsync` Метод создает `Task` , выполняющего `TodoService.EndDeleteTodoItem` метод один раз `TodoService.BeginDeleteTodoItem` завершения метода с `id` данным, которое было передано в параметр `BeginDeleteTodoItem` делегат для указания `TodoItem` должны быть удалены с веб-службы. Наконец, значение `TaskCreationOptions` перечисление указывает, что следует использовать поведение по умолчанию для создания и выполнения задач.

Служба вызывает исключение web `SoapException` если его не удается найти или удалить `TodoItem`, который обрабатывается приложением.

## <a name="summary"></a>Сводка

В этой статье показано, как использовать веб-службу ASMX, в приложении Xamarin.Forms. ASMX предоставляет возможность создавать веб-службы, отправляющие сообщения по протоколу HTTP с использованием протокола SOAP. Потребителям службы ASMX не нужно ничего знать об используемой платформе, объектной модели или язык программирования, используемый для реализации службы. Они должны понять, как отправлять и получать сообщения SOAP.


## <a name="related-links"></a>Связанные ссылки

- [TodoASMX (пример)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoASMX/)
- [IAsyncResult](https://msdn.microsoft.com/library/system.iasyncresult(v=vs.110).aspx)
