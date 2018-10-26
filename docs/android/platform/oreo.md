---
title: Компоненты Oreo
description: Как приступить к работе с помощью Xamarin.Android для разработки приложений для последней версии Android.
ms.prod: xamarin
ms.assetid: EAEF7341-7A00-4439-9FAF-43882637BEF8
ms.technology: xamarin-android
ms.custom: video
author: conceptdev
ms.author: crdun
ms.date: 07/06/2018
ms.openlocfilehash: e4f0138c0177c046c1e967a3d3625604f73327d6
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103196"
---
# <a name="oreo-features"></a>Компоненты Oreo

_Как приступить к работе с помощью Xamarin.Android для разработки приложений для последней версии Android._

[Android 8.0 Oreo](https://developer.android.com/index.html) доступна последняя версия Android от Google. Android Oreo предлагает множество новых функций интерес для разработчиков Xamarin.Android. Эти функции включают каналов уведомлений, уведомление эмблемы, пользовательские шрифты в XML, загружаемые шрифты, автозаполнение и картинка в картинке (PIP). Android Oreo включает в себя новые интерфейсы API для эти новые предложения, и эти интерфейсы API являются доступными для приложений Xamarin.Android при использовании Xamarin.Android 8.0 и более поздних версий.

[![Изображение героя Android Oreo](oreo-images/01-android-o-logo-sml.png)](oreo-images/01-android-o-logo.png#lightbox)

В этой статье структурирована, чтобы помочь вам приступить к разработке приложений Xamarin.Android для Android 8.0 Oreo. Здесь объясняется, как установить необходимые обновления, настройки пакета SDK и создание эмулятора (или устройства) для тестирования. Он также приводится обзор новых функций в Android 8.0 Oreo, со ссылками на примеры приложений, демонстрирующие способы использования возможностей Android Oreo в приложениях Xamarin.Android.


## <a name="requirements"></a>Требования

Чтобы использовать компоненты Android Oreo в приложениях на базе Xamarin требуется следующее:

-   **Visual Studio** &ndash; необходим при использовании Windows версии 15.5 или более поздней версии Visual Studio.  Если вы используете компьютер Mac, Visual Studio для Mac версии 7.2.0 не требуется.

-   **Xamarin.Android** &ndash; Xamarin.Android 8.0 или более поздней версии должен быть установлен и настроен с помощью Visual Studio.

-   **Пакет SDK для Android** &ndash; Android SDK 8.0 (API 26) или более поздней версии необходимо установить через диспетчер пакетов SDK для Android.



## <a name="getting-started"></a>Начало работы

Чтобы начать работу с помощью Android Oreo с помощью Xamarin.Android, необходимо скачать и установить новейшие средства и пакеты SDK, перед созданием проекта Android Oreo.

1. Обновление до последней версии Visual Studio.

2. Установка **8.0.0 Android (API 26)** или более поздней версии пакетов и средств и через диспетчер пакетов SDK.

3. Создание нового проекта Xamarin.Android излюбленного Android Oreo (API 26).

4. Настройте эмулятор или устройство для тестирования приложений для Android Oreo.

Каждый из этих шагов описан в следующих разделах:



### <a name="update-visual-studio-and-xamarinandroid"></a>Обновление Visual Studio и Xamarin.Android

Чтобы добавить поддержку Android Oreo для Visual Studio, сделайте следующее:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

-   Если вы используете Visual Studio 2017: 

    1. Обновление для Visual Studio 2017 версии 15.7 или более поздней версии (см. в разделе [обновление Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/update-visual-studio)).

    2. Используйте [диспетчер пакетов SDK](~/android/get-started/installation/android-sdk.md) установить уровень API 26.0 или более поздней версии.

-   Если вы используете Visual Studio 2015, мы рекомендуется до 25 значения SDK Tools и использовать старый графический пользовательский Интерфейс диспетчера эмуляторов Google. Средства пакета SDK 25 по-прежнему может использоваться вместе с API 26, 27 и более поздних версиях и не влияют на разработку для новых платформ. Это позволит получить интерфейс для управления Android SDK для более старых версий Visual STUDIO.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

-   Обновление до последней стабильной версии Visual Studio 2017 для Mac, как описано в [обновление Visual Studio для Mac](https://docs.microsoft.com/visualstudio/mac/update).

-----

Дополнительные сведения о поддержке в Xamarin для Android Oreo см. в разделе [заметки о выпуске Xamarin.Android 8.0](https://developer.xamarin.com/releases/android/xamarin.android_8/xamarin.android_8.0/).



### <a name="install-the-android-sdk"></a>Установка пакета SDK для Android

Создание проекта с помощью Xamarin.Android 8.0, необходимо сначала использовать диспетчер пакетов SDK Xamarin Android для установки платформы SDK для **Android 8.0 - Oreo** или более поздней версии. Кроме того, необходимо установить Android SDK Tools 26.0 или более поздней версии.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Запустите диспетчер пакетов SDK (в Visual Studio щелкните **Сервис > Android > Диспетчер пакетов SDK Android**).

2. Установка **Android 8.0 - Oreo** пакетов. Если вы используете эмулятор пакета SDK для Android, не забудьте включить **x86** образы системы, которые вам потребуется:

    [![Выбор пакетов Android 8.0 Диспетчер пакетов SDK для Android](oreo-images/win/01-android-o-packages.png)](oreo-images/win/01-android-o-packages.png#lightbox)

3. Установка **Android SDK Tools 26.0.2** или более поздней версии, **Android SDK Platform-Tools 26.0.0** или более поздней версии, и **Android SDK-средства сборки 26.0.0** (или более поздней версии):

    [![Выбор Android SDK Tools 26 в диспетчере пакетов SDK для Android](oreo-images/win/02-sdk-tools.png)](oreo-images/win/02-sdk-tools.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

1. Запустите диспетчер пакетов SDK (в Visual Studio для Mac щелкните **Сервис > Диспетчер пакетов SDK**).

2. Установка **Android 8.0 - Oreo** пакетов SDK. Если вы используете эмулятор пакета SDK для Android, не забудьте включить **x86** образы системы, которые вам потребуется:

    [![Выбор пакетов Android 8.0 Диспетчер пакетов SDK](oreo-images/mac/01-android-o-packages.png)](oreo-images/mac/01-android-o-packages.png#lightbox)

3. Установка **Android SDK Tools 26.0.2** или более поздней версии, **Android SDK Platform-Tools 26.0.0** или более поздней версии, и **Android SDK-средства сборки 26.0.0** (или более поздней версии):

    [![Выбрав в диспетчер пакетов SDK Android SDK Tools 26](oreo-images/mac/02-sdk-tools.png)](oreo-images/mac/02-sdk-tools.png#lightbox)

-----



### <a name="start-a-xamarinandroid-project"></a>Запуск проекта Xamarin.Android

Создание нового проекта Xamarin.Android. Если вы не знакомы с разработки приложений Android с помощью Xamarin, см. в разделе [Привет, Android](~/android/get-started/hello-android/index.md) Дополнительные сведения о создании проектов Xamarin.Android.

При создании проекта Android, необходимо настроить параметры целевой объект Android 8.0 или более поздней версии. Например, для целевого объекта проекта для Android 8.0, необходимо настроить уровень Android API целевого проекта, чтобы **Android 8.0 (API 26)**. Рекомендуется также задать уровень target framework API 26 или более поздней версии. Дополнительные сведения о настройке уровней уровня Android API, см. в разделе [уровнях API Android понимание](~/android/app-fundamentals/android-api-levels.md).


### <a name="configure-an-emulator-or-device"></a>Настройка эмулятора или устройства

Если при попытке запуска по умолчанию Google Графическим пользовательским интерфейсом диспетчера AVD после установки Android SDK Tools 26.0 или более поздней версии, может появиться следующее диалоговое окно ошибки, который указывает, как использовать средство командной строки диспетчера AVD **avdmanager** вместо :

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Диалоговое окно с предупреждением диспетчер эмуляторов Android](oreo-images/win/03-avd-warning.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

![Диалоговое окно с предупреждением диспетчер эмуляторов Android](oreo-images/mac/03-avd-warning.png)

-----

Это сообщение отображается, так как Google не предоставляет отдельное диспетчер AVD графического пользовательского интерфейса, который поддерживает API 26.0 и более поздние версии. Для Android 8.0 Oreo необходимо использовать диспетчер эмуляторов Android Xamarin или из командной строки `avdmanager` средство для создания виртуальных устройств для Android Oreo.

Чтобы использовать диспетчер устройств Android для создания и управления виртуальными устройствами, см. в разделе [управление виртуальными устройствами с помощью диспетчера устройств Android](~/android/get-started/installation/android-emulator/device-manager.md).
Чтобы создать виртуальные устройства без диспетчер устройств Android, выполните действия, описанные в следующем разделе.


#### <a name="creating-virtual-devices-using-avdmanager"></a>Создание виртуального устройства с помощью avdmanager

Чтобы использовать **avdmanager** для создания нового виртуального устройства, выполните следующие действия:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  Откройте окно командной строки и установите `JAVA_HOME` в расположение пакета SDK для Java на компьютере. Для обычной установки Xamarin можно использовать следующую команду:

    ```cmd
    setx JAVA_HOME "C:\Program Files\Java\jdk1.8.0_131"
    ```

2.  Добавьте расположение пакета SDK для Android `bin` папку для вашей `PATH`.
    Для обычной установки Xamarin можно использовать следующую команду:

    ```cmd
    setx PATH "%PATH%;C:\Program Files (x86)\Android\android-sdk\tools\bin"
    ```

3.  Закройте окно командной строки и откройте новое окно командной строки. Создание виртуального устройства с помощью [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html) команды. Например, чтобы создать виртуальное устройство AVD с именем **AVD Oreo 8.0** с помощью x86 образ системы для уровня API 26, используйте следующую команду:

    ```cmd
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

4.  При появлении с **вы хотите создать профиль оборудования [нет]** можно ввести **не** и принять конфигурации по умолчанию. Если вы ответите **Да**, **avdmanager** появится список вопросов для настройки профиля оборудования.

После того как вы **avdmanager** для создания виртуального устройства, он будет включен в раскрывающемся меню устройства:

[![Новый устройство AVD, добавленное в раскрывающееся меню устройства](oreo-images/win/04-android-o-avd-sml.png)](oreo-images/win/04-android-o-avd.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

1.  Откройте **терминалов** окно и измените расположение каталога средства пакета SDK для Android на компьютере Mac. Для обычной установки Xamarin можно использовать следующую команду:

    ```bash
    cd ~/Library/Developer/Xamarin/android-sdk-macosx/tools/bin
    ```

2.  Создание виртуального устройства с помощью [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html) команды. Например, чтобы создать виртуальное устройство AVD с именем **AVD Oreo 8.0** с помощью x86 образ системы для уровня API 26, используйте следующую команду:

    ```bash
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

3.  При появлении с **вы хотите создать профиль оборудования [нет]** можно ввести **не** и принять конфигурации по умолчанию. Если вы ответите **Да**, **avdmanager** появится список вопросов для настройки профиля оборудования.

После использования **avdmanager** для создания виртуального устройства, он будет включен в раскрывающемся меню устройства:

[![Новый устройство AVD, добавленное в раскрывающееся меню устройства](oreo-images/mac/04-android-o-avd-sml.png)](oreo-images/mac/04-android-o-avd.png#lightbox)

-----

Дополнительные сведения о настройке эмулятора Android для тестирования и отладки, см. в разделе [отладки в эмуляторе Android](~/android/deploy-test/debugging/debug-on-emulator.md).

При использовании физического устройства, например хранилища или пиксель, вы можете обновить устройство с помощью автоматического над обновлениями режиме (OTA) или загрузить образ системы и flash устройства напрямую. Дополнительные сведения о обновление устройства Android Oreo вручную, см. в разделе [фабрики образов для хранилища и пикселей устройства](https://developers.google.com/android/images).



## <a name="new-features"></a>Новые функции

Android Oreo дает ряд новых функций и возможностей, таких как каналы уведомления, уведомление эмблемы, пользовательские шрифты в формате XML, загружаемые шрифты, автозаполнение и рисунок в изображение. В следующих разделах, выделите эти функции и дать ссылки, которые помогут вам приступить к работе с их использованием в приложении.



### <a name="notification-channels"></a>Каналы уведомления

*Каналы уведомления* заданное приложением категорий для уведомлений.
Можно создать канал уведомления для каждого типа уведомление, которое нужно отправить, и можно создать каналы уведомления с учетом вариантов, внесенными пользователями приложения. Новая функция каналы уведомлений позволяет предоставить пользователям возможность точного управления различных типов уведомлений. Например при реализации приложении для обмена сообщениями, можно создать отдельное уведомление каналы на каждую группу сообщений, созданные пользователем.

[Каналы уведомления](~/android/app-fundamentals/notifications/local-notifications.md#notif-chan) объясняется, как создать канал уведомления и использовать его для отправки локального уведомления. Пример кода реального мира, см. в разделе [каналы уведомлений](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels) образец; этот пример приложения управляет два канала и задает дополнительные варианты оповещения.



### <a name="notification-badges"></a>Значки уведомлений

Уведомление эмблемы, точки, которые отображаются над значки приложений, как показано на следующем снимке экрана:

[![Пример уведомления эмблем на значки приложений](oreo-images/02-badges-sml.png)](oreo-images/02-badges.png#lightbox)

Эти точки обозначают, что существуют новых уведомлений для одного или нескольких каналов уведомлений в приложении, связанных с этим значком приложения &ndash; это уведомления, которые еще не закрыт или обрабатываемое пользователем. Пользователей можно долго нажмите на значок посмотрите на уведомления, связанные с значка уведомления, отклонение или воздействия на уведомления в меню нажмите клавишу долго, appeaars.

Дополнительные сведения о значки уведомлений, см. в разделе разработчика Android [уведомление эмблемы](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#Badges) раздела.



### <a name="custom-fonts-in-xml"></a>Пользовательские шрифты в формате XML

Представляет Android Oreo *шрифты в формате XML*, что усложняет можно внедрить пользовательские шрифты в качестве ресурсов. OpenType (**.otf**) и TrueType (**.ttf**) поддерживаются форматы вывода шрифтов. Чтобы добавить шрифты в качестве ресурсов, сделайте следующее:

1. Создание **ресурсы/шрифта** папки.

2. Скопируйте файлы шрифта (примере **.ttf** и **.otf** файлы) для **ресурсы/шрифта**. 

3. При необходимости переименуйте каждый файл шрифта, чтобы оно соответствовало файл Android, соглашения об именовании (т. е. Используйте только строчные *a – z*, *0 – 9*и символы подчеркивания в именах файлов). Например, файл шрифта `Pacifico-Regular.ttf` может быть переименован в нечто подобное `pacifico.ttf`.

4. Применение пользовательского шрифта с помощью нового `android:fontFamily` атрибут в макете XML. Например, следующая `TextView` объявлении используется добавленного **pacifico.ttf** ресурс шрифта:

   ```xml
   <TextView
     android:text="Example Text in Pacifico Regular"
     android:layout_width="wrap_content"
     android:layout_height="wrap_content"
     android:fontFamily="@font/pacifico" />
   ```

Можно также создать шрифта семейства XML файл, описывающий несколько шрифтов, а также сведения о стиле и вес. Дополнительные сведения см. в разделе разработчика Android [шрифты в формате XML](https://developer.android.com/guide/topics/ui/look-and-feel/fonts-in-xml.html) раздела.


### <a name="downloadable-fonts"></a>Загружаемые шрифты

Начиная с Android Oreo, приложения могут запрашивать шрифты из поставщика, а не их объединения в пакет APK. Шрифты загружаются из сети только при необходимости. Эта функция сокращает размер пакета APK, экономия использование данных памяти и сотовый телефон. Также можно использовать эту функцию в версиях Android API 14 и более поздних версий путем установки пакета Android 26 библиотеки поддержки.

Если вашему приложению необходим шрифт, создании `FontsRequest` (указывающий шрифт для загрузки) и затем передать его в `FontsContract` метод для загрузки шрифта. Следующие шаги описывают процесс загрузки шрифта более подробно:

1.  Создать экземпляр [FontRequest](https://developer.android.com/reference/android/provider/FontRequest.html) объекта. 

2.  Подкласс и создание их экземпляров [FontsContract.FontRequestCallback](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html).

3.  Реализуйте [FontRequestCallback.OnTypeFaceRetrieved](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRetrieved%28android.graphics.Typeface%29) метод, который используется для обработки выполнения запроса шрифта.

4.  Реализуйте [FontRequestCallback.OnTypeFaceRequestFailed](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRequestFailed%28int%29) метод, который используется для информирования об ошибках, которые вносятся во время обработки запроса шрифтов приложения.

5.  Вызовите [FontsContract.RequestFonts](https://developer.android.com/reference/android/provider/FontsContract.html#requestFonts(android.content.Context,%20android.provider.FontRequest,%20android.os.Handler,%20android.os.CancellationSignal,%20android.provider.FontsContract.FontRequestCallback)) метод для извлечения шрифт из поставщика шрифта. 

При вызове `RequestFonts` метод, его сначала проверяет, если шрифт локально кэшированных (из предыдущего вызова `RequestFont`). Если он не был кэширован, он вызывает поставщик шрифта, асинхронно извлекает шрифт, а затем передает результаты обратно в ваше приложение путем вызова вашего `OnTypeFaceRetrieved` метод.

[Загружаемые шрифты](https://developer.xamarin.com/samples/monodroid/android-o/DownloadableFonts) образце показано, как использовать функцию шрифтов, представленные в Android Oreo. 

Дополнительные сведения о загрузке шрифтов см. в разделе разработчика Android [загружаемые шрифты](https://developer.android.com/guide/topics/ui/look-and-feel/downloadable-fonts.html) раздела.



### <a name="autofill"></a>Автозаполнение

Новый _Автозаполнение_ framework в Android Oreo упрощает для пользователей для обработки повторяющихся задач, таких как имя входа, создание учетной записи и транзакций с кредитными картами. Пользователи тратят меньше времени, повторно вводить сведения (что может привести к входных данных ошибок). Прежде чем приложение может работать с Autofill Framework, то должна быть включена служба Автозаполнение, в параметрах системы (пользователей можно включить или отключить автозаполнение).

[AutofillFramework](https://developer.xamarin.com/samples/monodroid/android-o/AutoFillFramework/) образце демонстрируется использование Autofill Framework. В нем реализованы клиента действий с представлениями, которые должны быть autofilled и служба, которая может предоставлять данные Автозаполнение клиенту действий.

Дополнительные сведения о новой функции автозаполнения и как оптимизировать приложения для автозаполнения, см. в разделе разработчика Android [Autofill Framework](https://developer.android.com/guide/topics/text/autofill.html) раздела.



### <a name="picture-in-picture-pip"></a>Картинка в картинке (PIP)

Android Oreo делает возможным для действия для запуска в режиме рисунок in-picture (PIP), наложения на экране другого действия. Эта функция предназначена для воспроизведения видео.

Чтобы указать, что действие вашего приложения можно использовать режим PIP, задайте следующий флаг в значение true в манифесте Android:

```xml
android:supportsPictureInPicture
```

Чтобы указать поведение действие, когда он находится в режиме PIP, используйте новый [PictureInPictureParams](https://developer.android.com/reference/android/app/PictureInPictureParams.html) объекта. `PictureInPictureParams` Представляет набор параметров, используемых для инициализации и обновления действие в режиме PIP (например, действия предпочтительный пропорции). Были добавлены следующие новые методы PIP `Activity` в Android Oreo:

-   [EnterPictureInPictureMode](https://developer.android.com/reference/android/app/Activity.html#enterPictureInPictureMode%28android.app.PictureInPictureParams%29) &ndash; переводит действие в режиме PIP. Действие помещается в углу экрана, а остальная часть экрана заполняется предыдущего действия, которое было на экране.

-   [SetPictureInPictureParams](https://developer.android.com/reference/android/app/Activity.html#setPictureInPictureParams%28android.app.PictureInPictureParams%29) &ndash; обновляет параметры конфигурации PIP действия (например, изменения в пропорции).

[PictureInPicture](https://developer.xamarin.com/samples/monodroid/android-o/PictureInPicture) в нем демонстрируется использование базового приложения режим рисунок in-Picture (PiP) для портативных устройств, представленные в Oreo. Образец воспроизведение видео, которая по-прежнему не прерывать при переключении между режимами отображения или выполнения других действий.



### <a name="other-features"></a>Другие возможности

Android Oreo содержит множество новых функций, таких как библиотеки, Location API фона Эмодзи поддержки ограничения, широким цвет для приложений, новый аудиокодеком, усовершенствования веб-представления, поддержка переходов улучшенные клавиатуры и новый интерфейс API AAudio (pro звук) для аудио низкой задержкой высокой производительности, Дополнительные сведения об этих функциях см. в разделе разработчика Android [Android Oreo функции и API](https://developer.android.com/about/versions/oreo/android-8.0.html) раздела.



## <a name="behavior-changes"></a>Изменения в поведении

Android Oreo включает разнообразные системы и изменения в поведении API, которые могут повлиять на функциональные возможности существующих приложений. Ниже приводится описание этих изменений.


### <a name="background-execution-limits"></a>Ограничения выполнения фона

Чтобы улучшить взаимодействие с пользователем, в Android Oreo накладывает ограничения на приложения, которые можно сделать во время выполнения в фоновом режиме. Например при просмотре видео или игры пользователем приложение, запущенное в фоновом режиме может значительно снизить производительность приложения интенсивно видео, выполнение на переднем плане. В результате Android Oreo действуют следующие ограничения на приложения, которые напрямую не взаимодействует с пользователем:

1.  **Фоновые службы ограничениями** &ndash; когда приложение выполняется в фоновом режиме, он имеет несколько минут, в котором он является по-прежнему могут создавать и использовать службы окна. В конце этого окна, Android, останавливает фоновой службы приложения и обрабатывает его как _простоя_.

2.  **Рассылка ограничения** &ndash; Android 7.0 (API 25) уделено ограничения широковещательные рассылки, приложение регистрируется для получения. Android Oreo делает более жесткими, эти ограничения. Например приложения Android Oreo больше не могут регистрироваться широковещательных получателей для неявного широковещательные рассылки в своих манифестах.

Дополнительные сведения о новых ограничений фонового выполнения, см. в разделе разработчика Android [ограничения выполнения фоновой](https://developer.android.com/about/versions/oreo/background.html) раздела.


### <a name="breaking-changes"></a>Критические изменения

Приложений, предназначенных для Android Oreo или более поздней версии, необходимо изменить свои приложения для поддержки следующих изменений, где это возможно:

- Android Oreo мы отказались от возможность устанавливать приоритет отдельные уведомления. Вместо этого рекомендуется важности задать при создании канала уведомления. Уровень важности, присваиваемый канала уведомлений с помощью применяется для всех сообщений уведомления с вашим к нему.

- Для приложений, предназначенных для Android Oreo `PendingIntent.GetService()` не работает из-за нового пределы, наложенные на службы запущены в фоновом режиме. Если вы используете Android Oreo, следует использовать [PendingIntent.GetBroadcast](https://developer.xamarin.com/api/member/Android.App.PendingIntent.GetBroadcast/p/Android.Content.Context/System.Int32/Android.Content.Intent/Android.App.PendingIntentFlags/) вместо этого.  


## <a name="sample-code"></a>Пример кода

Чтобы показать, как воспользоваться преимуществами функций Android Oreo доступны несколько примеров Xamarin.Android:

-   [NotificationsChannels](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels) показано, как использовать новую систему каналов уведомлений, представленные в Android Oreo. В этом примере управляет два канала уведомления: один с важности по умолчанию, а другой — с высоким уровнем важности.

-   [PictureInPicture](https://developer.xamarin.com/samples/monodroid/android-o/PictureInPicture) демонстрируется использование базового приложения режим рисунок in-Picture (PiP) для портативных устройств, представленные в Oreo. Образец воспроизведение видео, которая по-прежнему не прерывать при переключении между режимами отображения или выполнения других действий.

-   [AutofillFramework](https://developer.xamarin.com/samples/monodroid/android-o/AutoFillFramework) демонстрируется использование функции автозаполнения Framework. В нем реализованы клиента действий с представлениями, которые должны быть autofilled и служба, которая может предоставлять данные Автозаполнение клиенту действий.

-   [Загружаемые шрифты](https://developer.xamarin.com/samples/monodroid/android-o/DownloadableFonts) пример демонстрирует использование шрифтов компонент, описанный ранее.

-   [EmojiCompat](https://developer.xamarin.com/samples/monodroid/android-o/EmojiCompat) демонстрирует использование библиотеки поддержки EmojiCompat. Чтобы избежать приложения из отображение недостающие эмодзи символы как символы «диаграмме» можно использовать эту библиотеку.

-   [Расположение обновлений Pending Intent](https://developer.xamarin.com/samples/monodroid/android-o/AndroidPlayLocation/LocUpdPendIntent) иллюстрирует использование API Location для получения обновлений, сведения о расположении устройства с помощью `PendingIntent`.

-   [Службы переднего плана обновления расположения](https://developer.xamarin.com/samples/monodroid/android-o/AndroidPlayLocation/LocUpdFgService) демонстрирует использование Location API для получения обновлений о местоположении устройства, с помощью службы переднего плана связанные и началу работы.


## <a name="video"></a>Видео

> [!VIDEO https://youtube.com/embed/OuvEcaMO-Ho]

**Android 8.0 Oreo разработки с помощью C#**


## <a name="summary"></a>Сводка

В этой статье представлены Android Oreo и было рассмотрено, как установить и настроить новейшие средства и пакеты для разработки приложений Xamarin.Android в Android Oreo. Она предоставляла с обзором ключевых функциях, доступных в Android Oreo со ссылками на примере исходного кода для несколько новых возможностей. Он включается ссылки на документацию по API и разделы разработчика Android, которые помогут приступить к созданию приложений для Android Oreo. Кроме того, он выделен наиболее важные изменения поведения Android Oreo, которые могут повлиять на существующие приложения.


## <a name="related-links"></a>Связанные ссылки

- [Android 8.0 Oreo](https://developer.android.com/index.html)
