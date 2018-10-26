---
title: Оптимизированные уведомления пользователя в Xamarin.iOS
description: В этой статье описывается framework уведомления для пользователей, впервые появился в iOS 10. В нем описывается локальных уведомлений, удаленных уведомлений, уведомлениями, действий по уведомлениям и многое другое.
ms.prod: xamarin
ms.assetid: 4E1FF652-28F0-4566-B383-9D12664401A4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/02/2017
ms.openlocfilehash: d1b1a59b432315532844f8fca3b613ff3392a7b5
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108253"
---
# <a name="enhanced-user-notifications-in-xamarinios"></a>Оптимизированные уведомления пользователя в Xamarin.iOS

Знакомы с iOS 10, уведомление пользователя, платформа позволяет доставки и обработки локальных и удаленных уведомлений. С помощью этой платформы, приложения или расширения приложения можно запланировать доставку локальных уведомлений, указав набор условий, таких как расположение или время суток.

## <a name="about-user-notifications"></a>О уведомления для пользователей

Как уже говорилось, новую платформу уведомление пользователя позволяет доставки и обработке локальных и удаленных уведомлений. С помощью этой платформы, приложения или расширения приложения можно запланировать доставку локальных уведомлений, указав набор условий, таких как расположение или время суток.

Кроме того, приложения или расширения может получать (и потенциально изменять) локальных и удаленных уведомлений, так как они доставляются на устройстве iOS пользователя.

Новая платформа пользовательского интерфейса для уведомления пользователя позволяет приложения или расширения приложения, чтобы настроить внешний вид локальных и удаленных уведомлений, когда они отображаются для пользователя.

Эта платформа предоставляет следующие методы, приложения могут доставлять уведомления пользователю:

- **Предупреждения Visual** — где скатывается уведомления в верхней части экрана в виде заголовка.
- **Звук и вибрации** -может быть связан с уведомлением.
- **Приложение значок эмблемы** - где значка приложения отображает значок, показывающий, что доступно новое содержимое, например количество непрочитанных сообщений.

Кроме того в зависимости от текущего контекста пользователя, разными способами, появляется уведомление:

- Если устройство будет разблокирован, уведомление будет развертывание из верхней части экрана как баннер.
- Если устройство заблокировано, уведомление будет отображаться на экране блокировки пользователя.
- Если пользователь пропустили уведомление, их можно открыть центр уведомлений и просмотреть любое из доступных, ожидающих уведомлений существует.

Приложения Xamarin.iOS имеют два вида, это возможность отправлять уведомления для пользователей:

- **Локальные уведомления** -отправляется по приложениям, установленным локально на устройстве пользователя.
- **Удаленные уведомления** -отправляются с удаленного сервера и либо отображаться для пользователя или они запускают фоновое обновление содержимого приложения.

### <a name="about-local-notifications"></a>О локальных уведомлений

Локальные уведомления, которые могут отправлять приложения iOS имеют следующие атрибуты и функции:

- Они отправляются в приложениях, которые являются локальными на устройстве пользователя. 
- Они могут быть настроены для использования времени или расположение на основе триггеров. 
- Приложение планирует уведомление с устройством пользователя, которое отображается при соблюдении условия активации.
- Когда пользователь взаимодействует с уведомлением, приложение получит обратный вызов.

Вот несколько примеров локальных уведомлений:

- Оповещения календаря
- Оповещения
- Триггеры учитывать расположение

Дополнительные сведения см. в разделе Apple [локальных и удаленных уведомлений руководство по программированию на](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/) документации.

### <a name="about-remote-notifications"></a>О удаленные уведомления

Удаленные уведомления, которые могут отправлять приложения iOS имеют следующие атрибуты и функции:

- Приложение имеет серверный компонент, с которым он взаимодействует.
- Службы Push-уведомлений Apple (APNs) используется для передачи без гарантии доставки удаленных уведомлений на устройство пользователя с серверов на основе облака для разработчиков.
- Когда приложение получает удаленного уведомления отображается для пользователя.
- Когда пользователь взаимодействует с уведомлением, приложение получит обратный вызов.

Вот несколько примеров удаленные уведомления:

- Оповещения о новых
- Спорт обновлений
- Мгновенные сообщения обмена сообщениями

Существует два вида удаленные уведомления в приложение iOS:

- **Пользователь с доступом к** -будут отображаться для пользователя на устройстве.
- **Автоматическая обновлений** -они предоставляют механизм для обновления содержимого приложения iOS в фоновом режиме. При получении автоматического обновления, приложение может обратиться к извлечем серверы удалить последнее содержимое.

Дополнительные сведения см. в разделе Apple [локальных и удаленных уведомлений руководство по программированию на](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/) документации.

### <a name="about-the-existing-notifications-api"></a>Об уведомлениях существующих API

До iOS 10, будет использовать приложение для iOS `UIApplication` для регистрации в системе уведомления и для планирования, как должна активироваться этого уведомления (либо по времени или расположение).

Существует несколько проблему, разработчик может возникать при работе с существующего API-Интерфейс уведомлений:

- Обнаружены различные обратных вызовов, необходимых для локальных или удаленных уведомлений, что может привести к дублированию кода.
- Приложение было мало управления уведомления после запланированная с системой.
- Обнаружены различные уровни поддержки во всех существующих платформ Apple.

### <a name="about-the-new-user-notification-framework"></a>О новой платформе уведомление пользователя

С iOS 10, Apple представила новую платформу уведомление пользователя, который заменяет существующий `UIApplication` метод указано выше.

Уведомление пользователя framework предоставляет следующие возможности.

- Знакомые API, который включает те же функции с предыдущих методов, облегчая процесс переноса кода из существующей версии .NET framework.
- Включает расширенный набор параметров содержимого, позволяющий расширенные уведомления для отправки пользователю.
- Как локальных, так и удаленные уведомления могут обрабатываться один и тот же код и обратные вызовы.
- Упрощает процесс обработки обратных вызовов, которые отправляются в приложение, когда пользователь взаимодействует с уведомлением.
- Улучшенное управление ожидающие и доставленные уведомления, включая возможность удалить или обновить уведомления.
- Добавляет возможность сделать презентации в приложении уведомлений.
- Добавляет возможность запланировать и обрабатывать уведомления из расширения приложения.
- Добавляет новую точку расширения для уведомлений. 

Новая платформа уведомление пользователя предоставляет унифицированную уведомление о API в нескольких платформ, Apple поддерживает в том числе: 

- **iOS** -полная поддержка для управления и запланировать уведомления.
- **tvOS** -добавляет возможность значки приложений эмблемы для локальных и удаленных уведомлений.
- **watchOS** — добавляет возможность перенаправлять уведомления в устройство iOS парных пользователя и на их Apple Watch и дает возможность выполнять локальные уведомления непосредственно на часах приложения watch.

Дополнительные сведения см. в разделе Apple [ссылка на платформу UserNotifications](https://developer.apple.com/reference/usernotifications) и [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui) документации.

## <a name="preparing-for-notification-delivery"></a>Подготовка для доставки уведомлений

Перед iOS приложение может отправлять уведомления для пользователя, которого необходимо зарегистрировать приложение в системе, и потому, что уведомление будет прервано для пользователя, приложение явно необходимо запрашивать разрешение перед их отправкой.

Существует три разных уровня уведомления запросов, которые пользователь может утвердить для приложения.

- Баннер отображается.
- Звуковые оповещения.
- Эмблемы значка приложения.

Кроме того эти уровни утверждения должно быть запрошено и задать для локальных и удаленных уведомлений.

Разрешение Notification должен запрашиваться сразу же после запуска приложения, добавив следующий код, чтобы `FinishedLaunching` метод `AppDelegate` и задав тип требуемой уведомлений (`UNAuthorizationOptions`):

```csharp
using UserNotifications;
...

public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    // Request notification permissions from the user
    UNUserNotificationCenter.Current.RequestAuthorization (UNAuthorizationOptions.Alert, (approved, err) => {
        // Handle approval
    });

    return true;
}
```

Кроме того, пользователь всегда может изменить привилегии уведомления для приложения в любое время **параметры** приложение на устройстве. Приложение должно проверять наличие прав пользователей запрошенное уведомление перед демонстрацией уведомления, используя следующий код:

```csharp
// Get current notification settings
UNUserNotificationCenter.Current.GetNotificationSettings ((settings) => {
    var alertsAllowed = (settings.AlertSetting == UNNotificationSetting.Enabled);
}); 
``` 

### <a name="configuring-the-remote-notifications-environment"></a>Настройка среды удаленные уведомления

Новое в iOS 10, разработчику необходимо сообщить ОС какое окружение Push-уведомлений качестве разработки или рабочей среде. Для предоставления этих сведений можно начисление в приложении, отклоненных при отправке в iTune App Store с помощью уведомление, похожее на следующее:

> Отсутствует назначение уведомлений Push - приложение включает API для службы Push-уведомлений Apple, но `aps-environment` прав отсутствует подпись приложения.

Чтобы предоставить необходимые права, сделайте следующее:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

1. Дважды щелкните `Entitlements.plist` файл **панели решения** чтобы открыть его для редактирования.
2. Переключиться в режим **источника** представления: 

    [![](enhanced-user-notifications-images/setup01.png "В представлении источника")](enhanced-user-notifications-images/setup01.png#lightbox)
3. Нажмите кнопку **+** , чтобы добавить новый ключ.
4. Введите `aps-environment` для **свойство**, оставьте **тип** как `String` и введите либо `development` или `production` для **значение**: 

    [![](enhanced-user-notifications-images/setup02.png "Свойство aps-environment")](enhanced-user-notifications-images/setup02.png#lightbox)
5. Сохраните изменения в файле.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Дважды щелкните `Entitlements.plist` файл **обозревателе решений** чтобы открыть его для редактирования.
3. Нажмите кнопку **+** , чтобы добавить новый ключ.
4. Введите `aps-environment` для **свойство**, оставьте **тип** как `String` и введите либо `development` или `production` для **значение**: 

    [![](enhanced-user-notifications-images/setup02w.png "Свойство aps-environment")](enhanced-user-notifications-images/setup02.png#lightbox)
5. Сохраните изменения в файле.

-----

### <a name="registering-for-remote-notifications"></a>Регистрация для получения удаленных уведомлений

Если приложение будет отправлять и получать удаленные уведомления, она по-прежнему потребуется выполнить _регистрации токена_ с помощью существующих `UIApplication` API. Эта регистрация требуется устройство должно иметь доступ к соединению динамической сети APNs, которые создаст необходимые токена, которое будет отправляться в приложение. Приложению необходимо пересылать этот маркер для разработчика приложения на стороне сервера для регистрации удаленных уведомлений:

[![](enhanced-user-notifications-images/token01.png "Общие сведения о маркера регистрации")](enhanced-user-notifications-images/token01.png#lightbox)

Используйте следующий код для инициализации необходимых регистрации:

```csharp
UIApplication.SharedApplication.RegisterForRemoteNotifications ();
```

Токен, который отправляется для приложения на стороне сервера разработчика необходимо быть включены как часть полезные данные уведомления, get отправленных сервером службе apns при отправке удаленного уведомления:

[![](enhanced-user-notifications-images/token02.png "Токен, являющемся частью полезные данные уведомления")](enhanced-user-notifications-images/token02.png#lightbox)

Маркер действует как ключ, который объединяет уведомления и приложение, используемое для открытия или ответ на уведомление.

Дополнительные сведения см. в разделе Apple [локальных и удаленных уведомлений руководство по программированию на](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/) документации.

## <a name="notification-delivery"></a>Доставка уведомлений

С помощью приложения полностью зарегистрированная и запрашиваемых необходимые разрешения и предоставлены пользователем, приложение теперь готов для отправки и получения уведомлений. 

### <a name="providing-notification-content"></a>Предоставление содержимого уведомлений

Новое в iOS 10, все уведомления содержать оба **Title** и **подзаголовок** , всегда отображается с **текст** содержимого уведомлений. Также новые, возможность добавления **вложения мультимедиа** содержимого уведомлений.

Чтобы создать содержимое локального уведомления, используйте следующий код:

```csharp
var content = new UNMutableNotificationContent();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
```

Процесс аналогичен для удаленных уведомлений:

```csharp
{
    "aps":{
        "alert":{
            "title":"Notification Title",
            "subtitle":"Notification Subtitle",
            "body":"This is the message body of the notification."
        },
        "badge":1
    }
}
```

### <a name="scheduling-when-a-notification-is-sent"></a>Планирование уведомлений при отправке

С указанным содержимым создания уведомления, приложение должно установить расписание появится уведомление для пользователя, задав *триггера*. iOS 10 предоставляет четыре различных типа триггера:

- **Push-уведомление** — используется исключительно с удаленных уведомлений и активируется, когда отправляет уведомление APNs упаковать приложение, работающее на устройстве.
- **Интервал времени** -позволяет локальное уведомление для планирования на момент времени, начало интервала с сейчас и конечным некоторые будущем. Например `var trigger =  UNTimeIntervalNotificationTrigger.CreateTrigger (5, false);` 
- **Календарь дату** -позволяет локальных уведомлений для планирования в определенную дату и время.
- **На основе расположения** -позволяет локальных уведомлений для планирования, когда устройство iOS является или оставив в определенном географическом расположении или в заданной близость к любой маяки Bluetooth.

При готовности локального уведомления приложению необходимо вызвать `Add` метод `UNUserNotificationCenter` объекта для планирования его вид для пользователя. Для удаленных уведомлений серверное приложение отправляет полезные данные уведомления APNs, который затем отправляет пакет на устройстве пользователя.

Объединяя все фрагменты, пример локального уведомления может выглядеть так:

```csharp
using UserNotifications;
...

var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;

var trigger =  UNTimeIntervalNotificationTrigger.CreateTrigger (5, false);

var requestID = "sampleRequest";
var request = UNNotificationRequest.FromIdentifier (requestID, content, trigger);

UNUserNotificationCenter.Current.AddNotificationRequest (request, (err) => {
    if (err != null) {
        // Do something with error...
    }
});
```

## <a name="handling-foreground-app-notifications"></a>Обработка уведомлений приложения переднего плана

Новое в iOS 10, приложение может обрабатывать уведомления по-разному когда он находится на переднем плане и инициируется уведомление. Предоставляя `UNUserNotificationCenterDelegate` и реализация `UserNotificationCenter` метод, приложение может принять на себя ответственность за отображение уведомления. Пример:

```csharp
using System;
using UserNotifications;

namespace MonkeyNotification
{
    public class UserNotificationCenterDelegate : UNUserNotificationCenterDelegate
    {
        #region Constructors
        public UserNotificationCenterDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void WillPresentNotification (UNUserNotificationCenter center, UNNotification notification, Action<UNNotificationPresentationOptions> completionHandler)
        {
            // Do something with the notification
            Console.WriteLine ("Active Notification: {0}", notification);

            // Tell system to display the notification anyway or use
            // `None` to say we have handled the display locally.
            completionHandler (UNNotificationPresentationOptions.Alert);
        }
        #endregion
    }
}
```

Этот код просто записывает содержимое `UNNotification` в выходных данных приложения и систему, чтобы отображать стандартный оповещение для уведомления. 

Если приложение было отобразить в уведомлении, когда она была на переднем плане и не использовать параметры системы по умолчанию, передайте `None` в обработчик завершения. Пример

```csharp
completionHandler (UNNotificationPresentationOptions.None);
```

В таком коде, а затем откройте `AppDelegate.cs` для редактирования и измените `FinishedLaunching` метод для поиска как в следующем:

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    // Request notification permissions from the user
    UNUserNotificationCenter.Current.RequestAuthorization (UNAuthorizationOptions.Alert, (approved, err) => {
        // Handle approval
    });

    // Watch for notifications while the app is active
    UNUserNotificationCenter.Current.Delegate = new UserNotificationCenterDelegate ();

    return true;
}
```

Этот код присоединяется пользовательский `UNUserNotificationCenterDelegate` выше текущему `UNUserNotificationCenter` чтобы приложение могло обработать уведомление, когда оно активно и находится на переднем плане.

## <a name="notification-management"></a>Управления уведомлений

Новое в iOS 10, уведомлениями предоставляет доступ к ожидающие и доставленные уведомления и добавляет возможность удалить, обновить или преобразовать эти уведомления.

Является важной частью управления уведомлений _идентификатор запроса_ , назначенный для уведомления, когда он был создан и запланировать с помощью системы. Для удаленных уведомлений, этот адрес назначается через новый `apps-collapse-id` в заголовке запроса HTTP.

Идентификатор запроса используется для выбора уведомления приложению необходимо выполнить управление уведомлений на.

### <a name="removing-notifications"></a>Удаление уведомления

Чтобы удалить ожидающие уведомление от системы, используйте следующий код:

```csharp
var requests = new string [] { "sampleRequest" };
UNUserNotificationCenter.Current.RemovePendingNotificationRequests (requests);
```

Чтобы удалить уведомление об уже доставленных, используйте следующий код:

```csharp
var requests = new string [] { "sampleRequest" };
UNUserNotificationCenter.Current.RemoveDeliveredNotifications (requests);
```

### <a name="updating-an-existing-notification"></a>Обновление существующего уведомление

Чтобы обновить существующее уведомление, создать уведомление с требуемыми параметрами изменения (например, новое время триггера) и добавьте его в систему с таким идентификатором запроса как уведомление, которое должно быть изменено. Пример


```csharp
using UserNotifications;
...

// Rebuild notification
var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;

// New trigger time
var trigger = UNTimeIntervalNotificationTrigger.CreateTrigger (10, false);

// ID of Notification to be updated
var requestID = "sampleRequest";
var request = UNNotificationRequest.FromIdentifier (requestID, content, trigger);

// Add to system to modify existing Notification
UNUserNotificationCenter.Current.AddNotificationRequest (request, (err) => {
    if (err != null) {
        // Do something with error...
    }
});
```

Для уже доставленных уведомлений существующие уведомление будет получить обновления и повышена до верхней части списка экранов домашней и блокировки и в центре уведомлений, если оно уже было считано этим пользователем.

## <a name="working-with-notification-actions"></a>Работа с действия уведомления

В iOS 10 уведомления, которые предоставляются для пользователя не являются статичными и предоставляют несколько способов, которые пользователь может взаимодействовать с ними (из встроенная учетная запись для настраиваемых действий).

Существует три типа действий, которые можно реагировать приложение iOS:

- **Действие по умолчанию** -это, когда пользователь касается уведомления, откройте приложение и отображения сведений данного уведомления.
- **Настраиваемые действия** — они были добавлены в iOS 8 и — это быстрый способ для пользователя для выполнения пользовательской задачи непосредственно из уведомления без необходимости запуска приложения. Они могут быть представлены как поля ввода текста которого можно запустить в фоновом режиме (когда приложение получает небольшой объем времени для выполнения запроса) или передний план (где запускается приложение на переднем плане fu или список кнопок с настраиваемые заголовки lfill запрос). Настраиваемые действия доступны в iOS и watchOS.
- **Закрыть действие** -это действие отправляется в приложение при закрытии пользователем данного уведомления.

### <a name="creating-custom-actions"></a>Создание настраиваемых действий

Чтобы создать и зарегистрировать настраиваемое действие в системе, используйте следующий код:

```csharp
// Create action
var actionID = "reply";
var title = "Reply";
var action = UNNotificationAction.FromIdentifier (actionID, title, UNNotificationActionOptions.None);

// Create category
var categoryID = "message";
var actions = new UNNotificationAction [] { action };
var intentIDs = new string [] { };
var categoryOptions = new UNNotificationCategoryOptions [] { };
var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.None);
    
// Register category
var categories = new UNNotificationCategory [] { category };
UNUserNotificationCenter.Current.SetNotificationCategories (new NSSet<UNNotificationCategory>(categories)); 
```

При создании нового `UNNotificationAction`, оно назначается уникальный идентификатор и заголовок, который будет отображаться на кнопке. По умолчанию, будет создано действие как действие фона, однако параметры могут передаваться для настройки поведения действия (например задание значения действия переднего плана).

Все созданные действия должны быть связаны с категорией. При создании нового `UNNotificationCategory`, ей назначается уникальный идентификатор, список действий может выполняться список идентификаторов намерение предоставить дополнительные сведения о намерениях действий в категории и некоторые параметры для управления поведением категории.

Наконец, все категории регистрируются с системой с помощью `SetNotificationCategories` метод.

### <a name="presenting-custom-actions"></a>Представления пользовательских действий

После создания и зарегистрирован в системе набор пользовательских действий и категорий, они могут быть представлены из локальных или удаленных уведомлений.

Для удаленного уведомления, задайте `category` в удаленном полезные данные уведомления, соответствует одной из категорий, созданной ранее. Пример:

```csharp
{
    aps:{
        alert:"Hello world!",
        category:"message"
    }
}
```

Для локальных уведомлений `CategoryIdentifier` свойство `UNMutableNotificationContent` объекта. Пример:

```csharp
var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
content.CategoryIdentifier = "message";
...
```

Опять же этот идентификатор должен соответствовать одному из категории, был создан выше.

### <a name="handling-dismiss-actions"></a>Обработка закрыть действия

Как уже говорилось выше, как Отклонить действие могут отправляться в приложение при закрытии пользователем уведомление. Поскольку эта возможность не стандартного действия, параметр необходимо задать при создании категории. Пример:

```csharp
var categoryID = "message";
var actions = new UNNotificationAction [] { action };
var intentIDs = new string [] { };
var categoryOptions = new UNNotificationCategoryOptions [] { };
var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.CustomDismissAction);

```

### <a name="handling-action-responses"></a>Обработка ответов на действия

Когда пользователь взаимодействует с настраиваемые действия и категории, которые были созданы выше, приложение должно выполнить поставленную задачу. Это делается путем указания `UNUserNotificationCenterDelegate` и реализация `UserNotificationCenter` метод. Пример:

```csharp
using System;
using UserNotifications;

namespace MonkeyNotification
{
    public class UserNotificationCenterDelegate : UNUserNotificationCenterDelegate
    {
        ...

        #region Override Methods
        public override void DidReceiveNotificationResponse (UNUserNotificationCenter center, UNNotificationResponse response, Action completionHandler)
        {
            // Take action based on Action ID
            switch (response.ActionIdentifier) {
            case "reply":
                // Do something
                break;
            default:
                // Take action based on identifier
                if (response.IsDefaultAction) {
                    // Handle default action...
                } else if (response.IsDismissAction) {
                    // Handle dismiss action
                }
                break;
            }

            // Inform caller it has been handled
            completionHandler();
        }
        #endregion
    }
}
```

Переданный в `UNNotificationResponse` класс имеет `ActionIdentifier` свойство, которое может быть действием по умолчанию или отклонить действие. Используйте `response.Notification.Request.Identifier` для тестирования для всех пользовательских действий.

`UserText` Свойство содержит значение любой входной текст для пользователей. `Notification` Свойство содержит исходный уведомление, которое включает в себя запрос с триггером и содержимого уведомлений Notification Content. Приложение можно в том случае, если он был локального или удаленного уведомления на основе типа триггера.

> [!NOTE]
> iOS 12 позволяет настраиваемое уведомление пользовательского интерфейса для изменения его кнопки действий во время выполнения. Дополнительные сведения, взгляните на [кнопки действий динамического уведомлений](~/ios/platform/introduction-to-ios12/notifications/dynamic-actions.md) документации.

## <a name="working-with-service-extensions"></a>Работа с расширениями службы

При работе с удаленных уведомлений _расширения службы_ позволяют включить шифрование end-to-end внутри полезные данные уведомления. Расширения службы — это пользовательский интерфейс расширение (доступно в iOS 10), в фоновом режиме с основным назначением дополнения или замены видимого содержимого уведомления отображаются для пользователя. 

[![](enhanced-user-notifications-images/extension01.png "Общие сведения о расширении службы")](enhanced-user-notifications-images/extension01.png#lightbox)

Расширения службы предназначены для быстрого запуска и предоставляется только короткий промежуток времени для выполнения в системе. В случае недоступности расширения службы завершается ошибкой для выполнения необходимых задач в течение заданного промежутка времени, будет вызываться метод резервной стратегии. В случае сбоя резервный вариант исходного содержимого уведомлений Notification Content отображается для пользователя.

Некоторые возможные способы использования расширений службы:

- Предоставление-сквозное шифрование содержимого удаленного уведомления.
- Добавление вложений для удаленных уведомлений, чтобы дополнять их.

### <a name="implementing-a-service-extension"></a>Реализация модуля службы

Чтобы реализовать расширение службы в приложение Xamarin.iOS, сделайте следующее:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

1. Откройте в решение приложения в Visual Studio для Mac.
2. Щелкните правой кнопкой мыши имя решения на **панели решения** и выберите **добавить** > **Добавление нового проекта**.
3. Выберите **iOS** > **расширения** > **расширения службы уведомлений** и нажмите кнопку **Далее** кнопки: 

    [![](enhanced-user-notifications-images/extension02.png "Выберите расширения служб уведомления")](enhanced-user-notifications-images/extension02.png#lightbox)
4. Введите **имя** расширение и нажмите кнопку **Далее** кнопки: 

    [![](enhanced-user-notifications-images/extension03.png "Введите имя для расширения")](enhanced-user-notifications-images/extension03.png#lightbox)
5. Настройка **имя_проекта** и/или **имя решения** Если требуется и нажмите кнопку **создать** кнопки: 

    [![](enhanced-user-notifications-images/extension04.png "Измените имя проекта и/или имя решения")](enhanced-user-notifications-images/extension04.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Откройте решение приложения в Visual Studio.
2. Щелкните правой кнопкой мыши имя решения на **обозревателе решений** и выберите **Добавить > Новый проект...** .
3. Выберите **Visual C# > расширения iOS > расширение Notification Service**:

    [![](enhanced-user-notifications-images/extension01.w157-sml.png "Выберите расширения служб уведомления")](enhanced-user-notifications-images/extension01.w157.png#lightbox)
4. Введите **имя** расширение и нажмите кнопку **ОК** кнопки.

-----

> [!IMPORTANT]
> Идентификатор пакета для расширения службы следует соответствуют идентификатору пакета основного приложения с помощью `.appnameserviceextension` в конце. Например, если идентификатор пакета из основного приложения `com.xamarin.monkeynotify`, расширение службы должен иметь идентификатор пакета из `com.xamarin.monkeynotify.monkeynotifyserviceextension`. Здесь следует установить автоматически в том случае, когда расширение добавляется в решение. 

Имеется один основной класс в расширение Notification Service, который необходимо изменить, чтобы обеспечить требуемую функциональность. Пример:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;

namespace MonkeyChatServiceExtension
{
    [Register ("NotificationService")]
    public class NotificationService : UNNotificationServiceExtension
    {
        #region Computed Properties
        public Action<UNNotificationContent> ContentHandler { get; set; }
        public UNMutableNotificationContent BestAttemptContent { get; set; }
        #endregion

        #region Constructors
        protected NotificationService (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void DidReceiveNotificationRequest (UNNotificationRequest request, Action<UNNotificationContent> contentHandler)
        {
            ContentHandler = contentHandler;
            BestAttemptContent = (UNMutableNotificationContent)request.Content.MutableCopy ();

            // Modify the notification content here...
            BestAttemptContent.Title = $"{BestAttemptContent.Title}[modified]";

            ContentHandler (BestAttemptContent);
        }

        public override void TimeWillExpire ()
        {
            // Called just before the extension will be terminated by the system.
            // Use this as an opportunity to deliver your "best attempt" at modified content, otherwise the original push payload will be used.

            ContentHandler (BestAttemptContent);
        }
        #endregion
    }
}
```

Первый метод, `DidReceiveNotificationRequest`, передается идентификатор уведомления, а также содержимого уведомления с помощью `request` объекта. Переданный в `contentHandler` нужно будет вызываться, чтобы пользователь получает уведомление.

Второй метод, `TimeWillExpire`, будет вызываться непосредственно перед времени собирается израсходовано расширение службы для обработки запроса. Если не удается вызвать метод расширения службы `contentHandler` в течение заданного промежутка времени, исходное содержимое будет отображаться для пользователя.

### <a name="triggering-a-service-extension"></a>Запуск расширения службы

С расширением службы создания и доставки с помощью приложения ее можно включить, изменив удаленного полезные данные уведомления, отправляемые устройству. Пример:

```csharp
{
    aps : {
        alert : "New Message Available",
        mutable-content: 1
    },
    encrypted-content : "#theencryptedcontent"
}
```

Новый `mutable-content` ключ указывает, что расширение службы будет необходимо запустить для обновления содержимого удаленного уведомления. `encrypted-content` Ключ содержит зашифрованные данные расширения службы может расшифровать перед представлением презентации пользователя.

Рассмотрим следующий пример расширения службы:

```csharp
using UserNotification;

namespace myApp {
    public class NotificationService : UNNotificationServiceExtension {
    
        public override void DidReceiveNotificationRequest(UNNotificationRequest request, contentHandler) {
            // Decrypt payload
            var decryptedBody = Decrypt(Request.Content.UserInfo["encrypted-content"]);
            
            // Modify Notification body
            var newContent = new UNMutableNotificationContent();
            newContent.Body = decryptedBody;
            
            // Present to user
            contentHandler(newContent);
        }
        
        public override void TimeWillExpire() {
            // Handle out-of-time fallback event
            ...
        }
        
    }
}
```

Этот код выполняет расшифровку зашифрованное содержимое из `encrypted-content` ключа, создает новый `UNMutableNotificationContent`, задает `Body` свойство расшифрованного содержимого и использует `contentHandler` для пользователь получает уведомление.

## <a name="summary"></a>Сводка

В этой статье подробно рассматривается любым способом, что пользователям уведомления были улучшены по iOS 10. Оно представлено в новой платформы уведомление пользователя и способы ее использования в приложении Xamarin.iOS или расширения приложения.



## <a name="related-links"></a>Связанные ссылки

- [Примеры iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Справочник по платформа Usernotifications](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [Руководство по программированию локальных и удаленных уведомлений](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
