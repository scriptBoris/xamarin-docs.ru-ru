---
title: Требования к установке инспектора и
description: В этом документе описывается установка инспектора Xamarin и описание поддерживаемых операционных систем, интегрированных средах разработки и платформах для приложений.
ms.prod: xamarin
ms.assetid: 81174493-02D3-4FF5-AD57-04F3288A7F94
author: topgenorth
ms.author: toopge
ms.date: 06/19/2018
ms.openlocfilehash: f7c5217a9c2d3881ca29094c3186e448975db6a3
ms.sourcegitcommit: d70fcc6380834127fdc58595aace55b7821f9098
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268973"
---
# <a name="inspector-installation-and-requirements"></a>Требования к установке инспектора и

## <a name="download-and-installation"></a>Загрузка и установка

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

1. Загрузите и установите [Visual Studio Enterprise](https://www.visualstudio.com/vs/) и выберите **Разработка мобильных приложений в .NET Framework** рабочей нагрузки.
1. [Войдите в](https://docs.microsoft.com/visualstudio/ide/signing-in-to-visual-studio) для включения подписки Enterprise.
1. [Проверить](~/tools/inspector/inspect.md) вашего собственного приложения!

# <a name="macostabmacos"></a>[macOS](#tab/macos)

1. Загрузите и установите [Visual Studio для Mac](https://www.visualstudio.com/vs/mac/).
1. [Войдите в](https://docs.microsoft.com/visualstudio/mac/activation) для включения подписки Enterprise.
1. [Проверить](~/tools/inspector/inspect.md) вашего собственного приложения!

-----

## <a name="requirements"></a>Требования

### <a name="supported-operating-systems"></a>Supported Operating Systems

- **Mac** -OS X 10.11 или выше
- **Windows** — Windows 7 или более поздней (с помощью Internet Explorer 11 или выше и .NET 4.6.1 или более поздней)

### <a name="supported-ides"></a>Поддерживаемые интегрированными средами разработки

- Visual Studio для Mac
- Visual Studio 2017 г. с **Разработка мобильных приложений в .NET Framework** рабочей нагрузки

Проверка работающем приложении доступен для корпоративных клиентов.

<a name="supported-platforms" />

### <a name="supported-app-platforms"></a>Поддерживаемые приложения платформы

|Платформа приложений|Поддержка интегрированной среды разработки|Примечания|
|--- |--- |--- |
|Mac|Поддерживается только в Visual Studio для Mac|
|iOS|Поддерживается в Visual Studio 2017 г. и Visual Studio для Mac| |
|Android|Поддерживается в Visual Studio 2017 г. и Visual Studio для Mac|Должны быть предназначены Android > = 4.0.3, с **fastdev** включена.<br />Необходимо использовать эмуляторы Google, Visual Studio и Xamarin Android. Эмуляторы Android 7 не может разрешить проверки в данный момент.|
|WPF|Поддерживается только в Visual Studio 2017 г.|

<a name="reporting-bugs" />

## <a name="reporting-bugs"></a>Регистрации ошибок

Должна быть представлена ошибок непосредственно через Visual Studio:

- **Справка > Отправить отзыв > сообщить о проблеме**

Включите все следующие сведения:

### <a name="platform-version-information"></a>Сведения о версии платформы

Эта информация является жизненно важной.

Visual Studio для Mac

- **Visual Studio > о Visual Studio > Показать подробности > скопировать сведения**
- Вставить в отчет об ошибках

Visual Studio

- **Справка > о Visual Studio > скопировать сведения**
- Сообщите нам о версии операционной системы и выполняется 32-разрядная или 64-разрядной Windows.

### <a name="log-files"></a>Файлы журнала

Всегда присоедините интегрированной среды разработки и инспектор файлы журналов клиента.

Инспектор клиента

- MAC: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

1.4.x также включает возможность выбора файла журнала в Finder (macOS) или в проводнике (Windows) непосредственно из главного меню:

- **Справка > отобразить файл журнала**

Visual Studio для Mac

- `~/Library/Logs/VisualStudio/7.0/Ide.log`

Visual Studio

- `%LOCALAPPDATA%\Xamarin\Logs\{VS version}\Inspector {date}.log`
- Содержимое Visual Studio **вывода** область может быть информативной.

### <a name="project-settings"></a>Параметры проекта

Если можно присоединить **.csproj** для проекта, который вы пытаетесь проверить, было бы очень полезны. Это проще, чем охватив отдельных параметров.

Также убедитесь, что вы находитесь в конфигурации отладки.

### <a name="selected-devices"></a>Выбранные устройства

Для Android и iOS крайне важно, что мы знаем, какое устройство при отладке, когда требуется проверить. Нам нужно знать следующее:

- Имя устройства, как показано в интерфейс IDE
- Версия ОС устройства
- Android: Убедитесь, что вы используете x86 эмулятора
- Android: Какие платформы эмулятор используется? Эмулятор Google? Эмулятор Android в Visual Studio? Xamarin Android Player?
- Правильно отлаживаемого приложения выглядеть и работать на устройстве?
- Устройство имеет подключение к сети (проверка через веб-браузер)?

[client-bugs]: https://github.com/Microsoft/workbooks/issues/new
