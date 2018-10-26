---
title: Требования к установке инспектора и
description: В этом документе описываются способы установки Xamarin Inspector и поддерживаемой операционной системы, интегрированные среды разработки и платформ приложений.
ms.prod: xamarin
ms.assetid: 81174493-02D3-4FF5-AD57-04F3288A7F94
author: lobrien
ms.author: laobri
ms.date: 06/19/2018
ms.openlocfilehash: 2357003e3a855981f053c48a596b932d9ba36d90
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50104965"
---
# <a name="inspector-installation-and-requirements"></a>Требования к установке инспектора и

## <a name="download-and-installation"></a>Загрузка и установка

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

1. Скачайте и установите [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/) и выберите **разработки мобильных приложений на .NET** рабочей нагрузки.
1. [Войдите в](https://docs.microsoft.com/visualstudio/ide/signing-in-to-visual-studio) Чтобы включить подписку Enterprise.
1. [Проверьте](~/tools/inspector/inspect.md) свое собственное приложение!

# <a name="macostabmacos"></a>[macOS](#tab/macos)

1. Скачайте и установите [Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/).
1. [Войдите в](https://docs.microsoft.com/visualstudio/mac/activation) Чтобы включить подписку Enterprise.
1. [Проверьте](~/tools/inspector/inspect.md) свое собственное приложение!

-----

## <a name="requirements"></a>Требования

### <a name="supported-operating-systems"></a>Supported Operating Systems

- **Mac** -OS X 10.11 или более поздней версии
- **Windows** -Windows 7 или более поздней версии (с помощью Internet Explorer 11 или более поздней версии и .NET 4.6.1 или более поздней версии)

### <a name="supported-ides"></a>Поддерживаемые интегрированные среды разработки

- Visual Studio для Mac
- Visual Studio 2017 с **разработки мобильных приложений на .NET** рабочей нагрузки

Проверка активного приложения доступна для корпоративных клиентов.

<a name="supported-platforms" />

### <a name="supported-app-platforms"></a>Поддерживаемые приложения платформы

|Платформа приложений|Поддержка IDE|Примечания|
|--- |--- |--- |
|Mac|Поддерживается только в Visual Studio для Mac|
|iOS|Поддерживается в Visual Studio 2017 и Visual Studio для Mac| |
|Android|Поддерживается в Visual Studio 2017 и Visual Studio для Mac|Необходимо применять Android > = 4.0.3, с помощью **fastdev** включена.<br />Необходимо использовать эмуляторы Google, Visual Studio или Xamarin Android. Эмуляторы Android 7 не должен позволять проверки в данный момент.|
|WPF|Поддерживается только в Visual Studio 2017|

<a name="reporting-bugs" />

## <a name="reporting-bugs"></a>Сообщения об ошибках

Ошибки должны сопровождаться непосредственно с помощью Visual Studio:

- **Справка > Отправить отзыв > сообщить о проблеме**

Укажите все следующие сведения:

### <a name="platform-version-information"></a>Сведения о версии платформы

Эта информация является жизненно важных.

Visual Studio для Mac

- **Visual Studio > о Visual Studio > Показать подробности > Копировать сведения**
- Вставьте в отчет об ошибках

Visual Studio

- **Справка > о Visual Studio > скопировать сведения**
- Расскажите нам о версии операционной системы и выполняется 32-разрядная или 64-разрядной Windows.

### <a name="log-files"></a>Файлы журнала

Всегда вложения интегрированной среды разработки и инспектор файлов журнала клиента.

Инспектор клиента

- MAC: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

1.4.x также предоставляет возможность выбора файла журнала в Finder (macOS) или в обозревателе (Windows) непосредственно из главного меню:

- **Справка > открыть файл журнала**

Visual Studio для Mac

- `~/Library/Logs/VisualStudio/7.0/Ide.log`

Visual Studio

- `%LOCALAPPDATA%\Xamarin\Logs\{VS version}\Inspector {date}.log`
- Содержимое в Visual Studio **вывода** панель также может быть информативные.

### <a name="project-settings"></a>Параметры проекта

Если вы можете подключить **.csproj** для проекта, вы пытаетесь проверить, было бы очень полезно. Это проще, чем вопросом о отдельные параметры.

Также убедитесь, что вы находитесь в конфигурации отладки.

### <a name="selected-devices"></a>Выбранные устройства

Для Android и iOS крайне важно, что мы знаем, какое устройство вы отладке, когда требуется проверить. Нам нужно знать:

- Имя устройства, как показано в интерфейсе IDE
- Версия ОС устройства
- Android: Убедитесь, что вы используете x86 эмулятора
- Android — Платформы эмулятора вы используете? Эмулятор Google? Эмулятор Android Visual Studio? Xamarin Android Player?
- Правильно отлаживаемое приложение выглядеть и работать на устройстве?
- Устройство имеет сетевое подключение (Ознакомьтесь с веб-браузер)?

[client-bugs]: https://github.com/Microsoft/workbooks/issues/new
