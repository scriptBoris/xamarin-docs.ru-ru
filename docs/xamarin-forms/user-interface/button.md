---
title: Кнопка Xamarin.Forms
description: Кнопки отвечает касания или щелчка, который направляет приложения для выполнения определенной задачи.
ms.prod: xamarin
ms.assetid: 62CAEB63-0800-44F4-9B8C-EE632138C2F5
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 06/01/2018
ms.openlocfilehash: d74e0b2aa5be6e8eee2ce5cb54572dd4113d4d7d
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35244920"
---
# <a name="xamarinforms-button"></a>Кнопка Xamarin.Forms

_Кнопки отвечает касания или щелчка, который направляет приложения для выполнения определенной задачи._

[ `Button` ](xref:Xamarin.Forms.Button) Является наиболее фундаментальных интерактивного управления во всех Xamarin.Forms. `Button` Обычно отображает Короткая текстовая строка, указывающее, команды, но его можно также, чтобы отобразить растровое изображение, или сочетание текста и изображения. Пользователь нажимает `Button` пальцем или его щелкает мышью для запуска этой команды.

Большинство разделов, в котором рассказывается ниже соответствует страниц в [ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos) образца.

## <a name="handling-button-clicks"></a>Обработка кнопки щелкает

`Button` Определяет [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) событие, возникающее, когда пользователь касается `Button` с указателем мыши или пальца. Событие при отпускании кнопки мыши или пальца поверхности от `Button`. `Button` Должен иметь его [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) свойство `true` для него реагировать на нажатия.

**Основные нажатие кнопки** страницы в [ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos) образце показано, как создать экземпляр `Button` в XAML и обработки его `Clicked` событий. **BasicButtonClickPage.xaml** файл содержит `StackLayout` с обоими `Label` и `Button`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.BasicButtonClickPage"
             Title="Basic Button Click">
    <StackLayout>

        <Label x:Name="label"
               Text="Click the Button below"
               FontSize="Large"
               VerticalOptions="CenterAndExpand"
               HorizontalOptions="Center" />

        <Button Text="Click to Rotate Text!"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Clicked="OnButtonClicked" />

    </StackLayout>
</ContentPage>
```

`Button` , Как правило, занимают все пространство, разрешенное для него. Например, если вы не задали `HorizontalOptions` свойство `Button` на что-то отличное от `Fill`, `Button` займет всю ширину родительского элемента.

По умолчанию `Button` прямоугольное, но вы предоставляете ИТ скругленные углы, с помощью [ `CornerRadius` ](xref:Xamarin.Forms.Button.CornerRadius) свойства, как описано ниже в разделе [ **внешний вид кнопки** ](#button-appearance).

[ `Text` ](xref:Xamarin.Forms.Button.Text) Свойство задает текст, отображаемый в `Button`. [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) Событие равно обработчик событий с именем `OnButtonClicked`. Этот обработчик находится в файле кода **BasicButtonClickPage.xaml.cs**:

```csharp
public partial class BasicButtonClickPage : ContentPage
{
    public BasicButtonClickPage ()
    {
        InitializeComponent ();
    }

    async void OnButtonClicked(object sender, EventArgs args)
    {
        await label.RelRotateTo(360, 1000);
    }
}
```

Когда `Button` выполняется отвод `OnButtonClicked` выполнения метода. `sender` Аргумент `Button` объекта инициатором этого события. Это можно использовать для доступа к `Button` объекта, либо для различения нескольких `Button` объектов с одинаковым `Clicked` событий.

Данный конкретный `Clicked` обработчик вызывает функцию анимации, которая вращается `Label` 360 градусов в 1000 миллисекунд. Вот программу на устройствах iOS и Android, а в приложении универсальной платформы Windows (UWP) на рабочем столе Windows 10.

[![Кнопка «основные» щелкните](button-images/BasicButtonClick.png "нажатия кнопки основные")](button-images/BasicButtonClick-Large.png#lightbox "нажатия основные кнопки")

Обратите внимание, что `OnButtonClicked` метод включает `async` модификатор из-за `await` используется в обработчике событий. Объект `Clicked` требует обработчика событий `async` модификатор только в том случае, если в теле обработчика используется `await`.

Подготавливает к просмотру каждой платформы `Button` в свой собственный особым образом. В [ **внешний вид кнопки** ](#button-appearance) раздел, вы увидите, как задать цвета и сделать `Button` видимые для более специализированного внешний вид границы. `Button` реализует [ `IFontElement` ](xref:Xamarin.Forms.Internals.IFontElement) интерфейс, включив в него [ `FontFamily` ](xref:Xamarin.Forms.Button.FontFamily), [ `FontSize` ](xref:Xamarin.Forms.Button.FontSize), и [ `FontAttributes` ](xref:Xamarin.Forms.Button.FontAttributes)свойства.

## <a name="creating-a-button-in-code"></a>Создание кнопки в коде

Обычно для создания экземпляра `Button` в XAML, но можно также создать `Button` в коде. Это может быть удобным, если приложению необходимо создать несколько кнопок на основе данных, с перечислимой `foreach` цикла.

**Нажатии кнопки код** страницы показано, как создать страницу, которая является функциональным эквивалентом **основные нажатие кнопки** страницы, но полностью на языке C#:

```csharp
public class CodeButtonClickPage : ContentPage
{
    public CodeButtonClickPage ()
    {
        Title = "Code Button Click";

        Label label = new Label
        {
            Text = "Click the Button below",
            FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)),
            VerticalOptions = LayoutOptions.CenterAndExpand,
            HorizontalOptions = LayoutOptions.Center
        };

        Button button = new Button
        {
            Text = "Click to Rotate Text!",
            VerticalOptions = LayoutOptions.CenterAndExpand,
            HorizontalOptions = LayoutOptions.Center
        };
        button.Clicked += async (sender, args) => await label.RelRotateTo(360, 1000);

        Content = new StackLayout
        {
            Children =
            {
                label,
                button
            }
        };
    }
}
```

Все, что можно сделать в конструктор класса. Поскольку `Clicked` обработчик только одна инструкция long, она может быть присоединена к событию очень просто:

```csharp
button.Clicked += async (sender, args) => await label.RelRotateTo(360, 1000);
```

Конечно, также можно определить обработчик событий как отдельный метод (так же, как `OnButtonClick` метод в **основные нажатие кнопки**) и присоединить к событию, метод:

```csharp
button.Clicked += OnButtonClicked;
```

## <a name="disabling-the-button"></a>Отключение кнопки

Иногда приложение — в определенном состоянии, где определенной `Button` щелкните не является недопустимой операцией. В таких случаях `Button` следует отключить, присвоив его `IsEnabled` свойства `false`. Классический пример — `Entry` управления для имени файла, сопровождается Открытие файла `Button`: `Button` должно быть включено только в том случае, если было введено некоторый текст в `Entry`.
Можно использовать `DataTrigger` для этой задачи, как показано в [ **триггеры данных** ](~/xamarin-forms/app-fundamentals/triggers.md#data-triggers) статьи.

## <a name="using-the-command-interface"></a>С помощью интерфейса командной

Это приложение реагировать на `Button` касания без обработки `Clicked` событий. `Button` Реализует механизм альтернативных уведомления называется _команда_ или _заставляя_ интерфейса. Он состоит из двух свойств:

- [`Command`](xref:Xamarin.Forms.Button.Command) Тип [ `ICommand` ](xref:System.Windows.Input.ICommand), интерфейс, определенный в [ `System.Windows.Input` ](xref:System.Windows.Input) пространства имен.
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) свойство типа [ `Object` ](xref:System.Object).

Этот подход особенно удобен в связи с привязкой к данным и, особенно при реализации архитектуры Model-View-ViewModel (MVVM). В этих разделах рассматриваются в статьях [привязки данных](~/xamarin-forms/app-fundamentals/data-binding/index.md), [из привязки к данным MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md), и [MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md).

В приложении MVVM ViewModel определяет свойства типа `ICommand` , связаны с XAML `Button` элементов с помощью привязки данных. Xamarin.Forms также определяет [ `Command` ]((xref:Xamarin.Forms.Command`1)) и [ `Command<T>` ](xref:Xamarin.Forms.Command`1) классы, реализующие `ICommand` интерфейса и помочь ViewModel задание свойств типа `ICommand`.

Выполнение команд описан более подробно в статье [ **интерфейс командной** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) , но **основную команду кнопки** страницы в [  **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos) приведен пример основной подход.

`CommandDemoViewModel` Класс является очень простым ViewModel, который определяет свойство типа `double` с именем `Number`и два свойства типа `ICommand` с именем `MultiplyBy2Command` и `DivideBy2Command`:

```csharp
class CommandDemoViewModel : INotifyPropertyChanged
{
    double number = 1;

    public event PropertyChangedEventHandler PropertyChanged;

    public CommandDemoViewModel()
    {
        MultiplyBy2Command = new Command(() => Number *= 2);

        DivideBy2Command = new Command(() => Number /= 2);
    }

    public double Number
    {
        set
        {
            if (number != value)
            {
                number = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Number"));
            }
        }
        get
        {
            return number;
        }
    }

    public ICommand MultiplyBy2Command { private set; get; }

    public ICommand DivideBy2Command { private set; get; }
}
```

Два `ICommand` свойства инициализируются в конструктор класса с двумя объектами типа `Command`. `Command` Небольшой функции включают конструкторы (называется `execute` аргумента конструктора), удваивается или уменьшению `Number` свойство.

**BasicButtonCommand.xaml** файл задает его `BindingContext` к экземпляру компонента `CommandDemoViewModel`. `Label` Элемент и два `Button` элементы содержат привязки для трех свойств в `CommandDemoViewModel`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.BasicButtonCommandPage"
             Title="Basic Button Command">

    <ContentPage.BindingContext>
        <local:CommandDemoViewModel />
    </ContentPage.BindingContext>

    <StackLayout>
        <Label Text="{Binding Number, StringFormat='Value is now {0}'}"
               FontSize="Large"
               VerticalOptions="CenterAndExpand"
               HorizontalOptions="Center" />

        <Button Text="Multiply by 2"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Command="{Binding MultiplyBy2Command}" />

        <Button Text="Divide by 2"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Command="{Binding DivideBy2Command}" />
    </StackLayout>
</ContentPage>
```

Как два `Button` элементы были нажаты, выполняются команды, и число изменяет значение:

[![Команда основных кнопок](button-images/BasicButtonCommand.png "команду основные кнопки")](button-images/BasicButtonCommand-Large.png#lightbox)

Преимущество этого подхода перед `Clicked` обработчики —, вся логика, включающих функциональные возможности этой страницы находится в ViewModel, а не в файле кода, добиться лучшего разделения пользовательского интерфейса от бизнес-логики.

Существует также возможность `Command` объектов для управления включения и отключения `Button` элементов. Предположим, например, можно ограничить диапазон числовых значений между 2<sup>10</sup> и 2<sup>&ndash;10</sup>. Можно добавить другую функцию в конструктор (называется `canExecute` аргумент), возвращающее `true` Если `Button` должен быть включен. Вот изменение `CommandDemoViewModel` конструктор:

```csharp
class CommandDemoViewModel : INotifyPropertyChanged
{
    ···
    public CommandDemoViewModel()
    {
        MultiplyBy2Command = new Command(
            execute: () =>
            {
                Number *= 2;
                ((Command)MultiplyBy2Command).ChangeCanExecute();
                ((Command)DivideBy2Command).ChangeCanExecute();
            },
            canExecute: () => Number < Math.Pow(2, 10));

        DivideBy2Command = new Command(
            execute: () =>
            {
                Number /= 2;
                ((Command)MultiplyBy2Command).ChangeCanExecute();
                ((Command)DivideBy2Command).ChangeCanExecute();
            },
            canExecute: () => Number > Math.Pow(2, -10));
    }
    ···
}
```

Вызовы `ChangeCanExecute` метод `Command` необходимы, чтобы `Command` можно вызвать метод `canExecute` метода и определите ли `Button` должен быть включен или отключен. Благодаря этому изменению кода столько достигает предела, `Button` отключена:

[![Команда основных кнопок - изменения](button-images/BasicButtonCommandModified.png "команду основные кнопки - изменения")](button-images/BasicButtonCommandModified-Large.png#lightbox)

Это возможно для двух или более `Button` элементов, привязанного к тому же `ICommand` свойство. `Button` Можно различать элементы с помощью [ `CommandParameter` ](xref:Xamarin.Forms.Button.CommandParameter) свойство `Button`. В этом случае может потребоваться использовать универсальный [ `Command<T>` ](xref:Xamarin.Forms.Command`1) класса. `CommandParameter` Объект затем передается в качестве аргумента для `execute` и `canExecute` методы. Этот способ показан в подробно [ **основные команды** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding) раздел [ **интерфейс командной** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding) статьи.

[ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos) образец также использует этот метод в его `MainPage` класса. **MainPage.xaml** файл содержит `Button` для каждой страницы образца:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.MainPage"
             Title="Button Demos">
    <ScrollView>
        <FlexLayout Direction="Column"
                    JustifyContent="SpaceEvenly"
                    AlignItems="Center">

            <Button Text="Basic Button Click"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:BasicButtonClickPage}" />

            <Button Text="Code Button Click"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:CodeButtonClickPage}" />

            <Button Text="Basic Button Command"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:BasicButtonCommandPage}" />

            <Button Text="Press and Release Button"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:PressAndReleaseButtonPage}" />

            <Button Text="Button Appearance"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:ButtonAppearancePage}" />

            <Button Text="Toggle Button Demo"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:ToggleButtonDemoPage}" />

            <Button Text="Image Button Demo"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:ImageButtonDemoPage}" />

        </FlexLayout>
    </ScrollView>
</ContentPage>
```

Каждый `Button` имеет его `Command` свойство привязано к свойству с именем `NavigateCommand`и `CommandParameter` равно [ `Type` ](xref:System.Type) объект, соответствующий одному из классов страницы в проекте.

Что `NavigateCommand` свойство относится к типу `ICommand` и определяется в файле кода программной части:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();

        NavigateCommand = new Command<Type>(async (Type pageType) =>
        {
            Page page = (Page)Activator.CreateInstance(pageType);
            await Navigation.PushAsync(page);
        });

        BindingContext = this;
    }

    public ICommand NavigateCommand { private set; get; }
}
```

Конструктор инициализирует `NavigateCommand` свойства `Command<Type>` объекта, так как `Type` тип `CommandParameter` на объект в XAML-файле. Это означает, что `execute` метод имеет аргумент типа `Type` , соответствующий этому `CommandParameter` объекта. Функция создает страницы и затем переходит к нему.

Обратите внимание, что конструктор завершается, установив его `BindingContext` к самому себе. Это необходимо для свойства в XAML-файле для привязки к `NavigateCommand` свойство.

## <a name="pressing-and-releasing-the-button"></a>Нажатие и отпускание кнопки

Помимо `Clicked` событий, `Button` также определяет [ `Pressed` ](xref:Xamarin.Forms.Button.Pressed) и [ `Released` ](xref:Xamarin.Forms.Button.Released) события. `Pressed` Событие происходит, когда палец нажимает `Button`, или нажатии кнопки мыши с указатель на `Button`. `Released` Событие происходит при отпускании кнопки мыши или пальца. Как правило `Clicked` событие также создается в то же время, как `Released` события, но если указатель мыши или пальца слайды вне области `Button` до освобождения, `Clicked` не может произойти событие.

`Pressed` И `Released` событий часто не используются, но они могут использоваться для особых целей, как показано в **клавишу и кнопку выпуск** страницы. XAML-файл содержит `Label` и `Button` с обработчики, вложенные для `Pressed` и `Released` событий:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.PressAndReleaseButtonPage"
             Title="Press and Release Button">
    <StackLayout>

        <Label x:Name="label"
               Text="Press and hold the Button below"
               FontSize="Large"
               VerticalOptions="CenterAndExpand"
               HorizontalOptions="Center" />

        <Button Text="Press to Rotate Text!"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Pressed="OnButtonPressed"
                Released="OnButtonReleased" />

    </StackLayout>
</ContentPage>
```

Анимирует файл кода программной `Label` при `Pressed` событие происходит, но приостанавливает поворот при `Released` событием:

```csharp
public partial class PressAndReleaseButtonPage : ContentPage
{
    bool animationInProgress = false;
    Stopwatch stopwatch = new Stopwatch();

    public PressAndReleaseButtonPage ()
    {
        InitializeComponent ();
    }

    void OnButtonPressed(object sender, EventArgs args)
    {
        stopwatch.Start();
        animationInProgress = true;

        Device.StartTimer(TimeSpan.FromMilliseconds(16), () =>
        {
            label.Rotation = 360 * (stopwatch.Elapsed.TotalSeconds % 1);

            return animationInProgress;
        });
    }

    void OnButtonReleased(object sender, EventArgs args)
    {
        animationInProgress = false;
        stopwatch.Stop();
    }
}
```

В результате `Label` только вращается при находится контакты с `Button`и останавливается, когда палец освобождается:

[![Нажмите и отпустите кнопку](button-images/PressAndReleaseButton.png "нажмите и отпустите кнопку")](button-images/PressAndReleaseButton-Large.png)

Такое поведение имеет приложений для игр: палец на `Button` может стать объект на экране переместить в определенном направлении.

<a name="button-appearance" />

## <a name="button-appearance"></a>Внешний вид кнопки

`Button` Наследует или определяет несколько свойств, которые влияют на внешний вид:

- [`TextColor`](xref:Xamarin.Forms.Button.TextColor) цвет `Button` текста
- [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) цвет фона для текста
- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) цвет области окружающего текста `Button`
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily) используется семейство шрифта для текста
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize) размер текста
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes) Указывает, является ли текст курсивом или полужирным шрифтом
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) Ширина границы
- [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) Округление углов

Эффекты шесть из этих свойств (за исключением `FontFamily` и `FontAttributes`) демонстрируются в **внешний вид кнопки** страницы. Другое свойство, [ `Image` ](xref:Xamarin.Forms.Button.Image), описанным в разделе [ **посредством точечных рисунков с кнопкой**](#image-button).

Все представления и привязки данных в **внешний вид кнопки** страницы определены в файле XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.ButtonAppearancePage"
             Title="Button Appearance">
    <StackLayout>
        <Button x:Name="button"
                Text="Button"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                TextColor="{Binding Source={x:Reference textColorPicker},
                                    Path=SelectedItem.Color}"
                BackgroundColor="{Binding Source={x:Reference backgroundColorPicker},
                                          Path=SelectedItem.Color}"
                BorderColor="{Binding Source={x:Reference borderColorPicker},
                                      Path=SelectedItem.Color}" />

        <StackLayout BindingContext="{x:Reference button}"
                     Padding="10">

            <Slider x:Name="fontSizeSlider"
                    Maximum="48"
                    Minimum="1"
                    Value="{Binding FontSize}" />

            <Label Text="{Binding Source={x:Reference fontSizeSlider},
                                  Path=Value,
                                  StringFormat='FontSize = {0:F0}'}"
                   HorizontalTextAlignment="Center" />

            <Slider x:Name="borderWidthSlider"
                    Minimum="-1"
                    Maximum="12"
                    Value="{Binding BorderWidth}" />

            <Label Text="{Binding Source={x:Reference borderWidthSlider},
                                  Path=Value,
                                  StringFormat='BorderWidth = {0:F0}'}"
                   HorizontalTextAlignment="Center" />

            <Slider x:Name="cornerRadiusSlider"
                    Minimum="-1"
                    Maximum="24"
                    Value="{Binding CornerRadius}" />

            <Label Text="{Binding Source={x:Reference cornerRadiusSlider},
                                  Path=Value,
                                  StringFormat='CornerRadius = {0:F0}'}"
                   HorizontalTextAlignment="Center" />

            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>

                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>

                <Grid.Resources>
                    <Style TargetType="Label">
                        <Setter Property="VerticalOptions" Value="Center" />
                    </Style>
                </Grid.Resources>

                <Label Text="Text Color:"
                       Grid.Row="0" Grid.Column="0" />

                <Picker x:Name="textColorPicker"
                        ItemsSource="{Binding Source={x:Static local:NamedColor.All}}"
                        ItemDisplayBinding="{Binding FriendlyName}"
                        SelectedIndex="0"
                        Grid.Row="0" Grid.Column="1" />

                <Label Text="Background Color:"
                       Grid.Row="1" Grid.Column="0" />

                <Picker x:Name="backgroundColorPicker"
                        ItemsSource="{Binding Source={x:Static local:NamedColor.All}}"
                        ItemDisplayBinding="{Binding FriendlyName}"
                        SelectedIndex="0"
                        Grid.Row="1" Grid.Column="1" />

                <Label Text="Border Color:"
                       Grid.Row="2" Grid.Column="0" />

                <Picker x:Name="borderColorPicker"
                        ItemsSource="{Binding Source={x:Static local:NamedColor.All}}"
                        ItemDisplayBinding="{Binding FriendlyName}"
                        SelectedIndex="0"
                        Grid.Row="2" Grid.Column="1" />
            </Grid>
        </StackLayout>
    </StackLayout>
</ContentPage>
```

`Button` В верхней части страницы есть три его `Color` свойства привязаны к `Picker` элементы в нижней части страницы. Элементы в `Picker` элементы, цвета из `NamedColor` класс включен в проект. Три `Slider` элементы содержать двусторонней привязки к `FontSize`, `BorderWidth`, и `CornerRadius` свойства `Button`.

Эта программа позволяет экспериментировать с сочетания этих свойств:

[![Внешний вид кнопки](button-images/ButtonAppearance.png "внешний вид кнопки")](button-images/ButtonAppearance-Large.png)

Чтобы увидеть `Button` границы, вам потребуется установить `BorderColor` на что-то отличное от `Default`и `BorderWidth` положительное значение.

На iOS, вы заметите, что значения ширины границы больших вторгаться в внутреннюю часть `Button` , влияют на отображение текста. Если вы решили использовать рамку с iOS `Button`, вам возможно потребуется начинаются и заканчиваются `Text` свойство пробелы для сохранения его видимость.

На UWP выбрав `CornerRadius` , превышает половину высоты `Button` вызывает исключение.

## <a name="creating-a-toggle-button"></a>Создание выключатель

Можно создать подкласс `Button` , чтобы он работает как параметр включения или выключения: коснитесь кнопки один раз для выключатель на и щелкните его еще раз, чтобы отключить данную функцию.

Следующие `ToggleButton` класс является производным от `Button` и определяет новое событие с именем `Toggled` и логическое свойство с именем `IsToggled`. Это же два свойства, определенные Xamarin.Forms [ `Switch` ](xref:Xamarin.Forms.Switch):

```csharp
class ToggleButton : Button
{
    public event EventHandler<ToggledEventArgs> Toggled;

    public static BindableProperty IsToggledProperty =
        BindableProperty.Create("IsToggled", typeof(bool), typeof(ToggleButton), false,
                                propertyChanged: OnIsToggledChanged);

    public ToggleButton()
    {
        Clicked += (sender, args) => IsToggled ^= true;
    }

    public bool IsToggled
    {
        set { SetValue(IsToggledProperty, value); }
        get { return (bool)GetValue(IsToggledProperty); }
    }

    protected override void OnParentSet()
    {
        base.OnParentSet();
        VisualStateManager.GoToState(this, "ToggledOff");
    }

    static void OnIsToggledChanged(BindableObject bindable, object oldValue, object newValue)
    {
        ToggleButton toggleButton = (ToggleButton)bindable;
        bool isToggled = (bool)newValue;

        // Fire event
        toggleButton.Toggled?.Invoke(toggleButton, new ToggledEventArgs(isToggled));

        // Set the visual state
        VisualStateManager.GoToState(toggleButton, isToggled ? "ToggledOn" : "ToggledOff");
    }
}
```

`ToggleButton` Конструктор присоединяет обработчик для `Clicked` событий, так что он может изменить значение `IsToggled` свойства. `OnIsToggledChanged` Вызывается метод `Toggled` событий.

В последней строке `OnIsToggledChanged` метод вызывает статический `VisualStateManager.GoToState` метод с двумя текст строки «ToggledOn» и «ToggledOff». Можно прочитать о этот метод и каким образом приложение может отвечать на визуальных состояний в статье [ **Xamarin.Forms Диспетчер визуальных состояний**](~/xamarin-forms/user-interface/visual-state-manager.md).

Поскольку `ToggleButton` делает вызов `VisualStateManager.GoToState`, самого класса не должны включать все дополнительные функции, чтобы изменить внешний вид кнопки на основе его `IsToggled` состояния. То есть на себя ответственность за XAML, на котором размещена `ToggleButton`.

**Демонстрация кнопки переключателя** страница содержит два экземпляра `ToggleButton`, включая разметку Диспетчер визуальных состояний, который задает `Text`, `BackgroundColor`, и `TextColor` основании визуальное состояние кнопки:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.ToggleButtonDemoPage"
             Title="Toggle Button Demo">

    <ContentPage.Resources>
        <Style TargetType="local:ToggleButton">
            <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            <Setter Property="HorizontalOptions" Value="Center" />
        </Style>
    </ContentPage.Resources>

    <StackLayout Padding="10, 0">
        <local:ToggleButton Toggled="OnItalicButtonToggled">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ToggleStates">
                    <VisualState Name="ToggledOff">
                        <VisualState.Setters>
                            <Setter Property="Text" Value="Italic Off" />
                            <Setter Property="BackgroundColor" Value="#C0C0C0" />
                            <Setter Property="TextColor" Value="Black" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="ToggledOn">
                        <VisualState.Setters>
                            <Setter Property="Text" Value=" Italic On " />
                            <Setter Property="BackgroundColor" Value="#404040" />
                            <Setter Property="TextColor" Value="White" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </local:ToggleButton>

        <local:ToggleButton Toggled="OnBoldButtonToggled">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ToggleStates">
                    <VisualState Name="ToggledOff">
                        <VisualState.Setters>
                            <Setter Property="Text" Value="Bold Off" />
                            <Setter Property="BackgroundColor" Value="#C0C0C0" />
                            <Setter Property="TextColor" Value="Black" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="ToggledOn">
                        <VisualState.Setters>
                            <Setter Property="Text" Value=" Bold On " />
                            <Setter Property="BackgroundColor" Value="#404040" />
                            <Setter Property="TextColor" Value="White" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </local:ToggleButton>

        <Label x:Name="label"
               Text="Just a little passage of some sample text that can be formatted in italic or boldface by toggling the two buttons."
               FontSize="Large"
               HorizontalTextAlignment="Center"
               VerticalOptions="CenterAndExpand" />

    </StackLayout>
</ContentPage>
```

`Toggled` Обработчики событий, в файле кода. Они отвечают за параметр `FontAttributes` свойство `Label` исходя из состояния кнопок:

```csharp
public partial class ToggleButtonDemoPage : ContentPage
{
    public ToggleButtonDemoPage ()
    {
        InitializeComponent ();
    }

    void OnItalicButtonToggled(object sender, ToggledEventArgs args)
    {
        if (args.Value)
        {
            label.FontAttributes |= FontAttributes.Italic;
        }
        else
        {
            label.FontAttributes &= ~FontAttributes.Italic;
        }
    }

    void OnBoldButtonToggled(object sender, ToggledEventArgs args)
    {
        if (args.Value)
        {
            label.FontAttributes |= FontAttributes.Bold;
        }
        else
        {
            label.FontAttributes &= ~FontAttributes.Bold;
        }
    }
}
```

Вот программу на iOS, Android и UWP.

[![Демонстрация кнопки переключения](button-images/ToggleButtonDemo.png "переключения Демонстрация кнопки")](button-images/ToggleButtonDemo-Large.png#lightbox)

<a name="image-button" />

## <a name="using-bitmaps-with-buttons"></a>С помощью точечных рисунков с кнопками

`Button` Класс определяет [ `Image` ](xref:Xamarin.Forms.Button.Image) свойство, которое позволяет отобразить растровое изображение на `Button`, отдельно или в сочетании с текстом. Можно также указать расположение текста и изображения.

`Image` Свойство относится к типу [ `FileImageSource` ](xref:Xamarin.Forms.FileImageSource), означающее, что точечные рисунки должен быть сохранен как ресурсы в проектах отдельных платформы и не в проекте библиотеки .NET Standard.

Каждой платформы, поддерживаемые Xamarin.Forms позволяет изображения для сохранения в нескольких размеров для различных разрешением различных устройств, которые приложение может работать на. Несколько точечные рисунки имеют с именем или хранятся таким образом, что операционной системы можно выбрать наиболее подходящие для видео устройства отображаются разрешения.

Для изображения на `Button`, оптимальный размер обычно составляет 32- и 64 аппаратно независимых единицах, в зависимости от размера вам хотелось бы быть. Изображения, используемые в этом примере основаны на размер 48 аппаратно независимых единицах.

В проекте iOS **ресурсов** папка содержит три размеры изображения:

- 48 квадратный точечный рисунок хранятся в виде **/Resources/MonkeyFace.png**
- Хранятся в виде квадратный растровое изображение 96 пикселей **/Resource/MonkeyFace@2x.png**
- 144 квадратный точечный рисунок хранятся в виде **/Resource/MonkeyFace@3x.png**

Все три растровых изображений приводилось **действие при построении** из **BundleResource**.

Для проекта Android, все точечные рисунки имеют то же имя, но они хранятся в различных вложенных папках **ресурсов** папки:

- 72 квадратный точечный рисунок хранятся в виде **/Resources/drawable-hdpi/MonkeyFace.png**
- Квадратный растровое изображение 96 пикселей хранятся в виде **/Resources/drawable-xhdpi/MonkeyFace.png**
- 144 квадратный точечный рисунок хранятся в виде **/Resources/drawable-xxhdpi/MonkeyFace.png**
- 192 квадратный точечный рисунок хранятся в виде **/Resources/drawable-xxxhdpi/MonkeyFace.png**

Эти приводилось **действие при построении** из **AndroidResource**.

В проекте UWP, точечные рисунки могут храниться в любом месте в проекте, но обычно хранятся в пользовательской папке или **активы** существующую папку. Проект UWP содержит эти битовые карты:

- 48 квадратный точечный рисунок хранятся в виде **/Assets/MonkeyFace.scale-100.png**
- Квадратный растровое изображение 96 пикселей хранятся в виде **/Assets/MonkeyFace.scale-200.png**
- 192 квадратный точечный рисунок хранятся в виде **/Assets/MonkeyFace.scale-400.png**

Они все приводилось **действие при построении** из **содержимого**.

Можно указать как `Text` и `Image` свойства упорядочены на `Button` с помощью [ `ContentLayout` ](xref:Xamarin.Forms.Button.ContentLayout) свойство `Button`. Это свойство имеет тип [ `ButtonContentLayout` ](xref:Xamarin.Forms.Button.ButtonContentLayout), который является классом, внедренные в `Button`. [Конструктор](xref:Xamarin.Forms.Button.ButtonContentLayout.%23ctor(Xamarin.Forms.Button.ButtonContentLayout.ImagePosition,System.Double)) имеет два аргумента:

- Член [ `ImagePosition` ](xref:Xamarin.Forms.Button.ButtonContentLayout.ImagePosition) перечисления: `Left`, `Top`, `Right`, или `Bottom` , указывающее, как отображается растрового изображения относительно текста.
- Объект `double` интервалы между точечный рисунок и текст.

По умолчанию используются `Left` и 10 единиц. Два свойства только для чтения из `ButtonContentLayout` с именем [ `Position` ](xref:Xamarin.Forms.Button.ButtonContentLayout.Position) и [ `Spacing` ](xref:Xamarin.Forms.Button.ButtonContentLayout.Spacing) предоставляют значения этих свойств.

В коде, можно создать `Button` и задать `ContentLayout` свойства следующим образом:

```csharp
Button button = new Button
{
    Text = "button text",
    Image = new FileImageSource
    {
        File = "image filename"
    },
    ContentLayout = new Button.ButtonContentLayout(Button.ButtonContentLayout.ImagePosition.Right, 20)
};
```

В языке XAML требуется указывать только члена перечисления или интервал или в любом порядке, разделенные запятыми:

```xaml
<Button Text="button text"
        Image="image filename"
        ContentLayout="Right, 20" />
```

**Демонстрация изображения кнопки** страница использует `OnPlatform` для указания различных имени файла для iOS, Android и UWP файлы точечных рисунков. Если вы хотите использовать это имя для всех трех платформ и избегайте использования `OnPlatform`, вам потребуется для хранения точечных рисунков UWP в корневом каталоге проекта.

Первый `Button` на **Демонстрация изображения кнопки** страницы наборы `Image` свойства, но не `Text` свойство:

```xaml
<Button>
    <Button.Image>
        <OnPlatform x:TypeArguments="FileImageSource">
            <On Platform="iOS, Android" Value="MonkeyFace.png" />
            <On Platform="UWP" Value="Assets/MonkeyFace.png" />
        </OnPlatform>
    </Button.Image>
</Button>
```

Если точечные рисунки UWP хранятся в корневом каталоге проекта, можно значительно упростить Эта разметка:

```xaml
<Button Image="MonkeyFace.png" />
```

Во избежание много повторяющихся разметки в **ImageButtonDemo.xaml** файл неявный `Style` определяется набор `Image` свойство. Это `Style` автоматически применяется к пяти других `Button` элементов. Ниже приведен полный файл XAML.

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.ImageButtonDemoPage">

    <FlexLayout Direction="Column"
                JustifyContent="SpaceEvenly"
                AlignItems="Center">

        <FlexLayout.Resources>
            <Style TargetType="Button">
                <Setter Property="Image">
                    <OnPlatform x:TypeArguments="FileImageSource">
                        <On Platform="iOS, Android" Value="MonkeyFace.png" />
                        <On Platform="UWP" Value="Assets/MonkeyFace.png" />
                    </OnPlatform>
                </Setter>
            </Style>
        </FlexLayout.Resources>

        <Button>
            <Button.Image>
                <OnPlatform x:TypeArguments="FileImageSource">
                    <On Platform="iOS, Android" Value="MonkeyFace.png" />
                    <On Platform="UWP" Value="Assets/MonkeyFace.png" />
                </OnPlatform>
            </Button.Image>
        </Button>

        <Button Text="Default" />

        <Button Text="Left - 10"
                ContentLayout="Left, 10" />

        <Button Text="Top - 10"
                ContentLayout="Top, 10" />

        <Button Text="Right - 20"
                ContentLayout="Right, 20" />

        <Button Text="Bottom - 20"
                ContentLayout="Bottom, 20" />
    </FlexLayout>
</ContentPage>
```

Окончательный четыре `Button` элементы сделать использование `ContentLayout` свойство, чтобы указать положение и интервала для текста или точечного рисунка:

[![Демонстрация кнопки изображения](button-images/ImageButtonDemo.png "изображения Демонстрация кнопки")](button-images/ImageButtonDemo-Large.png#lightbox)

Теперь вы увидели различные способы, которые можно обрабатывать `Button` события и изменение `Button` внешний вид.

## <a name="related-links"></a>Связанные ссылки

- [Образец ButtonDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos)
- [Кнопка API](xref:Xamarin.Forms.Button)
