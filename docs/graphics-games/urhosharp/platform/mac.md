---
title: Поддержка UrhoSharp Mac
description: В этом документе рассматривается поддержка UrhoSharp в macOS. Он описывает, как создать проект и ссылки на пример кода.
ms.prod: xamarin
ms.assetid: 95FFBD36-14E9-4C17-B1E8-9A04E81E824D
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 6d0a048020284319682c1bee0f9a1d7f9af00977
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113876"
---
# <a name="urhosharp-mac-support"></a>Поддержка UrhoSharp Mac

_Настройка определенных MAC и компоненты_

Хотя Urho является переносимой библиотеке классов и разрешает один и тот же API для использования на различных платформах для логики игр по-прежнему необходимо инициализировать Urho в конкретного драйвера, платформы, а в некоторых случаях, вам потребуется воспользоваться преимуществами определенных функций платформы .

В указанные ниже страницы предполагается, что `MyGame` является подклассом `Application` класса.

## <a name="macos"></a>macOS

**Поддерживаемые архитектуры:** x86/x86-64 для 32-разрядных и 64-разрядная версия.

## <a name="creating-a-project"></a>Создание проекта

Создайте проект консольного, ссылаются на Urho NuGet и убедитесь, что можно найти ресурсы (каталоги, содержащие каталог данных).

```csharp
DesktopUrhoInitializer.AssetsDirectory = "../Assets";
new MyGame().Run();
```

## <a name="example"></a>Пример

[Полный пример](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/Cocoa)


