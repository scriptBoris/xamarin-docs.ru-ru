---
title: Приступая к работе с macOS
description: Здесь описано, как приступить к использованию внедрение .NET с помощью macOS. Обсуждаются требования и представляет пример приложения, чтобы продемонстрировать, как привязать управляемую сборку и использовать созданные выходные данные в проекте Xcode.
ms.prod: xamarin
ms.assetid: AE51F523-74F4-4EC0-B531-30B71C4D36DF
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 3de47aa57df29f52f71508977ae017dff009c282
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121572"
---
# <a name="getting-started-with-macos"></a>Приступая к работе с macOS

## <a name="what-you-will-need"></a>Необходимо будет

* Следуйте инструкциям в [Приступая к работе с Objective-C](~/tools/dotnet-embedding/get-started/objective-c/index.md) руководства.

## <a name="hello-world"></a>Здравствуй, мир

Сначала нужно создайте простой hello world-пример на языке C#.

### <a name="create-c-sample"></a>Создание примера C#

Откройте Visual Studio для Mac, создайте новый проект библиотеки классов Mac с именем **hello из c#** и сохраните его для **~/Projects/hello-from-csharp**.

Замените код в **MyClass.cs** файла следующим фрагментом:

```csharp
using AppKit;
public class MyNSView : NSTextView
{
    public MyNSView ()
    {
        Value = "Hello from C#";
    }
}
```

Выполните построение проекта. Итоговой сборки будет сохранен как **~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll**.

### <a name="bind-the-managed-assembly"></a>Привязать управляемой сборки

Когда управляемая сборка, привяжите его путем вызова внедрение .NET.

Как описано в разделе [установки](~/tools/dotnet-embedding/get-started/install/install.md) руководство, это можно сделать в качестве шага после сборки в проекте с пользовательский целевой объект MSBuild, или вручную:

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=macOS-modern --abi=x86_64 --outdir=output -c --debug
```

Платформы будут помещены в **~/Projects/hello-from-csharp/output/hello-from-csharp.framework**.

### <a name="use-the-generated-output-in-an-xcode-project"></a>Использовать созданные выходные данные в проекте Xcode

Откройте Xcode и создайте новое приложение Cocoa. Назовите его **hello из c#** и выберите **Objective-C** языка.

Откройте **~/Projects/hello-from-csharp/output** каталог в Finder, выберите **hello из csharp.framework**, перетащите его в проект Xcode и поместите его над **hello из c#**  в папку проекта.

![Перетаскивание framework](macos-images/hello-from-csharp-mac-drag-drop-framework.png)

Убедитесь, что **копировать элементы при необходимости** проверяется в диалоговом окне, появившемся и нажмите кнопку **Готово**.

![Копировать элементы при необходимости](macos-images/hello-from-csharp-mac-copy-items-if-needed.png)

Выберите **hello из c#** проекта и перейдите к **hello из c#** целевого объекта **Общие** вкладки. В **Embedded двоичный** добавьте **hello из csharp.framework**.

![Внедренные двоичных файлов](macos-images/hello-from-csharp-mac-embedded-binaries.png)

Откройте **ViewController.m**и замените его содержимое с помощью:

```objc
#import "ViewController.h"

#include "hello-from-csharp/hello-from-csharp.h"

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    MyNSView *view = [[MyNSView alloc] init];
    view.frame = CGRectMake(0, 200, 200, 200);
    [self.view addSubview: view];
}

@end
```

Наконец, запустите проект Xcode, и будет отображаться примерно следующее:

![Привет от пример на C# запуск в симуляторе](macos-images/hello-from-csharp-mac.png)

Пример более полным и изучив [доступен здесь](https://github.com/mono/Embeddinator-4000/tree/objc/samples/mac/weather).
