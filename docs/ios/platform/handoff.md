---
title: Перемещение вручную в Xamarin.iOS
description: В этой статье рассматривается работа с переходной приложения Xamarin.iOS для передачи действий пользователей между приложениями под управлением пользователя на других устройствах.
ms.prod: xamarin
ms.assetid: 405F966A-4085-4621-AA15-33D663AD15CD
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.openlocfilehash: 4b19d060bd8adf1c2b09bb18b7ff608381a35231
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116671"
---
# <a name="handoff-in-xamarinios"></a>Перемещение вручную в Xamarin.iOS

_В этой статье рассматривается работа с переходной приложения Xamarin.iOS для передачи действий пользователей между приложениями под управлением пользователя на других устройствах._

Apple представила переходной в iOS 8 и OS X Yosemite (10.10) для предоставления общий механизм для пользователя, для передачи действий работы на одном из своих личных устройств, на другое устройство под управлением того же приложения или другого приложения, который поддерживает же действие.

[![](handoff-images/handoff02.png "Пример выполнения операции передачи")](handoff-images/handoff02.png#lightbox)

В этой статье посмотрите на пути к предоставлению действия, совместное использование в приложении Xamarin.iOS и рассматриваются framework переходной подробно:

## <a name="about-handoff"></a>Об Эстафетной

Перемещение вручную (также известный как непрерывности) был предложен компании Apple в iOS 8 и OS X Yosemite (10.10) позволяет пользователю начать действие на одном из своих устройств (iOS или Mac) и по-прежнему и том же действии на другом устройствах (которые определяются iClou пользователя d учетной записи).

Переадресуемое был развернут в iOS 9, чтобы также поддерживают новые, улучшенные возможности поиска. Дополнительные сведения см. в разделе наших [улучшенные возможности поиска](~/ios/platform/search/index.md) документации.

Например пользователь может начать сообщение электронной почты на своих устройствах iPhone и автоматически продолжается по электронной почте на их компьютере Mac, все те же сведения сообщения с заполненными полями и курсор в том же расположении, они остался в iOS.

Какие-либо приложения, использующих одинаковый _идентификатор команды_ , подходящих для с помощью перемещение вручную для продолжения действий пользователя в приложениях, до тех пор, пока эти приложения могут быть доставлено через iTunes App Store или со знаком с зарегистрированным разработчиком (для Mac, Enterprise или Ad Hoc приложения).

Любой `NSDocument` или `UIDocument` приложений автоматически получают переходной поддерживает встроенные и требующих минимальных изменений для поддержки передачи.

### <a name="continuing-user-activities"></a>Продолжение действия пользователей

`NSUserActivity` Класса (а также небольшие изменения, `UIKit` и `AppKit`) обеспечивает поддержку для определения активности пользователя, который потенциально может быть продолжена на другом устройств пользователя.

Действие может передаваться другой устройств пользователя, он должен быть инкапсулирован в экземпляре `NSUserActivity`, помеченная как _текущего действия_, задать его полезные данные (данные, используемые для выполнения продолжения) и действия, затем должен быть передан в это устройство.

Переадресуемое передает минимальные информацию, чтобы определить действие, чтобы продолжить, с более крупные пакеты данных, синхронизируемого с помощью iCloud.

На получающим устройством пользователь получит уведомление, что действие доступно для продолжения. Если пользователь выбирает для продолжения действий на новое устройство, указанное приложение запускается (если еще не запущен) и полезных данных из `NSUserActivity` для перезагрузки действия.

[![](handoff-images/handoffinteractions.png "Продолжение действия пользователя")](handoff-images/handoffinteractions.png#lightbox)

Только приложения, которые совместно используют один и тот же разработчик идентификатор команды и реагировать на них заданного _тип действия_ пригодны для продолжения. Приложение определяет типы действий, которую она поддерживает в разделе `NSUserActivityTypes` ключа из его **Info.plist** файла. Учитывая это, продолжение устройства выбирает приложению выполнения продолжения, в соответствии с Идентификатором команды, тип действия и при необходимости _название действия_.

Принимающее приложение использует сведения из `NSUserActivity`в `UserInfo` словаря для настройки его пользовательского интерфейса и восстановить состояние определенного действия, чтобы переход происходит незаметно для пользователя.

Если продолжение требуется больше информации, чем может быть отправлено эффективно до `NSUserActivity`, возобновление работы приложения может отправить вызов исходного приложения и установить один или несколько потоков для передачи необходимых данных. Например если действие было редактирование большого текстового документа с несколькими образами, потоковая передача потребовался бы для передачи информации, необходимой для продолжения действий на получающим устройством. Дополнительные сведения см. в разделе [вспомогательные потоки продолжения](#Supporting-Continuation-Streams) разделе ниже.

Как уже говорилось, `NSDocument` или `UIDocument` приложений автоматически получают переходной встроенной поддержки. Дополнительные сведения см. в разделе [поддерживают перемещение вручную в приложениях на основе документов](#Supporting-Handoff-in-Document-Based-Apps) разделе ниже.

### <a name="the-nsuseractivity-class"></a>Класс NSUserActivity

`NSUserActivity` Класс является основным объектом обмена Эстафетной и используется для инкапсуляции состояние активности пользователей, которая доступна для продолжения. Приложение будет создавать копию `NSUserActivity` для любого действия, он поддерживает и хочет продолжить на другом устройстве. Например редактор документов создать действие для каждого документа открыт. Тем не менее только первый документ, (отображается в первом окне или вкладке) является _текущего действия_ и поэтому доступна для продолжения.

Экземпляр `NSUserActivity` идентифицируется по его `ActivityType` и `Title` свойства. `UserInfo` Свойство словарь используется для переноса информации о состоянии действия. Задайте `NeedsSave` свойства `true` Если отложенного загрузки сведений о состоянии, с помощью `NSUserActivity`в делегат. Используйте `AddUserInfoEntries` метод для слияния новые данные с других клиентов в `UserInfo` словаря, когда возникает необходимость сохранять состояние действия.

### <a name="the-nsuseractivitydelegate-class"></a>Класс NSUserActivityDelegate

`NSUserActivityDelegate` Позволяет хранить сведения о в `NSUserActivity`в `UserInfo` словарь синхронизирован с текущим состоянием действия и обновление. Когда система нужна информация подлежащее обновлению действие (такие как до продолжения на другом устройстве), он вызывает `UserActivityWillSave` метод делегата.

Будет необходимо реализовать `UserActivityWillSave` метод и вносить изменения `NSUserActivity` (такие как `UserInfo`, `Title`т. д.) чтобы убедиться, что он по-прежнему отражает состояние текущего действия. Когда система вызывает `UserActivityWillSave` метода `NeedsSave` флаг будет очищен. При изменении каких-либо свойств данных действия, необходимо задать `NeedsSave` для `true` еще раз.

Вместо использования `UserActivityWillSave` способом, описанным выше, можно по желанию прикрепить `UIKit` или `AppKit` автоматически управлять активности пользователей. Чтобы сделать это, задайте объект респондент `UserActivity` свойство и реализуйте `UpdateUserActivityState` метод. См. в разделе [поддерживают перемещение вручную в ответчики](#Supporting-Handoff-in-Responders) Дополнительные сведения в приведенном ниже разделе.

### <a name="app-framework-support"></a>Поддержка инфраструктуры приложения

Оба `UIKit` (iOS) и `AppKit` (OS X) предоставляют встроенную поддержку для обработки в `NSDocument`, отвечающее устройство (`UIResponder`/`NSResponder`), и `AppDelegate` классы. Хотя каждая ОС реализует переходной немного по-разному, базовый механизм и API-интерфейсы совпадают.

#### <a name="user-activities-in-document-based-apps"></a>Действия пользователей в приложениях на основе документов

OS X приложения для iOS на основе документов и автоматически поддерживать переходной встроенные. Чтобы активировать эту поддержку, необходимо добавить `NSUbiquitousDocumentUserActivityType` ключ и значение для каждого `CFBundleDocumentTypes` запись в приложении **Info.plist** файла.

Если этот ключ присутствует, оба `NSDocument` и `UIDocument` автоматически создавать `NSUserActivity` экземпляров типа, указанного для документов на основе iCloud. Вам нужно будет предоставлять типом действия для каждого типа документа, который поддерживает приложение, и несколько типов документов можно использовать один и тот же тип действия. Оба `NSDocument` и `UIDocument` автоматически заполнять `UserInfo` свойство `NSUserActivity` с их `FileURL` значение свойства.

В OS X `NSUserActivity` управляет `AppKit` и связанный с ответчики, автоматически становятся текущее действие, когда окно документа становится главного окна. В iOS для `NSUserActivity` объектов, которыми управляет `UIKit`, должен либо вызвать метод `BecomeCurrent` метод явным образом или иметь документа `UserActivity` свойство установлено `UIViewController` когда приложение отображается на переднем плане.

`AppKit` автоматически восстановить любой `UserActivity` свойства, созданные таким образом в OS X. Это происходит, если `ContinueUserActivity` возвращает метод `false` или если это Нереализованная. В этом случае документ открыт с помощью `OpenDocument` метод `NSDocumentController` и затем получите `RestoreUserActivityState` вызова метода.

См. в разделе [поддерживают перемещение вручную в приложениях на основе документов](#Supporting-Handoff-in-Document-Based-Apps) Дополнительные сведения в приведенном ниже разделе.

#### <a name="user-activities-and-responders"></a>Действия пользователей и ответчиков

Оба `UIKit` и `AppKit` может автоматически управлять действием пользователя, если его значение как объект респондент `UserActivity` свойство. Если состояние было изменено, нужно будет задать `NeedsSave` свойство отвечающего `UserActivity` для `true`. Система автоматически сохранит `UserActivity` при необходимости после временной респондент для обновления состояния путем вызова его `UpdateUserActivityState` метод.

Если несколько ответчики совместное использование одного `NSUserActivity` экземпляр, они получают `UpdateUserActivityState` обратного вызова, когда система обновляет объект действия пользователя. Респондент должен вызывать `AddUserInfoEntries` метод обновления `NSUserActivity`в `UserInfo` словаря, чтобы отразить текущее состояние действия на этом этапе. `UserInfo` Словарь очищается перед каждым `UpdateUserActivityState` вызова.

Для отмены сам связи с действия, отвечающего устройства можно задать его `UserActivity` свойства `null`. Когда управляемый платформу приложения `NSUserActivity` экземпляр не имеет более связанных ответчики или документов, это автоматически, не прошедшего проверку.

См. в разделе [поддерживают перемещение вручную в ответчики](#Supporting-Handoff-in-Responders) Дополнительные сведения в приведенном ниже разделе.

#### <a name="user-activities-and-the-appdelegate"></a>Действия пользователей и AppDelegate

Приложения `AppDelegate` составляет его основная точка входа, при обработке продолжение перемещение вручную. Когда пользователь отвечает на уведомление о переадресации, соответствующее приложение запускается (если она не запущена) и `WillContinueUserActivityWithType` метод `AppDelegate` будет вызываться. На этом этапе приложение должно информировать пользователей о том, запускается продолжение.

`NSUserActivity` Экземпляр доставляется `AppDelegate`в `ContinueUserActivity` вызывается метод. На этом этапе необходимо настроить пользовательский интерфейс приложения и по-прежнему данное действие.

См. в разделе [реализации переходной](#Implementing-Handoff) Дополнительные сведения в приведенном ниже разделе.

## <a name="enabling-handoff-in-a-xamarin-app"></a>Включение переадресации в приложении Xamarin

Из-за требований безопасности, налагаемых перемещение вручную приложение Xamarin.iOS, которое использует платформу переходной необходимо правильно настроить оба на портале разработчика Apple и в файле проекта Xamarin.iOS.

Выполните следующие действия:

1. Войдите на [портал разработчиков Apple](http://developer.apple.com).
2. Щелкните **сертификаты, идентификаторы и профили**.
3. Если вы еще не сделали, щелкнуть **идентификаторы** и создайте код для приложения (например `com.company.appname`), иначе изменить свой существующий идентификатор.
4. Убедитесь, что **iCloud** службы проверки для данного ИД: 

    [![](handoff-images/provision01.png "Включение службы iCloud для данного ИД")](handoff-images/provision01.png#lightbox)
5. Сохраните изменения.
4. Щелкните **профили подготовки** > **разработки** и создание нового профиля подготовки для вас для разработки приложения: 

    [![](handoff-images/provision02.png "Создание нового профиля подготовки для приложения")](handoff-images/provision02.png#lightbox)
5. Скачайте и установите новый профиль подготовки или использовать Xcode, чтобы загрузить и установить профиль.
6. Измените параметры проекта Xamarin.iOS и убедитесь, что вы используете только что созданный профиль подготовки: 

    [![](handoff-images/provision03.png "Выберите только что создали профиль подготовки")](handoff-images/provision03.png#lightbox)
7. Далее следует изменить ваш **Info.plist** файл и убедитесь, что вы используете идентификатор приложения, который использовался для создания профиля подготовки: 

    [![](handoff-images/provision04.png "Задать идентификатор приложения")](handoff-images/provision04.png#lightbox)
8. Прокрутите страницу до **фоновые режимы** и установите следующие элементы: 

    [![](handoff-images/provision05.png "Включить требуемые фоновые режимы")](handoff-images/provision05.png#lightbox)
9. Сохраните изменения ко всем файлам.

Эти параметры заданы приложение теперь готов для доступа к API Framework перемещение вручную. Подробные сведения о подготовке, см. в разделе наших [подготовки устройств](~/ios/get-started/installation/device-provisioning/index.md) и [подготовки приложений](~/ios/get-started/installation/device-provisioning/index.md) руководства.

## <a name="implementing-handoff"></a>Реализация перемещение вручную

Действия пользователей может быть продолжен между приложениями, которые должны быть подписаны один и тот же разработчик идентификатор команды и поддерживают один и тот же тип действия. Реализация передачи в приложение Xamarin.iOS требует создания объекта действия пользователя (либо в `UIKit` или `AppKit`), обновление состояния объекта для отслеживания действий и продолжая действия получающим устройством.

### <a name="identifying-user-activities"></a>Определение действий пользователей

Является первым этапом реализации перемещение вручную для определения типов действий пользователей, которые поддерживает приложение, и видеть, какие из этих действий являются хорошими кандидатами для продолжения на другом устройстве. Например: приложение со списком задач могут поддерживать редактирования элементов как один _тип действия пользователя_и поддерживают просмотр списка доступных элементов, что и другой.

Приложения можно создать столько пользователя типы действий при необходимости, один для любой функции, которая предоставляет приложение. Для конкретного типа действия пользователя приложения должны отслеживать время действия типа начинается и заканчивается и должен поддерживать актуальную информацию о состоянии для продолжения этой задачи на другом устройстве.

Действия пользователей может быть продолжен в другом приложении, подписанные один и тот же идентификатор группы без любой взаимно-однозначное сопоставление между приложениями и отправки. Например определенное приложение можно создать четыре разные типы действий, которые могут использоваться разные, отдельные приложения на другом устройстве. Это — это обычное дело, между Mac версии приложения (которое может иметь множество функций и функций) и приложениями iOS, где каждое приложение сосредоточиться на определенной задачи, меньшего размера.

### <a name="creating-activity-type-identifiers"></a>Создание действия идентификаторы типа

_Идентификатор типа действия_ добавляется короткую строку `NSUserActivityTypes` массив приложения **Info.plist** файл, используемый для идентификации данного типа действий пользователя. В массиве для каждого действия, которое поддерживает приложения будет существовать одна запись. Apple предлагает нотации обратного DNS-стиля для идентификатора типа действия для предотвращения конфликтов. Например: `com.company-name.appname.activity` для конкретного приложения на основе действий или `com.company-name.activity` для действий, которые могут выполняться для нескольких приложений.

Идентификатор типа действия используется при создании `NSUserActivity` экземпляр для определения типа действия. При действие продолжается на другом устройстве, тип действия (а также идентификатор приложения Team) определяет, какие приложения, чтобы запустить продолжить действие.

Например, мы собираемся создавать пример приложения вызывается **MonkeyBrowser** ([загрузить здесь](https://developer.xamarin.com/samples/monotouch/ios8/MonkeyBrowser/)). Это приложение будет представлять четыре вкладки, на каждой из различных откройте URL-адрес в веб-представление браузера. Пользователь будет иметь возможность продолжать одну из вкладок на это устройство iOS, запустив его.

Чтобы создать необходимые идентификаторы типа действия для поддержки этого поведения, изменение **Info.plist** файл и переключитесь на **источника** представления. Добавление `NSUserActivityTypes` ключа и создайте следующие идентификаторы:

[![](handoff-images/type01.png "Ключ NSUserActivityTypes и необходимые идентификаторы в редакторе plist")](handoff-images/type01.png#lightbox)

Мы создали четыре новых типа идентификаторы действий, одной для каждой из вкладок в примере **MonkeyBrowser** приложения. При создании собственных приложений, замените содержимое файла `NSUserActivityTypes` массива с идентификаторами тип действия, относящиеся к действия приложения поддерживает.

### <a name="tracking-user-activity-changes"></a>Отслеживание изменении пользовательского действия

Когда мы создадим новый экземпляр класса `NSUserActivity` класс, мы укажем `NSUserActivityDelegate` экземпляр для отслеживания изменений в состоянии действия. Например следующий код может использоваться для отслеживания изменения состояния:

```csharp
using System;
using CoreGraphics;
using Foundation;
using UIKit;

namespace MonkeyBrowse
{
    public class UserActivityDelegate : NSUserActivityDelegate
    {
        #region Constructors
        public UserActivityDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void UserActivityReceivedData (NSUserActivity userActivity, NSInputStream inputStream, NSOutputStream outputStream)
        {
            // Log
            Console.WriteLine ("User Activity Received Data: {0}", userActivity.Title);
        }

        public override void UserActivityWasContinued (NSUserActivity userActivity)
        {
            Console.WriteLine ("User Activity Was Continued: {0}", userActivity.Title);
        }

        public override void UserActivityWillSave (NSUserActivity userActivity)
        {
            Console.WriteLine ("User Activity will be Saved: {0}", userActivity.Title);
        }
        #endregion
    }
}
```

`UserActivityReceivedData` Метод вызывается, когда Stream продолжения получает данные от отправляющего устройства. Дополнительные сведения см. в разделе [вспомогательные потоки продолжения](#Supporting-Continuation-Streams) разделе ниже.

`UserActivityWasContinued` Метод вызывается в том случае, когда другое устройство взял на себя действия из текущего устройства. В зависимости от типа действия, такие как добавление нового элемента в список ToDo приложение может требуется прервать выполнение действий на отправляющим устройством.

`UserActivityWillSave` Метод вызывается до любого изменения в действие сохраняются и синхронизируются по локально доступных устройств. Этот метод можно использовать, чтобы внести изменения в последнюю минуту `UserInfo` свойство `NSUserActivity` экземпляра перед отправкой.

### <a name="creating-a-nsuseractivity-instance"></a>Создание экземпляра NSUserActivity

Каждое действие, который приложение хочет предоставить возможность продолжения на другом устройстве должен быть инкапсулирован в `NSUserActivity` экземпляра. Приложение можно создать произвольное количество действий при необходимости и характер этих действий зависит от функций и возможностей в приложения. Например приложение электронной почты может создать одно действие для создания нового сообщения, а другой для чтения сообщения.

Для нашего примера приложения новый `NSUserActivity` создается каждый раз, когда пользователь вводит новый URL-адрес в одном из представление с вкладками веб-браузера. Следующий код сохраняет состояние заданной вкладки:

```csharp
public NSString UserActivityTab1 = new NSString ("com.xamarin.monkeybrowser.tab1");
public NSUserActivity UserActivity { get; set; }
...

UserActivity = new NSUserActivity (UserActivityTab1);
UserActivity.Title = "Weather Tab";
UserActivity.Delegate = new UserActivityDelegate ();

// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);

// Inform Activity that it has been updated
UserActivity.BecomeCurrent ();
```

Он создает новый `NSUserActivity` с помощью одного пользовательского типа действия создали выше и предоставляет понятное название для действия. Он подключается к экземпляру `NSUserActivityDelegate` созданный выше, чтобы просмотреть состояние изменяется и информирует iOS о том, что это действие пользователя является текущее действие.

### <a name="populating-the-userinfo-dictionary"></a>Заполнение словарь сведений о пользователе

Как мы видели ранее, `UserInfo` свойство `NSUserActivity` класс является `NSDictionary` пар "ключ значение", используемый для определения состояния данного действия. Значения, хранящиеся в `UserInfo` должен быть одним из следующих типов: `NSArray`, `NSData`, `NSDate`, `NSDictionary`, `NSNull`, `NSNumber`, `NSSet`, `NSString`, или `NSURL`. `NSURL` значения данных, ссылающиеся на документы iCloud будет автоматически настроен таким образом, чтобы они указывают один и тот же документ на получающим устройством.

В приведенном выше примере мы создали `NSMutableDictionary` объектом и заполняется один ключ, указав URL-адрес, пользователь просматривает в настоящее время на заданной вкладки. `AddUserInfoEntries` Метод действия пользователя был использован для обновления указанного действия с данными, который будет использоваться для восстановления уровня активности в получающим устройством:

```csharp
// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);
```

Apple предлагает сохранение сведения, передаваемые в самый-самый минимум, чтобы убедиться, что действия отправляется за отведенное время, чтобы получающим устройством. Если больше информации является обязательным, как изменить изображение, прикрепляемые к документу требуется отправить, следует использовать потоки для продолжения. См. в разделе [вспомогательные потоки продолжения](#Supporting-Continuation-Streams) Дополнительные сведения в приведенном ниже разделе.

### <a name="continuing-an-activity"></a>Продолжение действия

Переадресуемое автоматически информировать пользователей локального устройств iOS и OS X в физической близости к исходного устройства, которые вошли в ту же учетную запись iCloud, доступности, продолжение работы действий пользователя. Если пользователь выбирает для продолжения действия на новое устройство, система запустит соответствующее приложение (на основе идентификатора группы и типа действия) и сведения о его `AppDelegate` что продолжение должно произойти.

Во-первых, `WillContinueUserActivityWithType` метод вызывается, чтобы приложения могли сообщать пользователю, продолжение является началом. Мы используем следующий код в **AppDelegate.cs** файл нашего примера приложения для обработки начальной продолжения:

```csharp
public NSString UserActivityTab1 = new NSString ("com.xamarin.monkeybrowser.tab1");
public NSString UserActivityTab2 = new NSString ("com.xamarin.monkeybrowser.tab2");
public NSString UserActivityTab3 = new NSString ("com.xamarin.monkeybrowser.tab3");
public NSString UserActivityTab4 = new NSString ("com.xamarin.monkeybrowser.tab4");
...

public FirstViewController Tab1 { get; set; }
public SecondViewController Tab2 { get; set;}
public ThirdViewController Tab3 { get; set; }
public FourthViewController Tab4 { get; set; }
...

public override bool WillContinueUserActivity (UIApplication application, string userActivityType)
{
    // Report Activity
    Console.WriteLine ("Will Continue Activity: {0}", userActivityType);

    // Take action based on the user activity type
    switch (userActivityType) {
    case "com.xamarin.monkeybrowser.tab1":
        // Inform view that it's going to be modified
        Tab1.PreparingToHandoff ();
        break;
    case "com.xamarin.monkeybrowser.tab2":
        // Inform view that it's going to be modified
        Tab2.PreparingToHandoff ();
        break;
    case "com.xamarin.monkeybrowser.tab3":
        // Inform view that it's going to be modified
        Tab3.PreparingToHandoff ();
        break;
    case "com.xamarin.monkeybrowser.tab4":
        // Inform view that it's going to be modified
        Tab4.PreparingToHandoff ();
        break;
    }

    // Inform system we handled this
    return true;
}
```

В приведенном выше примере регистрируется каждый контроллер представления `AppDelegate` и имеющий открытый `PreparingToHandoff` метод, который отображает индикатор активности и сообщения, уведомляющего пользователя о, что действие будет передан на текущем устройстве. Пример

```csharp
private void ShowBusy(string reason) {

    // Display reason
    BusyText.Text = reason;

    //Define Animation
    UIView.BeginAnimations("Show");
    UIView.SetAnimationDuration(1.0f);

    Handoff.Alpha = 0.5f;

    //Execute Animation
    UIView.CommitAnimations();
}
...

public void PreparingToHandoff() {
    // Inform caller
    ShowBusy ("Continuing Activity...");
}
```

`ContinueUserActivity` Из `AppDelegate` будет вызываться действительности продолжить данное действие. Опять же из нашего примера приложения:

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Report Activity
    Console.WriteLine ("Continuing User Activity: {0}", userActivity.ToString());

    // Get input and output streams from the Activity
    userActivity.GetContinuationStreams ((NSInputStream arg1, NSOutputStream arg2, NSError arg3) => {
        // Send required data via the streams
        // ...
    });

    // Take action based on the Activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.monkeybrowser.tab1":
        // Preform handoff
        Tab1.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab1});
        break;
    case "com.xamarin.monkeybrowser.tab2":
        // Preform handoff
        Tab2.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab2});
        break;
    case "com.xamarin.monkeybrowser.tab3":
        // Preform handoff
        Tab3.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab3});
        break;
    case "com.xamarin.monkeybrowser.tab4":
        // Preform handoff
        Tab4.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab4});
        break;
    }

    // Inform system we handled this
    return true;
}
```

Открытый `PerformHandoff` метод каждого контроллера представления фактически выполняет передачу и восстанавливает действия на текущем устройстве. В случае примера он отображает же URL-адрес в заданной вкладки, которая вошла пользователь на другом устройстве. Пример

```csharp
private void HideBusy() {

    //Define Animation
    UIView.BeginAnimations("Hide");
    UIView.SetAnimationDuration(1.0f);

    Handoff.Alpha = 0f;

    //Execute Animation
    UIView.CommitAnimations();
}
...

public void PerformHandoff(NSUserActivity activity) {

    // Hide busy indicator
    HideBusy ();

    // Extract URL from dictionary
    var url = activity.UserInfo ["Url"].ToString ();

    // Display value
    URL.Text = url;

    // Display the give webpage
    WebView.LoadRequest(new NSUrlRequest(NSUrl.FromString(url)));

    // Save activity
    UserActivity = activity;
    UserActivity.BecomeCurrent ();

}
```

`ContinueUserActivity` Включает метод `UIApplicationRestorationHandler` , можно вызвать для документа или ответчик на базе возобновление действия. Вам потребуется передать `NSArray` или доступных для восстановления объектов в обработчик восстановления при вызове. Пример:

```csharp
completionHandler (new NSObject[]{Tab4});
```

Для каждого объекта, переданного его `RestoreUserActivityState` метод будет вызываться. Каждый объект можно затем использовать данные в `UserInfo` словаря для восстановления собственное состояние. Пример:

```csharp
public override void RestoreUserActivityState (NSUserActivity activity)
{
    base.RestoreUserActivityState (activity);

    // Log activity
    Console.WriteLine ("Restoring Activity {0}", activity.Title);
}
```

Для приложений на базе документа, если не реализовать `ContinueUserActivity` метод либо `false`, `UIKit` или `AppKit` автоматически можно возобновить действия. См. в разделе [поддерживают перемещение вручную в приложениях на основе документов](#Supporting-Handoff-in-Document-Based-Apps) Дополнительные сведения в приведенном ниже разделе.

### <a name="failing-handoff-gracefully"></a>Корректное поведение перемещение вручную

Так как перемещение вручную использует передачи сведений между iOS коллекции слабо подключенных устройств и OS X, могут возникать ошибки в процессе передачи. Следует разрабатывать приложения, чтобы правильно обрабатывать эти сбои и информирует пользователя о любых возникающих ситуациях.

В случае сбоя `DidFailToContinueUserActivitiy` метод `AppDelegate` будет вызываться. Пример:

```csharp
public override void DidFailToContinueUserActivitiy (UIApplication application, string userActivityType, NSError error)
{
    // Log information about the failure
    Console.WriteLine ("User Activity {0} failed to continue. Error: {1}", userActivityType, error.LocalizedDescription);
}
```

Следует использовать предоставленный `NSError` для предоставления сведений о сбое.

## <a name="native-app-to-web-browser-handoff"></a>Собственное приложение для переадресации браузера Web

Пользователь может потребоваться продолжить действие без необходимости соответствующие собственное приложение, установлены на устройство. В некоторых случаях веб-интерфейс может предоставлять требуемую функциональность, и действия по-прежнему может быть продолжен. Например учетная запись электронной почты пользователя может предоставлять пользовательский Интерфейс веб базы для составления и чтение сообщений.

Если исходный собственных приложений знает URL-адрес для веб-интерфейс (и синтаксис, необходимый для идентификации данного элемента продолжение), его можно закодировать эту информацию в `WebpageURL` свойство `NSUserActivity` экземпляра. Если получающим устройством нет соответствующего собственное приложение, установить для продолжения обработки, могут вызываться предоставленный веб-интерфейса.

## <a name="web-browser-to-native-app-handoff"></a>Переадресуемое собственного приложения в веб-браузере

Если пользователь использует веб-интерфейс на исходном устройстве и собственное приложение на получающим устройством утверждений доменной части `WebpageURL` свойство, то система будет использовать это приложение маркер продолжения. Новое устройство получит `NSUserActivity` экземпляр, который помечает тип действия, как `BrowsingWeb` и `WebpageURL` будет содержать URL-адрес, который пользователь посетил, `UserInfo` словарь будет пустым.

Для приложения для участия в этом типе перемещение вручную, необходимо claim домена в `com.apple.developer.associated-domains` прав с форматом `<service>:<fully qualified domain name>` (например: `activity continuation:company.com`).

Если совпадает с указанным доменом `WebpageURL` значение свойства, переходной загружает список утвержденных идентификаторы приложений с веб-сайта в этот домен. Веб-сайта необходимо указать список утвержденных идентификаторов в файле JSON со знаком, с именем **apple-app--связь с сайтом** (например, `https://company.com/apple-app-site-association`).

Этот JSON-файл содержит словарь, который определяет список идентификаторы приложений в виде `<team identifier>.<bundle identifier>`. Пример:

```csharp
{
    "activitycontinuation": {
        "apps": [    "YWBN8XTPBJ.com.company.FirstApp",
            "YWBN8XTPBJ.com.company.SecondApp" ]
    }
}
```

Для подписания файла JSON (таким образом, чтобы он содержал правильный `Content-Type` из `application/pkcs7-mime`), используйте **терминалов** приложения и `openssl` с сертификат и ключ, выданный центром сертификации, доверенным для iOS (см. в разделе [ http://support.apple.com/kb/ht5012 ](http://support.apple.com/kb/ht5012) список). Пример:

```csharp
echo '{"activitycontinuation":{"apps":["YWBN8XTPBJ.com.company.FirstApp",
"YWBN8XTPBJ.com.company.SecondApp"]}}' > json.txt

cat json.txt | openssl smime -sign -inkey company.com.key
-signer company.com.pem
-certfile intermediate.pem
-noattr -nodetach
-outform DER > apple-app-site-association
```

`openssl` Команда выводит файл JSON со знаком, размещенный на веб-сайте в **apple-app--связь с сайтом** URL-адрес. Пример:

```csharp
https://example.com/apple-app-site-association.
```

Приложение будет получать любое действие, `WebpageURL` домен находится в его `com.apple.developer.associated-domains` прав. Только `http` и `https` поддерживаются протоколы, другого протокола приведет к появлению исключения.

## <a name="supporting-handoff-in-document-based-apps"></a>Поддержка переадресации в приложения на основе документа

Как уже говорилось, в iOS и OS X, приложения на основе документа автоматически будет поддерживать получения документов на основе iCloud, если приложения **Info.plist** файл содержит `CFBundleDocumentTypes` ключ `NSUbiquitousDocumentUserActivityType`. Пример:

```xml
<key>CFBundleDocumentTypes</key>
<array>
    <dict>
        <key>CFBundleTypeName</key>
        <string>NSRTFDPboardType</string>
        . . .
        <key>LSItemContentTypes</key>
        <array>
        <string>com.myCompany.rtfd</string>
        </array>
        . . .
        <key>NSUbiquitousDocumentUserActivityType</key>
        <string>com.myCompany.myEditor.editing</string>
    </dict>
</array>
```

В этом примере строка имеет обозначение приложения обратного запроса DNS с именем добавляются действия. Если введено таким образом, тип записи действий не обязательно должны повторяться в `NSUserActivityTypes` массив **Info.plist** файла.

Автоматически создается объект действия пользователя (через документа `UserActivity` свойства) можно ссылаться на другие объекты в приложении и использовать для восстановления состояния на продолжение. Например, для отслеживания элементов выбора и документа позицию. Необходимо задать этот действия `NeedsSave` свойства `true` каждый раз, когда изменяется состояние и обновить `UserInfo` словарь в `UpdateUserActivityState` метод.

`UserActivity` Свойство можно использовать из любого потока и соответствует протоколу ключ значение наблюдения (KVO), чтобы его можно использовать для синхронизации документ при его перемещении из iCloud и выхода из системы. `UserActivity` Свойство станут недействительными при закрытии документа.

Дополнительные сведения см. в разделе Apple [поддержка действия пользователя в приложениях на основе документов](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/HandoffFundamentals/HandoffFundamentals.html#//apple_ref/doc/uid/TP40014338-CH3-SW5) документации.

## <a name="supporting-handoff-in-responders"></a>Поддержка переадресации в ответчики

Можно связать ответчики (наследуется от либо `UIResponder` в iOS или `NSResponder` в OS X) для действий, задав их `UserActivity` свойства. Система автоматически сохраняет `UserActivity` свойство в соответствующее время, вызвав отвечающего `UpdateUserActivityState` метод для добавления текущие данные в объект активности пользователей с помощью `AddUserInfoEntriesFromDictionary` метод.

## <a name="supporting-continuation-streams"></a>Поддержка продолжения потоков

Может возникнуть ситуация, где объем сведений, необходимых для продолжения действия не могут передаваться эффективно с начальной передачи полезных данных. В таких ситуациях принимающее приложение может установить один или несколько поток между собой и исходного приложения для передачи данных.

В исходном приложении настроен `SupportsContinuationStreams` свойство `NSUserActivity` экземпляр `true`. Пример:

```csharp
// Create a new user Activity to support this tab
UserActivity = new NSUserActivity (ThisApp.UserActivityTab1){
    Title = "Weather Tab",
    SupportsContinuationStreams = true
};
UserActivity.Delegate = new UserActivityDelegate ();

// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);

// Inform Activity that it has been updated
UserActivity.BecomeCurrent ();
```

Принимающее приложение может затем вызвать `GetContinuationStreams` метод `NSUserActivity` в его `AppDelegate` для установления потока. Пример:

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Report Activity
    Console.WriteLine ("Continuing User Activity: {0}", userActivity.ToString());

    // Get input and output streams from the Activity
    userActivity.GetContinuationStreams ((NSInputStream arg1, NSOutputStream arg2, NSError arg3) => {
        // Send required data via the streams
        // ...
    });

    // Take action based on the Activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.monkeybrowser.tab1":
        // Preform handoff
        Tab1.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab1});
        break;
    case "com.xamarin.monkeybrowser.tab2":
        // Preform handoff
        Tab2.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab2});
        break;
    case "com.xamarin.monkeybrowser.tab3":
        // Preform handoff
        Tab3.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab3});
        break;
    case "com.xamarin.monkeybrowser.tab4":
        // Preform handoff
        Tab4.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab4});
        break;
    }

    // Inform system we handled this
    return true;
}
```

На исходном устройстве делегата действия пользователя получает потоки путем вызова его `DidReceiveInputStream` метод для предоставления данных указано продолжать активности пользователей на устройстве возобновления.

Вы воспользуетесь `NSInputStream` для предоставления доступа только для чтения для потоковой передачи данных и `NSOutputStream` предоставляют доступ только для записи. В режиме запросов и ответов, где принимающее приложение запрашивает больше данных и предоставляет исходного приложения, его следует использовать потоки. Таким образом, чтобы данные, записанные в поток вывода для исходного устройства считываются из входного потока на продолжение устройстве и наоборот.

Даже в ситуациях, где необходимы продолжения Stream должна существовать минимальным назад и вперед взаимодействие между двумя приложениями.

Дополнительные сведения см. в разделе Apple [Using продолжения потоков](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/AdoptingHandoff/AdoptingHandoff.html#//apple_ref/doc/uid/TP40014338-CH2-SW13) документации.

## <a name="handoff-best-practices"></a>Переадресуемое советы и рекомендации

Из-за всех различных компонентов, вовлеченных успешную реализацию простого продолжение действий пользователей с помощью переходной процесс требует тщательного проектирования. Apple рекомендует придерживаться указанных внедрение выполните рекомендации для приложений включено перемещение вручную:

- Проектирование действия пользователя, чтобы требовать наименьшее полезных данных, можно связать состояние действия, чтобы продолжить. Чем больше полезных данных, тем больше времени занимает продолжение для запуска.
- Если необходимо передавать большие объемы данных для продолжения успешной, учитывайте затраты на конфигурации и сети.
- Довольно часто крупное приложение Mac для создания действий пользователя, которые обрабатываются несколько из них, меньшего размера, приложения для конкретных задач на устройствах iOS. Версии ОС и различных приложений должны разрабатываться эффективной совместной работы или сбой корректно.
- При указании типов действий, используется нотация обратного запроса DNS для предотвращения конфликтов. Если действие является специфичным для заданного приложения, имени должна быть включена в определение типа (например `com.myCompany.myEditor.editing`). Если действие можно будет работать для нескольких приложений, удалите имя приложения из определения (например `com.myCompany.editing`).
- Если приложению требуется обновить состояние активности пользователей (`NSUserActivity`) задайте `NeedsSave` свойства `true`. В соответствующее время переходной вызовет делегата `UserActivityWillSave` метод, чтобы вы могли добавить `UserInfo` словарь при необходимости.
- Так как процесс передачи может не инициализировать мгновенно на получающим устройством, следует реализовать `AppDelegate` `WillContinueUserActivity` и уведомить пользователя, запускается продолжение.

## <a name="example-handoff-app"></a>Переадресуемое примера приложения

Как пример использования переходной приложения Xamarin.iOS, мы включили [ **MonkeyBrowser** ](https://developer.xamarin.com/samples/monotouch/ios8/MonkeyBrowser/) пример приложения с помощью этого руководства. У приложения есть четыре вкладки, которые пользователь может использовать для просмотра веб-приложений, каждый с указанием типа данное действие: погоды, избранного, кофе и работы.

На вкладках, когда пользователь вводит новый URL-адрес и касания **Go** кнопку, новый `NSUserActivity` создается для этого вкладку, содержащую URL-адрес, который в настоящее время обзора:

[![](handoff-images/handoff01.png "Переадресуемое примера приложения")](handoff-images/handoff01.png#lightbox)

Если другой устройств пользователя — **MonkeyBrowser** установлено, приложение осуществил вход в iCloud, с помощью той же учетной записью, на той же сети и в непосредственной близости к устройству выше переходной действие будет отображаться на домашней странице экран (в левом нижнем углу):

[![](handoff-images/handoff02.png "Перемещение вручную действия, отображаемого на начальном экране в левом нижнем углу")](handoff-images/handoff02.png#lightbox)

Если пользователь перетаскивает вверх на значке переходной, запускается приложение, и действие пользователя, заданное в `NSUserActivity` будет продолжаться на новое устройство:

[![](handoff-images/handoff03.png "Продолжение действия пользователей на новом устройстве")](handoff-images/handoff03.png#lightbox)

Когда действие пользователя было успешно отправлено для другого Apple устройства, отправляющего устройства `NSUserActivity` получит вызов `UserActivityWasContinued` метод его `NSUserActivityDelegate` для знали, что активности пользователей были успешно переданы в другой устройство.

## <a name="summary"></a>Сводка

Эта статья дала введение в переходной инфраструктурой, использованной для продолжения действий пользователей между несколькими устройствами Apple пользователя. Далее показано, как включить и внедрить перемещение вручную в приложении Xamarin.iOS. Наконец это обсуждалось различные типы переходной продолжений доступны и переходной рекомендации.



## <a name="related-links"></a>Связанные ссылки

- [Примеры iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [Пример MonkeyBrowser](https://developer.xamarin.com/samples/monotouch/ios8/MonkeyBrowser/)
- [iOS 9 для разработчиков](https://developer.apple.com/ios/pre-release/)
- [Новые возможности в iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Руководство по HomeKitDeveloper](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [Рекомендации по HomeKit пользовательского интерфейса](https://developer.apple.com/homekit/ui-guidelines/)
- [Ссылка на платформу HomeKit](https://developer.apple.com/library/ios/home_kit_framework_ref)
