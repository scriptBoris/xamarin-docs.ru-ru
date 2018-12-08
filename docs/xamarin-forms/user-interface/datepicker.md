---
title: Xamarin.Forms DatePicker
description: DatePicker находится представление Xamarin.Forms, которое позволяет пользователю выбрать дату. В этой статье объясняется, как использовать DatePicker в приложении Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 68E8EF8A-42E7-4939-8ABE-64D060E609D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/04/2018
ms.openlocfilehash: 263cbb4aed47794a09341bb8c78c4835510e50c0
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53059578"
---
# <a name="xamarinforms-datepicker"></a>Xamarin.Forms DatePicker

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/DatePicker)

_Представление Xamarin.Forms, которое позволяет пользователю выбрать дату._

Xamarin.Forms [ `DatePicker` ](xref:Xamarin.Forms.DatePicker) вызывает элемент управления выбора даты платформы и позволяет пользователю выбрать дату. `DatePicker` Определяет восемь свойств:

- [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) типа [ `DateTime` ](xref:System.DateTime), которое по умолчанию к первому дню 1900 года.
- [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) типа `DateTime`, какие значения по умолчанию до последнего дня года 2100.
- [`Date`](xref:Xamarin.Forms.DatePicker.Date) типа `DateTime`, выбранной даты, по умолчанию используется значение [ `DateTime.Today` ](xref:System.DateTime.Today).
- [`Format`](xref:Xamarin.Forms.DatePicker.Format) типа `string`, [стандартный](/dotnet/standard/base-types/standard-date-and-time-format-strings/) или [пользовательских](/dotnet/standard/base-types/custom-date-and-time-format-strings/) .NET форматирования строки, которая по умолчанию «D», долго формат даты.
- [`TextColor`](xref:Xamarin.Forms.DatePicker.TextColor) типа [ `Color` ](xref:Xamarin.Forms.Color), цвет, используемый для отображения выбранной даты, значение по умолчанию — [ `Color.Default` ](xref:Xamarin.Forms.Color.Default).
- [`FontAttributes`](xref:Xamarin.Forms.DatePicker.FontAttributes) типа [ `FontAttributes` ](xref:Xamarin.Forms.FontAttributes), который по умолчанию [ `FontAtributes.None` ](xref:Xamarin.Forms.FontAttributes.None).
- [`FontFamily`](xref:Xamarin.Forms.DatePicker.FontFamily) типа `string`, который по умолчанию `null`.
- [`FontSize`](xref:Xamarin.Forms.DatePicker.FontSize) типа `double`, который по умолчанию -1,0.

`DatePicker` Активируется [ `DateSelected` ](xref:Xamarin.Forms.DatePicker.DateSelected) событие, когда пользователь выбирает дату.

> [!WARNING]
> При задании `MinimumDate` и `MaximumDate`, убедитесь, что `MinimumDate` , всегда меньше или равно `MaximumDate`. В противном случае `DatePicker` приведет к появлению исключения.

На внутреннем уровне `DatePicker` гарантирует, что `Date` между `MinimumDate` и `MaximumDate`включительно. Если `MinimumDate` или `MaximumDate` устанавливается таким образом, чтобы `Date` не между ними, `DatePicker` будет изменить значение параметра `Date`.

Все восемь свойств обеспечиваются [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) объектов, что означает, что их можно применить различные стили, и свойства могут быть целями привязки данных. `Date` Свойство имеет режим привязки по умолчанию [ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay), что означает, что он может быть целевой объект привязки данных в приложение, использующее [Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) архитектура.

## <a name="initializing-the-datetime-properties"></a>Инициализация свойств даты и времени

В коде, можно инициализировать `MinimumDate`, `MaximumDate`, и `Date` свойства к значениям типа `DateTime`:

```csharp
DatePicker datePicker = new DatePicker
{
    MinimumDate = new DateTime(2018, 1, 1),
    MaximumDate = new DateTime(2018, 12, 31),
    Date = new DateTime(2018, 6, 21)
};
```

Когда `DateTime` значение указывается в XAML, средство синтаксического анализа XAML использует `DateTime.Parse` метод с `CultureInfo.InvariantCulture` аргумент для преобразования строки в `DateTime` значение. Даты должен быть указан в виде точного: двузначное месяцев, дней из двух цифр и года из четырех цифр, разделенных косыми чертами:

```xaml
<DatePicker MinimumDate="01/01/2018"
            MaximumDate="12/31/2018"
            Date="06/21/2018" />
```

Если `BindingContext` свойство `DatePicker` присваивается экземпляр ViewModel, содержащий свойства типа `DateTime` с именем `MinDate`, `MaxDate`, и `SelectedDate` (например), можно создать экземпляр `DatePicker` следующим образом :

```xaml
<DatePicker MinimumDate="{Binding MinDate}"
            MaximumDate="{Binding MaxDate}"
            Date="{Binding SelectedDate}" />
```

В этом примере все три свойства инициализируются в соответствующие свойства в ViewModel. Так как `Date` свойство имеет режим привязки `TwoWay`, любой новой даты, выбранной пользователем автоматически отражаются в ViewModel.

Если `DatePicker` не содержит привязки на его `Date` свойство, приложения следует присоединить обработчик для `DateSelected` событие, чтобы быть в курсе, когда пользователь выбирает новую дату.

Сведения о настройке свойств шрифта, см. в разделе [шрифты](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="datepicker-and-layout"></a>DatePicker и макет

Можно использовать параметр неограниченного горизонтальном расположении, например `Center`, `Start`, или `End` с `DatePicker`:

```xaml
<DatePicker ···
            HorizontalOptions="Center"
            ··· />
```

Тем не менее это не рекомендуется. Зависимости от настройки `Format` выбранным свойством даты может потребоваться другое отображаемое ширины. Например, строка формата «D» вызывает `DateTime` для отображения даты в длинном формате и «среда, сентябрь 12 2018 г."необходим ширины экрана больше, чем «Пятница, май 4 2018 г.". В зависимости от платформы, это различие может привести к `DateTime` представление, чтобы изменить ширину в макете, или для отображения усечение.

> [!TIP]
> Лучше всего использовать значение по умолчанию `HorizontalOptions` параметр `Fill` с `DatePicker`и не использовать ширину `Auto` при помещении `DatePicker` в `Grid` ячейки.

## <a name="datepicker-in-an-application"></a>DatePicker в приложении

[ **DaysBetweenDates** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/DatePicker) пример включает в себя два `DatePicker` представлений на его странице. Их можно использовать для выбора двух дат, и программа вычисляет количество дней в указанном интервале дат. Программа не изменить параметры `MinimumDate` и `MaximumDate` свойства, поэтому необходимо указать две даты от 1900 до 2100.

Ниже приведен файл XAML.

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DaysBetweenDates"
             x:Class="DaysBetweenDates.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <StackLayout Margin="10">
        <Label Text="Days Between Dates"
               Style="{DynamicResource TitleStyle}"
               Margin="0, 20"
               HorizontalTextAlignment="Center" />

        <Label Text="Start Date:" />

        <DatePicker x:Name="startDatePicker"
                    Format="D"
                    Margin="30, 0, 0, 30"
                    DateSelected="OnDateSelected" />

        <Label Text="End Date:" />

        <DatePicker x:Name="endDatePicker"
                    MinimumDate="{Binding Source={x:Reference startDatePicker},
                                          Path=Date}"
                    Format="D"
                    Margin="30, 0, 0, 30"
                    DateSelected="OnDateSelected" />

        <StackLayout Orientation="Horizontal"
                     Margin="0, 0, 0, 30">
            <Label Text="Include both days in total: "
                   VerticalOptions="Center" />
            <Switch x:Name="includeSwitch"
                    Toggled="OnSwitchToggled" />
        </StackLayout>

        <Label x:Name="resultLabel"
               FontAttributes="Bold"
               HorizontalTextAlignment="Center" />

    </StackLayout>
</ContentPage>
```

Каждый `DatePicker` назначается `Format` свойство «D» для длинный формат даты. Обратите внимание, что `endDatePicker` объект имеет привязку, предназначенного его `MinimumDate` свойство. Источник привязки является выбранным `Date` свойство `startDatePicker` объекта. Это гарантирует, что дата окончания это всегда позже или равна дате начала. Дополнение к двум `DatePicker` объектов, `Switch` имеет метку «Включить оба дня в целом».

Два `DatePicker` представления имеют обработчики, присоединенные к `DateSelected` событий и `Switch` присоединил обработчик к его `Toggled` событий. Эти обработчики событий в файле кода и активировать новое вычисление дней между двумя датами:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
    }

    void OnDateSelected(object sender, DateChangedEventArgs args)
    {
        Recalculate();
    }

    void OnSwitchToggled(object sender, ToggledEventArgs args)
    {
        Recalculate();
    }

    void Recalculate()
    {
        TimeSpan timeSpan = endDatePicker.Date - startDatePicker.Date +
            (includeSwitch.IsToggled ? TimeSpan.FromDays(1) : TimeSpan.Zero);

        resultLabel.Text = String.Format("{0} day{1} between dates",
                                            timeSpan.Days, timeSpan.Days == 1 ? "" : "s");
    }
}
```

Когда образец сначала выполняется, оба `DatePicker` представления инициализируются на сегодняшнюю дату. На следующем снимке экрана показан программу на iOS, Android и универсальной платформы Windows:

[![Дней между датами начала](datepicker-images/DaysBetweenDatesStart.png "дней между датами начала")](datepicker-images/DaysBetweenDatesStart-Large.png#lightbox "дней между датами начала")

Коснитесь любой из `DatePicker` отображает вызывает средство выбора даты платформы. Платформы реализованы средства выбора даты самыми разными способами, но каждый подход крайне знакомы пользователям функции этой платформы:

[![Выберите дней между датами](datepicker-images/DaysBetweenDatesSelect.png "выберите дней между датами")](datepicker-images/DaysBetweenDatesSelect-Large.png#lightbox "выберите дней между датами")

> [!TIP]
> В Android `DatePicker` диалогового окна можно настроить путем переопределения `CreateDatePickerDialog` метод в пользовательское средство отрисовки. Это позволяет, например, дополнительные кнопки для добавления в диалоговое окно.

После выбора двумя датами, приложение отображает количество дней в указанном интервале дат:

[![Дней от даты результат](datepicker-images/DaysBetweenDatesResult.png "дней между датами результат")](datepicker-images/DaysBetweenDatesResult-Large.png#lightbox "дней между датами результат")

## <a name="related-links"></a>Связанные ссылки

- [Пример DaysBetweenDates](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/DatePicker)
- [DatePicker API](xref:Xamarin.Forms.DatePicker)
