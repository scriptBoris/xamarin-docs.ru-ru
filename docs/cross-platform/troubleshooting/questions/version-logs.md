---
title: Где найти Мои сведения о версии и журналы?
description: Этот документ описывает, где можно попытаться найти сведения о версии Xamarin и журналы. Эта информация полезна при диагностике проблемы, отправляйте сообщения об ошибках, или получение технической поддержки.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CF386485-EAB0-4B9E-AA17-CB1B6462E505
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 2c3ded2233810ff4e903596d65f64097aa3b0445
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118231"
---
# <a name="where-can-i-find-my-version-information-and-logs"></a>Где найти Мои сведения о версии и журналы?

## <a name="outline"></a>Контур

- [Сведения о версии](#version-information)
    - Сведения о версии Windows
    - Сведения о версии Mac
    - Platform-tools, Android SDK Tools Build-tools
- [Журналы интегрированной среды разработки и установщик](#ide-and-installer-logs)
    - [Журналы Windows](#windows-logs)
        - Xamarin Studio
        - Xamarin для Visual Studio
        - Универсальный установщик Xamarin
        - Отдельные `.msi` установщики, подробных журналов
        - Запуск Visual Studio, подробных журналов
    - [Журналы Mac](#mac-logs)
        - Основное приложение
    - Visual Studio для Mac
        - Xamarin Studio
        - Установщик Xamarin
- [Выходные данные подробного сборки](#verbose-build-output-logs)
- [Отладочный журналы для приложений Xamarin.Android и Xamarin.iOS](#debug-logs-for-xamarin-apps)
    - Android `adb` logcat журналы
    - симулятор iOS входе (Mac)
    - устройство iOS входе (Mac)

## <a name="a-idversion-information-nameversion-information-version-information"></a><a id="version-information" name="version-information" />Сведения о версии

Обычно лучше всего для отправки обратно все сведения из **сведения о копировании** кнопки. В противном случае мы зачастую требуются для запроса дополнительной информации. К примеру, версий операционной системы, версии Xcode, установлен уровнях Android API, и версии платформы .NET можно все быть важные при помощи в устранении проблемы.

### <a name="a-idwindows-version-information-namewindows-version-information-windows-version-information"></a><a id="windows-version-information" name="windows-version-information" />Сведения о версии Windows

#### <a name="xamarin-studio"></a>Xamarin Studio

**Справка > о > Показать подробности > сведения о копировании [Кнопка]**

#### <a name="visual-studio"></a>Visual Studio

**Справка > о Microsoft Visual Studio > Info копирования [Кнопка]**

### <a name="a-idmac-version-information-namemac-version-information-mac-version-information"></a><a id="mac-version-information" name="mac-version-information" />Сведения о версии Mac

#### <a name="visual-studio-for-mac"></a>Visual Studio для Mac

**Visual Studio > о программе Visual Studio > Показать подробности > сведения о копировании [Кнопка]**

### <a name="a-idandroid-sdk-tools-versions-nameandroid-sdk-tools-versions-android-sdk-tools-platform-tools-build-tools"></a><a id="android-sdk-tools-versions" name="android-sdk-tools-versions" />Platform-tools, Android SDK Tools Build-tools

Откройте диспетчер пакетов SDK для Android и снимки экрана верхней **средства** раздел.

#### <a name="visual-studio-for-mac"></a>Visual Studio для Mac

**Сервис > Диспетчер пакетов SDK для Android**

#### <a name="visual-studio"></a>Visual Studio

**Сервис > Android > Диспетчер пакетов SDK для Android...**

## <a name="a-idide-and-installer-logs-nameide-and-installer-logs-ide-and-installer-logs"></a><a id="ide-and-installer-logs" name="ide-and-installer-logs" />Журналы интегрированной среды разработки и установщик

Для каждого расположения журнала не забудьте заархивировать и присоединить папке всего журнала.

### <a name="a-idwindows-logs-namewindows-logs-windows-logs"></a><a id="windows-logs" name="windows-logs" />Журналы Windows

#### <a name="a-idwindows-logs-xamarin-vs-namewindows-logs-xamarin-vs--visual-studio-tools-for-xamarin"></a><a id="windows-logs-xamarin-vs" name="windows-logs-xamarin-vs" /> Инструменты Visual Studio для Xamarin

`%LOCALAPPDATA%\Xamarin\Logs`

#### <a name="a-idvs-2017-namevs-2017--visual-studio-2017"></a><a id="vs-2017" name="vs-2017" /> Visual Studio 2017

[Как получить журналы установки Visual Studio](https://docs.microsoft.com/visualstudio/install/troubleshooting-installation-issues#how-to-get-the-visual-studio-installation-logs)

#### <a name="a-idvs-2015-namevs-2015--visual-studio-2015"></a><a id="vs-2015" name="vs-2015" /> Visual Studio 2015

#### <a name="a-idwindows-universal-installer-namewindows-universal-installer--xamarin-universal-installer"></a><a id="windows-universal-installer" name="windows-universal-installer" /> «Универсальный» установщик Xamarin

`%LOCALAPPDATA%\Xamarin\Universal`

Это журналы из `XamarinInstaller.exe` установщика.

#### <a name="a-idindividual-msi-installers-verbose-logs-nameindividual-msi-installers-verbose-logs-individual-msi-installers-verbose-logs"></a><a id="individual-msi-installers-verbose-logs" name="individual-msi-installers-verbose-logs" />Отдельные `.msi` установщики, подробных журналов

```csharp
msiexec /i Xamarin.msi /l*vx "%USERPROFILE%\Desktop\Xamarin.log"
```

Ссылка: [параметры командной строки](http://msdn.microsoft.com/library/aa367988.aspx)

#### <a name="a-idvisual-studio-startup-verbose-logs-namevisual-studio-startup-verbose-logs-visual-studio-startup-verbose-logs"></a><a id="visual-studio-startup-verbose-logs" name="visual-studio-startup-verbose-logs" />Запуск Visual Studio, подробных журналов

```csharp
devenv.exe /log "%USERPROFILE%\Desktop\VisualStudio.log"
```

Ссылка:  [ /log (devenv.exe)](http://msdn.microsoft.com/library/ms241272.aspx)

### <a name="a-idmac-logs-namemac-logs-mac-logs"></a><a id="mac-logs" name="mac-logs" />Журналы Mac

Можно выбрать **перейти > Перейти к папке** меню элемента в Finder, а затем скопируйте и вставьте любой из этих путей в диалоговом окне.

#### <a name="a-idmac-logs-visual-studio-namemac-logs-visual-studio-visual-studio-for-mac"></a><a id="mac-logs-visual-studio" name="mac-logs-visual-studio" />Visual Studio для Mac

`~/Library/Logs/VisualStudio/7.0` (это число может изменяться в зависимости от версии, которую вы используете)

Эту папку можно также открыть с помощью «"Справка"-> открыть каталог журналов».

#### <a name="a-idmac-logs-xamarin-studio-namemac-logs-xamarin-studio-xamarin-studio"></a><a id="mac-logs-xamarin-studio" name="mac-logs-xamarin-studio" />Xamarin Studio

`~/Library/Logs/XamarinStudio-6.0` (это число может изменяться в зависимости от версии, которую вы используете)

Эту папку можно также открыть с помощью «"Справка"-> открыть каталог журналов».

#### <a name="a-idmac-universal-installer-namemac-universal-installer-xamarin-universal-installer"></a><a id="mac-universal-installer" name="mac-universal-installer" />«Универсальный» установщик Xamarin

`~/Library/Logs/XamarinInstaller/Universal`

Это журналы из `XamarinInstaller.dmg` установщика.

#### <a name="a-idmac-build-host-namemac-build-host-xamarin-build-host"></a><a id="mac-build-host" name="mac-build-host" />Узел сборки Xamarin

`~/Library/Logs/Xamarin-[MAJOR.MINOR]`

## <a name="a-idverbose-build-output-logs-nameverbose-build-output-logs-verbose-build-output"></a><a id="verbose-build-output-logs" name="verbose-build-output-logs" />Выходные данные подробного сборки

1.  Включить [вывод диагностики MSBuild](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output).

2.  Для приложений iOS, следует также включить **mtouch подробного вывода** , добавив `-v -v -v -v` под **свойства проекта > сборка iOS > Общие (вкладка) > Дополнительные параметры > Дополнительные аргументы mtouch**.

3.  Выполните очистку и перестройте проект.

4.  Скопируйте и вставьте выходные данные построения в интегрированной среде разработки в текстовый файл.
     - Visual Studio (Windows): **представление > выходные данные > Показать выходные данные из: построение**
     - Visual Studio для Mac: **Вид > панели > ошибки > выходных данных сборки (вкладка ")**

## <a name="a-iddebug-logs-for-xamarin-apps-namedebug-logs-for-xamarin-apps-debug-logs-for-xamarinandroid-and-xamarinios-apps"></a><a id="debug-logs-for-xamarin-apps" name="debug-logs-for-xamarin-apps" />Отладочный журналы для приложений Xamarin.Android и Xamarin.iOS

### <a name="visual-studio-for-mac"></a>Visual Studio для Mac

**Вид > панели > выходные данные приложения**

(Обратите внимание, что данный элемент меню будет отображаться только в том случае, после запуска приложения.)

### <a name="visual-studio"></a>Visual Studio

**Представление > выходные данные > Показать выходные данные из: отладка**

### <a name="a-idadb-logcat-nameadb-logcat-android-adbhttpdeveloperandroidcomtoolshelpadbhtml-logcat-logs"></a><a id="adb-logcat" name="adb-logcat" />Android [ `adb` ](http://developer.android.com/tools/help/adb.html) logcat журналы

После выполнения команды `adb` команды, снова подключить **android_logcat.txt** файл с рабочего стола. Эти инструкции предполагают, что у вас есть только одно устройство.

См. также [Android журнал отладки](~/android/deploy-test/debugging/android-debug-log.md) страницы.

#### <a name="visual-studio"></a>Visual Studio

1. **Сервис > Android > запустить Командная строка Android Adb**
2. Очистка журнала: `adb logcat -c`
3. Воспроизведите проблему.
4. Выходные данные журнала: `adb logcat -vtime -d > "%USERPROFILE%\Desktop\android_logcat.txt"`

#### <a name="visual-studio-for-mac"></a>Visual Studio для Mac

1. **Сервис > открыть командную строку пакета SDK для Android**
2. Очистка журнала: `adb logcat -c`
3. Воспроизведите проблему.
4. Выходные данные журнала: `adb logcat -vtime -d > ~/Desktop/android_logcat.txt`

### <a name="a-idios-simulator-logs-nameios-simulator-logs-ios-simulator-logs-on-mac"></a><a id="ios-simulator-logs" name="ios-simulator-logs" />симулятор iOS входе (Mac)

* Чтобы открыть журнал системы, выберите **Отладка > Открыть журнал системы...**  в приложении симулятора iOS.

* Чтобы просмотреть отчеты о сбоях в симуляторе, откройте Console.app и перейдите к `~/Library/Logs > DiagnosticReports`.

### <a name="a-idios-device-logs-nameios-device-logs-ios-device-logs-on-mac"></a><a id="ios-device-logs" name="ios-device-logs" />устройство iOS входе (Mac)

#### <a name="visual-studio-for-mac"></a>Visual Studio для Mac

**Вид > панели > журнал устройств iOS**

#### <a name="xcode"></a>Xcode

**Окно > устройства > ${DeviceName}**

Отчеты о сбоях можно найти в разделе **Просмотр журналов устройства** кнопки. Системный журнал на наличие устройства отображается в нижней части окна в разделе стрелки.

#### <a name="xcode-5"></a>Xcode 5

**Окно > Организатор > устройств (вкладка) > ${DeviceName}**
