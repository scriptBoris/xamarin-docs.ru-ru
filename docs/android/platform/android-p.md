---
title: Предварительная версия Android P
description: Как приступить к работе с помощью Xamarin.Android для разработки приложений для последней версии Android.
ms.prod: xamarin
ms.assetid: 6575DD32-9DC8-44E6-85EF-1F8BD07D3780
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 07/27/2018
ms.openlocfilehash: a3eef6f2537a4b09f603787d7cdbf70a173fca80
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351951"
---
# <a name="android-p-preview"></a>Предварительная версия Android P

_Как приступить к работе с помощью Xamarin.Android для разработки приложений для последней версии Android._

![](~/media/shared/preview.png)

[Android Developer P Preview](https://developer.android.com/preview/) теперь доступна от Google. Ряд новых функций и API-интерфейсы доступны в этом выпуске, и многие из них необходимы, чтобы воспользоваться преимуществами новых возможностей оборудования в последних устройствах Android.

![Android изображение героя P](android-p-images/01-android-p-logo.png)

В этой статье структурирована, чтобы помочь вам приступить к разработке приложений Xamarin.Android для использования предварительной версии Android P. Здесь объясняется, как установить необходимые обновления и настройки пакета SDK Подготовка эмуляторе или на устройстве для тестирования. Также приводится обзор новых функций в Android P и примеры исходного кода, показано, как использовать некоторые из ключевых функций Android P.


## <a name="requirements"></a>Требования

Чтобы использовать функции P для Android в приложениях на базе Xamarin требуется следующее:

-   **Visual Studio** &ndash; Если вы используете Windows, требуется выделить 15,8 предварительной версии 5 или более поздней версии Visual Studio требуется версия.  Если вы используете компьютер Mac, текущая версия бета-версии Visual Studio для Mac или более поздней версии не требуется.

-   **Xamarin.Android** &ndash; Xamarin.Android 9.0.0.17 или более поздней версии, должны быть установлены с Visual Studio.

-   **Java Developer Kit** &ndash; требует разработки Xamarin Android 9.0 [JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) (или можно попробовать предварительную версию распространяемого корпорацией Майкрософт [OpenJDK](~/android/get-started/installation/openjdk.md)).

-   **Пакет SDK для Android** &ndash; API пакета SDK для Android 28 или более поздней версии необходимо установить через диспетчер пакетов SDK для Android.

## <a name="getting-started"></a>Начало работы

Чтобы начать работу с помощью P для Android с Xamarin.Android, необходимо скачать и установить новейшие средства и пакеты SDK, перед созданием первого проекта Android P.

1. Обновление для Visual Studio 15.8 предварительной версии 5 или более поздней версии. Если вы используете Visual Studio для Mac, перейдите в Visual Studio для Mac [бета-версии](https://docs.microsoft.com/visualstudio/mac/update) канала.

2. Установка **Android P (API 28)** или более поздней версии пакетов и средств и через диспетчер пакетов SDK.

3. Создание нового проекта Xamarin.Android излюбленного Android P (API 28).

4. Настройте эмулятор или устройство для тестирования приложений Android P.

Каждый из этих шагов описан в следующих разделах:


### <a name="update-visual-studio"></a>Обновление Visual Studio

Добавление поддержки P для Android для Visual Studio, обновление до версии Visual Studio 2017 15,8 предварительной версии 5 или более поздней версии, как описано в [обновление Visual Studio 2017 до последней версии](https://docs.microsoft.com/visualstudio/install/update-visual-studio).
Для добавления поддержки P для Android для Visual Studio для Mac, обновите до последней версии бета-версии Visual Studio 2017 для Mac как описано в [обновление Visual Studio для Mac](https://docs.microsoft.com/visualstudio/mac/update).


### <a name="install-the-android-sdk"></a>Установка пакета SDK для Android

Создание проекта с Xamarin.Android 9.0, сначала необходимо использовать диспетчер пакетов SDK Android для установки платформы SDK для **P Android (уровень API 28)** или более поздней версии.

1. Запустите диспетчер пакетов SDK. В Visual Studio щелкните **Сервис > Android > Диспетчер пакетов SDK Android**. В Visual Studio для Mac щелкните **Сервис > Диспетчер пакетов SDK**.

2. В правом нижнем углу щелкните значок шестеренки и выберите **репозитория > Google (не поддерживается)**:

    [![Настройка репозитория в Google](android-p-images/vs/set-repo-sml.png)](android-p-images/vs/set-repo.png#lightbox)

3. Установка **Android P** пакетов, которые отображаются в виде **Android SDK платформы 28** в **платформ** вкладку (Дополнительные сведения об использовании диспетчера пакетов SDK см. в разделе (Android SDK Setup)[~/android/get-started/installation/android-sdk.md]): 

    [![Установка пакетов Android P](android-p-images/vs/sdk-manager-sml.png)](android-p-images/vs/sdk-manager.png#lightbox)

4. Если вы используете эмулятор, Создание виртуального устройства, которое поддерживает **28 уровень API**. Дополнительные сведения о создании виртуальных устройств см. в разделе [управление виртуальными устройствами с помощью диспетчера устройств Android](~/android/get-started/installation/android-emulator/device-manager.md).



### <a name="start-a-xamarinandroid-project"></a>Запуск проекта Xamarin.Android

Создание нового проекта Xamarin.Android. Если вы не знакомы с разработки приложений Android с помощью Xamarin, см. в разделе [Привет, Android](~/android/get-started/hello-android/index.md) Дополнительные сведения о создании проектов Xamarin.Android.

При создании проекта Android, необходимо настроить параметры целевого P для Android, версии или более поздней версии. Например, для целевого объекта проекта для Android P, необходимо настроить уровень Android API целевого проекта, чтобы **Android P (API 28)**. Рекомендуется также задать уровень целевой платформы, до API 28 или более поздней версии. Дополнительные сведения о настройке уровней уровня Android API, см. в разделе [уровнях API Android понимание](~/android/app-fundamentals/android-api-levels.md).


### <a name="configure-a-device-or-emulator"></a>Настройте устройство или эмулятор

При использовании физического устройства, например пиксель, можно обновить устройство Android P Preview, следуя инструкциям в [бета-версии устройств Android P](https://developer.android.com/preview/devices/).

Если вы используете эмулятор, создания виртуального устройства для уровня API с помощью образа x86 28. Сведения об использовании диспетчер устройств Android для создания и управления виртуальными устройствами, см. в разделе [управление виртуальными устройствами с помощью диспетчера устройств Android](~/android/get-started/installation/android-emulator/device-manager.md).
Сведения об использовании эмулятора Android для тестирования и отладки, см. в разделе [отладки с помощью Google Android Emulator](~/android/deploy-test/debugging/android-sdk-emulator/index.md).



## <a name="new-features"></a>Новые функции

Android P дает ряд новых функций. Некоторые из этих новых функций предназначены для использования новых возможностей оборудования, предлагаемых последних устройствах Android, а другие разработаны для улучшенного взаимодействия с пользователем Android:

-   **Поддержка обрезки монитора** &ndash; предоставляет интерфейсы API, чтобы найти местоположение и форма элементов _обрезки_ в верхней части экрана на новых устройствах с Android.

-   **Улучшения уведомлений** &ndash; уведомляющих сообщений сейчас может отображать изображения, а новые `Person` класс используется для упрощения участников диалога.

-   **Внутренний размещения** &ndash; платформы поддержку протокола приемов-передач времени Wi-Fi, который позволяет приложениям использовать устройства Wi-Fi для навигации в помещениях.

-   **Поддержка нескольких камеры** &ndash; предлагает возможность доступа потоков одновременно из нескольких физических камеры (например, «dual передней и задней двумя камеры).


В следующих разделах, выделите эти функции и дать примерами кода, которые помогут приступить к работе с их использованием в приложении.

### <a name="display-cutout-support"></a>Поддержка отображения обрезки

У многих новых устройствах с Android с экранами вплотную друг к другу *отображения обрезки* (или «деление») в верхней части экрана для камеры и выступает с докладами.
Следующем снимке экрана приведен пример эмулятор обрезки.

[![Имитация обрезки эмулятор Android](android-p-images/02-example-cutout-sml.png)](android-p-images/02-example-cutout.png#lightbox)

Для управления отображения его содержимого на устройствах с выключателем отображения окна приложения, P для Android добавлен новый [LayoutInDisplayCutoutMode](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#layoutInDisplayCutoutMode) атрибут макета окна. Этот атрибут может быть присвоено одно из следующих значений:

-   [LayoutInDisplayCutoutModeNever](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_NEVER) &ndash; окна никогда не может перекрывать области обрезки.

-   [LayoutInDisplayCutoutModeShortEdges](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_SHORT_EDGES) &ndash; окна разрешена для расширения в область обрезки, но только в за края экрана. 

-   [LayoutInDisplayCutoutModeDefault](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_DEFAULT) &ndash; окна может быть расширен области обрезки, если область отсечения содержится в системной панели.

Например, чтобы предотвратить перекрытие с областью обрезки в окно приложения, задайте макет обрезки режим *никогда не*: 

```csharp
Window.Attributes.LayoutInDisplayCutoutMode =
    Android.Views.LayoutInDisplayCutoutMode.Never;
```

Ниже приведены примеры примеры этих режимов обрезки. Первый снимок экрана в левой части — приложения в режиме не в полноэкранном режиме. На снимке экрана center, приложение переходит на весь экран с `LayoutInDisplayCutoutMode` присвоено `LayoutInDisplayCutoutModeShortEdges`. Обратите внимание на то, что приложения белый фон расширяет в области обрезки отображения:

[![Пример обрезки режимы отображения в эмуляторе](android-p-images/03-cutout-modes-sml.png)](android-p-images/03-cutout-modes.png#lightbox)

На снимке экрана окончательного (выше в правой части), `LayoutInDisplayCutoutMode` присваивается `LayoutInDisplayCutoutModeShortNever` затем перейдет на весь экран.
Обратите внимание на то, что приложения белый фон не разрешено для расширения в области обрезки отображения.

Если требуется более подробные сведения об области обрезки на устройстве, можно использовать новый [DisplayCutout](https://developer.android.com/reference/android/view/DisplayCutout.html) класса. `DisplayCutout` Представляет область отображения, не может использоваться для отображения содержимого. Эти сведения можно использовать для получения местонахождении и форме область отсечения, таким образом, чтобы приложения не пытается отобразить содержимое в этой области не работает.

Дополнительные сведения о новых возможностях обрезки P для Android, см. в разделе [отображения обрезки поддержки](https://developer.android.com/preview/features#cutout).



### <a name="notifications-enhancements"></a>Улучшения уведомлений

Android P появились следующие усовершенствования для повышения удобства обмена сообщениями.

-   Каналы уведомления (представленные в [Android Oreo](~/android/platform/oreo.md)) теперь поддерживает блокирование группы канала.

-   Система уведомлений имеет три новых-Not-беспокоить категорий (назначение приоритетов оповещений, системные звуки и источников мультимедиа). Кроме того существует семь новых-Not-беспокоить режимов, которые могут использоваться для подавления visual перерывы в работе (например, значки, индикаторы уведомлений, внешний вид строки состояния и запуск действий в полноэкранном режиме).

-   Новый [Person](https://developer.android.com/reference/android/app/Person.html) добавлен класс для представления отправителя сообщения. Используйте данный класс помогает оптимизировать отрисовку каждого уведомления путем выявления пользователи, участвующие в диалоге (включая их аватары и URI).

-   Уведомления теперь можно отобразить изображения. 

Следующий пример иллюстрирует способ использовать новые интерфейсы API для создания уведомления, содержащий образ. На следующих снимках экрана текстового уведомления публикации и следуют уведомление внедренными изображениями. Когда уведомления будут развернуты (как показано в правой части), отображается текст первого уведомления и внедренные изображения в увеличении второе уведомление:

[![Пример уведомления об с изображением](android-p-images/04-example-notifications-sml.png)](android-p-images/04-example-notifications.png#lightbox)

Приведенный ниже показано, как включить изображения в уведомлении P для Android, а также демонстрирует использование нового `Person` класса:

1. Создание `Person` объект, представляющий отправителя. Например, имя и значок отправителя, включенных в `fromPerson`:

    ```csharp
    Icon senderIcon = Icon.CreateWithResource(this, Resource.Drawable.sender_icon);
    Person fromPerson = new Person.Builder()
        .SetIcon(senderIcon)
        .SetName("Mark Sender")
        .Build();
    ```

2. Создание `Notification.MessagingStyle.Message` , содержащий изображение для отправки, передача образа в новый [Notification.MessagingStyle.Message.SetData](https://developer.android.com/reference/android/app/Notification.MessagingStyle.Message.html#setData%28java.lang.String,%20android.net.Uri) метод.
   Пример:

    ```csharp
    Uri imageUri = Uri.Parse("android.resource://com.xamarin.pminidemo/drawable/example_image");
    Notification.MessagingStyle.Message message = new Notification.MessagingStyle
            .Message("Here's a picture of where I'm currently standing", 0, fromPerson)
            .SetData("image/", imageUri);
    ```

3. Добавить сообщение `Notification.MessagingStyle` объекта. Пример:

    ```csharp
    Notification.MessagingStyle style = new Notification.MessagingStyle(fromPerson)
            .AddMessage(message);
    ```

4. Подключите этот стиль построителя уведомлений. Пример:

    ```csharp
    builder = new Notification.Builder(this, MY_CHANNEL)
        .SetContentTitle("Tour of the Colosseum")
        .SetContentText("I'm standing right here!")
        .SetSmallIcon(Resource.Mipmap.ic_notification)
        .SetStyle(style)
        .SetChannelId(MY_CHANNEL);
    ```

5. Опубликуйте уведомление. Пример:

    ```csharp
    const int notificationId = 1000;
    notificationManager.Notify(notificationId, builder.Build());
    ```

Дополнительные сведения о создании уведомлений см. в разделе [локальных уведомлений](~/android/app-fundamentals/notifications/local-notifications.md).


### <a name="indoor-positioning"></a>Внутренний позиционирования

P для Android обеспечивает поддержку для IEEE 802.11mc (также известный как _Wi-Fi приемов-передач времени_ или _Wi-Fi времени приема-Передачи_), который позволяет обнаружить расстояние к одному или точек доступа Wi-Fi. Используя эту информацию, возможна ситуация для вашего приложения, чтобы воспользоваться преимуществами *внутренний позиционирования* с точностью до 1 – 2 метров. На устройствах Android, которые обеспечивают поддержку оборудования для IEEE 801.11mc приложение может предложить возможностях навигации, например элемент управления на основе расположения смарт-устройств или включить в свою очередь инструкции с помощью хранилища:

[![Пример внутри помещений навигацию, с помощью Wi-Fi времени приема-Передачи](android-p-images/05-wifi-rtt-sml.png)](android-p-images/05-wifi-rtt.png#lightbox)

Новый [WifiRttManager](https://developer.android.com/reference/android/net/wifi/rtt/WifiRttManager) класс и несколько вспомогательных классов предоставляет средства для измерения расстояния для Wi-Fi устройства. Дополнительные сведения о внутренний API размещения, появившихся в P для Android, см. в разделе [Android.Net.Wifi.Rtt](https://developer.android.com/reference/android/net/wifi/rtt/package-summary).


### <a name="multi-camera-support"></a>Поддержка нескольких камеры

Множество новых устройствах с Android имеют dual-front и/или двойной обратной камеры, которые можно использовать для таких компонентов, как стерео зрением, улучшенные визуальные эффекты и масштаб улучшенные возможности. Android P вводится новый [несколькими камеры](https://developer.android.com/preview/features#camera) API, который позволяет приложению использовать *логических камеры* (или *логических несколькими камеры*), поддерживаемый двух или более Физический камеры.
Чтобы определить, если устройство поддерживает логические с несколькими веб-камеры, вы можете изучить возможности каждой камеры на устройстве, чтобы узнать, поддерживает ли [RequestAvailableCapabilitiesLogicalMultiCamera](https://developer.android.com/reference/android/hardware/camera2/CameraMetadata#REQUEST_AVAILABLE_CAPABILITIES_LOGICAL_MULTI_CAMERA).

Android P также включает новый [Конфигурациясеанса](https://developer.android.com/reference/android/hardware/camera2/params/SessionConfiguration.html) класс, который может использоваться способствует сокращению задержки во время первоначальной записи и полностью устранить необходимость запустить и поток камеры.

Дополнительные сведения о нескольких камеры поддержки в P для Android, см. в разделе [несколькими камеры поддержки и камеры обновления](https://developer.android.com/preview/features#camera).


### <a name="other-features"></a>Другие возможности

Кроме того P для Android поддерживает несколько новых возможностей:

-   Новый [AnimatedImageDrawable](https://developer.android.com/reference/android/graphics/drawable/AnimatedImageDrawable.html) класс, который может использоваться для рисования и отображение анимированных рисунков.

-   Новый [ImageDecoder](https://developer.android.com/reference/android/graphics/ImageDecoder.html) класс, который заменяет `BitmapFactory`. `ImageDecoder` можно использовать для декодирования `AnimatedImageDrawable`.

-   Поддержка видео HDR (High динамического диапазона) и HEIF (высокая эффективность формат файлов изображений) изображения.

-   [JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html) была улучшена для более интеллектуальной обработки задания, относящиеся к сети. Новый [GetNetwork](https://developer.android.com/reference/android/app/job/JobParameters#getNetwork%28%29) метод [JobParameters](https://developer.android.com/reference/android/app/job/JobParameters) класс возвращает лучших сетевых для выполнения любой сетевых запросов для данного задания.

Дополнительные сведения о новых функциях P для Android см. в разделе [Android P функции и API](https://developer.android.com/preview/features).


## <a name="behavior-changes"></a>Изменения в поведении

Если целевая версия Android установлен уровень API 28, существует несколько изменений платформы, которые могут повлиять на поведение приложения, даже если вы не реализуете новые возможности, описанные выше. Ниже приведен краткий обзор этих изменений.

-  Приложения теперь необходимо запросить разрешение на переднем плане перед использованием службы переднего плана.

-  Если приложение имеет более одного процесса, не могут совместно использовать один [WebView](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) каталог данных между процессами.

-  Прямой доступ к другим приложением данные каталога по пути больше не допускается.

Дополнительные сведения об изменениях в поведении для приложений, предназначенных для Android P см. в разделе [изменения в поведении](https://developer.android.com/preview/behavior-changes.html#p-apps).


## <a name="sample-code"></a>Пример кода

[AndroidPMiniDemo](https://github.com/xamarin/monodroid-samples/tree/master/android-p/AndroidPMiniDemo) — пример приложения Xamarin.Android, для Android P, как задать обрезки режимы отображения, как использовать новый `Person` , а также для отправки уведомлений, включающий изображение.


## <a name="summary"></a>Сводка

В этой статье представлены Android P предварительной версии и было рассмотрено, как установить и настроить новейшие средства и пакеты для разработки приложений Xamarin.Android с предварительной версией Android P. Общие сведения о ключевых функциях, доступных в Android P предоставлен примере исходного кода для нескольких из этих функций. Он включается ссылки на документацию по API и разделы разработчика Android, которые помогут приступить к созданию приложений для Android P. Кроме того, он выделен наиболее важные изменения поведения P для Android, которые могут повлиять на существующие приложения.


## <a name="related-links"></a>Связанные ссылки

- [P для Android Developer Preview](https://developer.android.com/preview/)
