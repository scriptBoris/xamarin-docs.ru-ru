---
title: Где можно задать Мой расположения пакета SDK для Android
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6A9DE6E9-3E27-4DD2-87D2-34E95E5D401C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 11/16/2017
ms.openlocfilehash: e7af6635aeec72a9a0e0c01eeb6eb0a77d2a1d7b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30764012"
---
# <a name="where-can-i-set-my-android-sdk-locations"></a>Где можно задать Мой расположения пакета SDK для Android

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

В Visual Studio, перейдите к **Сервис > Параметры > Xamarin > Параметры Android** для просмотра и задайте расположение пакета SDK для Android:

[![Пример расположения на вкладке настройки](android-sdk-location-images/win/01-locations-sml.png)](android-sdk-location-images/win/01-locations.png#lightbox)

Расположение по умолчанию для каждого пути выглядит следующим образом:

- Расположение комплект средств для разработки Java: 

    **C:\\программные файлы\\Java\\jdk1.8.0_131**

- Расположение пакета SDK для Android: 

    **C:\\Program Files (x86)\\Android\\android-sdk**

- Android NDK расположение: 

    **C:\\ProgramData\\Microsoft\\AndroidNDK64\\android ndk-r13b**

Обратите внимание, что номер версии NDK может меняться. Например, а не из **android ndk-r13b**, это может быть более ранней версии, такие как **android ndk-r10e**.

Чтобы указать расположение пакета SDK для Android, введите полный путь к каталогу пакета SDK для Android в **Android расположение пакета SDK** поле. Перейдите в расположение пакета SDK для Android в проводнике, скопировать путь из адресной строки и вставьте этот путь в **Android расположение пакета SDK** поле.
Например, если ваше расположение пакета SDK для Android находится в **C:\\пользователей\\username\\AppData\\локального\\Android\\Sdk**, снимите по старому пути в  **Android SDK расположение** , вставьте в этот путь, а щелкните **ОК**.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

В Visual Studio для Mac перейдите к **предпочтения > проекты > расположения пакета SDK > Android**. В **Android** щелкните **расположения** вкладку для просмотра и задайте расположение пакета SDK:

[![Пример расположения на вкладке настройки](android-sdk-location-images/mac/01-locations-sml.png)](android-sdk-location-images/mac/01-locations.png#lightbox)

Расположение по умолчанию для каждого пути выглядит следующим образом:

- Расположение пакета SDK для Android: 

    **~/Library/Developer/Xamarin/android-sdk-macosx**

- Android NDK расположение: 

    **~/Library/Developer/Xamarin/Android-ndk/Android-ndk-r14b**

- Расположение пакета SDK (JDK) Java: 

    **/usr**

Обратите внимание, что номер версии NDK может меняться. Например, а не из **android ndk-r14b**, это может быть более ранней версии, такие как **android ndk-r10e**.

Чтобы указать расположение пакета SDK для Android, введите полный путь к каталогу пакета SDK для Android в **Android расположение пакета SDK** поле. Можно выбрать папку пакета SDK для Android в системе поиска, нажмите клавишу **CTRL +&#8984;+ I** для просмотра сведений о папке, щелкните и перетащите путь справа от **где:**, копировать, а затем вставьте его **пакета SDK для Android Расположение** поле **расположения** вкладки. Например, если ваше расположение пакета SDK для Android находится в **~/Library/Developer/Android/Sdk**, снимите по старому пути в **Android расположение пакета SDK** , вставьте в этот путь, а щелкните **ОК**.

-----
