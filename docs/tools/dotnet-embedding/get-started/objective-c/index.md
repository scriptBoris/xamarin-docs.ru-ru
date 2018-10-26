---
title: Приступая к работе с Objective-C
description: Здесь описано, как приступить к использованию внедрение .NET с Objective-C. Обсуждаются требования, установка внедрение .NET из NuGet и поддерживаемые платформы.
ms.prod: xamarin
ms.assetid: 4ABC0247-B608-42D4-89CB-D2E598097142
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: c7bac0612679131383d3b89f24904c8083fa925b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103105"
---
# <a name="getting-started-with-objective-c"></a>Приступая к работе с Objective-C

Это начальной странице начала работы для Objective-C, основные сведения для всех поддерживаемых платформ.

## <a name="requirements"></a>Требования

Чтобы использовать внедрение .NET с Objective-C, вам потребуется компьютер Mac с:

* macOS 10.12 (Sierra) или более поздней версии
* Xcode 8.3.2 или более поздней версии
* [Mono 5.0](http://www.mono-project.com/download/)

Вы можете установить [Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/) для редактирования и компиляции вашей C# кода.

> [!NOTE]
> * Более ранних версий macOS, Xcode и Mono _может_ работать, но проверялась и не поддерживается
> * Создание кода может быть выполнена на Windows, но поддерживается только для его компиляции на компьютере Mac, где она установлена

## <a name="installing-net-embedding-from-nuget"></a>Установка внедрение .NET из NuGet

Следуйте этим [инструкции](~/tools/dotnet-embedding/get-started/install/install.md) для установки и настройки внедрение .NET для вашего проекта.

Пример вызова команды, перечисленные в [macOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md) и [iOS](~/tools/dotnet-embedding/get-started/objective-c/ios.md) руководства по началу работы.

## <a name="platforms"></a>Платформы

Objective-C — это язык, который чаще всего используется для создания приложений для macOS, iOS, tvOS и watchOS; внедрение .NET поддерживает всех этих платформ. Работа с каждой платформы подразумевает некоторые [здесь объясняются ключевые различия и их](~/tools/dotnet-embedding/objective-c/platforms.md).

### <a name="macos"></a>macOS

[Создание приложения macOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md) является простым, так как она не использует столько дополнительных действий, таких как Настройка удостоверения, provisining профили, симуляторы и устройства. Вы, рекомендуется начать с документа macOS перед преобразованием для iOS.

### <a name="ios--tvos"></a>iOS и tvOS

Убедитесь, что вы уже настроенные для разработки приложений iOS, прежде чем пытаться создать ее с помощью внедрение .NET. [Инструкциями](~/tools/dotnet-embedding/get-started/objective-c/ios.md) предполагается, что вы уже успешно создали и развернули приложение iOS с компьютера.

Поддержка tvOS аналогично работе iOS, просто используя проектов tvOS в IDE (Visual Studio и Xcode) вместо проектов iOS.

> [!NOTE]
> Поддержка будет предоставляться в будущем выпуске watchOS и будет очень похожа на iOS и tvOS.

## <a name="further-reading"></a>Дополнительные сведения

* [Внедрение .NET функций, специфичных для Objective-C](~/tools/dotnet-embedding/objective-c/index.md)
* [Советы и рекомендации для Objective-C](~/tools/dotnet-embedding/objective-c/best-practices.md)
* [Ограничения внедрение .NET](~/tools/dotnet-embedding/limitations.md)
* [Вносящая вклад в проект с открытым кодом](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Коды ошибок и описания](~/tools/dotnet-embedding/errors.md)
* [Целевые платформы](~/tools/dotnet-embedding/objective-c/platforms.md)

## <a name="related-links"></a>Связанные ссылки

- [Пример погоды (iOS и macOS)](https://github.com/jamesmontemagno/embeddinator-weather)
