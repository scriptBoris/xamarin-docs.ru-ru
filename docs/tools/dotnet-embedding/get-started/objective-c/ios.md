---
title: Начало работы с iOS
description: В этом документе описывается, как приступить к использованию внедрение .NET с iOS. Он обсуждаются требования и представляет пример приложения, чтобы продемонстрировать, как привязать управляемую сборку и использовать выходные данные в проекте Xcode.
ms.prod: xamarin
ms.assetid: D5453695-69C9-44BC-B226-5B86950956E2
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: d61eb8f1ad1def764c8552b2f047aa46cd712018
ms.sourcegitcommit: ef04a4ae1b19c1854a8e4e8315516d4030f4bbd6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39654832"
---
# <a name="getting-started-with-ios"></a>Начало работы с iOS

## <a name="requirements"></a>Требования

В дополнение к требованиям из наших [Приступая к работе с Objective-C](~/tools/dotnet-embedding/get-started/objective-c/index.md) руководства, вам также потребуется:

* [Xamarin.iOS 10.11](https://visualstudio.microsoft.com/xamarin/) или более поздней версии

## <a name="hello-world"></a>Здравствуй, мир

Сначала нужно создайте простой hello world-пример на языке C#.

### <a name="create-c-sample"></a>Создание примера C#

Откройте Visual Studio для Mac, создайте новый проект библиотеки классов iOS, назовите его **hello из c#** и сохраните его для **~/Projects/hello-from-csharp**.

Замените код в **MyClass.cs** файла следующим фрагментом:

```csharp
using UIKit;
public class MyUIView : UITextView
{
    public MyUIView ()
    {
        Text = "Hello from C#";
    }
}
```

Построение проекта и итоговой сборки будет сохранен как **~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll**.

### <a name="bind-the-managed-assembly"></a>Привязать управляемой сборки

Когда управляемая сборка, привяжите его путем вызова внедрение .NET.

Как описано в разделе [установки](~/tools/dotnet-embedding/get-started/install/install.md) руководство, это можно сделать в качестве шага после сборки в проекте с пользовательский целевой объект MSBuild, или вручную:

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=iOS --outdir=output -c --debug
```

Платформы будут помещены в **~/Projects/hello-from-csharp/output/hello-from-csharp.framework**.

### <a name="use-the-generated-output-in-an-xcode-project"></a>Использовать созданные выходные данные в проекте Xcode

Откройте Xcode, создание новых iOS приложение одного представления, назовите его **hello из c#** и выберите **Objective-C** языка.

Откройте **~/Projects/hello-from-csharp/output** каталог в Finder, выберите **hello из csharp.framework**, перетащите его в проект Xcode и поместите его над **hello из c#**  в папку проекта.

![Перетаскивание framework](ios-images/hello-from-csharp-ios-drag-drop-framework.png)

Убедитесь, что **копировать элементы при необходимости** проверяется в диалоговом окне, появившемся и нажмите кнопку **Готово**.

![Копировать элементы при необходимости](ios-images/hello-from-csharp-ios-copy-items-if-needed.png)

Выберите **hello из c#** проекта и перейдите к **hello из c#** целевого объекта **вкладке "Общие"**. В **Embedded двоичный** добавьте **hello из csharp.framework**.

![Внедренные двоичных файлов](ios-images/hello-from-csharp-ios-embedded-binaries.png)

Откройте **ViewController.m**и замените его содержимое с помощью:

```objective-c
#import "ViewController.h"
#include "hello-from-csharp/hello-from-csharp.h"

@interface ViewController ()
@end

@implementation ViewController
- (void)viewDidLoad {
    [super viewDidLoad];

    MyUIView *view = [[MyUIView alloc] init];
    view.frame = CGRectMake(0, 200, 200, 200);
    [self.view addSubview: view];
}
@end
```

Внедрение .NET не поддерживает bitcode в iOS, которая включается для некоторых шаблонов проекта Xcode. 

Его следует отключите в настройках проекта:

![Параметр Bitcode](../../images/ios-bitcode-option.png)

Наконец, запустите проект Xcode, и будет отображаться примерно следующее:

![Привет от пример на C# запуск в симуляторе](ios-images/hello-from-csharp-ios.png)
