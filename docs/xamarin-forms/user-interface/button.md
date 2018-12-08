---
title: Кнопка Xamarin.Forms
description: Кнопки реагирует на касание или щелчок, который направляет приложению выполнения определенной задачи.
ms.prod: xamarin
ms.assetid: 62CAEB63-0800-44F4-9B8C-EE632138C2F5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/19/2018
ms.openlocfilehash: 7108b24f73d1f7389bf37fe8640061ae3e077300
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53054428"
---
# <a name="xamarinforms-button"></a>Кнопка Xamarin.Forms

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos)

_Кнопки реагирует на касание или щелчок, который направляет приложению выполнения определенной задачи._

[ `Button` ](xref:Xamarin.Forms.Button) Является наиболее фундаментальным интерактивного управления во всех Xamarin.Forms. `Button` Обычно отображает, короткая строка текста, показывающее, команды, но его также можно отобразить растровое изображение, или сочетание текста и изображения. Пользователь нажимает клавишу `Button` пальцем или щелкает его с помощью мыши для запуска этой команды.

Большинство разделов, о котором рассказывается ниже соответствует страниц в [ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos) образца.

## <a name="handling-button-clicks"></a>Обработка кнопки "нажимает кнопку

`Button` Определяет [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) событие, возникающее, когда пользователь касается `Button` с указателем мыши или палец. Событие при отжатии кнопки мыши или палец поверхности от `Button`. `Button` Должен иметь его [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) свойство значение `true` для того, чтобы реагировать на них касания.

**Основные нажатия кнопки** странице в [ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos) образце показано, как создать экземпляр `Button` в XAML и обработки его `Clicked` событий. **BasicButtonClickPage.xaml** файл содержит `StackLayout` с обоими `Label` и `Button`:

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

`Button` Обычно занимают все пространство, разрешенное для него. Например, если вы не задали `HorizontalOptions` свойство `Button` на что-то отличное от `Fill`, `Button` займет всю ширину его родительского.

По умолчанию `Button` представляет собой прямоугольник, но можно дать углы округленное ИТ с помощью [ `CornerRadius` ](xref:Xamarin.Forms.Button.CornerRadius) свойства, как описано ниже в разделе [ **внешний вид кнопки** ](#button-appearance).

[ `Text` ](xref:Xamarin.Forms.Button.Text) Свойство определяет текст, отображаемый в `Button`. [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) Событий присваивается обработчик событий с именем `OnButtonClicked`. Этот обработчик находится в файле кода **BasicButtonClickPage.xaml.cs**:

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

Когда `Button` шифрованию, `OnButtonClicked` выполнения метода. `sender` Аргумент `Button` объекта, ответственного за это событие. Это можно использовать для доступа к `Button` объекта, или чтобы различать несколько `Button` объектов с одинаковым `Clicked` событий.

Данный конкретный `Clicked` обработчик вызывает функцию, анимации, который вращается `Label` 360 градусов в 1000 миллисекунд. Вот ее запуск на устройствах iOS и Android и как приложение универсальной платформы Windows (UWP) на рабочем столе Windows 10.

[![Нажатие кнопки основные](button-images/BasicButtonClick.png "нажатие кнопки основные")](button-images/BasicButtonClick-Large.png#lightbox "нажатие основные кнопки")

Обратите внимание, что `OnButtonClicked` метод включает `async` модификатор поскольку `await` используется в обработчике событий. Объект `Clicked` обработчика событий требует `async` модификатор только в том случае, если в теле обработчика используется `await`.

Выполняет визуализацию каждой платформы `Button` в свой собственный особым образом. В [ **внешний вид кнопки** ](#button-appearance) разделе будет показано, как задать цвета и сделать `Button` видимые для более функциональный внешний вид границы. `Button` реализует [ `IFontElement` ](xref:Xamarin.Forms.Internals.IFontElement) интерфейс, включив в него [ `FontFamily` ](xref:Xamarin.Forms.Button.FontFamily), [ `FontSize` ](xref:Xamarin.Forms.Button.FontSize), и [ `FontAttributes` ](xref:Xamarin.Forms.Button.FontAttributes)свойства.

## <a name="creating-a-button-in-code"></a>Создание кнопки в коде

Обычно для создания экземпляра `Button` в XAML, но вы также можете создать `Button` в коде. Это может быть удобным, если приложению требуется создать несколько кнопок, на основе данных, который является перечислимым с `foreach` цикла.

**Нажатия кнопки код** странице показано, как создать страницу, которая является функциональным эквивалентом **основные нажатия кнопки** страницы, но полностью в C#:

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

Все, что делается в конструктор класса. Так как `Clicked` обработчик является только одна инструкция длинное, его можно будет присоединить к событию очень просто:

```csharp
button.Clicked += async (sender, args) => await label.RelRotateTo(360, 1000);
```

Конечно, можно также определить обработчик событий как отдельный метод (так же, как `OnButtonClick` метод в **основные нажатия кнопки**) и присоединить этот метод к событию:

```csharp
button.Clicked += OnButtonClicked;
```

## <a name="disabling-the-button"></a>Отключение кнопки

Иногда приложение находится в определенном состоянии, там, где определенный `Button` щелкните не является допустимой операцией. В таком случае `Button` следует отключить, задав его `IsEnabled` свойства `false`. Классическим примером является `Entry` управления для имени файла с открывающей файл `Button`: `Button` должно быть включено только в том случае, если какой-либо текст, набранный в `Entry`.
Можно использовать `DataTrigger` для этой задачи, как показано в [ **триггеры данных** ](~/xamarin-forms/app-fundamentals/triggers.md#data-triggers) статьи.

## <a name="using-the-command-interface"></a>С помощью интерфейса командной

Существует возможность приложения реагировать на `Button` касания без обработки `Clicked` событий. `Button` Реализует механизм альтернативную вызывается _команда_ или _команды_ интерфейс. Он состоит из двух свойств:

- [`Command`](xref:Xamarin.Forms.Button.Command) типа [ `ICommand` ](xref:System.Windows.Input.ICommand), интерфейс, определенный в [ `System.Windows.Input` ](xref:System.Windows.Input) пространства имен.
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) свойство типа [ `Object` ](xref:System.Object).

Этот подход особенно удобен в связи с привязкой к данным и особенно в том случае, если реализация архитектуры Model-View-ViewModel (MVVM). В этих разделах рассматриваются в статьях [привязки данных](~/xamarin-forms/app-fundamentals/data-binding/index.md), [из привязки данных до MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md), и [MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md).

В приложении MVVM ViewModel определяет свойства типа `ICommand` , которые затем подключены к XAML `Button` элементов при помощи привязки данных. Xamarin.Forms также определяет [ `Command` ]((xref:Xamarin.Forms.Command`1)) и [ `Command<T>` ](xref:Xamarin.Forms.Command`1) классы, реализующие `ICommand` интерфейс, а также упрощения ViewModel определения свойств типа `ICommand`.

Система команд описан более подробно в статье [ **интерфейс командной** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) , но **базовая команда кнопку** странице в [  **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos) пример основной подход.

`CommandDemoViewModel` Класс является очень простой ViewModel, который определяет свойство типа `double` с именем `Number`и два свойства типа `ICommand` с именем `MultiplyBy2Command` и `DivideBy2Command`:

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

Два `ICommand` свойства инициализируются в конструктор класса с двумя объектами типа `Command`. `Command` Конструкторы включать немного функцию (вызывается `execute` аргумент конструктора), удваивается или уменьшению `Number` свойство.

**BasicButtonCommand.xaml** файле задает его `BindingContext` к экземпляру `CommandDemoViewModel`. `Label` Элемент и два `Button` элементы содержат привязки в три свойства `CommandDemoViewModel`:

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

Как два `Button` нажатые элементов, выполняются команды, и номер изменяет значение:

[![Команда основных кнопок](button-images/BasicButtonCommand.png "основные кнопки команды")](button-images/BasicButtonCommand-Large.png#lightbox)

Преимущество этого подхода перед использованием `Clicked` обработчики —, что вся логика, включающих функциональные возможности этой страницы находится в ViewModel, а не в файл с выделенным кодом достижение лучшее разделение пользовательского интерфейса от бизнес-логики.

Существует также возможность `Command` объектов для управления, включение и отключение `Button` элементов. Например, предположим, вы хотите ограничить диапазон числовых значений между 2<sup>10</sup> и 2<sup>&ndash;10</sup>. Можно добавить другую функцию в конструктор (вызывается `canExecute` аргумента), возвращающий `true` Если `Button` должен быть включен. Вот изменение, `CommandDemoViewModel` конструктор:

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

Вызовы `ChangeCanExecute` метод `Command` необходимы, чтобы `Command` можно вызвать метод `canExecute` метод и определить ли `Button` должен быть включен или отключен. Благодаря этому изменению кода как номер достигает предельного размера, `Button` отключен:

[![Команда основных кнопок — Изменить](button-images/BasicButtonCommandModified.png "команда основных кнопок — изменения")](button-images/BasicButtonCommandModified-Large.png#lightbox)

Это возможно в двух или более `Button` элементы для привязки к тому же `ICommand` свойство. `Button` Можно различать элементы с помощью [ `CommandParameter` ](xref:Xamarin.Forms.Button.CommandParameter) свойство `Button`. В этом случае необходимо использовать универсальный [ `Command<T>` ](xref:Xamarin.Forms.Command`1) класса. `CommandParameter` Объект затем передается как аргумент `execute` и `canExecute` методы. Эта методика показана подробно [ **основные команды** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding) раздел [ **интерфейс командной** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding) статьи.

[ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos) пример также использует эту методику в его `MainPage` класса. **MainPage.xaml** файл содержит `Button` для каждой страницы примера:

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

Каждый `Button` имеет его `Command` свойство привязано к свойству с именем `NavigateCommand`и `CommandParameter` присваивается [ `Type` ](xref:System.Type) объект, соответствующий одному из классов страницы в проекте.

Что `NavigateCommand` свойство имеет тип `ICommand` и определен в файле с выделенным кодом:

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

Конструктор инициализирует `NavigateCommand` свойства `Command<Type>` объекта, так как `Type` — это тип `CommandParameter` объекта заданы в файле XAML. Это означает, что `execute` метод имеет аргумент типа `Type` , соответствующий этому `CommandParameter` объекта. Функция создает экземпляр страницы и затем переходит к нему.

Обратите внимание, что конструктор завершает, задав его `BindingContext` на самого себя. Это необходимо для свойств в файле XAML для привязки к `NavigateCommand` свойство.

## <a name="pressing-and-releasing-the-button"></a>Нажатие и отпускание кнопки

Помимо `Clicked` событий, `Button` также определяет [ `Pressed` ](xref:Xamarin.Forms.Button.Pressed) и [ `Released` ](xref:Xamarin.Forms.Button.Released) события. `Pressed` Событие возникает при нажатии палец `Button`, или нажатии кнопки мыши с помощью указателя за пределы `Button`. `Released` Событие возникает при отпускании кнопки мыши или палец. Как правило `Clicked` событие также инициируется в то же время, как `Released` события, но если указатель мыши или палец слайды от поверхности `Button` до освобождения, `Clicked` событие может не произойти.

`Pressed` И `Released` часто не используются события, но они могут использоваться для особых целей, как показано в **нажмите и отпустите кнопку** страницы. Файл XAML содержит `Label` и `Button` с обработчики, вложенные для `Pressed` и `Released` события:

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

Файл с выделенным кодом анимирует `Label` при `Pressed` событие происходит, но приостанавливает поворот при `Released` событием:

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

В результате `Label` поворачивает при находится контакты с `Button`и останавливается при выпуске пальца:

[![Нажмите и отпустите кнопку](button-images/PressAndReleaseButton.png "нажмите и отпустите кнопку")](button-images/PressAndReleaseButton-Large.png)

Такое поведение имеет приложений для игр: палец пройдет `Button` может придать объекту на экране переместить в определенном направлении.

<a name="button-appearance" />

## <a name="button-appearance"></a>Внешний вид кнопки

`Button` Наследует или определяет несколько свойств, которые влияют на его внешний вид:

- [`TextColor`](xref:Xamarin.Forms.Button.TextColor) цвет `Button` текста
- [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) цвет фона для текста
- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) цвет области, окружающей `Button`
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily) Семейство шрифтов, используемых для текста
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize) размер текста
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes) Указывает, является ли текст курсивом или полужирным шрифтом
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) Ширина границы
- [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) — Это радиус скругления углов `Button`

> [!NOTE]
> `Button` Класс также имеет [ `Margin` ](xref:Xamarin.Forms.View.Margin) и [ `Padding` ](xref:Xamarin.Forms.Button.Padding) свойства, которые управляют поведением макета `Button`. Дополнительные сведения см. в разделе [поля и заполнение](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

Последствия шесть из этих свойств (за исключением `FontFamily` и `FontAttributes`) показаны в **внешний вид кнопки** страницы. Еще одно свойство, [ `Image` ](xref:Xamarin.Forms.Button.Image), рассматривается в разделе [ **с помощью точечных рисунков с кнопкой**](#image-button).

Все представления и данные привязки в **внешний вид кнопки** страницы определены в файле XAML:

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

`Button` В верхней части страницы имеет трех ее `Color` свойства привязаны к `Picker` элементы в нижней части страницы. Элементы в `Picker` элементы являются цветов из `NamedColor` классов, включенных в проект. Три `Slider` элементы содержат имеют двухсторонние привязки к `FontSize`, `BorderWidth`, и `CornerRadius` свойства `Button`.

Эта программа позволяет экспериментировать с различными комбинациями все эти свойства:

[![Внешний вид кнопки](button-images/ButtonAppearance.png "внешний вид кнопки")](button-images/ButtonAppearance-Large.png)

Чтобы увидеть `Button` границы, вам потребуется задать `BorderColor` на что-то отличное от `Default`и `BorderWidth` положительное значение.

В iOS, вы заметите, что ширина большой границы вторгаться в внутреннюю часть `Button` , влияют на отображение текста. Если вы решили использовать элемент border с iOS `Button`, вы, вероятно, захотите начинаются и заканчиваются `Text` свойство с пробелами, чтобы сохранить его видимость.

На UWP выбрав `CornerRadius` , превышает половину высоты `Button` приводит к появлению исключения.

## <a name="button-visual-states"></a>Визуальные состояния кнопки

[`Button`](xref:Xamarin.Forms.Button) имеет `Pressed` [ `VisualState` ](xref:Xamarin.Forms.VisualState) , можно использовать для запуска Визуальное изменение в `Button` при нажатии пользователем, в условии, что он включен.

В следующем примере XAML показано, как определить визуальное состояние для `Pressed` состояния:

```xaml
<Button Text="Click me!"
        ...>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="Scale"
                            Value="1" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Pressed">
                <VisualState.Setters>
                    <Setter Property="Scale"
                            Value="0.8" />
                </VisualState.Setters>
            </VisualState>

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</ImageButton>
```

`Pressed` [ `VisualState` ](xref:Xamarin.Forms.VisualState) Указывает, что при [ `Button` ](xref:Xamarin.Forms.Button) нажата, его [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) свойства изменяется от его значение по умолчанию 1 к 0,8. `Normal` `VisualState` Указывает, что при `Button` находится в обычном состоянии, его `Scale` будет установлено значение 1. Таким образом, общий эффект том, что при `Button` является нажата, она масштабируется повторно немного меньше, и когда `Button` является выпуска, она масштабируется повторно размер по умолчанию.

Дополнительные сведения о визуальных состояниях см. в разделе [Xamarin.Forms Диспетчер визуальных состояний](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="creating-a-toggle-button"></a>Создание кнопку-переключатель

Можно создать подкласс `Button` таким образом, чтобы он работает как параметр включения или выключения: используйте кнопку один раз для выключатель на и коснитесь его еще раз, чтобы отключить данную функцию.

Следующие `ToggleButton` класс является производным от `Button` и определяет новое событие, именуемое `Toggled` и логическое свойство с именем `IsToggled`. Это те же два свойства, определяемые Xamarin.Forms [ `Switch` ](xref:Xamarin.Forms.Switch):

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

`ToggleButton` Конструктор присоединяет обработчик к `Clicked` событий, так что он может изменить значение `IsToggled` свойства. `OnIsToggledChanged` Вызывается метод `Toggled` событий.

В последней строке `OnIsToggledChanged` метод вызывает статический `VisualStateManager.GoToState` метод с двумя текст строки «ToggledOn» и «ToggledOff». Читайте о этот метод и реакцию приложения на визуальные состояния, в этой статье [ **Xamarin.Forms Диспетчер визуальных состояний**](~/xamarin-forms/user-interface/visual-state-manager.md).

Так как `ToggleButton` , вызывающих `VisualStateManager.GoToState`, сам класс не должен включать все дополнительные функции, чтобы изменить внешний вид кнопки, на основе его `IsToggled` состояния. То есть ответственность за XAML, на котором размещена `ToggleButton`.

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

`Toggled` Обработчики событий находятся в файле кода. Они отвечают за параметр `FontAttributes` свойство `Label` на основе состояния кнопок:

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

Ниже приведен программу на iOS, Android и UWP.

[![Переключить Демонстрация кнопки](button-images/ToggleButtonDemo.png "Демонстрация кнопки переключения")](button-images/ToggleButtonDemo-Large.png#lightbox)

<a name="image-button" />

## <a name="using-bitmaps-with-buttons"></a>С помощью точечных рисунков с кнопками

`Button` Класс определяет [ `Image` ](xref:Xamarin.Forms.Button.Image) свойство, которое позволяет отображать растровое изображение на `Button`, отдельно или в сочетании с текстом. Можно также указать расположение изображения и текста.

`Image` Свойство имеет тип [ `FileImageSource` ](xref:Xamarin.Forms.FileImageSource), что означает, что точечные рисунки должны храниться в качестве ресурсов в проекты для отдельных платформ, а не в проекте библиотеки .NET Standard.

Каждой платформы, поддерживаемых Xamarin.Forms позволяет образов для сохранения в несколько размеров для различных разрешением различных устройств, которые приложение может работать на. Эти несколько точечные рисунки имеют с именем или хранятся таким образом, что операционной системы можно выбрать наиболее подходящий для видео устройство разрешением экрана.

Для изображения на `Button`, наилучший размер обычно между 32- и 64 аппаратно независимых единицах, в зависимости от размера вам хотелось бы быть. Образы, используемые в этом примере основаны на размером 48 аппаратно независимых единицах.

В проекте iOS **ресурсы** папка содержит три размера этого изображения:

- Хранятся в виде квадрата точечный рисунок 48 пикселей **/Resources/MonkeyFace.png**
- Хранятся в виде квадрата точечный рисунок 96 пикселей **/Resource/MonkeyFace@2x.png**
- Хранятся в виде квадрата точечный рисунок 144 пикселей **/Resource/MonkeyFace@3x.png**

Все три битовые карты были переданы **действие при построении** из **BundleResource**.

Для проекта Android, все точечные рисунки имеют одинаковые имена, но они хранятся в различных подпапках **ресурсы** папку:

- Хранятся в виде квадрата точечный рисунок 72 пикселя **/Resources/drawable-hdpi/MonkeyFace.png**
- Хранятся в виде квадрата точечный рисунок 96 пикселей **/Resources/drawable-xhdpi/MonkeyFace.png**
- 144-square точек хранятся в виде **/Resources/drawable-xxhdpi/MonkeyFace.png**
- 192-square точек хранятся в виде **/Resources/drawable-xxxhdpi/MonkeyFace.png**

Они были переданы **действие при построении** из **AndroidResource**.

В проекте UWP, растровые изображения можно хранить в любом месте проекта, но обычно они хранятся в пользовательской папке или **активы** существующую папку. Проект универсальной платформы Windows содержит эти точечные рисунки:

- Хранятся в виде квадрата точечный рисунок 48 пикселей **/Assets/MonkeyFace.scale-100.png**
- Хранятся в виде квадрата точечный рисунок 96 пикселей **/Assets/MonkeyFace.scale-200.png**
- 192-square точек хранятся в виде **/Assets/MonkeyFace.scale-400.png**

Все они были переданы **действие при построении** из **содержимого**.

Можно указать как `Text` и `Image` свойства, располагаются на `Button` с помощью [ `ContentLayout` ](xref:Xamarin.Forms.Button.ContentLayout) свойство `Button`. Это свойство имеет тип [ `ButtonContentLayout` ](xref:Xamarin.Forms.Button.ButtonContentLayout), который является классом, внедренные в `Button`. [Конструктор](xref:Xamarin.Forms.Button.ButtonContentLayout.%23ctor(Xamarin.Forms.Button.ButtonContentLayout.ImagePosition,System.Double)) имеет два аргумента:

- Является членом [ `ImagePosition` ](xref:Xamarin.Forms.Button.ButtonContentLayout.ImagePosition) перечисления: `Left`, `Top`, `Right`, или `Bottom` , указывающее, как растровое изображение отображается относительно текста.
- Объект `double` интервалы между точечный рисунок и текст.

По умолчанию используются `Left` и 10 единиц. Два свойства только для чтения из `ButtonContentLayout` с именем [ `Position` ](xref:Xamarin.Forms.Button.ButtonContentLayout.Position) и [ `Spacing` ](xref:Xamarin.Forms.Button.ButtonContentLayout.Spacing) предоставить значения этих свойств.

В коде, можно создать `Button` и задайте `ContentLayout` свойства следующим образом:

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

В XAML вы должны указать только члена перечисления или интервалы или в любом порядке через запятую:

```xaml
<Button Text="button text"
        Image="image filename"
        ContentLayout="Right, 20" />
```

**Демонстрация изображение кнопки** странице использует `OnPlatform` для указания различных имен файлов, для iOS, Android и универсальной платформы Windows файлы точечных рисунков. Если вы хотите использовать это имя для каждой платформы и избегайте использования `OnPlatform`, вам потребуется для хранения растровых изображений универсальной платформы Windows в корневом каталоге проекта.

Первый `Button` на **Демонстрация изображение кнопки** странице наборы `Image` свойство, но не `Text` свойство:

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

Если точечные рисунки универсальной платформы Windows хранятся в корневом каталоге проекта, можно значительно упростить эту разметку:

```xaml
<Button Image="MonkeyFace.png" />
```

Чтобы избежать много повторяющихся разметки в **ImageButtonDemo.xaml** файл, неявным `Style` также определяется для задания `Image` свойство. Это `Style` автоматически применяется к пяти других `Button` элементов. Ниже приведен полный файл XAML.

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

Окончательный четыре `Button` элементы сделать использование `ContentLayout` свойство, чтобы указать положение и расстояние между текстом и точечный рисунок:

[![Изображение Демонстрация кнопки](button-images/ImageButtonDemo.png "изображения Демонстрация кнопки")](button-images/ImageButtonDemo-Large.png#lightbox)

Теперь вы знаете о вариантах, которые можно обрабатывать `Button` события и изменение `Button` внешний вид.

## <a name="related-links"></a>Связанные ссылки

- [Пример ButtonDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos)
- [Кнопка API](xref:Xamarin.Forms.Button)
