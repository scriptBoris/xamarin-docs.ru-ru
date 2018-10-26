---
title: Поддержка Windows UrhoSharp
description: В этом документе рассматривается поддержка UrhoSharp в Windows. Он описывает, как создать проект, настроить и запустить Urho, интегрировать с WPF и интеграции с универсальной платформы Windows.
ms.prod: xamarin
ms.assetid: A4F36014-AE4E-4F07-A1AC-F264AAA68ACF
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 8aca028ec1015616a9884cd09b7ffa5e04f2e43d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119609"
---
# <a name="urhosharp-windows-support"></a>Поддержка Windows UrhoSharp

Хотя Urho является переносимой библиотеке классов и разрешает один и тот же API для использования на различных платформах для логики игр по-прежнему необходимо инициализировать Urho в конкретного драйвера, платформы, а в некоторых случаях, вам потребуется воспользоваться преимуществами определенных функций платформы .

В указанные ниже страницы предполагается, что `MyGame` является подклассом `Application` класса.

**Поддерживаемые архитектуры:** только 64-разрядная версия Windows.

Вы увидите полный пример, показывающий, как использовать это в нашем [примеры](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples)

## <a name="standalone-project"></a>Отдельный проект

### <a name="creating-a-project"></a>Создание проекта

Создайте проект консольного, ссылаются на Urho NuGet и убедитесь, что можно найти ресурсы (каталоги, содержащие каталог данных).

### <a name="configuring-and-launching-urho"></a>Настраивая и запуская Urho

Чтобы запустить приложение, для этого:

```csharp
DesktopUrhoInitializer.AssetsDirectory = "../Assets";
new MyGame().Run();
```

### <a name="example"></a>Пример

[Полный пример](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/Desktop)

## <a name="integrated-with-wpf"></a>Интегрируется с WPF

### <a name="creating-a-project"></a>Создание проекта

Создание проекта WPF, ссылаются на Urho NuGet и убедитесь, что можно найти ресурсы (каталоги, содержащие каталог данных).

### <a name="configuring-and-launching-urho-from-wpf"></a>Настраивая и запуская Urho из WPF

Создать подкласс `Window` и настройки ресурсов следующим образом:

```csharp
    public partial class MainWindow : Window
    {
        Application currentApplication;

        public MainWindow()
        {
            InitializeComponent();
            DesktopUrhoInitializer.AssetsDirectory = @"../../Assets";
            Loaded += (s,e) => RunGame (new MyGame ());
        }

        async void RunGame(MyGame game)
        {
            var urhoSurface = new Panel { Dock = DockStyle.Fill };
            WindowsFormsHost.Child = urhoSurface;
            WindowsFormsHost.Focus();
            urhoSurface.Focus();
            await Task.Yield();
            var appOptions = new ApplicationOptions(assetsFolder: "Data")
                {
                    ExternalWindow = RunInSdlWindow.IsChecked.Value ? IntPtr.Zero : urhoSurface.Handle,
                    LimitFps = false, //true means "limit to 200fps"
                };
            currentApplication = Urho.Application.CreateInstance(value.Type, appOptions);
            currentApplication.Run();
        }
    }
```

### <a name="example"></a>Пример

[Полный пример](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/WPF)

## <a name="integrated-with-uwp"></a>Интегрируется с универсальной платформы Windows

### <a name="creating-a-project"></a>Создание проекта

Создайте проект UWP, ссылаются на Urho NuGet и убедитесь, что можно найти ресурсы (каталоги, содержащие каталог данных).

### <a name="configuring-and-launching-urho-from-uwp"></a>Настраивая и запуская Urho из универсальной платформы Windows

Создать подкласс `Window` и настройки ресурсов следующим образом:

```csharp
{
            InitializeComponent();
            GameTypes = typeof(Sample).GetTypeInfo().Assembly.GetTypes()
                .Where(t => t.GetTypeInfo().IsSubclassOf(typeof(Application)) && t != typeof(Sample))
                .Select((t, i) => new TypeInfo(t, $"{i + 1}. {t.Name}", ""))
                .ToArray();
            DataContext = this;
            Loaded += (s, e) => RunGame (new MyGame ());
        }

        public void RunGame(TypeInfo value)
        {
            //at this moment, UWP supports assets only in pak files (see PackageTool)
            currentApplication = UrhoSurface.Run(value.Type, "Data.pak");
        }
    }
```

### <a name="example"></a>Пример

[Полный пример](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/UWP)

## <a name="integrated-with-windowsforms"></a>Интегрируется с Windows.Forms

### <a name="creating-a-project"></a>Создание проекта

Создание проекта Windows.Forms, ссылаются на Urho NuGet и убедитесь, что можно найти ресурсы (каталоги, содержащие каталог данных).

### <a name="configuring-and-launching-urho-from-windowsforms"></a>Настраивая и запуская Urho из Windows.Forms

Запустите Urho из формы, см. в разделе [полный пример](https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/WinForms/SamplesForm.cs)
