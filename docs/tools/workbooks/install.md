---
title: Требования к установке книг и
description: В этом документе описывается Загрузите и установите Xamarin книг, обсуждение поддерживаемых платформах и требования к системе.
ms.prod: xamarin
ms.assetid: 9D4E10E8-A288-4C6C-9475-02969198C119
author: topgenorth
ms.author: toopge
ms.date: 06/19/2018
ms.openlocfilehash: a35038a948a89889bbf067a453b7465c1a6a7b49
ms.sourcegitcommit: d70fcc6380834127fdc58595aace55b7821f9098
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268996"
---
# <a name="workbooks-installation-and-requirements"></a>Требования к установке книг и

<a name="install" />

## <a name="download-and-install"></a>Загрузка и установка

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

1. Проверьте [требования](#requirements) ниже.
2. Загрузите и установите [Xamarin книг для Windows](https://dl.xamarin.com/interactive/XamarinInteractive.msi).
3. Запуск [воспроизведение](~/tools/workbooks/workbook.md) с книгами или испытать [образцы](https://developer.xamarin.com/workbooks)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

1. Проверьте [требования](#Requirements) ниже.
2. Загрузите и установите [Xamarin книги для Mac](https://dl.xamarin.com/interactive/XamarinInteractive.pkg).
3. Запуск [воспроизведение](~/tools/workbooks/workbook.md) с книгами или испытать [образцы](https://developer.xamarin.com/workbooks)

-----

## <a name="requirements"></a>Требования

#### <a name="supported-operating-systems"></a>Supported Operating Systems

- **Mac** -OS X 10.11 или выше
- **Windows** — Windows 7 или более поздней (с помощью Internet Explorer 11 или выше и .NET 4.6.1 или более поздней)

#### <a name="supported-app-platforms"></a>Поддерживаемые приложения платформы

|Платформа приложений|Поддержка ОС|Примечания|
|--- |--- |--- |
|Mac|Поддерживаются только для Mac|
|iOS|Поддерживается на Mac и Windows|Xamarin.iOS 11.0 и Xcode версии 9.0 или более поздней, должны быть установлены на компьютере Mac. Необходим узел построения Mac выполнения всех указанных выше, для выполнения операций ввода-вывода книг в Windows и [удаленной симулятор iOS](~/tools/ios-simulator.md) установить в Windows.|
|Android|Поддерживается на Mac и Windows|Необходимо использовать эмулятор Google, Visual Studio или Xamarin Android, с виртуальным устройством > = 5.0|
|WPF|Поддерживается только в Windows|
|Консоль (.NET Framework)|Поддерживается на Mac и Windows|
|Консоль (.NET Core)|Поддерживается на Mac и Windows|


## <a name="reporting-bugs"></a>Регистрации ошибок

Проверьте [сообщить о проблемах на GitHub][bugs]и включают все следующие сведения:

### <a name="log-files"></a>Файлы журнала

Всегда Присоедините файлы журналов клиента книги:

- MAC: `~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

1.4.x также включает возможность выбора файла журнала в Finder (macOS) или в проводнике (Windows) непосредственно из главного меню:

- **Справка > отобразить файл журнала**

#### <a name="log-paths-for-workbooks-13-and-earlier"></a>Пути журнала для книг 1.3 и более ранних версий:

- MAC: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

### <a name="platform-version-information"></a>Сведения о версии платформы

Он очень полезно знать сведения об операционной системе и установленные продукты Xamarin.

В главном меню в книгах:

* **Справка > скопировать сведения о версии**

#### <a name="instructions-for-workbooks-13-and-earlier"></a>Инструкции для книг 1.3 и более ранних версий:

Visual Studio для Mac

- **Visual Studio > о Visual Studio > Показать подробности > скопировать сведения**
- Вставить в отчет об ошибках

Visual Studio

- **Справка > о Visual Studio > скопировать сведения**
- Сообщите нам о версии операционной системы и выполняется 32-разрядная или 64-разрядной Windows.

### <a name="samples"></a>Примеры

Если можно присоединить или связать **.workbooks** файла возникают затруднения, которые могут помочь устранить ошибку быстрее.

### <a name="devices"></a>Устройства

Если возникают затруднения при подключении устройства iOS или Android книги, а проверка уже [нашу страницу отзывов по устранению неполадок](~/tools/workbooks/troubleshooting/index.md), нам необходимо знать:

- Имя устройства, который вы пытаетесь подключиться к
- Версия ОС устройства
- Android: Убедитесь, что вы используете x86 эмулятора
- Android: Какие платформы эмулятор используется? Эмулятор Google?
  Эмулятор Android в Visual Studio? Xamarin Android Player?
- операций ввода-вывода в Windows: какая версия эмулятора IOS удаленного Xamarin установки (проверьте **Установка и удаление программ** в **панели управления**)?
- операций ввода-вывода в Windows: также укажите сведения о версии платформы для построения узла Mac
- Устройство имеет подключение к сети (проверка через веб-браузер)?

[bugs]: https://github.com/Microsoft/workbooks/issues/new

## <a name="uninstall"></a>Удалить

### <a name="windows"></a>Windows

В зависимости от того, как вы получили книги необходимо выполнить две процедуры удаления. Проверьте эти полностью удалить программное обеспечение.

#### <a name="visual-studio-installer"></a>Visual Studio Installer

При наличии 2017 г. для Visual Studio, откройте **установщик Visual Studio**и найдите в **отдельные компоненты** для **книги Xamarin**. Если он установлен, снимите этот флажок и нажмите кнопку **изменить** для удаления.

#### <a name="system-uninstall"></a>Удаление системы

Если вы установили книг самостоятельно Скачанный установщик, его необходимо установить через **приложений и возможности** страница параметров настройки системы в Windows 10 или с помощью **Установка и удаление программ** в элементе управления Панель в предыдущих версиях Windows.

> **Пуск > Параметры > Система > приложений и компонентов**

![](install-images/windows-remove.png "Xamarin книги, как указано в &quot;приложения &amp; функции&quot;")

**По-прежнему необходимо следовать процедура установщик Visual Studio, чтобы убедиться, что книги не получить переустановки без ведома пользователя.**

<a name="uninstall-macos" />

### <a name="macos"></a>macOS

Начиная с [1.2.2](https://developer.xamarin.com/releases/interactive/interactive-1.2/), Xamarin книги могут быть удалены из терминала, выполнив:

```bash
sudo /Library/Frameworks/Xamarin.Interactive.framework/Versions/Current/uninstall
```

Программа удаления подробно файлов и каталогов, она удаляет и запрашивать подтверждение перед продолжением.

Передайте `-help` аргумент `uninstall` скрипт для более сложных сценариев.

В более старых версиях требуется вручную удалить следующие компоненты:

1. Приложение Workbooks в `"/Applications/Xamarin Workbooks.app"`
2. Приложение Inspector в `"Applications/Xamarin Inspector.app"`
2. Надстройки `"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Interactive"` и `"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Inspector"`
3. Inspector и вспомогательные файлы `/Library/Frameworks/Xamarin.Interactive.framework` и `/Library/Frameworks/Xamarin.Inspector.framework`

## <a name="downgrading"></a>Понижение уровня

Идентификатор пакета для **Workbooks.app/Applications/Xamarin** изменилось с `com.xamarin.Inspector` для `com.xamarin.Workbooks` 1,4 выпуска, как книг и инспектора теперь полностью разделяются.

Из-за ошибки в более старых программ установки не удается понизить выпуски 1.4 или более поздней версии с помощью 1.3.2 или более старых программ установки.

Понизить 1.4 или более нового до 1.3.2 или старых:

1. [Вручную удалите книг & инспектора](#macOS)
2. Запустите 1.3.2 или более ранних версий `.pkg` установщика
