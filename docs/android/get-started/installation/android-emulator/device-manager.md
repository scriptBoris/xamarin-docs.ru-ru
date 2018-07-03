---
title: Управление виртуальными устройствами с помощью Android Device Manager
description: Это руководство описывает использование Android Device Manager для создания и настройки виртуальных устройств Android (AVD), эмулирующих физические устройства Android. Эти виртуальные устройства позволяют запустить и протестировать приложение без использования физического устройства.
ms.prod: xamarin
ms.assetid: ECB327F3-FF1C-45CC-9FA6-9C11032BD5EF
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/22/2018
ms.openlocfilehash: a7c1aeafd94d7e2639617cda13312ee8a09e2c94
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935332"
---
# <a name="managing-virtual-devices-with-the-android-device-manager"></a>Управление виртуальными устройствами с помощью Android Device Manager

_Это руководство описывает использование Android Device Manager для создания и настройки виртуальных устройств Android (AVD), эмулирующих физические устройства Android. Эти виртуальные устройства позволяют запускать и тестировать приложение без использования физического устройства._

## <a name="overview"></a>Обзор

Убедившись, что аппаратное ускорение включено (как описано в статье [Аппаратное ускорение для производительной работы эмулятора](~/android/get-started/installation/android-emulator/hardware-acceleration.md)), воспользуйтесь _Android Device Manager_ (который также называют _Xamarin Android Device Manager_), чтобы создать виртуальные устройства, которые можно использовать для тестирования и отладки приложения.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Эта статья поясняет, как создавать, дублировать, настраивать и запускать виртуальные устройства Android с помощью Android Device Manager.

[![Снимок экрана с Android Device Manager на вкладке устройств](device-manager-images/win/01-devices-dialog-sml.png)](device-manager-images/win/01-devices-dialog.png#lightbox)

Android Device Manager предназначен для создания и настройки _виртуальных устройств Android_ (AVD), запускаемых в [Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md).
Каждое AVD является конфигурацией эмулятора, имитирующей физическое устройство Android. Это позволяет запускать и тестировать приложение в различных конфигурациях, имитирующих различные физические устройства Android.

## <a name="requirements"></a>Требования

Чтобы использовать Android Device Manager, необходимо следующее:

-   Требуется Visual Studio 2017 версии 15.7 или более поздней. Поддерживаются выпуски Visual Studio Community, Professional и Enterprise.

-   Инструменты Visual Studio для Xamarin версии 4.9 или более поздней.

-   Нужно установить пакет SDK для Android (см. раздел [Настройка пакета SDK для Android для Xamarin.Android](~/android/get-started/installation/android-sdk.md)) и SDK Tools версии 26.1.1 или более поздней, как описано в следующем разделе. Обязательно установите пакет SDK для Android в следующее расположение (если это еще не сделано): **C:\\Program Files (x86)\\Android\\android-sdk**.


## <a name="launching-the-device-manager"></a>Запуск диспетчера устройств

Запустите Android Device Manager в меню **Сервис**, выбрав **Сервис > Android > Диспетчер устройств с Android**:

[![Запуск из меню "Сервис"](device-manager-images/win/04-tools-menu-sml.png)](device-manager-images/win/04-tools-menu.png#lightbox)

Если вы видите следующее диалоговое окно ошибки при запуске, см. инструкции по обходу в разделе [Устранение неполадок в Android Emulator](~/android/get-started/installation/android-emulator/troubleshooting.md).

![Ошибка экземпляра SDK для Android](device-manager-images/win/32-sdk-error.png)

Прежде чем использовать Android Device Manager, нужно установить Android SDK Tools версии 26.1.1 или более поздней. При отсутствии Android SDK Tools версии 26.1.1 или более поздней при запуске может появиться следующее диалоговое окно ошибки:

![Диалоговое окно ошибки экземпляра SDK для Android](device-manager-images/win/02-sdk-instance-error.png)

Если вы видите это диалоговое окно ошибки, щелкните **ОК**, чтобы открыть диспетчер пакетов SDK для Android. В диспетчере пакетов SDK для Android откройте вкладку **Сервис** и установите следующее:

-   **Android SDK Tools 26.1.1** или более поздней версии 
-   **Средства платформы пакета SDK для Android 27.0.1** или более поздней версии  
-   **Средства сборки пакета SDK для Android 27.0.3** или более поздней версии

Эти пакеты должны отображаться с состоянием **Установлено**, как показано на следующем снимке экрана:

[![Установка Android SDK Tools 27.0](device-manager-images/win/03-sdk-tools-sml.png)](device-manager-images/win/03-sdk-tools.png#lightbox)

После установки этих пакетов можно закрыть диспетчер пакетов SDK и повторно запустить диспетчер устройств Android.

## <a name="main-screen"></a>Главный экран

При первом запуске диспетчер устройств Android выводит экран, где отображаются все настроенные виртуальные устройства. Для каждого устройства указаны параметры **Имя**, **Операционная система** (уровень API Android), **ЦП**, **Память**, а также разрешение экрана:

[![Список установленных устройств и их параметров](device-manager-images/win/05-installed-list-sml.png)](device-manager-images/win/05-installed-list.png#lightbox)

Если щелкнуть устройство в списке, справа отображается кнопка **Запустить**. Можно нажать кнопку **Запустить**, чтобы запустить эмулятор с этим виртуальным устройством:

[![Кнопка "Запустить" для образа устройства](device-manager-images/win/06-start-button-sml.png)](device-manager-images/win/06-start-button.png#lightbox)

После запуска эмулятора с выбранным виртуальным устройством кнопка **Запустить** изменяется на кнопку **Остановить**, позволяющую остановить эмулятор:

[![Кнопка "Остановить" для работающего устройства](device-manager-images/win/07-stop-button-sml.png)](device-manager-images/win/07-stop-button.png#lightbox)

### <a name="new-device"></a>Создание устройства

Чтобы создать устройство, нажмите кнопку **Создать** в верхней правой части экрана:

[![Кнопка "Создать" для создания устройства](device-manager-images/win/08-new-button-sml.png)](device-manager-images/win/08-new-button.png#lightbox)

При нажатии кнопки **Создать** открывается экран **Новое устройство**:

[![Экран "Новое устройство" диспетчера устройств](device-manager-images/win/09-new-device-editor-sml.png)](device-manager-images/win/09-new-device-editor.png#lightbox)

Чтобы настроить новое устройство на экране **Новое устройство**, выполните следующие действия:

1. Выберите физическое устройство для эмуляции, щелкнув раскрывающееся меню **Устройство**:

    [![Раскрывающееся меню "Устройство"](device-manager-images/win/10-device-menu-sml.png)](device-manager-images/win/10-device-menu.png#lightbox)

2. Выберите образ системы для использования с этим виртуальным устройством, щелкнув раскрывающееся меню **Образ системы**. В разделе **Установлено** этого меню перечислены установленные образы диспетчера устройств системы. В разделе **Загрузки** перечислены образы диспетчера устройств системы, которые сейчас недоступны на компьютере разработчика, но могут быть установлены автоматически:

    [![Раскрывающееся меню "Образ системы"](device-manager-images/win/11-system-image-menu-sml.png)](device-manager-images/win/11-system-image-menu.png#lightbox)

3. Задайте новое имя для устройства. В следующем примере новое устройство называется **Nexus 5 API 25**:

    [![Именование нового устройства](device-manager-images/win/12-device-name-sml.png)](device-manager-images/win/12-device-name.png#lightbox)

4. Измените нужные свойства. Сведения о том, как это сделать, см. в разделе [Изменение свойств виртуальных устройств Android](~/android/get-started/installation/android-emulator/device-properties.md).

5. Добавьте любые дополнительные свойства, которые нужно задать явно. На экране **Новое устройство** перечислены только часто изменяемые свойства, но можно щелкнуть раскрывающееся меню **Добавление свойства** в нижнем левом углу, чтобы добавить дополнительные свойства. В следующем примере добавляется свойство `hw.lcd.backlight`:

    [![Раскрывающееся меню "Добавление свойства"](device-manager-images/win/13-add-property-menu-sml.png)](device-manager-images/win/13-add-property-menu.png#lightbox)

6. Нажмите кнопку **Создать** в нижнем правом углу, чтобы создать устройство:

    ![Кнопка "Создать"](device-manager-images/win/14-create-button.png)

7. Может появиться экран **Принятие условий лицензионного соглашения**. Если вы согласны с условиями лицензии, щелкните **Принять**:

    ![Экран "Принятие условий лицензионного соглашения"](device-manager-images/win/15-license-acceptance.png)

8. Пока устройство создается, Android Device Manager добавляет его в список установленных виртуальных устройств, отображая индикатор хода выполнения **Создание**:

    [![Индикатор хода выполнения "Создание"](device-manager-images/win/16-creating-the-device-sml.png)](device-manager-images/win/16-creating-the-device.png#lightbox)

9. После завершения создания новое устройство отображается в списке установленных виртуальных устройств с кнопкой **Запустить**, готовое к запуску:

   [![Только что созданное устройство, готовое к запуску](device-manager-images/win/17-created-device-sml.png)](device-manager-images/win/17-created-device.png#lightbox)


### <a name="edit-device"></a>Изменение устройства

Чтобы изменить существующее виртуальное устройство, выберите его и нажмите кнопку **Изменить** в правом верхнем углу:

[![Кнопка "Изменить" для изменения нового устройства](device-manager-images/win/19-edit-button-sml.png)](device-manager-images/win/19-edit-button.png#lightbox)

При щелчке элемента **Изменить** запускается редактор устройств для выбранного виртуального устройства:

[![Окно редактора устройств](device-manager-images/win/20-device-editor-sml.png)](device-manager-images/win/20-device-editor.png#lightbox)

На экране **Device Editor** (Редактор устройств) в первом столбце перечислены свойства виртуального устройства, а во втором — соответствующие значения для них. При выборе свойства справа отображается его подробное описание.

Например, на следующем снимке экрана свойство `hw.lcd.density` изменяется с **420** на **240**:

[![Пример изменения устройства](device-manager-images/win/21-device-editing-sml.png)](device-manager-images/win/21-device-editing.png#lightbox)

После внесения необходимых изменений в конфигурацию нажмите кнопку **Сохранить**.
Дополнительные сведения об изменении свойств виртуального устройства см. в разделе [Изменение свойств виртуальных устройств Android](~/android/get-started/installation/android-emulator/device-properties.md).

 
### <a name="additional-options"></a>Дополнительные параметры

В меню &hellip; в правом верхнем углу доступны дополнительные параметры для работы с устройствами:

[![Расположение меню с дополнительными параметрами](device-manager-images/win/22-overflow-menu-sml.png)](device-manager-images/win/22-overflow-menu.png#lightbox)

Меню с дополнительными параметрами содержит следующие элементы:

-   **Duplicate and Edit** (Дублировать и править) &ndash; дублирует выбранное устройство и открывает экран **Новое устройство** с другим уникальным именем. Например, если выбрать **VisualStudio_android-23_x86_phone** и щелкнуть **Duplicate and Edit** (Дублировать и править), к имени добавляется счетчик:

    [![Экран "Duplicate and Edit" (Дублировать и править)](device-manager-images/win/23-dupe-and-edit-sml.png)](device-manager-images/win/23-dupe-and-edit.png#lightbox)

-   **Отобразить в проводнике** &ndash; открывает окно проводника в папке с файлами для виртуального устройства. Например, если выбрать **Nexus 5X API 25** и щелкнуть **Отобразить в проводнике**, открывается окно, похожее на следующее:

    [![Результаты выбора параметра "Отобразить в проводнике"](device-manager-images/win/24-reveal-in-explorer-sml.png)](device-manager-images/win/24-reveal-in-explorer.png#lightbox)

-   **Сброс параметров** &ndash; сбрасывает выбранное устройство в параметры по умолчанию, удаляя любые изменения, внесенные пользователем во внутреннее состояние устройства во время его работы (также удаляется любой актуальный моментальный снимок [быстрой загрузки](~/android/deploy-test/debugging/debug-on-emulator.md#quick-boot) при его наличии). Эта операция не затрагивает изменения, внесенные на виртуальном устройстве во время его создания и редактирования. Отображается диалоговое окно с напоминанием, что этот сброс невозможно отменить. Нажмите кнопку **Wipe user data** (Очистить пользовательские данные), чтобы подтвердить сброс.

-   **Удалить** &ndash; окончательно удаляет выбранное виртуальное устройство.
    Отображается диалоговое окно с напоминанием, что такое удаление невозможно отменить. Щелкните **Удалить**, если уверены, что устройство нужно удалить.


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Эта статья поясняет, как создавать, дублировать, настраивать и запускать виртуальные устройства Android с помощью Android Device Manager.

[![Снимок экрана с Android Device Manager на вкладке устройств](device-manager-images/mac/01-devices-dialog-sml.png)](device-manager-images/mac/01-devices-dialog.png#lightbox)

> [!NOTE]
> Это руководство распространяется только на Visual Studio для Mac.
Xamarin Studio не совместим с Android Device Manager.

Android Device Manager предназначен для создания и настройки *виртуальных устройств Android* (AVD), запускаемых в [Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md).
Каждое AVD является конфигурацией эмулятора, имитирующей физическое устройство Android. Это позволяет запускать и тестировать приложение в различных конфигурациях, имитирующих различные физические устройства Android.

## <a name="requirements"></a>Требования

- Visual Studio для Mac 7.5 или более поздней версии.

- Нужно установить пакет SDK для Android 8.0 (API 26) или более поздней версии через диспетчер пакетов SDK для Android.


## <a name="launching-the-device-manager"></a>Запуск диспетчера устройств

Запустите Android Device Manager, выбрав **Сервис > Диспетчер устройств**:

[![Запуск из меню "Сервис"](device-manager-images/mac/16-tools-menu-sml.png)](device-manager-images/mac/16-tools-menu.png#lightbox)

Прежде чем использовать Android Device Manager, нужно установить Android SDK Tools версии 26.0.2 или более поздней. При отсутствии Android SDK Tools версии 26.0.2 или более поздней при запуске появится следующее диалоговое окно ошибки:

![Диалоговое окно ошибки экземпляра SDK для Android](device-manager-images/mac/02-sdk-instance-error.png)

Если вы видите это диалоговое окно ошибки, нажмите кнопку **ОК**, чтобы открыть диспетчер пакетов SDK для Android. В диспетчере пакетов SDK для Android откройте вкладку **Сервис** и установите следующее:

-   **Android SDK Tools 26.0.2** или более поздней версии 
-   **Средства платформы пакета SDK для Android 26.0.0** или более поздней версии 
-   **Средства сборки пакета SDK для Android 26.0.0** или более поздней версии

Эти пакеты должны отображаться с состоянием **Установлено**, как показано на следующем снимке экрана:

[![Установка Android SDK Tools 26.0](device-manager-images/mac/03-sdk-tools-sml.png)](device-manager-images/mac/03-sdk-tools.png#lightbox)

## <a name="main-screen"></a>Главный экран

При первом запуске диспетчер устройств Android выводит экран, где отображаются все настроенные виртуальные устройства. Для каждого устройства указаны параметры **Имя**, **Образ системы** (уровень API Android), **ЦП**, **Память**, а также разрешение экрана:

[![Список установленных устройств и их параметров](device-manager-images/mac/05-devices-list-sml.png)](device-manager-images/mac/05-devices-list.png#lightbox)

Нажмите **Воспроизвести**, чтобы запустить эмулятор с выбранным виртуальным устройством:

[![Кнопка "Запустить" для образа устройства](device-manager-images/mac/06-start-button-sml.png)](device-manager-images/mac/06-start-button.png#lightbox)

После запуска эмулятора с выбранным виртуальным устройством кнопка **Воспроизвести** изменяется на кнопку **Остановить**, позволяющую остановить эмулятор:

[![Кнопка "Остановить" для работающего устройства](device-manager-images/mac/07-stop-button-sml.png)](device-manager-images/mac/07-stop-button.png#lightbox)

### <a name="new-device"></a>Создание устройства

Чтобы создать устройство, нажмите кнопку **Создать устройство** в верхней правой части экрана:

[![Кнопка "Создать" для создания устройства](device-manager-images/mac/08-new-button-sml.png)](device-manager-images/mac/08-new-button.png#lightbox)

При нажатии кнопки **Создать устройство** открывается экран **Новое устройство**:

[![Экран "Новое устройство" диспетчера устройств](device-manager-images/mac/09-new-device-editor-sml.png)](device-manager-images/mac/09-new-device-editor.png#lightbox)

Чтобы настроить новое устройство на экране **Новое устройство**, выполните следующие действия:

1. Выберите физическое устройство для эмуляции, щелкнув раскрывающееся меню **Устройство**:

    [![Раскрывающееся меню "Устройство"](device-manager-images/mac/10-device-menu-sml.png)](device-manager-images/mac/10-device-menu.png#lightbox)

2. Выберите образ системы для использования с этим виртуальным устройством, щелкнув раскрывающееся меню **Образ системы**. В разделе **Установлено** этого меню перечислены установленные образы диспетчера устройств системы. В разделе **Загрузки** (при его наличии) перечислены образы диспетчера устройств системы, которые сейчас недоступны на компьютере разработчика, но могут быть установлены автоматически:

    [![Раскрывающееся меню "Образ системы"](device-manager-images/mac/11-system-image-menu-sml.png)](device-manager-images/mac/11-system-image-menu.png#lightbox)

3. Задайте новое имя для устройства. В следующем примере новое устройство называется **Nexus 5X API 25**:

    [![Именование нового устройства](device-manager-images/mac/12-device-name-sml.png)](device-manager-images/mac/12-device-name.png#lightbox)

4. Измените нужные свойства. Сведения о том, как это сделать, см. в разделе [Изменение свойств виртуальных устройств Android](~/android/get-started/installation/android-emulator/device-properties.md).

5. Добавьте любые дополнительные свойства, которые нужно задать явно. На экране **Новое устройство** перечислены только часто изменяемые свойства, но можно щелкнуть раскрывающееся меню **Добавление свойства** в нижнем левом углу, чтобы добавить дополнительные свойства:

    [![Раскрывающееся меню "Добавление свойства"](device-manager-images/mac/13-add-property-menu-sml.png)](device-manager-images/mac/13-add-property-menu.png#lightbox)

6. Можно также щелкнуть элемент **Настраиваемый**, чтобы определить новое свойство для устройства:

    ![Кнопка "Создать"](device-manager-images/mac/14-custom-button.png)

7. Нажмите кнопку **Создать** в нижнем правом углу, чтобы создать устройство:

    ![Кнопка "Создать"](device-manager-images/mac/15-create-button.png)

8. Может появиться экран **Принятие условий лицензионного соглашения**. Если вы согласны с условиями лицензии, щелкните **Принять**.

9. Пока устройство создается, Android Device Manager добавляет его в список установленных виртуальных устройств, отображая индикатор хода выполнения **Создание**:

    [![Индикатор хода выполнения "Создание"](device-manager-images/mac/17-creating-the-device-sml.png)](device-manager-images/mac/17-creating-the-device.png#lightbox)

10. После завершения создания новое устройство отображается в списке устройств с кнопкой **Воспроизвести**, готовое к запуску:

   [![Только что созданное устройство, готовое к запуску](device-manager-images/mac/18-created-device-sml.png)](device-manager-images/mac/18-created-device.png#lightbox)


### <a name="edit-device"></a>Изменение устройства

Для изменения существующего виртуального устройства щелкните раскрывающееся меню **Дополнительные параметры** (значок шестеренки) и выберите **Изменить**:
 
[![Пункт меню "Изменить" для изменения нового устройства](device-manager-images/mac/19-edit-button-sml.png)](device-manager-images/mac/19-edit-button.png#lightbox)

При щелчке элемента **Изменить** запускается редактор устройств для выбранного виртуального устройства:
 
[![Окно редактора устройств](device-manager-images/mac/20-device-editor-sml.png)](device-manager-images/mac/20-device-editor.png#lightbox)

На экране **Device Editor** (Редактор устройств) в первом столбце перечислены свойства виртуального устройства, а во втором — соответствующие значения для них. При выборе свойства справа отображается его подробное описание.

Например, на следующем снимке экрана свойство `hw.lcd.density` изменяется с **320** на **240**, а свойство `hw.ramSize` изменяется на **768**:
 
[![Пример изменения устройства](device-manager-images/mac/21-device-editing-sml.png)](device-manager-images/mac/21-device-editing.png#lightbox)

После внесения необходимых изменений в конфигурацию нажмите кнопку **Сохранить**.
Дополнительные сведения об изменении свойств виртуального устройства см. в разделе [Изменение свойств виртуальных устройств Android](~/android/get-started/installation/android-emulator/device-properties.md).

 
### <a name="additional-options"></a>Дополнительные параметры

Дополнительные параметры для работы с устройством доступны в раскрывающемся меню, расположенном слева от кнопки **Воспроизвести**:

[![Расположение меню с дополнительными параметрами](device-manager-images/mac/22-overflow-menu-sml.png)](device-manager-images/mac/22-overflow-menu.png#lightbox)

Меню с дополнительными параметрами содержит следующие элементы:

-   **Изменить** &ndash; открывает выбранное устройство в редакторе устройств, как описано выше.

-   **Duplicate and Edit** (Дублировать и править) &ndash; дублирует выбранное устройство и открывает экран **Новое устройство** с другим уникальным именем.
    Например, если выбрать **Nexus 5X API 25** и щелкнуть **Duplicate and Edit** (Дублировать и править), к имени добавляется счетчик:

    [![Экран "Duplicate and Edit" (Дублировать и править)](device-manager-images/mac/23-dupe-and-edit-sml.png)](device-manager-images/mac/23-dupe-and-edit.png#lightbox)

-   **Отобразить в Finder** &ndash; открывает окно macOS Finder в папке с файлами для виртуального устройства. Например, если выбрать **Nexus 5X API 25** и щелкнуть **Отобразить в Finder**, открывается окно, похожее на следующее:

    [![Результаты выбора параметра "Отобразить в проводнике"](device-manager-images/mac/24-reveal-in-finder-sml.png)](device-manager-images/mac/24-reveal-in-finder.png#lightbox)

-   **Сброс параметров** &ndash; сбрасывает выбранное устройство в параметры по умолчанию, удаляя любые изменения, внесенные пользователем во внутреннее состояние устройства во время его работы. Эта операция не затрагивает изменения, внесенные на виртуальном устройстве во время его создания и редактирования. Отображается диалоговое окно с напоминанием, что этот сброс невозможно отменить. Нажмите кнопку **Wipe user data** (Очистить пользовательские данные), чтобы подтвердить сброс.

-   **Удалить** &ndash; окончательно удаляет выбранное виртуальное устройство.
    Отображается диалоговое окно с напоминанием, что такое удаление невозможно отменить. Щелкните **Удалить**, если уверены, что устройство нужно удалить.

-----

## <a name="troubleshooting"></a>Устранение неполадок

В следующих разделах описано, как диагностировать и устранять неполадки, которые могут возникать при использовании Android Device Manager для настройки виртуальных устройств.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

### <a name="android-sdk-in-non-standard-location"></a>Пакет SDK для Android в нестандартном расположении

Как правило, пакет SDK для Android устанавливается в следующем расположении:

**C:\\Program Files (x86)\\Android\\android-sdk**

Если пакет SDK не установлен в этом расположении, при запуске Android Device Manager может возникнуть следующая ошибка:

![Ошибка экземпляра SDK для Android](troubleshooting-images/win/01-sdk-error.png)

Чтобы избежать этой проблемы, сделайте следующее:

1. На рабочем столе Windows перейдите в **C:\\Users\\*имя_пользователя*\\AppData\\Roaming\\XamarinDeviceManager**:

    ![Расположение файла журнала для Android Device Manager](troubleshooting-images/win/02-log-files.png)

2. Дважды щелкните, чтобы открыть один из файлов журнала, и найдите **Путь к файлу конфигурации**. Пример:

    [![Путь к файлу конфигурации в файле журнала](troubleshooting-images/win/03-config-file-path-sml.png)](troubleshooting-images/win/03-config-file-path.png#lightbox)

3. Перейдите в это расположение и дважды щелкните **user.config**, чтобы открыть его. 

4. В **user.config** найдите элемент **&lt;UserSettings&gt;** и добавьте к нему атрибут **AndroidSdkPath**. Задайте для этого атрибута путь, по которому установлен пакет SDK для Android на вашем компьютере, и сохраните файл. Например, **&lt;UserSettings&gt;** будет выглядеть следующим образом, если пакет SDK для Android был установлен в **C:\\Programs\\Android\\SDK**:
        
    ```xml
    <UserSettings SdkLibLastWriteTimeUtcTicks="636409365200000000" AndroidSdkPath="C:\Programs\Android\SDK" />
    ```

Внесение этого изменения в **user.config** позволит запустить Android Device Manager.

### <a name="snapshot-disables-wifi-on-android-oreo"></a>При создании моментального снимка в Android Oreo отключается Wi-Fi

Если для Android Oreo настроено виртуальное устройство AVD с имитацией доступа по Wi-Fi, перезапуск AVD после создания моментального снимка может привести к отключению доступа по Wi-Fi.

Действия по решению этой проблемы:

1. Выберите AVD в Android Device Manager.

2. В меню дополнительных параметров выберите пункт **Отобразить в проводнике**.

3. Последовательно выберите **snapshots (моментальные снимки) > default_boot**.

4. Удалите файл **snapshot.pb**:

    ![Расположение файла snapshot.pb](troubleshooting-images/win/05-delete-snapshot.png)

5. Перезапустите AVD. 

После внесения этих изменений AVD перезапустится в состоянии, которое позволяет возобновить работу Wi-Fi.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

### <a name="snapshot-disables-wifi-on-android-oreo"></a>При создании моментального снимка в Android Oreo отключается Wi-Fi

Если для Android Oreo настроено виртуальное устройство AVD с имитацией доступа по Wi-Fi, перезапуск AVD после создания моментального снимка может привести к отключению доступа по Wi-Fi.

Действия по решению этой проблемы:

1. Выберите AVD в Android Device Manager.

2. В меню дополнительных параметров выберите пункт **Отобразить в средстве поиска**.

3. Последовательно выберите **snapshots (моментальные снимки) > default_boot**.

4. Удалите файл **snapshot.pb**:

    [![Расположение файла snapshot.pb](troubleshooting-images/mac/02-delete-snapshot-sml.png)](troubleshooting-images/mac/02-delete-snapshot.png#lightbox)

5. Перезапустите AVD. 

После внесения этих изменений AVD перезапустится в состоянии, которое позволяет возобновить работу Wi-Fi.

-----

### <a name="generating-a-bug-report"></a>Создание отчета об ошибках

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Если вы обнаружили проблему с Android Device Manager, которую не удается разрешить с помощью приведенных выше советов, отправьте отчет об ошибках. Для этого щелкните правой кнопкой мыши строку заголовка и выберите пункт **Generate Bug Report** (Создать отчет об ошибках):

[![Расположение пункта меню для заполнения отчета об ошибках](troubleshooting-images/win/04-bug-report-sml.png)](troubleshooting-images/win/04-bug-report.png#lightbox)


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Если вы обнаружили проблему с Android Device Manager, которую не удается решить с помощью приведенных выше советов, отправьте отчет об ошибках. Для этого последовательно выберите **Help > Generate Bug Report** (Справка > Создать отчет об ошибках):

[![Расположение пункта меню для заполнения отчета об ошибках](troubleshooting-images/mac/01-bug-report-sml.png)](troubleshooting-images/mac/01-bug-report.png#lightbox)


-----

## <a name="summary"></a>Сводка

Это руководство содержит общие сведения об Android Device Manager, доступном в Visual Studio для Mac и Инструментах Visual Studio для Xamarin. Оно описывает важные функции, такие как запуск и остановка эмулятора Android, выбор виртуального устройства Android (AVD) для запуска, создание и изменение виртуальных устройств. В нем рассказывается, как редактировать свойства профиля оборудования для дальнейшей настройки, и приводятся советы по устранению распространенных неполадок.


## <a name="related-links"></a>Связанные ссылки

- [Изменения в инструментарии пакета SDK для Android](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [Отладка приложений в Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md)
- [Заметки о выпуске пакета SDK Tools (Google)](https://developer.android.com/studio/releases/sdk-tools)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
