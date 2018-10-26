---
title: Objective-C платформ
description: В этом документе описывается внедрение .NET можно ориентироваться при работе с кодом Objective-C различных платформ. В нем описывается macOS, iOS, tvOS и watchOS.
ms.prod: xamarin
ms.assetid: 43253BE4-A03A-4646-9A14-32C05174E672
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 8091fb4e8328f61f1471d061b51b4735de3c089c
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108889"
---
# <a name="objective-c-platforms"></a>Objective-C платформ

Внедрение .NET можно предназначенных для различных платформ при создании кода Objective-C:

* macOS
* iOS
* tvOS
* [еще не реализован] watchOS

Платформа выбирается путем передачи `--platform=<platform>` внедрение .NET аргумент командной строки.

При построении для iOS, tvOS и watchOS платформ, внедрение .NET всегда создает структуру, содержащую Xamarin.iOS, так как Xamarin.iOS содержит массу кода поддержки среды выполнения, который необходим на этих платформах.

Тем не менее при построении для платформы macOS можно выбрать, должен ли созданный framework внедрить Xamarin.Mac или нет. Можно не внедрять Xamarin.Mac, если связанные сборки ссылается на Xamarin.Mac.dll (прямо или косвенно), и этот параметр выбран, передав `--platform=macOS` к средству внедрение .NET.

Если связанный сборка содержит ссылку на Xamarin.Mac.dll, необходимо внедрить Xamarin.Mac и Кроме того embeddinator необходимо знать, какой целевой платформы для использования.

Существует три возможных целевых платформ Xamarin.Mac: `modern` (прежнее название — `mobile`), `full` и `system` (разница между каждым описан в Xamarin.Mac [требуемой версии .NET framework] [ 1] документации), и каждый выбирается путем передачи `--platform=macOS-modern`, `--platform=macOS-full` или `--platform=macOS-system` к средству внедрение .NET.

[1]: ~/mac/platform/target-framework.md
