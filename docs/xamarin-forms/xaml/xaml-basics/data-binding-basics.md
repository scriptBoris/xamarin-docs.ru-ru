---
title: Часть 4. Основы привязки данных
description: Привязки данных позволяют свойства двух объектов для связывания таким образом, чтобы изменение одного приводит к изменению в другой.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 342288C3-BB4C-4924-B178-72E112D777BA
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: aa576e4af5d804eb5b8d78643ffa762b8a15c6dc
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171772"
---
# <a name="part-4-data-binding-basics"></a>Часть 4. Основы привязки данных

_Привязки данных позволяют свойства двух объектов для связывания таким образом, чтобы изменение одного приводит к изменению в другой. Это очень ценным средством, и во время привязки данных можно определить полностью в коде, XAML предоставляет сочетания клавиш и удобство работы. Следовательно одним из наиболее важных расширений разметки в Xamarin.Forms привязан._

## <a name="data-bindings"></a>Привязки данных

Свойства двух объектов, который называется connect привязки данных *источника* и *целевой*. В коде, требуются два этапа: `BindingContext` свойство целевого объекта должно быть присвоено исходного объекта и `SetBinding` метод (часто используется в сочетании с `Binding` класс) должен быть вызван для целевого объекта для привязки свойства этого объекта Объект, свойства исходного объекта.

Целевое свойство должно быть свойством привязки, это означает, что целевой объект должен быть производным от `BindableObject`. В электронной документации Xamarin.Forms указывает, какие свойства связываемые свойства. Свойство `Label` например `Text` связан с свойство, используемое `TextProperty`.

В разметке, необходимо также выполнить те же два действия, которые необходимы в коде, за исключением того, что `Binding` расширение разметки занимает место `SetBinding` вызова и `Binding` класса.

Тем не менее, при определении привязки данных в XAML, существует несколько способов установки `BindingContext` целевого объекта. Иногда она задается в файле кода, иногда с помощью `StaticResource` или `x:Static` расширения разметки, а иногда, как содержимое `BindingContext` теги элемента свойства.

Привязки используются чаще всего для подключения визуальные элементы программы с базовой модели данных, обычно в реализации архитектуры MVVM (Model-View-ViewModel) приложения, как описано в [часть 5. От привязки данных до MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md), но возможны и другие сценарии.

## <a name="view-to-view-bindings"></a>Привязки представления

Вы можете определить привязки данных для связывания свойства из двух представлений на одной странице. В этом случае можно задать `BindingContext` целевого объекта с использованием `x:Reference` расширение разметки.

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

`Slider` Содержит `x:Name` атрибут, на который ссылается двух `Label` представлениями с использованием `x:Reference` расширение разметки.

`x:Reference` Расширения привязки определяет свойство, именуемое `Name` присвоено имя элемента, на который указывает ссылка, в данном случае `slider`. Тем не менее `ReferenceExtension` класс, определяющий `x:Reference` расширения разметки также определяет `ContentProperty` для атрибута `Name`, что означает, что это не требуется явным образом. Просто для разнообразия первый `x:Reference` включает «имя =», но второй — нет:

```csharp
BindingContext="{x:Reference Name=slider}"
…
BindingContext="{x:Reference slider}"
```

`Binding` Расширение разметки, сам может иметь несколько свойств, так же, как `BindingBase` и `Binding` класса. `ContentProperty` Для `Binding` — `Path`, но «путь =» расширение разметки можно опустить, если путь является первым элементом в `Binding` расширение разметки. В первом примере имеется «Path =», но во втором примере пропускает его:

```csharp
Rotation="{Binding Path=Value}"
…
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

Свойства могут размещаться на одной строке или разделены на несколько строк:

```csharp
Text="{Binding Value,
               StringFormat='The angle is {0:F0} degrees'}"
```

Сделайте все, что удобно.

Обратите внимание, что `StringFormat` свойство во втором `Binding` расширение разметки. В Xamarin.Forms, привязки не выполняют любые неявные преобразования типов, и если вам нужно отобразить не строковый объект в виде строки необходимо предоставить преобразователь типов или использовать `StringFormat`. На самом деле статический `String.Format` метод используется для реализации `StringFormat`. Потенциально это проблема, так как спецификации форматирования .NET включают фигурные скобки, которые также используются для разделения расширения разметки. Это создает угрозу сбивающих с толку средство синтаксического анализа XAML. Чтобы избежать этого, поместите всю строку форматирования в одиночные кавычки:

```csharp
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

Ниже приведен выполняющейся программе.

[![](data-binding-basics-images/sliderbinding.png "Представление позволяет просматривать привязки")](data-binding-basics-images/sliderbinding-large.png#lightbox "привязок представления на представление ")

## <a name="the-binding-mode"></a>Режим привязки

Единое представление может иметь привязки данных на нескольких из его свойств. Тем не менее, каждое представление может иметь только один `BindingContext`, поэтому необходимо несколько привязок данных в этом представлении все ссылки на свойства одного объекта.

Решение для этого и других проблем включает в себя `Mode` свойство, которому присвоено значение является членом `BindingMode` перечисления:

- `Default`
- `OneWay` — значения передаются из источника в целевой объект
- `OneWayToSource` — значения передаются от целевого объекта к источнику
- `TwoWay` — значения передаются обе стороны между источником и целью

В следующей программе показано, как правило `OneWayToSource` и `TwoWay` режимы привязки. Четыре `Slider` представления предназначены для управления `Scale`, `Rotate`, `RotateX`, и `RotateY` свойства `Label`. Поначалу кажется, как если эти четыре свойства `Label` должно быть цели привязки данных, так как каждый задано `Slider`. Тем не менее `BindingContext` из `Label` может быть только один объект, и существует четыре разных ползунка.

По этой причине все привязки установлены первый взгляд назад способов: `BindingContext` каждого из четырех ползунки присваивается `Label`, и привязки установлены на `Value` свойства ползунки. С помощью `OneWayToSource` и `TwoWay` режимы, они `Value` свойства можно задать свойства источника, которые являются `Scale`, `Rotate`, `RotateX`, и `RotateY` свойства `Label`:

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

Привязки на три `Slider` представления — это `OneWayToSource`, то есть, `Slider` значение приводит к изменению в свойстве его `BindingContext`, который является `Label` с именем `label`. Эти три `Slider` представления вызвать изменения `Rotate`, `RotateX`, и `RotateY` свойства `Label`.

Однако привязки для `Scale` свойство `TwoWay`. Это обусловлено `Scale` свойство имеет значение по умолчанию 1 и по использованию `TwoWay` привязки причины `Slider` начальное значение, устанавливаемое на 1 вместо 0. Если были, привязку к `OneWayToSource`, `Scale` свойство изначально будет иметь значение 0 из `Slider` значение по умолчанию. `Label` Не будет видима, и это может привести к путанице для пользователя.

 [![](data-binding-basics-images/slidertransforms.png "Обратной привязки")](data-binding-basics-images/slidertransforms-large.png#lightbox "обратной привязки")

 > [!NOTE]
 > [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) Класс также имеет [ `ScaleX` ](xref:Xamarin.Forms.VisualElement.ScaleX) и [ `ScaleY` ](xref:Xamarin.Forms.VisualElement.ScaleY) свойства, которые масштабируют `VisualElement` на оси x и оси y соответственно.

## <a name="bindings-and-collections"></a>Привязки и коллекции

Ничто не иллюстрирует мощь XAML и привязки данных лучше, чем шаблонного `ListView`.

`ListView` Определяет `ItemsSource` свойство типа `IEnumerable`, и она отображает элементы в этой коллекции. Эти элементы могут быть объекты любого типа. По умолчанию `ListView` использует `ToString` метод каждого элемента для отображения этого элемента. Иногда это, что вам требуется, но во многих случаях `ToString` возвращает только имя полного класса объекта.

Тем не менее элементы в `ListView` коллекции может отображаться любым способом, который вы хотите воспользоваться *шаблона*, который включает в себя класс, производный от `Cell`. Шаблон клонируется для каждого элемента в `ListView`, и привязок данных, которые были заданы в шаблоне передаются отдельных клонов.

Очень часто, необходимо создать пользовательские ячейку для этих элементов, с помощью `ViewCell` класса. Этот процесс немного запутанным, в коде, но в XAML становится очень просто.

В XamlSamples проект входит класс с именем `NamedColor`. Каждый `NamedColor` объект имеет `Name` и `FriendlyName` свойства типа `string`и `Color` свойство типа `Color`. Кроме того `NamedColor` имеет 141 статические поля только для чтения типа `Color` соответствующие цвета, определенные в Xamarin.Forms `Color` класса. Создает статический конструктор `IEnumerable<NamedColor>` коллекцию, содержащую `NamedColor` объектов, соответствующий эти статические поля и присваивает его открытые статические `All` свойство.

Задайте для статического `NamedColor.All` свойства `ItemsSource` из `ListView` легко с помощью `x:Static` расширение разметки:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.ListViewDemoPage"
             Title="ListView Demo Page">

    <ListView ItemsSource="{x:Static local:NamedColor.All}" />

</ContentPage>
```

Итоговый отображение устанавливает, что элементы по-настоящему имеют тип `XamlSamples.NamedColor`:

[![](data-binding-basics-images/listview1.png "Привязка к коллекции")](data-binding-basics-images/listview1-large.png#lightbox "привязка к коллекции")

Это не много информации, но `ListView` является прокручиваемым и доступный для выбора.

Определение шаблона для элементов, вы захотите разделить `ItemTemplate` свойства в виде элемента свойства и присвойте ему значение `DataTemplate`, которая затем ссылается на `ViewCell`. Чтобы `View` свойство `ViewCell` можно определить макет одно или несколько представлений для отображения каждого элемента. Ниже приведен простой пример:

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

`Label` Элементу присваивается `View` свойство `ViewCell`. ( `ViewCell.View` Теги не требуются, так как `View` свойство является свойством содержимого из `ViewCell`.) Эта разметка отображает `FriendlyName` свойства каждого `NamedColor` объекта:

[![](data-binding-basics-images/listview2.png "Привязка к коллекции с помощью DataTemplate")](data-binding-basics-images/listview2-large.png#lightbox "привязка к коллекции с помощью DataTemplate")

Гораздо лучше. Теперь все, что требуется — настройка шаблона элемента с Дополнительные сведения и фактический цвет. Для поддержки этого шаблона, некоторые значения и объекты были определены в словаре ресурсов страницы:

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

Обратите внимание на использование `OnPlatform` для определения размера `BoxView` и высоту `ListView` строк. Несмотря на то, что значения для всех платформ совпадают, разметка может легко адаптировать для других значений, скорректировав способ представления.

## <a name="binding-value-converters"></a>Привязка преобразователей значений

Предыдущий **Демонстрация ListView** файл XAML отображает отдельные `R`, `G`, и `B` свойства Xamarin.Forms `Color` структуры. Эти свойства имеют тип `double` и диапазон от 0 до 1. Если вы хотите отображать шестнадцатеричные значения, нельзя использовать просто `StringFormat` с «X2» форматирование спецификации. Это работает только для целых чисел, а кроме того, `double` значения должны быть умножено на 255.

Это маленькая проблема была решена с *преобразователь значений*, называемой *преобразователь привязки*. Это класс, реализующий `IValueConverter` интерфейс, который означает, что он имеет два метода с именем `Convert` и `ConvertBack`. `Convert` Метод вызывается при передаче значения от источника к целевому объекту; `ConvertBack` метод вызывается для передачи от целевого объекта к источнику в `OneWayToSource` или `TwoWay` привязки:

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

`ConvertBack` Метод не играют роль в этой программе из-за привязки только один из способов из источника к целевому объекту.

Привязка ссылается на преобразователь привязки с `Converter` свойство. Преобразователь привязки также может принимать параметр указан с `ConverterParameter` свойство. Для некоторых универсальности это как указывается множитель. Преобразователь привязки проверяет параметр преобразователя для является допустимым `double` значение.

Преобразователь создан в словаре ресурсов, поэтому он может совместно использоваться несколькими привязками:

```xaml
<local:DoubleToIntConverter x:Key="intConverter" />
```

Справочник по привязкам три данных, этот экземпляр. Обратите внимание, что `Binding` расширение разметки содержит встроенный `StaticResource` расширение разметки:

```xaml
<Label Text="{Binding Color.R,
                      Converter={StaticResource intConverter},
                      ConverterParameter=255,
                      StringFormat='R={0:X2}'}" />
```

Ниже приведен результат.

[![](data-binding-basics-images/listview3.png "Привязка к коллекции с DataTemplate и преобразователями")](data-binding-basics-images/listview3-large.png#lightbox "привязка к коллекции с DataTemplate и преобразователи типов")

`ListView` Достаточно сложная в обработка изменений, которые динамически могут возникнуть в базовых данных, но только если сначала выполнить ряд действий. Если назначен коллекции элементов `ItemsSource` свойство `ListView` изменения во время выполнения — это, если элементы могут добавляться к или удален из коллекции — использовать `ObservableCollection` класс для этих элементов. `ObservableCollection` реализует `INotifyCollectionChanged` интерфейс, и `ListView` установит обработчик `CollectionChanged` событий.

Если изменить свойства сами элементы во время выполнения, то элементы в коллекции должны реализовывать `INotifyPropertyChanged` интерфейс и сигнала изменения значений свойств с помощью `PropertyChanged` событий. Это показано в следующей части этой серии [часть 5. От привязки данных к MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

## <a name="summary"></a>Сводка

Привязки данных предоставляют мощный механизм для связывания свойства между двумя объектами внутри страницы или между визуальными объектами и базовых данных. Но когда приложение готово к работе с источниками данных, шаблон архитектуры популярное приложение начинает разработок полезную парадигму программирования. Это рассматривается в [часть 5. От привязки данных до MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).



## <a name="related-links"></a>Связанные ссылки

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [Часть 1. Начало работы с XAML (пример)](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Часть 2. Основному синтаксису XAML (пример)](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Часть 3. Расширения разметки XAML (пример)](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Часть 5. Из привязки данных до MVVM (пример)](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
