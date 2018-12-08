---
title: Xamarin.Forms "ползунок"
description: Ползунок Xamarin.Forms — горизонтальную панель, могут управляться пользователю выбрать значение типа double из непрерывного диапазона. В этой статье объясняется, как использовать класс "ползунок" позволяет выбрать значение из диапазона непрерывных значений.
ms.prod: xamarin
ms.assetid: 36B1C645-26E0-4874-B6B6-BDBF77662878
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: ac5ad85f7bdd08b0d071e7c333c959e94dcbc53f
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53057497"
---
# <a name="xamarinforms-slider"></a>Xamarin.Forms "ползунок"

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos)

_Используйте ползунок для выбора из диапазона непрерывных значений._

Xamarin.Forms [ `Slider` ](xref:Xamarin.Forms.Slider) является горизонтальную панель, можно управлять, пользователь может выбрать `double` значение из непрерывного диапазона.

`Slider` Определяет трех свойств типа `double`:

- [`Minimum`](xref:Xamarin.Forms.Slider.Minimum) — Это минимальная диапазон со значением по умолчанию 0.
- [`Maximum`](xref:Xamarin.Forms.Slider.Maximum) — Это максимальное значение диапазона, со значением по умолчанию 1.
- [`Value`](xref:Xamarin.Forms.Slider.Value) имеет значение ползунка, что допустимы значения от `Minimum` и `Maximum` и имеет значение по умолчанию 0.

Все три свойства, обеспечиваются `BindableProperty` объектов. `Value` Свойство имеет режим привязки по умолчанию `BindingMode.TwoWay`, что означает, что это подходит в качестве источника привязки в приложение, использующее [Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) архитектуры.

> [!WARNING]
> На внутреннем уровне `Slider` гарантирует, что `Minimum` — меньше, чем `Maximum`. Если `Minimum` или `Maximum` когда-либо заданы таким образом, чтобы `Minimum` — не меньше `Maximum`, возникает исключение. См. в разделе [ **меры предосторожности** ](#precautions) приведенном ниже разделе Дополнительные сведения об настройке `Minimum` и `Maximum` свойства.

`Slider` Приводит `Value` свойство, чтобы оно было между `Minimum` и `Maximum`включительно. Если `Minimum` свойству присваивается значение больше, чем `Value` свойство, `Slider` задает `Value` свойства `Minimum`. Аналогично Если `Maximum` присвоено значение меньше, чем `Value`, затем `Slider` задает `Value` свойства `Maximum`.

`Slider` Определяет [ `ValueChanged` ](xref:Xamarin.Forms.Slider.ValueChanged) событие, возникающее, когда `Value` изменения, либо с помощью операции пользователя из `Slider` или когда программа устанавливает `Value` свойство напрямую. Объект `ValueChanged` событие также инициируется, когда `Value` приводится как описано в предыдущем абзаце.

[ `ValueChangedEventArgs` ](xref:Xamarin.Forms.ValueChangedEventArgs) Объект, который прилагается к `ValueChanged` событий имеет два свойства: тип `double`: [ `OldValue` ](xref:Xamarin.Forms.ValueChangedEventArgs.OldValue) и [ `NewValue` ](xref:Xamarin.Forms.ValueChangedEventArgs.NewValue). Во время события, значение `NewValue` совпадает со значением `Value` свойство `Slider` объекта.

> [!WARNING]
> Не используйте параметры неограниченного горизонтальная разметка `Center`, `Start`, или `End` с `Slider`. В Android и UWP `Slider` сворачивает в строку нулевой длины, а также на iOS, панели очень мал. Сохраните значение по умолчанию `HorizontalOptions` параметр `Fill`и не используйте в ширину `Auto` при помещении `Slider` в `Grid` макета.

`Slider` Также определяет несколько свойств, которые влияют на его внешний вид:

- [`MinimumTrackColor`](xref:Xamarin.Forms.Slider.MinimumTrackColorProperty) панель цветов слева от бегунка.
- [`MaximumTrackColor`](xref:Xamarin.Forms.Slider.MaximumTrackColorProperty) панель Цвет правой стороны от бегунка.
- [`ThumbColor`](xref:Xamarin.Forms.Slider.ThumbColorProperty) Это цвет бегунка.
- [`ThumbImage`](xref:Xamarin.Forms.Slider.ThumbImageProperty) Представляет изображение, используемое для ползунка, тип [ `FileImageSource` ](xref:Xamarin.Forms.FileImageSource).

> [!NOTE]
> `ThumbColor` И `ThumbImage` свойства являются взаимоисключающими. Если заданы оба свойства, `ThumbImage` свойство будет иметь приоритет.

## <a name="basic-slider-code-and-markup"></a>Базовый код ползунок и разметки

[ **SliderDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos) пример начинается с тремя страницами, которые функционально идентичны, но реализуются по-разному. На первой странице используется только код C#, во втором используется XAML с помощью обработчика событий в коде и третий — это возможность избежать обработчик событий с помощью привязки данных в файле XAML.

### <a name="creating-a-slider-in-code"></a>Создание ползунка в коде

**Базовый код ползунок** странице в [ **SliderDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos) образце показано, чтобы создать `Slider` и два `Label` объектов в коде:

```csharp
public class BasicSliderCodePage : ContentPage
{
    public BasicSliderCodePage()
    {
        Label rotationLabel = new Label
        {
            Text = "ROTATING TEXT",
            FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)),
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        Label displayLabel = new Label
        {
            Text = "(uninitialized)",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        Slider slider = new Slider
        {
            Maximum = 360
        };
        slider.ValueChanged += (sender, args) =>
        {
            rotationLabel.Rotation = slider.Value;
            displayLabel.Text = String.Format("The Slider value is {0}", args.NewValue);
        };

        Title = "Basic Slider Code";
        Padding = new Thickness(10, 0);
        Content = new StackLayout
        {
            Children =
            {
                rotationLabel,
                slider,
                displayLabel
            }
        };
    }
}
```

`Slider` Инициализируется иметь `Maximum` свойство 360. `ValueChanged` Обработчик `Slider` использует `Value` свойство `slider` объект для задания `Rotation` свойства первого `Label` и использует `String.Format` метод с `NewValue` свойство аргументы события, чтобы задать `Text` свойство второго `Label`. Эти два подхода, чтобы получить текущее значение `Slider` являются взаимозаменяемыми.

Ниже приведен программу на iOS, Android и универсальной платформы Windows (UWP) устройств.

[![Ползунок базовый код](slider-images/BasicSliderCode.png "кода основные \"ползунок\"")](slider-images/BasicSliderCode-Large.png#lightbox)

Второй `Label` отображается текст «(неинициализированные)» до `Slider` будут обрабатывать, который вызывает первый `ValueChanged` , возникает событие. Обратите внимание на то, что число десятичных разрядов, выводимых отличается для каждой платформы. Эти различия, относящиеся к реализации платформы `Slider` и рассматриваются далее в этой статье в разделе [различий в реализации платформы](#implementations).

### <a name="creating-a-slider-in-xaml"></a>Создание ползунка в XAML

**Базовые XAML ползунок** страница функционально является таким же, как **базовый код ползунок** но реализуется, главным образом в XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SliderDemos.BasicSliderXamlPage"
             Title="Basic Slider XAML"
             Padding="10, 0">
    <StackLayout>
        <Label x:Name="rotatingLabel"
               Text="ROTATING TEXT"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider Maximum="360"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="displayLabel"
               Text="(uninitialized)"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

Файл кода содержит обработчик `ValueChanged` событий:

```csharp
public partial class BasicSliderXamlPage : ContentPage
{
    public BasicSliderXamlPage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        double value = args.NewValue;
        rotatingLabel.Rotation = value;
        displayLabel.Text = String.Format("The Slider value is {0}", value);
    }
}
```

Можно также для обработчика событий для получения `Slider` , он генерирует события с помощью `sender` аргумент. `Value` Свойство содержит текущее значение:

```csharp
double value = ((Slider)sender).Value;
```

Если `Slider` объекта были присваивается имя в файле XAML с `x:Name` атрибут (например, «ползунок»), а затем обработчик событий может ссылаться на этот объект напрямую:

```csharp
double value = slider.Value;
```

### <a name="data-binding-the-slider"></a>Ползунок привязки данных

**Основных привязок ползунок** странице показано, как написать практически эквивалентные программу, которая исключает `Value` обработчик событий с помощью [привязки данных](~/xamarin-forms/app-fundamentals/data-binding/index.md):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SliderDemos.BasicSliderBindingsPage"
             Title="Basic Slider Bindings"
             Padding="10, 0">
    <StackLayout>
        <Label Text="ROTATING TEXT"
               Rotation="{Binding Source={x:Reference slider},
                                  Path=Value}"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Maximum="360" />

        <Label x:Name="displayLabel"
               Text="{Binding Source={x:Reference slider},
                              Path=Value,
                              StringFormat='The Slider value is {0:F0}'}"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

`Rotation` Свойства первого `Label` привязан к `Value` свойство `Slider`, так как `Text` свойство второго `Label` с `StringFormat` спецификации. **Основных привязок ползунок** странице функций немного по-разному на двух предыдущих страницах: при первом отображении страницы, второй `Label` отображает текстовую строку со значением. Это является преимуществом с использованием привязки данных. Для отображения текста без привязки данных, необходимо специально инициализировать `Text` свойство `Label` или имитировать срабатывание `ValueChanged` событие путем вызова обработчика событий из конструктора класса.

<a name="precautions" />

## <a name="precautions"></a>Меры предосторожности

Значение `Minimum` свойства всегда должно быть меньше, чем значение `Maximum` свойства. В следующем примере кода причины фрагмент `Slider` для вызова исключения:

```csharp
// Throws an exception!
Slider slider = new Slider
{
    Minimum = 10,
    Maximum = 20
};
```

Компилятор C# создает код, который задает эти два свойства в последовательности, и когда `Minimum` свойство имеет значение 10, больше, чем значение по умолчанию `Maximum` значение 1. В этом случае исключения можно избежать, задав `Maximum` свойства первого:

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

Установка `Maximum` 20 проблема не возникает потому, что больше, чем значение по умолчанию `Minimum` значение 0. Когда `Minimum` не установлен, значение меньше, чем `Maximum` значение 20.

Та же проблема существует в XAML. Задайте свойства в порядке, который гарантирует, что `Maximum` был всегда больше, чем `Minimum`:

```xaml
<Slider Maximum="20"
        Minimum="10" ... />
```

Можно задать `Minimum` и `Maximum` значения для отрицательных чисел, но только в порядке, где `Minimum` — всегда меньше, чем `Maximum`:

```xaml
<Slider Minimum="-20"
        Maximum="-10" ... />
```

`Value` Свойство всегда имеет больше или равно `Minimum` значение и меньше или равно `Maximum`. Если `Value` задано значение за пределами этого диапазона, значения будут приведены к находиться в диапазоне, но исключения не было. Например, этот код будет *не* исключение:

```csharp
Slider slider = new Slider
{
    Value = 10
};
```

Вместо этого `Value` приводится к типу `Maximum` значение 1.

Ниже приведен фрагмент кода, показанный выше.

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

Когда `Minimum` равно 10, затем `Value` также имеет значение 10.

Если `ValueChanged` во время был присоединен обработчик событий, `Value` приводится отличное значение по умолчанию 0, то `ValueChanged` события. Ниже приведен фрагмент XAML.

```xaml
<Slider ValueChanged="OnSliderValueChanged"
        Maximum="20"
        Minimum="10" />
```

Когда `Minimum` равно 10, `Value` также имеет значение 10 и `ValueChanged` события. Это может произойти, прежде чем остальной части страницы был построен, и обработчик может пытаться ссылаться на другие элементы на странице, которые еще не созданы. Может потребоваться добавить код для `ValueChanged` обработчик, который проверяет наличие `null` значений других элементов на странице. Или можно задать `ValueChanged` обработчик событий после `Slider` были инициализированы значениями.

<a name="implementations" />

## <a name="platform-implementation-differences"></a>Различия в реализации платформы

Снимки экрана, показанный ранее отображения значения `Slider` с Разное количество десятичных запятых. Это относится к как `Slider` реализуется на платформах Android и UWP.

### <a name="the-android-implementation"></a>Реализация Android

Android реализация `Slider` основан на Android [ `SeekBar` ](https://developer.xamarin.com/api/type/Android.Widget.SeekBar/) всегда устанавливается [ `Max` ](https://developer.xamarin.com/api/property/Android.Widget.ProgressBar.Max/) свойство до 1000. Это означает, что `Slider` в Android имеет только 1001 дискретные значения. Если задать `Slider` иметь `Minimum` 0 и `Maximum` 5000, а затем как `Slider` будут обрабатывать, `Value` свойство может принимать значения от 0, 5, 10, 15 и т. д.

### <a name="the-uwp-implementation"></a>Реализация универсальной платформы Windows

Реализация UWP `Slider` основана на платформе UWP [ `Slider` ](/uwp/api/windows.ui.xaml.controls.slider) элемента управления. `StepFrequency` Свойство UWP `Slider` присваивается разность `Maximum` и `Minimum` свойства, разделенного на 10, но не больше 1.

Например, для диапазона от 0 до 1, по умолчанию `StepFrequency` свойство имеет значение 0.1. Как `Slider` будут обрабатывать, `Value` свойство будет ограничен 0, 0.1, 0.2, 0.3, 0,4, 0,5, 0.6, 0,7, 0.8, 0.9 и 1.0. (Это видно в последней страницы в [ **SliderDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos) пример.) Когда разница между `Maximum` и `Minimum` свойства 10 или более поздней версии, затем `StepFrequency` имеет значение 1 и `Value` имеет целочисленного значения.

### <a name="the-stepslider-solution"></a>Решение StepSlider

Более универсальным `StepSlider` рассматривается в [Глава 27. Пользовательские отрисовщики](https://xamarin.azureedge.net/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf) книги *Создание мобильных приложений с помощью Xamarin.Forms*. `StepSlider` Аналогичен `Slider` , но добавляет `Steps` свойство, чтобы указать количество значений между `Minimum` и `Maximum`.

## <a name="sliders-for-color-selection"></a>Ползунки для выбранного цвета

Последние два страниц в [ **SliderDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos) пример, как использовать три `Slider` экземпляры для выбранного цвета. Первой страницы обрабатывает все взаимодействия в файле кода, в то время второй странице показано, как использовать привязку данных с помощью модели представления.

### <a name="handling-sliders-in-the-code-behind-file"></a>Ползунки обработки в файл с выделенным кодом

**Ползунки цвета RGB** создает страницу `BoxView` для отображения цвета, три `Slider` экземпляров для выбора красного, зеленого и синего компонентов цвета и три `Label` элементов для отображения этих цвет значения:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SliderDemos.RgbColorSlidersPage"
             Title="RGB Color Sliders">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Slider">
                <Setter Property="Maximum" Value="255" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10">
        <BoxView x:Name="boxView"
                 Color="Black"
                 VerticalOptions="FillAndExpand" />

        <Slider x:Name="redSlider"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="redLabel" />

        <Slider x:Name="greenSlider"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="greenLabel" />

        <Slider x:Name="blueSlider"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="blueLabel" />
    </StackLayout>
</ContentPage>
```

Объект `Style` предоставляет все три `Slider` элементов в диапазоне от 0 до 255. `Slider` Элементы одного и того же `ValueChanged` обработчик, который реализован в файле кода:

```csharp
public partial class RgbColorSlidersPage : ContentPage
{
    public RgbColorSlidersPage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if (sender == redSlider)
        {
            redLabel.Text = String.Format("Red = {0:X2}", (int)args.NewValue);
        }
        else if (sender == greenSlider)
        {
            greenLabel.Text = String.Format("Green = {0:X2}", (int)args.NewValue);
        }
        else if (sender == blueSlider)
        {
            blueLabel.Text = String.Format("Blue = {0:X2}", (int)args.NewValue);
        }

        boxView.Color = Color.FromRgb((int)redSlider.Value,
                                      (int)greenSlider.Value,
                                      (int)blueSlider.Value);
    }
}
```

Первый раздел наборы `Text` свойство одного из `Label` экземпляров короткая строка текста, указывающее значение `Slider` в шестнадцатеричном формате. Затем все три `Slider` экземпляры доступны для создания `Color` значение RGB компонентов:

[![Ползунки цвета RGB](slider-images/RgbColorSliders.png "RGB шкалы")](slider-images/RgbColorSliders-Large.png#lightbox)

### <a name="binding-the-slider-to-a-viewmodel"></a>Привязка ползунка к модели представления

**HSL шкалы** странице показано, как использовать модель представления для выполнения вычислений, используемый для создания `Color` значение на основе значений цветового тона, насыщенности и яркости. Все ViewModels, такие как `HSLColorViewModel` класс реализует `INotifyPropertyChanged` интерфейс и активируется `PropertyChanged` событие при изменении одного из свойств:

```csharp
public class HslColorViewModel : INotifyPropertyChanged
{
    Color color;

    public event PropertyChangedEventHandler PropertyChanged;

    public double Hue
    {
        set
        {
            if (color.Hue != value)
            {
                Color = Color.FromHsla(value, color.Saturation, color.Luminosity);
            }
        }
        get
        {
            return color.Hue;
        }
    }

    public double Saturation
    {
        set
        {
            if (color.Saturation != value)
            {
                Color = Color.FromHsla(color.Hue, value, color.Luminosity);
            }
        }
        get
        {
            return color.Saturation;
        }
    }

    public double Luminosity
    {
        set
        {
            if (color.Luminosity != value)
            {
                Color = Color.FromHsla(color.Hue, color.Saturation, value);
            }
        }
        get
        {
            return color.Luminosity;
        }
    }

    public Color Color
    {
        set
        {
            if (color != value)
            {
                color = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Hue"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Saturation"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Luminosity"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Color"));
            }
        }
        get
        {
            return color;
        }
    }
}
```

ViewModels и `INotifyPropertyChanged` в этой статье обсуждаются интерфейс [привязки данных](~/xamarin-forms/app-fundamentals/data-binding/index.md).

**HslColorSlidersPage.xaml** файл экземпляр `HslColorViewModel` и задает его на страницу `BindingContext` свойство. Благодаря этому все элементы в файле XAML для привязки к свойствам в ViewModel:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:SliderDemos"
             x:Class="SliderDemos.HslColorSlidersPage"
             Title="HSL Color Sliders">

    <ContentPage.BindingContext>
        <local:HslColorViewModel Color="Chocolate" />
    </ContentPage.BindingContext>

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10">
        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <Slider Value="{Binding Hue}" />
        <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />

        <Slider Value="{Binding Saturation}" />
        <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />

        <Slider Value="{Binding Luminosity}" />
        <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
    </StackLayout>
</ContentPage>
```

Как `Slider` элементы управления этим состоянием `BoxView` и `Label` элементы будут обновлены из ViewModel:

[![Ползунки цвета HSL](slider-images/HslColorSliders.png "HSL шкалы")](slider-images/HslColorSliders-Large.png#lightbox)

`StringFormat` Компонент `Binding` расширение разметки настроено для формата «F2» отображение двумя десятичными разрядами. (Строка форматирования в привязках данных рассматривается в статье [форматирования строки](~/xamarin-forms/app-fundamentals/data-binding/string-formatting.md).) Тем не менее значения 0, 0.1, 0.2, ограничено UWP версию программы... 0.9, а 1.0. Это является прямым следствием реализации UWP `Slider` как описано выше в разделе [различий в реализации платформы](#implementations).

## <a name="related-links"></a>Связанные ссылки

- [Пример демонстрации "ползунок"](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos)
- [Ползунок API](xref:Xamarin.Forms.Slider)
