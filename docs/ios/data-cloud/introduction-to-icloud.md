---
title: С помощью iCloud с помощью Xamarin.iOS
description: В этом документе описывается iCloud и использовать его в приложениях Xamarin.iOS. В нем описывается хранилище ключ значение, хранилище документов и резервной копии в iCloud.
ms.prod: xamarin
ms.assetid: C6F3B87C-C195-4434-EF14-D66E63894F09
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/09/2016
ms.openlocfilehash: b72ecc40994d9336c4941f3db700796edd80e81f
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353221"
---
# <a name="using-icloud-with-xamarinios"></a>С помощью iCloud с помощью Xamarin.iOS

Хранилище iCloud API в iOS 5 позволяет приложениям сохранять пользовательские документы и данные приложения в центральном расположении и доступ к ним получить все его устройства.

Существует четыре типа хранилищ:

- **Хранилище ключ-значение** — для совместного использования небольших объемов данных с приложением на другим устройствам пользователя.

- **Хранилище данных** — для хранения документов и другие данные в учетной записи iCloud, используя подкласс данных.

- **CoreData** -хранилища базы данных SQLite.

- **Отдельные файлы и каталоги** — для управления множество различных файлов непосредственно в файловой системе.

В этом документе рассматриваются первых двух типов — пары "ключ-значение" и подклассы данных — и способы использования этих возможностей в Xamarin.iOS.

> [!IMPORTANT]
> Компания Apple [предоставляет инструменты](https://developer.apple.com/support/allowing-users-to-manage-data/), которые помогают разработчикам надлежащим образом соблюдать Общий регламент по защите данных Европейского союза (GDPR).

## <a name="requirements"></a>Требования

- Последняя стабильная версия Xamarin.iOS
- Xcode 8 или более поздней версии
- Visual Studio для Mac или Visual Studio 2015 и более поздних версиях.

## <a name="preparing-for-icloud-development"></a>Подготовка для разработки приложений iCloud

Приложения должны быть настроены для использования iCloud обоих в [портал подготовки Apple](https://developer.apple.com/account/ios/overview.action) и сам проект. Прежде чем разработка для iCloud (или примерами программирования) выполните следующие действия.

Для правильной настройки приложения для доступа к iCloud:

-   **Найти ваш TeamID** -войдите в [developer.apple.com](http://developer.apple.com) и посетите **Member Center > учетная запись > Сводка по учетной записи разработчика** идентификатор команды (или Individual ID в случае единственного разработчика ). Он будет строка 10-значный ( **A93A5CM278** к примеру)-это является частью «идентификатор контейнера».

-   **Создайте новый идентификатор приложения** - Чтобы создать идентификатор приложения, выполните действия, описанные в [подготовки для технологии Store разделе руководства по подготовке устройств](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md)и не забудьте проверить **iCloud** как Разрешенные службы:

 [![](introduction-to-icloud-images/icloud-sml.png "Проверьте iCloud как службу, разрешенных")](introduction-to-icloud-images/icloud.png#lightbox)

- **Создать новый профиль подготовки** - Чтобы создать профиль подготовки, выполните действия, описанные в [руководство по подготовке устройств](~/ios/get-started/installation/device-provisioning/index.md#provisioning-your-device) .

- **Добавьте идентификатор контейнера в Entitlements.plist** -формат идентификатора контейнера является `TeamID.BundleID`. Дополнительные сведения см. [работа с назначениями](~/ios/deploy-test/provisioning/entitlements.md) руководства.

- **Настройка свойств проекта** — в файле Info.plist, убедитесь, файл **идентификатор пакета** соответствует **идентификатор пакета** устанавливается, когда [Создание идентификатора приложения ](~/ios/deploy-test/provisioning/capabilities/index.md); Подписывание пакета iOS для этого используется **профиль подготовки** , содержащие идентификатор приложения с помощью службы приложений iCloud и **настраиваемые назначения** выбранный файл. Это все делается в Visual Studio в списке на панели свойств проекта.

- **Включить iCloud на вашем устройстве** - перейдите к **параметры > iCloud** и убедитесь, что устройство регистрируется в.
Выберите и включите **документы и данные** параметр.

- **Необходимо использовать устройство для тестирования iCloud** — он не будет работать в симуляторе.
На самом деле что нужно несколько устройств все для войти в один и тот же идентификатор Apple см. в разделе iCloud в действии.


## <a name="key-value-storage"></a>Хранилище ключ значение

Хранилище ключ значение предназначено для небольших объемов данных, который пользователь может хранить их для всех устройств — например страницы, на котором они просмотрели в книге или журнале. Хранилище ключ значение не должно использоваться для резервное копирование данных.

Существуют некоторые ограничения, которые следует учитывать при использовании хранилища ключ значение:

- **Максимальный размер ключа** -имен ключей не должна превышать 64 байта.

- **Максимальное значение размера** -не удается сохранить более 64 килобайт в одно значение.

- **Размер максимального хранилище значений ключей для приложения** -всего приложения могут хранить только размером до 64 килобайт данных ключ значение. Чтобы задать разделы за рамками этого ограничения не будут пройдены и предыдущее значение сохранится.

- **Типы данных** — только базовые типы, такие как строки, числа и логические значения могут храниться.

**ICloudKeyValue** примере показано, как это работает. В примере кода создается ключ с именем для каждого устройства: можно задать этот ключ на одном устройстве и наблюдайте за значением переданы другим пользователям. Он также создает ключ с именем «Shared», который можно изменять с любого устройства, если изменить сразу на несколько устройствах, iCloud будет определить, какую значение «wins» (с помощью метки времени на изменение) и возвращает распространяются.

На этом снимке экрана показан пример используется. При получении уведомления об изменениях из iCloud они выводились в прокрутки представления текста в нижней части экрана и обновляются в поля ввода.



 [![](introduction-to-icloud-images/icloud-kv-arrows.png "Поток сообщений между устройствами")](introduction-to-icloud-images/icloud-kv-arrows.png#lightbox)

### <a name="setting-and-retrieving-data"></a>Установка и получение данных

Этот код показывает, как задать строковое значение.

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.SetString("testkey", "VALUE IN THE CLOUD");  // key and value
store.Synchronize();
```

Вызов синхронизации гарантирует, что значение сохраняется только локальное дисковое хранилище. Синхронизация с iCloud происходит в фоновом режиме и не может быть «принудительно» кодом приложения. С помощью хорошее сетевое подключение синхронизации часто произойдет в течение 5 секунд, однако если сеть слишком низкая (или отключено) обновление может занять гораздо больше времени.

Можно извлечь значение следующим кодом:

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
display.Text = store.GetString("testkey");
```

Значение извлекается из локального хранилища данных — этот метод не пытается связаться с серверами iCloud, чтобы получить значение «latest». iCloud обновит локального хранилища данных в соответствии с расписанием свой собственный.

### <a name="deleting-data"></a>Удаление данных

Чтобы полностью удалить пару ключ значение, используйте метод Remove следующим образом:

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.Remove("testkey");
store.Synchronize();
```

### <a name="observing-changes"></a>Наблюдение за изменения

Приложение также может получать уведомления при изменении значений с iCloud, добавив наблюдатель `NSNotificationCenter.DefaultCenter`.
В следующем коде из **KeyValueViewController.cs** `ViewWillAppear` методе показано, как для прослушивания этих уведомлений и создать список, из которых были изменены ключи:

```csharp
keyValueNotification =
NSNotificationCenter.DefaultCenter.AddObserver (
    NSUbiquitousKeyValueStore.DidChangeExternallyNotification, notification => {
    Console.WriteLine ("Cloud notification received");
    NSDictionary userInfo = notification.UserInfo;

    var reasonNumber = (NSNumber)userInfo.ObjectForKey (NSUbiquitousKeyValueStore.ChangeReasonKey);
    nint reason = reasonNumber.NIntValue;

    var changedKeys = (NSArray)userInfo.ObjectForKey (NSUbiquitousKeyValueStore.ChangedKeysKey);
    var changedKeysList = new List<string> ();
    for (uint i = 0; i < changedKeys.Count; i++) {
        var key = changedKeys.GetItem<NSString> (i); // resolve key to a string
        changedKeysList.Add (key);
    }
    // now do something with the list...
});
```

Код затем может выполнять некоторые действия со списком измененных ключей, например обновление локальной копии их или обновление пользовательского интерфейса с новыми значениями.

Изменение возможные причины: ServerChange (0), InitialSyncChange (1) или QuotaViolationChange (2). Вы можете обращаться причину и при необходимости выполнить различную обработку (например, может потребоваться удалить некоторые разделы, в результате использования *QuotaViolationChange*).

## <a name="document-storage"></a>Хранилище документов

Документ хранилище iCloud предназначен для управления данными, важно, в приложение (и для пользователя). Его можно использовать для управления файлами и другими данными, необходимый вашему приложению для выполнения, в то же время предоставляя резервного копирования iCloud и общий доступ к функциональности на устройствах пользователей.

На этой схеме показано, как все это сочетается друг с другом. Каждое устройство имеет данные, сохраненные в локальном хранилище (UbiquityContainer) и iCloud операционной системы, которую управляющая программа отвечает за отправку и прием данных в облаке. Весь файл доступ к UbiquityContainer должны выполняться через FilePresenter/FileCoordinator, чтобы предотвратить одновременный доступ. `UIDocument` Класс реализует для вас; в этом примере показано использование данных.

 [![](introduction-to-icloud-images/icloud-overview.png "Общие сведения о документе хранилища")](introduction-to-icloud-images/icloud-overview.png#lightbox)

В примере iCloudUIDoc реализуется простой `UIDocument` подкласс, содержащий одно текстовое поле. Текст отображается в `UITextView` и изменения передаются с iCloud на других устройствах с уведомлением, выделяются красным цветом. В примере кода не работают с более сложные функции iCloud, как разрешение конфликтов.

На этом снимке экрана показан пример приложения — после изменения текста и нажав клавишу **UpdateChangeCount** синхронизации документа с помощью iCloud с другими устройствами.

 [![](introduction-to-icloud-images/iclouduidoc.png "На этом снимке экрана показан пример приложения после изменения текста и нажав клавишу UpdateChangeCount")](introduction-to-icloud-images/iclouduidoc.png#lightbox)

В примере iCloudUIDoc пять этапов:

1. **Доступ к UbiquityContainer** -определить, если включено iCloud и если да путь к области хранения приложения iCloud.

1. **Создание подкласса данных** -создание класса для промежуточного хранения iCloud и объектов модели.

1. **Поиск и открытие документов с iCloud** -использовать `NSFileManager` и `NSPredicate` найти документы iCloud и открыть их.

1. **Отображение документов в iCloud** -предоставляют свойства из вашего `UIDocument` , чтобы вы могли взаимодействовать с элементами управления пользовательского интерфейса.

1. **Сохранение документов в iCloud** -убедитесь, что изменения, внесенные в пользовательском Интерфейсе, сохраняются на диск и iCloud.

Все операции iCloud запуска (или должен выполняться) асинхронно, чтобы они не блокируются при ожидании происходящие события. Вы увидите три разных способа решения этой проблемы в этом примере:

 **Потоки** — в `AppDelegate.FinishedLaunching` начальный вызов `GetUrlForUbiquityContainer` выполняется в другом потоке, чтобы предотвратить блокировку основного потока.

 **NotificationCenter** - регистрации для получения уведомлений при асинхронной операции, такие как `NSMetadataQuery.StartQuery` завершения.

 **Обработчики завершения** - передачей методов для выполнения после завершения асинхронной операции, такие как `UIDocument.Open`.

### <a name="accessing-the-ubiquitycontainer"></a>Доступ к UbiquityContainer

Первым шагом в использовании iCloud хранения документов — определить включено iCloud и если да расположение «распространенность контейнера» (каталог, где включено iCloud файлы хранятся на устройстве).

Этот код находится в `AppDelegate.FinishedLaunching` метод выборки.

```csharp
// GetUrlForUbiquityContainer is blocking, Apple recommends background thread or your UI will freeze
ThreadPool.QueueUserWorkItem (_ => {
    CheckingForiCloud = true;
    Console.WriteLine ("Checking for iCloud");
    var uburl = NSFileManager.DefaultManager.GetUrlForUbiquityContainer (null);
    // OR instead of null you can specify "TEAMID.com.your-company.ApplicationName"

    if (uburl == null) {
        HasiCloud = false;
        Console.WriteLine ("Can't find iCloud container, check your provisioning profile and entitlements");

        InvokeOnMainThread (() => {
            var alertController = UIAlertController.Create ("No \uE049 available",
            "Check your Entitlements.plist, BundleId, TeamId and Provisioning Profile!", UIAlertControllerStyle.Alert);
            alertController.AddAction (UIAlertAction.Create ("OK", UIAlertActionStyle.Destructive, null));
            viewController.PresentViewController (alertController, false, null);
        });
    } else { // iCloud enabled, store the NSURL for later use
        HasiCloud = true;
        iCloudUrl = uburl;
        Console.WriteLine ("yyy Yes iCloud! {0}", uburl.AbsoluteUrl);
    }
    CheckingForiCloud = false;
});
```

Несмотря на то, что пример не делает, компания Apple рекомендует вызова GetUrlForUbiquityContainer всякий раз, когда приложение отображается на переднем плане.

### <a name="creating-a-uidocument-subclass"></a>Создание подкласса данных

Все iCloud файлы и каталоги (т. е. все сохраненные в каталоге UbiquityContainer) должно осуществляться с помощью методов NSFileManager, реализующей протокол NSFilePresenter и записи с помощью NSFileCoordinator.
Самый простой способ сделать все это — не передавать его самостоятельно, но подкласс данных, который сделает все за вас.

Существует только два метода, которые необходимо реализовать в подкласс данных для работы с iCloud:

- **LoadFromContents** -передает в NSData содержимое файла можно распаковать в модели классов/es.

- **ContentsForType** -запрос для указания NSData представлением модели класс/es для сохранения на диск (и в облаке).

Этот пример кода из **iCloudUIDoc\MonkeyDocument.cs** показан способ реализации данных.

```csharp
public class MonkeyDocument : UIDocument
{
    // the 'model', just a chunk of text in this case; must easily convert to NSData
    NSString dataModel;
    // model is wrapped in a nice .NET-friendly property
    public string DocumentString {
        get {
            return dataModel.ToString ();
        }
        set {
            dataModel = new NSString (value);
        }
    }
    public MonkeyDocument (NSUrl url) : base (url)
    {
        DocumentString = "(default text)";
    }
    // contents supplied by iCloud to display, update local model and display (via notification)
    public override bool LoadFromContents (NSObject contents, string typeName, out NSError outError)
    {
        outError = null;

        Console.WriteLine ("LoadFromContents({0})", typeName);

        if (contents != null)
            dataModel = NSString.FromData ((NSData)contents, NSStringEncoding.UTF8);

        // LoadFromContents called when an update occurs
        NSNotificationCenter.DefaultCenter.PostNotificationName ("monkeyDocumentModified", this);
        return true;
    }
    // return contents for iCloud to save (from the local model)
    public override NSObject ContentsForType (string typeName, out NSError outError)
    {
        outError = null;

        Console.WriteLine ("ContentsForType({0})", typeName);
        Console.WriteLine ("DocumentText:{0}",dataModel);

        NSData docData = dataModel.Encode (NSStringEncoding.UTF8);
        return docData;
    }
}
```

Модель данных в данном случае является очень простой - одно текстовое поле. Модель данных может быть сложным, если потребуется, такие как XML-документ или двоичные данные. Основную роль в реализации данных — преобразование между классах модели и представлением NSData, могут быть сохранены или загружены на диске.

### <a name="finding-and-opening-icloud-documents"></a>Поиск и открытие документов iCloud

Пример приложения обрабатывает только один файл - test.txt — поэтому код в **AppDelegate.cs** создает `NSPredicate` и `NSMetadataQuery` специально искать имя этого файла. `NSMetadataQuery` Выполняется асинхронно и отправляет уведомление о завершении. `DidFinishGathering` вызывается для уведомления наблюдателя, прекращает запрос и вызывает LoadDocument, который использует `UIDocument.Open` метод для загрузки файла и отображения его в завершающий обработчик `MonkeyDocumentViewController`.

```csharp
string monkeyDocFilename = "test.txt";
void FindDocument ()
{
    Console.WriteLine ("FindDocument");
    query = new NSMetadataQuery {
        SearchScopes = new NSObject [] { NSMetadataQuery.UbiquitousDocumentsScope }
    };

    var pred = NSPredicate.FromFormat ("%K == %@", new NSObject[] {
        NSMetadataQuery.ItemFSNameKey, new NSString (MonkeyDocFilename)
    });

    Console.WriteLine ("Predicate:{0}", pred.PredicateFormat);
    query.Predicate = pred;

    NSNotificationCenter.DefaultCenter.AddObserver (
        this,
        new Selector ("queryDidFinishGathering:"),
        NSMetadataQuery.DidFinishGatheringNotification,
        query
    );

    query.StartQuery ();
}

[Export ("queryDidFinishGathering:")]
void DidFinishGathering (NSNotification notification)
{
    Console.WriteLine ("DidFinishGathering");
    var metadataQuery = (NSMetadataQuery)notification.Object;
    metadataQuery.DisableUpdates ();
    metadataQuery.StopQuery ();

    NSNotificationCenter.DefaultCenter.RemoveObserver (this, NSMetadataQuery.DidFinishGatheringNotification, metadataQuery);
    LoadDocument (metadataQuery);
}

void LoadDocument (NSMetadataQuery metadataQuery)
{
    Console.WriteLine ("LoadDocument");

    if (metadataQuery.ResultCount == 1) {
        var item = (NSMetadataItem)metadataQuery.ResultAtIndex (0);
        var url = (NSUrl)item.ValueForAttribute (NSMetadataQuery.ItemURLKey);
        doc = new MonkeyDocument (url);

        doc.Open (success => {
            if (success) {
                Console.WriteLine ("iCloud document opened");
                Console.WriteLine (" -- {0}", doc.DocumentString);
                viewController.DisplayDocument (doc);
            } else {
                Console.WriteLine ("failed to open iCloud document");
            }
        });
    } // TODO: if no document, we need to create one
}
```

### <a name="displaying-icloud-documents"></a>Отображение документов в iCloud

Отображение данных не должен быть отличается для любого другого класса модели
- свойства отображаются в элементах управления пользовательского интерфейса, возможно изменено пользователем, а затем обратно в модель.

В примере **iCloudUIDoc\MonkeyDocumentViewController.cs** отображает текст MonkeyDocument в `UITextView`. `ViewDidLoad` прослушивает уведомление, отправленное `MonkeyDocument.LoadFromContents` метод. `LoadFromContents` вызывается при iCloud будет иметь новые данные для файла, таким образом, чтобы уведомление указывает, что документ был обновлен.

```csharp
NSNotificationCenter.DefaultCenter.AddObserver (this,
    new Selector ("dataReloaded:"),
    new NSString ("monkeyDocumentModified"),
    null
);
```

Обработчик уведомлений образец кода вызывает метод для обновления пользовательского интерфейса: в этом случае без обнаружения конфликтов и разрешения.

```csharp
[Export ("dataReloaded:")]
void DataReloaded (NSNotification notification)
{
    doc = (MonkeyDocument)notification.Object;
    // we just overwrite whatever was being typed, no conflict resolution for now
    docText.Text = doc.DocumentString;
}
```

### <a name="saving-icloud-documents"></a>Сохранение документов в iCloud

Для добавления данных в iCloud, можно вызвать `UIDocument.Save` напрямую (для новых документов только) или перемещения существующего файла с помощью `NSFileManager.DefaultManager.SetUbiquitious`. В примере кода создается новый документ напрямую в контейнере распространенность следующим кодом (два завершения обработчиков, один для `Save` операции, а другой для открытой):

```csharp
var docsFolder = Path.Combine (iCloudUrl.Path, "Documents"); // NOTE: Documents folder is user-accessible in Settings
var docPath = Path.Combine (docsFolder, MonkeyDocFilename);
var ubiq = new NSUrl (docPath, false);
var monkeyDoc = new MonkeyDocument (ubiq);
monkeyDoc.Save (monkeyDoc.FileUrl, UIDocumentSaveOperation.ForCreating, saveSuccess => {
Console.WriteLine ("Save completion:" + saveSuccess);
if (saveSuccess) {
    monkeyDoc.Open (openSuccess => {
        Console.WriteLine ("Open completion:" + openSuccess);
        if (openSuccess) {
            Console.WriteLine ("new document for iCloud");
            Console.WriteLine (" == " + monkeyDoc.DocumentString);
            viewController.DisplayDocument (monkeyDoc);
        } else {
            Console.WriteLine ("couldn't open");
        }
    });
} else {
    Console.WriteLine ("couldn't save");
}
```

Последующие изменения в документе не «сохраняются» напрямую, вместо этого сообщается о `UIDocument` , он был изменен с `UpdateChangeCount`, и он будет автоматически расписание сохранения для операции диска:

```csharp
doc.UpdateChangeCount (UIDocumentChangeKind.Done);
```

### <a name="managing-icloud-documents"></a>Управление документами iCloud

Пользователи могут управлять документы iCloud в **документов** каталог «container распространенность» вне вашего приложения с помощью параметров; они могут просматривать список файлов и проведите по экрану, чтобы удалить. Код приложения должен быть возможность обрабатывать ситуации, где документы удаляются пользователем. Не храните внутренним данным приложения в **документов** каталога.

 [![](introduction-to-icloud-images/icloudstorage.png "Управление рабочим процессом документов в iCloud")](introduction-to-icloud-images/icloudstorage.png#lightbox)



Пользователи также будут получать различные предупреждения, при попытке удалить приложение включено iCloud со своего устройства, чтобы уведомить пользователя о состоянии документы iCloud, относящиеся к этому приложению.

 [![](introduction-to-icloud-images/icloud-delete1.png "Пример диалогового окна, когда пользователь пытается удалить приложение включено iCloud со своего устройства")](introduction-to-icloud-images/icloud-delete1.png#lightbox)

 [![](introduction-to-icloud-images/icloud-delete2.png "Пример диалогового окна, когда пользователь пытается удалить приложение включено iCloud со своего устройства")](introduction-to-icloud-images/icloud-delete2.png#lightbox)

## <a name="icloud-backup"></a>резервной копии в iCloud

Хотя создание резервной копии iCloud не является компонентом, к которому осуществляется непосредственно разработчиками, на способ разработки приложения может повлиять на взаимодействие с пользователем.
Компания Apple предоставляет [iOS правила хранения данных](http://developer.apple.com/icloud/documentation/data-storage/) для разработчиков в свои приложения iOS.

Наиболее важным аспектом является ли приложение хранит большие файлы, которые не являются, созданное пользователем (например, приложение читатель журнала, которое хранит hundred-plus мегабайтах содержимого на одну проблему). Apple предпочитает не храните такого рода данных, где он будет иметь копии в iCloud и без необходимости заливки рамки квоты пользователя iCloud.

Приложения, в которых хранятся большие объемы данных следующим образом следует либо хранить его в одном из каталогов пользователей, не находится в сохраненный в резервной копии (например) Кэши или tmp) или использовать `NSFileManager.SetSkipBackupAttribute` для применения к этим файлам флаг, чтобы их игнорирует iCloud во время операций резервного копирования.

## <a name="summary"></a>Сводка

В этой статье представлена новая функция iCloud, включенные в iOS 5. Она проверяет действия, необходимые для настройки проекта для использования iCloud, затем приводятся примеры того, как для реализации возможностей iCloud.

В примере хранилище ключ значение показано, как можно использовать iCloud для хранения небольшой объем данных, аналогично тому, как хранятся NSUserPreferences. В примере данных показал тем, как более сложные данные можно хранить и синхронизировать между несколькими устройствами с помощью iCloud.

Наконец, оно включено краткое описание влияния при разработке приложения, в Добавление резервной копии в iCloud.



## <a name="related-links"></a>Связанные ссылки

- [Введение в iCloud (пример)](https://developer.xamarin.com/samples/monotouch/IntroductionToiCloud)
- [iCloud семинар пример кода](https://github.com/xamarin/Seminars/tree/master/2012-03-22-iCloud)
- [Семинар слайды iCloud](http://www.slideshare.net/Xamarin/using-icloud-with-monotouch)
- [iCloud NSUbiquitousKeyValueStore](https://developer.apple.com/library/prerelease/ios/)
- [Хранилище iCloud](http://support.apple.com/kb/HT4847)
