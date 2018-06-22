---
title: Вопросы и ответы
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 0F0FDD2B-FFB1-476F-B674-81DB3A5E1CF3
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/19/2018
ms.openlocfilehash: 2deeb4309be44c1c5f8d78980707336ac8301761
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30774360"
---
# <a name="frequently-asked-questions"></a>Вопросы и ответы

## <a name="installation--setup"></a>Установка и настройка

### <a name="which-android-sdk-packages-should-i-installinstall-android-sdk-packagesmd"></a>[Какие пакеты SDK Android следует установить?](install-android-sdk-packages.md)

Установка пакета SDK для Android не содержит автоматически все минимальный набор обязательных пакетов для разработки. Хотя отдельные разработчик должен отличаться, в этом руководстве рассматриваются пакеты, которые обычно будут необходимы для разработки с использованием Xamarin.Android.

### <a name="where-can-i-set-my-android-sdk-locationsandroid-sdk-locationmd"></a>[Где можно задать свои расположения пакета SDK для Android?](android-sdk-location.md)

В этом руководстве описаны параметры по умолчанию из пакета SDK для Android, который должен работать для большинства установок; и при необходимости изменить эти значения по умолчанию в Visual Studio для Mac или Visual Studio.

### <a name="how-do-i-update-the-java-development-kit-jdk-versionupdate-jdkmd"></a>[Как обновить версию Java Development Kit (JDK)?](update-jdk.md)

В этой статье показано, как обновить версию Java Development Kit (JDK) на Windows и Mac.

### <a name="can-i-use-java-development-kit-jdk-version-9jdk9-errorsmd"></a>[Можно ли использовать Java Development Kit (JDK) версии 9?](jdk9-errors.md)

Xamarin.Android требуется JDK 8, а не JDK 9. В этой статье перечислены некоторые из распространенных сообщений об ошибках, которые может определить, установлен ли JDK 9, а также инструкции по проверке версии JDK.


### <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packagesinstall-android-support-librarymd"></a>[Как вручную установить вспомогательные библиотеки Android, необходимые для пакетов Xamarin.Android.Support?](install-android-support-library.md)

Здесь приведены примеры действий по установке `Xamarin.Android.Support.v4` библиотека поддержки на Windows & Mac.

### <a name="how-do-i-install-google-play-services-in-an-emulatorinstall-gpsmd"></a>[Как установить службы Google Play в эмуляторе?](install-gps.md)

Это руководство, ссылки на сведения об установке службы Google Play в эмуляторе Android в Visual Studio.

### <a name="what-usb-drivers-do-i-need-to-debug-android-on-windowsandroid-drivers-debug-windowsmd"></a>[Какие драйверы USB нужны для отладки Android в Windows?](android-drivers-debug-windows.md)

Для отладки на устройстве с Android при разработке в Windows; необходимо установить совместимый драйвер USB. Диспетчер Android SDK включает «Драйвер Google USB» по умолчанию, что добавлена поддержка для устройств хранилища.
Другие устройства требуют USB драйверов, в частности опубликованных производителем устройства. Это руководство предоставляет сведения о поиске драйверов для этих также как альтернативные методы тестирования.

### <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vmconnect-android-emulator-mac-windowsmd"></a>[Можно ли подключиться с виртуальной машины Windows к эмуляторам Android под управлением Mac?](connect-android-emulator-mac-windows.md)

В этом руководстве рассматриваются методы, при использовании эмулятора Google Android.

## <a name="general-questions"></a>Общие вопросы

### <a name="how-do-i-automate-an-android-nunit-test-projectautomate-android-nunit-testmd"></a>[Как автоматизировать тестовый проект Android NUnit?](automate-android-nunit-test.md)

В настоящем руководстве приводятся инструкции по настройке тестового проекта Android NUnit, _не_ Xamarin.UITest проекта. Можно найти руководства Xamarin.UITest [здесь](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest).

### <a name="how-do-i-enable-intellisense-in-android-axml-filesenable-axml-intellisensemd"></a>[Как включить Intellisense в AXML-файлах Android?](enable-axml-intellisense.md)

В этом руководстве описывается активация Intellisense в Visual Studio для Android .axml файлов.

### <a name="why-cant-my-android-release-build-connect-to-the-internetandroid-internetmd"></a>[Почему моя сборка выпуска Android не подключается к Интернету?](android-internet.md)

Наиболее распространенной причиной этой проблемы является, **INTERNET** разрешение автоматически включается в отладочном построении, но должен быть установлен вручную для сборки выпуска. В этом руководстве описывается включение разрешения для сборки выпуска.

### <a name="smarter-xamarin-android-support-v4--v13-nuget-packagesandroid-support-v4v13-librariesmd"></a>[Оптимизированные пакеты NuGet поддержки Android v4 и v13 в Xamarin](android-support-v4v13-libraries.md)

`Support-v4` и `Support-v13` не могут использоваться вместе в одном приложении, то есть они являются взаимоисключающими. Это вызвано `Support-v13` фактически содержит все типы и реализации `Support-v4`. Если повторите и ссылаться на обоих в одном проекте возникнет повторяющийся тип ошибки.

### <a name="how-do-i-resolve-a-pathtoolongexception-errorpath-too-long-exceptionmd"></a>[Как устранить ошибку PathTooLongException?](path-too-long-exception.md)

В этой статье описываются способы решения **PathTooLongException** ошибка, которая может возникнуть при построении проекта Xamarin.Android.



## <a name="deprecated"></a>Рекомендуется использовать

> [!NOTE]
> В следующей статье применяются к проблемам, которые будут устранены в последних версиях Xamarin. Тем не менее, если эта проблема возникает на последнюю версию программного обеспечения, проверьте файл [новую ошибку](~/cross-platform/troubleshooting/questions/howto-file-bug.md) с помощью полное управление версиями сведения и полный создать выходные данные журнала.

### <a name="what-version-of-xamarinandroid-added-lollipop-supportxa-lollipopmd"></a>[В какой версии Xamarin.Android добавлена поддержка Lollipop?](xa-lollipop.md)

В этом руководстве изначально был записан в предварительной версии Android L. Xamarin.Android 4.17 добавлена поддержка предварительной версии Android L & Xamarin.Android 4.20 добавлена поддержка Android без описания операций.

### <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawablemissing-action-mode-share-drawablemd"></a>[Android.Support.v7.AppCompat — не удалось найти ресурс, который соответствует указанному имени: attr 'android:actionModeShareDrawable'](missing-action-mode-share-drawable.md)

Эта ошибка возникает в более старых версиях Xamarin, если отсутствуют некоторые необходимые pacakages пакета SDK для Android.

### <a name="adjusting-java-memory-parameters-for-the-android-designerandroid-designer-java-memorymd"></a>[Настройка параметров памяти Java для конструктора Android](android-designer-java-memory.md)

Параметры памяти по умолчанию, которые будут использоваться при запуске `java` обработать конструктора Android могут быть несовместимы с некоторых конфигураций системы. Начиная с Xamarin Studio 5.7.2.7 и Xamarin для Visual Studio 3.9.344 эти параметры можно настроить отдельно для каждого проекта.

### <a name="my-android-resourcedesignercs-file-will-not-updateresource-designer-wont-updatemd"></a>[Не обновляется файл Resource.designer.cs Android](resource-designer-wont-update.md)

Ошибка в Xamarin.Studio 5.1 ранее поврежденные файлы .csproj, частично или полностью удалите XML-код в CSPROJ-файл. Это приведет к важные части Android системы (например, обновление Android Resource.designer.cs) сборки к сбою. Начиная с 5.1.4 стабильный выпуске на 15 июля см. в этот момент был исправлен; но во многих случаях приходится исправить вручную, как описано в этом руководстве файл проекта.



