---
title: Android 9 Pie
description: Как приступить к разработке приложений для Android круговой 9, с помощью Xamarin.Android.
ms.prod: xamarin
ms.assetid: 6575DD32-9DC8-44E6-85EF-1F8BD07D3780
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2018
ms.openlocfilehash: cd1c374fa68420e1923ef4dee0bb37a4665f3535
ms.sourcegitcommit: 215cad17324ba3fbc23487ce66cd4e1cc74eb879
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52710027"
---
# <a name="android-pie-features"></a>Android круговой функции

_Как приступить к разработке приложений для Android круговой 9, с помощью Xamarin.Android._

[Android 9 круговой](https://developer.android.com/about/versions/pie/) теперь доступна от Google. Ряд новых функций и API-интерфейсы доступны в этом выпуске, и многие из них необходимы, чтобы воспользоваться преимуществами новых возможностей оборудования в последних устройствах Android.

![Android изображение героя круговая](pie-images/01-android-p-logo.png)

Чтобы помочь вам приступить к разработке приложений Xamarin.Android для Android круговой структурируются в этой статье. Здесь объясняется, как установить необходимые обновления и настройки пакета SDK Подготовка эмуляторе или на устройстве для тестирования. Также приводится обзор новых функций в Android круговой и примеры исходного кода, показано, как использовать некоторые из ключевых функций Android круговой.

Xamarin.Android 9.0 обеспечивает поддержку Android круга. Дополнительные сведения о поддержке Xamarin.Android Android круговой см. в разделе [Android P Developer Preview 3](https://developer.xamarin.com/releases/android/xamarin.android_9/xamarin.android_9.0/#android-p-dp1) заметки о выпуске.

## <a name="requirements"></a>Требования

Следующий список должен использовать компоненты Android круговой в приложениях на базе Xamarin:

-   **Visual Studio** &ndash; Если вы используете Windows, обновление до Visual Studio 2017 версии 15,8 или более поздней версии. Если вы используете компьютер Mac, обновление до Visual Studio 2017 для Mac равен 7,6 или более поздней версии.

-   **Xamarin.Android** &ndash; Xamarin.Android 9.0.0.17 или более поздней версии, должны быть установлены с Visual Studio (Xamarin.Android автоматически устанавливается как часть **разработки мобильных приложений на .NET** рабочей нагрузки).

-   **Java Developer Kit** &ndash; требует разработки Xamarin Android 9.0 [JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) (или можно попробовать предварительную версию распространяемого корпорацией Майкрософт [OpenJDK](~/android/get-started/installation/openjdk.md)). JDK8 автоматически устанавливается как часть **разработки мобильных приложений на .NET** рабочей нагрузки.

-   **Пакет SDK для Android** &ndash; API пакета SDK для Android 28 или более поздней версии необходимо установить через диспетчер пакетов SDK для Android.

## <a name="getting-started"></a>Начало работы

Чтобы приступить к разработке приложений Android круговой с помощью Xamarin.Android, необходимо скачать и установить новейшие средства и пакеты SDK, перед созданием первого проекта Android круговой.

1. Обновление до [Visual Studio 2017 версии 15.8](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes) или более поздней версии. Если вы используете Visual Studio для Mac, обновлений [Visual Studio 2017 для Mac версии 7.6](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes) или более поздней версии.

2. Установка **круговой Android (API 28)** пакеты и средства через диспетчер пакетов SDK.

3. Создайте новый проект Xamarin.Android излюбленного **Android 9.0**.

4. Настройте эмулятор или устройство для тестирования приложений Android круговой.

Каждый из этих шагов описан в следующих разделах:


### <a name="update-visual-studio"></a>Обновление Visual Studio

Чтобы добавить поддержку круговой Android для Visual Studio, обновление до Visual Studio 2017 версии 15,8 или более поздней версии (инструкции см. в разделе [обновление Visual Studio 2017 до последней версии](https://docs.microsoft.com/visualstudio/install/update-visual-studio)).

Чтобы добавить поддержку круговой Android для Visual Studio для Mac, обновление до Visual Studio 2017 для Mac 7.6 или более поздней версии (инструкции см. в разделе [Установка и настройка Visual Studio для Mac](https://docs.microsoft.com/visualstudio/mac/installation)).


### <a name="install-the-android-sdk"></a>Установка пакета SDK для Android

Создание проекта с Xamarin.Android 9.0, сначала необходимо использовать диспетчер пакетов SDK Android для установки платформы SDK для **круговой Android (уровень API 28)** или более поздней версии.

1. Запустите диспетчер пакетов SDK. В Visual Studio щелкните **Сервис > Android > Диспетчер пакетов SDK Android**. В Visual Studio для Mac щелкните **Сервис > Диспетчер пакетов SDK**.

2. В правом нижнем углу щелкните значок шестеренки и выберите **репозитория > Google (не поддерживается)**:

    [![Настройка репозитория в Google](pie-images/vs/set-repo-sml.png)](pie-images/vs/set-repo.png#lightbox)

3. Установка **Android круговой** пакетов SDK, которые указаны в виде **Android SDK платформы 28** в **платформ** вкладке (Дополнительные сведения об использовании диспетчера пакетов SDK см. в разделе [ Программа установки Android SDK](~/android/get-started/installation/android-sdk.md)):

    [![Установка пакетов Android круговая](pie-images/vs/sdk-manager-sml.png)](pie-images/vs/sdk-manager.png#lightbox)

4. Если вы используете эмулятор, Создание виртуального устройства, которое поддерживает **28 уровень API**. Дополнительные сведения о создании виртуальных устройств см. в разделе [управление виртуальными устройствами с помощью диспетчера устройств Android](~/android/get-started/installation/android-emulator/device-manager.md).



### <a name="start-a-xamarinandroid-project"></a>Запуск проекта Xamarin.Android

Создание нового проекта Xamarin.Android. Если вы не знакомы с разработки приложений Android с помощью Xamarin, см. в разделе [Привет, Android](~/android/get-started/hello-android/index.md) Дополнительные сведения о создании проектов Xamarin.Android.

При создании проекта Android, необходимо настроить параметры целевой объект Android 9.0 или более поздней версии. Например, для проекта Android круга, необходимо настроить уровень Android API целевого проекта, чтобы **Android 9.0** (API 28). Рекомендуется также задать целевой платформой уровня API 28 или более поздней версии. Дополнительные сведения о настройке уровней Android API, см. в разделе [уровнях API Android понимание](~/android/app-fundamentals/android-api-levels.md).


### <a name="configure-a-device-or-emulator"></a>Настройте устройство или эмулятор

При использовании физического устройства, например хранилища или пиксель, можно обновить устройство Android круговой диаграммы, следуя инструкциям в [фабрики образов для хранилища и пикселей устройства](https://developers.google.com/android/images).

Если вы используете эмулятор, создания виртуального устройства для уровня API 28 и выберите образ x86. Сведения об использовании диспетчер устройств Android для создания и управления виртуальными устройствами, см. в разделе [управление виртуальными устройствами с помощью диспетчера устройств Android](~/android/get-started/installation/android-emulator/device-manager.md).
Сведения об использовании эмулятора Android для тестирования и отладки, см. в разделе [отладки в эмуляторе Android](~/android/deploy-test/debugging/debug-on-emulator.md).



## <a name="new-features"></a>Новые функции

Android круговой дает ряд новых функций. Некоторые из этих новых функций предназначены для использования новых возможностей оборудования, предлагаемых последних устройствах Android, а другие разработаны для улучшенного взаимодействия с пользователем Android:

-   **Поддержка обрезки монитора** &ndash; предоставляет интерфейсы API, чтобы найти местоположение и форма элементов _обрезки_ в верхней части экрана на новых устройствах с Android.

-   **Улучшения уведомлений** &ndash; уведомляющих сообщений сейчас может отображать изображения, а новые `Person` класс используется для упрощения участников диалога.

-   **Внутренний размещения** &ndash; платформы поддержку протокола приемов-передач времени Wi-Fi, который позволяет приложениям использовать устройства Wi-Fi для навигации в помещениях.

-   **Поддержка нескольких камеры** &ndash; предлагает возможность доступа потоков одновременно из нескольких физических камеры (например, «dual передней и задней двумя камеры).


В следующих разделах, выделите эти функции и дать примерами кода, которые помогут приступить к работе с их использованием в приложении.

### <a name="display-cutout-support"></a>Поддержка отображения обрезки

У многих новых устройствах с Android с экранами вплотную друг к другу *отображения обрезки* (или «деление») в верхней части экрана для камеры и выступает с докладами.
Следующем снимке экрана приведен пример эмулятор обрезки.

[![Эмулятор Android, имитируя обрезки](pie-images/02-example-cutout-sml.png)](pie-images/02-example-cutout.png#lightbox)

Для управления отображения его содержимого на устройствах с выключателем отображения окна приложения, круговой Android добавлен новый [LayoutInDisplayCutoutMode](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#layoutInDisplayCutoutMode) атрибут макета окна. Этот атрибут может быть присвоено одно из следующих значений:

-   [LayoutInDisplayCutoutModeNever](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_NEVER) &ndash; окна никогда не может перекрывать области обрезки.

-   [LayoutInDisplayCutoutModeShortEdges](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_SHORT_EDGES) &ndash; окна разрешена для расширения в область обрезки, но только в за края экрана. 

-   [LayoutInDisplayCutoutModeDefault](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_DEFAULT) &ndash; окна может быть расширен области обрезки, если область отсечения содержится в системной панели.

Например, чтобы предотвратить перекрытие с областью обрезки в окно приложения, задайте макет обрезки режим *никогда не*: 

```csharp
Window.Attributes.LayoutInDisplayCutoutMode =
    Android.Views.LayoutInDisplayCutoutMode.Never;
```

Ниже приведены примеры примеры этих режимов обрезки. Первый снимок экрана в левой части — приложения в режиме не в полноэкранном режиме. На снимке экрана center, приложение переходит на весь экран с `LayoutInDisplayCutoutMode` присвоено `LayoutInDisplayCutoutModeShortEdges`. Обратите внимание на то, что приложения белый фон расширяет в области обрезки отображения:

[![Пример обрезки режимы отображения в эмуляторе](pie-images/03-cutout-modes-sml.png)](pie-images/03-cutout-modes.png#lightbox)

На снимке экрана окончательного (выше в правой части), `LayoutInDisplayCutoutMode` присваивается `LayoutInDisplayCutoutModeShortNever` затем перейдет на весь экран.
Обратите внимание на то, что приложения белый фон не разрешено для расширения в области обрезки отображения.

Если требуется более подробные сведения об области обрезки на устройстве, можно использовать новый [DisplayCutout](https://developer.android.com/reference/android/view/DisplayCutout.html) класса. `DisplayCutout` Представляет область отображения, не может использоваться для отображения содержимого. Эти сведения можно использовать для получения местонахождении и форме область отсечения, таким образом, чтобы приложения не пытается отобразить содержимое в этой области не работает.

Дополнительные сведения о новых возможностях обрезки P для Android, см. в разделе [отображения обрезки поддержки](https://developer.android.com/about/versions/pie/android-9.0#cutout).



### <a name="notifications-enhancements"></a>Улучшения уведомлений

Android круговой появились следующие усовершенствования для повышения удобства обмена сообщениями.

-   Каналы уведомления (представленные в [Android Oreo](~/android/platform/oreo.md)) теперь поддерживает блокирование группы канала.

-   Система уведомлений имеет три новых-Not-беспокоить категорий (назначение приоритетов оповещений, системные звуки и источников мультимедиа). Кроме того существует семь новых-Not-беспокоить режимов, которые могут использоваться для подавления visual перерывы в работе (например, значки, индикаторы уведомлений, внешний вид строки состояния и запуск действий в полноэкранном режиме).

-   Новый [Person](https://developer.android.com/reference/android/app/Person.html) добавлен класс для представления отправителя сообщения. Используйте данный класс помогает оптимизировать отрисовку каждого уведомления путем выявления пользователи, участвующие в диалоге (включая их аватары и URI).

-   Уведомления теперь можно отобразить изображения. 

Следующий пример иллюстрирует способ использовать новые интерфейсы API для создания уведомления, содержащий образ. На следующих снимках экрана текстового уведомления публикации и следуют уведомление внедренными изображениями. Когда уведомления будут развернуты (как показано в правой части), отображается текст первого уведомления и внедренные изображения в увеличении второе уведомление:

[![Пример уведомления об с изображением](pie-images/04-example-notifications-sml.png)](pie-images/04-example-notifications.png#lightbox)

Приведенный ниже показано, как включить изображения в уведомление Android круговой, а также демонстрирует использование нового `Person` класса:

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

Android круговой обеспечивает поддержку для IEEE 802.11mc (также известный как _Wi-Fi приемов-передач времени_ или _Wi-Fi времени приема-Передачи_), который позволяет обнаружить расстояние к одному или точек доступа Wi-Fi. Используя эту информацию, возможна ситуация для вашего приложения, чтобы воспользоваться преимуществами *внутренний позиционирования* с точностью до 1 – 2 метров. На устройствах Android, которые обеспечивают поддержку оборудования для IEEE 801.11mc приложение может предложить возможностях навигации, например элемент управления на основе расположения смарт-устройств или включить в свою очередь инструкции с помощью хранилища:

[![Пример внутри помещений навигацию, с помощью Wi-Fi времени приема-Передачи](pie-images/05-wifi-rtt-sml.png)](pie-images/05-wifi-rtt.png#lightbox)

Новый [WifiRttManager](https://developer.android.com/reference/android/net/wifi/rtt/WifiRttManager) класс и несколько вспомогательных классов предоставляет средства для измерения расстояния для Wi-Fi устройства. Дополнительные сведения о внутренний API размещения, появившихся в P для Android, см. в разделе [Android.Net.Wifi.Rtt](https://developer.android.com/reference/android/net/wifi/rtt/package-summary).


### <a name="multi-camera-support"></a>Поддержка нескольких камеры

Множество новых устройствах с Android имеют dual-front и/или двойной обратной камеры, которые можно использовать для таких компонентов, как стерео зрением, улучшенные визуальные эффекты и масштаб улучшенные возможности. Android P вводится новый [несколькими камеры](https://developer.android.com/about/versions/pie/android-9.0#camera) API, который позволяет приложению использовать *логических камеры* (или *логических несколькими камеры*), поддерживаемый двух или более Физический камеры.
Чтобы определить, если устройство поддерживает логические с несколькими веб-камеры, вы можете изучить возможности каждой камеры на устройстве, чтобы узнать, поддерживает ли [RequestAvailableCapabilitiesLogicalMultiCamera](https://developer.android.com/reference/android/hardware/camera2/CameraMetadata#REQUEST_AVAILABLE_CAPABILITIES_LOGICAL_MULTI_CAMERA).

Android круговой также включает новый [Конфигурациясеанса](https://developer.android.com/reference/android/hardware/camera2/params/SessionConfiguration.html) класс, который может использоваться способствует сокращению задержки во время первоначальной записи и полностью устранить необходимость запустить и поток камеры.

Дополнительные сведения о нескольких камеры поддержки в P для Android, см. в разделе [несколькими камеры поддержки и камеры обновления](https://developer.android.com/about/versions/pie/android-9.0#camera).


### <a name="other-features"></a>Другие возможности

Кроме того круговой Android поддерживает несколько новых возможностей:

-   Новый [AnimatedImageDrawable](https://developer.android.com/reference/android/graphics/drawable/AnimatedImageDrawable.html) класс, который может использоваться для рисования и отображение анимированных рисунков.

-   Новый [ImageDecoder](https://developer.android.com/reference/android/graphics/ImageDecoder.html) класс, который заменяет `BitmapFactory`. `ImageDecoder` можно использовать для декодирования `AnimatedImageDrawable`.

-   Поддержка видео HDR (High динамического диапазона) и HEIF (высокая эффективность формат файлов изображений) изображения.

-   [JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html) была улучшена для более интеллектуальной обработки задания, относящиеся к сети. Новый [GetNetwork](https://developer.android.com/reference/android/app/job/JobParameters#getNetwork%28%29) метод [JobParameters](https://developer.android.com/reference/android/app/job/JobParameters) класс возвращает лучших сетевых для выполнения любой сетевых запросов для данного задания.

Дополнительные сведения о новых функциях Android круговой см. в разделе [Android 9 функции и API](https://developer.android.com/about/versions/pie/android-9.0).


## <a name="behavior-changes"></a>Изменения в поведении

Если целевая версия Android установлен уровень API 28, существует несколько изменений платформы, которые могут повлиять на поведение приложения, даже если вы не реализуете новые возможности, описанные выше. Ниже приведен краткий обзор этих изменений:

-  Приложения теперь необходимо запросить разрешение на переднем плане перед использованием службы переднего плана.

-  Если приложение имеет более одного процесса, не могут совместно использовать один [WebView](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) каталог данных между процессами.

-  Прямой доступ к другим приложением данные каталога по пути больше не допускается.

Дополнительные сведения об изменениях в поведении для приложений, предназначенных для Android P см. в разделе [изменения в поведении](https://developer.android.com/about/versions/pie/android-9.0-changes-all#p-apps).


## <a name="sample-code"></a>Пример кода

[AndroidPMiniDemo](https://github.com/xamarin/monodroid-samples/tree/master/android-p/AndroidPMiniDemo) — это пример приложения Xamarin.Android Android круга, как задать режимы отображения обрезки, как использовать новый `Person` , а также для отправки уведомления, включающий изображение.


## <a name="summary"></a>Сводка

В этой статье представлены Android круговой и было рассмотрено, как установить и настроить в Android круговой новейшие средства и пакеты для разработки приложений Xamarin.Android. Общие сведения о ключевых функциях, доступных в Android круговой предоставлен примере исходного кода для нескольких из этих функций.
Он включен ссылки на документацию по API и разделы разработчика Android, которые помогут приступить к созданию приложений Android круга. Кроме того, он выделен наиболее важные изменения поведения круговой Android, которые могут повлиять на существующие приложения.


## <a name="related-links"></a>Связанные ссылки

- [Android круговой 9](https://developer.android.com/about/versions/pie/)
