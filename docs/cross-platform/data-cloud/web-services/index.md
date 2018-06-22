---
title: Общие сведения о веб-служб
description: В этом руководстве демонстрируется использование различных веб-технологий, службы. Рассматриваются связи с службы REST, SOAP служб и служб Windows Communication Foundation.
ms.prod: xamarin
ms.assetid: 72627B90-586A-02B6-E231-F7CE015A1B97
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 5c07443519391192f7bdb62cc19ff8b1f87b2558
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2018
ms.locfileid: "33919089"
---
# <a name="introduction-to-web-services"></a>Общие сведения о веб-служб

_В этом руководстве демонстрируется использование различных веб-технологий, службы. Рассматриваются связи с службы REST, SOAP служб и служб Windows Communication Foundation._

Для правильной работы многих мобильных приложений, зависящих от облака, и таким образом интеграции веб-служб в мобильных приложениях это очень распространенный сценарий. Платформа Xamarin поддерживает использование различных веб-технологий, службы и включает встроенные и сторонние поддержку использования служб категории RESTful, ASMX и Windows Communication Foundation (WCF).

В этой статье рассматриваются следующие вопросы:

- [Службы REST](#rest)
- [Веб-службы ASP.Net (ASMX)](#asmx)
- [Службы WCF](#wcf)

Для клиентов с помощью Xamarin.Forms, имеются полные примеры, используя каждый из этих технологий в [Xamarin.Forms веб-службы](~/xamarin-forms/data-cloud/index.md) документации.

> [!IMPORTANT]
> В iOS 9 безопасности транспорта приложения (ATS) обеспечивает безопасное соединение между Интернет-ресурсов (например, приложение серверных) и приложения, чтобы предотвратить случайное раскрытие конфиденциальных сведений.
> Поскольку ATS включена по умолчанию в приложениях, разработанных для iOS 9, все соединения будут применяться ATS требования безопасности. Если соединения не удовлетворяют этим требованиям, произойдет сбой с исключением.

Вы можете отказаться от ATS Если невозможно использовать `HTTPS` протокола и безопасную передачу Интернет-ресурсов. Это можно сделать путем обновления приложения **Info.plist** файла. Дополнительные сведения см. [безопасность транспорта приложения](~/ios/app-fundamentals/ats.md).



<a name="rest" />

## <a name="rest"></a>REST

Representational State Transfer (REST) представляет собой архитектурный стиль для построения веб-служб. ОСТАЛЬНЫЕ запросы выполняются по протоколу HTTP, используются те же глаголы HTTP, используемых веб-браузеры для получения веб-страниц и отправки данных на серверы. Ниже приведены команды.

- **ПОЛУЧИТЬ** — эта операция используется для получения данных из веб-службы.
- **POST** — эта операция используется для создания нового элемента данных на веб-службы.
- **ПОМЕСТИТЕ** — эта операция используется для обновления элемента данных в веб-службе.
- **ИСПРАВЛЕНИЕ** — эта операция используется для обновления элемента данных на веб-службы, описывающий набор инструкций о том, как изменить элемент. Эта команда не используется в примере приложения.
- **Удалить** — эта операция используется для удаления элемента данных на веб-службы.

API-интерфейсы, которые соответствуют ОСТАЛЬНОЙ называются RESTful API-интерфейсов, а также определяются с помощью веб-службы:

- Базовый URI.
- Методы HTTP, такие как GET, POST, PUT, PATCH или DELETE.
- Тип носителя для данных, таких как JavaScript Object Notation (JSON).

Простота REST сделала его основной метод для доступа к веб-служб в мобильных приложениях.

## <a name="consuming-rest-services"></a>Использование служб REST

Существует несколько библиотек и классы, которые могут использоваться для использования служб REST, а в следующих подразделах обсуждаются их. Дополнительные сведения об использовании службы REST см. в разделе [RESTful веб-служба](~/xamarin-forms/data-cloud/consuming/rest.md).

### <a name="httpclient"></a>HttpClient

[Клиентских библиотек HTTP Майкрософт](https://www.nuget.org/packages/Microsoft.Net.Http) предоставляет `HttpClient` класс, который используется для отправки и получения запросов по протоколу HTTP. Он предоставляет функциональность для отправки запросов HTTP и получения HTTP-ответов из указанной в URI ресурса. Каждый запрос будет отправлен в качестве асинхронной операции. Дополнительные сведения об асинхронных операциях см. в разделе [Общие сведения о поддержке Async](~/cross-platform/platform/async.md).

`HttpResponseMessage` Класс представляет сообщение ответа HTTP, полученных от веб-службы, после внесения HTTP-запроса. Он содержит сведения об ответе, включая код состояния, заголовки и текст. `HttpContent` Класс представляет основного текста HTTP и заголовки содержимого, такие как `Content-Type` и `Content-Encoding`. Содержимое можно просмотреть с помощью любой из `ReadAs` методы, такие как `ReadAsStringAsync` и `ReadAsByteArrayAsync`, в зависимости от формата данных.

Дополнительные сведения о `HttpClient` см. в описании [создание объекта HTTPClient](~/xamarin-forms/data-cloud/consuming/rest.md).

<a name="Using_HTTPWebRequest" />

### <a name="httpwebrequest"></a>HTTPWebRequest

Вызов веб-служб с `HTTPWebRequest` включает в себя:

-  Создание экземпляра запроса для определенного URI.
-  Настройка различных свойств HTTP для экземпляра запроса.
-  Получение `HttpWebResponse` из запроса.
-  Чтение данных из ответа.

Например следующий код извлекает данные из США. Национальный библиотеки веб-службы:

```csharp
var rxcui = "198440";
var request = HttpWebRequest.Create(string.Format(@"http://rxnav.nlm.nih.gov/REST/RxTerms/rxcui/{0}/allinfo", rxcui));
request.ContentType = "application/json";
request.Method = "GET";

using (HttpWebResponse response = request.GetResponse() as HttpWebResponse)
{
  if (response.StatusCode != HttpStatusCode.OK)
     Console.Out.WriteLine("Error fetching data. Server returned status code: {0}", response.StatusCode);
  using (StreamReader reader = new StreamReader(response.GetResponseStream()))
  {
               var content = reader.ReadToEnd();
               if(string.IsNullOrWhiteSpace(content)) {
                       Console.Out.WriteLine("Response contained empty body...");
               }
               else {
                       Console.Out.WriteLine("Response Body: \r\n {0}", content);
               }

               Assert.NotNull(content);
  }
}
```

В приведенном выше примере создается `HttpWebRequest` , возвращает данные в формате JSON. Данные возвращаются в `HttpWebResponse`, из которого `StreamReader` доступны для чтения данных.

<a name="Using_RESTSHARP" />

### <a name="restsharp"></a>RestSharp

Использует другой подход для использования служб REST [RestSharp](http://restsharp.org/) библиотеки. RestSharp инкапсулирует HTTP-запросов, включая поддержку для получения результатов в виде необработанного строкового содержимого или Десериализованный объект C#. Например следующий код выполняет запрос в США. Строку в формате национального библиотеки веб-службы, а затем извлекает результаты как JSON:

```csharp
var request = new RestRequest(string.Format("{0}/allinfo", rxcui));
request.RequestFormat = DataFormat.Json;
var response = Client.Execute(request);
if(string.IsNullOrWhiteSpace(response.Content) || response.StatusCode != System.Net.HttpStatusCode.OK) {
       return null;
}
rxTerm = DeserializeRxTerm(response.Content);
```

`DeserializeRxTerm` метод, который будет принимать необработанную строку JSON из `RestSharp.RestResponse.Content` свойство и его преобразования в объект C#. Далее в этой статье рассматривается десериализации данных, возвращаемых из веб-служб.

<a name="Using_NSUrlconnection" />

### <a name="nsurlconnection"></a>NSUrlConnection

Помимо классов, доступных в базовом классе моно библиотека классов (BCL), такие как `HttpWebRequest`и библиотек сторонних разработчиков C#, например RestSharp, классы для определенной платформы, также доступны для использования веб-служб. Например, в iOS `NSUrlConnection` и `NSMutableUrlRequest` классы могут быть использованы.

В следующем примере кода показано, как вызвать США. Национальный веб-службы библиотеки с помощью классов iOS:

```csharp
var rxcui = "198440";
var request = new NSMutableUrlRequest(new NSUrl(string.Format("http://rxnav.nlm.nih.gov/REST/RxTerms/rxcui/{0}/allinfo", rxcui)),
       NSUrlRequestCachePolicy.ReloadRevalidatingCacheData, 20);
request["Accept"] = "application/json";

var connectionDelegate = new RxTermNSURLConnectionDelegate();
var connection = new NSUrlConnection(request, connectionDelegate);
connection.Start();

public class RxTermNSURLConnectionDelegate : NSUrlConnectionDelegate
{
       StringBuilder _ResponseBuilder;
       public bool IsFinishedLoading { get; set; }
       public string ResponseContent { get; set; }

       public RxTermNSURLConnectionDelegate()
               : base()
       {
               _ResponseBuilder = new StringBuilder();
       }

       public override void ReceivedData(NSUrlConnection connection, NSData data)
       {
               if(data != null) {
                       _ResponseBuilder.Append(data.ToString());
               }
       }
       public override void FinishedLoading(NSUrlConnection connection)
       {
               IsFinishedLoading = true;
               ResponseContent = _ResponseBuilder.ToString();
       }
}
```

В общем случае классы конкретную платформу для использования веб-служб должны быть ограничены в сценариях, где переносятся машинного кода в C#. По возможности, чтобы он мог быть общей кросс платформенных кода доступа к веб-службы должны быть переносимыми.

<a name="Using_ServiceStack_Client" />

### <a name="servicestack"></a>ServiceStack

Другой вариант для вызова веб-службы — [стека службы](http://www.servicestack.net/) библиотеки. Например, ниже показано использование службы стека `IServiceClient.GetAsync` метод для выдачи запроса на обслуживание:

```csharp
client.GetAsync<CustomersResponse>("",
          (response) => {
               foreach(var c in response.Customers) {
                       Console.WriteLine(c.CompanyName);
               }
       },
       (response, ex) => {
               Console.WriteLine(ex.Message);
       });
```

> [!IMPORTANT]
> Хотя средства, такие как ServiceStack и RestSharp позволяют легко вызывать и использовать REST служб, иногда бывает нетривиальный для использования XML или JSON, которые не соответствуют стандарту _DataContract_ соглашения о сериализации. При необходимости, вызвать запрос и обрабатывать соответствующий сериализацию явным образом с помощью библиотеки ServiceStack.Text, описаны ниже.


<a name="Options_for_consuming_RESTful_data" />

## <a name="consuming-restful-data"></a>Использование RESTful данных

Веб-служб rESTful обычно используется сообщений JSON для возвращения данных клиенту. JSON представляет основанные на тексте формат обмена данными, создает compact полезных данных, что приводит к ограниченной пропускной способности при передаче данных. В этом разделе будут проверены механизмы для использования RESTful ответов JSON и простой старый XML (POX).

<a name="Using_System.JSON" />

### <a name="systemjson"></a>System.JSON

Платформа Xamarin поставляется с поддержкой JSON без дополнительной настройки. С помощью `JsonObject`, результаты можно получить, как показано в следующем примере кода:

```csharp
var obj = JsonObject.Parse(json);
var properties = obj["rxtermsProperties"];
term.BrandName = properties["brandName"];
term.DisplayName = properties["displayName"];
term.Synonym = properties["synonym"];
term.FullName = properties["fullName"];
term.FullGenericName = properties["fullGenericName"];
term.Strength = properties["strength"];
```

Тем не менее, важно иметь в виду, что `System.Json` средства загрузки для полноты данных в память.

<a name="Using_JSON.NET" />

### <a name="jsonnet"></a>JSON.NET

[NewtonSoft JSON.NET библиотеки](http://www.newtonsoft.com/json) является библиотекой, широко используемый для сериализации и десериализации сообщений JSON. В следующем примере кода показано, как использовать JSON.NET для десериализации сообщения JSON в объект C#:

```csharp
var term = new RxTerm();
var properties = JObject.Parse(json)["rxtermsProperties"];
term.BrandName = properties["brandName"].Value<string>();
term.DisplayName = properties["displayName"].Value<string>();
term.Synonym = properties["synonym"].Value<string>();;
term.FullName = properties["fullName"].Value<string>();;
term.FullGenericName = properties["fullGenericName"].Value<string>();;
term.Strength = properties["strength"].Value<string>();
term.RxCUI = properties["rxcui"].Value<string>();
```

<a name="Using_ServiceStack.Text" />

### <a name="servicestacktext"></a>ServiceStack.Text

ServiceStack.Text — это библиотека сериализации JSON, предназначенных для работы с библиотекой ServiceStack. В следующем примере кода показано, как выполнить синтаксический анализ JSON с помощью `ServiceStack.Text.JsonObject`:

```csharp
var result = JsonObject.Parse(json).Object("rxtermsProperties")
       .ConvertTo(x => new RxTerm {
               BrandName = x.Get("brandName"),
               DisplayName = x.Get("displayName"),
               Synonym = x.Get("synonym"),
               FullName = x.Get("fullName"),
               FullGenericName = x.Get("fullGenericName"),
               Strength = x.Get("strength"),
               RxTermDoseForm = x.Get("rxtermsDoseForm"),
               Route = x.Get("route"),
               RxCUI = x.Get("rxcui"),
               RxNormDoseForm = x.Get("rxnormDoseForm"),
       });
```

<a name="Using_System.Xml.Linq" />

### <a name="systemxmllinq"></a>System.Xml.Linq

В случае использования веб-служба REST на основе XML, LINQ to XML можно использовать для синтаксического анализа XML и заполнить C# встроенный объект, как показано в следующем примере кода:

```csharp
var doc = XDocument.Parse(xml);
var result = doc.Root.Descendants("rxtermsProperties")
.Select(x=> new RxTerm()
       {
               BrandName = x.Element("brandName").Value,
               DisplayName = x.Element("displayName").Value,
               Synonym = x.Element("synonym").Value,
               FullName = x.Element("fullName").Value,
               FullGenericName = x.Element("fullGenericName").Value,
               //bind more here...
               RxCUI = x.Element("rxcui").Value,
       });
```

<a name="asmx" />

## <a name="aspnet-web-service-asmx"></a>Веб-службы ASP.NET (ASMX)

ASMX предоставляет возможность создания веб-служб, используемых при отправке сообщений с помощью Simple Object Access Protocol (SOAP). SOAP — это независимая от платформы и независимые от языка протокол для создания и доступа к веб-служб. Потребителям службы ASMX, никакая о платформе, объектной модели или язык программирования, используемый для реализации службы не требуется. Они должны понимать, как отправлять и получать сообщения SOAP.

Сообщение SOAP — это документ XML, содержащую следующие элементы:

- Корневой элемент *конверт* , идентифицирующий XML-документа, как сообщение SOAP.
- Необязательный *заголовок* элемент, который содержит сведения о приложении, например, данные проверки подлинности. Если *заголовок* присутствует элемент он должен быть первым дочерним элементом *конверт* элемента.
- Обязательный *текст* элемент, содержащий сообщение SOAP, предназначенное для получателя.
- Необязательный *ошибки* элемент, который используется для указания сообщения об ошибках. Если *ошибки* элемент присутствует, он должен быть дочерним для элемента *текст* элемента.

SOAP может работать через несколько транспортных протоколов, включая HTTP, SMTP, TCP и UDP. Однако службы ASMX может работать только по протоколу HTTP. Платформа Xamarin поддерживает стандартные реализации SOAP 1.1 по протоколу HTTP, и она включает поддержку для многих стандартных конфигураций службы ASMX.

### <a name="generating-a-proxy"></a>Создание учетной записи-посредника

Объект *прокси* должен быть создан для использования службой ASMX, который позволяет подключиться к службе в приложении. Прокси-сервер создается путем много метаданных службы, определяющий методы и связанные службы конфигурации. Эти метаданные предоставляются в виде документа языка описания веб-служб (WSDL), создается веб-службой. Прокси-сервер создается с помощью Visual Studio или Visual Studio для Mac для добавления в проекты под конкретные платформы веб-ссылку на веб-службу.

URL веб-службы может быть размещенного удаленного источника или ресурса локальной файловой системы, доступны через `file:///` префикс пути, например:

```csharp
file:///Users/myUserName/projects/MyProjectName/service.wsdl
```

[![](images/add-webreference-dialog.png "URL веб-службы может быть размещенного удаленного источника или через префикс пути файла ресурса локальной файловой системы")](images/add-webreference-dialog.png#lightbox)

Это приводит к возникновению ошибки прокси-сервера в папке проекта веб- или ссылки на службу. С момента создания учетной записи-посредника кода, не должны быть изменены.

<a name="Manually_adding_a_proxy_to_a_project" />

#### <a name="manually-adding-a-proxy-to-a-project"></a>Добавление учетной записи-посредника в проект вручную

При наличии существующего прокси-сервера, сформированные с помощью средств, совместимы эти выходные данные могут быть использованы, когда включен как часть проекта. В Visual Studio для Mac с помощью **добавить файлы...** параметр меню, чтобы добавить учетную запись-посредник. Кроме того, это требует *System.Web.Services.dll* должно быть указано явным образом с помощью **Добавление ссылок...** диалоговое окно.

### <a name="consuming-the-proxy"></a>Использование прокси-сервера

Созданный прокси-классы предоставляют методы для использования веб-службы, использующие шаблон разработки для асинхронной модели программирования (APM). В этом шаблоне асинхронную операцию, реализуется в виде двух методов с именами *BeginOperationName* и *EndOperationName*, который начинают и завершают асинхронную операцию.

*BeginOperationName* метод начинает асинхронную операцию и возвращает объект, реализующий интерфейс `IAsyncResult` интерфейс. После вызова метода *BeginOperationName*, приложение может продолжить выполнение инструкций в вызывающем потоке, пока асинхронная операция выполняется в потоке пула потоков.

Для каждого вызова *BeginOperationName*, приложение должно также вызывать *EndOperationName* для получения результатов операции. Возвращаемое значение *EndOperationName* имеет тот же тип, возвращаемый метод синхронной веб-службы. В следующем примере кода показан пример этого.

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync<ASMXService.TodoItem[]> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);
  ...
}
```

Библиотека параллельных задач (TPL) может упростить процесс использует пары методов begin и end APM, инкапсулируя асинхронных операций в том же `Task` объекта. Эта инкапсуляция предоставляется несколько перегрузок `Task.Factory.FromAsync` метод. Этот метод создает `Task` , выполняющего `TodoService.EndGetTodoItems` метод один раз `TodoService.BeginGetTodoItems` метод завершения, с `null` параметр, указывающий, что данные не передается в `BeginGetTodoItems` делегата. Наконец, значение `TaskCreationOptions` перечисление указывает, что следует использовать поведение по умолчанию для создания и выполнения задач.

Дополнительные сведения о APM см. в разделе [модели асинхронного программирования](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) и [библиотека параллельных задач и традиционное .NET Framework асинхронное программирование](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) на сайте MSDN.

Дополнительные сведения об использовании службы ASMX см. в разделе [использование службы ASP.NET Web (ASMX)](~/xamarin-forms/data-cloud/consuming/asmx.md).

<a name="wcf" />

## <a name="windows-communication-foundation-wcf"></a>Windows Communication Foundation (WCF)

WCF является единой framework корпорации Майкрософт для построения сервисноориентированных приложений. Она позволяет разработчикам создавать распределенные приложения безопасную, надежную, транзакций и с возможностью взаимодействия.

WCF описание службы с различными разные контракты, в том числе следующие:

- **Контракты данных** — структуры данных, которые образуют основу для содержимого сообщения.
- **Контрактами сообщений** — создания сообщений с существующие контракты данных.
- **Контракты сбоя** — разрешить пользовательских ошибок SOAP, должен быть задан.
- **Контракты службы** — указать операции, которые поддерживают службы и сообщения, необходимые для взаимодействия с каждой операцией. Они также определяют любое поведение пользовательской ошибки, могут быть связаны с операциями для каждой службы.

Существуют различия между службами ASP.NET Web (ASMX) и WCF, но важно понимать, что WCF поддерживает те же возможности, предоставляемые ASMX-сообщений SOAP по протоколу HTTP.

Как правило платформа Xamarin поддерживает тот же поднабор стороны клиента WCF, который поставляется со средой выполнения Silverlight. Сюда входят наиболее распространенные кодировку и протокол реализации WCF — транспорта кодировке текста сообщения SOAP по протоколу HTTP с помощью протокола `BasicHttpBinding` класса. Кроме того поддержка WCF требует использования средств доступно только в среде Windows для создания прокси-сервера.

Дополнительные сведения об использовании платформе Xamarin WCF использовать веб-службы с `BasicHttpBinding` см. в описании [Пошаговое руководство. Работа с WCF](walkthrough-working-with-wcf.md).

### <a name="generating-a-proxy"></a>Создание учетной записи-посредника

Объект *прокси* должен быть создан для использования службы WCF, которая позволяет подключиться к службе в приложении. Прокси-сервер создается путем много метаданных службы, определяющий методы и связанные службы конфигурации. Эти метаданные предоставляются в виде документа языка описания веб-служб (WSDL), который создается веб-службой. Прокси-сервера можно создать с помощью поставщика ссылочных службы Microsoft WCF Web в Visual Studio 2017 г. для добавления ссылки на службу для веб-службы для стандартной библиотеки .NET.

Вместо создания прокси-сервера, используя Microsoft WCF Web ссылку поставщик службы в Visual Studio 2017 г. является использование ServiceModel Metadata Utility Tool (svcutil.exe). Дополнительные сведения см. в разделе [ServiceModel Metadata Utility Tool (Svcutil.exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe).

<a name="Calling_a_WCF_Service_with_Client_Credential_Security" />

### <a name="configuring-the-proxy"></a>Настройка прокси-сервера

Настройка созданного прокси-сервера обычно займет два аргумента конфигурации (в зависимости от протокола SOAP 1.1/ASMX или WCF) во время инициализации: `EndpointAddress` и связанные сведения о привязке, как показано в следующем примере:

```csharp
var binding = new BasicHttpBinding () {
       Name= "basicHttpBinding",
       MaxReceivedMessageSize = 67108864,
};

binding.ReaderQuotas = new System.Xml.XmlDictionaryReaderQuotas() {
       MaxArrayLength = 2147483646,
       MaxStringContentLength = 5242880,
};

var timeout = new TimeSpan(0,1,0);
binding.SendTimeout= timeout;
binding.OpenTimeout = timeout;
binding.ReceiveTimeout = timeout;

client = new Service1Client (binding, new EndpointAddress ("http://192.168.1.100/Service1.svc"));
```

Привязка используется для указания транспорта, кодировки и данных протокола, требуемых для приложений и служб для взаимодействия друг с другом. `BasicHttpBinding` Указывает отправки сообщения SOAP с кодировкой текста по транспортному протоколу HTTP. Задание адреса конечной точки позволяет приложению подключаться к различным экземплярам службы WCF, при условии, что несколько опубликованных экземпляров.

### <a name="consuming-the-proxy"></a>Использование прокси-сервера

Созданный прокси-классы предоставляют методы для использования веб-служб, использующих шаблон разработки для асинхронной модели программирования (APM). В этом шаблоне асинхронную операцию, реализуется в виде двух методов с именами *BeginOperationName* и *EndOperationName*, который начинают и завершают асинхронную операцию.

*BeginOperationName* метод начинает асинхронную операцию и возвращает объект, реализующий интерфейс `IAsyncResult` интерфейс. После вызова метода *BeginOperationName*, приложение может продолжить выполнение инструкций в вызывающем потоке, пока асинхронная операция выполняется в потоке пула потоков.

Для каждого вызова *BeginOperationName*, приложение должно также вызывать *EndOperationName* для получения результатов операции. Возвращаемое значение *EndOperationName* имеет тот же тип, возвращаемый метод синхронной веб-службы. В следующем примере кода показан пример этого.

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync <ObservableCollection<TodoWCFService.TodoItem>> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);
  ...
}
```

Библиотека параллельных задач (TPL) может упростить процесс использует пары методов begin и end APM, инкапсулируя асинхронных операций в том же `Task` объекта. Эта инкапсуляция предоставляется несколько перегрузок `Task.Factory.FromAsync` метод. Этот метод создает `Task` , выполняющего `TodoServiceClient.EndGetTodoItems` метод один раз `TodoServiceClient.BeginGetTodoItems` метод завершения, с `null` параметр, указывающий, что данные не передается в `BeginGetTodoItems` делегата. Наконец, значение `TaskCreationOptions` перечисление указывает, что следует использовать поведение по умолчанию для создания и выполнения задач.

Дополнительные сведения о APM см. в разделе [модели асинхронного программирования](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) и [библиотека параллельных задач и традиционное .NET Framework асинхронное программирование](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) на сайте MSDN.

Дополнительные сведения об использовании службы WCF см. в разделе [этого веб-служба Windows Communication Foundation (WCF)](~/xamarin-forms/data-cloud/consuming/wcf.md).

<a name="Calling_a_WCF_Service_with_Transport_Security" />

#### <a name="using-transport-security"></a>Использование безопасности транспорта

Службы WCF могут использовать безопасность на уровне транспорта для защиты от перехвата сообщений. Платформа Xamarin поддерживает привязок, использующих безопасность на уровне транспорта с помощью протокола SSL. Тем не менее могут быть случаи, в которых стек может потребоваться проверить сертификат, который приводит к непредвиденного поведения. Проверки можно переопределить, зарегистрировав `ServerCertificateValidationCallback` делегат перед вызовом службы, как показано в следующем примере кода:

```csharp
System.Net.ServicePointManager.ServerCertificateValidationCallback +=
(se, cert, chain, sslerror) => { return true; };
```

Это обеспечивает шифрование транспорта, игнорируя проверки сертификата на стороне сервера. Тем не менее этот подход фактически не учитывает доверия проблемы, связанные с сертификатом и может быть неприемлемо. Дополнительные сведения см. в разделе [с помощью доверенных корневых папок уважительно](http://www.mono-project.com/UsingTrustedRootsRespectfully) на [project.com моно](http://www.mono-project.com).

<a name="Calling_a_WCF_Service_with_Client_Credential_Security" />

#### <a name="using-client-credential-security"></a>С помощью безопасности учетных данных клиента

Службы WCF может также потребоваться клиентов службы для проверки подлинности с использованием учетных данных. Платформа Xamarin не поддерживает протокол WS-Security, которая позволяет клиентам отправлять учетные данные в конверте SOAP сообщения. Тем не менее, платформа Xamarin поддерживает возможность отправлять учетные данные обычной проверки подлинности HTTP на сервер, указав соответствующую `ClientCredentialType`:

```csharp
basicHttpBinding.Security.Transport.ClientCredentialType = HttpClientCredentialType.Basic;
```

Затем можно указать учетные данные обычной проверки подлинности:

```csharp
client.ClientCredentials.UserName.UserName = @"foo";
client.ClientCredentials.UserName.Password = @"mrsnuggles";
```

В приведенном выше примере, если появится сообщение «Не хватило trampolines типа 0» можно увеличить количество trampolines тип 0 путем добавления `–aot “trampolines={number of trampolines}”` аргумент для сборки. Дополнительные сведения см. в разделе [Устранение неполадок](~/ios/troubleshooting/troubleshooting.md#trampolines).

Дополнительные сведения о базовой аутентификации HTTP, несмотря на то что в контексте веб-службы REST, в разделе [проверки подлинности веб-службы RESTful](~/xamarin-forms/data-cloud/authentication/rest.md).

## <a name="summary"></a>Сводка

В этом руководстве было продемонстрировано, как использовать другой веб-технологий, службы. Рассматриваются связи с службы REST, SOAP служб и служб Windows Communication Foundation.

## <a name="related-links"></a>Связанные ссылки

- [Пример веб-службы](https://developer.xamarin.com/samples/mobile/WebServices/WebServiceSamples/)
- [Веб-служб в Xamarin.Forms](~/xamarin-forms/data-cloud/index.md)
- [Средство ServiceModel Metadata Utility Tool (svcutil.exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
- [BasicHttpBinding](http://msdn.microsoft.com/library/system.servicemodel.basichttpbinding.aspx)
