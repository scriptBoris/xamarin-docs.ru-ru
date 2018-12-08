---
title: Часть 5. От привязки данных до MVVM
description: Шаблон MVVM обеспечивает разделение уровней программного обеспечения и три — пользовательский интерфейс XAML, именем представления. базовые данные, называется моделью; а посредником между представлением и моделью, называются ViewModel.
ms.prod: xamarin
ms.assetid: 48B37D44-4FB1-41B2-9A5E-6D383B041F81
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: c7bf7ca28200004e2383631c68cdaa4299348ecb
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53054037"
---
# <a name="part-5-from-data-bindings-to-mvvm"></a>Часть 5. От привязки данных до MVVM

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)

_Шаблон архитектуры Model-View-ViewModel (MVVM) была изобретена с XAML в виду. Шаблон обеспечивает разделение уровней программного обеспечения и три — пользовательский интерфейс XAML, именем представления. базовые данные, называется моделью; а посредником между представлением и моделью, называются ViewModel. View и ViewModel часто соединены через привязки данных, определенные в файле XAML. BindingContext для представления обычно является экземпляром ViewModel._

## <a name="a-simple-viewmodel"></a>Простой модели представления

Введение в классы ViewModel давайте сначала посмотрим программы без него.
Ранее вы узнали, как определить новое объявление пространства имен XML, чтобы разрешить файл XAML для ссылочных классов в других сборках. Вот это программа, которая определяет объявление пространства имен XML для `System` пространство имен:

```csharp
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

Программа может использовать `x:Static` получить текущую дату и время из статического `DateTime.Now` свойство и задайте их `DateTime` значение `BindingContext` на `StackLayout`:

```xaml
<StackLayout BindingContext="{x:Static sys:DateTime.Now}" …>
```

`BindingContext` представляет собой очень специальное свойство: при задании `BindingContext` на элементе, он наследует все дочерние элементы этого элемента. Это означает, что все дочерние `StackLayout` этот же `BindingContext`, и они могут содержать простые привязки к свойствам этого объекта.

В **One-Shot DateTime** программы, два дочерних содержат привязки к свойствам, `DateTime` значение, а два других дочерних элементов содержат привязок, которые отсутствуют пути привязки. Это означает, что `DateTime` само значение используется для `StringFormat`:

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

Конечно большая проблема состоит в дату и время, набор после сначала при построении страницы и никогда не изменяются:

[![](data-bindings-to-mvvm-images/oneshotdatetime.png "Представление, содержащее дату и время")](data-bindings-to-mvvm-images/oneshotdatetime-large.png#lightbox "представления, отображающее даты и времени")

Файл XAML можно отобразить, всегда показывает текущее время часов, но его необходимо написать код, чтобы оказать помощь. Когда мыслить категориями MVVM, модель и модель представления являются классами, написанных полностью на код. Это представление часто является файл XAML, который ссылается на свойства, определенные в модели представления через привязки данных.

Правильной модели неведении относительно того, ViewModel и правильный ViewModel неведении представления. Тем не менее очень часто программист применяет типы данных, предоставляемые ViewModel в типы данных, связанный с конкретной пользовательских интерфейсов. Например если модель обращается к базе данных, содержащий строки 8-битовых символов ASCII, ViewModel потребуется преобразование этих строк для строк Юникода в соответствии с их эксклюзивное использование Юникода в пользовательском интерфейсе.

В простые примеры MVVM (например, показано ниже) часто не существует модели и шаблон включает в себя только просматривать и ViewModel соединенные привязки данных.

Вот ViewModel для часы, с использованием только к одному свойству с именем `DateTime`, но обновлений, которые `DateTime` свойство каждую секунду:

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

Обычно реализуют классы ViewModel `INotifyPropertyChanged` интерфейс, который означает, что класс запускает `PropertyChanged` событие при изменении одного из его свойств. Механизм привязки данных в Xamarin.Forms присоединяет обработчик к этому `PropertyChanged` событий, поэтому он может получать уведомления об изменении свойства и сохранить целевой объект обновляется новое значение.

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

Обратите внимание, что как `ClockViewModel` присваивается `BindingContext` из `Label` с помощью тегов элементов свойства. Кроме того, можно создать экземпляр `ClockViewModel` в `Resources` коллекции и присвойте ему значение `BindingContext` через `StaticResource` расширение разметки. Кроме того, файл с выделенным кодом можно создавать экземпляры ViewModel.

`Binding` Расширение разметки на `Text` свойство `Label` форматы `DateTime` свойство. Здесь отображается по:

[![](data-bindings-to-mvvm-images/clock.png "Представление, содержащее дату и время с помощью ViewModel")](data-bindings-to-mvvm-images/clock-large.png#lightbox "представления, отображающее даты и времени с помощью модели представления")

Также возможен доступ к отдельным свойствам `DateTime` свойство ViewModel, разделив их свойства с периодами:

```xaml
<Label Text="{Binding DateTime.Second, StringFormat='{0}'}" … >
```

## <a name="interactive-mvvm"></a>Интерактивный MVVM

MVVM довольно часто используется с помощью двухсторонней привязки для это интерактивное представление на основе базовой модели данных.

Вот класс с именем `HslViewModel` , преобразующий `Color` значение в `Hue`, `Saturation`, и `Luminosity` значения и наоборот:

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

Изменения в `Hue`, `Saturation`, и `Luminosity` причина свойства `Color` изменение свойства, а также изменения `Color` вызывает три свойства для изменения. Это может показаться бесконечный цикл, за исключением того, что класс не вызвать `PropertyChanged` событие, если только свойство действительно были изменены. Это означает прекращение цикла в противном случае неконтролируемых обратной связи.

Следующий XAML-файл содержит `BoxView` которого `Color` свойство привязано к `Color` свойство ViewModel, а остальные три `Slider` и три `Label` представления привязан к `Hue`, `Saturation`и `Luminosity` свойства:

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

Привязка на каждом `Label` используется по умолчанию `OneWay`. Его достаточно для отображения значения. Однако привязки на каждом `Slider` является `TwoWay`. Это позволяет `Slider` инициализированную из ViewModel. Обратите внимание, что `Color` свойству `Aqua` при создании экземпляра модели представления. Но изменение `Slider` также необходимо задать новое значение для свойства в ViewModel, который затем вычисляет новый цвет.

[![](data-bindings-to-mvvm-images/hslcolorscroll.png "MVVM, с помощью двусторонней привязки данных")](data-bindings-to-mvvm-images/hslcolorscroll-large.png#lightbox "MVVM, с помощью двусторонней привязки данных")

## <a name="commanding-with-viewmodels"></a>Выполнение команд с помощью модели ViewModel

Во многих случаях шаблон MVVM ограничен манипуляции элементов данных: объекты пользовательского интерфейса в представлении параллельные объекты данных в модели представления.

Тем не менее иногда представления должен содержать кнопки, вызова различных действий в ViewModel. Но не может содержать ViewModel `Clicked` обработчики для кнопок, так как, будет привязана ViewModel парадигме конкретного пользовательского интерфейса.

Чтобы разрешить ViewModel, чтобы быть более независимой от объектов определенного пользовательского интерфейса, но по-прежнему допускает методов, вызываемых в ViewModel, *команда* интерфейс существует. Этот интерфейс командной поддерживает следующие элементы в Xamarin.Forms.

-  `Button`
-  `MenuItem`
-  `ToolbarItem`
-  `SearchBar`
-  `TextCell` (и, следовательно также `ImageCell`)
-  `ListView`
-  `TapGestureRecognizer`

За исключением элемента `SearchBar` и `ListView` элемента, эти элементы определяют два свойства:

-  `Command` типа  `System.Windows.Input.ICommand`
-  `CommandParameter` типа  `Object`

`SearchBar` Определяет `SearchCommand` и `SearchCommandParameter` свойства, хотя `ListView` определяет `RefreshCommand` свойство типа `ICommand`.

`ICommand` Интерфейс определяет два метода и одно событие:

-  `void Execute(object arg)`
-  `bool CanExecute(object arg)`
-  `event EventHandler CanExecuteChanged`

ViewModel можно определить свойства типа `ICommand`. Теперь можно привязать эти свойства, чтобы `Command` свойства каждого `Button` или другой элемент, или возможно пользовательские представления, которое реализует этот интерфейс. При необходимости можно задать `CommandParameter` свойство для идентификации отдельных `Button` объектов (или другие элементы), привязанные к этому свойству модели представления. На внутреннем уровне `Button` вызовы `Execute` метод всякий раз, когда пользователь касается `Button`, передав для `Execute` метод его `CommandParameter`.

`CanExecute` Метод и `CanExecuteChanged` событий используются для случаев, где `Button` tap может быть недопустимых в данный момент, в этом случае `Button` следует отключить сам. `Button` Вызовы `CanExecute` при `Command` сначала задано и в любое время `CanExecuteChanged` события. Если `CanExecute` возвращает `false`, `Button` отключается и не генерирует `Execute` вызовов.

Для справки при добавлении команды для своих ViewModel Xamarin.Forms определяет два класса, которые реализуют `ICommand`: `Command` и `Command<T>` где `T` — это тип аргументов `Execute` и `CanExecute`. Эти классы определяют несколько конструкторов, а также `ChangeCanExecute` метод, который можно вызвать ViewModel, чтобы принудительно `Command` объект, подлежащий `CanExecuteChanged` событий.

Вот ViewModel для простой клавиатуре, который предназначен для ввода номера телефона. Обратите внимание, что `Execute` и `CanExecute` метод определяются как лямбда-выражение прямо в конструкторе:

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

Предполагается, что этого ViewModel `AddCharCommand` свойство привязано к `Command` свойство несколько кнопок (или все, что содержит интерфейс команды), каждый из которых идентифицируется по `CommandParameter`. Эти кнопки позволяют добавлять символы для `InputString` свойство, которое затем форматируется как номер телефона для `DisplayText` свойство.

Имеется также свойство типа `ICommand` с именем `DeleteCharCommand`. Привязывается к кнопке назад пробелов, но кнопки следует отключить, если нет символов для удаления.

Следующие клавиатуре не является сложных как визуально, как хотелось бы. Вместо этого разметка разбивается на как минимум для демонстрации более четко использование интерфейса командной:

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

`Command` Свойства первого `Button` , отображаемый в этом разметки привязан к `DeleteCharCommand`; остальные привязаны к `AddCharCommand` с `CommandParameter` то есть так же, как символ, который отображается на `Button` лиц. Вот программа в действии:

[![](data-bindings-to-mvvm-images/keypad.png "Калькулятор, используя MVVM и команды")](data-bindings-to-mvvm-images/keypad-large.png#lightbox "калькулятора, с помощью команды и MVVM")

### <a name="invoking-asynchronous-methods"></a>Вызов асинхронных методов

Команды также можно вызывать асинхронные методы. Это достигается с помощью `async` и `await` ключевые слова, при указании `Execute` метод:

```csharp
DownloadCommand = new Command (async () => await DownloadAsync ());
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

[XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/) программа, которая содержит весь исходный код в этой серии статей использует модель представления для его домашней страницы. Этого ViewModel — это определение класса с коротким три свойства с именем `Type`, `Title`, и `Description` , содержащие тип каждого из образцов страниц, название и краткое описание. Кроме того, ViewModel определяет статическое свойство с именем `All` , являющийся коллекцией всех страниц в программе:

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

Файл XAML для `MainPage` определяет `ListBox` которого `ItemsSource` свойству, `All` свойство и который содержит `TextCell` для отображения `Title` и `Description` свойства каждой страницы:

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

Обработчик в файле кода активируется в том случае, когда пользователь выбирает элемент. Задает обработчик `SelectedItem` свойство `ListBox` обратно `null` и затем создает экземпляр выбранной страницы и переходу на нее:

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

**Конференция Xamarin Evolve 2016: MVVM, ставшая простой благодаря Xamarin.Forms и Prism**

## <a name="summary"></a>Сводка

XAML — это мощное средство для определения пользовательских интерфейсов приложений Xamarin.Forms, особенно если привязки данных и используются MVVM. Результат — это чистая, элегантные и потенциально оснащен инструментами, представление пользовательского интерфейса с поддержкой всех фона в коде.


## <a name="related-links"></a>Связанные ссылки

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [Часть 1. Начало работы с XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Часть 2. Основной синтаксис XAML](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Часть 3. Расширения разметки XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Часть 4. Основы привязки данных](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
