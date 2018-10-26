---
title: Как обновить версию Java Development Kit (JDK)?
description: В этой статье показано, как обновить версию Java Development Kit (JDK) в Windows и Mac.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 4b3ac51d-18dd-4034-87b4-4365194e4ece
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/07/2018
ms.openlocfilehash: aa04d944f803dded0e9448de27ed7d5ced2efb54
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109189"
---
# <a name="how-do-i-update-the-java-development-kit-jdk-version"></a>Как обновить версию Java Development Kit (JDK)?

_В этой статье показано, как обновить версию Java Development Kit (JDK) в Windows и Mac._

## <a name="overview"></a>Обзор

Xamarin.Android использует Java Development Kit (JDK) для интеграции пакета SDK Android для создания приложений Android и под управлением Android designer. Последние версии пакета SDK для Android (API 24 и более поздних версий) требуют JDK 8 (1.8). Кроме того, можно установить [предварительной версии Microsoft Mobile OpenJDK](~/android/get-started/installation/openjdk.md). В OpenJDK Microsoft Mobile заменит JDK 8 для разработки приложений Xamarin.Android.

Чтобы обновить до OpenJDK Mobile Майкрософт, см. в разделе [предварительной версии Microsoft Mobile OpenJDK](~/android/get-started/installation/openjdk.md). Чтобы обновить до JDK 8, выполните следующие действия.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  Скачать JDK 8 (1.8) из [веб-сайте Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![Снимок экрана JDK загрузить на веб-сайте Oracle](update-jdk-images/image1.png)

2.  Выберите 64-разрядной версии, чтобы разрешить отображение [пользовательские элементы управления](https://developer.xamarin.com/releases/vs/xamarin.vs_4/xamarin.vs_4.2/#androiddesignercustomcontrols) в конструкторе Xamarin Android:

    ![Выбор пакета JDK Windows x64 загрузить со страницы загрузки JDK](update-jdk-images/image2.png)

3.  Запустите .exe и установите **средства разработки**:

    ![Установка средства разработки в установщике JDK](update-jdk-images/image3.png)

4.  Откройте Visual Studio и обновите **расположение пакета Java Development Kit** указывал на новый пакет JDK в разделе **Сервис > Параметры > Xamarin > Параметры Android > расположение пакета Java Development Kit**:

    [![Параметр Path для JDK на странице Параметры Android](update-jdk-images/image4-sml.png)](update-jdk-images/image4.png#lightbox)

Не забудьте перезапустить Visual Studio после обновления расположения.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

1.  Скачать JDK 8 (1.8) из [веб-сайте Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![Снимок экрана JDK загрузить на веб-сайте Oracle](update-jdk-images/image1.png)

2.  Откройте файл .dmg и запустите установщик .pkg:

    ![Запустив установщик JDK в macOS](update-jdk-images/image5.png)

Mac OS автоматически установит новую версию JDK по умолчанию, обновив **/System/Library/Frameworks/JavaVM.framework/Versions/Current**. Можно затем убедиться, что **пакет SDK для Java (JDK)** расположение указано ожидаемое значение по умолчанию **/usr** под **Visual Studio для Mac > Параметры > проекты > расположения пакета SDK для > Android > расположения > расположение пакета Java SDK (JDK)**:

[![Задание расположения JDK на вкладке "Android расположения"](update-jdk-images/image6-sml.png)](update-jdk-images/image6.png#lightbox)

-----

