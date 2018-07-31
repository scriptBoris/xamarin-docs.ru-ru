---
title: Средство выбора документов в Xamarin.iOS
description: В этом документе описываются iOS средство выбора документов и способы ее использования в Xamarin.iOS. Он смотрит на iCloud, документы, общий код установки, расширения поставщика документов и другие.
ms.prod: xamarin
ms.assetid: 89539D79-BC6E-4A3E-AEC6-69D9A6CC6818
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/05/2017
ms.openlocfilehash: ca0c7a6e655fdc44aa673a59be71bc83044d3085
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353338"
---
# <a name="document-picker-in-xamarinios"></a>Средство выбора документов в Xamarin.iOS

Средство выбора документа позволяет документы совместно использоваться приложениями. Эти документы могут храниться в iCloud или в каталоге различные приложения. Документы доступны через набор [расширений поставщиков документа](~/ios/platform/extensions.md) пользователь установил на своем устройстве. 

Из-за сложности хранение документов синхронизации приложений и в облаке с ними связаны определенный объем необходимых сложности.

## <a name="requirements"></a>Требования

Чтобы завершить действия, описанные в этой статье требуется следующее:

-  **Xcode 7 и iOS 8 или более поздней версии** — Apple Xcode 7 и iOS 8 или более новых интерфейсов API необходимо установить и настроить на компьютере разработчика.
-  **Visual Studio или Visual Studio для Mac** — должна быть установлена последняя версия Visual Studio для Mac.
-  **Устройства iOS** — устройства iOS под управлением iOS 8 или более поздней версии.

## <a name="changes-to-icloud"></a>Изменения в iCloud

Для реализации новых возможностей выбора документа, были внесены следующие изменения Apple iCloud службы:

-  ICloud управляющая программа была полностью переписана помощи CloudKit.
-  Существующие iCloud функции были переименованы iCloud Drive.
-  Добавлена поддержка ОС Microsoft Windows в iCloud.
-  В Mac OS Finder была добавлена в папку iCloud.
-  устройства iOS можно получить содержимое папки iCloud Mac OS.

> [!IMPORTANT]
> Компания Apple [предоставляет инструменты](https://developer.apple.com/support/allowing-users-to-manage-data/), которые помогают разработчикам надлежащим образом соблюдать Общий регламент по защите данных Европейского союза (GDPR).

## <a name="what-is-a-document"></a>Что такое документ?

При обращении к документу в iCloud, доступен единый, автономный и восприятия таким образом пользователь. Пользователь может потребоваться изменить документ или поделиться им с другими пользователями (с помощью электронной почты, например).

Существует несколько типов файлов, что пользователь сразу же распознает как документов, например страницы, основной доклад или числа файлов. Тем не менее iCloud не ограничивается этой концепции. Например состояние игры (например, совпадение Chess) можно обрабатывать как документ и хранящиеся в iCloud. Этот файл может передаваться между устройствами пользователя и дают возможность выбрать игры, он остановился на другом устройстве.

## <a name="dealing-with-documents"></a>Работа с документами

Прежде чем углубляться в код, необходимый для используйте средство выбора документа с помощью Xamarin, в этой статье будет представлены рекомендации для работы с документами iCloud и некоторые изменения, внесенные в существующих API должны поддерживать средство выбора документа.

### <a name="using-file-coordination"></a>С помощью файла координации

Поскольку файл можно изменять из нескольких мест, координации должна использоваться для предотвращения потери данных.

 [![](document-picker-images/image1.png "С помощью файла координации")](document-picker-images/image1.png#lightbox)

Давайте рассмотрим пример на рисунке выше:

1.  Устройства iOS с помощью файла координации создает новый документ и сохраняет его в папку icloud.
2.  iCloud сохраняет измененный файл в облако для распространения для каждого устройства.
3.  Вложенные Mac видит измененный файл в папку iCloud и использует файл координации скопировать изменения в файл.
4.  Устройство не использует файл координации вносит изменение в файл и сохраняет его в папку icloud. Эти изменения сразу же реплицируются на другие устройства.

Предположим, исходное устройство iOS или Mac редактирования файла теперь потери или заменены версию файла с несогласованных устройства свои изменения. Чтобы предотвратить потерю данных, координации файл является обязательным при работе с документами на основе облака.

### <a name="using-uidocument"></a>С помощью данных

 `UIDocument` упрощает вещи (или `NSDocument` в macOS), выполнив все часть тяжелой работы для разработчиков. Он обладает встроенной файл координации с очередями фона для предотвращения блокировки интерфейса пользователя приложения.

 `UIDocument` предоставляет несколько, требуется API высокого уровня, оптимизации процесса разработки приложения Xamarin, с любой целью разработчика.

Следующий код создает подкласс `UIDocument` реализовать универсальный текстовый документ, который может использоваться для хранения и извлечения текста из iCloud:

```csharp
using System;
using Foundation;
using UIKit;

namespace DocPicker
{
    public class GenericTextDocument : UIDocument
    {
        #region Private Variable Storage
        private NSString _dataModel;
        #endregion

        #region Computed Properties
        public string Contents {
            get { return _dataModel.ToString (); }
            set { _dataModel = new NSString(value); }
        }
        #endregion

        #region Constructors
        public GenericTextDocument (NSUrl url) : base (url)
        {
            // Set the default document text
            this.Contents = "";
        }

        public GenericTextDocument (NSUrl url, string contents) : base (url)
        {
            // Set the default document text
            this.Contents = contents;
        }
        #endregion

        #region Override Methods
        public override bool LoadFromContents (NSObject contents, string typeName, out NSError outError)
        {
            // Clear the error state
            outError = null;

            // Were any contents passed to the document?
            if (contents != null) {
                _dataModel = NSString.FromData( (NSData)contents, NSStringEncoding.UTF8 );
            }

            // Inform caller that the document has been modified
            RaiseDocumentModified (this);

            // Return success
            return true;
        }

        public override NSObject ContentsForType (string typeName, out NSError outError)
        {
            // Clear the error state
            outError = null;

            // Convert the contents to a NSData object and return it
            NSData docData = _dataModel.Encode(NSStringEncoding.UTF8);
            return docData;
        }
        #endregion

        #region Events
        public delegate void DocumentModifiedDelegate(GenericTextDocument document);
        public event DocumentModifiedDelegate DocumentModified;

        internal void RaiseDocumentModified(GenericTextDocument document) {
            // Inform caller
            if (this.DocumentModified != null) {
                this.DocumentModified (document);
            }
        }
        #endregion
    }
}
```

`GenericTextDocument` Представленные выше класс будет использоваться в этой статье, при работе с средство выбора документов и внешних документов в приложении Xamarin.iOS 8.

## <a name="asynchronous-file-coordination"></a>Асинхронный файловый координации

iOS 8 представлено несколько новых функций координации асинхронной файл через новые интерфейсы API координации файл. Перед iOS 8 все существующие API координации файла были полностью синхронной. Это означало, что разработчик был ответственен за реализацию фон очереди для предотвращения блокировки интерфейса пользователя приложения файл координации.

Новый `NSFileAccessIntent` класс содержит URL-адрес, указывающий на файл и несколько вариантов для управления типом координацию, запрашиваемую. В следующем коде показано перемещение файла из одного расположения в другое с помощью целей:

```csharp
// Get source options
var srcURL = NSUrl.FromFilename ("FromFile.txt");
var srcIntent = NSFileAccessIntent.CreateReadingIntent (srcURL, NSFileCoordinatorReadingOptions.ForUploading);

// Get destination options
var dstURL = NSUrl.FromFilename ("ToFile.txt");
var dstIntent = NSFileAccessIntent.CreateReadingIntent (dstURL, NSFileCoordinatorReadingOptions.ForUploading);

// Create an array
var intents = new NSFileAccessIntent[] {
    srcIntent,
    dstIntent
};

// Initialize a file coordination with intents
var queue = new NSOperationQueue ();
var fileCoordinator = new NSFileCoordinator ();
fileCoordinator.CoordinateAccess (intents, queue, (err) => {
    // Was there an error?
    if (err!=null) {
        Console.WriteLine("Error: {0}",err.LocalizedDescription);
    }
});
```

## <a name="discovering-and-listing-documents"></a>Обнаружение и списка документов

Способ обнаружения и перечисление документов — с помощью существующего `NSMetadataQuery` API-интерфейсы. В этом разделе будут рассмотрены новые возможности, добавленные к `NSMetadataQuery` , сделать работу с документами даже невиданной ранее.

### <a name="existing-behavior"></a>Существующее поведение

До 8, iOS `NSMetadataQuery` упала для отправки локального файла изменения таких как: удаляет, создание и изменение имени.

 [![](document-picker-images/image2.png "Общие сведения об изменениях NSMetadataQuery локального файла")](document-picker-images/image2.png#lightbox)

На схеме выше:

1.  Для файлов, которые уже существуют в контейнере приложения `NSMetadataQuery` имелось `NSMetadata` записей уже создана и помещаются в очередь, поэтому они сразу же доступны для приложения.
1.  Приложение создает новый файл в контейнере приложения.
1.  Задержка перед `NSMetadataQuery` увидит изменений в контейнер приложения и создает необходимые `NSMetadata` записи.


Из-за задержки при создании `NSMetadata` записей, приложение должно было иметь открыть источники данных: один для изменения локального файла и один для облака на основе изменений.

### <a name="stitching"></a>Совмещение

В iOS 8 `NSMetadataQuery` проще в использовании непосредственно с помощью новой функции склеивание:

 [![](document-picker-images/image3.png "NSMetadataQuery с помощью новой функции вызывается склеивание")](document-picker-images/image3.png#lightbox)

С помощью склеивание на схеме выше:

1.  Как и раньше, для файлов, которые уже существует в контейнере приложения `NSMetadataQuery` имелось `NSMetadata` записей уже создана и помещаются в очередь.
1.  Приложение создает новый файл в контейнере приложения, с помощью файла координации.
1.  Обработчик в контейнере приложения увидит изменения и вызовы `NSMetadataQuery` создать необходимые `NSMetadata` записи.
1.  `NSMetadata` Запись создается сразу после файла и становится доступным для приложения.


С помощью склеивание приложение больше не нужно открыть источник данных для мониторинга локальных и облачных изменения файлов. Теперь приложение может полагаться на `NSMetadataQuery` напрямую.

> [!IMPORTANT]
> Совмещение работает, только если в приложении используется файл координации, представленный в предыдущем разделе. Если не используется файл координации, API-интерфейсы по умолчанию существующее поведение pre iOS 8.




### <a name="new-ios-8-metadata-features"></a>Новые функции метаданных iOS 8

Были добавлены следующие новые функции `NSMetadataQuery` в iOS 8:

-   `NSMetatadataQuery` Теперь можно вывести список не являющейся локальной для документов, хранящихся в облаке.
-  Были добавлены новые интерфейсы API для доступа к информации метаданных для документов на основе облака. 
-  Доступна новая `NSUrl_PromisedItems` API, который будет обращаться к атрибутам файла файлов, которые может иметь или не иметь их содержимое доступны локально.
-  Используйте `GetPromisedItemResourceValue` метод, чтобы получить сведения о заданном файле и воспользуйтесь `GetPromisedItemResourceValues` метод для получения сведений о более чем одного файла за раз.


Для работы с метаданными были добавлены две новые флаги координации файла:

-   `NSFileCoordinatorReadImmediatelyAvailableMetadataOnly` 
-   `NSFileCoordinatorWriteContentIndependentMetadataOnly` 


Флаги, то выше содержимое файла документа не обязательно должны быть доступны локально для использования.

В следующем фрагменте кода показано, как использовать `NSMetadataQuery` для запроса на наличие определенного файла и создать файл, если он не существует:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using Foundation;
using UIKit;
using ObjCRuntime;
using System.IO;


#region Static Properties
public const string TestFilename = "test.txt"; 
#endregion

#region Computed Properties
public bool HasiCloud { get; set; }
public bool CheckingForiCloud { get; set; }
public NSUrl iCloudUrl { get; set; }

public GenericTextDocument Document { get; set; }
public NSMetadataQuery Query { get; set; }
#endregion

#region Private Methods
private void FindDocument () {
    Console.WriteLine ("Finding Document...");

    // Create a new query and set it's scope
    Query = new NSMetadataQuery();
    Query.SearchScopes = new NSObject [] {
                NSMetadataQuery.UbiquitousDocumentsScope,
                NSMetadataQuery.UbiquitousDataScope,
                NSMetadataQuery.AccessibleUbiquitousExternalDocumentsScope
            };

    // Build a predicate to locate the file by name and attach it to the query
    var pred = NSPredicate.FromFormat ("%K == %@"
        , new NSObject[] {
            NSMetadataQuery.ItemFSNameKey
            , new NSString(TestFilename)});
    Query.Predicate = pred;

    // Register a notification for when the query returns
    NSNotificationCenter.DefaultCenter.AddObserver (this,
            new Selector("queryDidFinishGathering:"),           NSMetadataQuery.DidFinishGatheringNotification,
            Query);

    // Start looking for the file
    Query.StartQuery ();
    Console.WriteLine ("Querying: {0}", Query.IsGathering);
}

[Export("queryDidFinishGathering:")]
public void DidFinishGathering (NSNotification notification) {
    Console.WriteLine ("Finish Gathering Documents.");

    // Access the query and stop it from running
    var query = (NSMetadataQuery)notification.Object;
    query.DisableUpdates();
    query.StopQuery();

    // Release the notification
    NSNotificationCenter.DefaultCenter.RemoveObserver (this
        , NSMetadataQuery.DidFinishGatheringNotification
        , query);

    // Load the document that the query returned
    LoadDocument(query);
}

private void LoadDocument (NSMetadataQuery query) {
    Console.WriteLine ("Loading Document...");    

    // Take action based on the returned record count
    switch (query.ResultCount) {
    case 0:
        // Create a new document
        CreateNewDocument ();
        break;
    case 1:
        // Gain access to the url and create a new document from
        // that instance
        NSMetadataItem item = (NSMetadataItem)query.ResultAtIndex (0);
        var url = (NSUrl)item.ValueForAttribute (NSMetadataQuery.ItemURLKey);

        // Load the document
        OpenDocument (url);
        break;
    default:
        // There has been an issue
        Console.WriteLine ("Issue: More than one document found...");
        break;
    }
}
#endregion

#region Public Methods
public void OpenDocument(NSUrl url) {

    Console.WriteLine ("Attempting to open: {0}", url);
    Document = new GenericTextDocument (url);

    // Open the document
    Document.Open ( (success) => {
        if (success) {
            Console.WriteLine ("Document Opened");
        } else
            Console.WriteLine ("Failed to Open Document");
    });

    // Inform caller
    RaiseDocumentLoaded (Document);
}

public void CreateNewDocument() {
    // Create path to new file
    // var docsFolder = Environment.GetFolderPath (Environment.SpecialFolder.Personal);
    var docsFolder = Path.Combine(iCloudUrl.Path, "Documents");
    var docPath = Path.Combine (docsFolder, TestFilename);
    var ubiq = new NSUrl (docPath, false);

    // Create new document at path 
    Console.WriteLine ("Creating Document at:" + ubiq.AbsoluteString);
    Document = new GenericTextDocument (ubiq);

    // Set the default value
    Document.Contents = "(default value)";

    // Save document to path
    Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForCreating, (saveSuccess) => {
        Console.WriteLine ("Save completion:" + saveSuccess);
        if (saveSuccess) {
            Console.WriteLine ("Document Saved");
        } else {
            Console.WriteLine ("Unable to Save Document");
        }
    });

    // Inform caller
    RaiseDocumentLoaded (Document);
}

public bool SaveDocument() {
    bool successful = false;

    // Save document to path
    Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForOverwriting, (saveSuccess) => {
        Console.WriteLine ("Save completion: " + saveSuccess);
        if (saveSuccess) {
            Console.WriteLine ("Document Saved");
            successful = true;
        } else {
            Console.WriteLine ("Unable to Save Document");
            successful=false;
        }
    });

    // Return results
    return successful;
}
#endregion

#region Events
public delegate void DocumentLoadedDelegate(GenericTextDocument document);
public event DocumentLoadedDelegate DocumentLoaded;

internal void RaiseDocumentLoaded(GenericTextDocument document) {
    // Inform caller
    if (this.DocumentLoaded != null) {
        this.DocumentLoaded (document);
    }
}
#endregion
```

### <a name="document-thumbnails"></a>Миниатюры документа

Apple полагает, что удобства работы пользователей при перечислении документов для приложения, можно выполнить предварительный просмотр. Это обеспечивает контекст конечных пользователей, они могут легко находить документ, который они хотят работать с.

До iOS 8 отображение документа обязаны пользовательской реализации. Знакомы с iOS 8, атрибутов файловой системы, которые позволяют разработчикам быстро работать с эскизами документов.

#### <a name="retrieving-document-thumbnails"></a>Получение эскизами документов 

Путем вызова `GetPromisedItemResourceValue` или `GetPromisedItemResourceValues` методы, `NSUrl_PromisedItems` API, `NSUrlThumbnailDictionary`, возвращается. Только ключ в настоящее время в данном словаре `NSThumbnial1024X1024SizeKey` и соответствующим ему `UIImage`.

#### <a name="saving-document-thumbnails"></a>Сохранение документа эскизов

Самый простой способ сохранить эскиза, — с помощью `UIDocument`. Путем вызова `GetFileAttributesToWrite` метод `UIDocument` и настройки миниатюры, автоматически сохраняются при файла документа. Управляющая программа iCloud будет видит это изменение и передать их в iCloud. В Mac OS X эскизы создаются автоматически для разработчиков с помощью подключаемого модуля быстрого поиска.

С основами работы с документами на основе iCloud на месте, а также изменения в существующие интерфейсы API, мы готовы реализовать контроллера представления средство выбора документа в Xamarin iOS 8 мобильного приложения.


## <a name="enabling-icloud-in-xamarin"></a>Включение iCloud в Xamarin

Прежде чем средство выбора документа можно использовать в приложении Xamarin.iOS, iCloud поддержки необходимо включить в приложении, а также с помощью Apple. 

Следующий пример действия в процессе подготовки для iCloud.

1. Создайте контейнер iCloud.
2. Создание идентификатора приложения, который содержит iCloud службы приложений.
3. Создать профиль подготовки, который включает этот идентификатор приложения.

[Работа с возможностями](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md) руководстве содержатся сведения о первых двух шагов. Чтобы создать профиль подготовки, выполните действия, описанные в [профиль подготовки](~/ios/get-started/installation/device-provisioning/index.md#provisioning-your-device) руководства.



Шаги пошагового процесса настройки приложения для iCloud:

Выполните следующие действия:

1.  Откройте проект в Visual Studio для Mac или Visual Studio.
2.  В **обозревателе решений**, щелкните правой кнопкой мыши проект и выберите параметры.
3.  В диалоговом окне "Параметры" выберите **приложение iOS**, убедитесь, что **идентификатор пакета** соответствует типу, который был определен в **идентификатор приложения** созданной ранее для приложения. 
4.  Выберите **подписывание пакета iOS**выберите **Developer Identity** и **профиль подготовки** созданной выше.
5.  Нажмите кнопку **ОК** кнопку, чтобы сохранить изменения и закрыть диалоговое окно.
6.  Щелкните правой кнопкой мыши `Entitlements.plist` в **обозревателе решений** чтобы открыть его в редакторе.

    > [!IMPORTANT]
    > В Visual Studio может потребоваться открыть редактор объемов обслуживания, щелкнув его, выбрав **открыть с помощью...** и выбрав редактор списка свойств

7.  Проверьте **включить iCloud** , **документы iCloud** , **хранилище ключ значение** и **CloudKit** .
8.  Убедитесь, **контейнера** существует для приложения (как созданной ранее). Пример: `iCloud.com.your-company.AppName`
9.  Сохраните изменения в файле.

Дополнительные сведения о назначениях см. [работа с назначениями](~/ios/deploy-test/provisioning/entitlements.md) руководства.

С указанными параметрами в месте приложение теперь можно использовать документы на основе облака и новый контроллер представления средство выбора документа.

## <a name="common-setup-code"></a>Общий код установки

Перед началом работы с контроллером представления средство выбора документа есть некоторые стандартной установки код, необходимый. Начать с изменения приложения `AppDelegate.cs` файл и это должно выглядеть следующим образом:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using Foundation;
using UIKit;
using ObjCRuntime;
using System.IO;

namespace DocPicker
{

    [Register ("AppDelegate")]
    public partial class AppDelegate : UIApplicationDelegate
    {
        #region Static Properties
        public const string TestFilename = "test.txt"; 
        #endregion

        #region Computed Properties
        public override UIWindow Window { get; set; }
        public bool HasiCloud { get; set; }
        public bool CheckingForiCloud { get; set; }
        public NSUrl iCloudUrl { get; set; }

        public GenericTextDocument Document { get; set; }
        public NSMetadataQuery Query { get; set; }
        public NSData Bookmark { get; set; }
        #endregion

        #region Private Methods
        private void FindDocument () {
            Console.WriteLine ("Finding Document...");

            // Create a new query and set it's scope
            Query = new NSMetadataQuery();
            Query.SearchScopes = new NSObject [] {
                NSMetadataQuery.UbiquitousDocumentsScope,
                NSMetadataQuery.UbiquitousDataScope,
                NSMetadataQuery.AccessibleUbiquitousExternalDocumentsScope
            };

            // Build a predicate to locate the file by name and attach it to the query
            var pred = NSPredicate.FromFormat ("%K == %@",
                new NSObject[] {NSMetadataQuery.ItemFSNameKey
                , new NSString(TestFilename)});
            Query.Predicate = pred;

            // Register a notification for when the query returns
            NSNotificationCenter.DefaultCenter.AddObserver (this
                , new Selector("queryDidFinishGathering:")
                , NSMetadataQuery.DidFinishGatheringNotification
                , Query);

            // Start looking for the file
            Query.StartQuery ();
            Console.WriteLine ("Querying: {0}", Query.IsGathering);
        }


        [Export("queryDidFinishGathering:")]
        public void DidFinishGathering (NSNotification notification) {
            Console.WriteLine ("Finish Gathering Documents.");

            // Access the query and stop it from running
            var query = (NSMetadataQuery)notification.Object;
            query.DisableUpdates();
            query.StopQuery();

            // Release the notification
            NSNotificationCenter.DefaultCenter.RemoveObserver (this
                , NSMetadataQuery.DidFinishGatheringNotification
                , query);

            // Load the document that the query returned
            LoadDocument(query);
        }

        private void LoadDocument (NSMetadataQuery query) {
            Console.WriteLine ("Loading Document...");  

            // Take action based on the returned record count
            switch (query.ResultCount) {
            case 0:
                // Create a new document
                CreateNewDocument ();
                break;
            case 1:
                // Gain access to the url and create a new document from
                // that instance
                NSMetadataItem item = (NSMetadataItem)query.ResultAtIndex (0);
                var url = (NSUrl)item.ValueForAttribute (NSMetadataQuery.ItemURLKey);

                // Load the document
                OpenDocument (url);
                break;
            default:
                // There has been an issue
                Console.WriteLine ("Issue: More than one document found...");
                break;
            }
        }
        #endregion

        #region Public Methods

        public void OpenDocument(NSUrl url) {

            Console.WriteLine ("Attempting to open: {0}", url);
            Document = new GenericTextDocument (url);

            // Open the document
            Document.Open ( (success) => {
                if (success) {
                    Console.WriteLine ("Document Opened");
                } else
                    Console.WriteLine ("Failed to Open Document");
            });

            // Inform caller
            RaiseDocumentLoaded (Document);
        }

        public void CreateNewDocument() {
            // Create path to new file
            // var docsFolder = Environment.GetFolderPath (Environment.SpecialFolder.Personal);
            var docsFolder = Path.Combine(iCloudUrl.Path, "Documents");
            var docPath = Path.Combine (docsFolder, TestFilename);
            var ubiq = new NSUrl (docPath, false);

            // Create new document at path 
            Console.WriteLine ("Creating Document at:" + ubiq.AbsoluteString);
            Document = new GenericTextDocument (ubiq);

            // Set the default value
            Document.Contents = "(default value)";

            // Save document to path
            Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForCreating, (saveSuccess) => {
                Console.WriteLine ("Save completion:" + saveSuccess);
                if (saveSuccess) {
                    Console.WriteLine ("Document Saved");
                } else {
                    Console.WriteLine ("Unable to Save Document");
                }
            });

            // Inform caller
            RaiseDocumentLoaded (Document);
        }

        /// <summary>
        /// Saves the document.
        /// </summary>
        /// <returns><c>true</c>, if document was saved, <c>false</c> otherwise.</returns>
        public bool SaveDocument() {
            bool successful = false;

            // Save document to path
            Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForOverwriting, (saveSuccess) => {
                Console.WriteLine ("Save completion: " + saveSuccess);
                if (saveSuccess) {
                    Console.WriteLine ("Document Saved");
                    successful = true;
                } else {
                    Console.WriteLine ("Unable to Save Document");
                    successful=false;
                }
            });

            // Return results
            return successful;
        }
        #endregion

        #region Override Methods
        public override void FinishedLaunching (UIApplication application)
        {

            // Start a new thread to check and see if the user has iCloud
            // enabled.
            new Thread(new ThreadStart(() => {
                // Inform caller that we are checking for iCloud
                CheckingForiCloud = true;

                // Checks to see if the user of this device has iCloud
                // enabled
                var uburl = NSFileManager.DefaultManager.GetUrlForUbiquityContainer(null);

                // Connected to iCloud?
                if (uburl == null)
                {
                    // No, inform caller
                    HasiCloud = false;
                    iCloudUrl =null;
                    Console.WriteLine("Unable to connect to iCloud");
                    InvokeOnMainThread(()=>{
                        var okAlertController = UIAlertController.Create ("iCloud Not Available", "Developer, please check your Entitlements.plist, Bundle ID and Provisioning Profiles.", UIAlertControllerStyle.Alert);
                        okAlertController.AddAction (UIAlertAction.Create ("Ok", UIAlertActionStyle.Default, null));
                        Window.RootViewController.PresentViewController (okAlertController, true, null);
                    });
                }
                else
                {   
                    // Yes, inform caller and save location the the Application Container
                    HasiCloud = true;
                    iCloudUrl = uburl;
                    Console.WriteLine("Connected to iCloud");

                    // If we have made the connection with iCloud, start looking for documents
                    InvokeOnMainThread(()=>{
                        // Search for the default document
                        FindDocument ();
                    });
                }

                // Inform caller that we are no longer looking for iCloud
                CheckingForiCloud = false;

            })).Start();
                
        }
        
        // This method is invoked when the application is about to move from active to inactive state.
        // OpenGL applications should use this method to pause.
        public override void OnResignActivation (UIApplication application)
        {
        }
        
        // This method should be used to release shared resources and it should store the application state.
        // If your application supports background execution this method is called instead of WillTerminate
        // when the user quits.
        public override void DidEnterBackground (UIApplication application)
        {
            // Trap all errors
            try {
                // Values to include in the bookmark packet
                var resources = new string[] {
                    NSUrl.FileSecurityKey,
                    NSUrl.ContentModificationDateKey,
                    NSUrl.FileResourceIdentifierKey,
                    NSUrl.FileResourceTypeKey,
                    NSUrl.LocalizedNameKey
                };

                // Create the bookmark
                NSError err;
                Bookmark = Document.FileUrl.CreateBookmarkData (NSUrlBookmarkCreationOptions.WithSecurityScope, resources, iCloudUrl, out err);

                // Was there an error?
                if (err != null) {
                    // Yes, report it
                    Console.WriteLine ("Error Creating Bookmark: {0}", err.LocalizedDescription);
                }
            }
            catch (Exception e) {
                // Report error
                Console.WriteLine ("Error: {0}", e.Message);
            }
        }
        
        // This method is called as part of the transition from background to active state.
        public override void WillEnterForeground (UIApplication application)
        {
            // Is there any bookmark data?
            if (Bookmark != null) {
                // Trap all errors
                try {
                    // Yes, attempt to restore it
                    bool isBookmarkStale;
                    NSError err;
                    var srcUrl = new NSUrl (Bookmark, NSUrlBookmarkResolutionOptions.WithSecurityScope, iCloudUrl, out isBookmarkStale, out err);

                    // Was there an error?
                    if (err != null) {
                        // Yes, report it
                        Console.WriteLine ("Error Loading Bookmark: {0}", err.LocalizedDescription);
                    } else {
                        // Load document from bookmark
                        OpenDocument (srcUrl);
                    }
                }
                catch (Exception e) {
                    // Report error
                    Console.WriteLine ("Error: {0}", e.Message);
                }
            }

        }
        
        // This method is called when the application is about to terminate. Save data, if needed.
        public override void WillTerminate (UIApplication application)
        {
        }
        #endregion

        #region Events
        public delegate void DocumentLoadedDelegate(GenericTextDocument document);
        public event DocumentLoadedDelegate DocumentLoaded;

        internal void RaiseDocumentLoaded(GenericTextDocument document) {
            // Inform caller
            if (this.DocumentLoaded != null) {
                this.DocumentLoaded (document);
            }
        }
        #endregion
    }
}

```

> [!IMPORTANT]
> Приведенный выше код содержит код из раздела Discovering и листинг документов выше. Оно выводится здесь целиком, так же, как в настоящем приложении. Для простоты в этом примере работает с единый файл жестко (`test.txt`) только.

Приведенный выше код предоставляет несколько советов диска iCloud, чтобы облегчить их для работы в остальной части приложения.

Добавьте следующий код для любого контейнера представление, используя средство выбора документа или работа с документами на основе облака или представление:

```csharp
using CloudKit;
...

#region Computed Properties
/// <summary>
/// Returns the delegate of the current running application
/// </summary>
/// <value>The this app.</value>
public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
#endregion
```

Это добавляет ярлык для получения `AppDelegate` и получить доступ к iCloud ярлыки, созданные выше.

Этот код в месте давайте рассмотрим реализация контроллера представления средство выбора документа в приложении Xamarin iOS 8.

## <a name="using-the-document-picker-view-controller"></a>С помощью контроллера представления средство выбора документа

До появления iOS 8 было очень трудно получить доступ к документам из другого приложения, поскольку не существовало возможности для обнаружения документов вне приложения из в приложении.

### <a name="existing-behavior"></a>Существующее поведение

 [![](document-picker-images/image31.png "Общие сведения о существующих поведение")](document-picker-images/image31.png#lightbox)

Давайте рассмотрим, доступ к внешнему документу до iOS 8:

1.  Сначала пользователю необходимо открыть приложение, которое первоначально создал документ.
1.  Выбран документ и `UIDocumentInteractionController` используется для отправки документа для нового приложения.
1.  Наконец копии исходного документа помещается в контейнер нового приложения.


Оттуда документ доступен для второго приложения открывать и редактировать.

### <a name="discovering-documents-outside-of-an-apps-container"></a>Обнаружение документов за пределами приложения контейнера

В iOS 8 приложение — доступ к документам за пределами своего собственного приложения контейнера без труда:

 [![](document-picker-images/image32.png "Обнаружение документов за пределами приложения контейнера")](document-picker-images/image32.png#lightbox)

С помощью нового iCloud средство выбора документов ( `UIDocumentPickerViewController`), приложение iOS напрямую можно найти и использовать за пределами своего контейнера приложения. `UIDocumentPickerViewController` Предоставляет механизм для предоставления доступа к и редактировать их обнаружения документов с помощью разрешений.

Приложения должны согласиться его документов отображаются в iCloud средство выбора документов и будут доступны для других приложений для обнаружения и работать с ними. Чтобы приложение Xamarin iOS 8 совместно использовать приложения контейнера, изменить его `Info.plist` в обычном текстовом редакторе и добавьте следующие две строки в нижнюю часть словаря (между `<dict>...</dict>` теги):

```xml
<key>NSUbiquitousContainerIsDocumentScopePublic</key>
<true/>
```

`UIDocumentPickerViewController` Предоставляет отличный новый пользовательский Интерфейс, который позволяет пользователю выбирать документы. Чтобы отобразить средство выбора контроллера представления документа в приложении Xamarin iOS 8, сделайте следующее:

```csharp
using MobileCoreServices;
...

// Allow the Document picker to select a range of document types
        var allowedUTIs = new string[] {
            UTType.UTF8PlainText,
            UTType.PlainText,
            UTType.RTF,
            UTType.PNG,
            UTType.Text,
            UTType.PDF,
            UTType.Image
        };

        // Display the picker
        //var picker = new UIDocumentPickerViewController (allowedUTIs, UIDocumentPickerMode.Open);
        var pickerMenu = new UIDocumentMenuViewController(allowedUTIs, UIDocumentPickerMode.Open);
        pickerMenu.DidPickDocumentPicker += (sender, args) => {

            // Wireup Document Picker
            args.DocumentPicker.DidPickDocument += (sndr, pArgs) => {

                // IMPORTANT! You must lock the security scope before you can
                // access this file
                var securityEnabled = pArgs.Url.StartAccessingSecurityScopedResource();

                // Open the document
                ThisApp.OpenDocument(pArgs.Url);

                // IMPORTANT! You must release the security lock established
                // above.
                pArgs.Url.StopAccessingSecurityScopedResource();
            };

            // Display the document picker
            PresentViewController(args.DocumentPicker,true,null);
        };

pickerMenu.ModalPresentationStyle = UIModalPresentationStyle.Popover;
PresentViewController(pickerMenu,true,null);
UIPopoverPresentationController presentationPopover = pickerMenu.PopoverPresentationController;
if (presentationPopover!=null) {
    presentationPopover.SourceView = this.View;
    presentationPopover.PermittedArrowDirections = UIPopoverArrowDirection.Down;
    presentationPopover.SourceRect = ((UIButton)s).Frame;
}
```

> [!IMPORTANT]
> Разработчику необходимо вызвать `StartAccessingSecurityScopedResource` метод `NSUrl` можно получить доступ к внешнему документу. `StopAccessingSecurityScopedResource` Метод должен вызываться для снятия блокировки безопасности, сразу после загрузки документа.

### <a name="sample-output"></a>Пример результатов выполнения

Ниже приведен пример как приведенный выше код будет отображаться средство выбора документа при запуске на устройстве iPhone:

1.  Пользователь запускает приложение и отображается основной интерфейс:   
 
    [![](document-picker-images/image33.png "Основной интерфейс отображается")](document-picker-images/image33.png#lightbox)
1.  Пользователь касается **действие** кнопку в верхней части экрана и предлагается выбрать **документа поставщика** из списка доступных поставщиков:   
 
    [![](document-picker-images/image34.png "Выберите поставщик документа из списка доступных поставщиков")](document-picker-images/image34.png#lightbox)
1.  **Контроллер представления средство выбора документа** отображается для выбранного **документа поставщика**:   
 
    [![](document-picker-images/image35.png "Отображается средство выбора контроллера представления документа")](document-picker-images/image35.png#lightbox)
1.  Пользователь нажимает на **папку документов** для отображения его содержимого:   
 
    [![](document-picker-images/image36.png "Ее содержимое документа")](document-picker-images/image36.png#lightbox)
1.  Пользователь выбирает **документа** и **средство выбора документа** закрыт.
1.  Основной интерфейс отобразится, **документа** загружается из внешнего контейнера и отображения его содержимого.


Фактическому отображению контроллера представления средство выбора документа зависит от поставщиков документа, что пользователь установил на устройстве, и какой режим выбора документа был реализовать. Приведенный выше пример использует открытый режим, другие типы режим будет рассказано ниже подробно.

## <a name="managing-external-documents"></a>Управление внешним документам

Как уже говорилось ранее, до 8, iOS приложения только удалось получить доступ к документам, которые были частью приложения контейнера. В iOS 8 приложение можно получить доступ к документам из внешних источников:

 [![](document-picker-images/image37.png "Внешние документы общие сведения об управлении")](document-picker-images/image37.png#lightbox)

Когда пользователь выбирает документ из внешнего источника, справочный документ записывается контейнер приложения, который указывает исходный документ.

Чтобы упростить добавление этой новой возможности в существующие приложения, добавлены несколько новых возможностей `NSMetadataQuery` API. Как правило приложение использует повсеместно области документа в список документов, проживающих в пределах контейнера приложения. С помощью этой области, только документы, в контейнере приложения будет продолжать отображаться.

С помощью новой повсеместно внешней области документа будет возвращать документы, live вне контейнера приложения и возвращения метаданных для них. `NSMetadataItemUrlKey` Будет указывать на URL-адрес, где фактически находится документ.

Иногда приложения не хочет работать с документами, указанный по ссылке th. Вместо этого приложение хочет работать непосредственно с справочный документ. Например приложение может возникнуть необходимость откройте документ в папке приложения в пользовательском Интерфейсе, либо позволяет пользователю перемещаться ссылки в папку.

В iOS 8 новый `NSMetadataItemUrlInLocalContainerKey` предоставляется прямой доступ к справочный документ. Этот ключ указывает на фактическую ссылку на внешний документ, в контейнер приложения.

`NSMetadataUbiquitousItemIsExternalDocumentKey` Используется для проверки ли документ является внешним по отношению к контейнеру приложения. `NSMetadataUbiquitousItemContainerDisplayNameKey` Используется для доступа к имени контейнера, который размещения исходной копии внешнего документа.

### <a name="why-document-references-are-required"></a>Почему требуются ссылки на документ

Основная причина этого iOS 8, используемый для доступа ко внешним документам ссылки — безопасность. Приложение не предоставляется доступ к контейнеру любого другого приложения. Только средство выбора документа можно сделать, так как выполнение вне процесса и имеет доступ на уровне системы.

Единственный способ получить документ вне контейнера приложения — с помощью функции выбора документа, а если URL-адрес, возвращаемый средство выбора — области безопасности. URL-адрес с заданной областью безопасности содержит достаточно информации для выбранного документа вместе с областью действия прав, необходимых для предоставить приложению доступ к документу.

Важно отметить, что если URL-адрес с заданной областью безопасности был сериализован в строку и затем десериализованный, сведения о безопасности будут потеряны, и файл станет недоступным в URL-адресе. Функция ссылку на документ предоставляет механизм для возврата к файлам, на которые указывают эти URL-адреса.

Таким образом, если приложение получает `NSUrl` из одного из документов, он уже был присоединен область безопасности и может использоваться для доступа к файлу. По этой причине настоятельно рекомендуется использовать `UIDocument` так, как он обрабатывает все эти сведения и процессы для них.

### <a name="using-bookmarks"></a>Использование закладок

Это не всегда целесообразно перечислить документы приложения, чтобы вернуться к конкретному документу, например, при выполнении восстановления состояния. iOS 8 предоставляет механизм для создания закладки, которые предназначены непосредственно для заданного документа.

Следующий код создает закладку из `UIDocument`в `FileUrl` свойство:

```csharp
// Trap all errors
try {
    // Values to include in the bookmark packet
    var resources = new string[] {
        NSUrl.FileSecurityKey,
        NSUrl.ContentModificationDateKey,
        NSUrl.FileResourceIdentifierKey,
        NSUrl.FileResourceTypeKey,
        NSUrl.LocalizedNameKey
    };

    // Create the bookmark
    NSError err;
    Bookmark = Document.FileUrl.CreateBookmarkData (NSUrlBookmarkCreationOptions.WithSecurityScope, resources, iCloudUrl, out err);

    // Was there an error?
    if (err != null) {
        // Yes, report it
        Console.WriteLine ("Error Creating Bookmark: {0}", err.LocalizedDescription);
    }
}
catch (Exception e) {
    // Report error
    Console.WriteLine ("Error: {0}", e.Message);
}
```

Существующий интерфейс API закладки используется для создания закладки с существующей `NSUrl` , можно сохранять и загружать для предоставления прямого доступа во внешний файл. Следующий код восстановит закладки, который был создан выше:

```csharp
if (Bookmark != null) {
    // Trap all errors
    try {
        // Yes, attempt to restore it
        bool isBookmarkStale;
        NSError err;
        var srcUrl = new NSUrl (Bookmark, NSUrlBookmarkResolutionOptions.WithSecurityScope, iCloudUrl, out isBookmarkStale, out err);

        // Was there an error?
        if (err != null) {
            // Yes, report it
            Console.WriteLine ("Error Loading Bookmark: {0}", err.LocalizedDescription);
        } else {
            // Load document from bookmark
            OpenDocument (srcUrl);
        }
    }
    catch (Exception e) {
        // Report error
        Console.WriteLine ("Error: {0}", e.Message);
    }
}
```

## <a name="open-vs-import-mode-and-the-document-picker"></a>Откройте vs. Режим импорта и средство выбора документа

Контроллер представления средство выбора документа содержит два разных режима работы:

1.  **Откройте режим** — в этом режиме, когда пользователь выбирает и внешний документ, средство выбора документа будет создать закладку с заданной областью безопасности в контейнере приложения.   
 
    [![](document-picker-images/image37.png "Закладки в контейнере приложения с заданной областью безопасности")](document-picker-images/image37.png#lightbox)
1.  **Режим импорта** — в этом режиме, когда пользователь выбирает и внешний документ, средство выбора документа не создать закладку, но вместо этого скопируйте файл во временное местоположение и предоставить приложению доступ к документу в этом месте:   
 
    [![](document-picker-images/image38.png "Средство выбора документа будет скопируйте файл во временное местоположение и предоставить приложению доступ к документу в этом расположении")](document-picker-images/image38.png#lightbox)   
 Когда приложение завершает работу по любой причине, очищается во временную папку и файл удален. Если приложение должно поддерживать доступ к файлу, его следует создать копию и поместите его в своем контейнере приложения.


Откройте режим полезен в тех случаях, когда приложение желает, чтобы для совместной работы с другим приложением и совместно использовать любые изменения, внесенные в документ с этим приложением. Режим импорта используется в том случае, если приложение необходимо передать все изменения в документ с другими приложениями.

## <a name="making-a-document-external"></a>Чего внешних к документу

Как отмечалось выше, приложение iOS 8 не имеет доступа к контейнерам вне контейнера приложения. Приложение можно записывать на собственный контейнер, локально или в во временную папку, а затем использовать режим специальный документ для перемещения итоговый документ вне контейнера приложения в пользовательское расположение.

Чтобы переместить документ во внешнее расположение, сделайте следующее:

1.  Создаете новый документ в локальной, так и временное расположение.
1.  Создание `NSUrl` , указывающего на новый документ.
1.  Откройте контроллер представления выбора нового документа и передать его `NSUrl` с режимом `MoveToService` . 
1.  Когда пользователь выбирает новое расположение, документ перемещается из ее текущего расположения в новое расположение.
1.  Справочный документ будут записаны в контейнер приложение приложения таким образом, чтобы файл по-прежнему доступен для создания приложения.


Для перемещения во внешнее расположение документа можно использовать следующий код: `var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.MoveToService);`

Справочный документ, возвращаемый описанный выше процесс именно так же, как созданное открытый режим выбора документа. Однако бывают случаи, которые приложение может потребоваться переместить документ без сохранения ссылку на него.

Чтобы переместить документ без генерации ссылки, используйте `ExportToService` режим. Пример: `var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.ExportToService);`

При использовании `ExportToService` режим, он копируется для внешнего контейнера, и имеющуюся копию остается в исходном расположении.

## <a name="document-provider-extensions"></a>Расширения поставщиков документа

С iOS 8 Apple хочет иметь возможность доступа к любому из их документы на основе облака, независимо от того, где они фактически существуют конечному пользователю. Для достижения этой цели, iOS 8 предоставляет новый механизм расширения поставщика документа.

### <a name="what-is-a-document-provider-extension"></a>Что такое расширение поставщика документа?

Проще говоря, расширение поставщика документа — это способ для разработчиком или третьей стороной, укажите в локальном хранилище альтернативные документа, к которым можно получить доступ точно так же, как в существующие хранилище iCloud.

Пользователь может выбрать одно из следующих расположений альтернативное хранилище с помощью средства выбора документа, и они могут использовать точное же режимы доступа (Open, импорта, перемещения или экспорта) для работы с файлами в этом расположении.

Это реализуется с помощью двух различных расширений:

-  **Расширение для выбора документов** — предоставляет `UIViewController` подкласса, который предоставляет графический интерфейс пользователя на выбор документа альтернативное расположение хранения. Этот подкласс будет отображаться как часть контроллера представления средство выбора документа.
-  **Укажите расширение файла** — это модуль без пользовательского интерфейса, который имеет дело с фактически предоставления содержимого файлов. Эти расширения, обеспечивается за счет координации файл ( `NSFileCoordinator` ). Это другой важный случай, когда необходим файл координации.


В примере ниже показан поток типовых данных при работе с расширениями поставщиков документа:

 [![](document-picker-images/image39.png "На этой схеме показан поток типовых данных при работе с расширениями поставщиков документа")](document-picker-images/image39.png#lightbox)

Следующая процедура выполняется:

1.  Приложение представляет контроллера средство выбора документа, чтобы разрешить пользователю выбрать файл для работы с.
1.  Пользователь выбирает опции альтернативный файл и пользовательский `UIViewController` расширения вызывается для отображения пользовательского интерфейса.
1.  Пользователь выбирает файл из этого расположения и URL-адрес передается обратно в средство выбора документа.
1.  Средство выбора документа выбирает URL-адрес файла и возвращает его приложению для пользователя для работы.
1.  URL-адрес передается координатору файл для возврата содержимого файлов в приложение.
1.  Координатор файл вызывает пользовательский расширение поставщика файлов для извлечения файла.
1.  Содержимое файла возвращаются к координатору файл.
1.  Содержимое файла возвращается в приложение.


### <a name="security-and-bookmarks"></a>Безопасность и закладки

В этом разделе займет кратко рассмотрим, как безопасность и постоянный доступ к файлам через works закладки с расширениями поставщиков документа. В отличие от iCloud поставщика документа, который автоматически сохраняет безопасности и создает закладки в контейнер приложения, расширения поставщика документов не так, как они не являются частью документа эталонной системы.

Например: на предприятии, предоставляет свой собственный безопасное хранилище данных для всей компании, администраторы не хотите конфиденциальной корпоративной информации доступ или не обработанные открытый iCloud серверов. Таким образом нельзя использовать встроенную систему ссылку документа.

По-прежнему используется система закладки, и он отвечает за расширение поставщика файлов, чтобы правильно обработать закладками URL-адреса и вернуть содержимое документа, на которые указывают его.

В целях безопасности iOS 8 имеет уровень изоляции, которая сохраняет информацию о том, какие приложения обращаются к какой идентификатор, в какой файл поставщик. Следует отметить, что весь файл доступ управляется этот уровень изоляции.

В примере ниже показан поток данных при работе с закладками и расширение поставщика документа:

 [![](document-picker-images/image40.png "На этой схеме показан поток данных при работе с расширением поставщика документов и закладки")](document-picker-images/image40.png#lightbox)

Следующая процедура выполняется:

1.  Приложение ко входу в фоновом режиме и требуется сохранить свое состояние. Он вызывает `NSUrl` для создания закладки в файл в альтернативное хранилище.
1.  `NSUrl` вызывает расширение поставщика файлов для получения постоянного URL-адрес для документа. 
1.  Расширение поставщика файлов возвращает URL-адрес в виде строки для `NSUrl` .
1.  `NSUrl` Объединяет URL-адрес в закладки и возвращает его приложению.
1.  Когда приложение awakes из процесса в фоновом режиме и необходимо восстановить состояние, он передает закладку `NSUrl` .
1.  `NSUrl` вызывает расширение поставщика файлов с помощью URL-адрес файла.
1.  Расширение поставщика файлов обращается к файлу и возвращает расположение файла для `NSUrl` .
1.  Расположение файла в состав сведения о безопасности и возвращаются в приложение.


На этой странице приложения доступа к файлу и работать с ними в обычном режиме.

### <a name="writing-files"></a>Запись файлов

В этом разделе займет кратко рассмотрим, как написание файлы в альтернативное расположение с works расширение поставщика документа. Приложение iOS будет использовать файл координации для сохранения информации на диск в контейнере приложения. Вскоре после успешной записи файла расширение поставщика файлов будет уведомления об изменениях.

На этом этапе расширение поставщика файлов можно начать отправку файла в альтернативное расположение (или пометьте этот файл как "грязный" и требовать передачи).

### <a name="creating-new-document-provider-extensions"></a>Создание нового документа поставщика расширений

Создание нового документа поставщика расширений находится вне области в этой вводной статье. Эта информация предоставляется здесь для показывают, что, в зависимости от расширения, которые пользователь загруженные в своем устройстве iOS, приложение может иметь доступ к документа места хранения за пределами Apple, предоставляемые расположение iCloud.

Разработчик должен учитывать этот факт при использовании средство выбора документа и работа с внешним документам. Они не предполагают размещения этих документов в iCloud.

Дополнительные сведения о создании поставщика хранилища или расширение Document Picker, см. в разделе [введение расширения приложения](~/ios/platform/extensions.md) документа.

## <a name="migrating-to-icloud-drive"></a>Переход на iCloud Drive

В iOS 8 пользователи могут продолжить использование существующих iCloud документов система используется в iOS 7 (и предыдущие системы) или можно перенести существующие документы в новый механизм диск iCloud.

В Mac OS X Yosemite, Apple не обеспечивает обратную совместимость поэтому все документы, которые должны быть перенесены в iCloud Drive или больше не будет обновляться на устройствах.

После переноса учетной записи пользователя iCloud Drive только для устройств с помощью iCloud Drive будут иметь возможность распространяют изменения в документы на этих устройствах.

> [!IMPORTANT]
> Разработчикам следует иметь в виду, что новые функции, описанные в данной статье доступна, только если учетная запись пользователя была перенесена iCloud Drive. 

## <a name="summary"></a>Сводка

Статья описывает изменения в существующие iCloud, API-интерфейсы необходимые для поддержки iCloud Drive и новый контроллер представления средство выбора документа. Он подробно рассматривается файл координации и почему это важно при работе с документами на основе облака. Описывает настройку, необходимую для включения документов на основе облака в приложение Xamarin.iOS и учитывая познакомимся работа с документами за пределами приложения контейнера приложения, с помощью контроллера представления средство выбора документа.

Кроме того в этой статье кратко описаны расширения поставщика документов и почему разработчик должен учитывать их при написании приложений, которые могут обрабатывать документы на основе облака.

## <a name="related-links"></a>Связанные ссылки

- [DocPicker (пример)](https://developer.xamarin.com/samples/monotouch/ios8/DocPicker/)
- [Введение в iOS 8](~/ios/platform/introduction-to-ios8.md)
- [Введение в расширения приложений](~/ios/platform/extensions.md)
