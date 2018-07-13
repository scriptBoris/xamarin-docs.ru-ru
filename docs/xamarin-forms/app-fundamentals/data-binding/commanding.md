---
title: Интерфейс командной Xamarin.Forms
description: В этой статье объясняется, как реализовать свойство Command с привязками данных Xamarin.Forms. Интерфейс команд предоставляет альтернативный подход к реализации команд, гораздо лучше подходят для архитектуре MVVM.
ms.prod: xamarin
ms.assetid: 69922284-F398-45C3-B4CC-B8E29BB4C533
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: b18d042e34146a72b488da9017648a430c9cd353
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996377"
---
# <a name="the-xamarinforms-command-interface"></a>Интерфейс командной Xamarin.Forms

Архитектуры Model-View-ViewModel (MVVM), определены привязки данных между свойствами в ViewModel, который обычно является класс, производный от `INotifyPropertyChanged`и свойства в представлении, который обычно является файл XAML. Иногда приложение имеет стоящие перед выходят за рамки этих привязок свойство, требуя пользователю запускать команды, влияющие на что-нибудь в ViewModel. Эти команды обычно обозначаются путем нажатия кнопки или пальца касания и обычно они обрабатываются в файле кода в обработчике `Clicked` событие `Button` или `Tapped` событие `TapGestureRecognizer`.

Интерфейс команд предоставляет альтернативный подход к реализации команд, гораздо лучше подходят для архитектуре MVVM. Самой модели представления могут содержать команды, которые представляют собой методы, которые выполняются в ответ на определенное действие в представлении, такие как `Button` нажмите кнопку. Между эти команды определены привязки данных и `Button`.

Чтобы разрешить привязку данных между `Button` и ViewModel, `Button` определяет два свойства:

- [`Command`](xref:Xamarin.Forms.Button.Command) типа <xref:System.Windows.Input.ICommand>
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) типа `Object`

Чтобы использовать интерфейс команды, определить привязку данных, предназначенный `Command` свойство `Button` где источник — свойства в ViewModel типа `ICommand`. ViewModel содержит код, связанный с этим `ICommand` свойство, которое выполняется при нажатии кнопки. Можно задать `CommandParameter` к произвольные данные, чтобы различать несколько кнопок, если все они привязаны к тому же `ICommand` свойства в ViewModel.

`Command` И `CommandParameter` свойства также определяются следующие классы:

- [`MenuItem`](xref:Xamarin.Forms.MenuItem) и, следовательно [ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem), который является производным от `MenuItem`
- [`TextCell`](xref:Xamarin.Forms.TextCell) и, следовательно [ `ImageCell` ](xref:Xamarin.Forms.ImageCell), который является производным от `TextCell`
- [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)

[`SearchBar`](xref:Xamarin.Forms.SearchBar) Определяет [ `SearchCommand` ](xref:Xamarin.Forms.SearchBar.SearchCommand) свойство типа `ICommand` и [ `SearchCommandParameter` ](xref:Xamarin.Forms.SearchBar.SearchCommandParameter) свойство. [ `RefreshCommand` ](xref:Xamarin.Forms.ListView.RefreshCommand) Свойство [ `ListView` ](xref:Xamarin.Forms.ListView) также является типом `ICommand`.

Все эти команды могут быть выполнены в ViewModel, таким образом, не зависят от конкретного пользовательского интерфейса объекта в представлении.

## <a name="the-icommand-interface"></a>Интерфейс ICommand

<xref:System.Windows.Input.ICommand> Интерфейс не является частью Xamarin.Forms. Вместо этого он определен в [System.Windows.Input](xref:System.Windows.Input) пространства имен и состоит из двух методов и одно событие:

```csharp
public interface ICommand
{
    public void Execute (Object parameter);

    public bool CanExecute (Object parameter);

    public event EventHandler CanExecuteChanged;
}
```

Чтобы использовать интерфейс командной, вашей ViewModel содержит свойства типа `ICommand`:

```csharp
public ICommand MyCommand { private set; get; }
```

ViewModel также должен ссылаться на класс, реализующий `ICommand` интерфейс. Этот класс будет описано позже. В представлении `Command` свойство `Button` привязан к этому свойству:

```xaml
<Button Text="Execute command"
        Command="{Binding MyCommand}" />
```

Когда пользователь нажимает `Button`, `Button` вызовы `Execute` метод в `ICommand` объект привязан к его `Command` свойство. Это самый простой частью команд интерфейса.

`CanExecute` Метод более сложен. При привязке сначала определяются на `Command` свойство `Button`, и при изменении привязки данных каким-либо образом `Button` вызовы `CanExecute` метод в `ICommand` объекта. Если `CanExecute` возвращает `false`, а затем `Button` отключается. Это означает, что определенный команда в данный момент недоступен или недопустим.

`Button` Также подключает обработчик на `CanExecuteChanged` событие `ICommand`. Событие из в ViewModel. При возникновении этого события, `Button` вызовы `CanExecute` еще раз. `Button` Включает сам, если `CanExecute` возвращает `true` и отключается, если `CanExecute` возвращает `false`.

> [!IMPORTANT]
> Не используйте `IsEnabled` свойство `Button` при использовании интерфейса командной.  

## <a name="the-command-class"></a>Класс команд

Если ваш ViewModel определяет свойство типа `ICommand`, ViewModel должно также содержать или ссылки на класс, реализующий `ICommand` интерфейс. Этот класс должен содержать или ссылаться на `Execute` и `CanExecute` методы и fire `CanExecuteChanged` событие каждый раз, когда `CanExecute` метод может возвращать другое значение.

Можно написать такой класс самостоятельно, или можно использовать класс, который кто-то другой записан. Так как `ICommand` входит из Microsoft Windows, он используется для лет с приложениями Windows MVVM. С помощью класса Windows, который реализует `ICommand` позволяет совместно использовать ваши классы ViewModel между приложениями Windows и приложений Xamarin.Forms.

Если общий доступ к модели ViewModel между Windows и Xamarin.Forms не имеет значения, то можно использовать [ `Command` ](xref:Xamarin.Forms.Command) или [ `Command<T>` ](xref:Xamarin.Forms.Command`1) классов, включенных в Xamarin.Forms для реализации `ICommand`интерфейс. Эти классы позволяют указать тела `Execute` и `CanExecute` методы в конструкторах классов. Используйте `Command<T>` при использовании `CommandParameter` различать несколько представлений, привязанное к тому же `ICommand` свойство и более простой `Command` класса, не является обязательным требованием.

## <a name="basic-commanding"></a>Основные команды

**Запись Person** странице в [ **демонстрации привязки данных** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) программа демонстрирует некоторые простые команды, реализованные в ViewModel.

`PersonViewModel` Определены три свойства с именем `Name`, `Age`, и `Skills` , которые определяют человека. Этот класс делает *не* содержать `ICommand` свойства:

```csharp
public class PersonViewModel : INotifyPropertyChanged
{
    string name;
    double age;
    string skills;

    public event PropertyChangedEventHandler PropertyChanged;

    public string Name
    {
        set { SetProperty(ref name, value); }
        get { return name; }
    }

    public double Age
    {
        set { SetProperty(ref age, value); }
        get { return age; }
    }

    public string Skills
    {
        set { SetProperty(ref skills, value); }
        get { return skills; }
    }

    public override string ToString()
    {
        return Name + ", age " + Age;
    }

    bool SetProperty<T>(ref T storage, T value, [CallerMemberName] string propertyName = null)
    {
        if (Object.Equals(storage, value))
            return false;

        storage = value;
        OnPropertyChanged(propertyName);
        return true;
    }

    protected void OnPropertyChanged([CallerMemberName] string propertyName = null)
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }
}
```

`PersonCollectionViewModel` Показанный ниже создает объекты типа `PersonViewModel` и позволяет пользователю вводить данные. Для этой цели класс определяет свойства `IsEditing` типа `bool` и `PersonEdit` типа `PersonViewModel`. Кроме того, класс определяет три свойства типа `ICommand` и свойство с именем `Persons` типа `IList<PersonViewModel>`:

```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{
    PersonViewModel personEdit;
    bool isEditing;

    public event PropertyChangedEventHandler PropertyChanged;

    ···

    public bool IsEditing
    {
        private set { SetProperty(ref isEditing, value); }
        get { return isEditing; }
    }

    public PersonViewModel PersonEdit
    {
        set { SetProperty(ref personEdit, value); }
        get { return personEdit; }
    }

    public ICommand NewCommand { private set; get; }

    public ICommand SubmitCommand { private set; get; }

    public ICommand CancelCommand { private set; get; }

    public IList<PersonViewModel> Persons { get; } = new ObservableCollection<PersonViewModel>();

    bool SetProperty<T>(ref T storage, T value, [CallerMemberName] string propertyName = null)
    {
        if (Object.Equals(storage, value))
            return false;

        storage = value;
        OnPropertyChanged(propertyName);
        return true;
    }

    protected void OnPropertyChanged([CallerMemberName] string propertyName = null)
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }
}
```

Это сокращенный список не включены в конструктор класса, что выгодно трех свойств типа `ICommand` определены, которой будет отображаться чуть ниже. Обратите внимание, что изменения в трех свойств типа `ICommand` и `Persons` свойство не приводят к `PropertyChanged` событиями, порождаемыми. Эти свойства задаются при создании класса и не изменяйте соответственно.

Перед изучением конструктор `PersonCollectionViewModel` класса, давайте взглянем на файл XAML для **запись Person** программы. Этот файл содержит `Grid` с его `BindingContext` свойство значение `PersonCollectionViewModel`. `Grid` Содержит `Button` с текстом **New** с его `Command` , привязанное к `NewCommand` в ViewModel, форма ввода со свойствами, привязанное к `IsEditing` свойства, как а также свойства `PersonViewModel`, а еще две кнопки к `SubmitCommand` и `CancelCommand` свойства ViewModel. Конечный `ListView` отображается коллекция уже введены лиц:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.PersonEntryPage"
             Title="Person Entry">
    <Grid Margin="10">
        <Grid.BindingContext>
            <local:PersonCollectionViewModel />
        </Grid.BindingContext>

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <!-- New Button -->
        <Button Text="New"
                Grid.Row="0"
                Command="{Binding NewCommand}"
                HorizontalOptions="Start" />

        <!-- Entry Form -->
        <Grid Grid.Row="1"
              IsEnabled="{Binding IsEditing}">

            <Grid BindingContext="{Binding PersonEdit}">
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>

                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>

                <Label Text="Name: " Grid.Row="0" Grid.Column="0" />
                <Entry Text="{Binding Name}"
                       Grid.Row="0" Grid.Column="1" />

                <Label Text="Age: " Grid.Row="1" Grid.Column="0" />
                <StackLayout Orientation="Horizontal"
                             Grid.Row="1" Grid.Column="1">
                    <Stepper Value="{Binding Age}"
                             Maximum="100" />
                    <Label Text="{Binding Age, StringFormat='{0} years old'}"
                           VerticalOptions="Center" />
                </StackLayout>

                <Label Text="Skills: " Grid.Row="2" Grid.Column="0" />
                <Entry Text="{Binding Skills}"
                       Grid.Row="2" Grid.Column="1" />

            </Grid>
        </Grid>

        <!-- Submit and Cancel Buttons -->
        <Grid Grid.Row="2">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>

            <Button Text="Submit"
                    Grid.Column="0"
                    Command="{Binding SubmitCommand}"
                    VerticalOptions="CenterAndExpand" />

            <Button Text="Cancel"
                    Grid.Column="1"
                    Command="{Binding CancelCommand}"
                    VerticalOptions="CenterAndExpand" />
        </Grid>

        <!-- List of Persons -->
        <ListView Grid.Row="3"
                  ItemsSource="{Binding Persons}" />
    </Grid>
</ContentPage>
```

Вот как это работает: первый пользователь нажимает **New** кнопки. Это позволяет форме ввода, но отключает **New** кнопки. Пользователь вводит имя, возраст и навыки. В любое время, во время редактирования, пользователь может нажать **отменить** кнопку, чтобы начать заново. Только если были введены имя и допустимый возраст **отправить** кнопки включено. Нажав клавишу это **отправить** передача пользователя в коллекцию, отображаемого элементом `ListView`. После того, как **отменить** или **отправить** нажатии кнопки, форма ввода очищается и **New** включена кнопка.

На экране iOS слева показан макет до ввода допустимый возраст. Android и UWP экраны show **отправить** кнопки включено после задания возраст:

[![Запись пользователя](commanding-images/personentry-small.png "запись Person")](commanding-images/personentry-large.png#lightbox "запись пользователя")

Программа не поддерживает любые средства для редактирования существующих записей и не сохраняет записи при уходе страницы.

Вся логика для **New**, **отправить**, и **отменить** кнопки обрабатывается в `PersonCollectionViewModel` посредством определения `NewCommand`, `SubmitCommand`, и `CancelCommand` свойства. Конструктор `PersonCollectionViewModel` задает эти три свойства к объектам типа `Command`.  

Объект [конструктор](xref:Xamarin.Forms.Command.%23ctor(System.Action,System.Func{System.Boolean})) из `Command` класс позволяет передать аргументы типа `Action` и `Func<bool>` соответствующий `Execute` и `CanExecute` методы. Проще всего определить эти действия и функции как лямбда-выражения прямо в `Command` конструктор. Вот определение `Command` для объекта `NewCommand` свойство:

```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{

    ···

    public PersonCollectionViewModel()
    {
        NewCommand = new Command(
            execute: () =>
            {
                PersonEdit = new PersonViewModel();
                PersonEdit.PropertyChanged += OnPersonEditPropertyChanged;
                IsEditing = true;
                RefreshCanExecutes();
            },
            canExecute: () =>
            {
                return !IsEditing;
            });

        ···

    }

    void OnPersonEditPropertyChanged(object sender, PropertyChangedEventArgs args)
    {
        (SubmitCommand as Command).ChangeCanExecute();
    }

    void RefreshCanExecutes()
    {
        (NewCommand as Command).ChangeCanExecute();
        (SubmitCommand as Command).ChangeCanExecute();
        (CancelCommand as Command).ChangeCanExecute();
    }

    ···

}
```

Когда пользователь щелкает **New** кнопки `execute` передается функции `Command` конструктор выполняется. При этом создается новый `PersonViewModel` объекта, задает обработчик для этого объекта `PropertyChanged` задает событие, `IsEditing` для `true`и вызывает `RefreshCanExecutes` метода, определенного после конструктора.

Помимо реализации `ICommand` интерфейс, `Command` класс также определяет метод с именем `ChangeCanExecute`. К ViewModel должен вызывать `ChangeCanExecute` для `ICommand` свойство всякий раз, когда что-то случится, может измениться значение, возвращаемое `CanExecute` метод. Вызов `ChangeCanExecute` вызывает `Command` класс срабатывание `CanExecuteChanged` метод. `Button` Присоединил обработчик для этого события и отвечает вызовом `CanExecute` еще раз и включить само по себе основано на возвращаемое значение этого метода.

При `execute` метод `NewCommand` вызовы `RefreshCanExecutes`, `NewCommand` свойство получает вызов `ChangeCanExecute`и `Button` вызовы `canExecute` метод, который теперь возвращает `false` поскольку `IsEditing`свойство больше не `true`.

`PropertyChanged` Обработчик для нового `PersonViewModel` вызывает `ChangeCanExecute` метод `SubmitCommand`. Вот, как реализуется, свойство command:


```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{

    ···

    public PersonCollectionViewModel()
    {

        ···

        SubmitCommand = new Command(
            execute: () =>
            {
                Persons.Add(PersonEdit);
                PersonEdit.PropertyChanged -= OnPersonEditPropertyChanged;
                PersonEdit = null;
                IsEditing = false;
                RefreshCanExecutes();
            },
            canExecute: () =>
            {
                return PersonEdit != null &&
                       PersonEdit.Name != null &&
                       PersonEdit.Name.Length > 1 &&
                       PersonEdit.Age > 0;
            });

        ···
    }

    ···

}
```

`canExecute` Работать для `SubmitCommand` вызывается каждый раз при изменения в свойства `PersonViewModel` объект редактируется. Он возвращает `true` только тогда, когда `Name` свойство имеет по крайней мере один символ и `Age` больше 0. В этот момент **отправить** становится доступной кнопка.

`execute` Работать для **отправить** удаляет обработчиком property-changed из `PersonViewModel`, добавляет объект в `Persons` коллекции и возвращает все, что начальное условие.

`execute` Работать для **отменить** кнопку поддерживает все возможности **отправить** execept делает кнопка Добавить объект в коллекцию:

```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{

    ···

    public PersonCollectionViewModel()
    {

        ···

        CancelCommand = new Command(
            execute: () =>
            {
                PersonEdit.PropertyChanged -= OnPersonEditPropertyChanged;
                PersonEdit = null;
                IsEditing = false;
                RefreshCanExecutes();
            },
            canExecute: () =>
            {
                return IsEditing;
            });
    }

    ···

}
```

`canExecute` Возвращает `true` в любое время `PersonViewModel` редактируется.

Эти методы можно адаптировать для более сложных сценариев: свойство в `PersonCollectionViewModel` может быть привязан к `SelectedItem` свойство `ListView` для редактирования существующих элементов и **удалить** удалось добавить кнопку для удаления Эти элементы.

Это необязательно определить `execute` и `canExecute` методы, как лямбда-функции. Можно написать их как обычные закрытых методов в ViewModel и ссылаться на них в `Command` конструкторы. Тем не менее этот подход обычно приводить к появлению множества методов, на которые ссылаются только один раз в ViewModel.

## <a name="using-command-parameters"></a>С помощью параметров команды

Иногда бывает удобно для одной или нескольких кнопок (или другие объекты пользовательского интерфейса) для одного и того же `ICommand` свойства в ViewModel. В этом случае используется `CommandParameter` свойство, чтобы различать между кнопками.

Вы можете продолжать использовать `Command` класс для такие Общие `ICommand` свойства. Этот класс определяет [альтернативный конструктор](xref:Xamarin.Forms.Command.%23ctor(System.Action{System.Object},System.Func{System.Object,System.Boolean})) , принимающий `execute` и `canExecute` методов с параметрами типа `Object`. Это как `CommandParameter` передается этих методов.

Тем не менее при использовании `CommandParameter`, проще всего использовать универсальный [ `Command<T>` ](xref:Xamarin.Forms.Command`1) класс, чтобы указать тип объекта, значение `CommandParameter`. `execute` И `canExecute` методы, указываемые имеют параметры этого типа.

**Десятичное клавиатуры** страницы демонстрирующий этот способ, показывая, как реализовать клавиатуру для ввода десятичных чисел. `BindingContext` Для `Grid` является `DecimalKeypadViewModel`. `Entry` Связано свойство этого ViewModel `Text` свойство `Label`. Все `Button` объекты привязаны к различным командам в ViewModel: `ClearCommand`, `BackspaceCommand`, и `DigitCommand`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.DecimalKeypadPage"
             Title="Decimal Keyboard">

    <Grid WidthRequest="240"
          HeightRequest="480"
          ColumnSpacing="2"
          RowSpacing="2"
          HorizontalOptions="Center"
          VerticalOptions="Center">

        <Grid.BindingContext>
            <local:DecimalKeypadViewModel />
        </Grid.BindingContext>

        <Grid.Resources>
            <ResourceDictionary>
                <Style TargetType="Button">
                    <Setter Property="FontSize" Value="32" />
                    <Setter Property="BorderWidth" Value="1" />
                    <Setter Property="BorderColor" Value="Black" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Label Text="{Binding Entry}"
               Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="3"
               FontSize="32"
               LineBreakMode="HeadTruncation"
               VerticalTextAlignment="Center"
               HorizontalTextAlignment="End" />

        <Button Text="CLEAR"
                Grid.Row="1" Grid.Column="0" Grid.ColumnSpan="2"
                Command="{Binding ClearCommand}" />

        <Button Text="&#x21E6;"
                Grid.Row="1" Grid.Column="2"
                Command="{Binding BackspaceCommand}" />

        <Button Text="7"
                Grid.Row="2" Grid.Column="0"
                Command="{Binding DigitCommand}"
                CommandParameter="7" />

        <Button Text="8"
                Grid.Row="2" Grid.Column="1"
                Command="{Binding DigitCommand}"
                CommandParameter="8" />

        <Button Text="9"
                Grid.Row="2" Grid.Column="2"
                Command="{Binding DigitCommand}"
                CommandParameter="9" />

        <Button Text="4"
                Grid.Row="3" Grid.Column="0"
                Command="{Binding DigitCommand}"
                CommandParameter="4" />

        <Button Text="5"
                Grid.Row="3" Grid.Column="1"
                Command="{Binding DigitCommand}"
                CommandParameter="5" />

        <Button Text="6"
                Grid.Row="3" Grid.Column="2"
                Command="{Binding DigitCommand}"
                CommandParameter="6" />

        <Button Text="1"
                Grid.Row="4" Grid.Column="0"
                Command="{Binding DigitCommand}"
                CommandParameter="1" />

        <Button Text="2"
                Grid.Row="4" Grid.Column="1"
                Command="{Binding DigitCommand}"
                CommandParameter="2" />

        <Button Text="3"
                Grid.Row="4" Grid.Column="2"
                Command="{Binding DigitCommand}"
                CommandParameter="3" />

        <Button Text="0"
                Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2"
                Command="{Binding DigitCommand}"
                CommandParameter="0" />

        <Button Text="&#x00B7;"
                Grid.Row="5" Grid.Column="2"
                Command="{Binding DigitCommand}"
                CommandParameter="." />
    </Grid>
</ContentPage>
```

11 кнопки для 10 разрядов и десятичной запятой, имеют общую привязку к `DigitCommand`. `CommandParameter` Различает эти кнопки. Значения, заданного для `CommandParameter` обычно является таким же, как текст, отображаемый на кнопке, за исключением десятичного разделителя, который для большей ясности будет отображаться с символом средней точки.

Вот программа в действии:

[![Decimal клавиатуры](commanding-images/decimalkeyboard-small.png "десятичное клавиатуры")](commanding-images/decimalkeyboard-large.png#lightbox "десятичное клавиатуры")

Обратите внимание на то, что кнопки для десятичного разделителя в все три снимка экранов отключено, поскольку уже содержат десятичный разделитель введенного числа.

`DecimalKeypadViewModel` Определяет `Entry` свойство типа `string` (который является единственным свойством, которое активирует `PropertyChanged` событий) и трех свойств типа `ICommand`:

```csharp
public class DecimalKeypadViewModel : INotifyPropertyChanged
{
    string entry = "0";

    public event PropertyChangedEventHandler PropertyChanged;

    ···

    public string Entry
    {
        private set
        {
            if (entry != value)
            {
                entry = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Entry"));
            }
        }
        get
        {
            return entry;
        }
    }

    public ICommand ClearCommand { private set; get; }

    public ICommand BackspaceCommand { private set; get; }

    public ICommand DigitCommand { private set; get; }
}
```

Кнопка, соответствующий `ClearCommand` всегда включена и просто задает запись значение «0»:

```csharp
public class DecimalKeypadViewModel : INotifyPropertyChanged
{

    ···

    public DecimalKeypadViewModel()
    {
        ClearCommand = new Command(
            execute: () =>
            {
                Entry = "0";
                RefreshCanExecutes();
            });

        ···

    }

    void RefreshCanExecutes()
    {
        ((Command)BackspaceCommand).ChangeCanExecute();
        ((Command)DigitCommand).ChangeCanExecute();
    }

    ···

}
```

Поскольку кнопки всегда включена, не требуется указывать `canExecute` аргумента в `Command` конструктор.

Логику для ввода чисел и пробела является чуть сложнее, так как если были введены без знаков, а затем `Entry` свойство представляет собой строку «0». Если пользователь вводит дополнительные нули, а затем `Entry` по-прежнему содержит только один ноль. Если пользователь вводит любые другие цифры, то эта цифра заменяет ноль. Но если пользователь вводит десятичной запятой, прежде чем любые другие цифры, затем `Entry` «0». строка.

**Backspace** кнопка доступна только в том случае, если длина записи больше 1 или если `Entry` не равно строку «0»:

```csharp
public class DecimalKeypadViewModel : INotifyPropertyChanged
{

    ···

    public DecimalKeypadViewModel()
    {

        ···

        BackspaceCommand = new Command(
            execute: () =>
            {
                Entry = Entry.Substring(0, Entry.Length - 1);
                if (Entry == "")
                {
                    Entry = "0";
                }
                RefreshCanExecutes();
            },
            canExecute: () =>
            {
                return Entry.Length > 1 || Entry != "0";
            });

        ···

    }

    ···

}
```

Логика `execute` работать для **Backspace** кнопку гарантирует, что `Entry` — по крайней мере это строка «0».

`DigitCommand` Свойство привязано к 11 кнопок, каждый из которых определяет себя с `CommandParameter` свойство. `DigitCommand` Может быть присвоено экземпляра стандартные `Command` класса, но проще использовать `Command<T>` универсального класса. При использовании команд интерфейса с помощью XAML, `CommandParameter` свойства обычно являются строками, и этот тип универсального аргумента. `execute` И `canExecute` затем имеют аргументы типа `string`:

```csharp
public class DecimalKeypadViewModel : INotifyPropertyChanged
{

    ···

    public DecimalKeypadViewModel()
    {

        ···

        DigitCommand = new Command<string>(
            execute: (string arg) =>
            {
                Entry += arg;
                if (Entry.StartsWith("0") && !Entry.StartsWith("0."))
                {
                    Entry = Entry.Substring(1);
                }
                RefreshCanExecutes();
            },
            canExecute: (string arg) =>
            {
                return !(arg == "." && Entry.Contains("."));
            });
    }

    ···

}
```

`execute` Метод добавляет строковый аргумент `Entry` свойство. Тем не менее, если результат начинается с 0 (но не ноль и десятичной запятой) затем этого начального нуля необходимо удалить с помощью `Substring` функции.

`canExecute` Возвращает `false` только в том случае, если аргумент является десятичной запятой (что означает, что десятичной запятой нажата) и `Entry` уже содержит десятичной запятой.

Все `execute` вызов методов `RefreshCanExecutes`, который затем вызывает `ChangeCanExecute` для обоих `DigitCommand` и `ClearCommand`. Это гарантирует, что включены десятичной запятой и кнопки backspace или отключаются в соответствии текущей последовательности введенных цифр.

## <a name="adding-commands-to-existing-views"></a>Добавление команд на существующие представления

Если вы хотите использовать интерфейс команд с представлениями, которые не поддерживают его, можно использовать поведение Xamarin.Forms, которое преобразует событие в команду. Это описано в статье [ **EventToCommandBehavior для повторного использования**](~/xamarin-forms/app-fundamentals/behaviors/reusable/event-to-command-behavior.md).

## <a name="asynchronous-commanding-for-navigation-menus"></a>Асинхронные команды меню навигации

Команды удобно для реализации меню навигации, подобный показанному на [ **демонстрации привязки данных** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) программах. Вот часть **MainPage.xaml**:


```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.MainPage"
             Title="Data Binding Demos"
             Padding="10">
    <TableView Intent="Menu">
        <TableRoot>
            <TableSection Title="Basic Bindings">

                <TextCell Text="Basic Code Binding"
                          Detail="Define a data-binding in code"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:BasicCodeBindingPage}" />

                <TextCell Text="Basic XAML Binding"
                          Detail="Define a data-binding in XAML"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:BasicXamlBindingPage}" />

                <TextCell Text="Alternative Code Binding"
                          Detail="Define a data-binding in code without a BindingContext"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:AlternativeCodeBindingPage}" />

                ···

            </TableSection>
        </TableRoot>
    </TableView>
</ContentPage>
```

При использовании команды с XAML, `CommandParameter` обычно свойств в строки. В этом случае, однако используется расширение разметки XAML, чтобы `CommandParameter` имеет тип `System.Type`.

Каждый `Command` свойство привязано к свойству с именем `NavigateCommand`. Что свойство определено в файле кода **MainPage.xaml.cs**:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();

        NavigateCommand = new Command<Type>(
            async (Type pageType) =>
            {
                Page page = (Page)Activator.CreateInstance(pageType);
                await Navigation.PushAsync(page);
            });

        BindingContext = this;
    }

    public ICommand NavigateCommand { private set; get; }
}
```

Конструктор присваивает `NavigateCommand` свойства `execute` метод, создающий экземпляр `System.Type` параметра, а затем переходит к нему. Так как `PushAsync` вызова требуется `await` оператор, `execute` метод должен быть помечен как асинхронные. Это осуществляется с помощью `async` ключевое слово перед списком параметров.

Конструктор также задает `BindingContext` страницы на себя таким образом, справочник по привязкам `NavigateCommand` в этом классе.

Порядок код в этот конструктор устанавливает различие: `InitializeComponent` вызов приводит к XAML, который необходимо проанализировать, но в этот момент привязка к свойству с именем `NavigateCommand` не может быть разрешен, так как `BindingContext` присваивается `null`. Если `BindingContext` задается в конструкторе *перед* `NavigateCommand` имеет значение, то привязка может быть разрешена, когда `BindingContext` имеет значение, но в этот момент `NavigateCommand` по-прежнему `null`. Установка `NavigateCommand` после `BindingContext` не имеет смысла в привязке, так как изменение `NavigateCommand` не срабатывает `PropertyChanged` событий и привязки не знает, `NavigateCommand` теперь считается допустимым.

Установка `NavigateCommand` и `BindingContext` (в любом порядке) до вызова `InitializeComponent` будет работать, так как оба компонента привязки задаются в том случае, когда средство синтаксического анализа XAML обнаруживает определении привязки.

Привязки данных, иногда бывает непросто, но как вы убедились в этой серии статей, они являются мощным и гибким и сильно помочь, чтобы упорядочить код путем отделение основной логики из пользовательского интерфейса.



## <a name="related-links"></a>Связанные ссылки

- [Демонстрации, привязка данных (пример)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Данные привязки Глава из книги по Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter18.md)
