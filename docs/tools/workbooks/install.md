---
title: Требования к установке книг и
description: В этом документе описывается, как загрузить и установить Xamarin Workbooks, посвященные поддерживаемых платформ и требования к системе.
ms.prod: xamarin
ms.assetid: 9D4E10E8-A288-4C6C-9475-02969198C119
author: lobrien
ms.author: laobri
ms.date: 06/19/2018
ms.openlocfilehash: 7bef5de57b7ac709ebab4c39feedbec369e6bd14
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122430"
---
# <a name="workbooks-installation-and-requirements"></a>Требования к установке книг и

<a name="install" />

## <a name="download-and-install"></a>Скачайте и установите

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

1. Проверьте [требования](#requirements) ниже.
2. Скачайте и установите [Xamarin книг для Windows](https://dl.xamarin.com/interactive/XamarinInteractive.msi).
3. Запуск [баловаться](~/tools/workbooks/workbook.md) с книгами или Испытайте [примеры](https://developer.xamarin.com/workbooks)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

1. Проверьте [требования](#Requirements) ниже.
2. Скачайте и установите [Xamarin Workbooks для Mac](https://dl.xamarin.com/interactive/XamarinInteractive.pkg).
3. Запуск [баловаться](~/tools/workbooks/workbook.md) с книгами или Испытайте [примеры](https://developer.xamarin.com/workbooks)

-----

## <a name="requirements"></a>Требования

#### <a name="supported-operating-systems"></a>Supported Operating Systems

- **Mac** -OS X 10.11 или более поздней версии
- **Windows** -Windows 7 или более поздней версии (с помощью Internet Explorer 11 или более поздней версии и .NET 4.6.1 или более поздней версии)

#### <a name="supported-app-platforms"></a>Поддерживаемые приложения платформы

|Платформа приложений|Поддержка ОС|Примечания|
|--- |--- |--- |
|Mac|Поддерживается только на компьютере Mac|
|iOS|Поддерживается в Mac и Windows|Xamarin.iOS 11.0 и Xcode 9.0 или более поздней версии, должны быть установлены на компьютере Mac. Запускать книги iOS на Windows требует выполнения всех указанных выше, узлу сборки Mac и [удаленный симулятор iOS](~/tools/ios-simulator/index.md) установлен на Windows.|
|Android|Поддерживается в Mac и Windows|Необходимо использовать эмулятор Google, Visual Studio или Xamarin Android, с виртуальным устройством > = 5.0|
|WPF|Поддерживается только в Windows|
|Консоль (.NET Framework)|Поддерживается в Mac и Windows|
|Консоль (.NET Core)|Поддерживается в Mac и Windows|


## <a name="reporting-bugs"></a>Сообщения об ошибках

Пожалуйста [сообщить о проблемах на GitHub][bugs]и включают все следующие сведения:

### <a name="log-files"></a>Файлы журнала

Всегда Присоедините файлы журналов клиента книги:

- MAC: `~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

1.4.x также предоставляет возможность выбора файла журнала в Finder (macOS) или в обозревателе (Windows) непосредственно из главного меню:

- **Справка > открыть файл журнала**

#### <a name="log-paths-for-workbooks-13-and-earlier"></a>Пути журнала для книг 1.3 и более ранних версий:

- MAC: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

### <a name="platform-version-information"></a>Сведения о версии платформы

Это очень полезно узнать об операционной системе, установленных продуктов Xamarin.

В главном меню в книгах:

* **Справка > скопировать сведения о версии**

#### <a name="instructions-for-workbooks-13-and-earlier"></a>Инструкции для книг 1.3 и более ранних версий:

Visual Studio для Mac

- **Visual Studio > о Visual Studio > Показать подробности > Копировать сведения**
- Вставьте в отчет об ошибках

Visual Studio

- **Справка > о Visual Studio > скопировать сведения**
- Расскажите нам о версии операционной системы и выполняется 32-разрядная или 64-разрядной Windows.

### <a name="samples"></a>Примеры

Если можно присоединить или связать **.workbooks** файл, возникают неполадки, и, которые могут помочь устранить ошибку, более быстро.

### <a name="devices"></a>Устройства

Если возникают проблемы с подключением, iOS или Android книги и проверка уже [нашей странице](~/tools/workbooks/troubleshooting/index.md), нам нужно будет знать:

- Имя устройства, который вы пытаетесь подключиться к
- Версия ОС устройства
- Android: Убедитесь, что вы используете x86 эмулятора
- Android — Платформы эмулятора вы используете? Эмулятор Google?
  Эмулятор Android Visual Studio? Xamarin Android Player?
- iOS на Windows: какая версия симулятор iOS удаленной работы для Xamarin установки (проверьте **Add/Remove Programs** в **панели управления**)?
- iOS на Windows: также укажите сведения о версии платформы для узла сборки Mac
- Устройство имеет сетевое подключение (Ознакомьтесь с веб-браузер)?

[bugs]: https://github.com/Microsoft/workbooks/issues/new

## <a name="uninstall"></a>Удалить

### <a name="windows"></a>Windows

В зависимости от того, как вы получили книги необходимо выполнить две процедуры удаления. Проверьте оба варианта, чтобы полностью удалить программное обеспечение.

#### <a name="visual-studio-installer"></a>Visual Studio Installer

Если у вас есть Visual Studio 2017, откройте **установщик Visual Studio**и найдите в **отдельные компоненты** для **Xamarin Workbooks**. Если он установлен, снимите соответствующий флажок и нажмите кнопку **изменить** для удаления.

#### <a name="system-uninstall"></a>Удаление системы

Если вы установили книг самостоятельно с помощью загруженного установщика, его необходимо удалить с помощью **приложения и возможности** страница параметров настройки системы в Windows 10 или с помощью **Add/Remove Programs** в элементе управления Панель в старых версиях Windows.

> **Пуск > Параметры > Система > программы и компоненты**

![](install-images/windows-remove.png "Xamarin Workbooks, как указано в &quot;приложений &amp; функции&quot;")

**По-прежнему необходимо следовать процедуре для Visual Studio Installer, чтобы убедиться в том, книги не получить переустановлен без вашего ведома.**

<a name="uninstall-macos" />

### <a name="macos"></a>macOS

Начиная с [1.2.2](https://developer.xamarin.com/releases/interactive/interactive-1.2/), Xamarin Workbooks могут быть удалены из терминала, выполнив:

```bash
sudo /Library/Frameworks/Xamarin.Interactive.framework/Versions/Current/uninstall
```

Программа удаления подробно, файлы и каталоги, она удаляет и запрашивать подтверждение, прежде чем продолжить.

Передайте `-help` аргумент `uninstall` скрипт для более сложных сценариев.

В более старых версиях требуется вручную удалить следующие компоненты:

1. Приложение Workbooks в `"/Applications/Xamarin Workbooks.app"`
2. Приложение Inspector в `"Applications/Xamarin Inspector.app"`
2. Надстройки `"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Interactive"` и `"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Inspector"`
3. Inspector и вспомогательные файлы `/Library/Frameworks/Xamarin.Interactive.framework` и `/Library/Frameworks/Xamarin.Inspector.framework`

## <a name="downgrading"></a>Понижение уровня

Идентификатор пакета для **Workbooks.app приложения/Xamarin** изменилось с `com.xamarin.Inspector` для `com.xamarin.Workbooks` в выпуск 1.4, как Workbooks и Inspector теперь полностью разделены.

Из-за ошибки в более старых установщики это не возможность возврата к предыдущей версии 1.4 или более новые выпуски, с использованием 1.3.2 или более старых установщиков.

Понизить 1.4 или более нового до 1.3.2 или более ранней версии:

1. [Удаление Workbooks и Inspector вручную](#macOS)
2. Запустите 1.3.2 или более ранней версии `.pkg` установщика
