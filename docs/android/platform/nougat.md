---
title: Функции nougat
description: Как приступить к работе с помощью Xamarin.Android для разработки приложений для Android Nougat.
ms.prod: xamarin
ms.assetid: 5C74ABE2-C862-4ED0-8EA5-C7FEE5251D4B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/02/2018
ms.openlocfilehash: bf2842679aded21c9677d9cef31910f273518acd
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119180"
---
# <a name="nougat-features"></a>Функции nougat

_Как приступить к работе с помощью Xamarin.Android для разработки приложений для Android Nougat._

Эта статья содержит обзор функций, реализованных в Android Nougat объясняется способ подготовки для разработки приложений Android Nougat Xamarin.Android и ссылки на примеры приложений, демонстрирующие способы использования возможностей Android Nougat в Приложений Xamarin.Android.


## <a name="overview"></a>Обзор

[Android Nougat](https://developer.android.com/about/versions/nougat/android-7.0.html) является последующего действия Google для Android 6.0 Marshmallow. Xamarin.Android предоставляет поддержку для **Android 7.x привязки** в Xamarin Android 7.0 и более поздних версий. Android Nougat добавляет много новых интерфейсов API для Nougat функций, описанных ниже; Эти API доступны для приложений Xamarin.Android при использовании Xamarin.Android 7.0.

[![Образы герой планшеты и телефоны под управлением Android Nougat Android](nougat-images/android-n-hero-sml.png)](nougat-images/android-n-hero.png#lightbox)

Дополнительные сведения о Android 7.x API-интерфейсов, см. в разделе [Android 7.1 для разработчиков](http://developer.android.com/preview/api-overview.html).
Список известных проблем Xamarin.Android 7.0, см. в разделе [заметки о выпуске](https://developer.xamarin.com/releases/android/xamarin.android_7/xamarin.android_7.0/).

Android Nougat предоставляет много новых функций интерес для разработчиков Xamarin.Android. Эти функции включают перечисленные ниже.

-   **Поддержка нескольких окон** &ndash; это улучшение позволяет пользователям открывать два приложения на экране за один раз.

-   **Улучшения уведомлений** &ndash; включает модернизированный уведомления системы в Android Nougat *прямого ответа* функция, которая позволяет пользователю быстро реагировать на текстовые сообщения непосредственно из уведомления ПОЛЬЗОВАТЕЛЬСКИЙ ИНТЕРФЕЙС. Кроме того, если ваше приложение создает уведомления для полученных сообщений, новый *объединенными уведомления* функцию можно объединить уведомления как одну группу при получении более одного сообщения.

-   **Заставка данных** &ndash; эта функция — это новая служба системы, который позволяет снизить использование данных по сотовой сети в приложениях; он позволяет контролировать использование передачи данных приложения.

Кроме того, предоставляет множество других улучшений в Android Nougat интерес для разработчиков приложений, таких как новая функция конфигурации безопасности сети, Doze в дороге, ключевые улучшения WebView аттестации, новые быстрые параметры API-интерфейсы, поддержка нескольких языкового стандарта, ICU4J API-интерфейсы, доступ к функциям языка Java 8, доступ к каталогу с заданной областью, API пользовательских указатель, поддержка платформы виртуальной Реальности, виртуальные файлы и фоновой обработки оптимизации.

В этой статье объясняется, как приступить к созданию приложений с помощью Android Nougat опробовать новые функции и планировании миграции или функции работы для платформы Android Nougat.


## <a name="requirements"></a>Требования

Для использования новых функций Android Nougat в приложениях на базе Xamarin требуется следующее:

-   **Visual Studio или Visual Studio для Mac** &ndash; Если вы используете Visual Studio версии 4.2.0.628 или более поздней версии набора средств Visual Studio для Xamarin является обязательным. При использовании Visual Studio для Mac версии 6.1.0 или более поздней версией Visual Studio для Mac является обязательным.

-   **Xamarin.Android** &ndash; Xamarin.Android версии 7.0 или более поздней версии необходимо установить и настроить Visual Studio или Visual Studio для Mac.

-   **Пакет SDK для Android** -Android 7.0 SDK (API 24) или более поздней версии необходимо установить через диспетчер пакетов SDK для Android.

-   **Java Developer Kit** &ndash; требует разработки Xamarin Android 7.0 [JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) или более поздней версии, при разработке для API уровня 24 или более поздней версии (JDK 8 также поддерживает уровни API ниже 24). 64-разрядной версии JDK 8 является обязательным, если вы используете пользовательские элементы управления или средство предварительного просмотра формы.

> [!IMPORTANT]
> Xamarin.Android не поддерживает пакет JDK 9.

Обратите внимание на то, что приложения должны быть перестроены Xamarin C6SR4 или более поздней версии для надежной работы с Android Nougat. Так как Android Nougat можно связать только с [собственных библиотек, предоставляемых системой NDK](https://developer.android.com/about/versions/nougat/android-7.0-changes.html), существующие приложения, такие как с помощью библиотек **Mono.Data.Sqlite.dll** может аварийно завершить работу, если при использовании Android Nougat они не правильно Перестроить.



## <a name="getting-started"></a>Начало работы

Чтобы начать работу с помощью Android Nougat с помощью Xamarin.Android, необходимо загрузить и установить новейшие средства и пакеты SDK, перед созданием проекта Android Nougat:

1.  Установите последние обновления Xamarin.Android из Xamarin.

2.  Установка **Android 7.0 (API 24)** пакеты и средства или более поздней версии.

3.  Создание нового проекта Xamarin.Android излюбленного Android Nougat.

4.  Настройка для Android Nougat эмуляторе или на устройстве.

Каждый из этих шагов описан в следующих разделах:


### <a name="install-xamarin-updates"></a>Установка обновлений для Xamarin

Чтобы добавить поддержку Xamarin для Android Nougat, измените канал обновлений в Visual Studio или Visual Studio для Mac на стабильный канал и примените последние обновления. Если требуется также функции, которые в настоящее время доступны только в канал альфа или бета-версии, можно перевести канал альфа или бета-версии (альфа- и бета-версии каналы также предоставляют поддержку для Android 7.x). Сведения об изменении канала обновления (выпуски), см. в разделе [изменение канала обновления](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel).



### <a name="install-the-android-sdk"></a>Установка пакета SDK для Android

Создание проекта с помощью Xamarin Android 7.0, необходимо сначала использовать диспетчер пакетов SDK Android для установки **пакета SDK для платформы Android N (API 24)** или более поздней версии. Также необходимо установить последнюю версию **Android SDK Tools**:

1.  Запустить диспетчер пакетов SDK для Android (в Visual Studio для Mac, используйте **средства > открыть диспетчер пакетов SDK Android&hellip;**; в Visual Studio, используют **средства > Android > Диспетчер пакетов SDK Android**).

2.  Установка **Android 7.0 (API 24)** или более поздней версии:

    [![Выбор пакетов Android 7.0, диспетчер пакетов SDK для Android](nougat-images/preview-packages.png)](nougat-images/preview-packages.png#lightbox)

3.  Установите последнюю версию инструментов пакета SDK для Android:

    [![Выбрав новейшие средства пакета SDK для Android, диспетчер пакетов SDK для Android](nougat-images/preview-tools.png)](nougat-images/preview-tools.png#lightbox)

    Необходимо установить Android SDK Tools версии 25.2.2 или более поздней версии, Android SDK Platform tools 24.0.3 или более поздней версии и Android Средства сборки пакета SDK 24.0.2 или более поздней версии.

4.  Убедитесь, что **расположение пакета Java Development Kit** настроен для JDK 1.8:

    [![Настройка пути JDK 8 в разделе Сервис-Параметры](nougat-images/use-jdk-1.8.png)](nougat-images/use-jdk-1.8.png#lightbox)

    Чтобы просмотреть этот параметр в Visual Studio, нажмите кнопку **Сервис > Параметры > Xamarin > Параметры Android**. В Visual Studio для Mac щелкните **параметры > проекты > расположения SDK > Android**.



### <a name="start-a-xamarinandroid-project"></a>Запуск проекта Xamarin.Android

Создание нового проекта Xamarin.Android. Если вы не знакомы с разработки приложений Android с помощью Xamarin, см. в разделе [Привет, Android](~/android/get-started/hello-android/index.md) Дополнительные сведения о создании проектов Xamarin.Android.

При создании проекта Android, необходимо настроить параметры целевой объект Android 7.0 или более поздней версии. Например, для проекта Android 7.0, необходимо настроить уровень Android API целевого проекта, чтобы **Android 7.0 (API 24 - Nougat)**. Рекомендуется установить уровень target framework API 24 часа или более поздней версии. Дополнительные сведения о настройке уровней уровня Android API, см. в разделе [уровнях API Android понимание](~/android/app-fundamentals/android-api-levels.md).


> [!NOTE]
> В настоящее время необходимо задать **минимум Android версии** для **Android 7.0 (API 24 - Nougat)** для развертывания приложения для Android Nougat устройствах или эмуляторах.



### <a name="configure-an-emulator-or-device"></a>Настройка эмулятора или устройства

Если вы используете эмулятор, запустите диспетчер Android AVD и создайте новое устройство, используя следующие параметры:

-   Устройство: Nexus 5 X Nexus 6, Nexus 6P, проигрыватель Nexus Nexus 9 или пиксель C.
-   Целевой объект: Android 7.0 - API уровня 24
-   Интерфейс ABI: x86 или x86\_64

Например это виртуальное устройство настраивается для эмуляции Nexus 6:

[![Настройка с помощью устройства Nexus 6, целевой объект Android 7.0 и Intel Atom x86 ЦП/ABI виртуальное устройство AVD](nougat-images/android-n-avd.png)](nougat-images/android-n-avd.png#lightbox)

При использовании физического устройства, например Nexus 5 X 6 и 9, вы можете обновить устройство с помощью автоматического над обновлениями режиме (OTA) или загрузить образ системы и flash устройства напрямую. Дополнительные сведения о обновление устройства Android Nougat вручную, см. в разделе [OTA изображений для устройств Nexus](https://developers.google.com/android/nexus/ota).

Обратите внимание на то, что устройства Nexus 5 не поддерживаются Android Nougat.



## <a name="new-features"></a>Новые функции

Android Nougat дает ряд новых функций и возможностей, таких как поддержка нескольких окон, усовершенствования уведомления и заставки данных. В следующих разделах, выделите эти функции и дать ссылки, которые помогут вам приступить к работе с их использованием в приложении.



### <a name="multi-window-mode"></a>Многооконный режим

Многооконный режим позволяет пользователям за один раз открыть два приложения с поддержкой полной многозадачность. Эти приложения можно запускать side-by-side (альбомная) или один над-в две (книжная) в режиме с разделенным экраном.
Пользователи могут перетащить разделитель между приложениями для изменения их размера, и их можно вырезать и вставить содержимое между приложениями. Когда два приложения будут представлены в многооконный режим, выбранное действие продолжает выполняться, пока невыделенного действия приостановлена, но по-прежнему отображается. Многооконный режим не изменяет их Android жизненный цикл.

[![Пример приложения, работающие в режиме многооконный в книжной и альбомной](nougat-images/multi-window-mode.png)](nougat-images/multi-window-mode.png#lightbox)

Вы можете настроить, как сведения о действиях приложения Xamarin.Android поддерживают многооконный режим. Например можно настроить атрибуты, которые заданы в режиме многооконный минимальный размер и по умолчанию высоту и ширину приложения. Вы можете использовать новый `Activity.IsInMultiWindowMode` свойство, чтобы определить, является ли действие в многооконный режим. Пример:

```csharp
if (!IsInMultiWindowMode) {
    multiDisabledMessage.Visibility = ViewStates.Visible;
} else {
    multiDisabledMessage.Visibility = ViewStates.Gone;
}
```

[MultiWindowPlayground](https://developer.xamarin.com/samples/monodroid/android-n/MultiWindowPlayground/) пример приложения включает в себя код C#, который демонстрирует, как пользоваться преимуществами многооконный пользовательские интерфейсы с вашим приложением.

Дополнительные сведения о многооконный режим, см. в разделе [поддержка нескольких окон](https://developer.android.com/guide/topics/ui/multi-window.html).



### <a name="enhanced-notifications"></a>Улучшенные уведомления

Android Nougat вводит система модернизированный уведомлений. Она поддерживает новый компонент прямого ответа, который позволяет пользователям быстро ответить уведомления для входящих сообщений текст непосредственно в уведомлении пользовательского интерфейса. Начиная с Android 7.0, уведомления, что сообщения могут объединяться друг с другом как одну группу при получении более одного сообщения. Кроме того разработчики могут настраивать уведомления представлений, использовать системы оформление в уведомлениях и воспользоваться преимуществами новых шаблонов уведомлений, при создании уведомлений.


#### <a name="direct-reply"></a>Прямого ответа

Когда пользователь получает уведомление для входящего сообщения, Android Nougat позволяет ответить на сообщение в уведомлении (а не открытии приложения обмена сообщениями для отправки ответа).
Встроенный ответа благодаря этой функции пользователи могут быстро реагировать на сообщение SMS или текст непосредственно в интерфейс уведомлений:

[![Снимок экрана: уведомление с полем встроенный прямого ответа](nougat-images/notifications-inline-reply-sml.png)](nougat-images/notifications-inline-reply.png#lightbox)

Для поддержки этой возможности в приложении, необходимо добавить *встроенные действия ответа* к приложению через [RemoteInput](https://developer.xamarin.com/api/type/Android.App.RemoteInput/) таким образом, пользователи могут отправлять ответы через текст непосредственно из уведомления о пользовательском Интерфейсе.
Например, в следующем коде сборок `RemoteInput` для получения ввода текста, сборок, ожидающие намерения для действия ответа и создает удаленного действия ввода включена:

```csharp
// Build a RemoteInput for receiving text input:
var remoteInput = new Android.Support.V4.App.RemoteInput.Builder (EXTRA_REMOTE_REPLY)
    .SetLabel (GetString (Resource.String.reply))
    .Build ();

// Build a Pending Intent for the reply action to trigger:
PendingIntent replyIntent = PendingIntent.GetBroadcast (ApplicationContext,
                                conversation.ConversationId,
                                GetMessageReplyIntent (conversation.ConversationId),
                                PendingIntentFlags.UpdateCurrent);

// Build an Android 7.0 compatible Remote Input enabled action:
NotificationCompat.Action actionReplyByRemoteInput = new NotificationCompat.Action.Builder (
    Resource.Drawable.notification_icon,
    GetString (Resource.String.reply),
    replyIntent).AddRemoteInput (remoteInput).Build ();
```

Это действие добавляется уведомление:

```csharp
// Create the notification:
NotificationCompat.Builder builder = new NotificationCompat.Builder (ApplicationContext)
   .SetSmallIcon (Resource.Drawable.notification_icon)
   ...
   .AddAction (actionReplyByRemoteInput);
```

[Службе сообщений](https://developer.xamarin.com/samples/monodroid/android-n/MessagingService/) пример приложения включает в себя код C#, который демонстрирует расширение уведомлений с помощью `RemoteInput` объекта. Дополнительные сведения о добавлении ответа встроенных действий для приложения для Android 7.0 или более поздней версии, см. в разделе Android [ответе уведомления](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#direct) раздела.


#### <a name="bundled-notifications"></a>Распространение уведомлений

Android Nougat можно сгруппировать сообщения уведомления (например, по теме сообщения) и отображать группы, а не каждого отдельного сообщения.
Это *объединенными уведомления* благодаря функции пользователи могут отклонить или архивировать группу уведомлений в одно действие. Можно сдвинуть вниз для развертывания пакета уведомлений, чтобы просмотреть подробные сведения о каждом уведомлении:

[![Снимок экрана пример распространение уведомлений](nougat-images/bundled-notifications-sml.png)](nougat-images/bundled-notifications.png#lightbox)

Для поддержки распространение уведомлений, приложение может использовать [Builder.SetGroup](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetGroup/p/System.String/) метод, чтобы объединить похожие уведомления. Дополнительные сведения о группах распространение уведомлений в Android N см. в разделе Android [уведомления о том, как объединить](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#bundle) раздела.


#### <a name="custom-views"></a>Пользовательские представления

Android Nougat позволяет создать настраиваемое уведомление представления с помощью заголовков уведомлений системы, действия и расширяемый макеты. Дополнительные сведения о представлениях настраиваемого уведомления в Android Nougat см. в разделе Android [улучшения уведомлений](https://developer.android.com/about/versions/nougat/android-7.0.html#notification_enhancements) раздела.



### <a name="data-saver"></a>Заставка данных

Начиная с Android Nougat, пользователи могут включить новый *заставки данных* задание, что блоки фоновых данных об использовании. Этот параметр также сигнализирует о приложение для использования меньшего объема данных на переднем плане, везде, где это возможно. [ConnectivityManager](https://developer.xamarin.com/api/type/Android.Net.ConnectivityManager/) была расширена в Android Nougat, таким образом, чтобы приложение можно проверить, разрешен ли заставки данных для пользователя, таким образом, приложение может отправить для того, чтобы ограничить его использование данных, при включении экранной заставки данных.

Дополнительные сведения о новой функции данных заставки в Android Nougat см. в разделе Android [оптимизации использования сети данных](https://developer.android.com/training/basics/network-ops/data-saver.html) раздела.



### <a name="app-shortcuts"></a>Ярлыки приложения

Android 7.1 появился *ярлыками приложений* функция, которая позволяет пользователям быстро общих или рекомендуемые задачи запуска с вашим приложением.
Чтобы активировать меню ярлыков, пользователь долго нажатия значка приложения для второго и &ndash; меню отображается с помощью быстрого вибрации.
Освобождение прессе приводит к меню, чтобы оставаться:

[![Пример окна контекстное меню приложения для обмена сообщениями приложения](nougat-images/app-shortcuts-sml.png)](nougat-images/app-shortcuts.png#lightbox)

Эта функция доступна доступен только уровень API 25 или более поздней версии.
Дополнительные сведения о новых ярлыками приложений в Android 7.1 см. в разделе Android [ярлыками приложений](https://developer.android.com/guide/topics/ui/shortcuts.html) раздела.


### <a name="sample-code"></a>Пример кода

Чтобы показать, как воспользоваться преимуществами функций Android Nougat доступны несколько примеров Xamarin.Android:

-   [MultiWindowPlayground](https://developer.xamarin.com/samples/monodroid/android-n/MultiWindowPlayground/) демонстрирует использование API многооконный, доступных в Android Nougat. Пример приложения можно переключиться в режим несколькими windows, чтобы увидеть, как это влияет на жизненный цикл и поведение приложения.

-   [Служба обмена сообщениями](https://developer.xamarin.com/samples/monodroid/android-n/MessagingService/) использует простую службу, которая отправляет уведомления `NotificationCompatManager`. Он также расширяет уведомления с помощью `RemoteInput` объекта, чтобы разрешить устройствам Android Nougat ответ через текст непосредственно от уведомления без необходимости открывать приложение.

-   [Активные уведомления](https://developer.xamarin.com/samples/monodroid/android-n/ActiveNotifications/) демонстрирует использование `NotificationManager` API, чтобы определить, сколько уведомлений приложения в данный момент отображается.

-   [Доступ к каталогу с заданной областью](https://developer.xamarin.com/samples/monodroid/android-n/ScopedDirectoryAccess/) демонстрирует использование API доступа к области каталога для удобного доступа к определенные каталоги. Служит в качестве альтернативы не нужно задавать `READ_EXTERNAL_STORAGE` или `WRITE_EXTERNAL_STORAGE` разрешения в манифесте.

-   [Прямой загрузки](https://developer.xamarin.com/samples/monodroid/android-n/DirectBoot/) показано, как хранение данных в хранилище устройство зашифровано, который всегда доступен хотя устройства загружается как до и после ввода credentials(PIN/Pattern/Password) любого пользователя.


## <a name="summary"></a>Сводка

В этой статье представлены Android Nougat и было рассмотрено, как установить и настроить на Android Nougat новейшие средства и пакеты для разработки приложений Xamarin.Android. Он также ознакомились с обзором ключевых функциях, доступных в Android Nougat со ссылками на примере исходного кода, которые помогут приступить к созданию приложений для Android Nougat.


## <a name="related-links"></a>Связанные ссылки

- [Android 7.1 для разработчиков](https://developer.android.com/about/versions/nougat/android-7.1.html)
- [Заметки о выпуске Xamarin Android 7.0](https://developer.xamarin.com/releases/android/xamarin.android_7/xamarin.android_7.0/)
