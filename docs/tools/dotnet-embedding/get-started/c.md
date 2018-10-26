---
title: Начало работы с C#
description: В этом документе описывается, как использовать внедрение .NET для внедрения кода .NET в приложении C. Он описывает, как использовать внедрение .NET в Visual Studio 2017 и Visual Studio для Mac.
ms.prod: xamarin
ms.assetid: 2A27BE0F-95FB-4C3A-8A43-72540179AA85
author: lobrien
ms.author: laobri
ms.date: 04/19/2018
ms.openlocfilehash: a16821e83dc169d7800162e1eaf45c4be661185a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106810"
---
# <a name="getting-started-with-c"></a>Начало работы с C#

## <a name="requirements"></a>Требования

Чтобы использовать внедрение .NET с помощью c#, вам потребуется работающего компьютера Mac или Windows:

### <a name="macos"></a>macOS

* macOS 10.12 (Sierra) или более поздней версии
* Xcode 8.3.2 или более поздней версии
* [Mono](http://www.mono-project.com/download/)

### <a name="windows"></a>Windows

* Windows 7, 8, 10 или более поздней версии
* Visual Studio 2015 или более поздней версии

## <a name="installing-net-embedding-from-nuget"></a>Установка внедрение .NET из NuGet

Следуйте этим [инструкции](~/tools/dotnet-embedding/get-started/install/install.md) для установки и настройки внедрение .NET для вашего проекта.

Вызова команды, которые нужно настроить выглядят следующим образом (с разными номерами версий и путей):

### <a name="visual-studio-for-mac"></a>Visual Studio для Mac

```shell
mono {SolutionDir}/packages/Embeddinator-4000.0.4.0.0/tools/Embeddinator-4000.exe --gen=c --outdir=managed_c --platform=macos --compile managed.dll
```

### <a name="visual-studio-2017"></a>Visual Studio 2017

```shell
$(SolutionDir)\packages\Embeddinator-4000.0.2.0.80\tools\Embeddinator-4000.exe --gen=c --outdir=managed_c --platform=windows --compile managed.dll
```

## <a name="generation"></a>Создание

### <a name="output-files"></a>Выходные файлы

Если все работает правильно, появится следующий результат:

```shell
Parsing assemblies...
    Parsed 'managed.dll'
Processing assemblies...
Generating binding code...
    Generated: managed.h
    Generated: managed.c
    Generated: mscorlib.h
    Generated: mscorlib.c
    Generated: embeddinator.h
    Generated: glib.c
    Generated: glib.h
    Generated: mono-support.h
    Generated: mono_embeddinator.c
    Generated: mono_embeddinator.h
```

Так как `--compile` флаг был передан в средство, внедрение .NET должен также скомпилированных выходных файлов в общей библиотеки, который можно найти рядом с созданные файлы, **libmanaged.dylib** файл на macOS и **managed.dll** на Windows.

Для использования общей библиотеки, можно включить **managed.h** заголовочный файл C, который предоставляет C объявления, соответствующих на соответствующие управляемые API библиотек и выполните компоновку с ранее упомянутыми скомпилированные общей библиотеки.

## <a name="further-reading"></a>Дополнительные сведения

* [Ограничения внедрение .NET](~/tools/dotnet-embedding/limitations.md)
* [Вносящая вклад в проект с открытым кодом](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Коды ошибок и описания](~/tools/dotnet-embedding/errors.md)
