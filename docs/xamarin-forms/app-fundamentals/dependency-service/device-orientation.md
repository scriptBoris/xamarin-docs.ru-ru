---
title: Проверка ориентации устройства
description: В этой статье объясняется, как использовать класс Xamarin.Forms DependencyService для доступа к ориентации устройства из общего кода.
ms.prod: xamarin
ms.assetid: 5F60975F-47DB-4361-B97C-2290D6F77D2F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2016
ms.openlocfilehash: 52b82033cbd6fe0e1a44f5729c815074852230bf
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115423"
---
# <a name="checking-device-orientation"></a>Проверка ориентации устройства

В этой статье поможет вам использовать [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) для проверки ориентации устройства из общего кода, с помощью собственных API на каждой платформе. Это пошаговое руководство основано на существующий `DeviceOrientation` подключаемого модуля по Özgür али. См. в разделе [репозиторий GitHub](https://github.com/aliozgur/Xamarin.Plugins/tree/master/DeviceOrientation) Дополнительные сведения.

- **[Создание интерфейса](#Creating_the_Interface)**  &ndash; понять, как интерфейс создается в общем коде.
- **[iOS реализация](#iOS_Implementation)**  &ndash; Узнайте, как реализовать интерфейс в машинном коде для iOS.
- **[Android реализации](#Android_Implementation)**  &ndash; Узнайте, как реализовать интерфейс в машинном коде для Android.
- **[Реализация UWP](#WindowsImplementation)**  &ndash; Узнайте, как реализовать интерфейс в машинном коде для универсальной платформы Windows (UWP).
- **[Реализация в общем коде](#Implementing_in_Shared_Code)**  &ndash; сведения об использовании `DependencyService` выполнить вызов собственной реализации из общего кода.

Приложение с помощью `DependencyService` будет иметь следующую структуру:

![](device-orientation-images/orientation-diagram.png "Структура приложений DependencyService")

> [!NOTE]
> Можно определить, является ли устройство в книжной и альбомной ориентации в общем коде, как показано в [ориентации устройства](~/xamarin-forms/user-interface/layouts/device-orientation.md#Reacting_to_Changes_in_Orientation). Метод, описанный в этой статье использует собственные функции для получения дополнительных сведений об ориентации, включая является ли устройство сверху вниз.

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Создание интерфейса

Во-первых необходимо создаете интерфейс в общем коде, который выражает функцию, которую планируется реализовать. В этом примере интерфейс содержит один метод:

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

Создания кода для этого интерфейса в общем коде позволит приложению доступ к ориентации устройства API-интерфейсы на каждой платформе Xamarin.Forms.

> [!NOTE]
> Классы, реализующие интерфейс должен иметь конструктор без параметров для работы с `DependencyService`.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Реализация iOS

Интерфейс должен быть реализован в каждом проекте приложения для конкретной платформы. Обратите внимание, что класс имеет конструктор без параметров, чтобы `DependencyService` можно создавать новые экземпляры:

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

Наконец, добавьте это `[assembly]` атрибут над классом (и, вне любого пространства имен, определено), включая все необходимые `using` инструкции:

```csharp
using UIKit;
using Foundation;
using DependencyServiceSample.iOS; //enables registration outside of namespace

[assembly: Xamarin.Forms.Dependency (typeof (DeviceOrientationImplementation))]
namespace DependencyServiceSample.iOS {
    ...
```

Этот атрибут регистрирует класс как реализация `IDeviceOrientation` интерфейс, который означает, что `DependencyService.Get<IDeviceOrientation>` может использоваться в общем коде, чтобы создать его экземпляр.

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Реализация Android

В следующем коде реализуется `IDeviceOrientation` в Android:

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

Добавьте этот `[assembly]` атрибут над классом (и, вне любого пространства имен, определено), включая все необходимые `using` инструкции:

```csharp
using DependencyServiceSample.Droid; //enables registration outside of namespace
using Android.Hardware;

[assembly: Xamarin.Forms.Dependency (typeof (DeviceOrientationImplementation))]
namespace DependencyServiceSample.Droid {
    ...
```

Этот атрибут регистрирует класс как реализация `IDeviceOrientaiton` интерфейс, который означает, что `DependencyService.Get<IDeviceOrientation>` может использоваться в общий код можно создать его экземпляр.

<a name="WindowsImplementation" />

## <a name="universal-windows-platform-implementation"></a>Реализация платформы универсальной Windows

В следующем коде реализуется `IDeviceOrientation` интерфейс на универсальной платформе Windows:

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

Добавить `[assembly]` атрибут над классом (и, вне любого пространства имен, определено), включая все необходимые `using` инструкции:

```csharp
using DependencyServiceSample.WindowsPhone; //enables registration outside of namespace

[assembly: Dependency(typeof(DeviceOrientationImplementation))]
namespace DependencyServiceSample.WindowsPhone {
    ...
```

Этот атрибут регистрирует класс как реализация `DeviceOrientationImplementation` интерфейс, который означает, что `DependencyService.Get<IDeviceOrientation>` может использоваться в общий код можно создать его экземпляр.

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Реализация в общем коде

Теперь мы можно писать и тестировать общий код, который обращается к `IDeviceOrientation` интерфейс. Это простая страница имеется кнопка, которая обновляет свой собственный текст, в зависимости от ориентации устройства. Она использует `DependencyService` для получения экземпляра `IDeviceOrientation` интерфейс &ndash; во время выполнения этот экземпляр будет реализацию платформы, которая имеет полный доступ к собственным пакетом SDK для:

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

Текст кнопки, обновление с помощью ориентации устройства приведет к запуске этого приложения на iOS, Android или на платформах Windows, а затем нажмите кнопку.

![](device-orientation-images/orientation.png "Пример ориентации устройства")


## <a name="related-links"></a>Связанные ссылки

- [С помощью DependencyService (пример)](https://developer.xamarin.com/samples/UsingDependencyService)
- [DependencyService (пример)](https://developer.xamarin.com/samples/DependencyService/DependencyServiceSample/)
- [Примеры Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
