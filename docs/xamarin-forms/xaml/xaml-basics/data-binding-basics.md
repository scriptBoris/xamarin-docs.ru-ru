---
title: Часть 4. Основные сведения о привязке данных
description: Привязки данных позволяют свойства двух объектов должна быть установлена связь, чтобы изменение одного приводит к изменению в другой.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 342288C3-BB4C-4924-B178-72E112D777BA
author: charlespetzold
ms.author: chape
ms.date: 10/25/2017
ms.openlocfilehash: 117ddd033faedda871c33ba10c246739309e2e86
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245954"
---
# <a name="part-4-data-binding-basics"></a>Часть 4. Основные сведения о привязке данных

_Привязки данных позволяют свойства двух объектов должна быть установлена связь, чтобы изменение одного приводит к изменению в другой. Это очень ценным инструментом, и во время привязки данных можно определить полностью в коде, XAML предоставляет ярлыки и удобства. Следовательно одним из наиболее важных расширений разметки в Xamarin.Forms привязки._

## <a name="data-bindings"></a>Привязки данных

Привязки данных соединить свойства двух объектов, который называется *источника* и *целевой*. В коде, требуются два шага: `BindingContext` свойства целевого объекта должно быть присвоено исходный объект и `SetBinding` метод (часто используется в сочетании с `Binding` класс) должен быть вызван для целевого объекта для привязки свойства этого объекта Объект, свойства исходного объекта.

Свойство target должно быть привязываемые свойства, которое означает, что целевой объект должен быть производным от `BindableObject`. В электронной документации Xamarin.Forms указывает, какие свойства являются свойства для привязки. Свойство `Label` например `Text` связан со свойством привязываемых `TextProperty`.

В разметке, необходимо выполнить те же два действия, необходимые в коде, за исключением того, `Binding` расширение разметки занимает место `SetBinding` вызова и `Binding` класса.

Тем не менее, при определении привязки данных в XAML, существует несколько способов установки `BindingContext` целевого объекта. Иногда набор из файла кода, иногда с помощью `StaticResource` или `x:Static` расширения разметки, а иногда содержимое `BindingContext` теги элементов свойства.

Привязки чаще всего используются для подключения визуальных элементов программы с базовой модели данных, обычно в реализации архитектуры приложения MVVM (Model-View-ViewModel), как описано в [. часть 5. От привязок данных для MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md), но возможны и другие сценарии.

## <a name="view-to-view-bindings"></a>Привязки представления

Можно определить привязки данных для связывания свойства из двух представлений на одной странице. В этом случае можно задать `BindingContext` целевой объект с помощью `x:Reference` расширения разметки.

Ниже приведен файл XAML, который содержит `Slider` и два `Label` представления, один из которых поворачиваться `Slider` значение, а другая, которая отображает `Slider` значение:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SliderBindingsPage"
             Title="Slider Bindings Page">

    <StackLayout>
        <Label Text="ROTATION"
               BindingContext="{x:Reference Name=slider}"
               Rotation="{Binding Path=Value}"
               FontAttributes="Bold"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="CenterAndExpand" />

        <Label BindingContext="{x:Reference slider}"
               Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
               FontAttributes="Bold"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

`Slider` Содержит `x:Name` атрибут, на который ссылается два `Label` представления с помощью `x:Reference` расширения разметки.

`x:Reference` Расширения привязки определяет свойство с именем `Name` присваивается имя ссылочного элемента, в этом случае `slider`. Тем не менее `ReferenceExtension` класс, определяющий `x:Reference` расширения разметки также определяет `ContentProperty` для атрибута `Name`, это означает, что это не нужно. Просто для разнообразия первый `x:Reference` включает «имя =» не поддерживает второй:

```csharp
BindingContext="{x:Reference Name=slider}"
…
BindingContext="{x:Reference slider}"
```

`Binding` Расширения разметки, сам может иметь несколько свойств, так же, как `BindingBase` и `Binding` класса. `ContentProperty` Для `Binding` — `Path`, но» путь =» являются частью расширения разметки можно опустить, если путь является первым в `Binding` расширения разметки. Первый пример состоит из «путь =», но во втором примере пропускает его:

```csharp
Rotation="{Binding Path=Value}"
…
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

Свойства могут размещаться на одной строке и разделяются на несколько строк:

```csharp
Text="{Binding Value,
               StringFormat='The angle is {0:F0} degrees'}"
```

Выполните все, что является удобным.

Обратите внимание `StringFormat` свойства во втором `Binding` расширения разметки. В Xamarin.Forms, привязки не выполняют любого неявного преобразования типов и если требуется отобразить в виде строки не строковый объект, необходимо предоставить преобразователь типов или использовать `StringFormat`. В фоне статический `String.Format` метод используется для реализации `StringFormat`. Потенциально это проблема, тем, что спецификации форматирования .NET фигурные скобки, которые также используются для разделения расширения разметки. Это создает риск путать синтаксического анализа XAML. Чтобы этого избежать, поместите всю строку форматирования в одинарные кавычки:

```csharp
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

Вот выполняемой программы.

[![](data-binding-basics-images/sliderbinding.png "Привязки представления")](data-binding-basics-images/sliderbinding-large.png#lightbox "привязки представления ")

## <a name="the-binding-mode"></a>Режим привязки

Единое представление может иметь привязки данных на нескольких из его свойств. Тем не менее, каждое представление может иметь только один `BindingContext`, поэтому необходимо несколько привязок данных в этом представлении все ссылки на свойства одного объекта.

Решение для этого и других проблем `Mode` свойства, которое устанавливается с членом `BindingMode` перечисления:

- `Default`
- `OneWay` — значения передаются из источника в целевой объект
- `OneWayToSource` — значения передаются от цели к источнику
- `TwoWay` — значения передаются между источником и целью обоими способами

В следующей программе показано, как правило `OneWayToSource` и `TwoWay` режимы привязки. Четыре `Slider` представления предназначены для управления `Scale`, `Rotate`, `RotateX`, и `RotateY` свойства `Label`. Сначала, похоже, как если бы эти четыре свойства `Label` должно быть цели привязки данных, поскольку каждый задано `Slider`. Тем не менее `BindingContext` из `Label` может быть только один объект, и существует четыре разных ползунка.

По этой причине все привязки установлены первый взгляд вперед способов: `BindingContext` каждого из четырех ползунки равно `Label`, и привязки установлены на `Value` свойства ползунки. С помощью `OneWayToSource` и `TwoWay` режимов, они `Value` свойства можно задать свойства источника, которые являются `Scale`, `Rotate`, `RotateX`, и `RotateY` свойства `Label`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SliderTransformsPage"
             Padding="5"
             Title="Slider Transforms Page">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="Auto" />
        </Grid.ColumnDefinitions>

        <!-- Scaled and rotated Label -->
        <Label x:Name="label"
               Text="TEXT"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <!-- Slider and identifying Label for Scale -->
        <Slider x:Name="scaleSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="1" Grid.Column="0"
                Maximum="10"
                Value="{Binding Scale, Mode=TwoWay}" />

        <Label BindingContext="{x:Reference scaleSlider}"
               Text="{Binding Value, StringFormat='Scale = {0:F1}'}"
               Grid.Row="1" Grid.Column="1"
               VerticalTextAlignment="Center" />

        <!-- Slider and identifying Label for Rotation -->
        <Slider x:Name="rotationSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="2" Grid.Column="0"
                Maximum="360"
                Value="{Binding Rotation, Mode=OneWayToSource}" />

        <Label BindingContext="{x:Reference rotationSlider}"
               Text="{Binding Value, StringFormat='Rotation = {0:F0}'}"
               Grid.Row="2" Grid.Column="1"
               VerticalTextAlignment="Center" />

        <!-- Slider and identifying Label for RotationX -->
        <Slider x:Name="rotationXSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="3" Grid.Column="0"
                Maximum="360"
                Value="{Binding RotationX, Mode=OneWayToSource}" />

        <Label BindingContext="{x:Reference rotationXSlider}"
               Text="{Binding Value, StringFormat='RotationX = {0:F0}'}"
               Grid.Row="3" Grid.Column="1"
               VerticalTextAlignment="Center" />

        <!-- Slider and identifying Label for RotationY -->
        <Slider x:Name="rotationYSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="4" Grid.Column="0"
                Maximum="360"
                Value="{Binding RotationY, Mode=OneWayToSource}" />

        <Label BindingContext="{x:Reference rotationYSlider}"
               Text="{Binding Value, StringFormat='RotationY = {0:F0}'}"
               Grid.Row="4" Grid.Column="1"
               VerticalTextAlignment="Center" />
    </Grid>
</ContentPage>
```

Привязки на трех `Slider` представления, `OneWayToSource`, то есть, `Slider` значение приводит к изменению свойства его `BindingContext`, являющийся `Label` с именем `label`. Эти три `Slider` представления вызвать изменения `Rotate`, `RotateX`, и `RotateY` свойства `Label`.

Однако привязки для `Scale` свойство `TwoWay`. Это вызвано `Scale` свойство имеет значение по умолчанию 1 и по использованию `TwoWay` привязки причины `Slider` исходное значение, задаваемое в 1 вместо 0. Если были, привязку `OneWayToSource`, `Scale` свойство будет изначально равен 0, возвращаемое функцией `Slider` значение по умолчанию. `Label` Не будет видима, а это может привести к путанице для пользователя.

 [![](data-binding-basics-images/slidertransforms.png "Обратной привязки")](data-binding-basics-images/slidertransforms-large.png#lightbox "обратной привязки")

## <a name="bindings-and-collections"></a>Привязки и коллекции

Ничего не демонстрирует преимущества использования XAML и привязки данных лучше, чем шаблон `ListView`.

`ListView` Определяет `ItemsSource` свойство типа `IEnumerable`, и она отображает элементы в этой коллекции. Эти элементы могут быть объекты любого типа. По умолчанию `ListView` использует `ToString` метод каждого элемента для отображения этого элемента. Иногда это именно то, чего вы, но во многих случаях `ToString` возвращает только класс полное имя объекта.

Тем не менее элементы в `ListView` коллекции может быть отображен любой способ использования *шаблона*, который включает класс, производный от `Cell`. Является клоном шаблона для каждого элемента в `ListView`, и привязки данных, которые были установлены на шаблоне передаются отдельных клонов.

Очень часто, может потребоваться создание настраиваемой ячейки для этих элементов с помощью `ViewCell` класса. Этот процесс несколько запутанным, в коде, но в XAML становится очевидным.

В XamlSamples проекта включено класс с именем `NamedColor`. Каждый `NamedColor` объект имеет `Name` и `FriendlyName` свойств типа `string`и `Color` свойство типа `Color`. Кроме того `NamedColor` имеет 141 статического поля только для чтения типа `Color` соответствующий цветов, определенных в Xamarin.Forms `Color` класса. Создает статический конструктор `IEnumerable<NamedColor>` коллекцию, содержащую `NamedColor` объекты, соответствующие эти статические поля и присваивает его открытые статические `All` свойство.

Задание статического `NamedColor.All` свойства `ItemsSource` из `ListView` просто с помощью `x:Static` расширения разметки:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.ListViewDemoPage"
             Title="ListView Demo Page">

    <ListView ItemsSource="{x:Static local:NamedColor.All}" />

</ContentPage>
```

Итоговый экран устанавливает элементы, действительно типа `XamlSamples.NamedColor`:

[![](data-binding-basics-images/listview1.png "Привязка к коллекции")](data-binding-basics-images/listview1-large.png#lightbox "привязка к коллекции")

Это не так много сведений, но `ListView` является прокручиваемым и доступным для выбора.

Определение шаблонов для элементов, имеет смысл разделить `ItemTemplate` свойство как элемент свойства и присвойте ему значение `DataTemplate`, который затем ссылается на `ViewCell`. Чтобы `View` свойство `ViewCell` можно задать макет в один или несколько представлений для отображения каждого элемента. Ниже приведен простой пример.

```xaml
<ListView ItemsSource="{x:Static local:NamedColor.All}">
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <ViewCell.View>
                    <Label Text="{Binding FriendlyName}" />
                </ViewCell.View>
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

`Label` Задан равным `View` свойство `ViewCell`. ( `ViewCell.View` Теги не требуется, поскольку `View` свойство является свойством содержимого `ViewCell`.) Эта разметка отображает `FriendlyName` каждого экземпляра `NamedColor` объекта:

[![](data-binding-basics-images/listview2.png "Привязка к коллекции с DataTemplate")](data-binding-basics-images/listview2-large.png#lightbox "привязку в коллекцию с DataTemplate")

Гораздо лучше. Теперь все, что нужно — Настройка шаблона элемента с Дополнительные сведения и фактический цвет. Для поддержки этого шаблона, некоторые значения и объекты были определены в словаре ресурсов страницы:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.ListViewDemoPage"
             Title="ListView Demo Page">

    <ContentPage.Resources>
        <ResourceDictionary>
            <OnPlatform x:Key="boxSize"
                        x:TypeArguments="x:Double">
                <On Platform="iOS, Android, UWP" Value="50" />
            </OnPlatform>

            <OnPlatform x:Key="rowHeight"
                        x:TypeArguments="x:Int32">
                <On Platform="iOS, Android, UWP" Value="60" />
            </OnPlatform>

            <local:DoubleToIntConverter x:Key="intConverter" />

        </ResourceDictionary>
    </ContentPage.Resources>

    <ListView ItemsSource="{x:Static local:NamedColor.All}"
              RowHeight="{StaticResource rowHeight}">
        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <StackLayout Padding="5, 5, 0, 5"
                                 Orientation="Horizontal"
                                 Spacing="15">

                        <BoxView WidthRequest="{StaticResource boxSize}"
                                 HeightRequest="{StaticResource boxSize}"
                                 Color="{Binding Color}" />

                        <StackLayout Padding="5, 0, 0, 0"
                                     VerticalOptions="Center">

                            <Label Text="{Binding FriendlyName}"
                                   FontAttributes="Bold"
                                   FontSize="Medium" />

                            <StackLayout Orientation="Horizontal"
                                         Spacing="0">
                                <Label Text="{Binding Color.R,
                                       Converter={StaticResource intConverter},
                                       ConverterParameter=255,
                                       StringFormat='R={0:X2}'}" />

                                <Label Text="{Binding Color.G,
                                       Converter={StaticResource intConverter},
                                       ConverterParameter=255,
                                       StringFormat=', G={0:X2}'}" />

                                <Label Text="{Binding Color.B,
                                       Converter={StaticResource intConverter},
                                       ConverterParameter=255,
                                       StringFormat=', B={0:X2}'}" />
                            </StackLayout>
                        </StackLayout>
                    </StackLayout>
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>
```

Обратите внимание на использование `OnPlatform` для определения размера `BoxView` и высоту `ListView` строк. Несмотря на то, что значения для всех трех платформ совпадают, разметку легко может быть адаптирован и для других значений для точной настройки отображения.

## <a name="binding-value-converters"></a>Преобразователи значений привязки

Предыдущий **Демонстрация ListView** XAML-файл отображает отдельные `R`, `G`, и `B` свойства Xamarin.Forms `Color` структуры. Эти свойства имеют тип `double` и диапазон от 0 до 1. Если вы хотите отображать шестнадцатеричные значения, нельзя использовать просто `StringFormat` с «X2» спецификации форматирования. Это работает только для целых чисел и помимо, `double` значения должны быть умножено на 255.

Небольшая проблема была решена с *преобразователь значений*, который также называется *преобразователь привязки*. Это класс, реализующий `IValueConverter` интерфейса, поэтому он имеет два метода с именем `Convert` и `ConvertBack`. `Convert` Метод вызывается, когда значение передается от источника к цели; `ConvertBack` метод вызывается для передачи данных из целевого источника в `OneWayToSource` или `TwoWay` привязки:

```csharp
using System;
using System.Globalization;
using Xamarin.Forms;

namespace XamlSamples
{
    class DoubleToIntConverter : IValueConverter
    {
        public object Convert(object value, Type targetType,
                              object parameter, CultureInfo culture)
        {
            double multiplier;

            if (!Double.TryParse(parameter as string, out multiplier))
                multiplier = 1;

            return (int)Math.Round(multiplier * (double)value);
        }

        public object ConvertBack(object value, Type targetType,
                                  object parameter, CultureInfo culture)
        {
            double divider;

            if (!Double.TryParse(parameter as string, out divider))
                divider = 1;

            return ((double)(int)value) / divider;
        }
    }
}
```

`ConvertBack` Метод не играет роли в этой программе из-за привязки только один из способов из источника в целевую.

Привязка ссылается конвертер привязки с `Converter` свойство. Преобразователь привязки также может принимать параметр указан с `ConverterParameter` свойство. Для некоторых универсальность это как указывается множитель. Преобразователь привязки проверяет используемый параметр преобразователя является допустимым для `double` значение.

Преобразователь создается в словаре ресурсов, поэтому могут использоваться совместно несколькими привязками:

```xaml
<local:DoubleToIntConverter x:Key="intConverter" />
```

Привязки данных три ссылки на это. Обратите внимание, что `Binding` расширение разметки содержит встроенный `StaticResource` расширения разметки:

```xaml
<Label Text="{Binding Color.R,
                      Converter={StaticResource intConverter},
                      ConverterParameter=255,
                      StringFormat='R={0:X2}'}" />
```

Ниже приведен результат:

[![](data-binding-basics-images/listview3.png "Привязка к коллекции с DataTemplate и преобразователей")](data-binding-basics-images/listview3-large.png#lightbox "привязку в коллекцию с DataTemplate и преобразователи типов")

`ListView` Достаточно сложная при обработке изменений может динамически в базовых данных, но только если выполнить определенные действия. При присвоении коллекции элементов `ItemsSource` свойство `ListView` изменений во время выполнения —, если элементы могут быть добавлены или удалены из коллекции, используйте `ObservableCollection` класса для этих элементов. `ObservableCollection` реализует `INotifyCollectionChanged` интерфейс, и `ListView` установит обработчик `CollectionChanged` событий.

Если изменить свойства сами элементы во время выполнения, то элементы в коллекции должны реализовывать `INotifyPropertyChanged` изменения значений свойств с помощью интерфейса и сигнала `PropertyChanged` событий. Это показано в следующей части этой серии [. часть 5. Из привязка данных к MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

## <a name="summary"></a>Сводка

Привязки данных предоставляют мощный механизм для связывания свойства между двумя объектами внутри страницы или между визуальными объектами и базовые данные. Но когда приложение готово к работе с источниками данных, шаблон архитектуры популярные приложения начинается вырисовываться как полезные парадигма. Это рассматривается в [. часть 5. От привязок данных для MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).



## <a name="related-links"></a>Связанные ссылки

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [Часть 1. Начало работы с XAML (пример)](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Часть 2. Основные синтаксиса XAML (пример)](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Часть 3. Расширения разметки XAML (пример)](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Часть 5. Из привязки данных MVVM (пример)](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
