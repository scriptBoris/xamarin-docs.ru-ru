---
title: Проверка состояния батареи
description: В этой статье объясняется, как использовать класс Xamarin.Forms DependencyService для получения состояния батареи с помощью собственного интерфейса API для каждой платформы.
ms.prod: xamarin
ms.assetid: CF1C5A73-84ED-407D-BDC5-EB1D83D2D3DB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2016
ms.openlocfilehash: bdfef88e0cf5916b1eec08ddfaa09165a057765a
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53058588"
---
# <a name="checking-battery-status"></a>Проверка состояния батареи

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/DependencyService)

В этой статье приводятся пошаговые инструкции по созданию приложения, проверяющего состояние батареи. В статье используется подключаемый модуль Battery, разработанный Джеймсом Монтеманьо (James Montemagno). Дополнительные сведения см. в [репозитории GitHub](https://github.com/jamesmontemagno/Xamarin.Plugins/tree/master/Battery).

Так как в Xamarin.Forms нет возможностей для проверки текущего состояния батареи, приложению потребуется использовать [`DependencyService`](xref:Xamarin.Forms.DependencyService) для доступа к собственным интерфейсам API.  В этой статье рассматриваются следующие этапы использования `DependencyService`:

- **[Создание интерфейса](#Creating_the_Interface)** &ndash; сведения о создании интерфейса в общем коде.
- **[Реализация в iOS](#iOS_Implementation)** &ndash; сведения о реализации интерфейса в машинном коде для iOS.
- **[Реализация в Android](#Android_Implementation)** &ndash; сведения о реализации интерфейса в машинном коде для Android.
- **[Реализация на универсальной платформе Windows](#UWPImplementation)**  — сведения о реализации интерфейса в машинном коде для универсальной платформы Windows (UWP).
- **[Реализация в общем коде](#Implementing_in_Shared_Code)**  — сведения об использовании `DependencyService` для вызова собственной реализации из общего кода.

У готового приложения, использующего `DependencyService`, будет следующая структура:

![](battery-info-images/battery-diagram.png "Структура приложения DependencyService")

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Создание интерфейса

Сначала создайте в общем коде интерфейс для реализации нужной функциональности. В случае с приложением, проверяющим состояние батареи, требуются следующие сведения: оставшийся процент заряда, заряжается ли устройство в настоящее время и как подается питание на устройство:

```csharp
namespace DependencyServiceSample
{
  public enum BatteryStatus
  {
    Charging,
    Discharging,
    Full,
    NotCharging,
    Unknown
  }

  public enum PowerSource
  {
    Battery,
    Ac,
    Usb,
    Wireless,
    Other
  }

  public interface IBattery
  {
    int RemainingChargePercent { get; }
    BatteryStatus Status { get; }
    PowerSource PowerSource { get; }
  }
}
```

Реализация этого интерфейса в общем коде позволит приложению Xamarin.Forms обращаться к интерфейсам API управления питанием на каждой платформе.

> [!NOTE]
> Для работы с `DependencyService` классы, реализующие интерфейс, должны иметь конструктор без параметров. Конструкторы не могут определяться интерфейсами.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Реализация в iOS

Интерфейс `IBattery` необходимо реализовать в проекте приложения для каждой платформы. Реализация для iOS будет использовать собственные интерфейсы API [`UIDevice`](https://developer.xamarin.com/api/type/UIKit.UIDevice/) для доступа к сведениям о батарее. Обратите внимание на то, что следующий класс имеет конструктор без параметров, что позволяет `DependencyService` создавать экземпляры:

```csharp
using UIKit;
using Foundation;
using DependencyServiceSample.iOS;

namespace DependencyServiceSample.iOS
{
  public class BatteryImplementation : IBattery
  {
    public BatteryImplementation()
    {
      UIDevice.CurrentDevice.BatteryMonitoringEnabled = true;
    }

    public int RemainingChargePercent
    {
      get
      {
        return (int)(UIDevice.CurrentDevice.BatteryLevel * 100F);
      }
    }

    public BatteryStatus Status
    {
      get
      {
        switch (UIDevice.CurrentDevice.BatteryState)
        {
          case UIDeviceBatteryState.Charging:
            return BatteryStatus.Charging;
          case UIDeviceBatteryState.Full:
            return BatteryStatus.Full;
          case UIDeviceBatteryState.Unplugged:
            return BatteryStatus.Discharging;
          default:
            return BatteryStatus.Unknown;
        }
      }
    }

    public PowerSource PowerSource
    {
      get
      {
        switch (UIDevice.CurrentDevice.BatteryState)
        {
          case UIDeviceBatteryState.Charging:
            return PowerSource.Ac;
          case UIDeviceBatteryState.Full:
            return PowerSource.Ac;
          case UIDeviceBatteryState.Unplugged:
            return PowerSource.Battery;
          default:
            return PowerSource.Other;
        }
      }
    }
  }
}
```

Наконец, добавьте следующий атрибут `[assembly]` над классом (и вне всех определенных пространств имен), включая все обязательные операторы `using`:

```csharp
using UIKit;
using Foundation;
using DependencyServiceSample.iOS;//necessary for registration outside of namespace

[assembly: Xamarin.Forms.Dependency (typeof (BatteryImplementation))]
namespace DependencyServiceSample.iOS
{
  public class BatteryImplementation : IBattery {
    ...
```

Этот атрибут регистрирует класс как реализацию интерфейса `IBattery`. Это означает, что для создания его экземпляра можно использовать метод `DependencyService.Get<IBattery>` в общем коде.

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Реализация в Android

Реализация для Android использует интерфейс API [`Android.OS.BatteryManager`](https://developer.xamarin.com/api/type/Android.OS.BatteryManager/). Она сложнее, чем реализация для iOS, и требует проверки наличия разрешений на доступ к сведениям о батарее:

```csharp
using System;
using Android;
using Android.Content;
using Android.App;
using Android.OS;
using BatteryStatus = Android.OS.BatteryStatus;
using DependencyServiceSample.Droid;

namespace DependencyServiceSample.Droid
{
  public class BatteryImplementation : IBattery
  {
    private BatteryBroadcastReceiver batteryReceiver;
    public BatteryImplementation() { }

    public int RemainingChargePercent
    {
      get
      {
        try
        {
          using (var filter = new IntentFilter(Intent.ActionBatteryChanged))
          {
            using (var battery = Application.Context.RegisterReceiver(null, filter))
            {
              var level = battery.GetIntExtra(BatteryManager.ExtraLevel, -1);
              var scale = battery.GetIntExtra(BatteryManager.ExtraScale, -1);

              return (int)Math.Floor(level * 100D / scale);
            }
          }
        }
        catch
        {
          System.Diagnostics.Debug.WriteLine ("Ensure you have android.permission.BATTERY_STATS");
          throw;
        }

      }
    }

    public DependencyServiceSample.BatteryStatus Status
    {
      get
      {
        try
        {
          using (var filter = new IntentFilter(Intent.ActionBatteryChanged))
          {
            using (var battery = Application.Context.RegisterReceiver(null, filter))
            {
              int status = battery.GetIntExtra(BatteryManager.ExtraStatus, -1);
              var isCharging = status == (int)BatteryStatus.Charging || status == (int)BatteryStatus.Full;

              var chargePlug = battery.GetIntExtra(BatteryManager.ExtraPlugged, -1);
              var usbCharge = chargePlug == (int)BatteryPlugged.Usb;
              var acCharge = chargePlug == (int)BatteryPlugged.Ac;
              bool wirelessCharge = false;
              wirelessCharge = chargePlug == (int)BatteryPlugged.Wireless;

              isCharging = (usbCharge || acCharge || wirelessCharge);
              if (isCharging)
                return DependencyServiceSample.BatteryStatus.Charging;

              switch(status)
              {
                case (int)BatteryStatus.Charging:
                  return DependencyServiceSample.BatteryStatus.Charging;
                case (int)BatteryStatus.Discharging:
                  return DependencyServiceSample.BatteryStatus.Discharging;
                case (int)BatteryStatus.Full:
                  return DependencyServiceSample.BatteryStatus.Full;
                case (int)BatteryStatus.NotCharging:
                  return DependencyServiceSample.BatteryStatus.NotCharging;
                default:
                  return DependencyServiceSample.BatteryStatus.Unknown;
              }
            }
          }
        }
        catch
        {
          System.Diagnostics.Debug.WriteLine ("Ensure you have android.permission.BATTERY_STATS");
          throw;
        }
      }
    }

    public PowerSource PowerSource
    {
      get
      {
        try
        {
          using (var filter = new IntentFilter(Intent.ActionBatteryChanged))
          {
            using (var battery = Application.Context.RegisterReceiver(null, filter))
            {
              int status = battery.GetIntExtra(BatteryManager.ExtraStatus, -1);
              var isCharging = status == (int)BatteryStatus.Charging || status == (int)BatteryStatus.Full;

              var chargePlug = battery.GetIntExtra(BatteryManager.ExtraPlugged, -1);
              var usbCharge = chargePlug == (int)BatteryPlugged.Usb;
              var acCharge = chargePlug == (int)BatteryPlugged.Ac;

              bool wirelessCharge = false;
              wirelessCharge = chargePlug == (int)BatteryPlugged.Wireless;

              isCharging = (usbCharge || acCharge || wirelessCharge);

              if (!isCharging)
                return DependencyServiceSample.PowerSource.Battery;
              else if (usbCharge)
                return DependencyServiceSample.PowerSource.Usb;
              else if (acCharge)
                return DependencyServiceSample.PowerSource.Ac;
              else if (wirelessCharge)
                return DependencyServiceSample.PowerSource.Wireless;
              else
                return DependencyServiceSample.PowerSource.Other;
            }
          }
        }
        catch
        {
          System.Diagnostics.Debug.WriteLine ("Ensure you have android.permission.BATTERY_STATS");
          throw;
        }
      }
    }
  }
}
```

Добавьте следующий атрибут `[assembly]` над классом (и вне всех определенных пространств имен), включая все обязательные операторы `using`:

```csharp
...
using BatteryStatus = Android.OS.BatteryStatus;
using DependencyServiceSample.Droid; //enables registration outside of namespace

[assembly: Xamarin.Forms.Dependency (typeof (BatteryImplementation))]
namespace DependencyServiceSample.Droid
{
  public class BatteryImplementation : IBattery {
    ...
```

Этот атрибут регистрирует класс как реализацию интерфейса `IBattery`. Это означает, что для создания его экземпляра можно использовать метод `DependencyService.Get<IBattery>` в общем коде.

<a name="UWPImplementation" />

## <a name="universal-windows-platform-implementation"></a>Реализация на универсальной платформе Windows

Реализация для UWP использует интерфейсы API `Windows.Devices.Power` для получения сведений о состоянии батареи.

```csharp
using DependencyServiceSample.UWP;
using Xamarin.Forms;

[assembly: Dependency(typeof(BatteryImplementation))]
namespace DependencyServiceSample.UWP
{
    public class BatteryImplementation : IBattery
    {
        private BatteryStatus status = BatteryStatus.Unknown;
        Windows.Devices.Power.Battery battery;

        public BatteryImplementation()
        {
        }

        private Windows.Devices.Power.Battery DefaultBattery
        {
            get
            {
                return battery ?? (battery = Windows.Devices.Power.Battery.AggregateBattery);
            }
        }

        public int RemainingChargePercent
        {
            get
            {
                var finalReport = DefaultBattery.GetReport();
                var finalPercent = -1;

                if (finalReport.RemainingCapacityInMilliwattHours.HasValue && finalReport.FullChargeCapacityInMilliwattHours.HasValue)
                {
                    finalPercent = (int)((finalReport.RemainingCapacityInMilliwattHours.Value /
                        (double)finalReport.FullChargeCapacityInMilliwattHours.Value) * 100);
                }
                return finalPercent;
            }
        }

        public BatteryStatus Status
        {
            get
            {
                var report = DefaultBattery.GetReport();
                var percentage = RemainingChargePercent;

                if (percentage >= 1.0)
                {
                    status = BatteryStatus.Full;
                }
                else if (percentage < 0)
                {
                    status = BatteryStatus.Unknown;
                }
                else
                {
                    switch (report.Status)
                    {
                        case Windows.System.Power.BatteryStatus.Charging:
                            status = BatteryStatus.Charging;
                            break;
                        case Windows.System.Power.BatteryStatus.Discharging:
                            status = BatteryStatus.Discharging;
                            break;
                        case Windows.System.Power.BatteryStatus.Idle:
                            status = BatteryStatus.NotCharging;
                            break;
                        case Windows.System.Power.BatteryStatus.NotPresent:
                            status = BatteryStatus.Unknown;
                            break;
                    }
                }
                return status;
            }
        }

        public PowerSource PowerSource
        {
            get
            {
                if (status == BatteryStatus.Full || status == BatteryStatus.Charging)
                {
                    return PowerSource.Ac;
                }
                return PowerSource.Battery;
            }
        }
    }
}
```

Атрибут `[assembly]` над объявлением пространства имен регистрирует класс как реализацию интерфейса `IBattery`. Это означает, что для создания его экземпляра можно использовать метод `DependencyService.Get<IBattery>` в общем коде.

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Реализация в общем коде

Теперь, когда интерфейс реализован для каждой платформы, можно написать общее приложение, использующее его. Оно будет состоять из страницы с кнопкой, при нажатии которой текст изменяется на текущее состояние батареи. В приложении используется `DependencyService` для получения экземпляра интерфейса `IBattery`. Во время выполнения этот экземпляр будет представлять собой зависящую от платформы реализацию с полным доступом к собственному пакету SDK.

```csharp
public MainPage ()
{
    var button = new Button {
        Text = "Click for battery info",
        VerticalOptions = LayoutOptions.CenterAndExpand,
        HorizontalOptions = LayoutOptions.CenterAndExpand,
    };
    button.Clicked += (sender, e) => {
        var bat = DependencyService.Get<IBattery>();

        switch (bat.PowerSource){
          case PowerSource.Battery:
            button.Text = "Battery - ";
            break;
          case PowerSource.Ac:
            button.Text = "AC - ";
            break;
          case PowerSource.Usb:
            button.Text = "USB - ";
            break;
          case PowerSource.Wireless:
            button.Text = "Wireless - ";
            break;
          case PowerSource.Other:
          default:
            button.Text = "Other - ";
            break;
        }
        switch (bat.Status){
          case BatteryStatus.Charging:
            button.Text += "Charging";
            break;
          case BatteryStatus.Discharging:
            button.Text += "Discharging";
            break;
          case BatteryStatus.NotCharging:
            button.Text += "Not Charging";
            break;
          case BatteryStatus.Full:
            button.Text += "Full";
            break;
          case BatteryStatus.Unknown:
          default:
            button.Text += "Unknown";
            break;
        }
    };
    Content = button;
}
```

Если запустить это приложение в iOS, Android или UWP и нажать кнопку, ее текст обновится в соответствии с текущим состоянием питания устройства.

![](battery-info-images/battery.png "Пример состояния батареи")


## <a name="related-links"></a>Связанные ссылки

- [DependencyService (пример)](https://developer.xamarin.com/samples/DependencyService)
- [Использование DependencyService (пример)](https://developer.xamarin.com/samples/UsingDependencyService/)
- [Примеры Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
