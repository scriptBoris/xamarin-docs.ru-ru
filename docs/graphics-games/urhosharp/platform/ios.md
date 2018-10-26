---
title: UrhoSharp iOS и tvOS поддержки
description: В этом документе рассматриваются iOS и tvOS поддержке UrhoSharp. Он описывает создание проекта, настроить и запустить Urho и выполнения пользовательских внедрения из Urho.
ms.prod: xamarin
ms.assetid: 7B06567E-E789-4EA1-A2A9-F3B2212EDD23
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: f15ae458c6bd613b59700908ad7c121315e377ab
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108279"
---
# <a name="urhosharp-ios-and-tvos-support"></a>UrhoSharp iOS и tvOS поддержки

Хотя Urho является переносимой библиотеке классов и разрешает один и тот же API для использования на различных платформах для логики игр по-прежнему необходимо инициализировать Urho в конкретного драйвера, платформы, а в некоторых случаях, вам потребуется воспользоваться преимуществами определенных функций платформы .

В указанные ниже страницы предполагается, что `MyGame` является sublcass из `Application` класса.

## <a name="ios-and-tvos"></a>iOS и tvOS

**Поддерживаемые архитектуры:** armv7, arm64, i386

## <a name="creating-a-project"></a>Создание проекта

Создать проект iOS, а затем добавить данные в каталог ресурсов и убедитесь, что у всех файлов **BundleResource** как **действие при построении**.

![Установка проектов](ios-images/image-4.png "Добавление данных в каталог ресурсов")

## <a name="configuring-and-launching-urho"></a>Настраивая и запуская Urho

Добавьте выражения using для `Urho` и `Urho.iOS` пространства имен, а затем добавьте следующий код для инициализации Urho, а также для запуска приложения:

```csharp
new MyGame().Run();
```

Обратите внимание, что, так как ожидает iOS `FinishedLaunching` для завершения, вы должны очередь вызов `Run()` для выполнения после завершения работы метода, это распространенных случаях:

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    LaunchGame();
    return true;
}

async void LaunchGame()
{
    await Task.Yield();
    new SamplyGame().Run();
}
```

Важно отключить оптимизацию PNG, так как оптимизатор PNG операций ввода-вывода по умолчанию создает образы, которые Urho не может использовать в настоящее время должным образом

## <a name="custom-embedding-of-urho"></a>Внедрение пользовательских Urho

Вы также к двум фактам: Urho возьмет на экране всего приложения, и для использования в качестве компонента приложения, можно создать `UrhoSurface` это `UIView` , можно внедрить в существующее приложение.

Это, вам потребуется сделать:

```csharp
var view = new UrhoSurface () {
    Frame = new CGRect (100,100,200,200),
    BackgroundColor = UIColor.Red
}
window.AddSubview (view);
```

Это будет размещаться класса Urho, таким образом, а затем это делается:

```csharp
new MyGame().Run ();
```

