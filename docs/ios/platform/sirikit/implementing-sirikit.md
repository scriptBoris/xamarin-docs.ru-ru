---
title: Реализация SiriKit в Xamarin.iOS
description: В этом документе описываются шаги, необходимые для реализации поддержки SiriKit в приложениях Xamarin.iOS. Здесь рассматриваются способы расширения и расширения интерфейса Intents UI.
ms.prod: xamarin
ms.assetid: 20FFB981-EB10-48BA-BF79-40F37F0291EB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/03/2018
ms.openlocfilehash: ea037aaaac97d9f326f1a2fbcb28d97c9d8a9b45
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110255"
---
# <a name="implementing-sirikit-in-xamarinios"></a>Реализация SiriKit в Xamarin.iOS

_В этой статье рассматриваются шаги, необходимые для реализации поддержки SiriKit в приложениях Xamarin.iOS._

Новое в iOS 10, SiriKit позволяет приложения Xamarin.iOS для предоставления служб, доступных для пользователя с помощью Siri и приложения карты на устройстве iOS. В этой статье рассматриваются шаги, необходимые для реализации поддержки SiriKit в приложениях Xamarin.iOS, добавив необходимые расширения Intents, способы расширения пользовательского интерфейса и словарный запас.

Siri работает с концепцией **домены**, групп знать действия для связанных задач. Каждое взаимодействие с приложения с Siri необходимо относятся к одной из своих доменов известной службы следующим образом:

- Аудио- или видеосвязь.
- Резервирования поездки.
- Управление тренировок.
- Обмен сообщениями.
- Поиск фотографий.
- Отправлять или получать платежи.

Когда пользователь выполняет запрос Siri, связанных с одним расширение приложения служб, SiriKit отправляет расширение **намерение** объект, который описывает запрос пользователя, а также все дополнительные данные. Расширение приложения затем создает соответствующий **ответа** объекта для заданного **намерение**, о том, как расширение может обработать запрос.

В этом руководстве, представит краткий пример, в том числе поддержку SiriKit в существующее приложение. Для данного примера мы будем использовать фиктивное приложение MonkeyChat:

[![](implementing-sirikit-images/monkeychat01.png "Значок MonkeyChat")](implementing-sirikit-images/monkeychat01.png#lightbox)

MonkeyChat сохраняет свой собственный контактов книга друзей пользователя, каждый связанный с именем экрана (например, Bobo для примера) и пользователь может отправить текст обсуждения каждый друг по его отображаемому имени.

## <a name="extending-the-app-with-sirikit"></a>Расширение приложения с помощью SiriKit

Как показано в [основные сведения о понятиях SiriKit](~/ios/platform/sirikit/understanding-sirikit.md) руководство, в расширении возможностей приложения с помощью SiriKit участвуют три основные части:

[![](implementing-sirikit-images/elements01.png "Расширение приложения со схемой SiriKit")](implementing-sirikit-images/elements01.png#lightbox)

Сюда входит следующее.

1. **Расширение intents** -того, проверяет ответы пользователей приложение может обработать запрос и фактически выполняет задачу для выполнения запроса пользователя.
2. **Расширение intents UI** - *необязательно*, предоставляет удобный пользовательский Интерфейс ответов в среде Siri и можно добавить приложения пользовательского интерфейса и фирменной символики в Siri для расширения возможностей пользователя.
3. **Приложение** — предоставляет приложения с пользователем определенные словари для помощи в работе с ним Siri. 

Все эти элементы и действия, чтобы включить их в приложении будут рассматриваться подробно в следующих разделах.


## <a name="preparing-the-app"></a>Подготовка приложения

SiriKit лежит расширения, тем не менее, прежде чем добавлять какие-либо расширения в приложение, существует несколько действий, которые нужно сделать для помощи при внедрении SiriKit разработчику.

### <a name="moving-common-shared-code"></a>Перемещение общий код 

Во-первых, разработчик может переместить некоторые из общий код, который будет использоваться совместно между приложением и расширения в один _общих проектов_, _переносимых библиотек классов (PCL)_ или _собственного Библиотеки_.

Модули должны иметь возможность выполнять все действия, которые выполняет приложение. В терминах MonkeyChat примера приложения, например поиск контактов, добавления новых контактов, отправлять сообщения и получения сообщений журнала.

Путем перемещения общего кода в общий проект, PCL или собственной библиотеки он позволяет легко обслуживать этот код в одном месте, общие и позволяет расширение и родительское приложение предоставлять универсальный перспективах для пользователя.

В случае приложении-примере MonkeyChat модели данных и кода обработки, такие как доступ к сети и базы данных перемещаются в собственной библиотеки.

Выполните следующие действия:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

1. Запустите Visual Studio для Mac и открыть приложение MonkeyChat.
2. Щелкните правой кнопкой мыши имя решения на **панели решения** и выберите **добавить** > **новый проект...** : 

    [![](implementing-sirikit-images/prep01.png "Добавление нового проекта")](implementing-sirikit-images/prep01.png#lightbox)
3. Выберите **iOS** > **библиотеки** > **библиотеки классов** и нажмите кнопку **Далее** кнопки: 

    [![](implementing-sirikit-images/prep02.png "Выберите библиотеку классов")](implementing-sirikit-images/prep02.png#lightbox)
4. Введите `MonkeyChatCommon` для **имя** и нажмите кнопку **создать** кнопки: 

    [![](implementing-sirikit-images/prep03.png "Введите MonkeyChatCommon имени")](implementing-sirikit-images/prep03.png#lightbox)
5. Щелкните правой кнопкой мыши **ссылки** основного приложения в папке **обозревателе решений** и выберите **изменить ссылки...** . Проверьте **MonkeyChatCommon** проекта и нажмите кнопку **ОК** кнопки: 

    [![](implementing-sirikit-images/prep05.png "Проверка проекта MonkeyChatCommon")](implementing-sirikit-images/prep05.png#lightbox)
6. В **обозревателе решений**, перетащите общий код из основного приложения собственной библиотеки.
7. В случае MonkeyChat, перетащите **DataModels** и **процессоров** папки из основного приложения в собственной библиотеки: 

    [![](implementing-sirikit-images/prep06.png "DataModels и процессоры папки в обозревателе решений")](implementing-sirikit-images/prep06.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Запустите Visual Studio и откройте приложение MonkeyChat.
2. Щелкните правой кнопкой мыши имя решения на **обозревателе решений** и выберите **добавить** > **новый проект...** .
3. Выберите **Visual C#**   >  **общий проект** и нажмите кнопку **Далее** кнопки: 

    [![](implementing-sirikit-images/prep02.w157-sml.png "Выберите библиотеку классов")](implementing-sirikit-images/prep02.w157.png#lightbox)
4. Введите `MonkeyChatCommon` для **имя** и нажмите кнопку **создать** кнопки.
5. Щелкните правой кнопкой мыши **ссылки** основного приложения в папке **обозревателе решений** и выберите **изменить ссылки...** . Проверьте **MonkeyChatCommon** проекта и нажмите кнопку **ОК** кнопки: 

    [![](implementing-sirikit-images/prep05w.png "Проверка проекта MonkeyChatCommon")](implementing-sirikit-images/prep05w.png#lightbox)
6. В **обозревателе решений**, перетащите общий код из основного приложения в общий проект.
7. В случае MonkeyChat, перетащите **DataModels** и **процессоров** папки из основного приложения в собственной библиотеки.

-----

Любой из файлов, которые были перемещены на собственной библиотеки и измените пространство имен, чтобы он соответствовал библиотеки. Например, изменение `MonkeyChat` для `MonkeyChatCommon`:

```csharp
using System;
namespace MonkeyChatCommon
{
    /// <summary>
    /// A message sent from one user to another within a conversation.
    /// </summary>
    public class MonkeyMessage
    {
        public MonkeyMessage ()
        {
        }
        ...
    }
}
```

Затем вернитесь с основным приложением и добавьте `using` оператор для пространства имен в собственной библиотеки в любом приложение использует один из классов, которые были перемещены:

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;
using MonkeyChatCommon;

namespace MonkeyChat
{
    public partial class MasterViewController : UITableViewController
    {
        public DetailViewController DetailViewController { get; set; }

        DataSource dataSource;
        ...
    }
}
```

### <a name="architecting-the-app-for-extensions"></a>Разработка архитектуры приложения для расширения

Как правило приложение будет зарегистрироваться для нескольких целей и разработчику необходимо убедиться, что приложение предназначено для соответствующее количество расширений намерение.

В ситуации, где приложение требует более чем одной целью разработчик может выбрать размещение всех его намерения обработку в одно расширение намерение или создание использовать отдельное расширение намерения для каждой цели.

Если вы решили создать отдельное расширение намерения для каждой цели, разработчик может повторять большого объема стандартного кода в каждом расширении и создавать большой объем процессора и дополнительная нагрузка на память.

Чтобы выбрать один из двух вариантов, см. в разделе Если любой из намерений естественным образом связаны друг с другом. Например приложение, которое аудио и видео вызовы могут быть включены оба эти Intents в одном расширении намерения, как они обрабатываются схожих задач, что может привести большинство повторного использования кода.

Для любой цели или группа способов, которые не помещаются в существующую группу создайте новое расширение намерения в решении приложения должен содержать их.


### <a name="setting-the-required-entitlements"></a>Установка необходимых прав

Любое приложение Xamarin.iOS, которое включает интеграцию SiriKit, необходимо правильные права доступа задать. Если разработчик не задает эти необходимые назначения правильно, они не смогут для установки или тестирования приложений на оборудовании реальных iOS 10 (или выше), которая также является требованием с iOS 10 симулятор не поддерживает SiriKit.

Выполните следующие действия:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

1. Дважды щелкните `Entitlements.plist` файл **обозревателе решений** чтобы открыть его для редактирования.
2. Переключиться в режим **источника** вкладки.
3. Добавить `com.apple.developer.siri` **свойство**, задайте **тип** для `Boolean` и **значение** для `Yes`: 

    [![](implementing-sirikit-images/setup01.png "Добавить свойство com.apple.developer.siri")](implementing-sirikit-images/setup01.png#lightbox)
4. Сохраните изменения в файле.
5. Дважды щелкните **файл проекта** в **обозревателе решений** чтобы открыть его для редактирования.
6. Выберите **подписывание пакета iOS** и убедитесь, что `Entitlements.plist` выборе файла в **настраиваемые назначения** поля: 

    [![](implementing-sirikit-images/setup02.png "В поле настраиваемые назначения выберите файл Entitlements.plist")](implementing-sirikit-images/setup02.png#lightbox)
7. Нажмите кнопку **ОК**, чтобы сохранить изменения.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Дважды щелкните `Entitlements.plist` файл **обозревателе решений** чтобы открыть его для редактирования.
3. Добавить `com.apple.developer.siri` **свойство**, задайте **тип** для `Boolean` и **значение** для `Yes`: 

    [![](implementing-sirikit-images/setup01w.png "Добавить свойство com.apple.developer.siri")](implementing-sirikit-images/setup01w.png#lightbox)
4. Сохраните изменения в файле.
5. Дважды щелкните **файл проекта** в **обозревателе решений** чтобы открыть его для редактирования.
6. Выберите **подписывание пакета iOS** и убедитесь, что `Entitlements.plist` выборе файла в **настраиваемые назначения** поля.

-----

После завершения приложения `Entitlements.plist` файл должен выглядеть следующим образом (в открыть во внешнем редакторе):

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>com.apple.developer.siri</key>
    <true/>
</dict>
</plist>
```

### <a name="correctly-provisioning-the-app"></a>Правильно подготовки приложения

Из-за мер обеспечения безопасности, помещенный Apple вокруг framework SiriKit, любое приложение Xamarin.iOS, которое реализует SiriKit _необходимо_ имеют правильный идентификатор приложения и права (см. раздел выше) и должны быть подписаны строгим Профиль подготовки.

Выполните следующие действия на компьютере Mac:

1. В веб-браузер, перейдите к [ http://developer.apple.com ](http://developer.apple.com) и журналов в учетную запись.
2. Щелкните **сертификаты**, **идентификаторы** и **профили**.
3. Выберите **профили подготовки** и выберите **идентификаторов приложений**, затем нажмите кнопку **+** кнопки.
4. Введите **имя** нового профиля.
5. Введите **идентификатор пакета** следуя Apple именования элемента рекомендаций.
6. Прокрутите вниз до раздела **службы приложений** выберите **SiriKit** и нажмите кнопку **Продолжить** кнопки: 

    [![](implementing-sirikit-images/setup03.png "Выберите SiriKit")](implementing-sirikit-images/setup03.png#lightbox)
7. Проверьте все параметры, затем **отправить** идентификатор приложения.
8. Выберите **профили подготовки** > **разработки**, нажмите кнопку **+** кнопку, выберите **идентификатор Apple ID**, Нажмите кнопку **Продолжить**.
9. Нажмите кнопку выбрать **все**, затем нажмите кнопку **Продолжить**.
10. Нажмите кнопку **выбрать все** еще раз, нажмите кнопку **Продолжить**.
11. Введите **имя профиля** с помощью Apple именования элемента предложения, а затем **Продолжить**.
12. Запустите Xcode.
13. Выберите пункт Xcode **предпочтения...**
14. Выберите **учетные записи**, затем нажмите кнопку **Просмотр сведений о...** Кнопка: 

    [![](implementing-sirikit-images/setup04.png "Выберите учетные записи")](implementing-sirikit-images/setup04.png#lightbox)
15. Нажмите кнопку **скачать все профили** кнопку в левом нижнем углу: 

    [![](implementing-sirikit-images/setup05.png "Скачивание всех профилей")](implementing-sirikit-images/setup05.png#lightbox)
16. Убедитесь, что **профиль подготовки** созданные выше установлено в Xcode.
17. Откройте проект, чтобы добавить SiriKit поддержки, чтобы в Visual Studio для Mac.
18. Дважды щелкните `Info.plist` файл **обозревателе решений**.
18. Убедитесь, что **идентификатор пакета** совпадает со значением в портале разработчика Apple выше: 

    [![](implementing-sirikit-images/setup06.png "Идентификатор пакета")](implementing-sirikit-images/setup06.png#lightbox)
18. В **обозревателе решений**выберите **проекта**.
19. Щелкните правой кнопкой мыши проект и выберите **параметры**.
21. Выберите **подписывание пакета iOS**выберите **удостоверение подписывания** и **профиль подготовки** созданный выше: 

    [![](implementing-sirikit-images/setup07.png "Выберите удостоверение подписывания и профиль подготовки")](implementing-sirikit-images/setup07.png#lightbox)
22. Нажмите кнопку **ОК**, чтобы сохранить изменения.

> [!IMPORTANT]
> Тестирования SiriKit работает только на реальных iOS 10 аппаратного устройства, а не в iOS 10 симулятора. Если возникли проблемы при установке SiriKit приложения Xamarin.iOS на реальном оборудовании с поддержкой, убедитесь, что те же права, идентификатор приложения, идентификатор подписи и профиль подготовки правильно настроены в портале разработчика Apple и Visual Studio для Mac.

### <a name="requesting-siri-authorization"></a>Запрашивает авторизацию Siri

Прежде чем приложение добавляет любые словаря конкретного пользователя или расширения Intents подключается к Siri, он должен запрашивает авторизацию от пользователя для доступа к Siri.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

Изменить приложение `Info.plist` файл, перейдите в **источника** просмотра и добавления `NSSiriUsageDescription` ключа со строковым значением, описывающий, как приложение будет использовать Siri и новые типы данных будут отправляться. Например приложение MonkeyChat сказать «MonkeyChat контакты будут отправляться Siri»:

[![](implementing-sirikit-images/request01.png "NSSiriUsageDescription в редакторе Info.plist")](implementing-sirikit-images/request01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Изменить приложение `Info.plist` файл и добавьте `NSSiriUsageDescription` ключа со строковым значением, описывающий, как приложение будет использовать Siri и новые типы данных будут отправляться. Например приложение MonkeyChat сказать «MonkeyChat контакты будут отправляться Siri»:

[![](implementing-sirikit-images/request01w.png "NSSiriUsageDescription в редакторе Info.plist")](implementing-sirikit-images/request01w.png#lightbox)

-----

Вызовите `RequestSiriAuthorization` метод `INPreferences` класса при первом запуске приложения. Изменить `AppDelegate.cs` класса и сделать `FinishedLaunching` внешний метод следующим образом:


```csharp
using Intents;
...

public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{

    // Request access to Siri
    INPreferences.RequestSiriAuthorization ((INSiriAuthorizationStatus status) => {
        // Respond to returned status
        switch (status) {
        case INSiriAuthorizationStatus.Authorized:
            break;
        case INSiriAuthorizationStatus.Denied:
            break;
        case INSiriAuthorizationStatus.NotDetermined:
            break;
        case INSiriAuthorizationStatus.Restricted:
            break;
        }
    });


    return true;
}
```

При первом вызове этого метода, предупреждение отображается подтверждения пользователя, чтобы разрешить приложению доступ к Siri. Сообщение, которое разработчик добавляемый `NSSiriUsageDescription` выше, будет отображаться в этом предупреждении. Если пользователь изначально запрещает доступ, они могут использовать **параметры** приложения, чтобы предоставить доступ к приложению.

В любое время, приложение можно проверить возможность приложения к Siri путем вызова `SiriAuthorizationStatus` метод `INPreferences` класса.

### <a name="localization-and-siri"></a>Локализация и Siri

На устройстве iOS пользователю возможность выбрать язык для Siri, который отличается, а затем системной настройки по умолчанию. При работе с локализованные данные, приложение потребуется использовать `SiriLanguageCode` метод `INPreferences` класса для получения кода языка из Siri. Пример:

```csharp
var language = INPreferences.SiriLanguageCode();

// Take action based on language
if (language == "en-US") {
    // Do something...
}
```

### <a name="adding-user-specific-vocabulary"></a>Добавление словарь определенных пользователем

Словарь определенных пользователем будет обеспечивать слов или фраз, которые уникальны для отдельных пользователей приложения. Они будут представлены во время выполнения из основного приложения (не расширений приложений) в виде упорядоченного набора условий, упорядоченные в наиболее значимых приоритет по использованию для пользователей, наиболее важные условия в начале списка.

Словарь определенных пользователем должны принадлежать к одному из следующих категорий:

- Контактных имен (которые не управляются платформой контакты).
- Теги фото.
- Photo Album имена.
- Имена для тренировок.

При выборе терминология, регистрируемый в качестве пользовательского словаря только укажите условия, может быть неправильно понято любым пользователем, не знакомым с приложением. Никогда не register распространенных такие термины, «Мои тренировок» или «Мои альбома». Например приложение MonkeyChat зарегистрирует псевдонимы, связанные с обращением к каждому в адресной книге пользователя.

Приложение предоставляет словарь определенных пользователем путем вызова `SetVocabularyStrings` метод `INVocabulary` класса и передавая `NSOrderedSet` коллекции из основного приложения. Приложение всегда должно вызвать `RemoveAllVocabularyStrings` метод первой, чтобы удалить все существующие условия, прежде чем добавлять новые. Пример:

```csharp
using System;
using System.Linq;
using System.Collections.Generic;
using Foundation;
using Intents;

namespace MonkeyChatCommon
{
    public class MonkeyAddressBook : NSObject
    {
        #region Computed Properties
        public List<MonkeyContact> Contacts { get; set; } = new List<MonkeyContact> ();
        #endregion

        #region Constructors
        public MonkeyAddressBook ()
        {
        }
        #endregion

        #region Public Methods
        public NSOrderedSet<NSString> ContactNicknames ()
        {
            var nicknames = new NSMutableOrderedSet<NSString> ();

            // Sort contacts by the last time used
            var query = Contacts.OrderBy (contact => contact.LastCalledOn);

            // Assemble ordered list of nicknames by most used to least
            foreach (MonkeyContact contact in query) {
                nicknames.Add (new NSString (contact.ScreenName));
            }

            // Return names
            return new NSOrderedSet<NSString> (nicknames.AsSet ());
        }

        // This method MUST only be called on a background thread!
        public void UpdateUserSpecificVocabulary ()
        {
            // Clear any existing vocabulary
            INVocabulary.SharedVocabulary.RemoveAllVocabularyStrings ();

            // Register new vocabulary
            INVocabulary.SharedVocabulary.SetVocabularyStrings (ContactNicknames (), INVocabularyStringType.ContactName);
        }
        #endregion
    }
}
```

Этот код в месте он может быть вызвана следующим образом:

```csharp
using System;
using System.Threading;
using UIKit;
using MonkeyChatCommon;
using Intents;

namespace MonkeyChat
{
    public partial class ViewController : UIViewController
    {
        #region AppDelegate Access
        public AppDelegate ThisApp {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do we have access to Siri?
            if (INPreferences.SiriAuthorizationStatus == INSiriAuthorizationStatus.Authorized) {
                // Yes, update Siri's vocabulary
                new Thread (() => {
                    Thread.CurrentThread.IsBackground = true;
                    ThisApp.AddressBook.UpdateUserSpecificVocabulary ();
                }).Start ();
            }
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion
    }
}
```

> [!IMPORTANT]
> Siri считает пользовательского словаря, подсказки и будет включать как можно большую часть терминологии максимально. Тем не менее, пространство для пользовательского словаря ограничено, что необходимо зарегистрировать _только_ терминологии, может ввести в заблуждение, поэтому свести общее число зарегистрированных условия к минимуму.

Дополнительные сведения см. в разделе наших [конкретная справочная словаря пользователя](~/ios/platform/sirikit/understanding-sirikit.md) и Apple [Указание пользовательского словаря ссылку](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1).

### <a name="adding-app-specific-vocabulary"></a>Добавление приложения лексикона.

Владеют специальной терминологией приложения определяет конкретные слова и фразы, будет доступен для всех пользователей приложения, такие как типы автомобиля или имена для тренировок. Так как они являются частью приложения, они определяются в `AppIntentVocabulary.plist` файл как часть набора основного приложения. Кроме того эти слова и фразы должно быть локализовано.

Термины определенного приложения должны принадлежать к одному из следующих категорий:

- Переопределение параметров.
- Имена для тренировок.

Файл владеют специальной терминологией приложение содержит две корневые ключи уровня:

- `ParameterVocabularies` **Требуется** -определяет настраиваемые условия и параметры намерения, они применяются к приложения.
- `IntentPhrases` **Необязательный** -содержит пример фразы с помощью пользовательских термины, определенные в `ParameterVocabularies`.

Каждая запись в `ParameterVocabularies` необходимо указать строку идентификатора, термин и цели, к которому применяется термин. Кроме того один термин может применяться для нескольких целей.

Полный список допустимых значений и структуру необходимый файл, см. в разделе Apple [справочнике по формату файла словаря приложения](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CustomVocabularyKeys.html#//apple_ref/doc/uid/TP40016875-CH10-SW1).

Чтобы добавить `AppIntentVocabulary.plist` файл в проект приложения, выполните следующие действия:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

1. Щелкните правой кнопкой мыши имя проекта в **обозревателе решений** и выберите **добавить** > **новый файл...**   >  **iOS**:

    [![](implementing-sirikit-images/plist01.png "Добавить список свойств")](implementing-sirikit-images/plist01.png#lightbox)
2. Дважды щелкните `AppIntentVocabulary.plist` файл **обозревателе решений** чтобы открыть его для редактирования.
3. Нажмите кнопку **+** Чтобы добавить ключ, установите **имя** для `ParameterVocabularies` и **тип** для `Array`:

    [![](implementing-sirikit-images/plist02.png "Задайте имя ParameterVocabularies и тип массива")](implementing-sirikit-images/plist02.png#lightbox)
4. Разверните `ParameterVocabularies` и нажмите кнопку **+** кнопку и задайте **тип** для `Dictionary`:

    [![](implementing-sirikit-images/plist03.png "Задайте тип словаря")](implementing-sirikit-images/plist03.png#lightbox)
5. Нажмите кнопку **+** Чтобы добавить новый раздел, задайте **имя** для `ParameterNames` и **тип** для `Array`:

    [![](implementing-sirikit-images/plist04.png "Задайте имя ParameterNames и тип массива")](implementing-sirikit-images/plist04.png#lightbox)
6. Нажмите кнопку **+** Чтобы добавить новый раздел со **тип** из `String` и значение в один из доступных имен параметров. Например `INStartWorkoutIntent.workoutName`:

    [![](implementing-sirikit-images/plist05.png "Ключ INStartWorkoutIntent.workoutName")](implementing-sirikit-images/plist05.png#lightbox)
7. Добавить `ParameterVocabulary` ключа `ParameterVocabularies` ключа с **тип** из `Array`:

    [![](implementing-sirikit-images/plist06.png "Добавление ParameterVocabulary ключа для ключа ParameterVocabularies с тип массива")](implementing-sirikit-images/plist06.png#lightbox)
8. Добавьте новый раздел с **тип** из `Dictionary`:

    [![](implementing-sirikit-images/plist07.png "Добавьте новый раздел со словарем из типа")](implementing-sirikit-images/plist07.png#lightbox)
9. Добавить `VocabularyItemIdentifier` ключа с **тип** из `String` и укажите уникальный идентификатор для условия:

    [![](implementing-sirikit-images/plist08.png "Добавьте раздел VocabularyItemIdentifier со строковым и укажите уникальный идентификатор")](implementing-sirikit-images/plist08.png#lightbox)
10. Добавить `VocabularyItemSynonyms` ключа с **тип** из `Array`:

    [![](implementing-sirikit-images/plist09.png "Добавить ключ VocabularyItemSynonyms с тип массива")](implementing-sirikit-images/plist09.png#lightbox)
11. Добавьте новый раздел с **тип** из `Dictionary`:

    [![](implementing-sirikit-images/plist10.png "Добавьте новый раздел со словарем из типа")](implementing-sirikit-images/plist10.png#lightbox)
12. Добавить `VocabularyItemPhrase` ключа с **тип** из `String` и термин при определении приложения:

    [![](implementing-sirikit-images/plist11.png "Добавить ключ VocabularyItemPhrase с строковый тип и условия, при определении приложения")](implementing-sirikit-images/plist11.png#lightbox)
13. Добавить `VocabularyItemPronunciation` ключа с **тип** из `String` и фонетическое произношение термина:

    [![](implementing-sirikit-images/plist12.png "Добавить ключ VocabularyItemPronunciation с строковый тип и фонетическое произношение термина")](implementing-sirikit-images/plist12.png#lightbox)
14. Добавить `VocabularyItemExamples` ключа с **тип** из `Array`:

    [![](implementing-sirikit-images/plist13.png "Добавить ключ VocabularyItemExamples с тип массива")](implementing-sirikit-images/plist13.png#lightbox)
15. Добавить несколько `String` ключи с примерами использования термина:

    [![](implementing-sirikit-images/plist14.png "Добавьте несколько строковых ключей с примерами использования термина")](implementing-sirikit-images/plist14.png#lightbox)
16. Повторите описанные выше действия для любые другие пользовательские условия, необходимо определить приложение.
17. Свернуть `ParameterVocabularies` ключ.
18. Добавить `IntentPhrases` ключа с **тип** из `Array`:

    [![](implementing-sirikit-images/plist15.png "Добавить ключ IntentPhrases с тип массива")](implementing-sirikit-images/plist15.png#lightbox)
19. Добавьте новый раздел с **тип** из `Dictionary`:

    [![](implementing-sirikit-images/plist16.png "Добавьте новый раздел со словарем из типа")](implementing-sirikit-images/plist16.png#lightbox)
20. Добавить `IntentName` ключа с **тип** из `String` и намерений для примера:

    [![](implementing-sirikit-images/plist17.png "Добавьте раздел IntentName со строкового типа и только для чтения, для примера")](implementing-sirikit-images/plist17.png#lightbox)
21. Добавить `IntentExamples` ключа с **тип** из `Array`:

    [![](implementing-sirikit-images/plist18.png "Добавить ключ IntentExamples с тип массива")](implementing-sirikit-images/plist18.png#lightbox)
22. Добавить несколько `String` ключи с примерами использования термина:

    [![](implementing-sirikit-images/plist19.png "Добавьте несколько строковых ключей с примерами использования термина")](implementing-sirikit-images/plist19.png#lightbox)
23. Повторите описанные выше действия для любого намерения приложения необходимо выполнить пример использования.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Щелкните правой кнопкой мыши имя проекта в **обозревателе решений** и выберите **Добавить > новый элемент... > Apple > список свойств > Info.plist**:

    [![](implementing-sirikit-images/plist01.w157-sml.png "Добавить новый Info.plist")](implementing-sirikit-images/plist01.w157.png#lightbox)

2. Дважды щелкните `AppIntentVocabulary.plist` файл **обозревателе решений** чтобы открыть его для редактирования.
3. Нажмите кнопку **+** Чтобы добавить ключ, установите **имя** для `ParameterVocabularies` и **тип** для `Array`:

    [![](implementing-sirikit-images/plist02w.png "Задайте имя ParameterVocabularies и тип массива")](implementing-sirikit-images/plist02w.png#lightbox)
4. Разверните `ParameterVocabularies` и нажмите кнопку **+** кнопку и задайте **тип** для `Dictionary`:

    [![](implementing-sirikit-images/plist03w.png "Задайте тип словаря")](implementing-sirikit-images/plist03w.png#lightbox)
5. Нажмите кнопку **+** Чтобы добавить новый раздел, задайте **имя** для `ParameterNames` и **тип** для `Array`:

    [![](implementing-sirikit-images/plist04w.png "Задайте имя ParameterNames и тип массива")](implementing-sirikit-images/plist04w.png#lightbox)
6. Нажмите кнопку **+** Чтобы добавить новый раздел со **тип** из `String` и значение в один из доступных имен параметров. Например `INStartWorkoutIntent.workoutName`:

    [![](implementing-sirikit-images/plist05w.png "Ключ INStartWorkoutIntent.workoutName")](implementing-sirikit-images/plist05w.png#lightbox)
7. Добавить `ParameterVocabulary` ключа `ParameterVocabularies` ключа с **тип** из `Array`:

    [![](implementing-sirikit-images/plist06w.png "Добавление ParameterVocabulary ключа для ключа ParameterVocabularies с тип массива")](implementing-sirikit-images/plist06w.png#lightbox)
8. Добавьте новый раздел с **тип** из `Dictionary`:

    [![](implementing-sirikit-images/plist07w.png "Добавьте новый раздел со словарем из типа")](implementing-sirikit-images/plist07w.png#lightbox)
9. Добавить `VocabularyItemIdentifier` ключа с **тип** из `String` и укажите уникальный идентификатор для условия:

    [![](implementing-sirikit-images/plist08w.png "Добавьте раздел VocabularyItemIdentifier со строковым и укажите уникальный идентификатор для условия")](implementing-sirikit-images/plist08w.png#lightbox)
10. Добавить `VocabularyItemSynonyms` ключа с **тип** из `Array`:

    [![](implementing-sirikit-images/plist09w.png "Добавить ключ VocabularyItemSynonyms с тип массива")](implementing-sirikit-images/plist09w.png#lightbox)
11. Добавьте новый раздел с **тип** из `Dictionary`:

    [![](implementing-sirikit-images/plist10w.png "Добавьте новый раздел со словарем из типа")](implementing-sirikit-images/plist10w.png#lightbox)
12. Добавить `VocabularyItemPhrase` ключа с **тип** из `String` и термин при определении приложения:

    [![](implementing-sirikit-images/plist11w.png "Добавить ключ VocabularyItemPhrase с строковый тип и условия, при определении приложения")](implementing-sirikit-images/plist11w.png#lightbox)
13. Добавить `VocabularyItemPronunciation` ключа с **тип** из `String` и фонетическое произношение термина:

    [![](implementing-sirikit-images/plist12w.png "Добавить ключ VocabularyItemPronunciation с строковый тип и фонетическое произношение термина")](implementing-sirikit-images/plist12w.png#lightbox)
14. Добавить `VocabularyItemExamples` ключа с **тип** из `Array`:

    [![](implementing-sirikit-images/plist13w.png "Добавить ключ VocabularyItemExamples с тип массива")](implementing-sirikit-images/plist13w.png#lightbox)
15. Добавить несколько `String` ключи с примерами использования термина:

    [![](implementing-sirikit-images/plist14w.png "Добавьте несколько строковых ключей с примерами использования термина")](implementing-sirikit-images/plist14w.png#lightbox)
16. Повторите описанные выше действия для любые другие пользовательские условия, необходимо определить приложение.
17. Свернуть `ParameterVocabularies` ключ.
18. Добавить `IntentPhrases` ключа с **тип** из `Array`:

    [![](implementing-sirikit-images/plist15w.png "Добавить ключ IntentPhrases с тип массива")](implementing-sirikit-images/plist15w.png#lightbox)
19. Добавьте новый раздел с **тип** из `Dictionary`:

    [![](implementing-sirikit-images/plist16w.png "Добавьте новый раздел со словарем из типа")](implementing-sirikit-images/plist16w.png#lightbox)
20. Добавить `IntentName` ключа с **тип** из `String` и намерений для примера:

    [![](implementing-sirikit-images/plist17w.png "Добавьте раздел IntentName со строкового типа и только для чтения, для примера")](implementing-sirikit-images/plist17w.png#lightbox)
21. Добавить `IntentExamples` ключа с **тип** из `Array`:

    [![](implementing-sirikit-images/plist18w.png "Добавить ключ IntentExamples с тип массива")](implementing-sirikit-images/plist18w.png#lightbox)
22. Добавить несколько `String` ключи с примерами использования термина:

    [![](implementing-sirikit-images/plist19w.png "Добавьте несколько строковых ключей с примерами использования термина")](implementing-sirikit-images/plist19w.png#lightbox)
23. Повторите описанные выше действия для любого намерения приложения необходимо выполнить пример использования.

-----

> [!IMPORTANT]
> `AppIntentVocabulary.plist` Будут зарегистрированы с Siri на тестовых устройств во время разработки и он может занять некоторое время для Siri для включения пользовательского словаря. Таким образом тест-инженер придется подождать несколько минут, прежде чем тестировать владеют специальной терминологией приложения, если он был обновлен.

Дополнительные сведения см. в разделе наших [конкретная справочная словарь приложения](~/ios/platform/sirikit/understanding-sirikit.md) и Apple [Указание пользовательского словаря ссылку](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1).

## <a name="adding-an-intents-extension"></a>Добавление расширение Intents

Теперь, когда приложение было подготовлено внедрить SiriKit, разработчику нужно будет добавить расширения Intents один (или более) в решение для обработки намерения, необходимые для интеграции Siri.

Для каждой требуется расширение Intents сделайте следующее:

- Добавьте расширение Intents проект в решение приложения Xamarin.iOS.
- Настройка расширения Intents `Info.plist` файл.
- Измените основной класс расширения Intents.

Дополнительные сведения см. в разделе наших [ссылку на расширение Intents](~/ios/platform/sirikit/understanding-sirikit.md) и Apple [Создание ссылку на расширение Intents](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CreatingtheIntentsExtension.html#//apple_ref/doc/uid/TP40016875-CH4-SW1).

### <a name="creating-the-extension"></a>Создание расширения

Чтобы добавить расширение Intents решение, сделайте следующее:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

1. Щелкните правой кнопкой мыши **имя решения** в **панели решения** и выберите **добавить** > **добавить новый проект...** .
2. В диалоговом окне выберите **iOS** > **расширения** > **расширение намерение** и нажмите кнопку **Далее** кнопка: 

    [![](implementing-sirikit-images/intents05.png "Выберите расширение намерений")](implementing-sirikit-images/intents05.png#lightbox)
3. Далее введите **имя** намерение расширение и нажмите кнопку **Далее** кнопки: 

    [![](implementing-sirikit-images/intents06.png "Введите имя для модуля намерений")](implementing-sirikit-images/intents06.png#lightbox)
4. Наконец, нажмите кнопку **создать** кнопку, чтобы добавить расширение намерения в решение приложения: 

    [![](implementing-sirikit-images/intents07.png "Добавьте расширение намерения в решение приложения")](implementing-sirikit-images/intents07.png#lightbox)
5. В **обозревателе решений**, щелкните правой кнопкой мыши **ссылки** папку только что созданный намерение расширения. Проверьте имя Общие библиотеки проекта общего кода (создания приложения выше) и нажмите кнопку **ОК** кнопки: 

    [![](implementing-sirikit-images/intents08.png "Выберите имя проекта библиотеки common общего кода")](implementing-sirikit-images/intents08.png#lightbox)
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Щелкните правой кнопкой мыши **имя решения** в **обозревателе решений** и выберите **добавить** > **добавить новый проект...** .
2. В диалоговом окне выберите **Visual C# > расширения iOS > расширение намерение** и нажмите кнопку **Далее** кнопки:

    [![](implementing-sirikit-images/intents05.w157-sml.png "Выберите расширение намерений")](implementing-sirikit-images/intents05.w157.png#lightbox)
3. Далее введите **имя** намерение расширение и нажмите кнопку **ОК** кнопки.
1. В **обозревателе решений**, щелкните правой кнопкой мыши **ссылки** папку созданного расширения Intents и выберите **Добавить > ссылка**. Проверьте имя Общие библиотеки проекта общего кода (создания приложения выше) и нажмите кнопку **ОК** кнопки:

    [![](implementing-sirikit-images/intents08w.png "Выберите имя проекта библиотеки common общего кода")](implementing-sirikit-images/intents08w.png#lightbox)
    
-----

Повторите эти шаги для число расширений намерение (на основе [архитектуры приложения для расширения](#Architecting-the-App-for-Extensions) раздел выше), приложение потребует.

### <a name="configuring-the-infoplist"></a>Настройка файла Info.plist

Для каждого расширения Intents, которые были добавлены решение приложения, должны быть настроены в `Info.plist` файлы для работы с приложением.

Так же, как любой типичный расширение приложения, приложение будет включать существующие ключи `NSExtension` и `NSExtensionAttributes`. Для расширения Intents существует два новых атрибута, которые должны быть настроены:

[![](implementing-sirikit-images/intents01.png "Два новых атрибута, которые должны быть настроены")](implementing-sirikit-images/intents01.png#lightbox)

- **IntentsSupported** — требуется и состоит из массив имен классов намерение приложение хочет поддерживать из расширения намерение.
- **IntentsRestrictedWhileLocked** -является необязательный ключ для приложения задать поведение экрана блокировки расширения. Он состоит из массив имен классов намерение приложение хочет пользователь должен выполнить вход для использования из расширения намерение.

Чтобы настроить модуль намерение `Info.plist` файл, дважды щелкните его в **обозревателе решений** чтобы открыть его для редактирования. Затем переключиться в режим **источника** просмотреть, а затем разверните `NSExtension` и `NSExtensionAttributes` ключи в редакторе:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![](implementing-sirikit-images/intents02.png "Ключи NSExtension и NSExtensionAttributes в редакторе")](implementing-sirikit-images/intents02.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents02w.png "Ключи NSExtension и NSExtensionAttributes в редакторе")](implementing-sirikit-images/intents02w.png#lightbox)

-----

Разверните `IntentsSupported` ключа и добавьте имя класса намерения, это расширение будет поддерживать. Для примера приложения MonkeyChat, он поддерживает `INSendMessageIntent`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![](implementing-sirikit-images/intents09.png "Ключ INSendMessageIntent")](implementing-sirikit-images/intents09.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents09w.png "Ключ INSendMessageIntent")](implementing-sirikit-images/intents09w.png#lightbox)

-----

Если приложению требуется при необходимости от пользователя входа в систему на устройстве с помощью заданной цели, разверните `IntentRestrictedWhileLocked` ключа и добавьте имена классов способов, с ограниченным доступом. Для примера приложения MonkeyChat, пользователю необходимо войти для отправки мгновенного сообщения, поэтому мы добавили `INSendMessageIntent`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![](implementing-sirikit-images/intents10.png "Добавлена INSendMessageIntent ключ")](implementing-sirikit-images/intents10.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents10w.png "Добавлена INSendMessageIntent ключ")](implementing-sirikit-images/intents10w.png#lightbox)

-----


Полный список доступных доменов намерения, см. в разделе Apple [ссылку доменов намерение](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2).

### <a name="configuring-the-main-class"></a>Настройка основного класса

После этого разработчику необходимо настроить основной класс, который выступает в качестве главную точку входа для расширения намерения в Siri. Он должен быть подклассом `INExtension` которого соответствует `IINIntentHandler` делегировать. Пример:

```csharp
using System;
using System.Collections.Generic;

using Foundation;
using Intents;

namespace MonkeyChatIntents
{
    [Register ("IntentHandler")]
    public class IntentHandler : INExtension, IINSendMessageIntentHandling, IINSearchForMessagesIntentHandling, IINSetMessageAttributeIntentHandling
    {
        #region Constructors
        protected IntentHandler (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override NSObject GetHandler (INIntent intent)
        {
            // This is the default implementation.  If you want different objects to handle different intents,
            // you can override this and return the handler you want for that particular intent.

            return this;
        }
        #endregion
        ...
    }
}
```

Имеется один метод одиночные, приложение должно реализовать на основной класс расширения намерение `GetHandler` метод. Этот метод передается объект Intent по SiriKit и приложение должно возвращать **обработчик намерение** , соответствующий типу данного способа.

Так как пример приложения MonkeyChat обрабатывает только одна цель, оно возвращает сам в `GetHandler` метод. Если расширение обработано более чем одной целью, разработчик будет добавлен класс для каждого типа намерения и возврата экземпляра на основе `Intent` передается в метод.

### <a name="handling-the-resolve-stage"></a>Обработка на этапе разрешения

В рабочей области, разрешения которых расширение намерение будет прояснить и проверки параметров передается в из Siri и были заданы с помощью диалога пользователя.

Для каждого параметра, который отправляется из Siri, `Resolve` метод. Приложение будет необходимо реализовать этот метод для каждого параметра, что приложение может нужна помощь Siri, чтобы получить правильный ответ от пользователя.

В случае MonkeyChat приложении-примере расширение намерение потребует одного или нескольких получателей для отправки сообщения. Для каждого получателя, в списке расширение необходимо сделать поиска контактов, который можно будет получен следующий результат:

- Найден ровно один соответствующий запросу контакт.
- Найдено два или более подходящие контакты.
- Нет сопоставления контактов не найдены.

Кроме того MonkeyChat требует содержимое тела сообщения. Если пользователь не предоставил это, Siri необходимо запрашивать пользователя для содержимого.

Расширение намерение будет должны правильно обрабатывать каждый из этих случаев.


```csharp
[Export ("resolveRecipientsForSearchForMessages:withCompletion:")]
public void ResolveRecipients (INSendMessageIntent intent, Action<INPersonResolutionResult []> completion)
{
    var recipients = intent.Recipients;
    // If no recipients were provided we'll need to prompt for a value.
    if (recipients.Length == 0) {
        completion (new INPersonResolutionResult [] { (INPersonResolutionResult)INPersonResolutionResult.NeedsValue });
        return;
    }

    var resolutionResults = new List<INPersonResolutionResult> ();

    foreach (var recipient in recipients) {
        var matchingContacts = new INPerson [] { recipient }; // Implement your contact matching logic here to create an array of matching contacts
        if (matchingContacts.Length > 1) {
            // We need Siri's help to ask user to pick one from the matches.
            resolutionResults.Add (INPersonResolutionResult.GetDisambiguation (matchingContacts));
        } else if (matchingContacts.Length == 1) {
            // We have exactly one matching contact
            resolutionResults.Add (INPersonResolutionResult.GetSuccess (recipient));
        } else {
            // We have no contacts matching the description provided
            resolutionResults.Add ((INPersonResolutionResult)INPersonResolutionResult.Unsupported);
        }
    }

    completion (resolutionResults.ToArray ());
}

[Export ("resolveContentForSendMessage:withCompletion:")]
public void ResolveContent (INSendMessageIntent intent, Action<INStringResolutionResult> completion)
{
    var text = intent.Content;
    if (!string.IsNullOrEmpty (text))
        completion (INStringResolutionResult.GetSuccess (text));
    else
        completion ((INStringResolutionResult)INStringResolutionResult.NeedsValue);
}
```

Дополнительные сведения см. в разделе наших [ссылку этап устранения](~/ios/platform/sirikit/understanding-sirikit.md) и Apple [Resolving и обработка ссылку намерения](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ResolvingandHandlingIntents.html#//apple_ref/doc/uid/TP40016875-CH5-SW1).

### <a name="handling-the-confirm-stage"></a>Обработка Confirm рабочей области

На этапе подтверждения — где расширение намерение проверяет наличие всех данных для выполнения запроса пользователя. Приложение хочет отправить подтверждение вдоль будут все вспомогательные сведения о том, что собирается выполнить происходят для Siri, он может быть подтверждена с пользователем, это требуемое действие.

```csharp
[Export ("confirmSendMessage:completion:")]
public void ConfirmSendMessage (INSendMessageIntent intent, Action<INSendMessageIntentResponse> completion)
{
    // Verify user is authenticated and the app is ready to send a message.
    ...

    var userActivity = new NSUserActivity (nameof (INSendMessageIntent));
    var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Ready, userActivity);
    completion (response);
}
```

Дополнительные сведения см. в разделе наших [ссылку подтверждения этап](~/ios/platform/sirikit/understanding-sirikit.md).

### <a name="processing-the-intent"></a>Цель обработки

Это точка, где расширение намерение фактически выполняет задачу для выполнения запроса пользователя и передает результаты обратно Siri, поэтому пользователь получает уведомление о.


```csharp
public void HandleSendMessage (INSendMessageIntent intent, Action<INSendMessageIntentResponse> completion)
{
    // Implement the application logic to send a message here.
    ...

    var userActivity = new NSUserActivity (nameof (INSendMessageIntent));
    var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, userActivity);
    completion (response);
}

public void HandleSearchForMessages (INSearchForMessagesIntent intent, Action<INSearchForMessagesIntentResponse> completion)
{
    // Implement the application logic to find a message that matches the information in the intent.
    ...

    var userActivity = new NSUserActivity (nameof (INSearchForMessagesIntent));
    var response = new INSearchForMessagesIntentResponse (INSearchForMessagesIntentResponseCode.Success, userActivity);

    // Initialize with found message's attributes
    var sender = new INPerson (new INPersonHandle ("sarah@example.com", INPersonHandleType.EmailAddress), null, "Sarah", null, null, null);
    var recipient = new INPerson (new INPersonHandle ("+1-415-555-5555", INPersonHandleType.PhoneNumber), null, "John", null, null, null);
    var message = new INMessage ("identifier", "I am so excited about SiriKit!", NSDate.Now, sender, new INPerson [] { recipient });
    response.Messages = new INMessage [] { message };
    completion (response);
}

public void HandleSetMessageAttribute (INSetMessageAttributeIntent intent, Action<INSetMessageAttributeIntentResponse> completion)
{
    // Implement the application logic to set the message attribute here.
    ...

    var userActivity = new NSUserActivity (nameof (INSetMessageAttributeIntent));
    var response = new INSetMessageAttributeIntentResponse (INSetMessageAttributeIntentResponseCode.Success, userActivity);
    completion (response);
}
```

Дополнительные сведения см. в разделе наших [ссылку этап обработки](~/ios/platform/sirikit/understanding-sirikit.md).

## <a name="adding-an-intents-ui-extension"></a>Добавление расширение Intents UI

Необязательное расширение Intents UI предоставит возможность перевести пользовательского интерфейса приложения и фирменной символики в интерфейс Siri и сделать пользователей чувствовать, подключенной к приложению. С помощью этого расширения приложение можно достичь торговой марки, а также visual и другие сведения в записи.

[![](implementing-sirikit-images/intentsui01.png "Пример вывода расширение Intents UI")](implementing-sirikit-images/intentsui01.png#lightbox)

Так же, как расширение Intents разработчик будет сделать следующий шаг для расширение Intents UI:

- Добавьте расширение Intents UI проект в решение приложения Xamarin.iOS.
- Настройте расширение Intents UI `Info.plist` файл.
- Измените расширение Intents UI основного класса.

Дополнительные сведения см. в разделе наших [ссылку на расширение Intents UI](~/ios/platform/sirikit/understanding-sirikit.md) и Apple [предоставления ссылок пользовательский интерфейс](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ProvidingaCustomInterface.html#//apple_ref/doc/uid/TP40016875-CH7-SW1).

### <a name="creating-the-extension"></a>Создание расширения

Чтобы добавить расширение Intents UI в решение, сделайте следующее:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

1. Щелкните правой кнопкой мыши **имя решения** в **панели решения** и выберите **добавить** > **добавить новый проект...** .
2. В диалоговом окне выберите **iOS** > **расширения** > **расширения пользовательского интерфейса намерение** и нажмите кнопку **Далее** кнопка: 

    [![](implementing-sirikit-images/intents11.png "Выберите расширение намерений пользовательского интерфейса")](implementing-sirikit-images/intents11.png#lightbox)
3. Далее введите **имя** намерение расширение и нажмите кнопку **Далее** кнопки: 

    [![](implementing-sirikit-images/intents12.png "Введите имя для модуля намерений")](implementing-sirikit-images/intents12.png#lightbox)
4. Наконец, нажмите кнопку **создать** кнопку, чтобы добавить расширение намерения в решение приложения: 

    [![](implementing-sirikit-images/intents13.png "Добавьте расширение намерения в решение приложения")](implementing-sirikit-images/intents13.png#lightbox)
5. В **обозревателе решений**, щелкните правой кнопкой мыши **ссылки** папку только что созданный намерение расширения. Проверьте имя Общие библиотеки проекта общего кода (создания приложения выше) и нажмите кнопку **ОК** кнопки: 

    [![](implementing-sirikit-images/intents14.png "Выберите имя проекта библиотеки common общего кода")](implementing-sirikit-images/intents14.png#lightbox)
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Щелкните правой кнопкой мыши **имя решения** в **обозревателе решений** и выберите **добавить** > **добавить новый проект...**
2. В диалоговом окне выберите **iOS** > **расширения** > **расширения пользовательского интерфейса намерение** и нажмите кнопку **Далее** кнопка.
3. Далее введите **имя** намерение расширение и нажмите кнопку **ОК** кнопки.
5. В **обозревателе решений**, щелкните правой кнопкой мыши **ссылки** папку только что созданный намерение расширения. Проверьте имя Общие библиотеки проекта общего кода (создания приложения выше) и нажмите кнопку **ОК** кнопки.
    
-----

### <a name="configuring-the-infoplist"></a>Настройка файла Info.plist

Настройте расширение Intents UI `Info.plist` файл мог работать с приложением.

Так же, как любой типичный расширение приложения, приложение будет включать существующие ключи `NSExtension` и `NSExtensionAttributes`. Для расширения Intents есть один новый атрибут, который должен быть настроен:

[![](implementing-sirikit-images/intents03.png "Один новый атрибут, который должен быть настроен")](implementing-sirikit-images/intents03.png#lightbox)

**IntentsSupported** является обязательным и состоит из массива имен намерение класса, которые должны поддерживаться из расширения намерение приложения.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

Для настройки расширения пользовательского интерфейса намерение `Info.plist` файл, дважды щелкните его в **обозревателе решений** чтобы открыть его для редактирования. Затем переключиться в режим **источника** просмотреть, а затем разверните `NSExtension` и `NSExtensionAttributes` ключи в редакторе:

[![](implementing-sirikit-images/intents04.png "Ключи NSExtension и NSExtensionAttributes в редакторе")](implementing-sirikit-images/intents04.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Для настройки расширения пользовательского интерфейса намерение `Info.plist` файл, дважды щелкните его в **обозревателе решений** чтобы открыть его для редактирования. Разверните `NSExtension` и `NSExtensionAttributes` ключи в редакторе:

[![](implementing-sirikit-images/intents04w.png "Объект NSExtension и NSExtensionAttributes ключи в редакторе")](implementing-sirikit-images/intents04w.png#lightbox)

-----

Разверните `IntentsSupported` ключа и добавьте имя класса намерения, это расширение будет поддерживать. Для примера приложения MonkeyChat, он поддерживает `INSendMessageIntent`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![](implementing-sirikit-images/intents15.png "Ключ INSendMessageIntent")](implementing-sirikit-images/intents15.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents15w.png "Ключ INSendMessageIntent")](implementing-sirikit-images/intents15w.png#lightbox)

-----

Полный список доступных доменов намерения, см. в разделе Apple [ссылку доменов намерение](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2).

### <a name="configuring-the-main-class"></a>Настройка основного класса

Настройте основной класс, который выступает в качестве главную точку входа для расширения пользовательского интерфейса намерения в Siri. Он должен быть подклассом `UIViewController` которого соответствует `IINUIHostedViewController` интерфейс. Пример:

```csharp
using System;
using Foundation;
using CoreGraphics;
using Intents;
using IntentsUI;
using UIKit;

namespace MonkeyChatIntentsUI
{
    public partial class IntentViewController : UIViewController, IINUIHostedViewControlling
    {
        #region Constructors
        protected IntentViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }

        public override void DidReceiveMemoryWarning ()
        {
            // Releases the view if it doesn't have a superview.
            base.DidReceiveMemoryWarning ();

            // Release any cached data, images, etc that aren't in use.
        }
        #endregion

        #region Public Methods
        [Export ("configureWithInteraction:context:completion:")]
        public void Configure (INInteraction interaction, INUIHostedViewContext context, Action<CGSize> completion)
        {
            // Do configuration here, including preparing views and calculating a desired size for presentation.

            if (completion != null)
                completion (DesiredSize ());
        }

        [Export ("desiredSize:")]
        public CGSize DesiredSize ()
        {
            return ExtensionContext.GetHostedViewMaximumAllowedSize ();
        }
        #endregion
    }
}
```

Siri передаст `INInteraction` экземпляр класса `Configure` метод `UIViewController` экземпляра внутри намерение расширения пользовательского интерфейса.

`INInteraction` Предоставляет три основных элемента информации для расширения:

1. Блокировка намерения обрабатываемого объекта.
2. Объект ответа намерение из `Confirm` и `Handle` методы расширения намерение.
3. Состояние взаимодействия, которое определяет состояние взаимодействия между приложением и Siri.

`UIViewController` Экземпляра является принцип классом для взаимодействия с Siri и так как он наследует от `UIViewController`, он имеет доступ ко всем возможностям UIKit.

При вызове метода Siri `Configure` метод `UIViewController` он передает сведения в контекст представления, о том, что контроллер представления будет размещаться либо в Siri Snippit или карта карты.

Siri также будет передать в обработчик завершения, приложение должно возвращать требуемый размер представления после завершения его настройки приложения.

### <a name="design-the-ui-in-ios-designer"></a>Разработка пользовательского интерфейса, в конструкторе iOS

Макет, интерфейс пользователя расширение Intents UI в конструкторе iOS. Дважды щелкните значок расширения `MainInterface.storyboard` файл **обозревателе решений** чтобы открыть его для редактирования. Перетащите все необходимые элементы пользовательского интерфейса для создания пользовательского интерфейса и сохраните изменения.

> [!IMPORTANT]
> Хотя можно добавлять интерактивные элементы, такие как `UIButtons` или `UITextFields` для расширения пользовательского интерфейса намерение `UIViewController`, они являются строго запрещено как намерение пользовательский Интерфейс в Неинтерактивный режим и пользователь не сможет взаимодействовать с ними.

### <a name="wire-up-the-user-interface"></a>Проводной доступ пользовательского интерфейса

С пользовательским интерфейсом расширение Intents UI, созданные в конструкторе iOS, изменить `UIViewController` подклассов и переопределение `Configure` метод следующим образом:

```csharp
[Export ("configureWithInteraction:context:completion:")]
public void Configure (INInteraction interaction, INUIHostedViewContext context, Action<CGSize> completion)
{
    // Do configuration here, including preparing views and calculating a desired size for presentation.
    ...

    // Return desired size
    if (completion != null)
        completion (DesiredSize ());
}

[Export ("desiredSize:")]
public CGSize DesiredSize ()
{
    return ExtensionContext.GetHostedViewMaximumAllowedSize ();
}
```

### <a name="overriding-the-default-siri-ui"></a>Переопределение Siri пользовательский Интерфейс по умолчанию

Расширение Intents UI будут всегда отображаться вместе с другим содержимым Siri как значок приложения и имя в верхней части пользовательского интерфейса или, на основе пожеланий, кнопок (например, отправки или "Отмена"), которые могут отображаться в нижней.

Существует несколько экземпляров, где приложение может заменить сведения, который отображает Siri для пользователя по умолчанию, например обмен сообщениями или карты, где приложение может заменить взаимодействия по умолчанию одним специально для приложения.

Если расширение Intents UI должен переопределить элементы пользовательского интерфейса Siri, по умолчанию `UIViewController` подкласс будет необходимо реализовать `IINUIHostedViewSiriProviding` интерфейс и согласиться на отображение элемент определенного интерфейса.

Добавьте следующий код, чтобы `UIViewController` подкласс сообщить Siri, что расширения пользовательского интерфейса намерение уже отображается содержимое сообщения:

```csharp
public bool DisplaysMessage {
    get {return true;}
}
```

### <a name="considerations"></a>Особенности

Apple рекомендует придерживаться указанных, что разработчику принимать во внимание следующие аспекты во внимание при разработке и реализации расширения пользовательского интерфейса намерение:

- **Быть понимали, каким из использования памяти** — так как расширения пользовательского интерфейса намерение являются временными и только показано в течение некоторого времени, система налагает более тесную ограничений памяти, чем при использовании полного приложения.
- **Рассмотрите возможность минимальный и максимальный размеры представления** -убедитесь, что расширения пользовательского интерфейса намерение хорошо выглядит на каждый тип устройства iOS, размер и ориентацию. Кроме того требуемый размер, который приложение отправляет обратно в Siri не можно будет предоставлен.
- **Использовать гибкость применения и адаптивный макет шаблоны** — опять же, чтобы убедиться, что пользовательский Интерфейс выглядит отлично выглядит на каждом устройстве.

## <a name="summary"></a>Сводка

В этой статье в рамках SiriKit и показано, как это можно сделать для приложений Xamarin.iOS для предоставления служб, доступных для пользователя с помощью Siri и приложения карты на устройстве iOS.




## <a name="related-links"></a>Связанные ссылки

- [Пример ElizaChat](https://developer.xamarin.com/samples/monotouch/ios10/ElizaChat/)
- [Руководство по программированию в SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Ссылка на платформу намерений](https://developer.apple.com/reference/intents)
- [Ссылка на платформу пользовательского интерфейса intents](https://developer.apple.com/reference/intentsui)
- [Справочник по намерений доменов](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)
