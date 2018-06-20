---
title: Как обновить версию Java Development Kit (JDK)?
description: В этой статье показано, как обновить версию Java Development Kit (JDK) на Windows и Mac.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 4b3ac51d-18dd-4034-87b4-4365194e4ece
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/18/2018
ms.openlocfilehash: 979bf4572e0e0865c2254c3e1c2f707c8eecae8d
ms.sourcegitcommit: 57f9a9ba2f199697cb75e7be67f1a372c35a861b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36269664"
---
# <a name="how-do-i-update-the-java-development-kit-jdk-version"></a>Как обновить версию Java Development Kit (JDK)?

_В этой статье показано, как обновить версию Java Development Kit (JDK) на Windows и Mac._

## <a name="overview"></a>Обзор

Xamarin.Android использует Java Development Kit (JDK) для интеграции с Android SDK для приложений Android построения и запуска конструктора Android. Последние версии пакета SDK Android (API 24 и более поздние версии) требуют JDK 8 (1.8). Если вы еще не обновлен до JDK 8, выполните следующие действия, чтобы установить и включить его.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  Загрузка JDK 8 (1.8) из [веб-сайте Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![Снимок экрана JDK загрузки на веб-сайте Oracle](update-jdk-images/image1.png)

2.  Выберите 64-разрядную версию, чтобы разрешить визуализацию [пользовательские элементы управления](https://developer.xamarin.com/releases/vs/xamarin.vs_4/xamarin.vs_4.2/#androiddesignercustomcontrols) в конструкторе Xamarin Android:

    ![При выборе пакета JDK Windows x64 для загрузки на странице загрузки JDK](update-jdk-images/image2.png)

3.  Запустите .exe и установите **средства разработки**:

    ![Установка средства разработки в программе установки JDK](update-jdk-images/image3.png)

4.  Откройте Visual Studio и обновление **Java Development Kit расположение** для указания нового JDK в **средства > Параметры > Xamarin > Параметры Android > Java Development Kit расположение > Изменение**:

    [![Параметр Path для JDK на странице настроек Android IDE](update-jdk-images/image4-sml.png)](update-jdk-images/image4.png#lightbox)

Не забудьте перезапустить Visual Studio после обновления расположения.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

1.  Загрузка JDK 8 (1.8) из [веб-сайте Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![Снимок экрана JDK загрузки на веб-сайте Oracle](update-jdk-images/image1.png)

2.  Откройте файл .dmg и запустите установщик .pkg:

    ![Запустив установщик JDK на macOS](update-jdk-images/image5.png)

Mac OS автоматически установит новую версию JDK по умолчанию, обновив **/System/Library/Frameworks/JavaVM.framework/Versions/Current**. Можно затем убедиться, что **пакет SDK для Java (JDK)** расположение задается ожидаемое значение по умолчанию **/usr** под **Visual Studio для Mac > Параметры > проекты > расположения пакета SDK > Android > Java (JDK) пакета SDK > расположение**:

![Настройка расположения JDK на странице Android расположение пакета SDK](update-jdk-images/image6.png)

-----

