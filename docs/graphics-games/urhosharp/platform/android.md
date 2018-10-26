---
title: Поддержка UrhoSharp Android
description: В этом документе описываются настройки конкретного Android и сведения, связанные с функция UrhoSharp. В частности, здесь рассматриваются Поддерживаемые архитектуры, как создать проект, настраивая и запуская Urho, а также их пользовательских внедрение Urho.
ms.prod: xamarin
ms.assetid: 8409BD81-B1A6-4F5D-AE11-6BBD3F7C6327
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: e7371fa85fd5955e9a0fd285adb32844001821b3
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105442"
---
# <a name="urhosharp-android-support"></a>Поддержка UrhoSharp Android

_Android определенные настройки и компоненты_

Хотя Urho является переносимой библиотеке классов и разрешает один и тот же API для использования на различных платформах для логики игр по-прежнему необходимо инициализировать Urho в конкретного драйвера, платформы, а в некоторых случаях, вам потребуется воспользоваться преимуществами определенных функций платформы .

В указанные ниже страницы предполагается, что `MyGame` является подклассом `Application` класса.

## <a name="architectures"></a>Архитектуры

**Поддерживаемые архитектуры**: x86, armeabi, armeabi-v7a

## <a name="create-a-project"></a>Создание проекта

Создание проекта Android и добавьте пакет UrhoSharp NuGet.

Добавление данных, содержащих ресурсов на **активы** каталог и убедитесь, что все файлы имеют **AndroidAsset** как **действие при построении**.

![Установка проектов](android-images/image-3.png "Добавление данных с ресурсами в каталог активов")

## <a name="configure-and-launching-urho"></a>Настройка и запуск Urho

Добавьте выражения using для `Urho` и `Urho.Android` пространства имен, а затем добавьте следующий код для инициализации Urho, а также для запуска приложения.

Самый простой способ запуска игры, согласно реализации в классе MyGame является вызов

```csharp
UrhoSurface.RunInActivity<MyGame>();
```

Это будет открыт в полноэкранном режиме действие с игрой содержимого.

## <a name="custom-embedding-of-urho"></a>Внедрение пользовательских Urho

Вы также к двум фактам: Urho возьмет на экране всего приложения, и для использования в качестве компонента приложения, можно создать `SurfaceView` через:

```csharp
var surface = UrhoSurface.CreateSurface<MyGame>(activity)
```

Вам также необходимо отправить несколько событий от вас действий для UrhoSharp, например:

```csharp
protected override void OnPause()
{
    UrhoSurface.OnPause();
    base.OnPause();
}
```

То же необходимо сделать: `OnResume`, `OnPause`, `OnLowMemory`, `OnDestroy`, `DispatchKeyEvent` и `OnWindowFocusChanged`.

Это показывает типичные действия, которое запускает игры:

```csharp
[Activity(Label = "MyUrhoApp", MainLauncher = true,
    Icon = "@drawable/icon", Theme = "@android:style/Theme.NoTitleBar.Fullscreen",
    ConfigurationChanges = ConfigChanges.KeyboardHidden | ConfigChanges.Orientation,
    ScreenOrientation = ScreenOrientation.Portrait)]
public class MainActivity : Activity
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        var mLayout = new AbsoluteLayout(this);
        var surface = UrhoSurface.CreateSurface<MyUrhoApp>(this);
        mLayout.AddView(surface);
        SetContentView(mLayout);
    }

    protected override void OnResume()
    {
        UrhoSurface.OnResume();
        base.OnResume();
    }

    protected override void OnPause()
    {
        UrhoSurface.OnPause();
        base.OnPause();
    }

    public override void OnLowMemory()
    {
        UrhoSurface.OnLowMemory();
        base.OnLowMemory();
    }

    protected override void OnDestroy()
    {
        UrhoSurface.OnDestroy();
        base.OnDestroy();
    }

    public override bool DispatchKeyEvent(KeyEvent e)
    {
        if (!UrhoSurface.DispatchKeyEvent(e))
            return false;
        return base.DispatchKeyEvent(e);
    }

    public override void OnWindowFocusChanged(bool hasFocus)
    {
        UrhoSurface.OnWindowFocusChanged(hasFocus);
        base.OnWindowFocusChanged(hasFocus);
    }
}
```

