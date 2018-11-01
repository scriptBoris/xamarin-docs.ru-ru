---
title: Локальные уведомления
description: В этом разделе показано, как реализовать локальных уведомлений в Xamarin.Android. Он объясняет различные элементы пользовательского интерфейса уведомление Android и описываются API связанных с созданием и отображение уведомления.
ms.prod: xamarin
ms.assetid: 03E19D14-7C81-4D5C-88FC-C3A3A927DB46
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/16/2018
ms.openlocfilehash: 63f0d4d7ce5f056d8e1db6bc297258c6bc070086
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675553"
---
<a name="compatibility"></a>

# <a name="local-notifications"></a>Локальные уведомления

_В этом разделе показано, как реализовать локальных уведомлений в Xamarin.Android. Он объясняет различные элементы пользовательского интерфейса уведомление Android и описываются API связанных с созданием и отображение уведомления._

## <a name="local-notifications-overview"></a>Общие сведения о локальных уведомлений

Android предоставляет две области, управляемые системы для отображения значки уведомления и уведомления об уровне обслуживания для пользователя. При первой публикации уведомления его значок отображается в *области уведомлений*, как показано на следующем снимке экрана:

![Пример области уведомлений на устройстве](local-notifications-images/01-notification-shade.png)

Для получения сведений об уведомлении, пользователь может открыть панель уведомлений (которая разворачивается каждый значок уведомления, чтобы показать содержимое уведомления) и выполнять любые действия, связанные с уведомлениями. На следующем снимке экрана показан *панель уведомлений* , соответствующий области уведомлений, отображенный выше:

[![Панель уведомлений пример отображения трех уведомлений](local-notifications-images/02-notification-drawer-sml.png)](local-notifications-images/02-notification-drawer.png#lightbox)

Android уведомления используют два вида макетов:

-   ***Базовая схема*** &ndash; формат compact, основного представления.

-   ***Расширенный макет*** &ndash; формат представления, можно разворачивать для большего размера для получения сведений.

В следующих разделах описан каждый из этих типов макета (и об их создании).

> [!NOTE]
> Это руководство посвящено [API-интерфейсы NotificationCompat](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.html) из [библиотека поддержки Android](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Эти API-интерфейсы будут обеспечения максимально обратной совместимости для Android 4.0 (уровень API 14).


### <a name="base-layout"></a>Базовый макет

Все уведомления Android основаны на формате базовый макет, который, как минимум, включает следующие элементы:

1.  Объект *значок уведомления*, который представляет исходного приложения, или тип уведомления, если приложение поддерживает различные типы уведомлений.

2.  Уведомление *title*, или имя отправителя, если это уведомление личное сообщение.

3.  Сообщение уведомления.

4.  Объект *timestamp*.

Эти элементы отображаются, как показано на следующей схеме:

[![Расположение элементов уведомления о](local-notifications-images/03-notification-callouts-sml.png)](local-notifications-images/03-notification-callouts.png#lightbox)

Базовый макеты ограничены 64 density независимых пикселях (dp) в высоту. По умолчанию Android создает этот стиль базовое уведомление.

При необходимости уведомления можно отобразить крупный значок, представляющий приложение или фотографию отправителя. При использовании крупного значка в уведомлении в Android 5.0 и более поздних версий небольшой значок отображается в виде значка на значке больших:

![Простое уведомление о фото](local-notifications-images/04-simple-notification-photo.png)

Начиная с Android 5.0, уведомления можно также появляются на экране блокировки:

[![Пример уведомления об экрана блокировки](local-notifications-images/05-lockscreen-notification-sml.png)](local-notifications-images/05-lockscreen-notification.png#lightbox)

Пользователь может дважды щелкните уведомление экрана блокировки, разблокировки устройства и перейти к приложению, являющийся источником этого уведомления, или проведите по экрану, чтобы закрыть уведомление. Приложения можно задать уровень видимости уведомления для управления, то, что отображается на экране блокировки, и пользователи могут выбирать, следует ли разрешить конфиденциального содержимого, которые должны отображаться в уведомления на экране блокировки.

Android 5.0 появились формате презентации уведомлений с высоким приоритетом, который называется *информационных индикаторов*. Уведомления о новинках проведите пальцем вниз из верхней части экрана на несколько секунд и затем кто куда резервное копирование на области уведомлений:

[![Пример heads-up уведомлений](local-notifications-images/06-heads-up-notification-sml.png)](local-notifications-images/06-heads-up-notification.png#lightbox)

Уведомления о новинках делают возможным для системы пользовательского интерфейса, чтобы поместить важные сведения глазах у пользователя без нарушения состояния текущего выполняемого действия.

Android поддерживает метаданные уведомлений, чтобы уведомления могут быть отсортированы и интеллектуально. Метаданные уведомлений также контролирует, как уведомления будут представлены на экране блокировки и в формате информационных индикаторов. Приложение может задать следующие виды метаданных уведомлений:

-   **Приоритет** &ndash; уровень приоритета определяет, как и когда уведомления будут представлены. Например, в Android 5.0 важных уведомлений отображаются в виде уведомления информационных индикаторов.

-   **Видимость** &ndash; указывает объем содержимого уведомлений должен отображаться, когда уведомление отображается на экране блокировки.

-   **Категория** &ndash; сообщает системе, как обрабатывать уведомления в различных ситуациях, например когда устройство находится в *не беспокоить* режим.

**Примечание:** **видимость** и **категории** были введены в Android 5.0 и недоступны в более ранних версиях Android. Начиная с Android 8.0 [каналы уведомления](#notif-chan) используются для управления, как уведомления будут представлены для пользователя.


### <a name="expanded-layouts"></a>Развернутое макеты

Начиная с Android версии 4.1, уведомления можно настроить с использованием стилей расширенный макет, позволяющие пользователю развернуть высоту уведомление, чтобы просмотреть дополнительные материалы. Например в следующем примере показано уведомление о развернутой макет в режиме сокращенного:

![Проясняет уведомлений](local-notifications-images/07-contracted-notification.png)

При развертывании этого уведомления, оно показывает все сообщение:

![Расширенные уведомления](local-notifications-images/08-expanded-notification.png)

Android делятся на три категории расширенный макет для уведомлений о событиях один:

-   ***Большой фрагмент текста*** &ndash; в режиме проясняет, отображает выдержкой из первой строки сообщения, следуют две точки. В развернутом режиме отображаются все сообщение (как показано в приведенном выше примере).

-   ***Папки "Входящие"*** &ndash; в режиме проясняет, число новых сообщений. В развернутом режиме отображает первое сообщение электронной почты или список сообщений в папке "Входящие".

-   ***Изображение*** &ndash; в режиме проясняет, отображает только текст сообщения. В развернутом режиме отображает текст и изображения.

[Помимо уведомлений Basic](#beyond-the-basic-notification) (Далее в этой статье) описаны способы создания *большой фрагмент текста*, *папки "Входящие"*, и *изображение* уведомления.

<a name="notif-chan"></a>
<a name="notification-channels"></a>
## <a name="notification-channels"></a>Каналы уведомления

Начиная с Android 8.0 (Oreo), можно использовать *каналы уведомления* функции для создания настраиваемых пользователями канала для каждого типа уведомлений, которое будет отображаться. Каналы уведомления позволяют автоматически группы уведомлений, чтобы все уведомления отправляется приложение канал такое же поведение. Например возможно, предназначенный для получения уведомлений, которые требуют немедленного внимания канал уведомления и отдельный канал «тише», который используется для информационных сообщений.

**YouTube** приложение, которое устанавливается вместе с Android Oreo перечислены две категории уведомлений: **скачать уведомления** и **общие уведомления**:

[![Экраны уведомлений для YouTube в Android Oreo](local-notifications-images/27-youtube-sml.png)](local-notifications-images/27-youtube.png#lightbox)

Каждый из этих категорий соответствует канала уведомления. Реализует приложение YouTube **уведомления** канала и **общие уведомления** канала. Когда пользователь выберет **скачать уведомления**, отображающий на экране параметров для приложения загрузки канала уведомления:

[![Скачайте экрана уведомления для приложения YouTube](local-notifications-images/28-yt-download-sml.png)](local-notifications-images/28-yt-download.png#lightbox)

На этом экране, пользователь может изменить поведение **загрузить** канала уведомлений с помощью следующих действий:

-   Установите уровень важности **срочно**, **высокого уровня**, **Средний**, или **низкой**, который настраивает уровень звуковые и визуальные прерывания.

-   Отключение точки уведомления.

-   Выключать мигающий свет.

-   Показать или скрыть уведомления на экране блокировки.

-   Переопределить **не беспокоить** параметр.

**Общие уведомления** канал имеет те же параметры:

[![Общие уведомления экрана для приложения YouTube](local-notifications-images/29-yt-general-sml.png)](local-notifications-images/29-yt-general.png#lightbox)

Обратите внимание на то, что у вас нет контролирует взаимодействие с пользователем каналы уведомлений &ndash; пользователь может изменять параметры для любого канала уведомления на устройстве, как показано на снимках экрана выше. Тем не менее можно настроить значения по умолчанию (как будет описано ниже). Как показывают эти примеры, новая функция каналы уведомлений позволяет предоставить пользователям возможность точного управления различных типов уведомлений.


## <a name="notification-creation"></a>Создание уведомлений

Для создания уведомления в Android, используется [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder) класса из [Xamarin.Android.Support.v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) пакет NuGet. Этот класс позволяет создавать и публиковать уведомления в старых версиях Android. Дополнительные сведения об использовании `NotificationCompat.Builder`, см. в разделе [совместимости](#compatibility) далее в этом разделе.

`NotificationCompat.Builder` Предоставляет методы для установки различных параметров в уведомления, такие как:

-   Содержимое, включая заголовок, текст сообщения и значок уведомления.

-   Стиль уведомления, такие как *большой фрагмент текста*, *папки "Входящие"*, или *изображение* стиля.

-   Приоритет уведомления: минимум, низкое значение, по умолчанию, высокая или Максимальная. В Android 8.0 и более поздних версий, приоритет задается с помощью [ _канала уведомления_](#notification-channels).

-   Видимость уведомлений на экране блокировки: public, private или секрет.

-   Категории метаданных, которая помогает Android классификации и фильтрации уведомление.

-   Необязательный рассчитывает, что указывает действие для запуска при нажатии уведомления.

-   Идентификатор канала уведомления, что уведомление будет опубликовано на (Android 8.0 и более поздних версий).

После установки этих параметров в построителе, создается объект уведомления, который содержит параметры. Чтобы опубликовать уведомление, передать этот объект уведомления для *диспетчер уведомлений*. Android предоставляет [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/) класс, отвечающий за публикацию уведомления и их отображение для пользователя. Ссылка на класс можно получить из контекста, например действие или службы.


### <a name="creating-a-notification-channel"></a>Создание канала уведомления

Приложения, работающие на Android 8.0 необходимо создать канал уведомлений их уведомления. Канал уведомления требуются следующие три элемента информации:

* Строка идентификатора, который уникален в пакет, которому будет определяться канала.
* Имя канала, который будет отображаться для пользователя.  Имя должно быть от одного до 40 символов.
* Важность канала.

Приложения потребуется проверка версии Android, в которой они выполняются.
Устройства под управлением версии старше, чем Android 8.0 не следует создавать канал уведомления. Следующий метод является примером того, как создать канал уведомлений в действии:

```csharp
void CreateNotificationChannel()
{
    if (Build.VERSION.SdkInt < BuildVersionCodes.O)
    {
        // Notification channels are new in API 26 (and not a part of the
        // support library). There is no need to create a notification
        // channel on older versions of Android.
        return;
    }

    var channelName = Resources.GetString(Resource.String.channel_name);
    var channelDescription = GetString(Resource.String.channel_description);
    var channel = new NotificationChannel(CHANNEL_ID, channelName, NotificationImportance.Default)
                  {
                      Description = channelDescription
                  };

    var notificationManager = (NotificationManager) GetSystemService(NotificationService);
    notificationManager.CreateNotificationChannel(channel);
}
```

Канал уведомления должны создаваться каждый раз, когда действие создается. Для `CreateNotificationChannel` метод, он должен вызываться `OnCreate` метод действия.

### <a name="creating-and-publishing-a-notification"></a>Создание и публикация уведомление

Для создания уведомления в Android, выполните следующие действия.

1.  Создать экземпляр `NotificationCompat.Builder` объекта.

2.  Вызывать различные методы для `NotificationCompat.Builder` объекта, чтобы задать параметры уведомлений.

3.  Вызовите [построения](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.Build/) метод `NotificationCompat.Builder` объекта для создания экземпляра объекта уведомления.

4.  Вызовите [Notify](https://developer.xamarin.com/api/member/Android.App.NotificationManager.Notify/(System.Int32%2cAndroid.App.Notification)) метод опубликовать уведомление в диспетчер уведомлений.

Необходимо указать по крайней мере следующие сведения для каждого из уведомлений:

-   Небольшой значок (24 x 24 точки распространения в размер)

-   Краткое название

-   Текст уведомления

В следующем примере кода показано, как использовать `NotificationCompat.Builder` для создания базовое уведомление. Обратите внимание, что `NotificationCompat.Builder` методы поддерживают [объединения методов в цепочку](http://en.wikipedia.org/wiki/Method_chaining); то есть каждый метод возвращает построитель объекта, поэтому результат последнего вызова метода можно использовать для вызова следующего вызова метода:

```csharp
// Instantiate the builder and set notification elements:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();

// Get the notification manager:
NotificationManager notificationManager =
    GetSystemService (Context.NotificationService) as NotificationManager;

// Publish the notification:
const int notificationId = 0;
notificationManager.Notify (notificationId, notification);
```

В этом примере новый `NotificationCompat.Builder` объект с именем `builder` создается, а также идентификатор канала уведомления для использования. Заголовок и текст уведомления задания, а значок уведомления загружается из **Resources/drawable/ic_notification.png**. Вызов к конструктору уведомлений `Build` метод создает объект уведомления с использованием этих параметров. Следующим шагом является вызов `Notify` метод диспетчера уведомлений. Чтобы найти в диспетчер уведомлений, вызовите `GetSystemService`, как показано выше.

`Notify` Метод принимает два параметра: идентификатор уведомления и уведомления объект. Идентификатор уведомления является уникальным целым числом, определяющий уведомление в приложение. В этом примере идентификатор уведомления имеет значение ноль (0); Однако в рабочем приложении, необходимо предоставить уникальный идентификатор для каждого уведомления. Повторное использование предыдущего значения идентификатора в вызове `Notify` приводит к переопределению последнего уведомления.

Когда этот код выполняется на устройстве Android 5.0, он создает уведомление, которое выглядит как в следующем примере:

![Результат уведомления для примера кода](local-notifications-images/09-hello-world.png)

Значок уведомления отображается на левой уведомления &ndash; этот образ кружке &ldquo;я&rdquo; имеет альфа-канал, чтобы Android можно рисовать серый фон циклическая за ней. Можно также указать значок без альфа-канал. Для отображения фотографии в виде значка, см. в разделе [большого формата значков](#large-icon-format) далее в этом разделе.

Отметка времени задается автоматически, но этот параметр можно переопределить путем вызова [умолчаниюПри](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetWhen/) метод построителя уведомлений. Например в следующем примере кода задает метку времени на текущее время:

```csharp
builder.SetWhen (Java.Lang.JavaSystem.CurrentTimeMillis());
```

### <a name="enabling-sound-and-vibration"></a>Включение звука и вибрации

Следует также звуковое уведомление можно вызвать конструктор уведомлений [SetDefaults](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetDefaults/) метод и передать `NotificationDefaults.Sound` флаг:

```csharp
// Instantiate the notification builder and enable sound:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetDefaults (NotificationDefaults.Sound)
    .SetSmallIcon (Resource.Drawable.ic_notification);
```

Этот вызов `SetDefaults` устройство для воспроизведения звука при публикации уведомления. Если нужно предоставить устройству «вибрация», а не воспроизводить звук, вы можете передать `NotificationDefaults.Vibrate` для `SetDefaults.` Если нужно предоставить устройству для воспроизведения звука и управлять вибрацией устройства, вы можете передать обоих флагов для `SetDefaults`:

```csharp
builder.SetDefaults (NotificationDefaults.Sound | NotificationDefaults.Vibrate);
```

При включении звука без указания звук, воспроизводимый Android использует системный звук уведомлений по умолчанию. Тем не менее, можно изменить звук, который будет воспроизводиться, вызвав конструктор уведомлений [SetSound](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetSound/p/Android.Net.Uri/) метод. Например, воспроизведение сигнала звука уведомления (а не по умолчанию звуковой сигнал), можно получить URI для сигнала звук из [RingtoneManager](https://developer.xamarin.com/api/type/Android.Media.RingtoneManager/) и передать его в `SetSound`:

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Alarm));
```

В качестве альтернативы можно использовать мелодию звонка по умолчанию системы звуковые уведомления:

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Ringtone));
```

После создания объекта уведомления, можно задать свойства для уведомления объекта уведомления (вместо их настройки заранее до `NotificationCompat.Builder` методов). Например, вместо вызова метода `SetDefaults` способ включения вибрации для уведомления о можно непосредственно изменить одноразрядный флаг уведомления [по умолчанию](https://developer.xamarin.com/api/property/Android.App.Notification.Defaults/) свойство:

```csharp
// Build the notification:
Notification notification = builder.Build();

// Turn on vibrate:
notification.Defaults |= NotificationDefaults.Vibrate;
```

В этом примере заставляет устройство вибрацию, когда публикуется уведомление.

<a name="updating-a-notification" />

### <a name="updating-a-notification"></a>Обновление уведомление

Если вы хотите обновить содержимое уведомление после его публикации, можно повторно использовать существующий `NotificationCompat.Builder` для создания объекта уведомления, а также опубликовать это уведомление с идентификатором последнего уведомления. Пример:

```csharp
// Update the existing notification builder content:
builder.SetContentTitle ("Updated Notification");
builder.SetContentText ("Changed to this message.");

// Build a notification object with updated content:
notification = builder.Build();

// Publish the new notification with the existing ID:
notificationManager.Notify (notificationId, notification);
```

В этом примере существующий `NotificationCompat.Builder` объект используется для создания нового объекта уведомления с помощью различных заголовком и сообщением.
Новый объект уведомления опубликована с использованием идентификатор предыдущего уведомления и при этом обновляется содержимое уведомления ранее опубликованные:

![Обновленные уведомлений](local-notifications-images/12-updated-notification.png)

Текст предыдущего уведомления используется повторно &ndash; только заголовок и текст уведомления меняется, когда уведомление отображается в панели уведомлений. Текст заголовка меняется с «Пример уведомления» на «Обновить уведомление» и текст сообщения меняется с «Hello World! Это Мой первый уведомлений!» Для «Изменение на это сообщение.»

Уведомление остается видимой, пока не произойдет одно из трех действий:

-   Пользователь не закрывает уведомления (или касается его *очистить все*).

-   Приложение выполняет вызов `NotificationManager.Cancel`, передавая идентификатор уникальный уведомления, который был задан при публикации уведомления.

-   Приложение вызывает `NotificationManager.CancelAll`.

Дополнительные сведения об обновлении Android уведомлений, см. в разделе [изменить уведомление](http://developer.android.com/training/notify-user/managing.html#Updating).


### <a name="starting-an-activity-from-a-notification"></a>Запуск действия для уведомления

В Android, довольно часто уведомление должно быть связано с *действие* &ndash; действие, которое запускается, когда пользователь касается уведомления. Это действие может находиться в другом приложении или даже в другой задачи. Чтобы добавить действие уведомления, создаваемые [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/) и свяжите `PendingIntent` с уведомлением. Объект `PendingIntent` — это специальный тип рассчитывает, что позволяет приложению-получателю для запуска предварительно определенная часть кода с разрешениями, передающего приложения. Когда пользователь касается уведомления, Android запускается действие, заданное `PendingIntent`.

В следующем фрагменте кода показано, как создать уведомление с `PendingIntent` , запустит действие исходного приложения, `MainActivity`:

```csharp
// Set up an intent so that tapping the notifications returns to this app:
Intent intent = new Intent (this, typeof(MainActivity));

// Create a PendingIntent; we're only using one PendingIntent (ID = 0):
const int pendingIntentId = 0;
PendingIntent pendingIntent =
    PendingIntent.GetActivity (this, pendingIntentId, intent, PendingIntentFlags.OneShot);

// Instantiate the builder and set notification elements, including pending intent:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentIntent (pendingIntent)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first action notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();

// Get the notification manager:
NotificationManager notificationManager =
    GetSystemService (Context.NotificationService) as NotificationManager;

// Publish the notification:
const int notificationId = 0;
notificationManager.Notify (notificationId, notification);
```

Этот код очень похож код уведомления в предыдущем разделе, за исключением случаев, `PendingIntent` добавляется к объекту уведомления. В этом примере `PendingIntent` связан с действием исходного приложения, прежде чем передается в конструктор уведомлений [SetContentIntent](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetContentIntent/) метод. `PendingIntentFlags.OneShot` Флаг передается `PendingIntent.GetActivity` метод, чтобы `PendingIntent` используется только один раз. При выполнении этого кода, отображается следующее уведомление:

![Первый уведомление о действии](local-notifications-images/10-first-action-notification.png)

При касании этого уведомления направляет пользователя обратно к источника действия.

В рабочем приложении, приложение должно обрабатывать *стек переходов назад* при нажатии **обратно** кнопку в пределах действия уведомления (Если вы не знакомы с Android задачами и стек переходов назад, см. в разделе [ Задачи и стек переходов назад](http://developer.android.com/guide/components/tasks-and-back-stack.html)).
В большинстве случаев переход из действия уведомлений в обратном направлении должен возвращать пользователя из приложения и обратно на начальном экране. Для управления стек переходов назад, используемую приложением [TaskStackBuilder](https://developer.xamarin.com/api/type/Android.App.TaskStackBuilder/) создаваемого класса `PendingIntent` с стек переходов назад.

Еще один нюанс реальных является источника действия может потребоваться отправлять данные в действие уведомления. Например о том, что поступил текстовое сообщение, и действие уведомления (сообщения Просмотр экрана), требуется идентификатор сообщения для отображения сообщения пользователю. Действие, которое создает `PendingIntent` можно использовать [Intent.PutExtra](https://developer.xamarin.com/api/member/Android.Content.Intent.PutExtra/p/System.String/System.String/) метод для добавления данных (например, строка) к объекту intent, чтобы эти данные передаются в действие уведомления.

В следующем образце кода показано, как использовать `TaskStackBuilder` для управления стек переходов назад и он содержит пример того, как для отправки одного сообщения строки действие уведомления с именем `SecondActivity`:

```csharp
// Setup an intent for SecondActivity:
Intent secondIntent = new Intent (this, typeof(SecondActivity));

// Pass some information to SecondActivity:
secondIntent.PutExtra ("message", "Greetings from MainActivity!");

// Create a task stack builder to manage the back stack:
TaskStackBuilder stackBuilder = TaskStackBuilder.Create(this);

// Add all parents of SecondActivity to the stack:
stackBuilder.AddParentStack (Java.Lang.Class.FromType (typeof (SecondActivity)));

// Push the intent that starts SecondActivity onto the stack:
stackBuilder.AddNextIntent (secondIntent);

// Obtain the PendingIntent for launching the task constructed by
// stackbuilder. The pending intent can be used only once (one shot):
const int pendingIntentId = 0;
PendingIntent pendingIntent =
    stackBuilder.GetPendingIntent (pendingIntentId, PendingIntentFlags.OneShot);

// Instantiate the builder and set notification elements, including
// the pending intent:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentIntent (pendingIntent)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my second action notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();

// Get the notification manager:
NotificationManager notificationManager =
    GetSystemService (Context.NotificationService) as NotificationManager;

// Publish the notification:
const int notificationId = 0;
notificationManager.Notify (notificationId, notification);
```

В этом примере кода приложение состоит из двух действий: `MainActivity` (который содержит приведенный выше код уведомления), и `SecondActivity`, пользователь видит после при нажатии уведомления экрана. При выполнении этого кода, представлен простое уведомление (как в предыдущем примере). Щелкнув уведомление направляет пользователя к `SecondActivity` экрана:

![Второй снимок экрана действия](local-notifications-images/11-second-activity.png)

Строковое сообщение (переданные намерение `PutExtra` метод) извлекается в `SecondActivity` через эту строку кода:

```csharp
// Get the message from the intent:
string message = Intent.Extras.GetString ("message", "");
```

Этот извлеченного сообщения «Добро пожаловать из MainActivity!,» отображается в `SecondActivity` экрана, как показано в приведенном выше снимке экрана. Когда пользователь нажимает **обратно** кнопку в активном в `SecondActivity`, навигации ведет из приложения и на экран, перед запуском приложения.

Дополнительные сведения о создании ожидающие намерения см. в разделе [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/).


<a name="beyond-the-basic-notification" />

## <a name="beyond-the-basic-notification"></a>Помимо уведомлений basic

Уведомления по умолчанию в формате простого базовый макет в Android, но вы можете улучшить этот базовый формат путем внесения дополнительных `NotificationCompat.Builder` вызовы методов. В этом разделе вы узнаете, как добавить значок большую фотографию в уведомления, и вы увидите примеры того, как создать макет расширенные уведомления.

<a name="large-icon-format" />

### <a name="large-icon-format"></a>Формат крупный значок

Android уведомлений обычно значком исходного приложения (в левой части уведомление). Тем не менее, уведомления можно отобразить изображения или фотографии ( *крупный значок*) вместо стандартной мелкого значка. Например приложении для обмена сообщениями может отобразить фотографию отправителя, а не на значок приложения.

Ниже приведен пример базовое уведомление Android 5.0 &ndash; отображается только значок небольшое приложение:

![Пример обычного уведомления](local-notifications-images/13-sample-notification.png)

Ниже приведен снимок экрана уведомление после его изменения для отображения крупных значков &ndash; используется значок создается из образа операционной системы monkey кода Xamarin:

![Пример уведомления об крупный значок](local-notifications-images/14-large-icon-sample.png)

Обратите внимание, что когда уведомление предоставляется в формате больших значков, значок небольшое приложение виде значка в правом нижнем углу крупных значков.

Чтобы использовать изображение в виде крупных значков в уведомлении, вызовите конструктор уведомлений [SetLargeIcon](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetLargeIcon/) метод и передать растрового изображения. В отличие от `SetSmallIcon`, `SetLargeIcon` принимает только растрового изображения. Для преобразования файл изображения в растровое изображение, используется [BitmapFactory](https://developer.xamarin.com/api/type/Android.Graphics.BitmapFactory/) класса. Пример:

```csharp
builder.SetLargeIcon (BitmapFactory.DecodeResource (Resources, Resource.Drawable.monkey_icon));
```

Этот пример кода открывает файл изображения в **Resources/drawable/monkey_icon.png**, преобразует его в растровое изображение и передает конечного растрового изображения для `NotificationCompat.Builder`. Как правило, разрешение изображения источника больше, чем мелкого значка &ndash; , но не гораздо большего размера. Изображение слишком большого размера может привести к ненужных операций по изменению размера, которые могут вызвать задержку учета уведомления.


### <a name="big-text-style"></a>Стиль большой фрагмент текста

*Большой фрагмент текста* стиля приведен расширенный макет шаблона, используемого для отображения сообщений о длительных в уведомлениях. Как все уведомления расширенный макет уведомление большой фрагмент текста изначально отображается в формате compact представления:

![Пример уведомления об большой фрагмент текста](local-notifications-images/15-big-text-notification.png)

В таком формате отображается только фрагмент сообщения, завершаемый двумя точками. Когда пользователь перетаскивает на уведомление, он расширяет для отображения сообщения уведомлений:

![Расширенный большой фрагмент текста уведомления](local-notifications-images/16-big-text-expanded.png)

Этот формат развернутой макета также включает текст сводки в нижней части уведомление. Максимальная высота *большой фрагмент текста* уведомлений — 256 точки распространения.

Чтобы создать *большой фрагмент текста* уведомления, необходимо создать экземпляр `NotificationCompat.Builder` объекта, как и раньше и затем создать экземпляр и добавить [BigTextStyle](https://developer.xamarin.com/api/type/Android.App.Notification+BigTextStyle/) объект `NotificationCompat.Builder` объекта. Пример:

```csharp
// Instantiate the Big Text style:
Notification.BigTextStyle textStyle = new Notification.BigTextStyle();

// Fill it with text:
string longTextMessage = "I went up one pair of stairs.";
longTextMessage += " / Just like me. ";
//...
textStyle.BigText (longTextMessage);

// Set the summary text:
textStyle.SetSummaryText ("The summary text goes here.");

// Plug this style into the builder:
builder.SetStyle (textStyle);

// Create the notification and publish it ...
```

В этом примере текст сообщения и текст сводки, хранятся в `BigTextStyle` объекта (`textStyle`) перед передачей в `NotificationCompat.Builder.`


### <a name="image-style"></a>Стиль изображения

*Изображение* стиля (также называется *общая картина* стиля) является форматом расширенные уведомления, который можно использовать для отображения изображения в тексте уведомления. Например, можно использовать на снимке экрана приложение или приложение по обмену фотографиями *изображение* уведомлений стиля для предоставления пользователю уведомление о доступности последнего образа, который был записан. Обратите внимание, что максимальная высота *изображение* уведомлений — 256 dp &ndash; Android будет изменяет размер изображения, чтобы поместиться в это ограничение максимальной высоты, в пределах доступной памяти.

Подобно всем расширен уведомления макета *изображение* уведомления сначала отображаются в компактной форме, отображающий фрагмент сопутствующий текст сообщения:

![Уведомление Compact изображений показывает изображение не](local-notifications-images/17-image-compact.png)

Когда пользователь перетаскивает *изображение* уведомлений, он расширяет для отображения изображения. Например вот расширенную версию предыдущего уведомления:

![Изображение выявляет Развернутое изображение уведомлений](local-notifications-images/18-image-expanded.png)

Обратите внимание на то, что когда уведомление отображается в компактном формате, отображает текст уведомления (текст, который передается в конструктор уведомлений `SetContentText` метод, как показано выше). Тем не менее когда уведомление развернута для отображения изображения, отображает текст сводки над изображением.

Для создания *изображение* уведомления, необходимо создать экземпляр `NotificationCompat.Builder` объекта, как и раньше и затем создать и вставить [BigPictureStyle](https://developer.xamarin.com/api/type/Android.App.Notification+BigPictureStyle/) в коллекцию `NotificationCompat.Builder` объекта. Пример:

```csharp
// Instantiate the Image (Big Picture) style:
Notification.BigPictureStyle picStyle = new Notification.BigPictureStyle();

// Convert the image to a bitmap before passing it into the style:
picStyle.BigPicture (BitmapFactory.DecodeResource (Resources, Resource.Drawable.x_bldg));

// Set the summary text that will appear with the image:
picStyle.SetSummaryText ("The summary text goes here.");

// Plug this style into the builder:
builder.SetStyle (picStyle);

// Create the notification and publish it ...
```

Как и `SetLargeIcon` метод `NotificationCompat.Builder`, [BigPicture](https://developer.xamarin.com/api/member/Android.App.Notification+BigPictureStyle.BigPicture/) метод `BigPictureStyle` требует растрового изображения, которое будет отображаться в тексте уведомления. В этом примере [DecodeResource](https://developer.xamarin.com/api/member/Android.Graphics.BitmapFactory.DecodeResource/(Android.Content.Res.Resources%2cSystem.Int32)) метод `BitmapFactory` операций чтения, файл изображения, расположенный **Resources/drawable/x_bldg.png** и преобразует его в растровое изображение.

Также можно отобразить изображения, которые не упакованы как ресурс. Например, в следующем примере кода загружает изображение из локального SD-карту и отображает его в *изображение* уведомлений:

```csharp
// Using the Image (Big Picture) style:
Notification.BigPictureStyle picStyle = new Notification.BigPictureStyle();

// Read an image from the SD card, subsample to half size:
BitmapFactory.Options options = new BitmapFactory.Options();
options.InSampleSize = 2;
string imagePath = "/sdcard/Pictures/my-tshirt.jpg";
picStyle.BigPicture (BitmapFactory.DecodeFile (imagePath, options));

// Set the summary text that will appear with the image:
picStyle.SetSummaryText ("Check out my new T-Shirt!");

// Plug this style into the builder:
builder.SetStyle (picStyle);

// Create notification and publish it ...
```

В этом примере файл изображения расположенный **/sdcard/Pictures/my-tshirt.jpg** загружается, изменяется до половины от его исходного размера и затем преобразуется в растровое изображение для использования в уведомлении:

![Пример футболку изображения в уведомлении](local-notifications-images/19-tshirt-notification.png)

Если вы заранее не знаете размер файла изображения, рекомендуется поместить вызов [BitmapFactory.DecodeFile](https://developer.xamarin.com/api/member/Android.Graphics.BitmapFactory.DecodeFile/p/System.String/Android.Graphics.BitmapFactory+Options/) в обработчик исключений &ndash; `OutOfMemoryError` исключение может быть вызвано, если изображение слишком велико для Android для изменения размера.

Дополнительные сведения о загрузке и декодирование изображений большую битовую карту, см. в разделе [загрузка больших растровых изображений эффективно](https://github.com/xamarin/recipes/tree/master/Recipes/android/resources/general/load_large_bitmaps_efficiently).


### <a name="inbox-style"></a>Стиль папки "Входящие"

*Папки "Входящие"* формат — шаблон расширенный макет, предназначенный для отображения отдельных строк текста (например, в сводных ящик электронной почты) в тексте уведомления. *Папки "Входящие"* в компактной форме сначала отображается уведомление формат:

![Пример уведомления об compact папки "Входящие"](local-notifications-images/20-inbox-compact.png)

Когда пользователь перетаскивает на уведомление, он расширяет, чтобы отобразить сводку по электронной почте, как показано на следующем снимке экрана:

![Пример уведомления об папки "Входящие" развернута](local-notifications-images/21-inbox-expanded.png)

Чтобы создать *папки "Входящие"* уведомления, необходимо создать экземпляр `NotificationCompat.Builder` объекта, как и раньше и добавить [InboxStyle](https://developer.xamarin.com/api/type/Android.App.Notification+InboxStyle/) объект `NotificationCompat.Builder`. Пример:

```csharp
// Instantiate the Inbox style:
Notification.InboxStyle inboxStyle = new Notification.InboxStyle();

// Set the title and text of the notification:
builder.SetContentTitle ("5 new messages");
builder.SetContentText ("chimchim@xamarin.com");

// Generate a message summary for the body of the notification:
inboxStyle.AddLine ("Cheeta: Bananas on sale");
inboxStyle.AddLine ("George: Curious about your blog post");
inboxStyle.AddLine ("Nikko: Need a ride to Evolve?");
inboxStyle.SetSummaryText ("+2 more");

// Plug this style into the builder:
builder.SetStyle (inboxStyle);
```

Чтобы добавить новые строки текста в текст уведомления, вызовите [Addline](https://developer.xamarin.com/api/member/Android.App.Notification+InboxStyle.AddLine/p/System.String/) метод `InboxStyle` объекта (максимальная высота *папки "Входящие"* уведомлений — 256 dp). Обратите внимание, что, в отличие от *большой фрагмент текста* стиль, *папки "Входящие"* стиль поддерживает отдельные строки текста в текст уведомления.

Можно также использовать *папки "Входящие"* стиля для всех уведомлений, необходимые для отображения отдельные строки текста в развернутом виде. Например *папки "Входящие"* стиля уведомлений можно использовать для объединения нескольких уведомлений в состоянии ожидания в сводки уведомление &ndash; можно обновить один *папки "Входящие"* стиля уведомлений с помощью new строки содержимое уведомления (см. в разделе [обновление уведомление](#updating-a-notification) выше), а не создавать уведомления о новых, главным образом как непрерывный поток.


## <a name="configuring-metadata"></a>Настройка метаданных

`NotificationCompat.Builder` включает методы, которые можно вызвать, чтобы задать метаданные о уведомления, такие как приоритет, видимость и категории. Android использует эти сведения &mdash; вместе с настройки параметров пользователя &mdash; для определения того, как и когда отображать уведомления.


### <a name="priority-settings"></a>Параметры приоритета

Приложения, работающие на Android 7.1 и нижний необходимо задать приоритет непосредственно на в уведомлении. Параметр приоритета уведомления определяет два результата, когда публикуется уведомление:

-   Где уведомление отображается относительно других уведомлений.
    Например, с высоким приоритетом уведомления передаются выше нижней приоритет уведомления на панели уведомлений вне зависимости от при публикации каждого уведомления.

-   Следует ли отображать уведомление в формат Heads-up уведомлений (Android 5.0 и более поздние версии). Только *высокой* и *максимальное* приоритет уведомления отображаются в виде информационных индикаторов уведомления.

Xamarin.Android определяет следующие перечисления для задания приоритета уведомлений:

-   `NotificationPriority.Max` &ndash; Предупредит пользователя об срочные или важные условия (например, входящего вызова, направлениях включить, отключить или получен сигнал тревоги). На Android 5.0 и более поздних версий максимальный приоритет уведомления отображаются в формате информационных индикаторов.

-   `NotificationPriority.High` &ndash; Информирует пользователя о важных событиях (например, для важных сообщений электронной почты или получения сообщения чата в реальном времени). На Android 5.0 и более поздних версий с высоким приоритетом уведомления отображаются в формате информационных индикаторов.

-   `NotificationPriority.Default` &ndash; Уведомляет пользователя о условия, которым должны средний уровень важности.

-   `NotificationPriority.Low` &ndash; Не срочные сведения, что пользователь должен получать информацию (например, напоминания обновления программного обеспечения или обновления социальной сети).

-   `NotificationPriority.Min` &ndash; Справочные сведения, что пользователь видит только тогда, когда Просмотр уведомления (например, расположение или погоды сведения).

Чтобы задать приоритет уведомления, вызовите [SetPriority](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetPriority/) метод `NotificationCompat.Builder` объекта, передавая в уровень приоритета. Пример:

```csharp
builder.SetPriority (NotificationPriority.High);
```

В следующем примере, уведомление с высоким приоритетом, «Важное сообщение!» отображается в верхней части откройте панель уведомлений:

![Пример уведомления об высоким приоритетом](local-notifications-images/22-hi-priority-drawer.png)

Так как это уведомление высоким приоритетом, он также отображается в виде информационных индикаторов уведомлений поверх экрана текущее действие пользователя в Android 5.0:

![Пример уведомления об информационных индикаторов](local-notifications-images/23-heads-up-example.png)

В следующем примере уведомление низким приоритетом «Рассматривать за день» отображается уведомление уровня батареи более высоким приоритетом:

![Пример уведомления об низким приоритетом](local-notifications-images/24-lo-priority-drawer.png)

Так как низкоприоритетные это уведомление «Идейным за день», Android не отобразит его в формате Heads-up.

> [!NOTE]
> В Android 8.0 и более поздних версий приоритет канала и пользователем параметров уведомлений будет определить приоритет уведомления.

### <a name="visibility-settings"></a>Параметры видимости

Начиная с Android 5.0 *видимость* доступен параметр для управления объем содержимого уведомления появляются на экране блокировки безопасности.
Xamarin.Android определяет следующие перечисления для настройки видимости уведомлений:

-   `NotificationVisibility.Public` &ndash; Полный текст уведомления отображается на экране блокировки безопасности.

-   `NotificationVisibility.Private` &ndash; Отображаются только важные сведения на экране блокировки безопасности (например, значок и имя приложения, его публикации), но остальная часть сведений об уведомлении скрыты. По умолчанию все уведомления `NotificationVisibility.Private`.

-   `NotificationVisibility.Secret` &ndash; Ничего не отображается на экране блокировки безопасности, даже не значок уведомления. Содержимое уведомления становится доступна только в том случае, после этот пользователь разблокирует устройства.

Задание видимости уведомления, вызов приложения `SetVisibility` метод `NotificationCompat.Builder` объекта, передавая параметр видимости. Например, этот вызов `SetVisibility` делает уведомление `Private`:

```csharp
builder.SetVisibility (NotificationVisibility.Private);
```

Когда `Private` отправке уведомления, только имя и значок приложения отображается на экране блокировки безопасности. Вместо сообщения уведомления пользователь видит «Разблокировать устройства для этого уведомления см. в разделе»:

![Разблокировать сообщения уведомлений устройства](local-notifications-images/25-lockscreen-private.png)

В этом примере **NotificationsLab** имя исходного приложения. Это исправленная версия уведомление отображается версия только когда на экране блокировки является безопасным (т. е. защищено с помощью ПИН-кода, шаблон или пароль) &ndash; доступен Если экран блокировки не является безопасным, полный текст уведомления на экране блокировки.


### <a name="category-settings"></a>Параметры категории

Начиная с Android 5.0, стандартные категории доступны для ранжирования и фильтрации уведомлений. Xamarin.Android предоставляет следующие перечисления для следующих категорий:

-   `Notification.CategoryCall` &ndash; Телефонный звонок.

-   `Notification.CategoryMessage` &ndash; Текст входящего сообщения.

-   `Notification.CategoryAlarm` &ndash; Сигнал условие или таймер срока действия.

-   `Notification.CategoryEmail` &ndash; Входящее сообщение электронной почты.

-   `Notification.CategoryEvent` &ndash; События календаря.

-   `Notification.CategoryPromo` &ndash; Рекламное сообщение или объявления.

-   `Notification.CategoryProgress` &ndash; Ход выполнения фоновой операции.

-   `Notification.CategorySocial` &ndash; Социальные сети обновления.

-   `Notification.CategoryError` &ndash; Сбой в фоновом процессе операция или проверки подлинности.

-   `Notification.CategoryTransport` &ndash; Обновление воспроизведения мультимедиа.

-   `Notification.CategorySystem` &ndash; Зарезервировано для системного использования (состояние системы или устройства).

-   `Notification.CategoryService` &ndash; Указывает, что фоновая служба запущена.

-   `Notification.CategoryRecommendation` &ndash; Сообщение рекомендации, связанные с запущенного приложения.

-   `Notification.CategoryStatus` &ndash; Сведения об устройстве.

Когда уведомления были отсортированы, уведомления приоритет имеет приоритет над его параметр категории. К примеру, уведомление высоким приоритетом будет отображаться как информационных индикаторов, даже если он принадлежит `Promo` категории. Чтобы задать категорию уведомления, вызывать `SetCategory` метод `NotificationCompat.Builder` объекта, передавая параметр категории. Пример:

```csharp
builder.SetCategory (Notification.CategoryCall);
```

*Не беспокоить* функция (новое в Android 5.0) фильтрует уведомления на основе категорий. Например *не беспокоить* экрана в **параметры** позволяет исключить уведомления для телефонные звонки и сообщения:

![Не беспокоить коммутаторы экрана](local-notifications-images/26-do-not-disturb.png)

Когда пользователь настраивает *не беспокоить* блокировать все прерывания, за исключением телефонные звонки (как показано в приведенном выше снимке экрана), Android разрешает уведомлений с помощью настройки категории `Notification.CategoryCall` должна присутствовать и устройство в *не беспокоить* режим. Обратите внимание, что `Notification.CategoryAlarm` уведомления никогда не блокируются *не беспокоить* режим.

[LocalNotifications](https://developer.xamarin.com/samples/monodroid/LocalNotifications) образце показано, как использовать `NotificationCompat.Builder` для запуска второго действия для уведомления. В этом примере кода рассматривается в [с помощью локальных уведомлений в Xamarin.Android](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md) Пошаговое руководство.


### <a name="notification-styles"></a>Стили уведомлений

Для создания *большой фрагмент текста*, *изображение*, или *папки "Входящие"* стиля уведомлений с помощью `NotificationCompat.Builder`, приложение должно использовать совместимости версий этих стилей. Например, чтобы использовать *большой фрагмент текста* стиля, создать экземпляр `NotificationCompat.BigTextstyle`:

```csharp
NotificationCompat.BigTextStyle textStyle = new NotificationCompat.BigTextStyle();

// Plug this style into the builder:
builder.SetStyle (textStyle);
```

Аналогичным образом, приложение может использовать `NotificationCompat.InboxStyle` и `NotificationCompat.BigPictureStyle` для *папки "Входящие"* и *изображение* стили, соответственно.


### <a name="notification-priority-and-category"></a>Уведомление приоритета и категории

`NotificationCompat.Builder` поддерживает `SetPriority` (доступны начиная с Android версии 4.1). Тем не менее `SetCategory` метод *не* поддерживаемых `NotificationCompat.Builder` поскольку категории являются частью новой системы метаданных уведомлений, появившихся в Android 5.0.

Для поддержки более старых версиях Android, где `SetCategory` будет недоступен, ваш код может проверять уровень API во время выполнения для условного вызова `SetCategory` при уровне API равным или больше, чем Android 5.0 (уровень API 21):

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop) {
    builder.SetCategory (Notification.CategoryEmail);
}
```

В этом примере приложения **требуемой версии .NET Framework** присваивается Android 5.0 и **минимальной версии Android** присваивается **Android 4.1 (уровень API 16)**. Так как `SetCategory` — доступная в уровень API 21 и более поздние версии, будет вызывать этот пример кода `SetCategory` только он станет доступен &ndash; не вызовет `SetCategory` при меньше 21 уровень API.


### <a name="lock-screen-visibility"></a>Видимость экрана блокировки

Так как Android не поддерживает уведомления на экране блокировки до Android 5.0 (уровень API 21), `NotificationCompat.Builder` не поддерживает `SetVisibility` метод. Как описано выше для `SetCategory`, ваш код может проверять уровень API во время выполнения и вызов `SetVisiblity` только он станет доступен:

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop) {
    builder.SetVisibility (Notification.Public);
}
```


## <a name="summary"></a>Сводка

В этой статье описываются способы создания локальных уведомлений в Android. Описывались Анатомия уведомление, оно было рассмотрено, как использовать `NotificationCompat.Builder` для создания уведомлений, как уведомления стиля в крупных значков *большой фрагмент текста*, *изображение* и *папки "Входящие"*  форматы, как задать параметры метаданные, такие как приоритет, видимость и категорию уведомлений и запуск действия для уведомления. В этой статье также описано, как работают эти параметры уведомлений с помощью информационных индикаторов новый экран блокировки, и *не беспокоить* функции, представленные в Android 5.0. Наконец, вы узнали, как использовать `NotificationCompat.Builder` для поддержки уведомлений совместимости с более ранних версиях Android.

Рекомендации по проектированию уведомления для Android, см. в разделе [уведомления](http://developer.android.com/guide/topics/ui/notifiers/notifications.html).


## <a name="related-links"></a>Связанные ссылки

- [NotificationsLab (пример)](https://developer.xamarin.com/samples/monodroid/android5.0/NotificationsLab/)
- [LocalNotifications (пример)](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [Локальные уведомления в пошаговое руководство по Android](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md)
- [Уведомления пользователя](http://developer.android.com/training/notify-user/index.html)
- [Уведомление](https://developer.xamarin.com/api/type/Android.App.Notification/)
- [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/)
- [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/)
