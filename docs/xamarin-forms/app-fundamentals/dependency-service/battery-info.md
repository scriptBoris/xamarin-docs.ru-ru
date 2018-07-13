---
title: Проверка состояния батареи
description: В этой статье объясняется, как использовать класс Xamarin.Forms DependencyService для доступа к информации батареи, скомпилированных в собственном коде для каждой платформы.
ms.prod: xamarin
ms.assetid: CF1C5A73-84ED-407D-BDC5-EB1D83D2D3DB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2016
ms.openlocfilehash: cbb4a01ac2c6d933fe40a0b3c2571d1fe3ce75c0
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998405"
---
# <a name="checking-battery-status"></a>Проверка состояния батареи

В этой статье описывается создание приложения, которое проверяет состояние батареи. Эта статья основана на подключаемый модуль аккумулятора, Монтеманьо. Дополнительные сведения см. в разделе [репозиторий GitHub](https://github.com/jamesmontemagno/Xamarin.Plugins/tree/master/Battery).

Поскольку Xamarin.Forms не содержит функциональность для проверки текущего состояния батареи, это приложение будет нужно использовать [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) пользоваться преимуществами собственных API-интерфейсов.  В этой статье будут рассмотрены следующие действия по использованию `DependencyService`:

- **[Создание интерфейса](#Creating_the_Interface)**  &ndash; понять, как интерфейс создается в общем коде.
- **[iOS реализация](#iOS_Implementation)**  &ndash; Узнайте, как реализовать интерфейс в машинном коде для iOS.
- **[Android реализации](#Android_Implementation)**  &ndash; Узнайте, как реализовать интерфейс в машинном коде для Android.
- **[Универсальная реализация платформы Windows](#UWPImplementation)**  &ndash; Узнайте, как реализовать интерфейс в машинном коде для универсальной платформы Windows (UWP).
- **[Реализация в общем коде](#Implementing_in_Shared_Code)**  &ndash; сведения об использовании `DependencyService` выполнить вызов собственной реализации из общего кода.

После завершения приложения с помощью `DependencyService` будет иметь следующую структуру:

![](battery-info-images/battery-diagram.png "Структура приложений DependencyService")

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Создание интерфейса

Во-первых необходимо создаете интерфейс в общем коде, который выражает нужной функции. В случае батареи, проверка приложения соответствующие сведения выполняется процент аккумулятора, является ли устройство взимать плату или нет, и как устройство получает power:

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

Создания кода для этого интерфейса в общем коде позволит приложению доступ к API управления питанием на каждой платформе Xamarin.Forms.

> [!NOTE]
> Классы, реализующие интерфейс должен иметь конструктор без параметров для работы с `DependencyService`. Конструкторы не могут быть определены интерфейсы.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Реализация iOS

`IBattery` Интерфейс должен быть реализован в каждом проекте приложения для конкретной платформы. Реализация iOS будет применять собственную [ `UIDevice` ](https://developer.xamarin.com/api/type/UIKit.UIDevice/) API-интерфейсы для доступа к данным батареи. Обратите внимание, что класс имеет конструктор без параметров, чтобы `DependencyService` можно создавать новые экземпляры:

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

Наконец, добавьте это `[assembly]` атрибут над классом (и, вне любого пространства имен, определено), включая все необходимые `using` инструкции:

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

Этот атрибут регистрирует класс как реализация `IBattery` интерфейс, который означает, что `DependencyService.Get<IBattery>` может использоваться в общем коде, чтобы создать его экземпляр:

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Реализация Android

В Android реализации используется [ `Android.OS.BatteryManager` ](https://developer.xamarin.com/api/type/Android.OS.BatteryManager/) API. Эта реализация сложнее, чем версия iOS, требуя проверки для обработки нехватки разрешений аккумулятора:

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

Добавьте этот `[assembly]` атрибут над классом (и, вне любого пространства имен, определено), включая все необходимые `using` инструкции:

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

Этот атрибут регистрирует класс как реализация `IBattery` интерфейс, который означает, что `DependencyService.Get<IBattery>` может использоваться в общий код можно создать его экземпляр.

<a name="UWPImplementation" />

## <a name="universal-windows-platform-implementation"></a>Реализация платформы универсальной Windows

Реализация универсальной платформы Windows использует `Windows.Devices.Power` API-интерфейсы для получения информации о состоянии аккумулятора:

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

`[assembly]` Атрибут декларацией namespace регистрирует класс как реализация `IBattery` интерфейс, который означает, что `DependencyService.Get<IBattery>` может использоваться в общем коде, чтобы создать его экземпляр.

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Реализация в общем коде

Теперь, когда интерфейс реализован для каждой платформы, общий приложение может быть написано таким образом, чтобы воспользоваться его преимуществами. Приложение будет состоять страницы с кнопкой, что когда касание обновлений свой текст текущее состояние батареи. Она использует `DependencyService` для получения экземпляра `IBattery` интерфейс. Во время выполнения этот экземпляр будет реализацию платформы, которая имеет полный доступ к собственным пакетом SDK для.

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

Выполнение этого приложения в iOS, Android, или UWP и нажатие кнопки приведет к текст кнопки, обновление, чтобы отразить текущее состояние питания устройства.

![](battery-info-images/battery.png "Пример состояние батареи")


## <a name="related-links"></a>Связанные ссылки

- [DependencyService (пример)](https://developer.xamarin.com/samples/DependencyService)
- [С помощью DependencyService (пример)](https://developer.xamarin.com/samples/UsingDependencyService/)
- [Примеры Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
