---
title: Функции Oreo
description: Как приступить к работе с Xamarin.Android для разработки приложений для последней версии Android.
ms.prod: xamarin
ms.assetid: EAEF7341-7A00-4439-9FAF-43882637BEF8
ms.technology: xamarin-android
ms.custom: video
author: mgmclemore
ms.author: mamcle
ms.date: 06/22/2018
ms.openlocfilehash: a23072427a74119bfa339fea8a695cd13b775685
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935107"
---
# <a name="oreo-features"></a>Функции Oreo

_Как приступить к работе с Xamarin.Android для разработки приложений для последней версии Android._

[Android 8.0 Oreo](https://developer.android.com/index.html) доступна последняя версия Android из Google. Android Oreo предлагает множество новых возможностей интерес для разработчиков Xamarin.Android. Эти возможности включают каналов уведомлений, уведомление эмблемы, пользовательские шрифты в XML, загружаемые шрифты, автозаполнение и изображение в изображении (PIP). Android Oreo включает новые API для этих новых capabilties, а эти API-интерфейсы для Xamarin.Android приложений при использовании Xamarin.Android 8.0 и более поздних версий.

[![Android Oreo героя изображения](oreo-images/01-android-o-logo-sml.png)](oreo-images/01-android-o-logo.png#lightbox)

В этой статье структурирована, чтобы помочь вам приступить к работе в разработке Xamarin.Android приложений для Android 8.0 Oreo. Здесь объясняется, как установить необходимые обновления и настройки пакета SDK для создания эмулятор (или устройства) для тестирования. Обзор новых возможностей Android 8.0 Oreo, он также предоставляет ссылки на примеры приложений, которые показывают, как использовать функции Android Oreo в Xamarin.Android приложений.


## <a name="requirements"></a>Требования

Использовать функции Android Oreo в Xamarin-приложениям требуется следующее:

-   **Visual Studio** &ndash; при использовании Windows необходима версия 15,5 или более поздней версии среды Visual Studio.  Если вы используете Mac, Visual Studio для Mac версии 7.2.0 не требуется.

-   **Xamarin.Android** &ndash; Xamarin.Android версии 8.0 или более поздней версии необходимо установить и настроить с помощью Visual Studio.

-   **Пакет SDK для Android** &ndash; Android SDK 8.0 (API 26) или более поздней версии должны устанавливаться через диспетчер Android SDK.



## <a name="getting-started"></a>Начало работы

Чтобы приступить к работе с Android Oreo Xamarin.Android, необходимо загрузить и установить новейшие средства и пакеты SDK, прежде чем создавать проект Android Oreo:

1. Обновление до последней версии Visual Studio.

2. Установка **8.0.0 Android (API 26)** или более поздней версии пакетов и средств через диспетчер пакета SDK.

3. Создайте новый проект Xamarin.Android, предназначенное Android Oreo (API 26).

4. Настройте эмулятор или устройство для тестирования приложения Android Oreo.

Каждое из этих действий описан в следующих разделах:



### <a name="update-visual-studio-and-xamarinandroid"></a>Обновление Visual Studio и Xamarin.Android

Чтобы добавить поддержку Android Oreo в Visual Studio, выполните следующие действия.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

-   Если вы используете Visual Studio 2017 г.: 

    1. Обновление для Visual Studio 2017 г 15,5 или более поздней версии (в разделе [2017 г. обновление Visual Studio](https://docs.microsoft.com/en-us/visualstudio/install/update-visual-studio)).

    2. С помощью диспетчера пакета SDK [инструкции по установке](~/android/get-started/installation/android-sdk.md#installation) установить диспетчер пакета SDK для Xamarin.
       Необходимо установить диспетчер пакетов SDK для Xamarin, поскольку Google не предоставляет standlone диспетчера пакета SDK графического пользовательского интерфейса, который поддерживает API 26,0 и более поздней версии.

-   Если вы используете Visual Studio 2015, мы рекомендуется понизить версию средств SDK до 25 и с помощью старого диспетчера эмуляторов Google графического пользовательского интерфейса. Средства пакета SDK 25 по-прежнему может использоваться вместе с API 26, 27 и более поздних версиях и не отразится разработки для новых платформ. Это позволит получить интерфейс для управления Android SDK для более старых версий VS.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

-   Обновить до последней стабильной версии Visual Studio 2017 г. для Mac, как описано в [обновление Visual Studio для Mac](https://docs.microsoft.com/en-us/visualstudio/mac/update).

-----

Дополнительные сведения о поддержке Xamarin Android Oreo см. в разделе [заметки о выпуске Xamarin.Android 8.0](https://developer.xamarin.com/releases/android/xamarin.android_8/xamarin.android_8.0/).



### <a name="install-the-android-sdk"></a>Установка пакета SDK для Android

Создание проекта Xamarin.Android 8.0, сначала необходимо использовать диспетчер Android SDK Xamarin для установки пакета SDK платформы для **Android 8.0 - Oreo** или более поздней версии. Также необходимо установить пакет инструментов SDK Android 26,0 или более поздней версии.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Запустите диспетчер пакета SDK (в Visual Studio щелкните **Сервис > Android > Диспетчер Android SDK Manager**).

2. Установка **Android 8.0 - Oreo** пакетов. Если вы используете эмулятор Android SDK, не забудьте включить **x86** образы системы, которые вам потребуется:

    [![При выборе Android 8.0 пакетов в диспетчере Android SDK](oreo-images/win/01-android-o-packages.png)](oreo-images/win/01-android-o-packages.png#lightbox)

3. Установка **пакет инструментов SDK Android 26.0.2** или более поздней версии, **Android инструментов платформы SDK 26.0.0** или более поздней версии, и **Android SDK-средства построения 26.0.0** (или более поздней версии):

    [![При выборе средства пакета SDK для Android 26 в диспетчере Android SDK](oreo-images/win/02-sdk-tools.png)](oreo-images/win/02-sdk-tools.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

1. Запустите диспетчер пакета SDK (в Visual Studio для Mac, выберите **Сервис > Диспетчер пакетов SDK**).

2. Установка **Android 8.0 - Oreo** пакетов SDK. Если вы используете эмулятор Android SDK, не забудьте включить **x86** образы системы, которые вам потребуется:

    [![При выборе Android 8.0 пакетов в диспетчере SDK](oreo-images/mac/01-android-o-packages.png)](oreo-images/mac/01-android-o-packages.png#lightbox)

3. Установка **пакет инструментов SDK Android 26.0.2** или более поздней версии, **Android инструментов платформы SDK 26.0.0** или более поздней версии, и **Android SDK-средства построения 26.0.0** (или более поздней версии):

    [![Выбор средств пакета SDK для Android 26 в диспетчер пакета SDK](oreo-images/mac/02-sdk-tools.png)](oreo-images/mac/02-sdk-tools.png#lightbox)

-----



### <a name="start-a-xamarinandroid-project"></a>Запуск проекта Xamarin.Android

Создание нового проекта Xamarin.Android. Если вы не знакомы с разработки приложений Android с помощью Xamarin, см. раздел [Привет, Android](~/android/get-started/hello-android/index.md) Дополнительные сведения о создании проектов Xamarin.Android.

При создании проекта Android, необходимо настроить параметры целевой объект Android 8.0 или более поздней версии. Например, для целевого проекта для Android 8.0, необходимо настроить уровень Android API целевого проекта, чтобы **Android 8.0 (API 26)**. Рекомендуется также задать уровень target framework API 26 или более поздней версии. Дополнительные сведения о настройке Android API уровня уровней см. в разделе [основные сведения об уровнях Android API](~/android/app-fundamentals/android-api-levels.md).


### <a name="configure-an-emulator-or-device"></a>Настройка эмулятора или устройства

При попытке запустить диспетчер AVD Google Графическим интерфейсом пользователя по умолчанию после установки Android SDK средства 26.0 или более поздней версии, то появляется следующее диалоговое окно ошибки, который указывает, что позволяет использовать средства командной строки диспетчера AVD **avdmanager** вместо :

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Диалоговое окно предупреждения диспетчер эмулятора Android](oreo-images/win/03-avd-warning.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

![Диалоговое окно предупреждения диспетчер эмулятора Android](oreo-images/mac/03-avd-warning.png)

-----

Это сообщение появляется, поскольку компания Google предоставляет больше не standlone диспетчера AVD графического интерфейса пользователя, который поддерживает API 26,0 и более поздней версии. Для Android 8.0 Oreo, необходимо использовать диспетчер эмулятора Android Xamarin или командной строки `avdmanager` средство для создания виртуального устройства для Android Oreo.

В диспетчере устройств Android для создания и управления виртуальными устройствами, в разделе [управление виртуальными устройствами с помощью диспетчера устройств Android](~/android/get-started/installation/android-emulator/device-manager.md).
Чтобы создать виртуальные устройства без диспетчера устройств Android, следуйте указаниям в следующем разделе.


#### <a name="creating-virtual-devices-using-avdmanager"></a>Создание виртуального устройства с помощью avdmanager

Для использования **avdmanager** для создания нового виртуального устройства, выполните следующие действия:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  Откройте окно командной строки и установите `JAVA_HOME` расположение пакета SDK для Java на компьютере. Для обычной установки Xamarin можно использовать следующую команду:

    ```cmd
    setx JAVA_HOME "C:\Program Files\Java\jdk1.8.0_131"
    ```

2.  Добавьте расположение пакета SDK для Android `bin` папку для вашей `PATH`.
    Для обычной установки Xamarin можно использовать следующую команду:

    ```cmd
    setx PATH "%PATH%;C:\Program Files (x86)\Android\android-sdk\tools\bin"
    ```

3.  Закройте окно командной строки и открыть новое окно командной строки. Создание нового виртуального устройства с помощью [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html) команды. Например, чтобы создать AVD с именем **AVD Oreo 8.0** с помощью x86 образ системы для API уровня 26, используйте следующую команду:

    ```cmd
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

4.  При появлении с **вы хотите создать профиль оборудования [нет]** можно ввести **не** и принять конфигурации по умолчанию. Если выбрать вариант **Да**, **avdmanager** предложит список вопросов для настройки профиля оборудования.

По окончании **avdmanager** для создания виртуального устройства, он будет включен в раскрывающемся меню устройства:

[![Добавлен новый AVD устройство в раскрывающемся меню](oreo-images/win/04-android-o-avd-sml.png)](oreo-images/win/04-android-o-avd.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

1.  Откройте **терминалов** окно и измените расположение каталога средств пакета SDK для Android на компьютере Mac. Для обычной установки Xamarin можно использовать следующую команду:

    ```bash
    cd ~/Library/Developer/Xamarin/android-sdk-macosx/tools/bin
    ```

2.  Создание нового виртуального устройства с помощью [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html) команды. Например, чтобы создать AVD с именем **AVD Oreo 8.0** с помощью x86 образ системы для API уровня 26, используйте следующую команду:

    ```bash
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

3.  При появлении с **вы хотите создать профиль оборудования [нет]** можно ввести **не** и принять конфигурации по умолчанию. Если выбрать вариант **Да**, **avdmanager** предложит список вопросов для настройки профиля оборудования.

После использования **avdmanager** для создания виртуального устройства, он будет включен в раскрывающемся меню устройства:

[![Добавлен новый AVD устройство в раскрывающемся меню](oreo-images/mac/04-android-o-avd-sml.png)](oreo-images/mac/04-android-o-avd.png#lightbox)

-----

Дополнительные сведения о настройке эмулятор Android для тестирования и отладки см. в разделе [отладки в эмуляторе Android](~/android/deploy-test/debugging/debug-on-emulator.md).

При использовании физического устройства, такие как хранилища или пиксель, можно либо обновить устройство через автоматическое за обновлениями, беспроводные или загружать образ системы и непосредственно flash устройства. Дополнительные сведения о обновление устройства Android Oreo вручную см. в разделе [изображений фабрики для хранилища и пикселей устройства](https://developers.google.com/android/images).



## <a name="new-features"></a>Новые функции

Android Oreo дает ряд новых функций и возможностей, например каналов уведомлений, уведомление эмблемы, пользовательские шрифты в формате XML, загружаемые шрифты, автозаполнение и рисунка в изображение. В следующих разделах, выделите эти возможности и предоставляют ссылки, которые помогут вам приступить к работе с их в приложении.



### <a name="notification-channels"></a>Каналы уведомления

*Каналы уведомления* определенные приложения категорий для уведомлений.
Можно создать канал уведомления для каждого типа, необходимое для отправки уведомлений и создании каналов уведомлений с учетом параметров, внесенные пользователями приложения. Новая функция каналы уведомлений позволяет предоставить пользователям возможность точного управления различные типы уведомлений. Например при реализации обмена сообщениями приложения, можно создать каналов отдельные уведомления для каждой группы сообщений, созданных пользователем.

[Каналы уведомления](~/android/app-fundamentals/notifications/local-notifications.md#notif-chan) объясняется, как создать канал уведомления и использовать ее для учета локального уведомления. Пример кода реального мира, в разделе [NotificationChannels](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels) образец; приложение из этого примера управляет два канала и настраивает параметры дополнительных уведомлений.



### <a name="notification-badges"></a>Уведомление эмблемы

Уведомление эмблемы, точки, которые отображаются над значками приложения, как показано на этом снимке экрана:

[![Пример уведомление эмблемы на значки приложений](oreo-images/02-badges-sml.png)](oreo-images/02-badges.png#lightbox)

Эти точки указывает на наличие новых уведомлений для одного или нескольких каналов уведомлений в приложения, связанных с этим значком приложения &ndash; это уведомлений, которые пользователь еще не закрыт или ответных. Пользователей можно долго нажмите на значок посмотрите на уведомления, связанные с эмблемой уведомления, отклонения или воздействия на уведомления из меню нажмите клавишу долго, appeaars.

Дополнительные сведения о уведомление эмблемы разделе разработчика Android [уведомление эмблемы](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#Badges) раздела.



### <a name="custom-fonts-in-xml"></a>Пользовательские шрифты в формате XML

Представляет Android Oreo *шрифтов в XML*, что делает можно внедрить пользовательские шрифты в качестве ресурсов. OpenType (**.otf**) и TrueType (**.ttf**) поддерживаются форматы шрифта. Чтобы добавить шрифты в качестве ресурсов, выполните следующие действия:

1. Создание **ресурсы или шрифта** папки.

2. Скопируйте файлы шрифта (примере **.ttf** и **.otf** файлов) для **ресурсы или шрифта**. 

3. При необходимости переименуйте каждый файл шрифта так, чтобы оно соответствовало файле Android соглашения об именовании (т. е. Используйте только в нижнем регистре *a – z*, *0-9*и знаки подчеркивания в именах файлов). Например, файл шрифта `Pacifico-Regular.ttf` может быть переименован, например `pacifico.ttf`.

4. Применение пользовательского шрифта с помощью нового `android:fontFamily` атрибута в макете XML. Например, следующая `TextView` объявление использует дополнительную **pacifico.ttf** ресурс шрифта:

   ```xml
   <TextView
     android:text="Example Text in Pacifico Regular"
     android:layout_width="wrap_content"
     android:layout_height="wrap_content"
     android:fontFamily="@font/pacifico" />
   ```

Можно также создать шрифта семейства файл XML, описывающий несколько шрифтов, а также сведения о стиле и вес. Дополнительные сведения см. в разделе разработчика Android [шрифтов в XML](https://developer.android.com/guide/topics/ui/look-and-feel/fonts-in-xml.html) раздела.


### <a name="downloadable-fonts"></a>Загружаемые шрифты

Начиная с Android Oreo, приложения могут запросить шрифты поставщика, а не их объединения в APK. Шрифты загружаются из сети только при необходимости. Эта функция сокращает размер APK, экономия использование данных памяти и сотового телефона. Также можно использовать эту функцию на Android API-Интерфейс версии 14 и более поздние версии путем установки пакета Android 26 библиотеки поддержки.

Если ваше приложение должно шрифт, создаваемый `FontsRequest` объекта (с указанием шрифтов для загрузки) и затем передать его в `FontsContract` метод для загрузки шрифта. Следующие шаги описывают процесс загрузки шрифта более подробно:

1.  Создать экземпляр [FontRequest](https://developer.android.com/reference/android/provider/FontRequest.html) объекта. 

2.  Подкласс и создание экземпляра [FontsContract.FontRequestCallback](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html).

3.  Реализуйте [FontRequestCallback.OnTypeFaceRetrieved](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRetrieved%28android.graphics.Typeface%29) метод, который используется для обработки завершения запроса шрифта.

4.  Реализуйте [FontRequestCallback.OnTypeFaceRequestFailed](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRequestFailed%28int%29) метод, который используется для информирования приложение все ошибки, происходящие во время обработки запроса шрифта.

5.  Вызовите [FontsContract.RequestFonts](https://developer.android.com/reference/android/provider/FontsContract.html#requestFonts(android.content.Context,%20android.provider.FontRequest,%20android.os.Handler,%20android.os.CancellationSignal,%20android.provider.FontsContract.FontRequestCallback)) метод для извлечения шрифт из поставщика шрифта. 

При вызове `RequestFonts` метод, он сначала проверяет Если шрифт локального кэширования (в результате предыдущего вызова `RequestFont`). Если он не был кэширован, он вызывает поставщика шрифта, асинхронно извлекает шрифт, а затем передает результаты обратно в приложение путем вызова вашей `OnTypeFaceRetrieved` метод.

[Загружаемые шрифты](https://developer.xamarin.com/samples/monodroid/android-o/DownloadableFonts) образце показано, как использовать функцию загружаемые шрифты, представленные в Android Oreo. 

Дополнительные сведения о загрузке шрифты см. в разделе разработчика Android [загружаемые шрифты](https://developer.android.com/guide/topics/ui/look-and-feel/downloadable-fonts.html) раздела.



### <a name="autofill"></a>Автозаполнение

Новый _Автозаполнение_ framework в Android Oreo облегчает пользователям обрабатывать повторяющиеся задачи, такие как имя входа, создание учетной записи и транзакций с кредитными картами. Пользователи тратить меньше времени, повторно вводить сведения (что может привести к входным ошибок). Прежде чем приложение сможет работать с платформой Автозаполнение, то должна быть включена служба Автозаполнение, в параметрах системы (пользователей можно включить или отключить автозаполнение).

[AutofillFramework](https://developer.xamarin.com/samples/monodroid/android-o/AutoFillFramework/) образец демонстрирует использование платформы автозаполнение. Он включает реализации клиента действий с представлениями, которые должны быть autofilled и службой, может предоставлять данные Автозаполнение клиенту действий.

Дополнительные сведения о новой функции автозаполнения и способах оптимизации приложения для автозаполнения см. в разделе разработчика Android [Framework Автозаполнение](https://developer.android.com/guide/topics/text/autofill.html) раздела.



### <a name="picture-in-picture-pip"></a>Изображение в изображении (PIP)

Android Oreo делает возможным для действия для запуска в режиме изображение в изображении (PIP), наложенной экрана другого действия. Эта функция предназначена для воспроизведения видео.

Чтобы указать, что действия приложения можно использовать режим PIP, значение true в манифесте Android следующий флаг:

```xml
android:supportsPictureInPicture
```

Чтобы указать поведение действие когда он находится в режиме PIP, использовать новую [PictureInPictureParams](https://developer.android.com/reference/android/app/PictureInPictureParams.html) объекта. `PictureInPictureParams` Представляет набор параметров, которые используются для инициализации и обновления действие в режиме PIP-адрес (например, действия предпочтительный соотношение сторон). Были добавлены следующие новые методы PIP `Activity` в Android Oreo:

-   [EnterPictureInPictureMode](https://developer.android.com/reference/android/app/Activity.html#enterPictureInPictureMode%28android.app.PictureInPictureParams%29) &ndash; переводит действие в режиме PIP-адрес. Действие помещается в углу экрана, а остальная часть экрана заполняется предыдущие действия, которое было на экране.

-   [SetPictureInPictureParams](https://developer.android.com/reference/android/app/Activity.html#setPictureInPictureParams%28android.app.PictureInPictureParams%29) &ndash; обновляет параметры конфигурации PIP действия (например, изменения в пропорции).

[PictureInPicture](https://developer.xamarin.com/samples/monodroid/android-o/PictureInPicture) образце показано базовое использование режима карманные устройства, появившиеся в Oreo изображение в изображении (PiP). Образец играет видео, который продолжается без перерыва при переключении между режимами отображения или другие действия.



### <a name="other-features"></a>Другие функции

Android Oreo содержит множество новых функций, таких как библиотека API расположение фонового Emoji поддержки ограничений, широким цвет для приложений, новый аудиокодеки, усовершенствования WebView, поддержка навигации улучшенные клавиатуры и новый API AAudio (pro звука) для аудио низкой задержкой высокой производительности, Дополнительные сведения об этих возможностях см. разработчика Android [Android Oreo функции и API](https://developer.android.com/about/versions/oreo/android-8.0.html) раздела.



## <a name="behavior-changes"></a>Изменения в поведении

Android Oreo включает разнообразные системы и изменения в поведении API, может повлиять на функциональные возможности существующих приложений. Эти изменения как описано ниже.


### <a name="background-execution-limits"></a>Ограничения выполнения фона

Чтобы улучшить взаимодействие с пользователем, Android Oreo накладывает ограничения на приложения, которые можно сделать во время выполнения в фоновом режиме. Например если пользователь является просмотр видео или игры, приложения в фоновом режиме может значительно снизить производительность, интенсивно использующих видео приложения выполняется на переднем плане. В результате Android Oreo действуют следующие ограничения для приложений, которые непосредственно не взаимодействуют с пользователем:

1.  **Фоновая служба ограничения** &ndash; когда приложение выполняется в фоновом режиме, он имеет несколько минут, в котором он по-прежнему разрешено создавать и использовать службы окна. В конце этого окна Android останавливает фоновой службы приложения и воспринимает его как _простоя_.

2.  **Рассылка ограничения** &ndash; Android 7.0 (API 25) разместить ограничения на широковещательные рассылки, которые регистрирует приложение для получения. Android Oreo делает эти ограничения более строгой. Например приложения Android Oreo больше не могут регистрироваться широковещательных получателей для неявного вещания в манифестах.

Дополнительные сведения о новых ограничений выполнения фоновой см разработчика Android [ограничения выполнения фоновой](https://developer.android.com/about/versions/oreo/background.html) раздела.


### <a name="breaking-changes"></a>Критические изменения

Приложения, предназначенные Android Oreo или более поздней версии, необходимо изменить их приложений поддерживаются следующие изменения, где это возможно:

- Android Oreo отказывается возможность указать приоритет для отдельных уведомлений. Вместо этого рекомендуется важности задать при создании канала уведомления. Уровень важности, назначаемые канала уведомлений применяется для всех сообщений уведомлений, учете к нему.

- Для приложения, предназначенные для Android Oreo `PendingIntent.GetService()` не работает из-за нового пределы, наложенные на службы запущены в фоновом режиме. Если вы используете Android Oreo, следует использовать [PendingIntent.GetBroadcast](https://developer.xamarin.com/api/member/Android.App.PendingIntent.GetBroadcast/p/Android.Content.Context/System.Int32/Android.Content.Intent/Android.App.PendingIntentFlags/) вместо него.  


## <a name="sample-code"></a>Пример кода

Несколько примеров Xamarin.Android показывают, как использовать преимущества функций Android Oreo:

-   [NotificationsChannels](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels) показано, как использовать новую систему каналов уведомлений, представленные в Android Oreo. В этом примере управляет два канала уведомления: один с важность по умолчанию, а другой — с высоким уровнем важности.

-   [PictureInPicture](https://developer.xamarin.com/samples/monodroid/android-o/PictureInPicture) показано базовое использование режима карманные устройства, появившиеся в Oreo изображение в изображении (PiP). Образец играет видео, который продолжается без перерыва при переключении между режимами отображения или другие действия.

-   [AutofillFramework](https://developer.xamarin.com/samples/monodroid/android-o/AutoFillFramework) показано использование функции автозаполнения Framework. Он включает реализации клиента действий с представлениями, которые должны быть autofilled и службой, может предоставлять данные Автозаполнение клиенту действий.

-   [Загружаемые шрифты](https://developer.xamarin.com/samples/monodroid/android-o/DownloadableFonts) приведен пример того, как использовать функцию загружаемые шрифты, описанных ранее.

-   [EmojiCompat](https://developer.xamarin.com/samples/monodroid/android-o/EmojiCompat) демонстрирует использование библиотеки поддержки EmojiCompat. Чтобы избежать приложения с отображением недостающие emoji символы как символы «диаграмме» можно использовать эту библиотеку.

-   [Ожидающие намерения обновления расположения](https://developer.xamarin.com/samples/monodroid/android-o/AndroidPlayLocation/LocUpdPendIntent) иллюстрирует использование API расположение для получения обновлений о расположении устройства с помощью `PendingIntent`.

-   [Службы переднего плана обновления расположения](https://developer.xamarin.com/samples/monodroid/android-o/AndroidPlayLocation/LocUpdFgService) демонстрирует использование API расположение для получения обновлений о местоположении устройства, с помощью службы переднего плана привязанные и запущена.


## <a name="video"></a>Видео

> [!VIDEO https://youtube.com/embed/OuvEcaMO-Ho]

**8.0 Oreo разработки приложений Android с помощью C#**


## <a name="summary"></a>Сводка

В этой статье представлена Android Oreo и объяснено, как установить и настроить новейшие средства и пакеты для разработки Xamarin.Android на Android Oreo. Обзор ключевых функций, доступных в Android Oreo предоставлены ссылки на исходный код примера для несколько новых возможностей. Он включен ссылки на документацию по API и разработчика Android разделы, которые помогут начать создание приложения для Android Oreo. Он также выделяются наиболее важные изменения поведения Android Oreo, которые могут повлиять на существующие приложения.


## <a name="related-links"></a>Связанные ссылки

- [Android 8.0 Oreo](https://developer.android.com/index.html)
