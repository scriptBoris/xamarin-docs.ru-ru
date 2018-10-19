---
title: Средство выбора элемента управления в Xamarin.iOS
description: В этом документе описывается, как проектировать и работать с элементами управления средства выбора в приложении Xamarin.iOS. В этом примере обсуждается реализация управляющий элемент выбора в коде и в конструкторе iOS.
ms.prod: xamarin
ms.assetid: A2369EFC-285A-44DD-9E80-EC65BC3DF041
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/14/2018
ms.openlocfilehash: 0ef33c2036b1ff2d5a7e2035ca5fa8af58672867
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2018
ms.locfileid: "34789916"
---
# <a name="picker-control-in-xamarinios"></a>Средство выбора элемента управления в Xamarin.iOS

Объект [ `UIPickerView` ](https://developer.xamarin.com/api/type/UIKit.UIPickerView/) дает возможность выбрать значение из списка прокрутив отдельные компоненты интерфейса колесика по принципу.

Выбор часто используются для выбора даты и времени; Компания Apple предоставляет [`UIDatePicker`](https://developer.xamarin.com/api/type/UIKit.UIDatePicker/)
класс для этой цели.

Статья описывает, как реализовать и использовать `UIPickerView` и `UIDatePicker` элементов управления.

## <a name="uipickerview"></a>UIPickerView

### <a name="implementing-a-picker"></a>Реализация средства выбора

Реализовать управляющий элемент выбора путем создания нового `UIPickerView`:

```csharp
UIPickerView pickerView = new UIPickerView(
    new CGRect(
        UIScreen.MainScreen.Bounds.X - UIScreen.MainScreen.Bounds.Width, 
        UIScreen.MainScreen.Bounds.Height - 230,
        UIScreen.MainScreen.Bounds.Width,
        180
    )
);
```

### <a name="pickers-and-storyboards"></a>Выбор и раскадровки

Чтобы создать управляющий элемент выбора в **конструктор iOS**, перетащите **выбора представления** из **элементов** в область конструктора.

![Перетащите в область конструктора представления выбора](picker-images/image1.png "перетащите представление выбора в область конструктора")

### <a name="working-with-a-picker-control"></a>Работа с элементом управления выбора

Управляющий элемент выбора использует _модели_ для взаимодействия с данными:

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();
    var pickerModel = new PeopleModel(personLabel);
    personPicker.Model = pickerModel;
}
```

[ `UIPickerViewModel` ](https://developer.xamarin.com/api/type/UIKit.UIPickerViewModel/) Базовый класс реализует два интерфейса [`IUIPickerDataSource`](https://developer.xamarin.com/api/type/UIKit.IUIPickerViewDataSource/)
и [ `IUIPickerViewDelegate` ](https://developer.xamarin.com/api/type/UIKit.IUIPickerViewDelegate/), который объявлять различные методы, которые указывают средства выбора данных и обрабатывает взаимодействие:

```csharp
public class PeopleModel : UIPickerViewModel
{
    public string[] names = new string[] {
            "Amy Burns",
            "Kevin Mullins",
            "Craig Dunn",
            "Joel Martinez",
            "Charles Petzold",
            "David Britch",
            "Mark McLemore",
            "Tom Opegenorth",
            "Joseph Hill",
            "Miguel De Icaza"
        };

    private UILabel personLabel;

    public PeopleModel(UILabel personLabel)
    {
        this.personLabel = personLabel;
    }

    public override nint GetComponentCount(UIPickerView pickerView)
    {
        return 2;
    }

    public override nint GetRowsInComponent(UIPickerView pickerView, nint component)
    {
        return names.Length;
    }

    public override string GetTitle(UIPickerView pickerView, nint row, nint component)
    {
        if (component == 0)
            return names[row];
        else
            return row.ToString();
    }

    public override void Selected(UIPickerView pickerView, nint row, nint component)
    {
        personLabel.Text = $"This person is: {names[pickerView.SelectedRowInComponent(0)]},\n they are number {pickerView.SelectedRowInComponent(1)}";
    }

    public override nfloat GetComponentWidth(UIPickerView picker, nint component)
    {
        if (component == 0)
            return 240f;
        else
            return 40f;
    }

    public override nfloat GetRowHeight(UIPickerView picker, nint component)
    {
        return 40f;
    }
```

Управляющий элемент выбора может содержать несколько столбцов или _компоненты_. Компоненты секционировать управляющий элемент выбора на несколько секций, позволяя для выбора проще и более конкретных данных:

![Средство выбора с двумя компонентами](picker-images/image3.png "выбора с помощью двух компонентов")

Чтобы указать число компонентов в средстве выбора, используйте [`GetComponentCount`](https://developer.xamarin.com/api/member/UIKit.UIPickerViewModel.GetComponentCount/p/UIKit.UIPickerView/) 
метод.

### <a name="customizing-a-pickers-appearance"></a>Настройка внешнего вида управляющий элемент выбора

Чтобы настроить внешний вид управляющий элемент выбора, используйте [`UIPickerView.UIPickerViewAppearance`](https://developer.xamarin.com/api/type/UIKit.UIPickerView+UIPickerViewAppearance/)
класс, или переопределить [ `GetView` ](https://developer.xamarin.com/api/member/UIKit.UIPickerViewModel.GetView/p/UIKit.UIPickerView/System.nint/System.nint/UIKit.UIView/) и [ `GetRowHeight` ](https://developer.xamarin.com/api/member/UIKit.UIPickerViewModel.GetRowHeight/p/UIKit.UIPickerView/System.nint/) методы в `UIPickerViewModel`.

## <a name="uidatepicker"></a>UIDatePicker

### <a name="implementing-a-date-picker"></a>Реализация управляющий элемент выбора даты

Реализовать управляющий элемент выбора даты путем создания экземпляра `UIDatePicker`:

```csharp
UIPickerView pickerView = new UIPickerView(
    new CGRect(
        UIScreen.MainScreen.Bounds.X - UIScreen.MainScreen.Bounds.Width,
        UIScreen.MainScreen.Bounds.Height - 230,
        UIScreen.MainScreen.Bounds.Width,
        180
     )
);
```

### <a name="date-pickers-and-storyboards"></a>Выбор даты и раскадровки

Чтобы создать управляющий элемент выбора даты в **конструктор iOS**, перетащите **Date Picker** из **элементов** в область конструктора.

![Перетащите управляющий элемент выбора даты в область конструктора](picker-images/image2.png "перетащите управляющий элемент выбора даты в область конструктора")

### <a name="date-picker-properties"></a>Свойства выбора даты

#### <a name="minimum-and-maximum-date"></a>Минимальное и максимальное значение даты

[`MinimumDate`](https://developer.xamarin.com/api/property/UIKit.UIDatePicker.MinimumDate/) и [ `MaximumDate` ](https://developer.xamarin.com/api/property/UIKit.UIDatePicker.MaximumDate/) ограничить диапазон дат в элементе выбора даты. Например следующий код ограничивает управляющий элемент выбора даты до 60 лет, вплоть до момента присутствует:

```csharp
var calendar = new NSCalendar(NSCalendarType.Gregorian);
var currentDate = NSDate.Now;
var components = new NSDateComponents();
components.Year = -60;
NSDate minDate = calendar.DateByAddingComponents(components, NSDate.Now, NSCalendarOptions.None);
datePickerView.MinimumDate = minDate;
datePickerView.MaximumDate = NSDate.Now;
```

> [!TIP]
> Можно явно привести тип `DateTime` для `NSDate`:
> ```csharp
> DatePicker.MinimumDate = (NSDate)DateTime.Today.AddDays (-7);
> DatePicker.MaximumDate = (NSDate)DateTime.Today.AddDays (7);
> ```

#### <a name="minute-interval"></a>Пятиминутный интервал

[ `MinuteInterval` ](https://developer.xamarin.com/api/property/UIKit.UIDatePicker.MinuteInterval/) Свойство задает интервал, с которой средство выбора отобразит минут:

```csharp
datePickerView.MinuteInterval = 10;
```

#### <a name="mode"></a>Режим

Выбор даты поддерживают четыре [режимы](https://developer.xamarin.com/api/type/UIKit.UIDatePickerMode/), описанной ниже:

##### <a name="uidatepickermodetime"></a>UIDatePickerMode.Time

`UIDatePickerMode.Time` Отображает время с помощью селектора час и минуту и необязательно обозначение AM или PM:

```csharp
datePickerView.Mode = UIDatePickerMode.Time;
```

![UIDatePickerMode.Time](picker-images/image8.png "UIDatePickerMode.Time")

##### <a name="uidatepickermodedate"></a>UIDatePickerMode.Date

`UIDatePickerMode.Date` Отображает дату месяца, дня и года селектор:

```csharp
datePickerView.Mode = UIDatePickerMode.Date;
```

![UIDatePickerMode.Date](picker-images/image7.png "UIDatePickerMode.Date")

Порядок селекторы зависит от языкового стандарта элементе выбора даты, который по умолчанию использует языкового стандарта системы. На рисунке выше показан макет селекторов в `en_US` языкового стандарта, но следующие изменяет порядок день | Месяц | Год:

```csharp
datePickerView.Locale = NSLocale.FromLocaleIdentifier("en_GB");
```

![День | Месяц | Год](picker-images/image9.png "день | Месяц | Год")

##### <a name="uidatepickermodedateandtime"></a>UIDatePickerMode.DateAndTime

`UIDatePickerMode.DateAndTime` Отображает сокращенное представление даты, время в часах и минутах и необязательно обозначение AM или PM (в зависимости от того, используется ли в часовом 12 или 24):

```csharp
datePickerView.Mode = UIDatePickerMode.DateAndTime;
```

![UIDatePickerMode.DateAndTime](picker-images/image6.png "UIDatePickerMode.DateAndTime")

Как и в [ `UIDatePickerMode.Date` ](#uidatepickermodedate), порядок следования селекторы и использование в 12 или 24 часовом зависит от языкового стандарта элементе выбора даты.

> [!TIP]
> Используйте `Date` свойство, чтобы сохранить значение управляющий элемент выбора даты в режиме `UIDatePickerMode.Time`, `UIDatePickerMode.Date`, или `UIDatePickerMode.DateAndTime`. Это значение хранится в виде `NSDate`.

##### <a name="uidatepickermodecountdowntimer"></a>UIDatePickerMode.CountDownTimer

`UIDatePickerMode.CountDownTimer` Отображает значения часов и минут:

```csharp
datePickerView.Mode = UIDatePickerMode.CountDownTimer;
```

![«UIDatePickerMode.CountDownTimer»](picker-images/image5.png "UIDatePickerMode.CountDownTimer")

`CountDownDuration` Свойство отражает ценность управляющий элемент выбора даты в `UIDatePickerMode.CountDownTimer` режиме. Например, чтобы добавить значение обратного отсчета до текущей даты:

```csharp
var currentTime = NSDate.Now;
var countDownTimerTime = datePickerView.CountDownDuration;
var finishCountdown = currentTime.AddSeconds(countDownTimerTime);

dateLabel.Text = "Alarm set for:" + coundownTimeformat.ToString(finishCountdown);
```

#### <a name="nsdateformatter"></a>NSDateFormatter

Для форматирования `NSDate`, использовать [ `NSDateFormatter` ](https://developer.xamarin.com/api/type/Foundation.NSDateFormatter/).

Чтобы использовать `NSDateFormatter`, вызовите его [ `ToString` ](https://developer.xamarin.com/api/member/Foundation.NSDateFormatter.ToString/p/Foundation.NSDate/) метод. Пример:

```csharp
var date = NSDate.Now;
var formatter = new NSDateFormatter();
formatter.DateStyle = NSDateFormatterStyle.Full;
formatter.TimeStyle = NSDateFormatterStyle.Full;
var formattedDate = formatter.ToString(d);
// Tuesday, August 14, 2018 at 11:20:42 PM Mountain Daylight Time
```

##### <a name="dateformat"></a>DateFormat

[ `DateFormat` ](https://developer.xamarin.com/api/property/Foundation.NSDateFormatter.DateFormat/) Свойство (строка) `NSDateFormatter` обеспечивает настраиваемые даты в спецификации формата:

```csharp
NSDateFormatter dateFormat = new NSDateFormatter();
dateFormat.DateFormat = "yyyy-MM-dd";
```

##### <a name="timestyle"></a>TimeStyle

[ `TimeStyle` ](https://developer.xamarin.com/api/property/Foundation.NSDateFormatter.TimeStyle/) Свойство ( [ `NSDateFormatterStyle` ](https://developer.xamarin.com/api/type/Foundation.NSDateFormatterStyle/)) из `NSDateFormatter` указывает форматирование времени на основе предопределенных стилей:

```csharp
NSDateFormatter timeFormat = new NSDateFormatter();
timeFormat.TimeStyle = NSDateFormatterStyle.Short;
```

Различные `NSDateFormatterStyle` значения отображения времени следующим образом:

- `NSDateFormatterStyle.Full`: 19:46:00 по восточному времени перехода на летнее время
- `NSDateFormatterStyle.Long`: 7:47:00 PM ПО ЛЕТНЕМУ ВОСТОЧНОМУ ВРЕМЕНИ
- `NSDateFormatterStyle.Medium`: 7:47:00 PM
- `NSDateFormatterSytle.Short`: 7:47 PM

##### <a name="datestyle"></a>DateStyle

[ `DateStyle` ](https://developer.xamarin.com/api/property/Foundation.NSDateFormatter.DateStyle/) Свойство ( `NSDateFormatterStyle`) из `NSDateFormatter` определяет форматирование дат на основе предопределенных стилей:

```csharp
NSDateFormatter dateTimeformat = new NSDateFormatter();
dateTimeformat.DateStyle = NSDateFormatterStyle.Long;
```

Различные `NSDateFormatterStyle` значения отображение дат следующим образом:

- `NSDateFormatterStyle.Full`: Среда, август 2 2017 в 7:48
- `NSDateFormatterStyle.Long`: 2 август 2017 г. в 19:49:00
- `NSDateFormatterStyle.Medium`: 2 августа 2017 г., 19:49:00
- `NSDateFormatterStyle.Short`: 8/2/17, 7:50 PM

> [!NOTE]
> `DateFormat` и `DateStyle` / `TimeStyle` предоставляют различные способы указания форматирование даты и времени. Самое последнее задание свойств определяют выходной модуль форматирования даты.

## <a name="related-links"></a>Связанные ссылки

- [PickerControl (пример)](https://developer.xamarin.com/samples/monotouch/PickerControl/)
