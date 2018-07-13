---
title: Привязка преобразователей значений Xamarin.Forms
description: В этой статье объясняется, как приведение или преобразование значений в пределах привязку данных Xamarin.Forms путем реализации преобразователя значений (который также называется преобразователь привязки или преобразователь значений привязки).
ms.prod: xamarin
ms.assetid: 02B1BBE6-D804-490D-BDD4-8ACED8B70C92
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 28892692133020de1fa5a6eb007bb3f9bcf2612b
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997489"
---
# <a name="xamarinforms-binding-value-converters"></a>Привязка преобразователей значений Xamarin.Forms

Данные привязки обычно передачи данных из свойства источника к целевому свойству и в некоторых случаях из целевого свойства к свойству источника. Эта передача проста в том случае, когда исходные и целевые свойства того же типа или когда один тип может быть преобразован в тип путем неявного преобразования типов. Если это не так, преобразование типов необходимо будет выполнить.

В [ **форматирования строки** ](string-formatting.md) статье было показано, как можно использовать `StringFormat` свойство привязки данных для преобразования любого типа в строку. Для других типов преобразований, необходимо написать специализированный код в класс, реализующий [ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter) интерфейс. (Для универсальной платформы Windows содержит аналогичные класс с именем [ `IValueConverter` ](/uwp/api/Windows.UI.Xaml.Data.IValueConverter/) в `Windows.UI.Xaml.Data` пространства имен, но это `IValueConverter` в `Xamarin.Forms` пространства имен.) Классы, реализующие `IValueConverter` называются *значение преобразователи*, но они часто называются *привязка преобразователей* или *привязка преобразователей значений*.

## <a name="the-ivalueconverter-interface"></a>Интерфейс IValueConverter

Предположим, что вы хотите определить привязку данных, где свойство источника имеет тип `int` , но целевым свойством является `bool`. Требуется, чтобы эта привязка данных для создания `false` значение в источнике целое число равно 0, и `true` в противном случае.  

Это можно сделать с помощью класса, реализующего `IValueConverter` интерфейса:

```csharp
public class IntToBoolConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (int)value != 0;
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (bool)value ? 1 : 0;
    }
}
```

Настройка экземпляра этого класса [ `Converter` ](xref:Xamarin.Forms.Binding.Converter) свойство `Binding` класса или [ `Converter` ](xref:Xamarin.Forms.Xaml.BindingExtension.Converter) свойство `Binding` расширение разметки. Этот класс становится частью привязки данных.

`Convert` Метод вызывается при перемещении данных из источника в целевой объект в `OneWay` или `TwoWay` привязки. `value` Параметр — это объект или значение источника привязки данных. Метод должен возвращать значение типа целевого объекта привязки данных. Метод, показанный здесь приведения `value` параметр `int` и затем сравнивает их с 0 для `bool` возвращаемое значение.

`ConvertBack` Метод вызывается, когда данные перемещаются из целевой источник в `TwoWay` или `OneWayToSource` привязки. `ConvertBack` выполняет обратное преобразование: предполагается `value` параметр `bool` от целевого объекта и преобразует ее в `int` возвращаемое значение для источника.

Если привязка данных также содержит `StringFormat` задание, преобразователь значений вызывается прежде, чем результат форматируется как строка.

**Включить кнопки** странице в [ **демонстрации привязки данных** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) образце показано, как использовать этот преобразователь значений в привязке данных. `IntToBoolConverter` Создается в словаре ресурсов страницы. Затем он указывается с помощью `StaticResource` расширение разметки, чтобы задать `Converter` свойства привязки данных. Широко распространено для совместного использования преобразователей данных среди нескольких привязок данных на странице:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.EnableButtonsPage"
             Title="Enable Buttons">
    <ContentPage.Resources>
        <ResourceDictionary>
            <local:IntToBoolConverter x:Key="intToBool" />
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Padding="10, 0">
        <Entry x:Name="entry1"
               Text=""
               Placeholder="enter search term"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Search"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                IsEnabled="{Binding Source={x:Reference entry1},
                                    Path=Text.Length,
                                    Converter={StaticResource intToBool}}" />

        <Entry x:Name="entry2"
               Text=""
               Placeholder="enter destination"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Submit"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                IsEnabled="{Binding Source={x:Reference entry2},
                                    Path=Text.Length,
                                    Converter={StaticResource intToBool}}" />
    </StackLayout>
</ContentPage>
```

Если преобразователь значений используется на нескольких страницах приложения, его можно создать в словаре ресурсов в **App.xaml** файл.

**Включить кнопки** страница демонстрирует общий требуется, когда `Button` выполняет операцию на основе текста, который пользователь вводит в `Entry` представления. Если ничего не было введено в `Entry`, `Button` должен быть отключен. Каждый `Button` содержит привязку данных на его `IsEnabled` свойство. Источником привязки данных является `Length` свойство `Text` свойства соответствующего элемента `Entry`. Если это `Length` свойство не возвращает 0, преобразователь значений `true` и `Button` включен:

[![Включение кнопки](converters-images/enablebuttons-small.png "включить кнопки")](converters-images/enablebuttons-large.png#lightbox "включить кнопки")

Обратите внимание, что `Text` свойство в каждом `Entry` инициализируется пустой строкой. `Text` Свойство `null` по умолчанию, а данные привязки не будет работать в этом случае.

Некоторые преобразователи значений созданы специально для конкретных приложений, пока другие обобщены. Если вы знаете, что преобразователь значений будет использоваться только в `OneWay` привязки, а затем `ConvertBack` метод может просто возвращать `null`.

`Convert` Неявно в приведенном выше примере предполагается, что `value` аргумент имеет тип `int` и возвращаемое значение должно быть типа `bool`. Аналогичным образом `ConvertBack` метод предполагает, что `value` аргумент имеет тип `bool` и возвращается значение `int`. Если это не так, возникнет исключение времени выполнения.

Вы можете написать преобразователи значений была более обобщена и принимать несколько различных типов данных. `Convert` И `ConvertBack` можно использовать методы `as` или `is` операторы с `value` параметра, либо путем вызова `GetType` по этому параметру, чтобы определить, что-то его тип, а затем выполните соответствующие. Ожидаемый тип возвращаемого значения для каждого метода задается `targetType` параметра. В некоторых случаях преобразователи значений используются с привязками данных разных целевых типов; преобразователь значений можно использовать `targetType` аргумент для выполнения преобразования для правильного типа.

Если выполняется преобразование отличается для разных языков и региональных параметров, используйте `culture` параметр для этой цели. `parameter` Аргумент `Convert` и `ConvertBack` рассматривается далее в этой статье.

## <a name="binding-converter-properties"></a>Преобразователь свойств привязки

Класса преобразователя значений может иметь свойства и универсальных параметров. Преобразует этот преобразователь определенное значение `bool` из источника для объекта типа `T` для целевого объекта:

```csharp
public class BoolToObjectConverter<T> : IValueConverter
{
    public T TrueObject { set; get; }

    public T FalseObject { set; get; }

    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (bool)value ? TrueObject : FalseObject;
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return ((T)value).Equals(TrueObject);
    }
}
```

**Индикаторы коммутатора** странице рассказывается, как он может использоваться для отображения значения `Switch` представления. Несмотря на то, что характерно для создания экземпляра преобразователи значений как ресурсы в словаре ресурсов, эта страница показывает альтернативы: преобразователь значения создается между `Binding.Converter` теги элемента свойства. `x:TypeArguments` Указывает универсальный аргумент, и `TrueObject` и `FalseObject` задаются для объектов этого типа:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.SwitchIndicatorsPage"
             Title="Switch Indicators">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="FontSize" Value="18" />
                <Setter Property="VerticalOptions" Value="Center" />
            </Style>

            <Style TargetType="Switch">
                <Setter Property="VerticalOptions" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Padding="10, 0">
        <StackLayout Orientation="Horizontal"
                     VerticalOptions="CenterAndExpand">
            <Label Text="Subscribe?" />
            <Switch x:Name="switch1" />
            <Label>
                <Label.Text>
                    <Binding Source="{x:Reference switch1}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="x:String"
                                                         TrueObject="Of course!"
                                                         FalseObject="No way!" />
                        </Binding.Converter>
                    </Binding>
                </Label.Text>
            </Label>
        </StackLayout>

        <StackLayout Orientation="Horizontal"
                     VerticalOptions="CenterAndExpand">
            <Label Text="Allow popups?" />
            <Switch x:Name="switch2" />
            <Label>
                <Label.Text>
                    <Binding Source="{x:Reference switch2}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="x:String"
                                                         TrueObject="Yes"
                                                         FalseObject="No" />
                        </Binding.Converter>
                    </Binding>
                </Label.Text>
                <Label.TextColor>
                    <Binding Source="{x:Reference switch2}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="Color"
                                                         TrueObject="Green"
                                                         FalseObject="Red" />
                        </Binding.Converter>
                    </Binding>
                </Label.TextColor>
            </Label>
        </StackLayout>

        <StackLayout Orientation="Horizontal"
                     VerticalOptions="CenterAndExpand">
            <Label Text="Learn more?" />
            <Switch x:Name="switch3" />
            <Label FontSize="18"
                   VerticalOptions="Center">
                <Label.Style>
                    <Binding Source="{x:Reference switch3}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="Style">
                                <local:BoolToObjectConverter.TrueObject>
                                    <Style TargetType="Label">
                                        <Setter Property="Text" Value="Indubitably!" />
                                        <Setter Property="FontAttributes" Value="Italic, Bold" />
                                        <Setter Property="TextColor" Value="Green" />
                                    </Style>                                    
                                </local:BoolToObjectConverter.TrueObject>

                                <local:BoolToObjectConverter.FalseObject>
                                    <Style TargetType="Label">
                                        <Setter Property="Text" Value="Maybe later" />
                                        <Setter Property="FontAttributes" Value="None" />
                                        <Setter Property="TextColor" Value="Red" />
                                    </Style>
                                </local:BoolToObjectConverter.FalseObject>
                            </local:BoolToObjectConverter>
                        </Binding.Converter>
                    </Binding>
                </Label.Style>
            </Label>
        </StackLayout>
    </StackLayout>
</ContentPage>
```

За последние три `Switch` и `Label` пары, универсальный аргумент имеет значение `Style`и весь `Style` объектов предоставляются для значений `TrueObject` и `FalseObject`. Они переопределяют неявный стиль для `Label` задаются в словаре ресурсов, поэтому свойства в стиле, явно назначенные `Label`. Включение и выключение `Switch` вызывает соответствующий `Label` в соответствии с изменениями:

[![Переключение индикаторы](converters-images/switchindicators-small.png "переключения индикаторы")](converters-images/switchindicators-large.png#lightbox "переключения индикаторы")

Можно также использовать [ `Triggers` ](~/xamarin-forms/app-fundamentals/triggers.md) реализовать аналогичные изменения в интерфейсе пользователя, на основе других представлений.

## <a name="binding-converter-parameters"></a>Привязка параметров преобразователя

`Binding` Класс определяет [ `ConverterParameter` ](xref:Xamarin.Forms.Binding.ConverterParameter) свойство и `Binding` расширения разметки также определяет [ `ConverterParameter` ](xref:Xamarin.Forms.Xaml.BindingExtension.ConverterParameter) свойство. Если это свойство имеет значение, то значение, передаваемое в `Convert` и `ConvertBack` методы, как `parameter` аргумент. Даже если экземпляр преобразователя значений разделяется между несколькими привязками данных, `ConverterParameter` могут быть разными для выполнения преобразований, немного отличаются.

Использование `ConverterParameter` демонстрируется программы выделения цветом. В этом случае `RgbColorViewModel` имеет три свойства типа `double` с именем `Red`, `Green`, и `Blue` , используемого для создания `Color` значение:

```csharp
public class RgbColorViewModel : INotifyPropertyChanged
{
    Color color;
    string name;

    public event PropertyChangedEventHandler PropertyChanged;

    public double Red
    {
        set
        {
            if (color.R != value)
            {
                Color = new Color(value, color.G, color.B);
            }
        }
        get
        {
            return color.R;
        }
    }

    public double Green
    {
        set
        {
            if (color.G != value)
            {
                Color = new Color(color.R, value, color.B);
            }
        }
        get
        {
            return color.G;
        }
    }

    public double Blue
    {
        set
        {
            if (color.B != value)
            {
                Color = new Color(color.R, color.G, value);
            }
        }
        get
        {
            return color.B;
        }
    }

    public Color Color
    {
        set
        {
            if (color != value)
            {
                color = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Red"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Green"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Blue"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Color"));

                Name = NamedColor.GetNearestColorName(color);
            }
        }
        get
        {
            return color;
        }
    }

    public string Name
    {
        private set
        {
            if (name != value)
            {
                name = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Name"));
            }
        }
        get
        {
            return name;
        }
    }
}
```

`Red`, `Green`, И `Blue` свойства диапазон от 0 до 1. Тем не менее вы предпочитаете, что компоненты отображается как шестнадцатеричные значения из двух цифр.

Чтобы отобразить их в виде шестнадцатеричных значений в XAML, их должен быть умноженное на 255, преобразовать в целое число и затем в формате со спецификацией «X2» в `StringFormat` свойство. Преобразователь значений могут быть обработаны первые две задачи (умножения 255 и преобразование в целое число). Чтобы сделать преобразователь значений, как универсальная максимально, можно указать множитель с `ConverterParameter` свойство, которое означает, что он вводит `Convert` и `ConvertBack` методы, как `parameter` аргумент:

```csharp
public class DoubleToIntConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (int)Math.Round((double)value * GetParameter(parameter));
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (int)value / GetParameter(parameter);
    }

    double GetParameter(object parameter)
    {
        if (parameter is double)
            return (double)parameter;

        else if (parameter is int)
            return (int)parameter;

        else if (parameter is string)
            return double.Parse((string)parameter);

        return 1;
    }
}
```

`Convert` Преобразует из `double` для `int` при умножения `parameter` значение; `ConvertBack` делит целое число `value` аргумент по `parameter` и возвращает `double` результат. (В программе, показано ниже, преобразователь значений используется только в форматирование строк, поэтому `ConvertBack` не используется.)

Тип `parameter` аргумент может отличаться в зависимости от того, определен ли привязка данных в коде или в XAML. Если `ConverterParameter` свойство `Binding` задается в коде, вполне вероятно, будет присвоено числовое значение:

```csharp
binding.ConverterParameter = 255;
```

`ConverterParameter` Свойство имеет тип `Object`, поэтому компилятор C# интерпретирует литерал 255 как целое и задает значение свойства.

В XAML, однако `ConverterParameter` вероятнее всего задать следующим образом:

```xaml
<Label Text="{Binding Red,
                      Converter={StaticResource doubleToInt},
                      ConverterParameter=255,
                      StringFormat='Red = {0:X2}'}" />
```

255 выглядит как число, но, поскольку `ConverterParameter` имеет тип `Object`, средство синтаксического анализа XAML обрабатывает 255 как строка.

По этой причине преобразователь значений, показанный выше включает отдельный `GetParameter` метод, который обрабатывает случаи для `parameter` оно принадлежит к типу `double`, `int`, или `string`.  

**Выбора цвета RGB** создает страницу `DoubleToIntConverter` в словаре ресурсов после определения двух неявные стили:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.RgbColorSelectorPage"
             Title="RGB Color Selector">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Slider">
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>

            <local:DoubleToIntConverter x:Key="doubleToInt" />
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <StackLayout.BindingContext>
            <local:RgbColorViewModel Color="Gray" />
        </StackLayout.BindingContext>

        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <StackLayout Margin="10, 0">
            <Label Text="{Binding Name}" />

            <Slider Value="{Binding Red}" />
            <Label Text="{Binding Red,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Red = {0:X2}'}" />

            <Slider Value="{Binding Green}" />
            <Label Text="{Binding Green,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Green = {0:X2}'}" />

            <Slider Value="{Binding Blue}" />
            <Label>
                <Label.Text>
                    <Binding Path="Blue"
                             StringFormat="Blue = {0:X2}"
                             Converter="{StaticResource doubleToInt}">
                        <Binding.ConverterParameter>
                            <x:Double>255</x:Double>
                        </Binding.ConverterParameter>
                    </Binding>
                </Label.Text>
            </Label>
        </StackLayout>
    </StackLayout>
</ContentPage>    
```

Значения `Red` и `Green` свойства отображаются с `Binding` расширение разметки. `Blue` Свойство, тем не менее, создает `Binding` класс для демонстрации способа явно `double` может быть присвоено значение `ConverterParameter` свойства.

Ниже приведен результат.

[![Средство выбора цвета RGB](converters-images/rgbcolorselector-small.png "выбора цвета RGB")](converters-images/rgbcolorselector-large.png#lightbox "выбора цвета RGB")


## <a name="related-links"></a>Связанные ссылки

- [Демонстрации, привязка данных (пример)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Данные привязки Глава из книги по Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
