---
title: Проверка ориентации устройства
description: В этой статье объясняется, как использовать класс Xamarin.Forms DependencyService для получения сведений об ориентации устройства из общего кода.
ms.prod: xamarin
ms.assetid: 5F60975F-47DB-4361-B97C-2290D6F77D2F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2016
ms.openlocfilehash: 52b82033cbd6fe0e1a44f5729c815074852230bf
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115423"
---
# <a name="checking-device-orientation"></a>Проверка ориентации устройства

В этой статье приводятся инструкции по использованию [`DependencyService`](xref:Xamarin.Forms.DependencyService) для проверки ориентации устройства из общего кода с помощью собственных интерфейсов API на каждой платформе. Это пошаговое руководство основано на подключаемом модуле `DeviceOrientation`, разработанном Али Озгюром (Ali Özgür). Дополнительные сведения см. в [репозитории GitHub](https://github.com/aliozgur/Xamarin.Plugins/tree/master/DeviceOrientation).

- **[Создание интерфейса](#Creating_the_Interface)** &ndash; сведения о создании интерфейса в общем коде.
- **[Реализация в iOS](#iOS_Implementation)** &ndash; сведения о реализации интерфейса в машинном коде для iOS.
- **[Реализация в Android](#Android_Implementation)** &ndash; сведения о реализации интерфейса в машинном коде для Android.
- **[Реализация в UWP](#WindowsImplementation)** &ndash; сведения о реализации интерфейса в машинном коде для универсальной платформы Windows (UWP).
- **[Реализация в общем коде](#Implementing_in_Shared_Code)** &ndash; сведения об использовании `DependencyService` для вызова собственной реализации из общего кода.

У приложения, использующего `DependencyService`, будет следующая структура:

![](device-orientation-images/orientation-diagram.png "Структура приложения DependencyService")

> [!NOTE]
> Определить, имеет ли устройство книжную или альбомную ориентацию, можно с помощью метода, описанного в статье [Ориентация устройства](~/xamarin-forms/user-interface/layouts/device-orientation.md#Reacting_to_Changes_in_Orientation). Метод, рассматриваемый в этой статье, предполагает использование собственных функций для получения дополнительных сведений об ориентации, в том числе о том, перевернуто ли устройство.

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Создание интерфейса

Сначала создайте в общем коде интерфейс для функциональности, которую вы планируете реализовать. В этом примере интерфейс содержит один метод:

```csharp
namespace DependencyServiceSample.Abstractions
{
    public enum DeviceOrientations
    {
        Undefined,
        Landscape,
        Portrait
    }

    public interface IDeviceOrientation
    {
        DeviceOrientations GetOrientation();
    }
}
```

Реализация этого интерфейса в общем коде позволит приложению Xamarin.Forms обращаться к интерфейсам API ориентации устройства на каждой платформе.

> [!NOTE]
> Для работы с `DependencyService` классы, реализующие интерфейс, должны иметь конструктор без параметров.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Реализация в iOS

Интерфейс необходимо реализовать в проекте приложения для каждой платформы. Обратите внимание на то, что класс имеет конструктор без параметров. Это позволяет `DependencyService` создавать экземпляры.

```csharp
using UIKit;
using Foundation;

namespace DependencyServiceSample.iOS
{
    public class DeviceOrientationImplementation : IDeviceOrientation
    {
        public DeviceOrientationImplementation(){ }

        public DeviceOrientations GetOrientation()
        {
            var currentOrientation = UIApplication.SharedApplication.StatusBarOrientation;
            bool isPortrait = currentOrientation == UIInterfaceOrientation.Portrait
                || currentOrientation == UIInterfaceOrientation.PortraitUpsideDown;

            return isPortrait ? DeviceOrientations.Portrait: DeviceOrientations.Landscape;
        }
    }
}
```

Наконец, добавьте следующий атрибут `[assembly]` над классом (и вне всех определенных пространств имен), включая все обязательные операторы `using`:

```csharp
using UIKit;
using Foundation;
using DependencyServiceSample.iOS; //enables registration outside of namespace

[assembly: Xamarin.Forms.Dependency (typeof (DeviceOrientationImplementation))]
namespace DependencyServiceSample.iOS {
    ...
```

Атрибут регистрирует класс как реализацию интерфейса `IDeviceOrientation`. Это означает, что для создания его экземпляра можно использовать метод `DependencyService.Get<IDeviceOrientation>` в общем коде.

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Реализация в Android

В следующем коде реализуется интерфейс `IDeviceOrientation` в Android.

```csharp
using DependencyServiceSample.Droid;
using Android.Hardware;

namespace DependencyServiceSample.Droid
{
    public class DeviceOrientationImplementation : IDeviceOrientation
    {
        public DeviceOrientationImplementation() { }

        public static void Init() { }

        public DeviceOrientations GetOrientation()
        {
            IWindowManager windowManager = Android.App.Application.Context.GetSystemService(Context.WindowService).JavaCast<IWindowManager>();

            var rotation = windowManager.DefaultDisplay.Rotation;
            bool isLandscape = rotation == SurfaceOrientation.Rotation90 || rotation == SurfaceOrientation.Rotation270;
            return isLandscape ? DeviceOrientations.Landscape : DeviceOrientations.Portrait;
        }
    }
}
```

Добавьте следующий атрибут `[assembly]` над классом (и вне всех определенных пространств имен), включая все обязательные операторы `using`.

```csharp
using DependencyServiceSample.Droid; //enables registration outside of namespace
using Android.Hardware;

[assembly: Xamarin.Forms.Dependency (typeof (DeviceOrientationImplementation))]
namespace DependencyServiceSample.Droid {
    ...
```

Этот атрибут регистрирует класс как реализацию интерфейса `IDeviceOrientaiton`. Это означает, что для создания его экземпляра можно использовать метод `DependencyService.Get<IDeviceOrientation>` в общем коде.

<a name="WindowsImplementation" />

## <a name="universal-windows-platform-implementation"></a>Реализация на универсальной платформе Windows

В следующем коде реализуется интерфейс `IDeviceOrientation` на универсальной платформе Windows.

```csharp
namespace DependencyServiceSample.WindowsPhone
{
    public class DeviceOrientationImplementation : IDeviceOrientation
    {
        public DeviceOrientationImplementation() { }

        public DeviceOrientations GetOrientation()
        {
            var orientation = Windows.UI.ViewManagement.ApplicationView.GetForCurrentView().Orientation;
            if (orientation == Windows.UI.ViewManagement.ApplicationViewOrientation.Landscape) {
                return DeviceOrientations.Landscape;
            }
            else {
                return DeviceOrientations.Portrait;
            }
        }
    }
}
```

Добавьте атрибут `[assembly]` над классом (и вне всех определенных пространств имен), включая все обязательные операторы `using`:

```csharp
using DependencyServiceSample.WindowsPhone; //enables registration outside of namespace

[assembly: Dependency(typeof(DeviceOrientationImplementation))]
namespace DependencyServiceSample.WindowsPhone {
    ...
```

Этот атрибут регистрирует класс как реализацию интерфейса `DeviceOrientationImplementation`. Это означает, что для создания его экземпляра можно использовать метод `DependencyService.Get<IDeviceOrientation>` в общем коде.

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Реализация в общем коде

Теперь можно написать и протестировать общий код, который обращается к интерфейсу `IDeviceOrientation`. На этой простой странице есть кнопка, текст которой изменяется в зависимости от ориентации устройства. Для получения экземпляра `IDeviceOrientation` интерфейса &ndash; используется `DependencyService`. Во время выполнения этот экземпляр будет представлять собой зависящую от платформы реализацию с полным доступом к собственному пакету SDK.

```csharp
public MainPage ()
{
    var orient = new Button {
        Text = "Get Orientation",
        VerticalOptions = LayoutOptions.CenterAndExpand,
        HorizontalOptions = LayoutOptions.CenterAndExpand,
    };
    orient.Clicked += (sender, e) => {
       var orientation = DependencyService.Get<IDeviceOrientation>().GetOrientation();
       switch(orientation){
           case DeviceOrientations.Undefined:
                orient.Text = "Undefined";
                break;
           case DeviceOrientations.Landscape:
                orient.Text = "Landscape";
                break;
           case DeviceOrientations.Portrait:
                orient.Text = "Portrait";
                break;
       }
    };
    Content = orient;
}
```

Если запустить это приложение в iOS, Android или Windows и нажать кнопку, ее текст обновится в соответствии с ориентацией устройства.

![](device-orientation-images/orientation.png "Пример ориентации устройства")


## <a name="related-links"></a>Связанные ссылки

- [Использование DependencyService (пример)](https://developer.xamarin.com/samples/UsingDependencyService)
- [DependencyService (пример)](https://developer.xamarin.com/samples/DependencyService/DependencyServiceSample/)
- [Примеры Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
