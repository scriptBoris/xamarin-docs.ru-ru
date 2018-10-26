---
title: Дополнительные уведомления пользователя в Xamarin.iOS
description: В этой статье более внимательно на платформу уведомления для пользователей в iOS 10. В нем описывается уведомления для пользователей, уведомления о пользовательском интерфейсе, вложения мультимедиа, настраиваемые пользовательские интерфейсы и многое другое.
ms.prod: xamarin
ms.assetid: 4E0C60AE-6F54-4098-8FA0-AADF9AC86805
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/03/2018
ms.openlocfilehash: b0571f826101576b402368923c2147e35aa9299e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116333"
---
# <a name="advanced-user-notifications-in-xamarinios"></a>Дополнительные уведомления пользователя в Xamarin.iOS

Знакомы с iOS 10, уведомление пользователя, платформа позволяет доставки и обработки локальных и удаленных уведомлений. С помощью этой платформы, приложения или расширения приложения можно запланировать доставку локальных уведомлений, указав набор условий, таких как расположение или время суток.

## <a name="about-user-notifications"></a>О уведомления для пользователей

Новая платформа уведомление пользователя позволяет доставки и обработке локальных и удаленных уведомлений. С помощью этой платформы, приложения или расширения приложения можно запланировать доставку локальных уведомлений, указав набор условий, таких как расположение или время суток.

Кроме того, приложения или расширения может получать (и потенциально изменять) локальных и удаленных уведомлений, так как они доставляются на устройстве iOS пользователя.

Новая платформа пользовательского интерфейса для уведомления пользователя позволяет приложения или расширения приложения, чтобы настроить внешний вид локальных и удаленных уведомлений, когда они отображаются для пользователя.

Эта платформа предоставляет следующие методы, приложения могут доставлять уведомления пользователю:

- **Предупреждения Visual** — где скатывается уведомления в верхней части экрана в виде заголовка.
- **Звук и вибрации** -может быть связан с уведомлением.
- **Приложение значок эмблемы** — там, где значка приложения отображает значок, показывающий, что доступно новое содержимое. Например, количество непрочитанных сообщений.

Кроме того в зависимости от текущего контекста пользователя, разными способами, появляется уведомление:

- Если устройство будет разблокирован, уведомление будет развертывание из верхней части экрана как баннер.
- Если устройство заблокировано, уведомление будет отображаться на экране блокировки пользователя.
- Если пользователь пропустили уведомление, их можно открыть центр уведомлений и просмотреть любое из доступных, ожидающих уведомлений существует.

Приложения Xamarin.iOS имеют два вида, это возможность отправлять уведомления для пользователей:

- **Локальные уведомления** -отправляется по приложениям, установленным локально на устройстве пользователя.
- **Удаленные уведомления** — отправляются на удаленный сервер, либо отображаются для пользователя или запускает фоновое обновление содержимого приложения.

Дополнительные сведения см. в разделе наших [усиленной уведомления для пользователей](~/ios/platform/user-notifications/enhanced-user-notifications.md) документации.

## <a name="the-new-user-notification-interface"></a>Новый пользовательский интерфейс уведомлений

Уведомления для пользователей в iOS 10 предоставляется новый дизайн пользовательского интерфейса, который предоставляет материалы, такие как заголовок, подзаголовок и необязательно вложения мультимедиа, которое может быть представлено на экране блокировки, в виде заголовка в верхней части устройства или в центре уведомлений.

Независимо от того, где пользовательское уведомление отображается в iOS 10 оно выводится с тем же внешний вид и те же функции и функциональные возможности.

В iOS 8 Apple представила практические уведомлений, где разработчик может подключить настраиваемые действия на уведомление и позволяет пользователю принимать меры для уведомления без необходимости запуска приложения. Apple iOS 9, ряд улучшений практические уведомлений с помощью быстрый ответ, который позволяет пользователю ответ на уведомление с текстом.

Так как уведомления пользователя являются более неотъемлемой частью взаимодействие с пользователем в iOS 10, Apple дальнейшей расширила практические уведомления для поддерживающих 3D Touch, где пользователь нажимает на уведомление и настраиваемый пользовательский интерфейс отображается предоставляют широкие возможности взаимодействия с уведомлением.

При отображении пользовательского интерфейса уведомлений пользователя, если пользователь взаимодействует с каких-либо действий, подключенный к уведомление, чтобы предоставить отзыв о том, что изменилось пользовательского интерфейса могут обновляться мгновенно.

Новое в iOS 10, пользовательского интерфейса API уведомления пользователя позволяет приложения Xamarin.iOS, чтобы легко воспользоваться преимуществами этих новых функций пользовательского интерфейса для уведомления пользователя.

## <a name="adding-media-attachments"></a>Добавление вложения мультимедиа

Один из наиболее распространенных элементов, которые совместно между пользователями — фотографий, поэтому iOS 10 появилась возможность присоединить элемент мультимедиа (например, фотографии) непосредственно на уведомление, где будет показана и легко доступные для пользователя вместе с остальными контекс уведомления NT.

Тем не менее из-за размеров, задействованных при отправлении даже мелкое изображение, подключив его к удаленной полезные данные уведомления становится непрактичным. Чтобы решить эту проблему, разработчик может использовать новые расширения служб в iOS 10 загрузить изображение из другого источника (например, хранилище данных CloudKit) и присоединить ее к содержимое уведомления до его отображения пользователю.

Для удаленного уведомления изменять расширение службы полезные данные должны быть помечены как изменяемые. Пример:

```csharp
{
    aps : {
        alert : "New Photo Available",
        mutable-content: 1
    },
    my-attachment : "https://example.com/photo.jpg"
}
```

Рассмотрим следующие Обзор процесса:

[![](advanced-user-notifications-images/extension02.png "Добавление вложения мультимедиа процесса")](advanced-user-notifications-images/extension02.png#lightbox)

После удаленных уведомлений доставляется на устройство (с помощью APNs), требуется образ посредством любого требуемого затем можно загрузить расширение службы (такие как `NSURLSession`) и получив образ, его можно изменить содержимое уведомлений и отображения его пользователю.

Ниже приведен пример как этот процесс может обрабатываться в коде:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;

namespace MonkeyNotification
{
    public class NotificationService : UNNotificationServiceExtension
    {
        #region Constructors
        public NotificationService ()
        {
        }
        #endregion

        #region Override Methods
        public override void DidReceiveNotificationRequest (UNNotificationRequest request, Action<UNNotificationContent> contentHandler)
        {
            // Get file URL
            var attachementPath = request.Content.UserInfo.ObjectForKey (new NSString ("my-attachment"));
            var url = new NSUrl (attachementPath.ToString ());

            // Download the file
            var localURL = new NSUrl ("PathToLocalCopy");

            // Create attachment
            var attachmentID = "image";
            var options = new UNNotificationAttachmentOptions ();
            NSError err;
            var attachment = UNNotificationAttachment.FromIdentifier (attachmentID, localURL, options , out err);

            // Modify contents
            var content = request.Content.MutableCopy() as UNMutableNotificationContent;
            content.Attachments = new UNNotificationAttachment [] { attachment };

            // Display notification
            contentHandler (content);
        }

        public override void TimeWillExpire ()
        {
            // Handle service timing out
        }
        #endregion
    }
}
```

При получении уведомления от APNs, настраиваемый адрес образа считывается из содержимого и файл загружается с сервера. Затем `UNNotificationAttachement` создается уникальный идентификатор и локальное расположение образа (как `NSUrl`). Создается изменяемую копию содержимого уведомлений Notification Content и добавляются вложения мультимедиа. Наконец, уведомление отображается для пользователя путем вызова `contentHandler`.

После добавления вложения на уведомление, система берет на себя перемещения и управления файла.

В дополнение к удаленные уведомления, представленные выше, вложения мультимедиа также поддерживаются из локальных уведомлений, где `UNNotificationAttachement` создается и прикрепляется к уведомлению вместе с его содержимым.

Уведомление в iOS 10 поддерживает мультимедийные вложения изображений (статические и GIF), аудио или видео- и система автоматически отобразит правильный пользовательского интерфейса для каждого из этих типов вложений при появлении уведомления для пользователя.

> [!NOTE]
> Следует соблюдать осторожность, чтобы оптимизировать размер носителя и время, необходимое системе для скачивания мультимедиа из удаленного сервера (или сборка носителя для локальных уведомлений) применяет строгие ограничения, как при выполнении расширения службы приложения. Например рассмотрим отправку масштабированной вниз версии образа или мало места картинки видео представления в уведомление.

## <a name="creating-custom-user-interfaces"></a>Создание пользовательских интерфейсов

Чтобы создать собственный пользовательский интерфейс для его уведомления для пользователей, разработчику необходимо добавить в решение приложения расширение Notification Content (знакомы с iOS 10).

Расширение содержимого уведомлений позволяет разработчику добавлять свои собственные представления пользовательского интерфейса уведомлений и получения любые требуемые объекты. Начиная с iOS 12, уведомление содержимого расширения поддерживает интерактивные элементы управления пользовательского интерфейса, такие как кнопки и ползунков. Дополнительные сведения см. в разделе [интерактивные уведомления в iOS 12](~/ios/platform/introduction-to-ios12/notifications/interactive.md) документации.

Для поддержки взаимодействия с пользователем с уведомлением пользователя, настраиваемые действия должен создаваться, зарегистрированные в системе и подключаться к уведомлению до запланированного с системой. Расширение Notification Content будет вызываться для обработки процессов из этих действий. См. в разделе [работа с действия уведомления](~/ios/platform/user-notifications/enhanced-user-notifications.md) раздел [усиленной уведомления для пользователей](~/ios/platform/user-notifications/enhanced-user-notifications.md) Дополнительные сведения об настраиваемые действия.

Когда уведомление пользователя с помощью пользовательского интерфейса предоставляется пользователю, он будет иметь следующие элементы:

[![](advanced-user-notifications-images/customui01.png "Уведомление пользователя с элементами пользовательского интерфейса")](advanced-user-notifications-images/customui01.png#lightbox)

Если пользователь взаимодействует с Custom Actions (представленные ниже уведомления), пользовательском интерфейсе могут обновляться отзыв пользователя как что произошло при их вызове выполнения данного действия.

### <a name="adding-a-notification-content-extension"></a>Добавление расширение notification content

Для реализации пользовательского интерфейса для уведомления пользователя в приложение Xamarin.iOS, сделайте следующее:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

1. Откройте в решение приложения в Visual Studio для Mac.
2. Щелкните правой кнопкой мыши имя решения на **панели решения** и выберите **добавить** > **Добавление нового проекта**.
3. Выберите **iOS** > **расширения** > **содержимого расширения уведомлений** и нажмите кнопку **Далее** кнопки: 

    [![](advanced-user-notifications-images/notify01.png "Выберите расширения содержимого уведомлений")](advanced-user-notifications-images/notify01.png#lightbox)
4. Введите **имя** расширение и нажмите кнопку **Далее** кнопки: 

    [![](advanced-user-notifications-images/notify02.png "Введите имя для расширения")](advanced-user-notifications-images/notify02.png#lightbox)
5. Настройка **имя_проекта** и/или **имя решения** Если требуется и нажмите кнопку **создать** кнопки: 

    [![](advanced-user-notifications-images/notify03.png "Измените имя проекта и/или имя решения")](advanced-user-notifications-images/notify03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Откройте в решение приложения в Visual Studio для Mac.
2. Щелкните правой кнопкой мыши имя решения на **обозревателе решений** и выберите **Добавить > Новый проект...** .
3. Выберите **Visual C# > расширения iOS > расширение Notification Content**:

    [![](advanced-user-notifications-images/notify01.w157-sml.png "Выберите расширения содержимого уведомлений")](advanced-user-notifications-images/notify01.w157.png#lightbox)
4. Введите **имя** расширение и нажмите кнопку **ОК** кнопки.

-----

Когда в решение добавляется расширение Notification Content, будут созданы три файла в проекте расширения:

1. `NotificationViewController.cs` — Это расширение Notification Content, какой контроллер главного представления.
2. `MainInterface.storyboard` — Указывает Hive расположение разработчик размещает видимого пользовательского интерфейса для расширение Notification Content в конструкторе iOS.
3. `Info.plist` — Управляет конфигурации расширение Notification Content.

Значение по умолчанию `NotificationViewController.cs` файл выглядит следующим образом:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;


namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
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
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

        }
        #endregion
    }
}
```

`DidReceiveNotification` Метод вызывается в том случае, когда уведомление развернута для пользователя, так, что расширение Notification Content можно заполнить пользовательского интерфейса с содержимым `UNNotification`. Для приведенного выше примера метку был добавлен к представлению, открыт для кода с именем `label` и используется для отображения текста уведомления.

### <a name="setting-the-notification-content-extensions-categories"></a>Параметр категории расширение Notification Content

Система должна быть в курсе новостей о способах поиска на конкретные категории, которые она отвечает на основе расширение содержимого уведомлений приложения. Выполните следующие действия:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

1. Дважды щелкните значок расширения `Info.plist` файл **панели решения** чтобы открыть его для редактирования.
2. Переключиться в режим **источника** представления.
3. Разверните `NSExtension` ключ.
4. Добавить `UNNotificationExtensionCategory` ключа как тип **строка** со значением расширение принадлежит к категории (в этом примере "приглашение событий): 

    [![](advanced-user-notifications-images/customui02.png "Добавьте ключ UNNotificationExtensionCategory")](advanced-user-notifications-images/customui02.png#lightbox)
5. Сохраните изменения.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Дважды щелкните значок расширения `Info.plist` файл **обозревателе решений** чтобы открыть его для редактирования.
3. Разверните `NSExtension` ключ.
4. Добавить `UNNotificationExtensionCategory` ключа как тип **строка** со значением расширение принадлежит к категории (в этом примере "приглашение событий): 

    [![](advanced-user-notifications-images/customui02w.png "Добавьте ключ UNNotificationExtensionCategory")](advanced-user-notifications-images/customui02w.png#lightbox)
5. Сохраните изменения.

-----

Расширение категории уведомлений (`UNNotificationExtensionCategory`) Используйте те же значения категории, которые используются для регистрации действий по уведомлениям. В ситуации, где приложение будет использовать тот же пользовательский Интерфейс для нескольких категорий, переключение `UNNotificationExtensionCategory` к типу **массива** и укажите все необходимые категории. Пример:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![](advanced-user-notifications-images/customui03.png "Категории расширение содержимого уведомлений")](advanced-user-notifications-images/customui03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](advanced-user-notifications-images/customui03w.png "Категории расширение содержимого уведомлений")](advanced-user-notifications-images/customui03w.png#lightbox)

-----

### <a name="hiding-the-default-notification-content"></a>Скрытие содержимого уведомлений по умолчанию

В ситуации, где пользовательского интерфейса для уведомлений будут отображаться то же содержимое по умолчанию уведомления (заголовок, подзаголовок и текст автоматически отображается в нижней части пользовательского интерфейса уведомлений), можно скрыть эти сведения по умолчанию, добавив `UNNotificationExtensionDefaultContentHidden`ключа `NSExtensionAttributes` ключа как тип **логическое** со значением `YES` в расширении `Info.plist` файла:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![](advanced-user-notifications-images/customui04.png "Поиск сведений по умолчанию")](advanced-user-notifications-images/customui04.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](advanced-user-notifications-images/customui04w.png "Поиск сведений по умолчанию")](advanced-user-notifications-images/customui04w.png#lightbox)

-----

### <a name="designing-the-custom-ui"></a>Проектирование пользовательского интерфейса

Для проектирования пользовательского интерфейса расширение Notification Content, дважды щелкните `MainInterface.storyboard` перетащите файл, чтобы открыть его для редактирования в конструкторе iOS в элементах, необходимых для создания требуемого интерфейса (такие как `UILabels` и `UIImageViews`).

> [!NOTE]
> Начиная с версии iOS 12 расширение содержимого уведомлений может включать интерактивные элементы управления, такие как кнопки и текстовые поля. Дополнительные сведения см. в разделе [интерактивные уведомления в iOS 12](~/ios/platform/introduction-to-ios12/notifications/interactive.md) документации.

После того как компоновать пользовательский Интерфейс и предоставлены необходимые элементы управления C# кода, откройте `NotificationViewController.cs` для редактирования и измените `DidReceiveNotification` метод для заполнения пользовательского интерфейса, когда пользователь разворачивает уведомление. Пример:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;


namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
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
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = content.UserInfo ["location"].ToString ();
            if (location != null) {
                Event.Location.Text = location;
            }

        }
        #endregion
    }
}
```

### <a name="setting-the-content-area-size"></a>Задание размера области содержимого

Изменяет размер области содержимого, отображаемое пользователю, что происходит с параметром в приведенном ниже коде `PreferredContentSize` свойство в `ViewDidLoad` метод до нужного размера. Этот размер также может регулироваться путем применения ограничений к представлению в конструкторе iOS, он остается разработчику выбрать метод, который лучше всего подходит для них.

Так как начинают уведомления системы уже выполняется перед вызове расширение содержимого уведомлений области содержимого full размера и анимировать до запрошенного размера, когда отображаются для пользователя.

Чтобы устранить этот эффект, измените `Info.plist` файла для расширения и задайте `UNNotificationExtensionInitialContentSizeRatio` ключ `NSExtensionAttributes` ключ к типу **номер** с значение, представляющее нужное отношение. Пример:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![](advanced-user-notifications-images/customui05.png "Ключ UNNotificationExtensionInitialContentSizeRatio")](advanced-user-notifications-images/customui05.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](advanced-user-notifications-images/customui05w.png "Ключ UNNotificationExtensionInitialContentSizeRatio")](advanced-user-notifications-images/customui05w.png#lightbox)

-----

### <a name="using-media-attachments-in-custom-ui"></a>С помощью вложения мультимедиа в пользовательский Интерфейс

Так как вложения мультимедиа (как показано на [Добавление вложения мультимедиа](#Adding-Media-Attachments) раздел выше) являются частью полезные данные уведомления, они могут получить доступ к и отображаться в расширение Notification Content так же, как они будут в используемом по умолчанию Уведомление о пользовательском Интерфейсе.

Например, если включены пользовательского интерфейса для выше `UIImageView` , предоставляется в C# код, приведенный код может использоваться для заполнения его из с вложением мультимедиа:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;

namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
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
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = content.UserInfo ["location"].ToString ();
            if (location != null) {
                Event.Location.Text = location;
            }

            // Get Media Attachment
            if (content.Attachements.Length > 1) {
                var attachment = content.Attachments [0];
                if (attachment.Url.StartAccessingSecurityScopedResource ()) {
                    EventImage.Image = UIImage.FromFile (attachment.Url.Path);
                    attachment.Url.StopAccessingSecurityScopedResource ();
                }
            }
        }
        #endregion
    }
}
```

Так как вложение мультимедиа находится под управлением системы, это за пределами приложения "песочницы". Для расширения требуется для информирования системы, что ему доступ к файлу путем вызова `StartAccessingSecurityScopedResource` метод. При расширении выполняется с помощью файла, ей необходимо вызвать метод `StopAccessingSecurityScopedResource` освободить его подключения.

### <a name="adding-custom-actions-to-a-custom-ui"></a>Добавление настраиваемых действий пользовательского интерфейса

Настраиваемое действие кнопки можно использовать для добавления интерактивных функций пользовательского интерфейса для уведомлений. См. в разделе [работа с действия уведомления](~/ios/platform/user-notifications/enhanced-user-notifications.md) раздел [усиленной уведомления для пользователей](~/ios/platform/user-notifications/enhanced-user-notifications.md) документа, Дополнительные сведения о пользовательских действиях.

Помимо пользовательских действий расширение Notification Content можно ответить на следующие встроенные действия также:

- **Действие по умолчанию** -это, когда пользователь касается уведомления, откройте приложение и отображения сведений данного уведомления.
- **Закрыть действие** -это действие отправляется в приложение при закрытии пользователем данного уведомления.

Расширения содержимого уведомлений также иметь возможность обновлять их пользовательского интерфейса, когда пользователь вызывает одно из пользовательских действий, например отображение даты полей, когда пользователь касается **Accept** кнопки настраиваемого действия. Кроме того расширения содержимого уведомлений можно указать системе отложено увольнения пользовательского интерфейса уведомлений, поэтому пользователь может видеть последствия их действие перед закрытием уведомление.

Это делается путем реализации вторая версия `DidReceiveNotification` метод, который включает завершающий обработчик. Пример:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;
using CoreGraphics;

namespace myApp {
    public class NotificationViewController : UIViewController, UNNotificationContentExtension {

        public override void ViewDidLoad() {
            base.ViewDidLoad();

            // Adjust the size of the content area
            var size = View.Bounds.Size
            PreferredContentSize = new CGSize(size.Width, size.Width/2);
        }

        public void DidReceiveNotification(UNNotification notification) {

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = Content.UserInfo["location"] as string;
            if (location != null) {
                Event.Location.Text = location;
            }

            // Get Media Attachment
            if (content.Attachements.Length > 1) {
                var attachment = content.Attachments[0];
                if (attachment.Url.StartAccessingSecurityScopedResource()) {
                    EventImage.Image = UIImage.FromFile(attachment.Url.Path);
                    attachment.Url.StopAccessingSecurityScopedResource();
                }
            }
        }

        [Export ("didReceiveNotificationResponse:completionHandler:")]
        public void DidReceiveNotification (UNNotificationResponse response, Action<UNNotificationContentExtensionResponseOption> completionHandler)
        {

            // Update UI when the user interacts with the
            // Notification
            Server.PostEventResponse += (response) {
                // Take action based on the response
                switch(response.ActionIdentifier){
                case "accept":
                    EventResponse.Text = "Going!";
                    EventResponse.TextColor = UIColor.Green;
                    break;
                case "decline":
                    EventResponse.Text = "Not Going.";
                    EventResponse.TextColor = UIColor.Red;
                    break;
                }

                // Close Notification
                completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
            };
        }
    }
}
```

Путем добавления `Server.PostEventResponse` обработчик `DidReceiveNotification` метод расширение Notification Content, расширение *необходимо* обрабатывать все настраиваемые действия. Расширение может перенаправлять пользовательские действия в содержащем приложение, изменив `UNNotificationContentExtensionResponseOption`. Пример:

```csharp
// Close Notification
completionHandler (UNNotificationContentExtensionResponseOption.DismissAndForwardAction);
```

### <a name="working-with-the-text-input-action-in-custom-ui"></a>Работа с действием ввода текста в настраиваемый пользовательский Интерфейс

В зависимости от структуры приложения и уведомления возможны ситуации, которые пользователь должен вводить текст уведомления (например, при ответе на сообщение). Расширение Notification Content имеет доступ к входное действие встроенный текст, так же, как и стандартное уведомление.

Пример:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;


namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Computed Properties
        // Allow to take input
        public override bool CanBecomeFirstResponder {
            get { return true; }
        }

        // Return the custom created text input view with the
        // required buttons and return here
        public override UIView InputAccessoryView {
            get { return InputView; }
        }
        #endregion

        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
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
        #endregion

        #region Private Methods
        private UNNotificationCategory MakeExtensionCategory ()
        {

            // Create Accept Action
            ...

            // Create decline Action
            ...

            // Create Text Input Action
            var commentID = "comment";
            var commentTitle = "Comment";
            var textInputButtonTitle = "Send";
            var textInputPlaceholder = "Enter comment here...";
            var commentAction = UNTextInputNotificationAction.FromIdentifier (commentID, commentTitle, UNNotificationActionOptions.None, textInputButtonTitle, textInputPlaceholder);

            // Create category
            var categoryID = "event-invite";
            var actions = new UNNotificationAction [] { acceptAction, declineAction, commentAction };
            var intentIDs = new string [] { };
            var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.None);

            // Return new category
            return category;

        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = content.UserInfo ["location"].ToString ();
            if (location != null) {
                Event.Location.Text = location;
            }

            // Get Media Attachment
            if (content.Attachements.Length > 1) {
                var attachment = content.Attachments [0];
                if (attachment.Url.StartAccessingSecurityScopedResource ()) {
                    EventImage.Image = UIImage.FromFile (attachment.Url.Path);
                    attachment.Url.StopAccessingSecurityScopedResource ();
                }
            }
        }

        [Export ("didReceiveNotificationResponse:completionHandler:")]
        public void DidReceiveNotification (UNNotificationResponse response, Action<UNNotificationContentExtensionResponseOption> completionHandler)
        {

            // Is text input?
            if (response is UNTextInputNotificationResponse) {
                var textResponse = response as UNTextInputNotificationResponse;
                Server.Send (textResponse.UserText, () => {
                    // Close Notification
                    completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
                });
            }

            // Update UI when the user interacts with the
            // Notification
            Server.PostEventResponse += (response) {
                // Take action based on the response
                switch (response.ActionIdentifier) {
                case "accept":
                    EventResponse.Text = "Going!";
                    EventResponse.TextColor = UIColor.Green;
                    break;
                case "decline":
                    EventResponse.Text = "Not Going.";
                    EventResponse.TextColor = UIColor.Red;
                    break;
                }

                // Close Notification
                completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
            };
        }
        #endregion
    }
}
```

Этот код создает новое действие ввода текста и добавляет его к категории расширения (в `MakeExtensionCategory`) метод. В `DidReceive` Переопределите метод, он обрабатывает ввод текста со следующим кодом:

```csharp
// Is text input?
if (response is UNTextInputNotificationResponse) {
    var textResponse = response as UNTextInputNotificationResponse;
    Server.Send (textResponse.UserText, () => {
        // Close Notification
        completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
    });
}
```

Если проект предусматривает добавление новых кнопок к полю ввода текста, добавьте следующий код, чтобы включить их:

```csharp
// Allow to take input
public override bool CanBecomeFirstResponder {
    get {return true;}
}

// Return the custom created text input view with the
// required buttons and return here
public override UIView InputAccessoryView {
    get {return InputView;}
}
```

При активации пользователем комментарий действие контроллера представления и поля ввода пользовательского текста необходимо активировать:

```csharp
// Update UI when the user interacts with the
// Notification
Server.PostEventResponse += (response) {
    // Take action based on the response
    switch(response.ActionIdentifier){
    ...
    case "comment":
        BecomeFirstResponder();
        TextField.BecomeFirstResponder();
        break;
    }

    // Close Notification
    completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);

};
```

## <a name="summary"></a>Сводка

В этой статье были предприняты никакие современный взгляд на использование новой платформы уведомление пользователя в приложении Xamarin.iOS. Статья описывает добавление вложения мультимедиа для локальных и удаленных уведомлений и статья описывает использование новый пользовательский Интерфейс уведомления пользователя для создания пользовательских интерфейсах уведомлений.

## <a name="related-links"></a>Связанные ссылки

- [Примеры iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Справочник по платформа Usernotifications](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [Руководство по программированию локальных и удаленных уведомлений](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/Introduction.html)
