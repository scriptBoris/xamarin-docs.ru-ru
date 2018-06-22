---
title: Локальный уведомления
description: В этом разделе показано, как реализовать локальный уведомления в Xamarin.Android. Он объясняет Android уведомление о различных элементов пользовательского интерфейса и описываются API связанные с созданием и отображает соответствующее уведомление.
ms.prod: xamarin
ms.assetid: 03E19D14-7C81-4D5C-88FC-C3A3A927DB46
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 97c8372656f0cbfa5b8f7bb12d15b00feac4b5c3
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30773996"
---
# <a name="local-notifications"></a>Локальный уведомления

_В этом разделе показано, как реализовать локальный уведомления в Xamarin.Android. Он объясняет Android уведомление о различных элементов пользовательского интерфейса и описываются API связанные с созданием и отображает соответствующее уведомление._

## <a name="local-notifications-overview"></a>Общие сведения о локальном уведомления

В этом разделе объясняется, как реализовать локальный уведомления в приложении Xamarin.Android. В нем рассматриваются различные части Android уведомление, объясняется, какие стили различные уведомления, доступных разработчикам приложений, а также дает некоторые API-интерфейсов, которые используются для создания и публикации уведомления.

Android предоставляет две области контролем системы для отображения значков уведомлений и сведения об уведомлениях для пользователя. При публикации уведомления его значок отображается в *область уведомлений*, как показано на следующем снимке экрана:

![Пример области уведомлений на устройстве](local-notifications-images/01-notification-shade.png)

Для получения сведений об уведомлении, пользователь может открыть ящик уведомления (которая разворачивается каждый значок уведомления для отображения содержимого уведомлений) и выполнения действий, связанных с уведомлениями. На следующем снимке экрана показано *drawer уведомления* , соответствующий области уведомлений, отображенный выше:

[![Пример уведомления лоток отображать три уведомления](local-notifications-images/02-notification-drawer-sml.png)](local-notifications-images/02-notification-drawer.png#lightbox)

Уведомлений Android использовать два вида макетов:

-   ***Базовая схема*** &ndash; формате compact, основного представления.

-   ***Расширенная макета*** &ndash; формат представления, который может быть расширен до большего размера, для получения сведений.

В следующих разделах описан каждый из этих типов макета (и об их создании).


### <a name="base-layout"></a>Базовая схема

Все уведомления Android основаны на формат базовая схема, которой, как минимум, включает следующие элементы:

1.  Объект *значок уведомления*, который представляет исходного приложения или тип уведомления, если приложение поддерживает различные типы уведомлений.

2.  Уведомление *заголовка*, или имя отправителя, если личное сообщение уведомления.

3.  Сообщение уведомления.

4.  Объект *timestamp*.

Эти элементы отображаются, как показано на следующей схеме:

[![Расположение элементов уведомления](local-notifications-images/03-notification-callouts-sml.png)](local-notifications-images/03-notification-callouts.png#lightbox)

Базовый макеты ограничены 64 плотность независимых пикселях (dp) в высоту. Android создает этот стиль уведомлений basic по умолчанию.

При необходимости уведомлений можно отобразить крупный значок, представляющий приложение или фото отправителя. При использовании крупных значков в уведомлении в Android 5.0 и более поздних небольшой значок отображается как эмблемы на большой значок:

![Простое уведомление о фото](local-notifications-images/04-simple-notification-photo.png)

Начиная с Android 5.0, уведомления могут также находиться в экрана блокировки:

[![Пример уведомления экрана блокировки](local-notifications-images/05-lockscreen-notification-sml.png)](local-notifications-images/05-lockscreen-notification.png#lightbox)

Пользователь может дважды коснитесь уведомления экрана блокировки для разблокировки устройства и перехода приложения, создавшего оповещение, или проведите, чтобы закрыть уведомление. Приложения можно задать уровень видимости уведомления для управления отображаемым на экрана блокировки, и пользователи могут выбирать, следует ли разрешить конфиденциальное содержимое, которое будет отображаться в уведомления экрана блокировки.

Android 5.0 появился формате презентации уведомлений с высоким приоритетом, который называется *специальный*. Специальный уведомления соскальзывания из верхней части экрана на несколько секунд и затем retreat создать резервную копию в области уведомлений:

[![Пример heads-up уведомления](local-notifications-images/06-heads-up-notification-sml.png)](local-notifications-images/06-heads-up-notification.png#lightbox)

Специальный уведомлений позволяют системе пользовательского интерфейса для размещения важных информацию, не прерывая его состояние текущего выполняемого действия.

Android включает поддержку уведомлений метаданных, чтобы возможность сортировки и отображения интеллектуально уведомления. Метаданные уведомлений также управляет как уведомления должны быть представлены на экрана блокировки и специальный формат. Приложения можно установить следующие виды метаданных уведомлений:

-   **Приоритет** &ndash; уровень приоритета определяет, как и когда представлены уведомления. Например, в Android 5.0 важных уведомлений отображаются как специальный уведомления.

-   **Видимость** &ndash; указывает объем содержимого уведомление будет отображаться при появлении уведомления на экрана блокировки.

-   **Категория** &ndash; сообщает системе, как обрабатывать уведомления в различных ситуациях, например когда устройство находится в *не беспокоить* режим.

**Примечание:** **видимость** и **категории** впервые появились в Android 5.0 и недоступны в более ранних версиях Android. Начиная с Android 8.0 [каналов уведомлений](#notif-chan) используются для управления как уведомления должны быть представлены пользователю.


### <a name="expanded-layouts"></a>Расширенная макетов

Начиная с версии 4.1 платформы Android, уведомления можно настроить с помощью стилей развернутой макет, позволяющие пользователям разверните во всю высоту уведомлений для просмотра дополнительного содержимого. Например в следующем примере уведомление о расширенном макет в режиме договору:

![Договору уведомления](local-notifications-images/07-contracted-notification.png)

При развертывании этого уведомления, оно показывает все сообщение:

![Расширенная уведомления](local-notifications-images/08-expanded-notification.png)

Android поддерживает три стилей развернутой макета для уведомлений о событиях один:

-   ***Большой фрагмент текста*** &ndash; в режиме договору, отображает фрагмент первой строки сообщения следуют две точки. В расширенном режиме отображаются все сообщение (как показано в приведенном выше примере).

-   ***Папка "Входящие"*** &ndash; в режиме договору, отображает количество новых сообщений. В расширенном режиме отображается первое сообщение электронной почты или список сообщений в почтовом ящике.

-   ***Изображение*** &ndash; в режиме договору, отображает только текст сообщения. В развернутом режиме отображает текст и изображения.

[Помимо уведомлений Basic](#beyond-the-basic-notification) (Далее в этой статье) описывается создание *большой фрагмент текста*, *папки "Входящие"*, и *изображения* уведомления.


## <a name="notification-creation"></a>Создание уведомления

Чтобы создать уведомление в Android, используйте [Notification.Builder](https://developer.xamarin.com/api/type/Android.App.Notification+Builder/) класса. `Notification.Builder` появилась в версии 3.0 для упрощения создания объектов уведомлений Android. Создание уведомлений, которые совместимы с более ранними версиями Android, можно использовать [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html) вместо класса `Notification.Builder` (см. [совместимости](#compatibility) далее в этом разделе для Дополнительные сведения об использовании `NotificationCompat.Builder`).
`Notification.Builder` Предоставляет методы для задания различных параметров в уведомления, такие как:

-   Содержимое, включая заголовок, текст сообщения и значок уведомления.

-   Стиль уведомлений, таких как *большой фрагмент текста*, *папки "Входящие"*, или *изображения* стиля.

-   Приоритет уведомление: минимальное, низкое значение, по умолчанию, высокой или максимальное.

-   Видимость уведомления экрана блокировки: public, private или секретный код.

-   Категории метаданных, что помогает Android классификации и фильтрации уведомления.

-   Необязательное назначение, указывающее действие для запуска при касании уведомления.

После установки этих параметров в конструкторе, создается объект на уведомления, содержащий параметры. Чтобы опубликовать уведомление, передайте этот объект уведомления для *диспетчера уведомлений*. Предоставляет Android [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/) класс, который отвечает за публикации уведомления и отображать их пользователю. Ссылка на класс можно получить из любого контекста, например действие или службы.


### <a name="how-to-generate-a-notification"></a>Создание уведомления

Для создания уведомления в Android, выполните следующие действия.

1.  Создать экземпляр `Notification.Builder` объекта.

2.  Различные методы `Notification.Builder` объекта, чтобы задать параметры уведомления.

3.  Вызовите [построения](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.Build/) метод `Notification.Builder` объекта для создания экземпляра объекта уведомления.

4.  Вызовите [уведомления](https://developer.xamarin.com/api/member/Android.App.NotificationManager.Notify/(System.Int32%2cAndroid.App.Notification)) метод диспетчера уведомлений для публикации уведомления.

Необходимо указать по крайней мере следующие сведения для каждого из уведомлений:

-   Маленький значок (24 x 24 точки распространения в размер)

-   Краткое название

-   Текст уведомления

В следующем примере кода показано, как использовать `Notification.Builder` для создания основных уведомления. Обратите внимание, что `Notification.Builder` методы поддерживают [цепочки метод](http://en.wikipedia.org/wiki/Method_chaining); то есть каждый метод возвращает объект построителя, поэтому результат последнего вызова метода можно использовать для выполнения следующего вызова метода:

```csharp
// Instantiate the builder and set notification elements:
Notification.Builder builder = new Notification.Builder (this)
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

В этом примере новый `Notification.Builder` объекта с именем `builder` будет создан экземпляр, заголовок и текст уведомления и установлены значок уведомления загружается из **Resources/drawable/ic_notification.png**. Вызов построителя уведомления `Build` метод создает объект уведомления с помощью этих параметров. Следующим шагом является вызов `Notify` метод диспетчера уведомлений. Чтобы найти диспетчера уведомлений, вызовите `GetSystemService`, как показано выше.

`Notify` Метод принимает два параметра: идентификатор уведомления и объекта уведомления. Идентификатор уведомления является уникальным целым числом, идентифицирующее уведомление в приложение. В этом примере идентификатор уведомления равно нулю (0); Однако в реальном приложении необходимо предоставить уникальный идентификатор каждого уведомления. Повторное использование предыдущего значения идентификатора при обращении к `Notify` вызывает последнего уведомления будут перезаписаны.

Когда этот код выполняется на устройстве с Android 5.0, он создает уведомление, которое выглядит как в следующем примере:

![Результат уведомления в образце кода](local-notifications-images/09-hello-world.png)

Значок уведомления отображается в левой части уведомление &ndash; изображение кружке &ldquo;я&rdquo; имеет альфа-канал, чтобы Android можно нарисовать серым фоном циклические стоящий за ней. Можно также указать значок без альфа-канала. Фотографии отображаются в виде значков, в разделе [большой значок формата](#large-icon-format) далее в этом разделе.

Отметка времени задается автоматически, но этот параметр можно переопределить путем вызова [умолчаниюПри](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetWhen/) метод построителя уведомления. Например в следующем примере кода задает метку времени в текущее время:

```csharp
builder.SetWhen (Java.Lang.JavaSystem.CurrentTimeMillis());
```

### <a name="enabling-sound-and-vibration"></a>Включение звука и вибрации

Если требуется также звуковое уведомление, можно вызвать построитель уведомления [SetDefaults](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetDefaults/) метод и передайте его в `NotificationDefaults.Sound` флаг:

```csharp
// Instantiate the notification builder and enable sound:
Notification.Builder builder = new Notification.Builder (this)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetDefaults (NotificationDefaults.Sound)
    .SetSmallIcon (Resource.Drawable.ic_notification);
```

Этот вызов `SetDefaults` устройство для воспроизведения звука при публикации уведомления. Если требуется, чтобы устройства компакт-дисков, а не на воспроизведение звука, вы можете передать `NotificationDefaults.Vibrate` для `SetDefaults.` следует устройства для воспроизведения звука и управлять вибрацией устройства можно передать обоих флагов для `SetDefaults`:

```csharp
builder.SetDefaults (NotificationDefaults.Sound | NotificationDefaults.Vibrate);
```

При включении звук без указания звук, воспроизводимый Android использует звуковой сигнал системы по умолчанию. Тем не менее, можно изменить звук, который будет воспроизводиться вызовом построителя уведомления [SetSound](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetSound/p/Android.Net.Uri/) метод. Например, чтобы воспроизвести звуковой сигнал звука с уведомления (а не по умолчанию звуковой сигнал), можно получить URI для звуковой сигнал звук из [RingtoneManager](https://developer.xamarin.com/api/type/Android.Media.RingtoneManager/) и передать его в `SetSound`:

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Alarm));
```

Кроме того можно использовать мелодии звонка по умолчанию система звука для уведомления:

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Ringtone));
```

После создания объекта уведомления, можно задать свойства уведомления объекта уведомления (вместо их настройки с помощью заранее `Notification.Builder` методов). Например, вместо вызова метода `SetDefaults` способ включения вибрация на уведомление, можно непосредственно изменить битовым флагом уведомления [по умолчанию](https://developer.xamarin.com/api/property/Android.App.Notification.Defaults/) свойство:

```csharp
// Build the notification:
Notification notification = builder.Build();

// Turn on vibrate:
notification.Defaults |= NotificationDefaults.Vibrate;
```

В этом примере заставляет устройство компакт-дисков, при публикации уведомления.

<a name="updating-a-notification" />

### <a name="updating-a-notification"></a>Уведомление об обновлении

Если вы хотите обновить содержимое уведомление после его публикации, можно повторно использовать существующий `Notification.Builder` объект для создания нового объекта уведомления и опубликовать это уведомление с идентификатором последнего уведомления. Пример:

```csharp
// Update the existing notification builder content:
builder.SetContentTitle ("Updated Notification");
builder.SetContentText ("Changed to this message.");

// Build a notification object with updated content:
notification = builder.Build();

// Publish the new notification with the existing ID:
notificationManager.Notify (notificationId, notification);
```

В этом примере существующий `Notification.Builder` объект используется для создания нового объекта уведомления с другой заголовок и сообщение.
Новый объект уведомления опубликована с использованием идентификатор предыдущего уведомления и это обновляет содержимое уведомления, ранее опубликованные:

![Обновленные уведомления](local-notifications-images/12-updated-notification.png)

Повторно текст предыдущего уведомления &ndash; только заголовок и текст уведомления меняется во время отображения уведомления в ящике уведомления. Изменяет текст заголовка с «Пример уведомления» на «Обновлен уведомлений» и текст сообщения меняется с «Hello World! Это Мой первый уведомление!» Для «Изменение на данное сообщение.»

Уведомление остается видимым, пока не произойдет одно из трех действий:

-   Пользователь закрывает уведомление (или касается *очистить все*).

-   Приложение вызывает метод `NotificationManager.Cancel`, передавая уведомления уникальный идентификатор, назначенный при публикации уведомления.

-   Приложение вызывает `NotificationManager.CancelAll`.

Дополнительные сведения об обновлении уведомлений Android см. в разделе [изменить уведомление](http://developer.android.com/training/notify-user/managing.html#Updating).


### <a name="starting-an-activity-from-a-notification"></a>Запуск действия для уведомления

В Android, являются общими для уведомления должны быть связаны с *действия* &ndash; действие, которое запускается, когда пользователь касается уведомления. Это действие может находиться в другом приложении или даже в другой задачи. Чтобы добавить действие уведомление, создать [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/) и свяжите `PendingIntent` с уведомлением. Объект `PendingIntent` — это специальный тип пожеланий, позволяет приложению-получателю для запуска предварительно определенная часть кода с разрешениями, передающего приложения. Когда пользователь касается уведомления, Android запускается действие, заданное обработчиком `PendingIntent`.

В следующем фрагменте кода показано, как создать подписку на уведомления с `PendingIntent` , запускает действие исходного приложения `MainActivity`:

```csharp
// Set up an intent so that tapping the notifications returns to this app:
Intent intent = new Intent (this, typeof(MainActivity));

// Create a PendingIntent; we're only using one PendingIntent (ID = 0):
const int pendingIntentId = 0;
PendingIntent pendingIntent =
    PendingIntent.GetActivity (this, pendingIntentId, intent, PendingIntentFlags.OneShot);

// Instantiate the builder and set notification elements, including pending intent:
Notification.Builder builder = new Notification.Builder(this)
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

Этот код выполняется аналогично код уведомления в предыдущем разделе, за исключением того, что `PendingIntent` добавляется к объекту уведомления. В этом примере `PendingIntent` связан с активность исходного приложения до его передачи в конструктор уведомления [SetContentIntent](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetContentIntent/) метод. `PendingIntentFlags.OneShot` Флаг передается в `PendingIntent.GetActivity` метод, чтобы `PendingIntent` используется только один раз. При выполнении этого кода, отображается следующее уведомление:

![Первое действие-уведомление](local-notifications-images/10-first-action-notification.png)

Коснитесь уведомления направляет пользователя обратно на источника действия.

В рабочем приложении, приложения должны обрабатывать *стек* при нажатии **обратно** кнопка действия уведомления (Если вы не знакомы с Android задачами и назад, см. раздел [ Задачи и стек](http://developer.android.com/guide/components/tasks-and-back-stack.html)).
В большинстве случаев переход в обратном направлении из действия уведомлений должен возвращать пользователя из приложения и обратно в начальном экране. Для управления стек использует ваша приложения [TaskStackBuilder](https://developer.xamarin.com/api/type/Android.App.TaskStackBuilder/) для создаваемого класса `PendingIntent` с задней стека.

Еще один нюанс реальных является источника действия может потребоваться отправлять данные в действие уведомления. Например уведомление может указывать пришло текстовое сообщение, что действие уведомления (сообщение Просмотр экрана), требуется идентификатор сообщения для отображения сообщений пользователю. Действие, которое создает `PendingIntent` можно использовать [Intent.PutExtra](https://developer.xamarin.com/api/member/Android.Content.Intent.PutExtra/p/System.String/System.String/) метод для добавления данных (например, строку) в назначение, чтобы данные передаются в действие уведомления.

В следующем образце кода показано, как использовать `TaskStackBuilder` для управления этот стек, который содержит пример отправки одного сообщения строки уведомления действие с именем `SecondActivity`:

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
Notification.Builder builder = new Notification.Builder (this)
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

В этом примере кода приложение состоит из двух действий: `MainActivity` (который содержит указанный выше код уведомлений), и `SecondActivity`, пользователь увидит после касания уведомления экрана. При выполнении этого кода представлен простой уведомление (аналогично предыдущему примеру). Коснувшись уведомление направляет пользователя на `SecondActivity` экрана:

![Снимок экрана второго действия](local-notifications-images/11-second-activity.png)

Строковое сообщение (переданные в цель `PutExtra` метод) получается в `SecondActivity` через эту строку кода:

```csharp
// Get the message from the intent:
string message = Intent.Extras.GetString ("message", "");
```

Это полученное сообщение «Greetings из MainActivity!,» отображается в `SecondActivity` экрана, как показано в приведенном выше снимке экрана. Когда пользователь нажимает **обратно** кнопки при `SecondActivity`, интересы навигации из приложения и на экран, перед запуском приложения.

Дополнительные сведения о создании ожидающие намерения см. в разделе [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/).


<a name="notif-chan" />

## <a name="notification-channels"></a>Каналы уведомления

Начиная с Android 8.0 (Oreo), можно использовать *каналов уведомлений* компонент для создания канала, настраиваемые пользователем для каждого типа уведомление, которое нужно отобразить. Каналы уведомлений позволяют автоматически группы уведомлений, чтобы все уведомления опубликовано в приложение канал такое же поведение. Например может потребоваться канала уведомлений, которое предназначено для уведомления, которые требуют немедленного внимания и отдельный канал «тише», используемый для информационных сообщений.

**YouTube** приложение, которое устанавливается вместе с Android Oreo перечислены две категории уведомлений: **загрузить уведомления** и **общие уведомления**:

[![Экраны уведомления для YouTube в Android Oreo](local-notifications-images/27-youtube-sml.png)](local-notifications-images/27-youtube.png#lightbox)

Каждая из этих категорий соответствует канала уведомления. Реализует приложения YouTube **загрузить уведомления** канала и **общие уведомления** канала. Пользователь сможет нажать **загрузить уведомления**, отображающий параметры экрана для приложения загрузить канал уведомлений:

[![Загрузите экрана уведомления для приложения YouTube](local-notifications-images/28-yt-download-sml.png)](local-notifications-images/28-yt-download.png#lightbox)

На этом экране пользователь может изменить поведение **загрузки** канала уведомления следующим образом:

-   Установите уровень важности **срочно**, **высокой**, **Средний**, или **Low**, который настраивает уровень прерывания звуковые и visual.

-   Отключение точки уведомления.

-   Выключать мигающий свет.

-   Показать или скрыть уведомления на экране блокировки.

-   Переопределить **не беспокоить** параметр.

**Общие уведомления** канал имеет аналогичные параметры:

[![Общие уведомления экрана для приложения YouTube](local-notifications-images/29-yt-general-sml.png)](local-notifications-images/29-yt-general.png#lightbox)

Обратите внимание, что у вас Абсолютный контроль каналы уведомлений взаимодействие с пользователем &ndash; пользователь может изменить параметры для любого канала уведомления на устройстве, как показано на снимке экрана выше. Тем не менее можно настроить значения по умолчанию (как будет описано ниже). Как показывают эти примеры, новая функция каналы уведомлений делает можно предоставить пользователям тонкую настройку различных видов уведомления.

Следует ли добавлять поддержку каналов уведомлений в приложение? Если целевой Android 8.0, приложение *должен* реализации каналов уведомления.
Приложения, предназначенные для Oreo, который пытается отправить уведомление локального пользователя без использования канала уведомления не будут отображать уведомления на устройствах Oreo. Если не назначить Android 8.0, приложение будет выполняться на Android 8.0, но в то же самое уведомлений как переведет ее в ОС Android 7.1 или более ранней версии.


### <a name="creating-a-notification-channel"></a>Создание канала уведомления

Чтобы создать канал уведомления, выполните следующее:

1. Создать [NotificationChannel](https://developer.android.com/reference/android/app/NotificationChannel.html) объекта со следующими:

    - Строка идентификатора, который уникален в пределах пакета. В следующем примере строка `com.xamarin.myapp.urgent` используется.

    - Видимое пользователю имя канала (не более 40 знаков). В следующем примере имя **срочно** используется.

    - Важность канал, который определяет, каким образом прерывающихся уведомления отправляются `NotificationChannel`. Может быть важность `Default`, `High`, `Low`, `Max`, `Min`, `None`, или `Unspecified`.

    Передачи этих значений для [конструктор](https://developer.android.com/reference/android/app/NotificationChannel.html#NotificationChannel%28java.lang.String,%20java.lang.CharSequence,%20int%29) (в этом примере `Resource.String.noti_chan_urgent` равно **срочно**):

    ```csharp
    public const string URGENT_CHANNEL = "com.xamarin.myapp.urgent";
    . . .
    string chanName = GetString (Resource.String.noti_chan_urgent);
    var importance = NotificationImportance.High;
    NotificationChannel chan =
       new NotificationChannel (URGENT_CHANNEL, chanName, importance);
    ```

2.  Настройка `NotificationChannel` объекта первоначальной настройки.
    Например, следующий код настраивает `NotificationChannel` таким образом, опубликованных для этого канала уведомления будет управлять вибрацией устройства и отображаются на экране блокировки по умолчанию:

    ```csharp
    chan.EnableVibration (true);
    chan.LockscreenVisibility = NotificationVisibility.Public;
    ```

3.  Отправьте уведомления объект канала для диспетчера уведомлений:

    ```csharp
    NotificationManager notificationManager =
        (NotificationManager) GetSystemService (NotificationService);
    notificationManager.CreateNotificationChannel (chan);
    ```


### <a name="posting-to-a-notifications-channel"></a>Учет канал уведомления

Чтобы отправить уведомление для канала уведомления, сделайте следующее.

1.  Настройка уведомлений с помощью `Notification.Builder`, передавая идентификатор канала для `SetChannelId` метод. Пример:

    ```csharp
    Notification.Builder builder = new Notification.Builder (this)
        .SetContentTitle ("Attention!")
        .SetContentText ("This is an urgent notification message!")
        .SetChannelId (URGENT_CHANNEL);
    ```

2.  Построение и выдавать уведомления с помощью диспетчера уведомлений [уведомления](https://developer.xamarin.com/api/member/Android.App.NotificationManager.Notify/p/System.Int32/Android.App.Notification/) метод:

    ```csharp
    const int notificationId = 0;
    notificationManager.Notify (notificationId, builder.Build());
    ```

Можно повторить шаги выше, чтобы создать другой канал уведомления для информационных сообщений. Этот второй канал по умолчанию отключить вибрация, настроить видимость для экрана блокировки по умолчанию `Private`и задать важность уведомления `Default`.

Полный пример кода каналов уведомлений Android Oreo в действии см. в разделе [NotificationChannels](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels) образец; приложение из этого примера управляет два канала и настраивает параметры дополнительных уведомлений.



<a name="beyond-the-basic-notification" />

## <a name="beyond-the-basic-notification"></a>Помимо уведомлений Basic

По умолчанию уведомления о формате простую базовый макет в Android, но этот базовый формат можно улучшить путем внесения дополнительных `Notification.Builder` вызовы методов. В этом разделе вы узнаете, как добавить значок большое изображение уведомления, и вы увидите примеры создания уведомлений о расширенном макета.

<a name="large-icon-format" />

### <a name="large-icon-format"></a>Формат больших значков.

Уведомлений Android обычно отображают значок исходного приложения (слева уведомления). Тем не менее, может отображать уведомления, изображения или фотографии ( *большой значок*) вместо стандартной мелкого значка. Например обмена сообщениями приложение может отобразить фотографию отправителя, а не значка приложения.

Ниже приведен пример основные уведомления Android 5.0 &ndash; отображается только значок небольших приложений:

![Пример обычного уведомления](local-notifications-images/13-sample-notification.png)

А ниже приведен снимок экрана уведомления, изменив его для отображения крупных значков &ndash; он использует значок из образа monkey Xamarin кода:

![Пример большой значок уведомления](local-notifications-images/14-large-icon-sample.png)

Обратите внимание, что когда уведомление предоставляется в формате крупных значков, значок небольших приложений как значка в правом нижнем углу крупных значков.

Чтобы использовать изображение, как большой значок в уведомлении, вызовите построитель уведомления [SetLargeIcon](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetLargeIcon/) метод и передайте его в растровое изображение изображения. В отличие от `SetSmallIcon`, `SetLargeIcon` принимает только растровое изображение. Чтобы преобразовать файл изображения в растровое изображение, используйте [BitmapFactory](https://developer.xamarin.com/api/type/Android.Graphics.BitmapFactory/) класса. Пример:

```csharp
builder.SetLargeIcon (BitmapFactory.DecodeResource (Resources, Resource.Drawable.monkey_icon));
```

В этом примере кода открывается файл изображения на **Resources/drawable/monkey_icon.png**, преобразует его в растровое изображение и передает конечного растрового изображения для `Notification.Builder`. Как правило, разрешение изображения источника больше, чем мелкий значок &ndash; , но не гораздо большего размера. Слишком большое изображение может привести к ненужных операций по изменению размера, могут отложить учета уведомления.
Дополнительные сведения о размерах значок уведомления в Android см. в разделе [значка уведомления о](http://developer.android.com/design/style/iconography.html#notification).


### <a name="big-text-style"></a>Стиль большой фрагмент текста

*Большой фрагмент текста* стиль — расширенная макета шаблон, который используется для отображения длинных сообщений уведомления. Как и все уведомления развернутой макета большой фрагмент текста уведомления первоначального отображения в формате compact представления:

![Пример большой фрагмент текста уведомления](local-notifications-images/15-big-text-notification.png)

В этом формате отображается только фрагмент сообщения, завершается с двух точек. При перетаскивании на уведомление, он разворачивается для отображения всей уведомление:

![Расширенная большой фрагмент текста уведомления](local-notifications-images/16-big-text-expanded.png)

Этот формат развернутой макета также включает текст сводки в нижней части уведомления. Максимальная высота *большой фрагмент текста* уведомления — 256 точки распространения.

Для создания *большой фрагмент текста* уведомления, можно создать `Notification.Builder` объекта, как и ранее и затем создать и добавить [BigTextStyle](https://developer.xamarin.com/api/type/Android.App.Notification+BigTextStyle/) объект `Notification.Builder` объекта. Пример:

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

В этом примере текст сообщения и текст сводки хранятся в `BigTextStyle` объекта (`textStyle`) до его передачи в `Notification.Builder.`


### <a name="image-style"></a>Стиль изображения

*Изображения* стиля (также называется *общую картину* стиль)-это формат развернутой уведомления, можно использовать для отображения изображения в текст уведомления. Например, можно использовать на снимке экрана приложение или приложение фото *изображения* уведомления стиля для предоставления пользователю уведомление последнего образа, который был записан. Обратите внимание, что максимальная высота *изображения* уведомления — 256 dp &ndash; Android будут изменяет размер изображения, чтобы поместиться в это ограничение максимальной высоты, в пределах от доступной памяти.

Как и всех развернут уведомления макета *изображения* уведомления сначала отображаются в компактном формате, который отображает фрагмент сопутствующий текст сообщения:

![Уведомления Compact изображений показывает изображение не](local-notifications-images/17-image-compact.png)

Когда пользователь перетаскивает *изображения* уведомления, он разворачивается для отображения изображения. Например вот расширенная версия предыдущего уведомления.

![Изображение выявляет уведомлений расширенного изображения](local-notifications-images/18-image-expanded.png)

Обратите внимание, что при отображении уведомления в компактном формате отображает текст уведомления (текст, который передается в конструктор уведомления `SetContentText` метода, как показано выше). Однако при развертывании уведомление для отображения изображения отображается текст сводки над изображением.

Для создания *изображения* уведомления, можно создать `Notification.Builder` объекта, как и раньше и затем создать и вставить [BigPictureStyle](https://developer.xamarin.com/api/type/Android.App.Notification+BigPictureStyle/) объекта в `Notification.Builder` объекта. Пример:

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

Как `SetLargeIcon` метод `Notification.Builder`, [BigPicture](https://developer.xamarin.com/api/member/Android.App.Notification+BigPictureStyle.BigPicture/) метод `BigPictureStyle` требует битовой карты, изображения, которое будет отображаться в текст уведомления. В этом примере [DecodeResource](https://developer.xamarin.com/api/member/Android.Graphics.BitmapFactory.DecodeResource/(Android.Content.Res.Resources%2cSystem.Int32)) метод `BitmapFactory` считывает файл изображения, расположенный **Resources/drawable/x_bldg.png** и преобразует его в растровое изображение.

Можно также отобразить изображения, которые не упакованы как ресурс. Например, в следующем примере кода загружает изображение из локального SD-карту и отображает его в *изображения* уведомления:

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

В этом примере файл изображения размещается в **/sdcard/Pictures/my-tshirt.jpg** загружен, изменяется до половины его исходный размер и затем преобразуется в растровое изображение для использования в области уведомлений:

![Изображение примера футболки в уведомлении](local-notifications-images/19-tshirt-notification.png)

Если размер файла изображения не известен заранее, рекомендуется заключать вызов [BitmapFactory.DecodeFile](https://developer.xamarin.com/api/member/Android.Graphics.BitmapFactory.DecodeFile/p/System.String/Android.Graphics.BitmapFactory+Options/) в обработчике исключений &ndash; `OutOfMemoryError` исключение может быть вызвано, если изображение слишком велико для Android для изменения размера.

Дополнительные сведения о загрузке и декодирование изображений большую битовую карту, в разделе [нагрузки большие точечные рисунки эффективно](https://developer.xamarin.com/recipes/android/resources/general/load_large_bitmaps_efficiently).


### <a name="inbox-style"></a>Стиль папки "Входящие"

*Папки "Входящие"* форматом является развернутой макета шаблон предназначен для отображения отдельной строки текста (например, электронной почты папки "Входящие" Сводка) в текст уведомления. *Папки "Входящие"* сначала отображается уведомление о формате в компактном формате:

![Пример compact входящее уведомление](local-notifications-images/20-inbox-compact.png)

Когда пользователь перетаскивает на уведомление, он расширяет Показать сводку по электронной почте, как показано на снимке экрана ниже:

![Пример входящее уведомление развернут](local-notifications-images/21-inbox-expanded.png)

Для создания *папки "Входящие"* уведомления, можно создать `Notification.Builder` , как и ранее и добавьте [InboxStyle](https://developer.xamarin.com/api/type/Android.App.Notification+InboxStyle/) объект `Notification.Builder`. Пример:

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

Чтобы добавить новые строки текста в текст уведомления, вызовите [Addline](https://developer.xamarin.com/api/member/Android.App.Notification+InboxStyle.AddLine/p/System.String/) метод `InboxStyle` объекта (максимальная высота *папки "Входящие"* уведомления — 256 точки распространения). Обратите внимание, что в отличие от *большой фрагмент текста* стиля, *папки "Входящие"* стиль поддерживает отдельные строки текста в текст уведомления.

Можно также использовать *папки "Входящие"* стиля для всех уведомлений, который должен отображаться в расширенном формате отдельные строки текста. Например *папки "Входящие"* стиль уведомления может использоваться для объединения нескольких ожидающих уведомлений в сводного уведомления &ndash; можно обновить один *папки "Входящие"* стиля уведомления с помощью оператора new строкам уведомление содержимого (см. [обновление уведомление](#updating-a-notification) выше), а не создавать поток уведомления о новых, практически аналогично. Дополнительные сведения об этом подходе см. в разделе [суммировать уведомления](http://developer.android.com/design/patterns/notifications.html#summarize_your_notifications).


## <a name="configuring-metadata"></a>Настройка метаданных

`Notification.Builder` включает методы, которые можно вызвать, чтобы задать метаданные о вашей уведомления, например приоритет, видимость и категории. Android использует эти сведения &mdash; вместе с персональные настройки пользователя &mdash; для определения того, как и когда отображать уведомления.


### <a name="priority-settings"></a>Параметры приоритета

Параметр приоритета уведомления определяет два результата, при публикации уведомления:

-   Где уведомление отображается относительно других уведомлений.
    Например уведомления с высоким приоритетом представлены выше ниже приоритет уведомления в ящике уведомления, вне зависимости от каждого уведомления находилась при публикации.

-   Вывод уведомления в формате Heads-up уведомлений (Android 5.0 и более поздние версии). Только *высокой* и *максимальное* приоритет уведомления отображаются как специальный уведомления.

Xamarin.Android определяет следующие перечисления для задания приоритета уведомления:

-   `NotificationPriority.Max` &ndash; Предупреждает пользователя неотложной или критической условие (например, входящего вызова, направлениях включить, отключить или получен сигнал тревоги). На Android 5.0 и более поздних версий максимальный приоритет уведомления отображаются в формате планового выпуска.

-   `NotificationPriority.High` &ndash; Информирует пользователя о важных событиях (например важные сообщения электронной почты или поступления сообщения в режиме реального времени). На Android 5.0 и более поздних версий уведомления высокий приоритет, отображаются в специальный формат.

-   `NotificationPriority.Default` &ndash; Уведомляет пользователя условия, которым должны средний уровень важности.

-   `NotificationPriority.Low` &ndash; Не срочные сведения, что пользователь должен получать информацию (например, напоминания обновления программного обеспечения или обновления социальной сети).

-   `NotificationPriority.Min` &ndash; Сведения общего характера, что пользователь замечает, только если Просмотр уведомления (например, расположение или погоды сведения).

Чтобы задать приоритет уведомление, вызовите [SetPriority](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetPriority/) метод `Notification.Builder` объекта, передавая в уровень приоритета. Пример:

```csharp
builder.SetPriority (NotificationPriority.High);
```

Приведенный ниже, уведомление высокий приоритет, «Важное сообщение!» отображается в верхней части ящика уведомления:

![Пример важных уведомлений](local-notifications-images/22-hi-priority-drawer.png)

Это уведомление высоким приоритетом, также отображается в виде уведомлений специальный выше текущего экрана действия пользователя в Android 5.0:

![Пример специальный уведомления](local-notifications-images/23-heads-up-example.png)

В следующем примере уведомление низкого приоритета «Рассматривать за день» появляется в уведомления уровня батареи с более высоким приоритетом.

![Пример уведомления низким приоритетом](local-notifications-images/24-lo-priority-drawer.png)

Поскольку уведомление «Размышления за день» уведомление низкого приоритета, Android будет выводится в Heads-up формате.


### <a name="visibility-settings"></a>Параметры видимости

Начиная с Android 5.0 *видимость* настройка доступна для управления объем содержимого уведомления появляются на безопасный экрана блокировки.
Xamarin.Android определяет следующие перечисления для задания видимости уведомления:

-   `NotificationVisibility.Public` &ndash; Полный текст уведомления отображается на безопасный экрана блокировки.

-   `NotificationVisibility.Private` &ndash; Только важные данные отображаются на безопасный экрана блокировки (например, значок и имя приложения, она отправлена), но остальная часть сведений об уведомлениях скрыты. По умолчанию все уведомления `NotificationVisibility.Private`.

-   `NotificationVisibility.Secret` &ndash; Ничего не выводится на безопасный экрана блокировки даже значок уведомления. Содержимое уведомлений доступен только в том случае, если пользователь снимает блокировку устройства.

Задание видимости уведомления, вызов приложения `SetVisibility` метод `Notification.Builder` объект, передаваемый в параметр видимости. Например, этот вызов `SetVisibility` делает уведомление `Private`:

```csharp
builder.SetVisibility (NotificationVisibility.Private);
```

Когда `Private` отправляется уведомление, только имя и значок приложения отображается на безопасный экрана блокировки. Вместо сообщения уведомления пользователь видит «Разблокировать устройство для просмотра этого уведомления»:

![Разблокировать устройство сообщения уведомления](local-notifications-images/25-lockscreen-private.png)

В этом примере **NotificationsLab** имя исходного приложения. Это исправленной версии уведомления отображается, только когда экрана блокировки безопасного (т. е., защищен с помощью ПИН-кода, шаблон или пароль) &ndash; Если экрана блокировки не является безопасным, полный текст уведомления доступен в экрана блокировки.


### <a name="category-settings"></a>Категория параметров

Начиная с Android 5.0, стандартные категории доступны для ранжирования и фильтрации уведомления. Xamarin.Android предоставляет следующие перечисления для следующих категорий:

-   `Notification.CategoryCall` &ndash; Телефонный звонок.

-   `Notification.CategoryMessage` &ndash; Входящее сообщение об ошибке.

-   `Notification.CategoryAlarm` &ndash; Сигнал условие или таймер срока действия.

-   `Notification.CategoryEmail` &ndash; Входящее сообщение электронной почты.

-   `Notification.CategoryEvent` &ndash; События календаря.

-   `Notification.CategoryPromo` &ndash; Рекламное сообщение или объявление.

-   `Notification.CategoryProgress` &ndash; Ход выполнения фоновой операции.

-   `Notification.CategorySocial` &ndash; Социальные сети обновления.

-   `Notification.CategoryError` &ndash; Сбой фонового процесса операции или проверки подлинности.

-   `Notification.CategoryTransport` &ndash; Обновление воспроизведения мультимедиа.

-   `Notification.CategorySystem` &ndash; Зарезервировано для системного использования (состояние системы или устройства).

-   `Notification.CategoryService` &ndash; Указывает, что фоновая служба запущена.

-   `Notification.CategoryRecommendation` &ndash; Сообщение рекомендаций, связанных в данный момент выполняется приложение.

-   `Notification.CategoryStatus` &ndash; Сведения об устройстве.

При сортировке уведомлений уведомления приоритет имеет приоритет над его параметр категории. Например, уведомление высокоприоритетных отображается как специальный даже если он принадлежит `Promo` категории. Чтобы задать категорию уведомления, следует вызвать `SetCategory` метод `Notification.Builder` объект, передаваемый в параметре категории. Пример:

```csharp
builder.SetCategory (Notification.CategoryCall);
```

*Не беспокоить* (новые возможности Android 5.0) функция фильтрует уведомления на основе категорий. Например *не беспокоить* экрана в **параметры** дает пользователю возможность освобождения уведомления для телефонные звонки и сообщения:

![Не беспокоить коммутаторы экрана](local-notifications-images/26-do-not-disturb.png)

Когда пользователь настраивает *не беспокоить* блокировать все прерывания, за исключением телефонные звонки (как показано в приведенном выше снимке экрана), Android позволяет уведомления с параметром категории `Notification.CategoryCall` должна быть предоставлена, пока устройство в *не беспокоить* режим. Обратите внимание, что `Notification.CategoryAlarm` уведомления никогда не блокируются *не беспокоить* режим.


<a name="compatibility" />

## <a name="compatibility"></a>Совместимость

При создании приложения, будут выполняться в более ранних версиях Android (как раньше API уровень 4), используется [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html) вместо класса `Notification.Builder`. При создании уведомлений с `NotificationCompat.Builder`, Android гарантирует правильного отображения содержимого основные уведомлений на старые устройства. Тем не менее поскольку некоторые возможности расширенного уведомления не доступны в предыдущих версиях Android, код должен явным образом обрабатывать проблемы совместимости для развернутой уведомления стили, категории и уровни видимости, как описано ниже.

Для использования `NotificationCompat.Builder` в приложении, необходимо включить [библиотеку поддержки Android версии 4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) NuGet в проекте.

В следующем образце кода показано, как создать с помощью уведомлений basic `NotificationCompat.Builder`:

```csharp
// Instantiate the builder and set notification elements:
NotificationCompat.Builder builder = new NotificationCompat.Builder (this)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();
```

Как показано в этом примере, вызовы методов для важных уведомлений параметры идентичны из `Notification.Builder`. Однако имеет код для обработки проблем обратную совместимость для более сложных уведомления (как описано в следующем разделе).

[LocalNotifications](https://developer.xamarin.com/samples/monodroid/LocalNotifications) образце демонстрируется использование `NotificationCompat.Builder` для запуска второго действия для уведомления. Этот пример кода описан в [с помощью локального уведомления в Xamarin.Android](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md) Пошаговое руководство.


### <a name="notification-styles"></a>Стили уведомления

Для создания *большой фрагмент текста*, *изображения*, или *папки "Входящие"* стиля уведомления с `NotificationCompat.Builder`, приложение должно использовать совместимости версий этих стилей. Например, чтобы использовать *большой фрагмент текста* стиля, создать экземпляр `NotificationCompat.BigTextstyle`:

```csharp
NotificationCompat.BigTextStyle textStyle = new NotificationCompat.BigTextStyle();

// Plug this style into the builder:
builder.SetStyle (textStyle);
```

Аналогичным образом приложение может использовать `NotificationCompat.InboxStyle` и `NotificationCompat.BigPictureStyle` для *папки "Входящие"* и *изображения* стили, соответственно.


### <a name="notification-priority-and-category"></a>Уведомления приоритета и категории

`NotificationCompat.Builder` поддерживает `SetPriority` метод (доступным, начиная с версии 4.1 платформы Android). Тем не менее `SetCategory` метод *не* поддерживаемые `NotificationCompat.Builder` поскольку категории являются частью новой системы метаданных уведомления, которая появилась в Android 5.0.

Для поддержки более старые версии Android, где `SetCategory` будет недоступен, ваш код может проверить уровень API во время выполнения для условного вызова `SetCategory` при уровне API равно или больше, чем Android 5.0 (уровень API 21):

```csharp
if ((int) Android.OS.Build.Version.SdkInt >= 21) {
    builder.SetCategory (Notification.CategoryEmail);
}
```

В этом примере приложения **требуемой версии .NET Framework** равно Android 5.0 и **Минимальная версия Android** равно **Android 4.1 (уровень API 16)**. Поскольку `SetCategory` является вызов доступны в уровень API 21 и более поздней версии, в этом примере кода `SetCategory` только когда он станет доступен &ndash; не вызовет `SetCategory` при уровне API меньше, чем
21.


### <a name="lockscreen-visibility"></a>Видимость экрана блокировки

Так как Android не поддерживает уведомления экрана блокировки до Android 5.0 (уровень API 21), `NotificationCompat.Builder` не поддерживает `SetVisibility` метод. Как было описано выше для `SetCategory`, ваш код может проверить уровень API во время выполнения и вызовите `SetVisiblity` только когда он станет доступен:

```csharp
if ((int) Android.OS.Build.Version.SdkInt >= 21) {
    builder.SetVisibility (Notification.Public);
}
```


## <a name="summary"></a>Сводка

В этой статье объясняется создание локального уведомления в Android. Оно описано составляющие уведомления, оно описано как использовать `Notification.Builder` для создания уведомлений, как уведомления стиля в крупных значков *большой фрагмент текста*, *изображения* и *папки "Входящие"*  форматы, как задать параметры метаданные, такие как приоритет, видимость и категории уведомлений и способ запуска действия для уведомления. В этой статье также описывается, как работают эти параметры уведомлений с планового выпуска нового экрана блокировки, и *не беспокоить* функций, появившихся в Android 5.0. Наконец, вы узнали, как использовать `NotificationCompat.Builder` для поддержки уведомлений совместимости с более ранними версиями Android.

Рекомендации по разработке уведомления для Android в разделе [уведомления](http://developer.android.com/preview/notifications.html).


## <a name="related-links"></a>Связанные ссылки

- [NotificationsLab (пример)](https://developer.xamarin.com/samples/monodroid/android5.0/NotificationsLab/)
- [LocalNotifications (пример)](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [Локальный уведомления в пошаговое руководство по Android](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md)
- [Уведомления](http://developer.android.com/design/patterns/notifications.html)
- [Уведомления пользователя](http://developer.android.com/training/notify-user/index.html)
- [уведомления](https://developer.xamarin.com/api/type/Android.App.Notification/)
- [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/)
- [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/)
