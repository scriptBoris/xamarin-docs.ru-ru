---
title: Часть 5. Практические стратегии совместного использования кода
description: В этом документе обсуждаются практические стратегии для сценариев, таких как базы данных, доступ к файлам, сетевые операции и асинхронного кода совместного использования кода.
ms.prod: xamarin
ms.assetid: 328D042A-FF78-A7B6-1574-B5AF49A1AADB
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: b20760d3e8d7e168b0f0508222d8ae0b743a9368
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53058916"
---
# <a name="part-5---practical-code-sharing-strategies"></a>Часть 5. Практические стратегии совместного использования кода

В этом разделе приведены примеры того, как совместное использование кода для распространенных сценариев приложений.



## <a name="data-layer"></a>Уровень данных

Уровень данных состоит из подсистемы хранилища и методы для чтения и записи данных. Производительность, гибкость и кросс платформенной совместимости SQLite для кроссплатформенных приложений Xamarin рекомендуется компонент database engine.
Она может работать на разнообразных платформах, включая Windows, Android, iOS и Mac.


### <a name="sqlite"></a>SQLite

SQLite — это реализация базы данных с открытым исходным кодом. Исходный код и документация находятся в [SQLite.org](http://www.sqlite.org/). Поддержка SQLite доступна на каждой платформы мобильных устройств:

-  **iOS** — встроенной в операционную систему.
- **Android** — встроенной в операционную систему с момента Android 2.2 (API уровня 10).
- **Windows** — см. в разделе [SQLite для универсальной платформы Windows расширение](https://visualstudiogallery.msdn.microsoft.com/4913e7d5-96c9-4dde-a1a1-69820d615936).


Даже с ядром СУБД, доступна на всех платформах отличаются собственные методы для доступа к базе данных. Как iOS и Android предоставляют встроенные API для доступа к SQLite, который может быть использован с Xamarin.iOS или Xamarin.Android, тем не менее с помощью собственных методов пакета SDK предлагает возможность совместного использования кода (кроме, возможно SQL-запросы, при условии, что они хранятся как строки) . Дополнительные сведения о поиске функциональные возможности базы данных в собственном для `CoreData` в iOS или Android `SQLiteOpenHelper` класс; поскольку эти параметры не кросс платформенных они выходят за рамки этого документа.



### <a name="adonet"></a>ADO.NET

Поддержка Xamarin.iOS и Xamarin.Android `System.Data` и `Mono.Data.Sqlite` (см. в разделе Xamarin.iOS [документации](~/ios/data-cloud/system.data.md) Дополнительные сведения).
С помощью этих пространств имен позволяет писать код ADO.NET, который работает на обеих платформах. Изменение ссылок проекта для включения `System.Data.dll` и `Mono.Data.Sqlite.dll` и добавьте следующие операторы using в коде:

```csharp
using System.Data;
using Mono.Data.Sqlite;
```

В следующем примере кода будет работать:

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "items.db3");
bool exists = File.Exists (dbPath);
if (!exists)
    SqliteConnection.CreateFile (dbPath);
var connection = new SqliteConnection ("Data Source=" + dbPath);
connection.Open ();
if (!exists) {
    // This is the first time the app has run and/or that we need the DB.
    // Copy a "template" DB from your assets, or programmatically create one like this:
    var commands = new[]{
        "CREATE TABLE [Items] (Key ntext, Value ntext);",
        "INSERT INTO [Items] ([Key], [Value]) VALUES ('sample', 'text')"
    };
    foreach (var command in commands) {
        using (var c = connection.CreateCommand ()) {
            c.CommandText = command;
            c.ExecuteNonQuery ();
        }
    }
}
// use `connection`... here, we'll just append the contents to a TextView
using (var contents = connection.CreateCommand ()) {
    contents.CommandText = "SELECT [Key], [Value] from [Items]";
    var r = contents.ExecuteReader ();
    while (r.Read ())
        Console.Write("\n\tKey={0}; Value={1}",
                r ["Key"].ToString (),
                r ["Value"].ToString ());
}
connection.Close ();
```

Реальных примерах внедрения технологии ADO.NET, очевидно, что бы быть разбиты на различные методы и классы (в этом примере — только для демонстрационных целей).



### <a name="sqlite-net--cross-platform-orm"></a>SQLite-NET-ORM платформ

ORM (или объектно-реляционным модулем сопоставления) пытается упростить хранение данных, которые моделируются в классах. Вместо вручную написания SQL-запросов, создание таблиц или SELECT, INSERT и DELETE данных, которые вручную извлечь из поля и свойства, ORM добавляет слой кода, который делает это для вас. Использование отражения для анализа структуры классов, ORM может автоматически создавать таблицы и столбцы, которые соответствуют класс и создают запросы на чтение и запись данных. Это позволяет коду приложения просто отправку и получение экземпляров объектов продукт ORM, которая берет на себя все операции SQL взгляд изнутри.

SQLite-NET выступает в роли простой продукт ORM, который позволит вам сохранять и получать ваши классы в SQLite. Она скрывает сложность кросс-доступа к SQLite платформы с помощью директивы компилятора и другие приемы.

Функции SQLite NET:

-  Таблицы определяются путем добавления атрибутов к классам модели.
-  Экземпляр базы данных, представленный подкласс `SQLiteConnection` , основной класс в библиотеке SQLite Net.
-  Данные могут быть вставлены, запрашивать и удалять с помощью объектов. Инструкции SQL не являются обязательными (несмотря на то, что можно написать инструкций SQL, при необходимости).
-  Базовые запросы Linq можно выполнять для коллекций, возвращаемых SQLite-NET.


Исходный код и документацию для SQLite-NET доступен по [SQLite-Net на сайте github](https://github.com/praeclarum/sqlite-net) и была реализована в обоих исследований. Простой пример кода SQLite-NET (из *Tasky Pro* практический пример) показан ниже.

Во-первых, `TodoItem` класс использует атрибуты для определения поля должно быть первичного ключа базы данных:

```csharp
public class TodoItem : IBusinessEntity
{
    public TodoItem () {}
    [PrimaryKey, AutoIncrement]
    public int ID { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
}
```

Это позволяет `TodoItem` таблицы будут созданы с помощью следующей строки кода (и никакие инструкции SQL) для `SQLiteConnection` экземпляр:

```csharp
CreateTable<TodoItem> ();
```

Данные в таблице можно также управлять с помощью других методов на `SQLiteConnection` (опять же, не требуя от инструкций SQL):

```csharp
Insert (TodoItem); // 'task' is an instance with data populated in its properties
Update (TodoItem); // Primary Key field must be populated for Update to work
Table<TodoItem>.ToList(); // returns all rows in a collection
```

Ознакомьтесь с исходным кодом практический пример полные примеры.



## <a name="file-access"></a>Доступ к файлам

Доступ к файлам быть ключевой частью любого приложения. Распространенные примеры файлов, которые могут быть частью включаемый приложения:

-  Файлы базы данных SQLite.
-  Пользовательские данные (текст, изображения, звуковые, видео).
-  Загруженные данные для кэширования (изображения, html или PDF-файлов).




### <a name="systemio-direct-access"></a>System.IO прямой доступ

Xamarin.iOS и Xamarin.Android разрешить доступ к файловой системе, с помощью классов в `System.IO` пространства имен.

Каждая платформа имеет различные ограничения доступа, которые необходимо принимать во внимание:

-  iOS приложения выполняются в изолированной среде с помощью доступа очень ограничен файловой системы. Дополнительно Apple определяет, как следует использовать в файловой системе, указав определенные места, сохраненный в резервной копии (и другим пользователям, которые не являются). Ссылаться на [работа с файловой системой в Xamarin.iOS](~/ios/app-fundamentals/file-system.md) руководство для получения дополнительных сведений.
-  Android также ограничивает доступ к некоторые каталоги, связанные с приложением, но она также поддерживает внешние носители (например) SD-карты) и доступ к общим данным.
-  Запретить прямой доступ к файлам Windows Phone 8 (Silverlight) — файлы только могут управляться с помощью `IsolatedStorage`.
-  Проекты WinRT Windows 8.1 и Windows 10 UWP предлагаем только асинхронные операции с файлами с помощью `Windows.Storage` API-интерфейсы, которые отличаются от других платформ.

#### <a name="example-for-ios-and-android"></a>Пример для iOS и Android

Ниже приведен упрощенный пример, в котором записывает и читает текстовый файл.
С помощью `Environment.GetFolderPath` позволяет один и тот же код для запуска на iOS и Android, каждое из которых возвращает допустимый каталог на основе их соглашений файловой системы.

```csharp
string filePath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "MyFile.txt");
System.IO.File.WriteAllText (filePath, "Contents of text file");
Console.WriteLine (System.IO.ReadAllText (filePath));
```

Ссылаться на Xamarin.iOS [работа с файловой системой](~/ios/app-fundamentals/file-system.md) документа, Дополнительные сведения о функции конкретных операций ввода-вывода файловой системы. При написании кода доступа кросс платформенных файл, помните, что некоторые файловые системы чувствительны к регистру и разделителей другой каталог. Рекомендуется всегда использовать тот же регистр для имен файлов и `Path.Combine()` метод при создании пути к файлу или каталогу.



### <a name="windowsstorage-for-windows-8-and-windows-10"></a>Windows.Storage для Windows 8 и Windows 10

*Создание мобильных приложений с помощью Xamarin.Forms* [книги](https://developer.xamarin.com/r/xamarin-forms/book/)
[Глава 20. Асинхронный и файловый ввод-вывод](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf) включает в себя [примеры для Windows 8.1 и Windows 10](https://github.com/xamarin/xamarin-forms-book-preview-2/tree/master/Chapter20).

С помощью [ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md) можно читать и файл, файлы на этих платформах, с помощью поддерживаемых API:

```csharp
StorageFolder localFolder = ApplicationData.Current.LocalFolder;
IStorageFile storageFile = await localFolder.CreateFileAsync("MyFile.txt",
                                        CreationCollisionOption.ReplaceExisting);
await FileIO.WriteTextAsync(storageFile, "Contents of text file");
```

Ссылаться на [глава книги](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf) для получения дополнительных сведений.


<a name="Isolated_Storage" />

### <a name="isolated-storage-on-windows-phone-7--8-silverlight"></a>Изолированное хранилище в Windows Phone 7 и 8 (Silverlight)

Изолированное хранилище — общий API для сохранения и загрузки файлов во всех iOS, Android и более старых платформ Windows Phone.

Это механизм по умолчанию для доступа к файлам в Windows Phone (Silverlight), который был реализован в Xamarin.iOS и Xamarin.Android разрешить общий доступ к файлам код для записи. `System.IO.IsolatedStorage` Класс можно ссылаться на всех трех платформах в [общий проект](~/cross-platform/app-fundamentals/shared-projects.md).

Ссылаться на [изолированного хранилища Обзор для Windows Phone](http://msdn.microsoft.com/library/windowsphone/develop/ff402541(v=vs.105).aspx) Дополнительные сведения.

API изолированного хранилища не доступны в [переносимые библиотеки классов](~/cross-platform/app-fundamentals/pcl.md). Для тех PCL [PCLStorage NuGet](https://pclstorage.codeplex.com/)



### <a name="cross-platform-file-access-in-pcls"></a>Доступ к файлам между различными платформами в PCL

Имеется также совместимые с PCL Nuget — [PCLStorage](https://www.nuget.org/packages/PCLStorage/) —, доступ к файлам кросс платформенные средства, для Xamarin поддерживаемые платформы и новейшим интерфейсам API Windows.


## <a name="network-operations"></a>Сетевые операции

Большинство мобильных приложений будет иметь сетевой компонент, например:

-  Загрузка изображений, видео и аудио (например) эскизы, фотографии, музыка).
-  Загрузка документов (например) HTML, PDF).
-  Отправка пользовательских данных (таких как фотографии или текст).
-  Доступ к веб-служб или сторонним поставщиком API-интерфейсы (включая SOAP, XML или JSON).


.NET Framework предоставляет несколько разных классов для доступа к сетевым ресурсам: `HttpClient`, `WebClient`, и `HttpWebRequest`.

### <a name="httpclient"></a>HttpClient

`HttpClient` В класс `System.Net.Http` пространство имен доступно в Xamarin.iOS, Xamarin.Android и большинство платформ Windows. Существует [Nuget клиентской библиотеки Microsoft HTTP](https://www.nuget.org/packages/Microsoft.Net.Http/) , можно использовать для переноса этого API в переносимой библиотеки классов (и Windows Phone 8 Silverlight).

```csharp
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get, "https://xamarin.com");
var response = await myClient.SendAsync(request);
```

### <a name="webclient"></a>WebClient

`WebClient` Класс предоставляет простой API для получения удаленных данных с удаленных серверов.

Универсальная платформа Windows операций *необходимо* быть async, несмотря на то, что Xamarin.iOS и Xamarin.Android поддерживает синхронные операции (которые могут выполняться в фоновых потоках).

Код для простого ожидающее `WebClient` операции:

```csharp
var webClient = new WebClient ();
webClient.DownloadStringCompleted += (sender, e) =>
{
    var resultString = e.Result;
    // do something with downloaded string, do UI interaction on main thread
};
webClient.Encoding = System.Text.Encoding.UTF8;
webClient.DownloadStringAsync (new Uri ("http://some-server.com/file.xml"));
```

 `WebClient` также имеет `DownloadFileCompleted` и `DownloadFileAsync` получения двоичных данных.

<a name="HttpWebRequest" />

### <a name="httpwebrequest"></a>httpWebRequest

`HttpWebRequest` предлагает дополнительные настройки по сравнению со `WebClient` и поэтому требует больше кода для использования.

Код для простого синхронного `HttpWebRequest` операции:

```csharp
var request = HttpWebRequest.Create(@"http://some-server.com/file.xml ");
request.ContentType = "text/xml";
request.Method = "GET";
using (HttpWebResponse response = request.GetResponse() as HttpWebResponse)
{
    if (response.StatusCode != HttpStatusCode.OK)
        Console.WriteLine("Error fetching data. Server returned status code: {0}", response.StatusCode);
    using (StreamReader reader = new StreamReader(response.GetResponseStream()))
    {
        var content = reader.ReadToEnd();
        // do something with downloaded string, do UI interaction on main thread
    }
}
```

Пример в нашей [документации веб-служб](~/cross-platform/data-cloud/web-services/index.md).

 <a name="Reachability" />


### <a name="reachability"></a>Доступность

Мобильные устройства работают в различных условиях сети быстро Wi-Fi или 4G-подключениям к низкой приемных и медленными каналами данных EDGE. По этой причине рекомендуется определить, есть ли доступ к сети и если таким образом, какой тип доступен, перед попыткой подключения к удаленным серверам.

Следующие действия на мобильное приложение может уйти в таких ситуациях.

-  Если сеть недоступна, попросите пользователя. Если они вручную, он был отключен (например) Режим "в самолете" или отключив Wi-Fi) они могут решить проблему.
-  Если подключение установлено 3G, приложения могут вести себя иначе (например, Apple не поддерживает приложения больше 20 МБ для загрузки более 3G). Приложения могут использовать эти сведения, чтобы предупредить пользователя о чрезмерной загрузки время ожидания при получении больших файлов.
-  Даже если сеть доступна, рекомендуется проверить соединение с целевым сервером перед запуском других запросов. Это многократно предотвращения сетевых операций приложения, по истечении времени ожидания и также позволит более информативное сообщение об ошибке для отображения пользователю.


Существует [пример Xamarin.iOS](https://github.com/xamarin/monotouch-samples/tree/master/ReachabilitySample) доступных (основанная на Apple [достижимость пример кода](http://developer.apple.com/library/ios/#samplecode/Reachability/Introduction/Intro.html) ) помогут Определение доступности сети.


## <a name="webservices"></a>Веб-службы

См. в статье на [работа с веб-службами](~/cross-platform/data-cloud/web-services/index.md), где приведены все доступ к REST, конечные точки SOAP и WCF, с помощью Xamarin.iOS. Его можно самостоятельное запросов веб-службы и обработки ответов, тем не менее есть библиотеки сделать это гораздо проще, включая Azure, RestSharp и ServiceStack. Даже основные операции WCF может осуществляться в приложениях Xamarin.

### <a name="azure"></a>Azure

Microsoft Azure — это облачная платформа, которая предоставляет широкий спектр служб для мобильных приложений, включая хранение данных и синхронизации и Push-уведомлений.

Посетите [azure.microsoft.com](https://azure.microsoft.com/) попробуйте сделать это бесплатно.

### <a name="restsharp"></a>RestSharp

RestSharp — это библиотека .NET, которое может быть включено в мобильных приложениях для предоставления клиенту REST, который упрощает доступ к веб-служб. Она помогает, предоставляя простой API для запроса данных и проанализировать Ответ REST. RestSharp может быть полезно

[Веб-сайт RestSharp](http://restsharp.org/) содержит [документации](https://github.com/restsharp/RestSharp/wiki) о том, как реализовать с помощью клиента REST с помощью RestSharp.
RestSharp примеры Xamarin.iOS и Xamarin.Android [github](https://github.com/restsharp/RestSharp/).

Также имеется фрагмент кода Xamarin.iOS в наших [документации веб-служб](~/cross-platform/data-cloud/web-services/index.md).

 <a name="ServiceStack" />


### <a name="servicestack"></a>ServiceStack

В отличие от RestSharp ServiceStack — это обоих серверного решения для размещения веб-службы, а также клиентской библиотеки, который может быть реализован в мобильных приложениях для доступа к этим службам.

[ServiceStack веб-сайт](http://servicestack.net/) объясняющее назначение этого проекта и ссылки на документ и примеры кода. Примеры включают полная реализация на стороне сервера веб-службы, а также различные клиентские приложения, доступ к нему.

Существует [пример Xamarin.iOS](http://www.servicestack.net/monotouch/remote-info/) на веб-сайт ServiceStack и фрагмент кода в нашей [документации веб-служб](~/cross-platform/data-cloud/web-services/index.md).


### <a name="wcf"></a>WCF

Средства Xamarin могут помочь вам использовать некоторые службы Windows Communication Foundation (WCF). Как правило Xamarin поддерживает то же подмножество WCF, которая поставляется вместе со средой выполнения Silverlight в стороне клиента. Это включает в себя наиболее распространенные кодировку и протокол реализации WCF: транспорта кодировкой текста сообщений SOAP по протоколу HTTP с помощью протокола `BasicHttpBinding`.

Из-за размера и сложности платформы WCF могут существовать текущие и будущие службами, которые будут выходят за пределы области действия поддерживается доменом клиента подмножество Xamarin. Кроме того поддержка WCF требует применения средств доступно только в среде Windows для создания прокси-сервера.

 <a name="Threading" />


## <a name="threading"></a>Потоки

Скорость реагирования приложения важно для мобильных приложений — пользователи ожидают, что приложения для загрузки и быстро выполнять. «Замороженной» экран, на котором будет отображаться перестает принимать входные данные пользователя, чтобы указать, что произошло аварийное завершение приложения, поэтому очень важно не для перегрузки поток пользовательского интерфейса с выполняющейся длительное время блокирует вызовы, такие как сетевые запросы или медленных локальных операций (например, при распаковке файла). В частности, процесс запуска не должен содержать длительно выполняемых задач — всех мобильных платформ прервет приложение, которое занимает слишком много времени, для загрузки.

Это означает, что пользовательский интерфейс должны реализовывать «индикатор выполнения» или в противном случае «возможности» пользовательский Интерфейс, который не займет много времени для отображения и асинхронные задачи, для выполнения фоновых задач. Для выполнения фоновых задач необходимо использование потоков, что означает потребностей фоновой задачи способ обмена данными в основной поток для отображения хода выполнения, или когда они будут завершены.

 <a name="Parallel_Task_Library" />


### <a name="parallel-task-library"></a>Библиотека параллельных задач

Задачи, созданные с помощью библиотеки параллельных задач может выполняться асинхронно и возвращать их вызывающем потоке, что делает их очень полезен для активации длительных операций без блокировки пользовательского интерфейса.

Операция простых параллельных задач может выглядеть следующим образом:

```csharp
using System.Threading.Tasks;
void MainThreadMethod ()
{
    Task.Factory.StartNew (() => wc.DownloadString ("http://...")).ContinueWith (
        t => label.Text = t.Result, TaskScheduler.FromCurrentSynchronizationContext()
    );
}
```

Ключом является `TaskScheduler.FromCurrentSynchronizationContext()` которого будет повторно использовать SynchronizationContext.Current поток, вызывающий метод (здесь основной поток, на котором выполняется `MainThreadMethod`) как способ маршалинга вызовов обратной передачи в этот поток. Это означает, что если метод вызывается в потоке пользовательского интерфейса, он будет выполняться `ContinueWith` операции в потоке пользовательского интерфейса.

Если код запускается задачи из других потоков, используйте следующий шаблон для создания ссылки на поток пользовательского интерфейса и задачи по-прежнему может выполнять обратный вызов к нему:

```csharp
static Context uiContext = TaskScheduler.FromCurrentSynchronizationContext();
```

 <a name="Invoking_on_the_UI_Thread" />


### <a name="invoking-on-the-ui-thread"></a>Вызов в потоке пользовательского интерфейса

Для кода, не использовать библиотеку параллельных задач каждая платформа имеет свой собственный синтаксис для маршалинга операций в поток пользовательского интерфейса:

-  **iOS** — `owner.BeginInvokeOnMainThread(new NSAction(action))`
-  **Android** — `owner.RunOnUiThread(action)`
-  **Xamarin.Forms** — `Device.BeginInvokeOnMainThread(action)`
-  **Windows** — `Deployment.Current.Dispatcher.BeginInvoke(action)`



IOS и Android синтаксис требует класса «context» доступен в том, что означает, что код должен передать этот объект в методы, которые требуется обратный вызов в потоке пользовательского интерфейса.

Чтобы вызывать потока пользовательского интерфейса в общем коде, выполните [пример IDispatchOnUIThread](http://www.slideshare.net/follesoe/cross-platform-mobile-apps-using-net) (любезно предоставлено [ @follesoe ](http://jonas.follesoe.no/)). Объявление и программу, чтобы `IDispatchOnUIThread` интерфейс в общем коде, а затем реализовать классы платформы, как показано ниже:

```csharp
// program to the interface in shared code
public interface IDispatchOnUIThread {
    void Invoke (Action action);
}
// iOS
public class DispatchAdapter : IDispatchOnUIThread {
    public readonly NSObject owner;
    public DispatchAdapter (NSObject owner) {
        this.owner = owner;
    }
    public void Invoke (Action action) {
        owner.BeginInvokeOnMainThread(new NSAction(action));
    }
}
// Android
public class DispatchAdapter : IDispatchOnUIThread {
    public readonly Activity owner;
    public DispatchAdapter (Activity owner) {
        this.owner = owner;
    }
    public void Invoke (Action action) {
        owner.RunOnUiThread(action);
    }
}
// WP7
public class DispatchAdapter : IDispatchOnUIThread {
    public void Invoke (Action action) {
        Deployment.Current.Dispatcher.BeginInvoke(action);
    }
}
```

Разработчикам Xamarin.Forms следует использовать [ `Device.BeginInvokeOnMainThread` ](~/xamarin-forms/platform/device.md#Device_BeginInvokeOnMainThread) в общем коде (общие проекты или переносимой библиотеки Классов).

 <a name="Platform_and_Device_Capabilities_and_Degradation" />


## <a name="platform-and-device-capabilities-and-degradation"></a>Платформы и возможности устройств и снижение

Дополнительно конкретные примеры задач, связанных с разными возможностями приведены в документации по возможностям платформы. Он имеет дело с обнаружение различные возможности и как производительность понижалась корректно приложению предоставлять эффективное взаимодействие с пользователем, даже в том случае, если приложение не может работать на свой потенциал.
