---
title: Вопросы и ответы
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 0F0FDD2B-FFB1-476F-B674-81DB3A5E1CF3
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: ad3fc32245880f6603c63d33aac49309fd61b753
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2018
ms.locfileid: "36935468"
---
# <a name="frequently-asked-questions"></a>Вопросы и ответы

## <a name="installation--setup"></a>Установка и настройка

### <a name="which-android-sdk-packages-should-i-installinstall-android-sdk-packagesmd"></a>[Какие пакеты SDK Android следует установить?](install-android-sdk-packages.md)

Установка пакета SDK для Android не включает автоматически все минимальный набор обязательных пакетов для разработки. Хотя индивидуальный разработчик должен изменяться, здесь вы найдете пакеты, которые обычно будут необходимы для разработки с помощью Xamarin.Android.

### <a name="where-can-i-set-my-android-sdk-locationsandroid-sdk-locationmd"></a>[Где можно задать свои расположения пакета SDK для Android?](android-sdk-location.md)

В этом руководстве описывается, как параметры по умолчанию пакет SDK для Android, который должен работать для большинства установок; и при необходимости измените эти значения по умолчанию в Visual Studio для Mac или Visual Studio.

### <a name="how-do-i-update-the-java-development-kit-jdk-versionupdate-jdkmd"></a>[Как обновить версию Java Development Kit (JDK)?](update-jdk.md)

В этой статье показано, как обновить версию Java Development Kit (JDK) в Windows и Mac.

### <a name="can-i-use-java-development-kit-jdk-version-9-or-laterjdk9-errorsmd"></a>[Можно ли использовать Java Development Kit (JDK) версии 9 или более поздней версии](jdk9-errors.md)

Для Xamarin.Android требуется JDK 8 или Microsoft Mobile OpenJDK. В этой статье перечислены некоторые распространенные сообщения об ошибках, может появиться, если установлен JDK 9 или более поздней версии, а также инструкции по проверке версии JDK.


### <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packagesinstall-android-support-librarymd"></a>[Как вручную установить вспомогательные библиотеки Android, необходимые для пакетов Xamarin.Android.Support?](install-android-support-library.md)

Это руководство содержит примеры действий для установки `Xamarin.Android.Support.v4` библиотека поддержки в Windows и Mac.

### <a name="what-usb-drivers-do-i-need-to-debug-android-on-windowsandroid-drivers-debug-windowsmd"></a>[Какие драйверы USB нужны для отладки Android в Windows?](android-drivers-debug-windows.md)

Отладка на устройстве Android, при разработке в Windows; необходимо установить драйвер USB. Диспетчер Android SDK включает в себя «Драйвер Google USB» по умолчанию, который добавляет поддержку для устройств хранилища.
Другие устройства требуют драйверов USB, опубликованных производителем устройства. В этом руководстве сведения о поиске эти драйверы также как альтернативные методы тестирования.

### <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vmconnect-android-emulator-mac-windowsmd"></a>[Можно ли подключиться с виртуальной машины Windows к эмуляторам Android под управлением Mac?](connect-android-emulator-mac-windows.md)

В этом руководстве рассматриваются методы, при использовании эмулятора Android.

## <a name="general-questions"></a>Общие вопросы

### <a name="how-do-i-automate-an-android-nunit-test-projectautomate-android-nunit-testmd"></a>[Как автоматизировать тестовый проект Android NUnit?](automate-android-nunit-test.md)

В этом руководстве рассматриваются действия по настройке тестовый проект Android NUnit, _не_ проект Xamarin.UITest. Xamarin.UITest руководства можно найти [здесь](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest).

### <a name="how-do-i-enable-intellisense-in-android-axml-filesenable-axml-intellisensemd"></a>[Как включить Intellisense в AXML-файлах Android?](enable-axml-intellisense.md)

В этом руководстве описывается, как активировать Intellisense в Visual Studio для Android AXML-файлах.

### <a name="why-cant-my-android-release-build-connect-to-the-internetandroid-internetmd"></a>[Почему моя сборка выпуска Android не подключается к Интернету?](android-internet.md)

Наиболее распространенной причиной этой проблемы в том, что **INTERNET** разрешение автоматически включается в сборке отладки, но должен быть установлен вручную для построения выпуска. В этом руководстве описывается, как включить разрешение для сборок выпуска.

### <a name="smarter-xamarin-android-support-v4--v13-nuget-packagesandroid-support-v4v13-librariesmd"></a>[Оптимизированные пакеты NuGet поддержки Android v4 и v13 в Xamarin](android-support-v4v13-libraries.md)

`Support-v4` и `Support-v13` не может использоваться вместе в одном приложении, то есть они являются взаимоисключающими. Это обусловлено `Support-v13` фактически содержит все типы и реализации `Support-v4`. Если вы повторите и ссылки, в том же проекте, будут возникать ошибки повторяющийся тип.

### <a name="how-do-i-resolve-a-pathtoolongexception-errorpath-too-long-exceptionmd"></a>[Как устранить ошибку PathTooLongException?](path-too-long-exception.md)

В этой статье описываются способы решения **PathTooLongException** ошибка, которая может возникнуть при построении проекта Xamarin.Android.



## <a name="deprecated"></a>Устарело

> [!NOTE]
> Перечисленные ниже статьи применяются к проблемам, которые были устранены в последних версиях Xamarin. Тем не менее, если эта проблема возникает на последнюю версию программного обеспечения, отправьте [новую ошибку](~/cross-platform/troubleshooting/questions/howto-file-bug.md) благодаря полное управление версиями сведения и полный создавать выходные данные журнала.

### <a name="what-version-of-xamarinandroid-added-lollipop-supportxa-lollipopmd"></a>[В какой версии Xamarin.Android добавлена поддержка Lollipop?](xa-lollipop.md)

В этом руководстве, первоначально был написан для предварительной версии Android L. Xamarin.Android 4.17 добавлена поддержка предварительной версии Android L & Xamarin.Android 4.20 добавлена поддержка Android Lollipop.

### <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawablemissing-action-mode-share-drawablemd"></a>[Android.Support.v7.AppCompat — не удалось найти ресурс, который соответствует указанному имени: attr 'android:actionModeShareDrawable'](missing-action-mode-share-drawable.md)

Эта ошибка может возникать в более старых версиях Xamarin, если отсутствуют некоторые требуемые пакеты SDK для Android.

### <a name="adjusting-java-memory-parameters-for-the-android-designerandroid-designer-java-memorymd"></a>[Настройка параметров памяти Java для конструктора Android](android-designer-java-memory.md)

Параметры памяти по умолчанию, которые используются при запуске `java` процесс Android designer могут оказаться несовместимыми с некоторых конфигураций системы. Начиная с версии Xamarin Studio 5.7.2.7 и Xamarin для Visual Studio 3.9.344 эти параметры можно настроить на основе отдельных проектов.

### <a name="my-android-resourcedesignercs-file-will-not-updateresource-designer-wont-updatemd"></a>[Не обновляется файл Resource.designer.cs Android](resource-designer-wont-update.md)

Ошибка в Xamarin.Studio 5.1 ранее повреждены CSPROJ-файлов, частично или полностью удалите XML-код в CSPROJ-файл. Это приведет к важной части Android построить систему (например, обновление Android Resource.designer.cs) переход на другой. По состоянию на 5.1.4 стабильный выпуск 15 июля, эта ошибка будет исправлена; но во многих случаях приходится вручную, как описано в этом руководстве исправить файл проекта.



