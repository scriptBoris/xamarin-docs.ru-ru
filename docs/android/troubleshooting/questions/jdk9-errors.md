---
title: Xamarin.Android и комплект разработчика Java 9
description: В этой статье объясняется, как разрешить Java Development Kit (JDK) 9 или более поздней версии ошибки в Xamarin.Android.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7DCF0985-F77D-4A68-AC54-10C9846E189A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: d8c64ff79367d93e282edd9534ffb98f5bb90c93
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2018
ms.locfileid: "36269677"
---
# <a name="xamarinandroid-and-java-development-kit-9-or-later"></a>Xamarin.Android и комплект разработчика Java 9 или более поздней версии

_В этой статье объясняется, как разрешить Java Development Kit (JDK) 9 или более поздней версии ошибки в Xamarin.Android._


## <a name="overview"></a>Обзор

Xamarin.Android использует Java Development Kit (JDK) для интеграции пакета SDK Android для создания приложений Android и под управлением Android designer. Последние версии пакета SDK для Android (API 24 и более поздних версий) требуют JDK 8 (1.8) или предварительной версии Microsoft Mobile OpenJDK. **Так как инструменты пакета SDK для Android от Google не совместимы с JDK 9, Xamarin.Android не работает с JDK 9 или более поздней версии.**

## <a name="jdk-errors"></a>Ошибки JDK

Если вы попытаетесь выполнить сборку проекта Xamarin.Android с версией более поздней версии, чем JDK 8 JDK, вы получите явные сообщение о том, что эта версия JDK не поддерживается. Пример:

```shell
Building with JDK Version `9.0.4` is not supported. Please install JDK version `1.8.0`. See https://aka.ms/xamarin/jdk9-errors  
```

Чтобы устранить эти ошибки, необходимо установить JDK 8 (1.8), как описано в [как обновление версии Java Development Kit (JDK)?](~/android/troubleshooting/questions/update-jdk.md).
Кроме того, можно установить [предварительной версии Microsoft Mobile OpenJDK](~/android/get-started/installation/openjdk.md) The Microsoft Mobile OpenJDK заменит JDK 8 для разработки приложений Xamarin.Android.


## <a name="checking-the-jdk-version"></a>Проверка версии JDK

Можно проверить, чтобы просмотреть текущую версию Java, установки, введя следующую команду (JDK `bin` каталог должен быть в вашей `PATH`):

```shell
java -version
```

Если установлен пакет JDK 9, вы увидите примерно следующее сообщение:

```shell
java version "9.0.4"
Java(TM) SE Runtime Environment (build 9.0.4+11)
Java HotSpot(TM) 64-Bit Server VM (build 9.0.4+11, mixed mode)
```

Если установлен пакет JDK 9 или более поздней версии, необходимо установить Java JDK 8 (1.8) или предварительной версии Microsoft Mobile OpenJDK. Сведения о том, как установить JDK 8, см. в разделе [как обновление версии Java Development Kit (JDK)?](~/android/troubleshooting/questions/update-jdk.md). Сведения об установке OpenJDK Mobile Майкрософт, см. в разделе [предварительной версии Microsoft Mobile OpenJDK](~/android/get-started/installation/openjdk.md).

Обратите внимание, что у вас нет удаление более позднюю версию JDK; Тем не менее необходимо убедиться, что с помощью Xamarin, JDK 8, а не в более поздней версии JDK. В Visual Studio щелкните **Сервис > Параметры > Xamarin > Параметры Android**. Если **расположение пакета Java Development Kit** не задано расположение JDK 8 (такие как **C:\\Program Files\\Java\\jdk1.8.0_111**), нажмите кнопку **изменений**  и задайте его в расположение, где установлен JDK 8. В Visual Studio для Mac, перейдите к **предпочтения > проекты > расположения SDK > Android > пакет SDK для Java (JDK)** и нажмите кнопку **Обзор** обновить этот путь.

## <a name="known-issues-with-jdk-9"></a>Известные проблемы с JDK 9

### <a name="apksigner"></a>apksigner

Имеется известная проблема с apksigner и пакет JDK 9, в котором `apksigner.bat` файла вызывает `apksigner.jar` с `-Djava.ext.dirs` вместо `-classpath` которого ожидает, что пакет JDK 9. Рекомендуется использовать JDK 8 (1.8). Сведения о том, как установить JDK 8, см. в разделе [как обновление версии Java Development Kit (JDK)?](~/android/troubleshooting/questions/update-jdk.md)

Если вы установили пакет JDK 9, убедитесь, что следующий путь не на вашей `PATH` переменной среды, так как он будет по-прежнему указывают пакет JDK 9: `C:\ProgramData\Oracle\Java\javapath`. После удаления, `java-version` в командной строке должно отображаться JDK 8.
