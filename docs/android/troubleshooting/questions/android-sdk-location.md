---
title: Где можно задать моей расположения пакета SDK для Android?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6A9DE6E9-3E27-4DD2-87D2-34E95E5D401C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 11/16/2017
ms.openlocfilehash: c004fb7717f78750e7ac1e8dc1856a32ba808638
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119492"
---
# <a name="where-can-i-set-my-android-sdk-locations"></a>Где можно задать моей расположения пакета SDK для Android?

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

В Visual Studio, перейдите к **Сервис > Параметры > Xamarin > Параметры Android** для просмотра и задайте расположение пакета SDK для Android:

[![Вкладка "расположения" пример в установках](android-sdk-location-images/win/01-locations-sml.png)](android-sdk-location-images/win/01-locations.png#lightbox)

Расположение по умолчанию для каждого пути выглядит следующим образом:

- Расположение пакета Java Development Kit: 

    **C:\\Program Files\\Java\\jdk1.8.0_131**

- Расположение пакета SDK для Android: 

    **C:\\Program Files (x86)\\Android\\android-sdk**

- Расположение пакета Android NDK: 

    **C:\\ProgramData\\Microsoft\\AndroidNDK64\\android ndk-r13b**

Обратите внимание на то, что номер версии NDK могут отличаться. Например, а не из **android ndk-r13b**, это может быть более ранней версии, такие как **android ndk-r10e**.

Чтобы задать расположение пакета SDK для Android, введите полный путь к каталогу пакета SDK для Android в **расположение пакета Android SDK** поле. Перейдите к расположению пакета SDK для Android в проводнике, скопируйте путь из адресной строки и вставьте этот путь в **расположение пакета Android SDK** поле.
Например, если ваше расположение пакета SDK для Android находится в **C:\\пользователей\\имя пользователя\\AppData\\локального\\Android\\Sdk**, Очистить старый путь в  **Расположение пакета Android SDK** , вставьте в этот путь, а щелкните **ОК**.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

В Visual Studio для Mac, перейдите к **параметры > проекты > расположения SDK > Android**. В **Android** щелкните **расположения** tab для просмотра и задайте расположение пакета SDK:

[![Вкладка "расположения" пример в установках](android-sdk-location-images/mac/01-locations-sml.png)](android-sdk-location-images/mac/01-locations.png#lightbox)

Расположение по умолчанию для каждого пути выглядит следующим образом:

- Расположение пакета SDK для Android: 

    **~/Library/Developer/Xamarin/Android-SDK-MacOSX**

- Расположение пакета Android NDK: 

    **~/Library/Developer/Xamarin/Android-ndk/Android-ndk-r14b**

- Расположение пакета Java SDK (JDK): 

    **/usr**

Обратите внимание на то, что номер версии NDK могут отличаться. Например, а не из **android ndk-r14b**, это может быть более ранней версии, такие как **android ndk-r10e**.

Чтобы задать расположение пакета SDK для Android, введите полный путь к каталогу пакета SDK для Android в **расположение пакета Android SDK** поле. Вы можете выбрать папку пакета SDK для Android в средстве поиска, нажмите клавишу **CTRL +&#8984;+ I** Чтобы просмотреть сведения о папке, щелкните и перетащите путь справа от **где:**, скопируйте, а затем вставьте его в **пакета SDK для Android Расположение** поле **расположения** вкладки. Например, если ваше расположение пакета SDK для Android находится в **~/Library/Developer/Android/Sdk**, Очистить старый путь в **расположение пакета Android SDK** , вставьте в этот путь, а щелкните **ОК**.

-----
