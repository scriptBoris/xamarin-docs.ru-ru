---
title: Xamarin.Forms TimePicker
description: TimPicker является представлением Xamarin.Forms, который позволяет пользователю для выбора времени. В этой статье объясняется, как использовать TimePicker в приложении Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 2E99FB23-B82D-4EB4-AFB3-5002E736E7B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/16/2018
ms.openlocfilehash: 9a534c39d514fec9a0de4bc810f33c972453baa2
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111658"
---
# <a name="xamarinforms-timepicker"></a>Xamarin.Forms TimePicker

_Представление Xamarin.Forms, которое позволяет пользователю для выбора времени._

Xamarin.Forms [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) вызывает средство выбора времени управления платформы и позволяет пользователю для выбора времени. `TimePicker` определяет следующие свойства:

- [`Time`](xref:Xamarin.Forms.TimePicker.Time) типа `TimeSpan`, установленный период времени, по умолчанию — `TimeSpan` 0. `TimeSpan` Тип указывает период времени с полуночи.
- [`Format`](xref:Xamarin.Forms.TimePicker.Format) типа `string`, [стандартный](/dotnet/standard/base-types/standard-date-and-time-format-strings/) или [пользовательских](/dotnet/standard/base-types/custom-date-and-time-format-strings/) .NET форматирования строки, значение по умолчанию — «t», короткий шаблон времени.
- [`TextColor`](xref:Xamarin.Forms.TimePicker.TextColor) типа [ `Color` ](xref:Xamarin.Forms.Color), цвет, используемый для отображения установленный период времени, по умолчанию — [ `Color.Default` ](xref:Xamarin.Forms.Color.Default).
- [`FontAttributes`](xref:Xamarin.Forms.TimePicker.FontAttributes) типа [ `FontAttributes` ](xref:Xamarin.Forms.FontAttributes), который по умолчанию [ `FontAtributes.None` ](xref:Xamarin.Forms.FontAttributes.None).
- [`FontFamily`](xref:Xamarin.Forms.TimePicker.FontFamily) типа `string`, который по умолчанию `null`.
- [`FontSize`](xref:Xamarin.Forms.TimePicker.FontSize) типа `double`, который по умолчанию -1,0.

Все эти свойства, обеспечиваются [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) объектов, что означает, что их можно применить различные стили, и свойства могут быть целями привязки данных. [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) Свойство имеет режим привязки по умолчанию [ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay), что означает, что он может быть целевой объект привязки данных в приложение, использующее [ Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) архитектуры.

[ `TimePicker` ](xref:Xamarin.Forms.TimePicker) Не включает событие, указывающее, выбран новый [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) значение. Если вам нужно быть уведомлен об этом, можно добавить обработчик для [ `PropertyChanged` ](xref:Xamarin.Forms.BindableObject.PropertyChanged) событий.

## <a name="initializing-the-time-property"></a>Инициализация свойства времени

В коде, можно инициализировать [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) свойства со значением типа `TimeSpan`:

```csharp
TimePicker timePicker = new TimePicker
{
  Time = new TimeSpan(4, 15, 26) // Time set to "04:15:26"
};
```

Когда [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) свойство, указанное в XAML, преобразуется в значение `TimeSpan` и проверенные, чтобы убедиться, что время в миллисекундах больше или равно 0, и что менее 24 часов. Компоненты времени должны разделяться точкой с запятой:

```xaml
<TimePicker Time="4:15:26" />
```

Если [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) свойство [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) присваивается экземпляр ViewModel, содержащий свойство типа `TimeSpan` с именем `SelectedTime` (например), можно создать экземпляр `TimePicker` следующим образом:

```xaml
<TimePicker Time="{Binding SelectedTime}" />
```

В этом примере [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) инициализируется свойство `SelectedTime` свойства в ViewModel. Так как `Time` свойство имеет режим привязки [ `TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay), в любое время новый, выбранной пользователем автоматически распространяется на ViewModel.

Если [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) не содержит привязки на его [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) свойство, приложения следует присоединить обработчик для [ `PropertyChanged` ](xref:Xamarin.Forms.BindableObject.PropertyChanged) события Будьте в курсе, когда пользователь выбирает новое значение времени.

Сведения о настройке свойств шрифта, см. в разделе [шрифты](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="timepicker-and-layout"></a>TimePicker и макет

Можно использовать параметр неограниченного горизонтальном расположении, например `Center`, `Start`, или `End` с [ `TimePicker` ](xref:Xamarin.Forms.TimePicker):

```xaml
<TimePicker ···
            HorizontalOptions="Center"
            ··· />
```

Тем не менее это не рекомендуется. Зависимости от настройки [ `Format` ](xref:Xamarin.Forms.TimePicker.Format) выбранным свойством раз может потребоваться другое отображаемое ширины. Например, строка формата «T» вызывает [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) просмотра для отображения времени в длинном формате, и «4:15:26 AM» требует больше ширины экрана чем краткий формат времени («t») из «4:15 AM». В зависимости от платформы, это различие может привести к `TimePicker` представление, чтобы изменить ширину в макете, или для отображения усечение.

> [!TIP]
> Лучше всего использовать значение по умолчанию `HorizontalOptions` параметр `Fill` с [ `TimePicker` ](xref:Xamarin.Forms.TimePicker)и не использовать ширину `Auto` при помещении `TimePicker` в `Grid` ячейки.

## <a name="timepicker-in-an-application"></a>TimePicker в приложении

[ **SetTimer** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TimePicker/) пример включает в себя [ `TimePicker` ](xref:Xamarin.Forms.TimePicker), [ `Entry` ](xref:Xamarin.Forms.Entry), и [ `Switch` ](xref:Xamarin.Forms.Switch) представлений на его странице. `TimePicker` Можно использовать для выбора времени, и время, происходит, отображается диалоговое окно предупреждения, напоминая пользователем текста в `Entry`, в который `Switch` изменяется на противоположное. Ниже приведен файл XAML.

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:SetTimer"
             x:Class="SetTimer.MainPage">
    <StackLayout>
        ...
        <Entry x:Name="_entry"
               Placeholder="Enter event to be reminded of" />
        <Label Text="Select the time below to be reminded at." />
        <TimePicker x:Name="_timePicker"
                    Time="11:00:00"
                    Format="T"
                    PropertyChanged="OnTimePickerPropertyChanged" />
        <StackLayout Orientation="Horizontal">
            <Label Text="Enable timer:" />
            <Switch x:Name="_switch"
                    HorizontalOptions="EndAndExpand"
                    Toggled="OnSwitchToggled" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

[ `Entry` ](xref:Xamarin.Forms.Entry) Позволяет ввести текст напоминание, которое будет отображаться при возникновении установленный период времени. [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) Назначается [ `Format` ](xref:Xamarin.Forms.TimePicker.Format) свойство «T» для длинный формат времени. Он имеет обработчик событий, подключенный к [ `PropertyChanged` ](xref:Xamarin.Forms.BindableObject.PropertyChanged) событий и [ `Switch` ](xref:Xamarin.Forms.Switch) присоединил обработчик к его [ `Toggled` ](xref:Xamarin.Forms.Switch.Toggled) событий. Эти обработчики событий, в файл с выделенным кодом и вызов `SetTriggerTime` метод:

```csharp
public partial class MainPage : ContentPage
{
    DateTime _triggerTime;

    public MainPage()
    {
        InitializeComponent();

        Device.StartTimer(TimeSpan.FromSeconds(1), OnTimerTick);
    }

    bool OnTimerTick()
    {
        if (_switch.IsToggled && DateTime.Now >= _triggerTime)
        {
            _switch.IsToggled = false;
            DisplayAlert("Timer Alert", "The '" + _entry.Text + "' timer has elapsed", "OK");
        }
        return true;
    }

    void OnTimePickerPropertyChanged(object sender, PropertyChangedEventArgs args)
    {
        if (args.PropertyName == "Time")
        {
            SetTriggerTime();
        }
    }

    void OnSwitchToggled(object sender, ToggledEventArgs args)
    {
        SetTriggerTime();
    }

    void SetTriggerTime()
    {
        if (_switch.IsToggled)
        {
            _triggerTime = DateTime.Today + _timePicker.Time;
            if (_triggerTime < DateTime.Now)
            {
                _triggerTime += TimeSpan.FromDays(1);
            }
        }
    }
}
```

`SetTriggerTime` Метод вычисляет время таймера с использованием `DateTime.Today` значение свойства и `TimeSpan` значение, возвращаемое из [ `TimePicker` ](xref:Xamarin.Forms.TimePicker). Это необходимо, так как `DateTime.Today` возвращает `DateTime` , указывающее текущую дату, но со временем полуночи. Если уже сегодня истекло время таймера, предполагается на завтра.

Тактов таймера каждую секунду, выполнение `OnTimerTick` метод, который проверяет ли [ `Switch` ](xref:Xamarin.Forms.Switch) на и ли текущее время — больше или равно времени таймера. При наступлении времени таймера [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert*) метод представляет диалоговое окно предупреждения пользователю для напоминания.

При первом запуске образца, [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) представление инициализируется до 11: 00. При касании `TimePicker` вызывает средство выбора времени платформы. Трех платформ реализовать средство выбора времени самыми разными способами, но каждый подход знакомы пользователям функции этой платформы:

[![Выберите время](timepicker-images/timepicker-open.png "выберите время")](timepicker-images/timepicker-open-large.png#lightbox "выбора времени")

> [!TIP]
> В Android [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) диалогового окна можно настроить путем переопределения `CreateTimePickerDialog` метод в пользовательское средство отрисовки. Это позволяет, например, дополнительные кнопки для добавления в диалоговое окно.

После выбора времени, установленный период времени отображается в [ `TimePicker` ](xref:Xamarin.Forms.TimePicker):

[![Выбранное время](timepicker-images/timepicker-selected.png "выбранное время")](timepicker-images/timepicker-selected-large.png#lightbox "выбранное время")

При условии, что [ `Switch` ](xref:Xamarin.Forms.Switch) установлен в положение on приложение отображает диалоговое окно предупреждения о том, пользователем текста в [ `Entry` ](xref:Xamarin.Forms.Entry) при возникновении установленный период времени:

[![Всплывающее окно таймера](timepicker-images/timer-test.png "всплывающее окно таймера")](timepicker-images/timer-test-large.png#lightbox "таймера всплывающего окна")

Как только отображается диалоговое окно предупреждения, [ `Switch` ](xref:Xamarin.Forms.Switch) установлен в положение выкл.

## <a name="related-links"></a>Связанные ссылки

- [Пример SetTimer](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TimePicker/)
- [TimePicker API](xref:Xamarin.Forms.TimePicker)
