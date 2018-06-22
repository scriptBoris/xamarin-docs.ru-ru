---
title: Обработка файлов в Xamarin.Forms
description: Обработка с помощью Xamarin.Forms файлов может осуществляться с помощью кода в библиотеке .NET Standard или с помощью внедренных ресурсов.
ms.prod: xamarin
ms.assetid: 9987C3F6-5F04-403B-BBB4-ECB024EA6CC8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2018
ms.openlocfilehash: 0be441a7be9777698212e690aca95fdd75e5050f
ms.sourcegitcommit: eac092f84b603958c761df305f015ff84e0fad44
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36310157"
---
# <a name="file-handling-in-xamarinforms"></a>Обработка файлов в Xamarin.Forms

_Обработка с помощью Xamarin.Forms файлов может осуществляться с помощью кода в библиотеке .NET Standard или с помощью внедренных ресурсов._

## <a name="overview"></a>Обзор

Код Xamarin.Forms выполняется на нескольких платформах, каждая из которых имеет свою собственную файловую систему. Ранее это означает, что чтение и запись файлов проще всего выполнялось с помощью собственного API-интерфейсов файлового на каждой платформе. Кроме того внедренные ресурсы являются простым решением распределяйте файлы данных с помощью приложения. Тем не менее со стандартом .NET версии 2.0 имеется возможность совместного использования кода доступа к файлу в библиотеках .NET Standard.

Сведения об обработке файлов изображений [работа с образами](~/xamarin-forms/user-interface/images.md) страницы.

<a name="Loading_and_Saving_Files" />

## <a name="saving-and-loading-files"></a>Сохранение и загрузка файлов

`System.IO` Классы могут использоваться для доступа к файловой системе на каждой платформе. `File` Класс позволяет создавать, удалять и читать файлы и `Directory` класс позволяет создание, удаление или перечисление содержимого папки. Можно также использовать `Stream` подклассов, которые может обеспечить большую степень контроля над операции с файлами (например, сжатие или позицию поиска в файле).

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

Путь к файлу для каждой платформы, можно определить из библиотеки .NET Standard, используя значение [ `Environment.SpecialFolder` ](xref:System.Environment.SpecialFolder) перечисления как первый аргумент `Environment.GetFolderPath` метод. Это может затем быть объединен с файла с `Path.Combine` метод:

```csharp
string fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "temp.txt");
```

Эти операции демонстрируются в примере приложения, включая страницы, который сохраняет и загружает текст:

[![Сохранение и загрузка текст](files-images/saveandload-sml.png "сохранение и загрузка файлов в приложении")](files-images/saveandload.png#lightbox "сохранение и загрузка файлов в приложении")

<a name="Loading_Files_Embedded_as_Resources" />

## <a name="loading-files-embedded-as-resources"></a>Загрузка файлов, внедренных в качестве ресурсов

Чтобы внедрить файл в **.NET Standard** сборки, создание или добавление файла и убедитесь, что **действие при построении: EmbeddedResource**.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Настройка внедренных ресурсов действие построения](files-images/vs-embeddedresource-sml.png "EmbeddedResource BuildAction параметр")](files-images/vs-embeddedresource.png#lightbox "EmbeddedResource BuildAction параметр")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Текстовый файл, внедренных в PCL, Настройка действие построения внедренного ресурса](files-images/xs-embeddedresource-sml.png "EmbeddedResource BuildAction параметр")](files-images/xs-embeddedresource.png#lightbox "EmbeddedResource BuildAction параметр")

-----

`GetManifestResourceStream` используется для доступа к внедренного файла с помощью его **идентификатор ресурса**. По умолчанию, идентификатор ресурса — имя файла, в качестве префикса пространства имен по умолчанию для проекта, который он внедрен - в этом случае сборка является **WorkingWithFiles** и имя файла — **PCLTextResource.txt**, Поэтому идентификатор ресурса `WorkingWithFiles.PCLTextResource.txt`.

```csharp
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(LoadResourceText)).Assembly;
Stream stream = assembly.GetManifestResourceStream("WorkingWithFiles.PCLTextResource.txt");
string text = "";
using (var reader = new System.IO.StreamReader (stream)) {
    text = reader.ReadToEnd ();
}
```

`text` Переменной может затем использоваться для отображения текста или в противном случае используйте его в коде. Этот снимок экрана [пример приложения](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/) показывает текста, отображаемого в `Label` элемента управления.

 [![Текстовый файл, внедренных в PCL](files-images/pcltext-sml.png "внедренные текстовый файл в PCL, отображаемый в приложении")](files-images/pcltext.png#lightbox "внедренные текстовый файл в PCL, отображаемый в приложении")

Загрузка и десериализации XML выполняется так же просто. В следующем коде показано файла XML, загрузки и десериализован из ресурса, а затем привязать к `ListView` для отображения. XML-файл содержит массив `Monkey` объектов (в примере кода определяется класс).

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

 [![XML-файл, внедренных в PCL, отображаемых в ListView](files-images/pclxml-sml.png "внедренный XML-файл в PCL, отображаемых в ListView")](files-images/pclxml.png#lightbox "внедренный XML-файл в PCL, отображаемых в ListView")

<a name="Embedding_in_Shared_Projects" />

### <a name="embedding-in-shared-projects"></a>Внедрение в общих проектов

Общие проекты также могут содержать файлы как внедренные ресурсы, однако так как содержимое общий проект компилируются в ссылающейся проекты, префикс, используемый для внедренный ресурс файла, который можно изменить идентификаторы. Это означает, что идентификатор ресурса для каждого внедренного файла может быть разным для каждой платформы.

Существует два решения этой проблемы с общих проектов:

-  **Синхронизировать проекты** -изменение свойств проекта для каждой платформы, чтобы использовать **же** пространство имен по умолчанию и имя сборки. Затем это значение может быть «жестко задано» как префикс для внедренного ресурса идентификаторов в общий проект.
-  **директивы компилятора #if** -используйте директивы компилятора префикс идентификатора ресурсов и использовать это значение для динамического построения идентификатор необходимого ресурса.


Ниже приведен исходный код, иллюстрирующий второй вариант. Директивы компилятора, используемые для выбора префикс жестко закодировано ресурсов (который обычно является таким же, как пространство имен по умолчанию для ссылающегося проекта). `resourcePrefix` Переменная затем используется для создания идентификатора ресурса допустимым путем объединения его с именем файла внедренных ресурсов.

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

### <a name="organizing-resources"></a>Организация ресурсов

Приведенных выше примерах предполагается, что файл внедрен в корне проекта .NET Standard библиотеки, в котором регистр идентификатор ресурса имеет форму **пространство_имен.имя_файла.расширение**, такие как `WorkingWithFiles.PCLTextResource.txt` и `WorkingWithFiles.iOS.SharedTextResource.txt`.

Можно организовать внедренные ресурсы в папках. При внедренный ресурс помещается в папку, имя папки становится частью Идентификаторы ресурсов (разделенных точками), так что становится формат идентификатора ресурса **Namespace.Folder.Filename.Extension**. Размещение файлов, используемый в пример приложения, в папку **MyFolder** сделает соответствующие идентификаторы ресурсов `WorkingWithFiles.MyFolder.PCLTextResource.txt` и `WorkingWithFiles.iOS.MyFolder.SharedTextResource.txt`.

<a name="Debugging_Embedded_Resources" />

### <a name="debugging-embedded-resources"></a>Отладка внедренные ресурсы

Поскольку иногда бывает трудно понять, почему не загружаемых определенного ресурса, следующий код отладки можно добавить временно в приложение, чтобы гарантировать, что ресурсы настроены правильно. Будут выведены все известные ресурсы, встроенные в данной сборке для **ошибки** прокладки, чтобы помочь в отладке проблем загрузки ресурсов.

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

В этой статье на примере некоторых операций простого файла для сохранения и загрузки текста на устройстве, а также для загрузки внедренных ресурсов. С .NET Standard 2.0 можно совместно использовать код доступа к файлу в библиотеках .NET Standard.

## <a name="related-links"></a>Связанные ссылки

- [FilesSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/)
- [Примеры Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
- [Работа с файловой системой в Xamarin.iOS](~/ios/app-fundamentals/file-system.md)
- [Файлы книги](https://developer.xamarin.com/workbooks/xamarin-forms/application-fundamentals/files/files.workbook)
