---
title: Обработка файлов в Xamarin.Forms
description: Обработка с помощью Xamarin.Forms файлов может осуществляться с помощью кода в библиотеке .NET Standard или с помощью внедренных ресурсов.
ms.prod: xamarin
ms.assetid: 9987C3F6-5F04-403B-BBB4-ECB024EA6CC8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2018
ms.openlocfilehash: ddffb45b8cd8d47371e4ab57f30a467cea45b27d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117776"
---
# <a name="file-handling-in-xamarinforms"></a>Обработка файлов в Xamarin.Forms

_Обработка с помощью Xamarin.Forms файлов может осуществляться с помощью кода в библиотеке .NET Standard или с помощью внедренных ресурсов._

## <a name="overview"></a>Обзор

Код Xamarin.Forms выполняется на нескольких платформах, каждая из которых имеет свою собственную файловую систему. Ранее это означало, что проще всего было чтение и запись файлов выполнено с помощью собственных API файлов на каждой платформе. Кроме того внедренные ресурсы, более простое решение для распространения файлов данных с помощью приложения. Тем не менее с помощью .NET Standard 2.0 имеется возможность совместного использования кода доступа к файлу в библиотеки .NET Standard.

Дополнительные сведения об обработке файлов изображений, см. [работа с образами](~/xamarin-forms/user-interface/images.md) страницы.

<a name="Loading_and_Saving_Files" />

## <a name="saving-and-loading-files"></a>Сохранение и загрузка файлов

`System.IO` Классы могут использоваться для доступа к файловой системе на каждой платформе. `File` Класс позволяет создавать, удалять и читать файлы и `Directory` класс позволяет создавать, удалять или перечислять содержимое каталогов. Можно также использовать `Stream` подклассов, которые может предоставить большую степень контроля над операции с файлами (например, сжатие или позицию поиска в файле).

Текстовый файл могут быть написаны с использованием `File.WriteAllText` метод:

```csharp
File.WriteAllText(fileName, text);
```

Текстовый файл можно считать с помощью `File.ReadAllText` метод:

```csharp
string text = File.ReadAllText(fileName);
```

Кроме того `File.Exists` метод определяет, существует ли указанный файл:

```csharp
bool doesExist = File.Exists(fileName);
```

Путь к файлу на каждой платформе можно определить из библиотеки .NET Standard, используя значение [ `Environment.SpecialFolder` ](xref:System.Environment.SpecialFolder) перечисления как первый аргумент `Environment.GetFolderPath` метод. Это можно объединить с имени файла с `Path.Combine` метод:

```csharp
string fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "temp.txt");
```

Эти операции демонстрируются в примере приложения, включая страницы, которое сохраняет и загружает текст:

[![Сохранение и загрузка текст](files-images/saveandload-sml.png "сохранение и загрузка файлов в приложении")](files-images/saveandload.png#lightbox "сохранение и загрузка файлов в приложении")

<a name="Loading_Files_Embedded_as_Resources" />

## <a name="loading-files-embedded-as-resources"></a>Загрузка файлов, внедренных в качестве ресурсов

Внедрение файла в **.NET Standard** сборки, создать или добавить файл и убедитесь, что **действие при построении: EmbeddedResource**.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Настройка встроенные действия сборки ресурса](files-images/vs-embeddedresource-sml.png "EmbeddedResource BuildAction параметр")](files-images/vs-embeddedresource.png#lightbox "EmbeddedResource BuildAction параметр")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![Текстовый файл, внедренный в PCL, Настройка действие сборки внедренного ресурса](files-images/xs-embeddedresource-sml.png "EmbeddedResource BuildAction параметр")](files-images/xs-embeddedresource.png#lightbox "EmbeddedResource BuildAction параметр")

-----

`GetManifestResourceStream` используется для доступа к внедренного файла с помощью его **идентификатор ресурса**. По умолчанию, идентификатор ресурса имеет имя файла с префиксом пространства имен по умолчанию для проекта, который он внедрен - в этом случае сборка является **WorkingWithFiles** и имя файла — **PCLTextResource.txt**, Поэтому идентификатор ресурса имеет `WorkingWithFiles.PCLTextResource.txt`.

```csharp
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(LoadResourceText)).Assembly;
Stream stream = assembly.GetManifestResourceStream("WorkingWithFiles.PCLTextResource.txt");
string text = "";
using (var reader = new System.IO.StreamReader (stream)) {
    text = reader.ReadToEnd ();
}
```

`text` Переменная может затем использоваться для отображения текста или в противном случае использовать его в коде. Этот снимок экрана [пример приложения](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/) отображается текст, подготавливается к просмотру в `Label` элемента управления.

 [![Текстовый файл, внедренный в PCL](files-images/pcltext-sml.png "внедренный текстовый файл в PCL, отображаемый в приложении")](files-images/pcltext.png#lightbox "внедренный текстовый файл в PCL, отображаемый в приложении")

Загрузке и десериализации XML выполняется так же просто. В следующем коде показано XML файлу, который загружен и десериализации из ресурса, а затем привязан к `ListView` для отображения. XML-файл содержит массив `Monkey` объекты (класс определен в образце кода).

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

 [![XML-файл, внедренный в PCL, отображаемый в ListView](files-images/pclxml-sml.png "внедренный XML-файл в PCL, отображаемый в ListView")](files-images/pclxml.png#lightbox "внедренный XML-файл в PCL, отображаемый в ListView")

<a name="Embedding_in_Shared_Projects" />

### <a name="embedding-in-shared-projects"></a>Внедрение в общих проектов

Общие проекты также могут содержать файлы как внедренные ресурсы, но так как содержимое общего проекта компилируются в ссылающейся проекты, префикс, используемый для внедренных файл ресурса, который можно изменить идентификаторы. Это означает, что идентификатор ресурса для каждого внедренного файла может быть разным для каждой платформы.

Существует два решения этой проблемы, с помощью общих проектов.

-  **Синхронизировать проекты** -редактирования свойств проекта для каждой платформы использовать **же** имя и значение по умолчанию пространства имен сборки. Это значение затем может быть «жестко» как префикс для внедренного ресурса идентификаторов в общий проект.
-  **директивы компилятора #if** -используйте директивы компилятора префикс идентификатора необходимого ресурса и использовать это значение для динамического построения идентификатор необходимого ресурса.


Ниже приведен код, иллюстрирующий второй вариант. Директивы компилятора используются для выбора префикс ресурса жестко (которое обычно так же, как пространство имен по умолчанию для ссылающегося проекта). `resourcePrefix` Переменная затем используется для создания идентификатора допустимый ресурс, объединяя его с именем файла внедренных ресурсов.

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

Приведенных выше примерах предполагается, что файл будет внедрен в корне проекта библиотеки .NET Standard, в котором регистр идентификатор ресурса имеет форму **пространство_имен.имя_файла.расширение**, такие как `WorkingWithFiles.PCLTextResource.txt` и `WorkingWithFiles.iOS.SharedTextResource.txt`.

Это возможность организации внедренные ресурсы в папках. Если внедренный ресурс находится в папке, имя папки становится частью идентификатора ресурса (разделенных точками), таким образом, чтобы формат идентификатора ресурсов становится **Namespace.Folder.Filename.Extension**. Поместив файлы, используемые в примере приложения в папку **MyFolder** сделает соответствующих идентификаторов ресурсов `WorkingWithFiles.MyFolder.PCLTextResource.txt` и `WorkingWithFiles.iOS.MyFolder.SharedTextResource.txt`.

<a name="Debugging_Embedded_Resources" />

### <a name="debugging-embedded-resources"></a>Отладка внедренные ресурсы

Так как иногда сложно понять, почему не загрузке конкретного ресурса, следующий код отладки можно временно добавляется приложения чтобы убедиться, что ресурсы настроены правильно. Будут выведены все известные ресурсов, внедренных в заданной сборке для **ошибки** панели для отладки проблем загрузки ресурсов.

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

В этой статье было показано, некоторые операции простой файл для сохранения и загрузки текста на устройстве, а также для загрузки внедренных ресурсов. С помощью .NET Standard 2.0 существует возможность совместного использования кода доступа к файлу в библиотеки .NET Standard.

## <a name="related-links"></a>Связанные ссылки

- [FilesSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/)
- [Примеры Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
- [Работа с файловой системой в Xamarin.iOS](~/ios/app-fundamentals/file-system.md)

