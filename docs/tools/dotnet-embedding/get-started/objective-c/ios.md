---
title: Начало работы с iOS
description: В этом документе описывается начало работы с iOS с помощью внедрения .NET. Обсуждаются требования она представляет пример приложения, чтобы продемонстрировать, как привязать управляемой сборки и использовать выходные данные в проекте Xcode.
ms.prod: xamarin
ms.assetid: D5453695-69C9-44BC-B226-5B86950956E2
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 683f448c3e0cdba1bbe3ba68f0db3225675f5830
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37066901"
---
# <a name="getting-started-with-ios"></a>Начало работы с iOS

## <a name="requirements"></a>Требования

Помимо требований к из наших [Приступая к работе с Objective-C](~/tools/dotnet-embedding/get-started/objective-c/index.md) руководства, необходимо:

* [Xamarin.iOS 10.11](https://visualstudio.microsoft.com/xamarin/) или более поздней версии

## <a name="hello-world"></a>Здравствуй, мир

Сначала нужно создать простой hello world-пример на языке C#.

### <a name="create-c-sample"></a>Создайте пример на C#

Откройте Visual Studio для Mac, создайте новый проект библиотеки классов iOS, назовите его **hello из c#** и сохраните файл в **~/Projects/hello-from-csharp**.

Замените код в **MyClass.cs** файла с помощью следующего фрагмента:

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

Выполните построение проекта и получившуюся сборку будет сохранена как **~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll**.

### <a name="bind-the-managed-assembly"></a>Привязать управляемой сборки

Когда управляемая сборка, привяжите его путем вызова внедрения .NET.

Как описано в [установки](~/tools/dotnet-embedding/get-started/install/install.md) руководстве, это можно сделать в качестве шага после сборки в проекте с пользовательской целевой объект MSBuild, или вручную:

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=iOS --outdir=output -c --debug
```

Платформа будет помещен в **~/Projects/hello-from-csharp/output/hello-from-csharp.framework**.

### <a name="use-the-generated-output-in-an-xcode-project"></a>Использовать созданные выходные данные в проекте Xcode

Откройте в Xcode, создайте новый iOS одним приложением представление, назовите его **hello из c#** и выберите **Objective-C** языка.

Откройте **~/Projects/hello-from-csharp/output** каталог в системе поиска выберите **hello из csharp.framework**, перетащите его к проекту Xcode и поместите его непосредственно над **hello из c#**  папки в проекте.

! [Перетаскивание framework] Images/Hello-from-CSharp-IOS-Drag-DROP-Framework.PNG)

Убедитесь, что **копирование элементов при необходимости** проверяется в появившемся окне и нажмите кнопку **Готово**.

![Копирование элементов при необходимости](ios-images/hello-from-csharp-ios-copy-items-if-needed.png)

Выберите **hello из c#** проекта и перейдите к **hello из c#** целевого объекта **вкладка "Общие"**. В **внедренные двоичный** добавьте **hello из csharp.framework**.

![Внедренные двоичных файлов](ios-images/hello-from-csharp-ios-embedded-binaries.png)

Откройте **ViewController.m**и замените содержимое с:

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

Внедрение .NET не поддерживает bitcode на iOS, которая включена для некоторые шаблоны проекта Xcode. 

Отключите ее в параметрах проекта.

![Параметр Bitcode](../../images/ios-bitcode-option.png)

Наконец, запустите проект Xcode и будут отображаться примерно следующим образом:

![Hello из примера для C# в симуляторе](ios-images/hello-from-csharp-ios.png)
