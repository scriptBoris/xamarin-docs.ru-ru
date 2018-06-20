---
title: Часть 5. От привязок данных для MVVM
description: Шаблон MVVM обеспечивает разделение между трех слоев программного обеспечения — пользовательского интерфейса XAML, который называется представления; базовых данных, называемых моделью. и в роли посредника между представления и модель, вызывается ViewModel.
ms.prod: xamarin
ms.assetid: 48B37D44-4FB1-41B2-9A5E-6D383B041F81
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 10/25/2017
ms.openlocfilehash: e83f8a585c4badc31bffaea53bb2f183e7b11fc9
ms.sourcegitcommit: d70fcc6380834127fdc58595aace55b7821f9098
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268866"
---
# <a name="part-5-from-data-bindings-to-mvvm"></a>Часть 5. От привязок данных для MVVM

_Схема архитектуры Model-View-ViewModel (MVVM) и, следовательно с XAML в виду. Шаблон обеспечивает разделение между трех слоев программного обеспечения — пользовательского интерфейса XAML, который называется представления; базовых данных, называемых моделью. и в роли посредника между представления и модель, вызывается ViewModel. Представление и ViewModel обычно соединяются через привязки данных, определенные в XAML-файле. BindingContext для представления обычно представляет собой экземпляр ViewModel._

## <a name="a-simple-viewmodel"></a>Простой ViewModel

Введение в ViewModels давайте сначала посмотрим программы без него.
Ранее вы узнали, как определить новое объявление пространства имен XML, чтобы разрешить XAML-файл для ссылочных классов в других сборках. Вот программа, которая определяет объявление пространства имен XML для `System` пространство имен:

```csharp
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

Программа может использовать `x:Static` для получения текущей даты и времени из статического `DateTime.Now` свойство и укажите его `DateTime` значение `BindingContext` на `StackLayout`:

```xaml
<StackLayout BindingContext="{x:Static sys:DateTime.Now}" …>
```

`BindingContext` представляет собой очень специальное свойство: при установке `BindingContext` на элемент, он наследует все дочерние элементы этого элемента. Это означает, что все дочерние элементы `StackLayout` этот же `BindingContext`, и они могут содержать простые привязки к свойствам этого объекта.

В **One-Shot DateTime** программы, два дочерних элементов содержат привязки к свойствам, `DateTime` значение, а два других дочерних объектов содержат привязок, которые отсутствуют путь привязки. Это означает, что `DateTime` само значение используется для `StringFormat`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=mscorlib"
             x:Class="XamlSamples.OneShotDateTimePage"
             Title="One-Shot DateTime Page">

    <StackLayout BindingContext="{x:Static sys:DateTime.Now}"
                 HorizontalOptions="Center"
                 VerticalOptions="Center">

        <Label Text="{Binding Year, StringFormat='The year is {0}'}" />
        <Label Text="{Binding StringFormat='The month is {0:MMMM}'}" />
        <Label Text="{Binding Day, StringFormat='The day is {0}'}" />
        <Label Text="{Binding StringFormat='The time is {0:T}'}" />

    </StackLayout>
</ContentPage>
```

Конечно большой проблемой является дата и время набор после сначала при построении страницы и никогда не изменений:

[![](data-bindings-to-mvvm-images/oneshotdatetime.png "Представление, содержащее дату и время")](data-bindings-to-mvvm-images/oneshotdatetime-large.png#lightbox "представление, содержащее дату и время")

XAML-файл может отображать часов, которые всегда показывает текущее время, но его необходимо написать код, чтобы помочь. Если решения с точки зрения MVVM модели и ViewModel являются классами, написан полностью в коде. Представление чаще всего XAML-файл, который ссылается на свойства, определенные в ViewModel через привязки данных.

Соответствующие модели относительно этих ViewModel, поэтому правильный ViewModel игнорирующих представления. Тем не менее очень часто программист адаптирует типы данных, предоставляемые ViewModel для типов данных, связанных с интерфейсами конкретного пользователя. Например если модель обращается к базе данных, содержащий строки 8-разрядных символов ASCII, ViewModel потребуется преобразование этих строк для строк Юникода для монопольного использования Юникода в пользовательском интерфейсе.

В простых примеров MVVM (например, как показано ниже) часто не предусмотрена модель и шаблон включает в себя только просматривать и ViewModel соединенным привязки данных.

Вот ViewModel для часов с только одним свойством `DateTime`, но которая обновляет, `DateTime` свойство каждую секунду:

```csharp
using System;
using System.ComponentModel;
using Xamarin.Forms;

namespace XamlSamples
{
    class ClockViewModel : INotifyPropertyChanged
    {
        DateTime dateTime;

        public event PropertyChangedEventHandler PropertyChanged;

        public ClockViewModel()
        {
            this.DateTime = DateTime.Now;

            Device.StartTimer(TimeSpan.FromSeconds(1), () =>
                {
                    this.DateTime = DateTime.Now;
                    return true;
                });
        }

        public DateTime DateTime
        {
            set
            {
                if (dateTime != value)
                {
                    dateTime = value;

                    if (PropertyChanged != null)
                    {
                        PropertyChanged(this, new PropertyChangedEventArgs("DateTime"));
                    }
                }
            }
            get
            {
                return dateTime;
            }
        }
    }
}
```

Обычно реализуют ViewModels `INotifyPropertyChanged` интерфейса, это означает, что класс запускает `PropertyChanged` событие при изменении одного из его свойств. Механизм привязки данных в Xamarin.Forms обработчик присоединяется к этому `PropertyChanged` событий, можно получать уведомления при изменении свойств и сохранить целевой обновлены новым значением.

Часы, в зависимости от этого ViewModel может быть сложнее, чем это:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.ClockPage"
             Title="Clock Page">

    <Label Text="{Binding DateTime, StringFormat='{0:T}'}"
           FontSize="Large"
           HorizontalOptions="Center"
           VerticalOptions="Center">
        <Label.BindingContext>
            <local:ClockViewModel />
        </Label.BindingContext>
    </Label>
</ContentPage>
```

Обратите внимание как `ClockViewModel` равно `BindingContext` из `Label` с помощью теги элементов свойства. Кроме того, можно создать экземпляр `ClockViewModel` в `Resources` коллекции и присвойте ему значение `BindingContext` через `StaticResource` расширения разметки. Также файл кода можно создать экземпляр ViewModel.

`Binding` Расширения разметки на `Text` свойство `Label` форматы `DateTime` свойство. Ниже приведен список.

[![](data-bindings-to-mvvm-images/clock.png "Представление, содержащее дату и время с помощью ViewModel")](data-bindings-to-mvvm-images/clock-large.png#lightbox "представление, содержащее дату и время с помощью ViewModel")

Можно также получить доступ к свойствам объекта `DateTime` свойства ViewModel, разделив их точками:

```xaml
<Label Text="{Binding DateTime.Second, StringFormat='{0}'}" … >
```

## <a name="interactive-mvvm"></a>Интерактивный MVVM

MVVM довольно часто используется с двухсторонней привязки для интерактивное представление на основе базовой модели данных.

Ниже приведен класс с именем `HslViewModel` , преобразующий `Color` значение в `Hue`, `Saturation`, и `Luminosity` значения и наоборот:

```csharp
using System;
using System.ComponentModel;
using Xamarin.Forms;

namespace XamlSamples
{
    public class HslViewModel : INotifyPropertyChanged
    {
        double hue, saturation, luminosity;
        Color color;

        public event PropertyChangedEventHandler PropertyChanged;

        public double Hue
        {
            set
            {
                if (hue != value)
                {
                    hue = value;
                    OnPropertyChanged("Hue");
                    SetNewColor();
                }
            }
            get
            {
                return hue;
            }
        }

        public double Saturation
        {
            set
            {
                if (saturation != value)
                {
                    saturation = value;
                    OnPropertyChanged("Saturation");
                    SetNewColor();
                }
            }
            get
            {
                return saturation;
            }
        }

        public double Luminosity
        {
            set
            {
                if (luminosity != value)
                {
                    luminosity = value;
                    OnPropertyChanged("Luminosity");
                    SetNewColor();
                }
            }
            get
            {
                return luminosity;
            }
        }

        public Color Color
        {
            set
            {
                if (color != value)
                {
                    color = value;
                    OnPropertyChanged("Color");

                    Hue = value.Hue;
                    Saturation = value.Saturation;
                    Luminosity = value.Luminosity;
                }
            }
            get
            {
                return color;
            }
        }

        void SetNewColor()
        {
            Color = Color.FromHsla(Hue, Saturation, Luminosity);
        }

        protected virtual void OnPropertyChanged(string propertyName)
        {
            PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
        }
    }
}
```

Изменения `Hue`, `Saturation`, и `Luminosity` причина свойства `Color` свойства для изменения и изменения `Color` вызывает три свойства для изменения. Это может показаться бесконечный цикл, за исключением того, чтобы не вызвать класс `PropertyChanged` события, если свойство действительно были изменены. Этот запрос помещает end для цикла в противном случае неконтролируемых обратной связи.

Следующий XAML-файл содержит `BoxView` которого `Color` свойство привязано к `Color` свойства ViewModel и три `Slider` и три `Label` представления привязан к `Hue`, `Saturation`и `Luminosity` свойства:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.HslColorScrollPage"
             Title="HSL Color Scroll Page">
    <ContentPage.BindingContext>
        <local:HslViewModel Color="Aqua" />
    </ContentPage.BindingContext>

    <StackLayout Padding="10, 0">
        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}"
               HorizontalOptions="Center" />

        <Slider Value="{Binding Hue, Mode=TwoWay}" />

        <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}"
               HorizontalOptions="Center" />

        <Slider Value="{Binding Saturation, Mode=TwoWay}" />

        <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}"
               HorizontalOptions="Center" />

        <Slider Value="{Binding Luminosity, Mode=TwoWay}" />
    </StackLayout>
</ContentPage>
```

Привязка на каждом `Label` значение по умолчанию `OneWay`. Ему нужно только отобразить значение. Однако привязки для каждого `Slider` — `TwoWay`. Это позволяет `Slider` инициализированную из ViewModel. Обратите внимание, что `Color` свойству `Aqua` при создании экземпляра ViewModel. Однако изменения в `Slider` также необходимо указать новое значение для свойства в ViewModel, который затем вычисляет новый цвет.

[![](data-bindings-to-mvvm-images/hslcolorscroll.png "С помощью привязки данных двусторонняя MVVM")](data-bindings-to-mvvm-images/hslcolorscroll-large.png#lightbox "MVVM с помощью двусторонней привязки данных")

## <a name="commanding-with-viewmodels"></a>Выполнение команд с ViewModels

Во многих случаях шаблона MVVM ограничивается манипуляции элементов данных: объекты данных в ViewModel параллельные объекты пользовательского интерфейса в представлении.

Тем не менее иногда представления должен содержать кнопок, инициирующих различных действий в ViewModel. Но ViewModel не должны содержать `Clicked` обработчики для кнопок, так как, будет связать ViewModel парадигме конкретного пользовательского интерфейса.

Чтобы разрешить ViewModels более зависеть от объектов конкретного пользовательского интерфейса, но по-прежнему позволяет методы будут вызываться в рамках ViewModel, *команда* интерфейс существует. Этот интерфейс команда поддерживается следующими элементами в Xamarin.Forms:

-  `Button`
-  `MenuItem`
-  `ToolbarItem`
-  `SearchBar`
-  `TextCell` (и поэтому также `ImageCell`)
-  `ListView`
-  `TapGestureRecognizer`

За исключением элемента `SearchBar` и `ListView` элемента, эти элементы определяют два свойства:

-  `Command` типа  `System.Windows.Input.ICommand`
-  `CommandParameter` типа  `Object`

`SearchBar` Определяет `SearchCommand` и `SearchCommandParameter` свойства, пока `ListView` определяет `RefreshCommand` свойство типа `ICommand`.

`ICommand` Интерфейс определяет два метода и одно событие:

-  `void Execute(object arg)`
-  `bool CanExecute(object arg)`
-  `event EventHandler CanExecuteChanged`

ViewModel можно определить свойства типа `ICommand`. После этого можно связать эти свойства, чтобы `Command` каждого экземпляра `Button` или другой элемент, или возможно пользовательское представление, реализующий этот интерфейс. При необходимости можно задать `CommandParameter` свойство для идентификации отдельных `Button` объектов (или другие элементы), привязанных к этому свойству ViewModel. На внутреннем уровне `Button` вызовы `Execute` метод всякий раз, когда пользователь касается `Button`, передав в `Execute` метод его `CommandParameter`.

`CanExecute` Метод и `CanExecuteChanged` события используются для случаев, где `Button` tap может быть в данный момент недопустим, в этом случае `Button` отключите сам. `Button` Вызовы `CanExecute` при `Command` сначала свойству и каждый раз, когда `CanExecuteChanged` событие. Если `CanExecute` возвращает `false`, `Button` отключается и не генерирует `Execute` вызовов.

Для получения справки при добавлении команды для вашего ViewModels Xamarin.Forms определяет два класса, реализующих `ICommand`: `Command` и `Command<T>` где `T` тип аргументов `Execute` и `CanExecute`. Эти классы определяют несколько конструкторов, а также `ChangeCanExecute` метод, который можно вызвать ViewModel, чтобы принудительно `Command` объекта срабатывание `CanExecuteChanged` событий.

Вот ViewModel для простой клавиатуре, которое предназначено для ввода номера телефона. Обратите внимание, что `Execute` и `CanExecute` метод определяются как лямбда-выражение прямо в конструктор:

```csharp
using System;
using System.ComponentModel;
using System.Windows.Input;
using Xamarin.Forms;

namespace XamlSamples
{
    class KeypadViewModel : INotifyPropertyChanged
    {
        string inputString = "";
        string displayText = "";
        char[] specialChars = { '*', '#' };

        public event PropertyChangedEventHandler PropertyChanged;

        // Constructor
        public KeypadViewModel()
        {
            AddCharCommand = new Command<string>((key) =>
                {
                    // Add the key to the input string.
                    InputString += key;
                });

            DeleteCharCommand = new Command(() =>
                {
                    // Strip a character from the input string.
                    InputString = InputString.Substring(0, InputString.Length - 1);
                },
                () =>
                {
                    // Return true if there's something to delete.
                    return InputString.Length > 0;
                });
        }

        // Public properties
        public string InputString
        {
            protected set
            {
                if (inputString != value)
                {
                    inputString = value;
                    OnPropertyChanged("InputString");
                    DisplayText = FormatText(inputString);

                    // Perhaps the delete button must be enabled/disabled.
                    ((Command)DeleteCharCommand).ChangeCanExecute();
                }
            }

            get { return inputString; }
        }

        public string DisplayText
        {
            protected set
            {
                if (displayText != value)
                {
                    displayText = value;
                    OnPropertyChanged("DisplayText");
                }
            }
            get { return displayText; }
        }

        // ICommand implementations
        public ICommand AddCharCommand { protected set; get; }

        public ICommand DeleteCharCommand { protected set; get; }

        string FormatText(string str)
        {
            bool hasNonNumbers = str.IndexOfAny(specialChars) != -1;
            string formatted = str;

            if (hasNonNumbers || str.Length < 4 || str.Length > 10)
            {
            }
            else if (str.Length < 8)
            {
                formatted = String.Format("{0}-{1}",
                                          str.Substring(0, 3),
                                          str.Substring(3));
            }
            else
            {
                formatted = String.Format("({0}) {1}-{2}",
                                          str.Substring(0, 3),
                                          str.Substring(3, 3),
                                          str.Substring(6));
            }
            return formatted;
        }

        protected void OnPropertyChanged(string propertyName)
        {
            PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
        }
    }
}
```

Предполагается, что это ViewModel `AddCharCommand` свойство привязано к `Command` свойство несколько кнопок (или все, что содержит интерфейс команды), каждый из которых определяется `CommandParameter`. Эти кнопки Добавить символов для `InputString` свойство, которое затем форматируется как номер телефона для `DisplayText` свойства.

Имеется также второе свойство типа `ICommand` с именем `DeleteCharCommand`. Привязывается к кнопке назад от пробельных, но кнопки следует отключить, если нет символов для удаления.

Следующие клавиатуре не является сложных как визуально, как он может быть. Вместо этого разметка было уменьшено до минимум для демонстрации более четко использования интерфейса командной:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.KeypadPage"
             Title="Keypad Page">

    <Grid HorizontalOptions="Center"
          VerticalOptions="Center">
        <Grid.BindingContext>
            <local:KeypadViewModel />
        </Grid.BindingContext>

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="80" />
            <ColumnDefinition Width="80" />
            <ColumnDefinition Width="80" />
        </Grid.ColumnDefinitions>

        <!-- Internal Grid for top row of items -->
        <Grid Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="3">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="Auto" />
            </Grid.ColumnDefinitions>

            <Frame Grid.Column="0"
                   OutlineColor="Accent">
                <Label Text="{Binding DisplayText}" />
            </Frame>

            <Button Text="&#x21E6;"
                    Command="{Binding DeleteCharCommand}"
                    Grid.Column="1"
                    BorderWidth="0" />
        </Grid>

        <Button Text="1"
                Command="{Binding AddCharCommand}"
                CommandParameter="1"
                Grid.Row="1" Grid.Column="0" />

        <Button Text="2"
                Command="{Binding AddCharCommand}"
                CommandParameter="2"
                Grid.Row="1" Grid.Column="1" />

        <Button Text="3"
                Command="{Binding AddCharCommand}"
                CommandParameter="3"
                Grid.Row="1" Grid.Column="2" />

        <Button Text="4"
                Command="{Binding AddCharCommand}"
                CommandParameter="4"
                Grid.Row="2" Grid.Column="0" />

        <Button Text="5"
                Command="{Binding AddCharCommand}"
                CommandParameter="5"
                Grid.Row="2" Grid.Column="1" />

        <Button Text="6"
                Command="{Binding AddCharCommand}"
                CommandParameter="6"
                Grid.Row="2" Grid.Column="2" />

        <Button Text="7"
                Command="{Binding AddCharCommand}"
                CommandParameter="7"
                Grid.Row="3" Grid.Column="0" />

        <Button Text="8"
                Command="{Binding AddCharCommand}"
                CommandParameter="8"
                Grid.Row="3" Grid.Column="1" />

        <Button Text="9"
                Command="{Binding AddCharCommand}"
                CommandParameter="9"
                Grid.Row="3" Grid.Column="2" />

        <Button Text="*"
                Command="{Binding AddCharCommand}"
                CommandParameter="*"
                Grid.Row="4" Grid.Column="0" />

        <Button Text="0"
                Command="{Binding AddCharCommand}"
                CommandParameter="0"
                Grid.Row="4" Grid.Column="1" />

        <Button Text="#"
                Command="{Binding AddCharCommand}"
                CommandParameter="#"
                Grid.Row="4" Grid.Column="2" />
    </Grid>
</ContentPage>
```

`Command` Свойства первого `Button` , которая отображается в этом разметки привязан к `DeleteCharCommand`; остальные привязаны к `AddCharCommand` с `CommandParameter` именно таким же, как символ, который отображается на `Button` начертания. Вот программы в действии:

[![](data-bindings-to-mvvm-images/keypad.png "Калькулятор с помощью команд и MVVM")](data-bindings-to-mvvm-images/keypad-large.png#lightbox "калькулятора с помощью команд и MVVM")

### <a name="invoking-asynchronous-methods"></a>Вызов асинхронных методов

Команды также можно вызывать асинхронные методы. Это достигается с помощью `async` и `await` ключевые слова при указании `Execute` метод:

```csharp
DownloadCommand = new Command (async () => await DownloadAsync ());
```

Это означает, что `DownloadAsync` метод `Task` и следует ожидать:

```csharp
async Task DownloadAsync ()
{
    await Task.Run (() => Download ());
}

void Download ()
{
    ...
}
```

## <a name="implementing-a-navigation-menu"></a>Реализация меню навигации

[XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/) программы, которая содержит исходный код в этой серии статей использует ViewModel для домашней страницы. Это ViewModel является определением короткий класса с тремя свойствами с именем `Type`, `Title`, и `Description` , содержащие типа для каждого из образцов страниц, название и краткое описание. Кроме того, ViewModel определяет статическое свойство с именем `All` , являющийся коллекцией всех страниц в программе:

```csharp
public class PageDataViewModel
{
    public PageDataViewModel(Type type, string title, string description)
    {
        Type = type;
        Title = title;
        Description = description;
    }

    public Type Type { private set; get; }

    public string Title { private set; get; }

    public string Description { private set; get; }

    static PageDataViewModel()
    {
        All = new List<PageDataViewModel>
        {
            // Part 1. Getting Started with XAML
            new PageDataViewModel(typeof(HelloXamlPage), "Hello, XAML",
                                  "Display a Label with many properties set"),

            new PageDataViewModel(typeof(XamlPlusCodePage), "XAML + Code",
                                  "Interact with a Slider and Button"),

            // Part 2. Essential XAML Syntax
            new PageDataViewModel(typeof(GridDemoPage), "Grid Demo",
                                  "Explore XAML syntax with the Grid"),

            new PageDataViewModel(typeof(AbsoluteDemoPage), "Absolute Demo",
                                  "Explore XAML syntax with AbsoluteLayout"),

            // Part 3. XAML Markup Extensions
            new PageDataViewModel(typeof(SharedResourcesPage), "Shared Resources",
                                  "Using resource dictionaries to share resources"),

            new PageDataViewModel(typeof(StaticConstantsPage), "Static Constants",
                                  "Using the x:Static markup extensions"),

            new PageDataViewModel(typeof(RelativeLayoutPage), "Relative Layout",
                                  "Explore XAML markup extensions"),

            // Part 4. Data Binding Basics
            new PageDataViewModel(typeof(SliderBindingsPage), "Slider Bindings",
                                  "Bind properties of two views on the page"),

            new PageDataViewModel(typeof(SliderTransformsPage), "Slider Transforms",
                                  "Use Sliders with reverse bindings"),

            new PageDataViewModel(typeof(ListViewDemoPage), "ListView Demo",
                                  "Use a ListView with data bindings"),

            // Part 5. From Data Bindings to MVVM
            new PageDataViewModel(typeof(OneShotDateTimePage), "One-Shot DateTime",
                                  "Obtain the current DateTime and display it"),

            new PageDataViewModel(typeof(ClockPage), "Clock",
                                  "Dynamically display the current time"),

            new PageDataViewModel(typeof(HslColorScrollPage), "HSL Color Scroll",
                                  "Use a view model to select HSL colors"),

            new PageDataViewModel(typeof(KeypadPage), "Keypad",
                                  "Use a view model for numeric keypad logic")
        };
    }

    public static IList<PageDataViewModel> All { private set; get; }
}
```

XAML-файл для `MainPage` определяет `ListBox` которого `ItemsSource` свойству, `All` свойства, содержащем `TextCell` для отображения `Title` и `Description` свойства каждой страницы:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.MainPage"
             Padding="5, 0"
             Title="XAML Samples">

    <ListView ItemsSource="{x:Static local:PageDataViewModel.All}"
              ItemSelected="OnListViewItemSelected">
        <ListView.ItemTemplate>
            <DataTemplate>
                <TextCell Text="{Binding Title}"
                          Detail="{Binding Description}" />
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>
```

Страницы отображаются в прокручиваемый список:

[![](data-bindings-to-mvvm-images/mainpage.png "Прокручиваемый список страниц")](data-bindings-to-mvvm-images/mainpage-large.png#lightbox "прокручиваемый список страниц")

Обработчик в файле кода активируется, когда пользователь выбирает элемент. Задает обработчик `SelectedItem` свойство `ListBox` к `null` создает выбранной страницы и переходит к нему:

```csharp
private async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs args)
{
    (sender as ListView).SelectedItem = null;

    if (args.SelectedItem != null)
    {
        PageDataViewModel pageData = args.SelectedItem as PageDataViewModel;
        Page page = (Page)Activator.CreateInstance(pageData.Type);
        await Navigation.PushAsync(page);
    }
}
```

## <a name="video"></a>Видео

> [!VIDEO https://youtube.com/embed/DYRLcqG2BAY]

**Xamarin развивать 2016: MVVM просто с Xamarin.Forms и призмы**

## <a name="summary"></a>Сводка

XAML является мощным средством для определения пользовательских интерфейсов в Xamarin.Forms приложения, особенно если привязка данных и MVVM используются. Результатом является представлением понятен, элегантный и потенциально допускает использование средств разработки пользовательского интерфейса с поддержкой всех фона в коде.


## <a name="related-links"></a>Связанные ссылки

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [Часть 1. Начало работы с XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Часть 2. Основной синтаксис XAML](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Часть 3. Расширения разметки XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Часть 4. Основы привязки данных](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
