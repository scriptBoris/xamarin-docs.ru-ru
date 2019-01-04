---
title: Обработка файлов в Xamarin.Forms
description: Обработку файлов в Xamarin.Forms можно выполнить с помощью кода в библиотеке .NET Standard или внедренных ресурсов.
ms.prod: xamarin
ms.assetid: 9987C3F6-5F04-403B-BBB4-ECB024EA6CC8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2018
ms.openlocfilehash: 93218289cefe26c523bf14721202f7ec4743f022
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53057617"
---
# <a name="file-handling-in-xamarinforms"></a>Обработка файлов в Xamarin.Forms

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/)

_Обработку файлов в Xamarin.Forms можно выполнить с помощью кода в библиотеке .NET Standard или внедренных ресурсов._

## <a name="overview"></a>Обзор

Код Xamarin.Forms выполняется на нескольких платформах, каждая из которых имеет свою собственную файловую систему. Ранее это означало, что чтение и запись файлов проще всего выполнялись с помощью собственных API файлов на каждой платформе. Кроме того, внедренные ресурсы позволяют проще распространять файлы данных с приложением. Однако с помощью .NET Standard 2.0 можно совместно использовать код доступа к файлам в библиотеках .NET Standard.

Дополнительные сведения об обработке файлов изображений см. на странице [Работа с изображениями](~/xamarin-forms/user-interface/images.md).

<a name="Loading_and_Saving_Files" />

## <a name="saving-and-loading-files"></a>Сохранение и загрузка файлов

Классы `System.IO` можно использовать для доступа к файловой системе на каждой платформе. Класс `File` позволяет создавать, удалять и считывать файлы, а класс и `Directory` — создавать, удалять или перечислять содержимое каталогов. Вы также можете использовать подклассы `Stream`, способные полнее контролировать операции с файлами (например, сжатие или поиск позиции в файле).

Текстовый файл может быть записан с помощью метода `File.WriteAllText`:

```csharp
File.WriteAllText(fileName, text);
```

Текстовый файл может быть считан с помощью метода `File.ReadAllText`:

```csharp
string text = File.ReadAllText(fileName);
```

Кроме того, метод `File.Exists` определяет, существует ли заданный файл:

```csharp
bool doesExist = File.Exists(fileName);
```

Путь к файлу на каждой платформе можно определить из библиотеки .NET Standard, используя значение перечисления [`Environment.SpecialFolder`](xref:System.Environment.SpecialFolder) в качестве первого аргумента метода `Environment.GetFolderPath`. Затем его можно объединить с именем файла с помощью метода `Path.Combine`:

```csharp
string fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "temp.txt");
```

Эти операции продемонстрированы в примере приложения, включающем в себя страницу, которая сохраняет и загружает текст:

[![Сохранение и загрузка текст](files-images/saveandload-sml.png "Сохранение и загрузка файлов в приложении")](files-images/saveandload.png#lightbox "Сохранение и загрузка файлов в приложении")

<a name="Loading_Files_Embedded_as_Resources" />

## <a name="loading-files-embedded-as-resources"></a>Загрузка файлов, внедряемых в качестве ресурсов

Чтобы внедрить файл в сборку **.NET Standard**, создайте или добавьте файл и убедитесь, что задана конфигурация **Build Action (Действие при сборке): EmbeddedResource**.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Настройка действия при сборке для внедренного ресурса](files-images/vs-embeddedresource-sml.png "Настройка BuildAction EmbeddedResource")](files-images/vs-embeddedresource.png#lightbox "Настройка BuildAction EmbeddedResource")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![Текстовый файл, внедренный в PCL, настройка действия при сборке для внедренного ресурса](files-images/xs-embeddedresource-sml.png "Настройка BuildAction EmbeddedResource")](files-images/xs-embeddedresource.png#lightbox "Настройка BuildAction EmbeddedResource")

-----

`GetManifestResourceStream` используется для доступа к внедренному файлу с помощью его **идентификатора ресурса**. По умолчанию идентификатор ресурса представляет собой имя файла с префиксом в виде пространства имен по умолчанию для проекта, куда он внедрен. В этом случае используется сборка **WorkingWithFiles** и имя файла **PCLTextResource.txt**, поэтому идентификатор ресурса имеет вид `WorkingWithFiles.PCLTextResource.txt`.

```csharp
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(LoadResourceText)).Assembly;
Stream stream = assembly.GetManifestResourceStream("WorkingWithFiles.PCLTextResource.txt");
string text = "";
using (var reader = new System.IO.StreamReader (stream)) {
    text = reader.ReadToEnd ();
}
```

После этого можно воспользоваться переменной `text`, чтобы отобразить текст или использовать его в коде иным образом. Этот снимок экрана [примера приложения](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/) отображает текст, отрисованный в элементе управления `Label`.

 [![Текстовый файл, внедренный в PCL](files-images/pcltext-sml.png "Внедренный в PCL текстовый файл, отображаемый в приложении")](files-images/pcltext.png#lightbox "Внедренный в PCL текстовый файл, отображаемый в приложении")

Загрузка и десериализация XML выполняется так же просто. В следующем коде показан XML-файл, который загружается и десериализируется из ресурса, а затем привязывается к `ListView` для отображения. Этот XML-файл содержит массив объектов `Monkey` (класс определен в примере кода).

```csharp
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(LoadResourceText)).Assembly;
Stream stream = assembly.GetManifestResourceStream("WorkingWithFiles.PCLXmlResource.xml");
List<Monkey> monkeys;
using (var reader = new System.IO.StreamReader (stream)) {
    var serializer = new XmlSerializer(typeof(List<Monkey>));
    monkeys = (List<Monkey>)serializer.Deserialize(reader);
}
var listView = new ListView ();
listView.ItemsSource = monkeys;
```

 [![Внедренный в PCL XML-файл, отображаемый в ListView](files-images/pclxml-sml.png "Внедренный XML-файл в PCL, отображаемый в ListView")](files-images/pclxml.png#lightbox "Внедренный XML-файл в PCL, отображаемый в ListView")

<a name="Embedding_in_Shared_Projects" />

## <a name="embedding-in-shared-projects"></a>Внедрение в общие проекты

Общие проекты также могут содержать файлы в виде внедренных ресурсов, но так как содержимое общего проекта компилируется в ссылающиеся проекты, префикс, используемый для идентификаторов ресурса внедренных файлов, может измениться. Это означает, что идентификатор ресурса для каждого внедренного файла может быть разным для каждой платформы.

Существует два решения этой проблемы в общих проектах:

-  **Синхронизация проектов** — можно изменить свойства проекта для каждой платформы, чтобы использовать **одни и те же** имя сборки и пространство имен по умолчанию. Затем это значение можно жестко задать в качестве префикса для идентификаторов внедренных ресурсов в общем проекте.
-  **Директивы компилятора #if** — можно использовать директивы компилятора, чтобы задать правильный префикс идентификатора ресурса и использовать это значение для динамического формирования необходимого идентификатора ресурса.


Ниже приведен код, иллюстрирующий второй вариант. Директивы компилятора используются для выбора жестко заданного префикса ресурса (который обычно такой же, как и пространство имен по умолчанию для ссылающегося проекта). Затем переменная `resourcePrefix` используется для создания допустимого идентификатора ресурса путем сцепления с именем файла внедренного ресурса.

```csharp
#if __IOS__
var resourcePrefix = "WorkingWithFiles.iOS.";
#endif
#if __ANDROID__
var resourcePrefix = "WorkingWithFiles.Droid.";
#endif

Debug.WriteLine("Using this resource prefix: " + resourcePrefix);
// note that the prefix includes the trailing period '.' that is required
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(SharedPage)).Assembly;
Stream stream = assembly.GetManifestResourceStream
    (resourcePrefix + "SharedTextResource.txt");
```

<a name="Organizing_Resources" />

### <a name="organizing-resources"></a>Упорядочение ресурсов

В приведенных выше примерах предполагается, что файл внедряется в корень проекта библиотеки .NET Standard, то есть идентификатор ресурса имеет форму **пространство_имен.имя_файла.расширение**, например `WorkingWithFiles.PCLTextResource.txt` и `WorkingWithFiles.iOS.SharedTextResource.txt`.

Можно упорядочить внедренные ресурсы по папкам. Если внедренный ресурс помещается в папку, ее имя становится частью идентификатора ресурса (разделенного точками), таким образом, формат идентификатора ресурса принимает вид **пространство_имен.папка.имя_файла.расширение**. Если поместить файлы, используемые в примере приложения, в папку **MyFolder**, соответствующие идентификаторы ресурсов примут вид `WorkingWithFiles.MyFolder.PCLTextResource.txt` и `WorkingWithFiles.iOS.MyFolder.SharedTextResource.txt`.

<a name="Debugging_Embedded_Resources" />

### <a name="debugging-embedded-resources"></a>Отладка внедренных ресурсов

Так как иногда сложно понять, почему конкретный ресурс не загружается, можно временно добавить в приложение приведенный ниже код отладки, чтобы убедиться, что ресурсы настроены правильно. Он выводит все известные ресурсы, внедренные в заданную сборку, на панели **Ошибки**, чтобы облегчить отладку проблем с загрузкой ресурсов.

```csharp
using System.Reflection;
// ...
// use for debugging, not in released app code!
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(SharedPage)).Assembly;
foreach (var res in assembly.GetManifestResourceNames()) {
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

## <a name="summary"></a>Сводка

В этой статье показаны некоторые простые операции с файлами для сохранения и загрузки текста на устройстве, а также для загрузки внедренных ресурсов. С помощью .NET Standard 2.0 можно совместно использовать код доступа к файлам в библиотеках .NET Standard.

## <a name="related-links"></a>Связанные ссылки

- [FilesSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/)
- [Примеры Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
- [Работа с файловой системой в Xamarin.iOS](~/ios/app-fundamentals/file-system.md)

