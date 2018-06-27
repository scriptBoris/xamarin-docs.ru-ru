---
title: Настройка пакета SDK для Android для Xamarin.Android
description: В состав Visual Studio входит диспетчер пакетов SDK для Android, который заменяет автономный диспетчер пакетов SDK от Google. Это руководство содержит сведения об использовании диспетчера пакетов SDK для скачивания инструментов, платформ и других компонентов пакета SDK для Android, необходимых для разработки приложений Xamarin.Android.
ms.prod: xamarin
ms.assetid: 9A857F52-2EC1-414F-8010-CEE67B60A4B4
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/02/2018
ms.openlocfilehash: 0af0ef56630103498041ad61f7c5ce900358b055
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34732857"
---
# <a name="setting-up-the-android-sdk-for-xamarinandroid"></a>Настройка пакета SDK для Android для Xamarin.Android

_Visual Studio включает диспетчер пакетов SDK для Android, который заменяет автономный диспетчер пакетов SDK от Google. В этом руководстве описано, как использовать диспетчер пакетов SDK для скачивания инструментов, платформ и других компонентов пакета SDK для Android, необходимых для разработки приложений Xamarin.Android._


## <a name="overview"></a>Обзор

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Это руководство описывает, как установить и использовать диспетчер пакетов SDK Xamarin Android для Visual Studio в Windows (или [для Mac](?tabs=vsmac)).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Это руководство описывает, как установить и использовать диспетчер пакетов SDK Xamarin Android для Visual Studio для Mac (или [для Windows](?tabs=vswin)).

> [!NOTE]
> Это руководство распространяется только на Visual Studio 2017 и Visual Studio для Mac.  

-----

Диспетчер пакетов SDK Xamarin Android помогает скачивать новейшие компоненты Android, необходимые для разработки приложения Xamarin.Android.
Он заменяет автономный диспетчер пакетов SDK от Google, который был признан нерекомендуемым.

Почему следует использовать диспетчер пакетов SDK Xamarin Android вместо диспетчера пакетов SDK, входящего в состав пакета SDK для Android? В версии 25.2.3 пакета Android SDK Tools компания Google ввела новый инструмент для обслуживания пакета SDK для Android. Этот новый инструмент — **[sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)**  — является программой командной строки, которая заменяет автономный диспетчер на базе пользовательского интерфейса для пакета SDK для Android. Таким образом, если вы выполняете обновление до SDK Tools версии 26.0.1 (требуется для Android 8.0) или более поздней версии и хотите и дальше управлять пакетом SDK для Android через пользовательский интерфейс, нужно использовать диспетчер пакетов SDK Xamarin Android.

## <a name="requirements"></a>Требования

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Чтобы использовать диспетчер пакетов SDK Xamarin Android, необходимо следующее:

- Visual Studio 2017 (выпуск Community, Professional или Enterprise). Visual Studio 2017 версии 15.5 или более поздней.

- Инструменты Visual Studio для Xamarin версии 4.5.0 или более поздней. 

Диспетчер пакетов SDK Xamarin Android не совместим с Visual Studio
2015. Пользователям Visual Studio 2015 следует использовать инструменты диспетчера пакетов SDK, предоставляемые в пакете SDK для Android от Google.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

-   Visual Studio для Mac 7.0.0.3146 или более поздней версии.

-----

Диспетчеру пакетов SDK Xamarin Android также требуется Java Development Kit (которая устанавливается автоматически вместе с Xamarin.Android).
Xamarin.Android использует пакет [JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html), который необходим при разработке для API уровня 24 или выше (пакет JDK 8 также поддерживает уровни API ниже 24). При разработке специально для уровня API 23 или ниже можно продолжать использовать пакет [JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html).

> [!IMPORTANT]
> Xamarin.Android не поддерживает пакет JDK 9.


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="installation"></a>Установка

Диспетчер пакетов SDK Xamarin можно добавить в Visual Studio 2017 во время установки. При установке Visual Studio щелкните **Отдельные компоненты** и прокрутите вниз до раздела **Действия разработки**.
Включите **Диспетчер пакетов SDK Xamarin**, если он еще не выбран:

![Включение диспетчера пакетов SDK Xamarin из отдельных компонентов](android-sdk-images/win/01-sdk-manager-install.png)

Если вы уже установили Visual Studio 2017, см. раздел [Изменение Visual Studio 2017](https://docs.microsoft.com/en-us/visualstudio/install/modify-visual-studio) с инструкциями по изменению Visual Studio, а затем выполните описанную выше процедуру, чтобы включить диспетчер пакетов SDK для Xamarin. Если отображается запрос на обновление диспетчера пакетов SDK, вы можете воспользоваться той же процедурой для установки диспетчера пакетов SDK Xamarin.

Если выбрать **Сервис > Android > Диспетчер пакетов SDK Android** (как описано ниже), диспетчер пакетов SDK Xamarin Android будет запускаться вместо диспетчера пакетов SDK Android от Google. При использовании более ранней версии пакета SDK для Android, которая поддерживает автономный диспетчер пакетов SDK Android от Google, установка диспетчера пакетов SDK Xamarin Android не приведет к конфликту &ndash; вы по-прежнему сможете запустить автономный диспетчер пакетов SDK от Google за пределами Visual Studio, чтобы управлять пакетами SDK для Android.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)
 
-----

 
## <a name="sdk-manager"></a>Диспетчер пакетов SDK 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Чтобы запустить диспетчер пакетов SDK в Visual Studio, щелкните **Сервис > Android > Диспетчер пакетов SDK Android**:

[![Элемент меню с расположением диспетчера пакетов SDK Android](android-sdk-images/win/02-sdk-manager-menu-item-sml.png)](android-sdk-images/win/02-sdk-manager-menu-item.png#lightbox)

**Диспетчер пакетов SDK Xamarin Android** открывается на экране **Пакеты SDK и инструменты для Android**. Этот экран содержит две вкладки &ndash; **Платформы** и **Сервис**:

[![Снимок экрана с диспетчером пакетов SDK Android, открытым на вкладке "Платформы"](android-sdk-images/win/03-sdk-manager-platforms-sml.png)](android-sdk-images/win/03-sdk-manager-platforms.png#lightbox)

Экран **Пакеты SDK и инструменты для Android** более подробно описан в следующих разделах.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Чтобы запустить диспетчер пакетов SDK в Visual Studio для Mac, щелкните **Сервис > Диспетчер пакетов SDK**:
 
![Элемент меню с расположением диспетчера пакетов SDK Android](android-sdk-images/mac/sdkmanager-01.png )

**Диспетчер пакетов SDK Android** открывается в **окне параметров**, содержащем три вкладки — **Платформы**, **Сервис** и **Расположения**:

![Снимок экрана с диспетчером пакетов SDK Android, открытым на вкладке "Платформы"](android-sdk-images/mac/sdkmanager-02.png)

Вкладки диспетчера пакетов SDK Xamarin Android описаны в следующих разделах.

-----



# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

### <a name="android-sdk-location"></a>Расположение пакета SDK для Android

Расположение пакета SDK для Android настраивается в верхней части экрана **Пакеты SDK и инструменты для Android**, как показано на предыдущем рисунке. Это расположение нужно настроить для правильной работы вкладок **Платформы** и **Сервис**. Задание расположения пакета SDK для Android может потребоваться по одной или нескольким из следующих причин:

1. Диспетчеру пакетов SDK Xamarin не удалось найти пакет SDK для Android. 

2. Вы установили пакет SDK для Android в альтернативное расположение (отличное от используемого по умолчанию). 

Чтобы задать расположение пакета SDK для Android, нажмите кнопку &hellip; справа от элемента **Расположение пакета Android SDK**. При этом открывается диалоговое окно **Обзор папок**, в котором можно перейти к расположению пакета SDK для Android. На следующем снимке экрана выбран пакет SDK для Android в **Program Files (x86)\\Android**:

![Снимок экрана с диалоговым окном Windows "Обзор папок", указывающим расположение пакета SDK для Android](android-sdk-images/win/05-browse-for-folder.png)

При нажатии кнопки **ОК** диспетчер Xamarin пакетов SDK Android начнет управлять пакетом SDK для Android, установленным в выбранном расположении.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

### <a name="locations-tab"></a>Вкладка "Расположения"

Вкладка **Расположения** содержит три параметра для настройки расположений пакета SDK для Android, пакета NDK для Android и пакет SDK для Java (JDK). Эти расположения нужно настроить для правильной работы вкладок **Платформы** и **Сервис**.

При запуске диспетчер пакетов SDK автоматически определяет путь для каждого установленного пакета и указывает, что он был **обнаружен**, размещая зеленую галочку рядом с путем:

![Снимок экрана вкладки "Расположения"](android-sdk-images/mac/sdkmanager-03.png)

Нажмите кнопку **Сбросить к значениям по умолчанию**, чтобы диспетчер пакетов SDK искал пакеты SDK, NDK и JDK в расположениях по умолчанию. 

Как правило, вкладка **Расположения** используется, чтобы изменить расположение пакета SDK для Android и (или) пакета JDK для Java. Вам не нужно устанавливать пакет NDK для разработки приложений Xamarin.Android &ndash; NDK используется только в том случае, когда нужно разрабатывать части приложения с использованием языков машинного кода, таких как C и C++.

-----


### <a name="tools-tab"></a>Вкладка "Сервис"

Вкладка **Сервис** отображает список _инструментов_ и _дополнений_. Эта вкладка используется для установки инструментов пакета SDK для Android, инструментов платформы и инструментов сборки.
Кроме того, можно установить эмулятор Android, низкоуровневый отладчик (LLDB), NDK, ускорение HAXM и библиотеки Google Play.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Например, чтобы скачать пакет эмулятора Android от Google, установите флажок рядом с элементом **Эмулятор Android** и нажмите кнопку **Применить изменения**:

[![Установка эмулятора Android со вкладки "Сервис"](android-sdk-images/win/06-install-emulator-sml.png)](android-sdk-images/win/06-install-emulator.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Например, чтобы скачать пакет эмулятора Android от Google, установите флажок рядом с элементом **Эмулятор Android** и нажмите кнопку **Установить обновления**:

![Установка эмулятора Android с вкладки "Сервис"](android-sdk-images/mac/sdkmanager-08.png)

-----


Может появиться диалоговое окно с сообщением _Some components can be updated. Do you want to update them now?_ (Можно обновить некоторые компоненты. Вы хотите обновить их?) Нажмите кнопку **Да**. Далее отображается диалоговое окно "Принятие условий лицензионного соглашения":

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Экран "Принятие условий лицензионного соглашения"](android-sdk-images/win/07-license-acceptance.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

![Экран "Принятие условий лицензионного соглашения"](android-sdk-images/mac/sdkmanager-09.png)

-----

Щелкните **Принять**, чтобы принять условия лицензионного соглашения. В нижней части окна индикатор выполнения указывает ход скачивания и установки. После завершения установки вкладка **Сервис** указывает, что выбранные инструменты и дополнения установлены.



### <a name="platforms-tab"></a>Вкладка "Платформы"

Вкладка **Платформы** содержит список версий пакета SDK платформы вместе с другими ресурсами (например, образами системы) для каждой платформы.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Снимок экрана области "Платформы"](android-sdk-images/win/08-platforms-pane-sml.png)](android-sdk-images/win/08-platforms-pane.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

![Снимок экрана области "Платформы"](android-sdk-images/mac/sdkmanager-11.png)

-----

На этом экране указана версия Android (например, **Android 7.0**), кодовое название (**Nougat**), уровень API (например, **24**) и состояние (**Установлено**, если платформа установлена). Вкладка **Платформы** служит для установки компонентов для уровня API Android, на который требуется ориентироваться (дополнительные сведения о версиях Android и уровнях API см. в разделе [Общие сведения об уровнях API Android](~/android/app-fundamentals/android-api-levels.md)).

Если установлены все компоненты платформы, рядом с ее именем появляется флажок. Если установлены не все компоненты платформы, для нее заполняется поле. 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Вы можете развернуть платформу, чтобы просмотреть ее компоненты (в том числе и установленные), щелкнув поле **+** слева от нее.
Щелкните **-**, чтобы свернуть список компонентов для платформы.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Вы можете развернуть платформу, чтобы просмотреть ее компоненты (в том числе и установленные), щелкнув **стрелку** слева от нее.
Щелкните **стрелку вниз**, чтобы свернуть список компонентов для платформы.

-----

Чтобы добавить в пакет SDK другую платформу, щелкайте поле рядом с ней, пока в нем не появился флажок (обозначающий установку всех компонентов), а затем нажмите кнопку **Применить изменения**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Пример добавления компонентов Android 7.1 Nougat в пакет SDK для Android](android-sdk-images/win/09-adding-a-platform-sml.png)](android-sdk-images/win/09-adding-a-platform.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

![Пример добавления компонентов Android 4.4 в пакет SDK для Android](android-sdk-images/mac/sdkmanager-12.png)

-----

Чтобы установить пакет SDK, один раз щелкните поле рядом с платформой. Затем можно выбрать любые отдельные компоненты, которые вам нужны:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Пример добавления компонентов Android 7.1](android-sdk-images/win/10-adding-some-components-sml.png)](android-sdk-images/win/10-adding-some-components.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

![Пример добавления компонентов Android 4.4](android-sdk-images/mac/sdkmanager-13.png)

-----


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Обратите внимание, что число устанавливаемых компонентов отображается рядом с кнопкой **Применить изменения**. В приведенном выше примере к установке готовы шесть компонентов. После нажатия кнопки **Применить изменения** вы увидите экран **Принятие условий лицензионного соглашения**:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Обратите внимание, что число устанавливаемых компонентов отображается рядом с кнопкой **Применить изменения**. После нажатия кнопки **Применить изменения** вы увидите экран **Принятие условий лицензионного соглашения**:

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Вкладка "Платформа", диалоговое окно "Принятие условий лицензионного соглашения"](android-sdk-images/win/11-license-screen.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

![Вкладка "Платформа", диалоговое окно "Принятие условий лицензионного соглашения"](android-sdk-images/mac/sdkmanager-14.png)

-----

Щелкните **Принять**, чтобы принять условия лицензионного соглашения. Это диалоговое окно может появиться несколько раз, если устанавливается несколько компонентов. В нижней части окна индикатор выполнения указывает ход скачивания и установки. После завершения скачивания и установки (это может занять несколько минут в зависимости от числа скачиваемых компонентов) добавленные компоненты помечаются флажком и указываются в списке **Установленные**.

Теперь все готово к разработке приложения для последнего уровня API Android.


 
## <a name="summary"></a>Сводка

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Это руководство описывает, как установить и использовать диспетчер пакетов SDK Xamarin Android в Visual Studio.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Это руководство описывает, как использовать диспетчер пакетов SDK Xamarin Android в Visual Studio для Mac.

-----


## <a name="related-links"></a>Связанные ссылки

- [Изменения в инструментарии пакета SDK для Android](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [Общие сведения об уровнях API Android](~/android/app-fundamentals/android-api-levels.md)
- [Заметки о выпуске пакета SDK Tools (Google)](https://developer.android.com/studiohttps://developer.xamarin.com/releases/sdk-tools.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
