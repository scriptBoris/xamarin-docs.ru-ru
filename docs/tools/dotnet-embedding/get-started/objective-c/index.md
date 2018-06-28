---
title: Приступая к работе с Objective-c.
description: В этом документе описывается как приступить к использованию внедрения .NET с целью-C. Обсуждаются требования, установка внедрения .NET NuGet и поддерживаемые платформы.
ms.prod: xamarin
ms.assetid: 4ABC0247-B608-42D4-89CB-D2E598097142
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 02d79103825d150b6e6f5bec7ed3ee1788078312
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37066631"
---
# <a name="getting-started-with-objective-c"></a>Приступая к работе с Objective-c.

Это странице началу работы для Objective-C, описываются основы работы для всех поддерживаемых платформ.

## <a name="requirements"></a>Требования

Чтобы использовать внедрения .NET с Objective-C, необходимо иметь Mac под управлением:

* macOS 10.12 (Сьерра) или более поздней версии
* Xcode 8.3.2 или более поздней версии
* [Моно 5.0](http://www.mono-project.com/download/)

Можно установить [Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/) для редактирования и компиляции кода C#.

> [!NOTE]
> * Более ранние версии macOS, Xcode и Mono _могут_ работать, но проверялась и не поддерживается
> * Создание кода может выполняться в Windows, но поддерживается только для этой компиляции на компьютере Mac, где установлены Xcode

## <a name="installing-net-embedding-from-nuget"></a>Установка .NET внедрение из NuGet

Выполните следующие [инструкции](~/tools/dotnet-embedding/get-started/install/install.md) для установки и настройки внедрения .NET для проекта.

Пример вызова команды, перечисленные в [macOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md) и [iOS](~/tools/dotnet-embedding/get-started/objective-c/ios.md) руководства по началу работы.

## <a name="platforms"></a>Платформы

Objective-C — это язык, чаще всего используется для создания приложений для macOS, iOS, tvOS и watchOS, внедрение .NET поддерживает все эти платформы. Работа с каждой из платформ подразумевает некоторые [здесь описываются основные различия и следующих](~/tools/dotnet-embedding/objective-c/platforms.md).

### <a name="macos"></a>macOS

[Создание приложения macOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md) является простым, поскольку не включает в себя столько дополнительные действия, такие как Настройка удостоверений, provisining профили, симуляторов и устройств. Вы, рекомендуется начать с документом macOS перед преобразованием для операций ввода-вывода.

### <a name="ios--tvos"></a>iOS и tvOS

Убедитесь, что вы уже настроенные для разработки приложений iOS перед попыткой создать его с помощью внедрения .NET. [Инструкциям](~/tools/dotnet-embedding/get-started/objective-c/ios.md) предполагается, что вы уже выполнил успешное создания и развертывания приложения iOS с компьютера.

Поддержку для tvOS, аналогичен работе операций ввода-вывода, с использованием только проекты tvOS интегрированные среды разработки (Visual Studio и Xcode) вместо проекты iOS.

> [!NOTE]
> Поддержка watchOS будут доступны в будущих выпусках и будет очень похожа на iOS и tvOS.

## <a name="further-reading"></a>Дополнительные сведения

* [Внедрение .NET функций, специфичных для Objective-c.](~/tools/dotnet-embedding/objective-c/index.md)
* [Советы и рекомендации для Objective-C](~/tools/dotnet-embedding/objective-c/best-practices.md)
* [Ограничения внедрения .NET](~/tools/dotnet-embedding/limitations.md)
* [Вклад в проект с открытым исходным кодом](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Коды ошибок и описания](~/tools/dotnet-embedding/errors.md)
* [Целевые платформы](~/tools/dotnet-embedding/objective-c/platforms.md)

## <a name="related-links"></a>Связанные ссылки

- [Пример Weather (iOS и macOS)](https://github.com/jamesmontemagno/embeddinator-weather)
