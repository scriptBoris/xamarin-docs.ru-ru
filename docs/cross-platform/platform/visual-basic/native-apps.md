---
title: Visual Basic.NET в Xamarin iOS и Android
description: В этом пошаговом руководстве показано, как для создания собственных приложений Xamarin.iOS и Xamarin.Android, использующие бизнес-логику на Visual Basic.NET.
ms.prod: xamarin
ms.assetid: 455fda67-3879-4299-8036-b12840e6a498
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: affebab9bb6b07f204beef24cce2b57444d45e49
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/11/2018
ms.locfileid: "51527304"
---
# <a name="visual-basicnet-in-xamarin-ios-and-android"></a>Visual Basic.NET в Xamarin iOS и Android

[TaskyPortable](/samples/mobile/VisualBasic/TaskyPortableVB/) пример приложения демонстрирует, как можно использовать код Visual Basic, компилируются в переносимой библиотеки классов с помощью Xamarin. Ниже приведены некоторые снимки экрана, полученный приложений, работающих под управлением iOS, Android и Windows Phone.

 [![](native-apps-images/image5.png "iOS, Android и Windows телефонов под управлением приложения, созданного с помощью Visual Basic")](native-apps-images/image5.png#lightbox)

IOS, Android и Windows Phone, проекты, в примере записываются в C#. Пользовательский интерфейс для каждого приложения создается с собственными технологиями (раскадровки, Xml и Xaml соответственно), хотя `TodoItem` управления обеспечивается переносимой библиотеки классов Visual Basic с помощью `IXmlStorage` реализации собственный проект.

## <a name="sample-walkthrough"></a>Практический руководство

В этом руководстве рассматривается, как Visual Basic, был реализован в [TaskyPortableVB](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyPortableVB) пример Xamarin для iOS и Android.

> [!NOTE]
> Просмотрите инструкции на [Visual Basic.NET PCL](/guides/cross-platform/application_fundamentals/pcl/portable_visual_basic_net/) продолжения с помощью этого руководства.

## <a name="visualbasicportablelibrary"></a>VisualBasicPortableLibrary

Переносимые библиотеки классов Visual Basic могут создаваться только в Visual Studio.
Пример библиотеки содержит основные сведения о приложении в четырех файлах Visual Basic:

-  IXmlStorage.vb
-  TodoItem.vb
-  TodoItemManager.vb
-  TodoItemRepositoryXML.vb


### <a name="ixmlstoragevb"></a>IXmlStorage.vb

Поскольку доступ действия с файлами, значительно различаться между платформами, переносимые библиотеки классов не предоставляют `System.IO` файл API-интерфейсы хранилища в любой профиль. Это означает, что если мы хотим напрямую взаимодействовать с файловой системы в нашей переносимого кода, нам нужно обратный вызов наши собственные проекты на каждой платформе.  Путем написания кода Visual Basic с простой интерфейс, который может быть реализован в C# на каждой платформе, может иметь совместного использования кода Visual Basic, который по-прежнему имеет доступ к файловой системе.

В примере кода используется этот простейший интерфейс, который содержит только два метода: для чтения и записи сериализованного XML-файл.

```vb
Public Interface IXmlStorage
    Function ReadXml(filename As String) As List(Of TodoItem)
    Sub WriteXml(tasks As List(Of TodoItem), filename As String)
End Interface
```

iOS, Android и Windows Phone реализации этого интерфейса отображаются далее в этом руководстве.

### <a name="todoitemvb"></a>TodoItem.vb

Этот класс содержит бизнес-объект для использования в приложении. Он будет определен в Visual Basic и совместно с iOS, Android и Windows Phone проектов, которые создаются на языке C#.

Определение класса показано здесь:

```vb
Public Class TodoItem
    Property ID() As Integer
    Property Name() As String
    Property Notes() As String
    Property Done() As Boolean
End Class
```

В примере используется XML-сериализации и десериализации для загрузки и сохранения объекты TodoItem.

### <a name="todoitemmanagervb"></a>TodoItemManager.vb

Класс диспетчера представляется «API» для переносимого кода. Он предоставляет основные операции CRUD для `TodoItem` класс, но без реализации этих операций.

```vb
Public Class TodoItemManager
    Private _repository As TodoItemRepositoryXML
    Public Sub New(filename As String, storage As IXmlStorage)
        _repository = New TodoItemRepositoryXML(filename, storage)
    End Sub
    Public Function GetTask(id As Integer) As TodoItem
        Return _repository.GetTask(id)
    End Function
    Public Function GetTasks() As List(Of TodoItem)
        Return New List(Of TodoItem)(_repository.GetTasks())
    End Function
    Public Function SaveTask(item As TodoItem) As Integer
        Return _repository.SaveTask(item)
    End Function
    Public Function DeleteTask(item As TodoItem) As Integer
        Return _repository.DeleteTask(item.ID)
    End Function
End Class
```

Конструктор принимает экземпляр IXmlStorage в качестве параметра. Это позволяет каждой платформе, чтобы предоставить собственную реализацию работы, обеспечивая переносимого кода, которые описывают другие функциональные возможности, который может использоваться совместно.

### <a name="todoitemrepositoryvb"></a>TodoItemRepository.vb

Класс репозитория содержит логику для управления списком объекты TodoItem. Полный код показан ниже — логика существует главным образом, чтобы управлять уникальное значение идентификатора в TodoItems, так как они добавляются и удаляются из коллекции.

```vb
Public Class TodoItemRepositoryXML
    Private _filename As String
    Private _storage As IXmlStorage
    Private _tasks As List(Of TodoItem)

    ''' <summary>Constructor</summary>
    Public Sub New(filename As String, storage As IXmlStorage)
        _filename = filename
        _storage = storage
        _tasks = _storage.ReadXml(filename)
    End Sub
    ''' <summary>Inefficient search for a Task by ID</summary>
    Public Function GetTask(id As Integer) As TodoItem
        For t As Integer = 0 To _tasks.Count - 1
            If _tasks(t).ID = id Then
                Return _tasks(t)
            End If
        Next
        Return New TodoItem() With {.ID = id}
    End Function
    ''' <summary>List all the Tasks</summary>
    Public Function GetTasks() As IEnumerable(Of TodoItem)
        Return _tasks
    End Function
    ''' <summary>Save a Task to the Xml file
    ''' Calculates the ID as the max of existing IDs</summary>
    Public Function SaveTask(item As TodoItem) As Integer
        Dim max As Integer = 0
        If _tasks.Count > 0 Then
            max = _tasks.Max(Function(t As TodoItem) t.ID)
        End If
        If item.ID = 0 Then
            item.ID = ++max
            _tasks.Add(item)
        Else
            Dim j = _tasks.Where(Function(t) t.ID = item.ID).First()
            j = item
        End If
        _storage.WriteXml(_tasks, _filename)
        Return max
    End Function
    ''' <summary>Removes the task from the XMl file</summary>
    Public Function DeleteTask(id As Integer) As Integer
        For t As Integer = 0 To _tasks.Count - 1
            If _tasks(t).ID = id Then
                _tasks.RemoveAt(t)
                _storage.WriteXml(_tasks, _filename)
                Return 1
            End If
        Next
        Return -1
    End Function
End Class
```

> [!NOTE]
> Этот код является примером механизм хранения данных очень простой.
> Он предоставляется для демонстрации того, как создавать код переносимой библиотеки классов на основе интерфейса для доступа к функциям конкретной платформы (в данном случае, загрузки и сохранения XML-файл). Его он не предназначен для вместо рабочего уровня базы данных будет.

## <a name="ios-android-and-windows-phone-application-projects"></a>iOS, Android и Windows Phone Application Projects

В этом разделе содержит реализации платформы для интерфейса IXmlStorage и показано, как она используется в каждом приложении. Проекты приложений записываются C#.

### <a name="ios-and-android-ixmlstorage"></a>iOS и Android IXmlStorage

Xamarin.iOS и Xamarin.Android предоставить полный `System.IO` функциональные возможности, поэтому можно легко загрузить и сохранить XML-файл, выполнив следующий класс:

```csharp
public class XmlStorageImplementation : IXmlStorage
{
    public XmlStorageImplementation(){}
    public List<TodoItem> ReadXml(string filename)
    {
        if (File.Exists(filename))
        {
            var serializer = new XmlSerializer(typeof(List<TodoItem>));
            using (var stream = new FileStream(filename, FileMode.Open))
            {
                return (List<TodoItem>)serializer.Deserialize(stream);
            }
        }
        return new List<TodoItem>();
    }
    public void WriteXml(List<TodoItem> tasks, string filename)
    {
        var serializer = new XmlSerializer(typeof(List<TodoItem>));
        using (var writer = new StreamWriter(filename))
        {
            serializer.Serialize(writer, tasks);
        }
    }
}
```

В приложении iOS `TodoItemManager` и `XmlStorageImplementation` создаются в **AppDelegate.cs** файл, как показано в этом фрагменте кода. Первые четыре строки просто создаете путь к файлу, где будут храниться данные; Последние две строки показано два класса, экземпляр которого создается.

```csharp
var xmlFilename = "TodoList.xml";
string documentsPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); // Documents folder
string libraryPath = Path.Combine(documentsPath, "..", "Library"); // Library folder
var path = Path.Combine(libraryPath, xmlFilename);
var xmlStorage = new XmlStorageImplementation();
TaskMgr = new TodoItemManager(path, xmlStorage);
```

В приложении Android `TodoItemManager` и `XmlStorageImplementation` создаются в **Application.cs** файл, как показано в этом фрагменте кода. Первые три строки просто создаете путь к файлу, где будут храниться данные; Последние две строки показано два класса, экземпляр которого создается.

```csharp
var xmlFilename = "TodoList.xml";
string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
var path = Path.Combine(libraryPath, xmlFilename);
var xmlStorage = new AndroidTodo.XmlStorageImplementation();
TaskMgr = new TodoItemManager(path, xmlStorage);
```

Остальная часть кода приложения в основном беспокоиться о пользовательском интерфейсе и не используете `TaskMgr` класс для загрузки и сохранения `TodoItem` классы.

### <a name="windows-phone-ixmlstorage"></a>IXmlStorage Windows Phone

Windows Phone не предоставляет полный доступ к файловой системе устройства, вместо предоставления IsolatedStorage API. `IXmlStorage` Реализации для Windows Phone выглядит следующим образом:

```csharp
public class XmlStorageImplementation : IXmlStorage
{
    public XmlStorageImplementation(){}
    public List<TodoItem> ReadXml(string filename)
    {
        IsolatedStorageFile fileStorage = IsolatedStorageFile.GetUserStoreForApplication();
        if (fileStorage.FileExists(filename))
        {
            var serializer = new XmlSerializer(typeof(List<TodoItem>));
            using (var stream = new StreamReader(new IsolatedStorageFileStream(filename, FileMode.Open, fileStorage)))
            {
                return (List<TodoItem>)serializer.Deserialize(stream);
            }
        }
        return new List<TodoItem>();
    }
    public void WriteXml(List<TodoItem> tasks, string filename)
    {
        IsolatedStorageFile fileStorage = IsolatedStorageFile.GetUserStoreForApplication();
        var serializer = new XmlSerializer(typeof(List<TodoItem>));
        using (var writer = new StreamWriter(new IsolatedStorageFileStream(filename, FileMode.OpenOrCreate, fileStorage)))
        {
            serializer.Serialize(writer, tasks);
        }
    }
}
```

`TodoItemManager` И `XmlStorageImplementation` создаются в **App.xaml.cs** файл, как показано в этом фрагменте кода.

```csharp
var filename = "TodoList.xml";
var xmlStorage = new XmlStorageImplementation();
TodoMgr = new TodoItemManager(filename, xmlStorage);
```

Остальная часть приложения Windows Phone состоит из Xaml и C# для создания пользовательского интерфейса и использовать `TodoMgr` класс для загрузки и сохранения `TodoItem` объектов.

## <a name="visual-basic-pcl-in-visual-studio-for-mac"></a>Visual Basic библиотекой PCL в Visual Studio для Mac

Visual Studio для Mac не поддерживает язык Visual Basic — не удается создать или компиляции проектов Visual Basic с помощью Visual Studio для Mac.

Visual Studio для Mac поддержка переносимых библиотек классов означает, что он может ссылаться на сборки переносимой библиотеки Классов, которые были созданы с помощью Visual Basic.

В этом разделе объясняется, как скомпилировать в сборку переносимой библиотеки Классов в Visual Studio и убедитесь, что он будет храниться в системе управления версиями и ссылаться на другие проекты.

### <a name="keeping-the-pcl-output-from-visual-studio"></a>Сохранение выходных данных PCL из Visual Studio

По умолчанию большинство систем управления версиями (включая TFS и Git) будет настроен для игнорирования **/bin/** не будут храниться в каталоге, что означает скомпилированную сборку переносимой библиотеки Классов. Это означает, что необходимо вручную скопировать его в любые компьютеры под управлением Visual Studio для Mac добавить ссылку на него.

Чтобы убедиться, что системы управления версиями можно хранить выходные данные сборки переносимой библиотеки Классов, можно создать скрипт после сборки, который копирует его в корневую папку проекта. Этот шаг после сборки гарантирует сборки можно легко добавить в систему управления версиями и совместно с другими проектами.

#### <a name="visual-studio-2017"></a>Visual Studio 2017

1. Правой кнопкой мыши проект и выберите **свойства > события построения** раздел.

2. Добавить _после построения_ скрипт, который копирует выходные данные библиотеки DLL из этого проекта в корневом каталоге проекта (который находится за пределами **/bin/**). В зависимости от конфигурации управления версиями библиотека DLL должна появиться возможность добавить в систему управления версиями.

  [![](native-apps-images/image6-vs-sml.png "События сборки сценарий после построения для копирования VB DLL")](native-apps-images/image6-vs.png#lightbox)

#### <a name="visual-studio-2015"></a>Visual Studio 2015

1.  Правой кнопкой мыши проект и выберите **свойства > компиляции** , затем убедитесь, все конфигурации выбран в поле объединение левого верхнего. Нажмите кнопку **события сборки...**  кнопки в правом нижнем углу.

    [![](native-apps-images/image6.png "В разделе компиляции свойства проекта")](native-apps-images/image6.png#lightbox)

1.  Добавьте скрипт после сборки, который копирует выходные данные библиотеки DLL из этого проекта в корневом каталоге проекта (который находится за пределами **/bin/** ). В зависимости от конфигурации управления версиями библиотека DLL должна появиться возможность добавить в систему управления версиями.

    [![](native-apps-images/image7.png "Окне события сборки")](native-apps-images/image7.png#lightbox)

#### <a name="all-versions"></a>Все версии

Следующий раз выполняется сборка проекта, сборку переносимой библиотеки классов, которые будут скопированы в корневую папку проекта и проверка в фиксации и отправки изменений библиотеки DLL можно хранения (чтобы его можно скачать на компьютер Mac с помощью Visual Studio для Mac).

  [![](native-apps-images/image8-sml.png "Расположение файла выходной сборки Visual Basic")](native-apps-images/image8.png#lightbox)


Эта сборка затем добавляются в проекты Xamarin в Visual Studio для Mac, несмотря на то, что сам язык Visual Basic не поддерживается в Xamarin iOS или Android проектов.

### <a name="referencing-the-pcl-in-visual-studio-for-mac"></a>Ссылки на библиотеки PCL в Visual Studio для Mac

Поскольку Xamarin не поддерживает Visual Basic не удалось загрузить проект переносимой библиотеки Классов (и не приложения Windows Phone) как показано на следующем снимке экрана:

 [![](native-apps-images/image9.png "Visual Studio для Mac решение")](native-apps-images/image9.png#lightbox)

Мы по-прежнему может включать библиотеку DLL сборки переносимой библиотеки Классов Visual Basic в проектах Xamarin.iOS и Xamarin.Android:

1.  Щелкните правой кнопкой мыши **ссылки** узел и выберите **изменить ссылки...**

    [![](native-apps-images/image10.png "Меню \"проект изменить ссылки\"")](native-apps-images/image10.png#lightbox)

1.  Выберите **.Net сборки** вкладку и перейдите к выходной библиотеке DLL в каталоге проекта Visual Basic. Несмотря на то, что Visual Studio для Mac не удается открыть проект, все файлы должны присутствовать из системы управления версиями. Нажмите кнопку **добавить** затем **ОК** для добавления этой сборки для приложений iOS и Android.

    [![](native-apps-images/image11-sml.png "Нажмите кнопку Добавить и нажмите кнопку ОК для добавления этой сборки для приложений iOS и Android")](native-apps-images/image11.png#lightbox)

1.  Приложений iOS и Android теперь можно включить логику приложения, предоставляемые переносимой библиотеки классов Visual Basic. На этом снимке экрана показано приложение iOS, который ссылается на переносимой библиотеки Классов Visual Basic и содержит код, который использует функциональные возможности из этой библиотеки.

    [![](native-apps-images/image12-sml.png "Изменить ссылки добавьте окно сборки .NET")](native-apps-images/image12.png#lightbox)


При внесении изменений в проект Visual Basic в Visual Studio не забудьте сборку проекта, хранить результирующую сборку библиотеки DLL в системе управления версиями, а затем на включение внесенных изменений, новая библиотека DLL из системы управления версиями на компьютере Mac, чтобы сборка в Visual Studio для Mac содержит последнюю версию функциональные возможности.


## <a name="summary"></a>Сводка

В этой статье показано, как использовать код Visual Basic в приложениях Xamarin, с помощью Visual Studio и переносимых библиотек классов. Несмотря на то, что Xamarin не поддерживает напрямую Visual Basic, компиляции Visual Basic в переносимую библиотеку Классов позволяет код, созданный с помощью Visual Basic должны быть включены в приложениях iOS и Android.

## <a name="related-links"></a>Связанные ссылки

- [TaskyPortableVB (пример)](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyPortableVB)
- [Кроссплатформенная разработка в .NET Framework (Microsoft)](http://msdn.microsoft.com/library/gg597391(v=vs.110).aspx)
