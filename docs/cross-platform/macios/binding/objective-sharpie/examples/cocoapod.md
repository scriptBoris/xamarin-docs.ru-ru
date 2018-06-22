---
title: Реальный пример, с помощью CocoaPods
description: В этом документе показано, как использовать Sharpie цели для автоматического создания определения привязок C# из CocoaPod.
ms.prod: xamarin
ms.assetid: 233B781D-5841-4250-9F63-0585231D2112
author: asb3993
ms.author: amburns
ms.date: 03/28/2018
ms.openlocfilehash: 026b2c46f7c294d4ac4a110376131ec83c7c112e
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33947398"
---
# <a name="real-world-example-using-cocoapods"></a>Реальный пример, с помощью CocoaPods

> [!NOTE]
> В этом примере используется [AFNetworking CocoaPod](https://cocoapods.org/pods/AFNetworking).

Появился в версии 3.0 поддерживает привязывание CocoaPods Sharpie цель и даже содержит команду (`sharpie pod`) для загрузки, Настройка и сборка CocoaPods очень легко. Вы должны [ознакомиться с CocoaPods](https://cocoapods.org) перед использованием этой функции в целом.

## <a name="creating-a-binding-for-a-cocoapod"></a>Создание привязки для CocoaPod

`sharpie pod` Команда имеет один глобальный параметр и два подкоманд:

```bash
$ sharpie pod -help
usage: sharpie pod [OPTIONS] COMMAND [COMMAND_OPTIONS]

Pod Options:
  -d, -dir DIR     Use DIR as the CocoaPods binding directory,
                   defaulting to the current directory

Available Commands:
  init         Initialize a new Xamarin C# CocoaPods binding project
  bind         Bind an existing Xamarin C# CocoaPods project
```

`init` Подкоманда также содержит некоторые полезные справки:

```bash
$ sharpie pod init -help
usage: sharpie pod init [INIT_OPTIONS] TARGET_SDK POD_SPEC_NAMES

Init Options:
  -f, -force       Initialize a new Podfile and run actions against
                   it even if one already exists
```

Несколько имен CocoaPod и subspec имена могут быть предоставлены для `init`.

```bash
$ sharpie pod init ios AFNetworking
** Setting up CocoaPods master repo ...
   (this may take a while the first time)
** Searching for requested CocoaPods ...
** Working directory:
**   - Writing Podfile ...
**   - Installing CocoaPods ...
**     (running `pod install --no-integrate --no-repo-update`)
Analyzing dependencies
Downloading dependencies
Installing AFNetworking (2.6.0)
Generating Pods project
Sending stats
** 🍻 Success! You can now use other `sharpie podn`  commands.
```

После настройки вашей CocoaPod теперь можно создать привязки.

```bash
$ sharpie pod bind
```

Это приведет к проекту CocoaPod Xcode, выполняется построение и затем вычисляются и анализировать Sharpie цель. Большой объем выходных данных консоли будет создан, но следует вызвать определение привязки в конце:

```bash
(... lots of build output ...)

Parsing 19 header files...

Binding...
  [write] ApiDefinitions.cs
  [write] StructsAndEnums.cs

Done.
```

## <a name="next-steps"></a>Следующие шаги

После создания **ApiDefinitions.cs** и **StructsAndEnums.cs** файлы, рассмотрим следующую документацию для создания сборки для использования в приложениях:

- [Общие сведения о привязке к цели деятельности организации C](~/cross-platform/macios/binding/overview.md)
- [Библиотеки C цель привязки](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Пошаговое руководство: Привязка библиотеку Objective-C iOS](~/ios/platform/binding-objective-c/walkthrough.md)

