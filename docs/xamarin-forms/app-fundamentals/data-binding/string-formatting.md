---
title: Форматирование строки Xamarin.Forms
description: В этой статье объясняется, как использовать привязки данных в Xamarin.FOrms для форматирования и отображения объектов в виде строк. Это достигается путем установки StringFormat привязки в стандартную строку форматирования .NET с заполнителем.
ms.prod: xamarin
ms.assetid: 978C85B7-CB58-4483-A131-21B381A865E0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: a876e81c67b6ec61a2cb29143cb001a7d6160032
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998151"
---
# <a name="xamarinforms-string-formatting"></a>Форматирование строки Xamarin.Forms

Иногда удобно использовать для отображения строкового представления объекта или значение привязки данных. Например, может потребоваться использовать `Label` для отображения текущего значения `Slider`. В этой привязке данных `Slider` является источником и целевым объектом является `Text` свойство `Label`.

При отображении строки в коде, самое мощное средство является статическим [ `String.Format` ](xref:System.String.Format(System.String,System.Object)) метод. Строка форматирования содержит коды для различных типов объектов форматирования, и может содержать другой текст и форматировании значения. См. в разделе [типы форматирования в .NET](/dotnet/standard/base-types/formatting-types/) содержатся дополнительные сведения о форматировании строки.

## <a name="the-stringformat-property"></a>Свойство StringFormat

Эта функция позволяет переносится в привязки данных: выбрано [ `StringFormat` ](xref:Xamarin.Forms.BindingBase.StringFormat) свойство `Binding` (или [ `StringFormat` ](xref:Xamarin.Forms.Xaml.BindingExtension.StringFormat) свойство `Binding` расширение разметки) для стандартный .NET форматирования строки с один заполнитель:

```xaml
<Slider x:Name="slider" />
<Label Text="{Binding Source={x:Reference slider},
                      Path=Value,
                      StringFormat='The slider value is {0:F2}'}" />
```

Обратите внимание на то, что строка форматирования разделяются символов одинарные кавычки (апостроф), чтобы помочь избежать обработке фигурные скобки в качестве другого расширения разметки XAML средство синтаксического анализа XAML. В противном случае эта строка без символ одинарной кавычки та же строка используется для отображения значения с плавающей запятой в вызове `String.Format`. Спецификацию форматирования `F2` приводит значения для отображения с двумя десятичными разрядами.

`StringFormat` Свойство имеет смысл только если целевое свойство имеет тип `string`, и режим привязки является `OneWay` или `TwoWay`. Для двусторонней привязки `StringFormat` применим только для значений, передачи из источника в целевой объект.

Как можно будет увидеть в следующей статье на [путь привязки](binding-path.md), привязки данных может стать довольно сложным и сложные. При отладке эти привязки данных, можно добавить `Label` в файл XAML с `StringFormat` для отображения некоторые промежуточные результаты. Даже если он используется только для отображения типа объекта, который может пригодиться.

**Форматирования строки** страницы показано несколько способов применения `StringFormat` свойство:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=mscorlib"
             x:Class="DataBindingDemos.StringFormattingPage"
             Title="String Formatting">

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>

            <Style TargetType="BoxView">
                <Setter Property="Color" Value="Blue" />
                <Setter Property="HeightRequest" Value="2" />
                <Setter Property="Margin" Value="0, 5" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10">
        <Slider x:Name="slider" />
        <Label Text="{Binding Source={x:Reference slider},
                              Path=Value,
                              StringFormat='The slider value is {0:F2}'}" />

        <BoxView />

        <TimePicker x:Name="timePicker" />
        <Label Text="{Binding Source={x:Reference timePicker},
                              Path=Time,
                              StringFormat='The TimeSpan is {0:c}'}" />

        <BoxView />

        <Entry x:Name="entry" />
        <Label Text="{Binding Source={x:Reference entry},
                              Path=Text,
                              StringFormat='The Entry text is &quot;{0}&quot;'}" />

        <BoxView />

        <StackLayout BindingContext="{x:Static sys:DateTime.Now}">
            <Label Text="{Binding}" />
            <Label Text="{Binding Path=Ticks,
                                  StringFormat='{0:N0} ticks since 1/1/1'}" />
            <Label Text="{Binding StringFormat='The {{0:MMMM}} specifier produces {0:MMMM}'}" />
            <Label Text="{Binding StringFormat='The long date is {0:D}'}" />
        </StackLayout>

        <BoxView />

        <StackLayout BindingContext="{x:Static sys:Math.PI}">
            <Label Text="{Binding}" />
            <Label Text="{Binding StringFormat='PI to 4 decimal points = {0:F4}'}" />
            <Label Text="{Binding StringFormat='PI in scientific notation = {0:E7}'}" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

Привязки на `Slider` и `TimePicker` демонстрируют использование спецификаций формата, определенного для `double` и `TimeSpan` типов данных. `StringFormat` , Отображающий текст из `Entry` представление показано, как указать двойные кавычки в строке форматирования, с использованием `&quot;` сущности HTML.

Следующий раздел в файле XAML — `StackLayout` с `BindingContext` присвоено `x:Static` расширение разметки, который ссылается на статический `DateTime.Now` свойство. Первой привязки не имеет свойств:

```xaml
<Label Text="{Binding}" />
```

Это просто отображает `DateTime` значение `BindingContext` с форматированием по умолчанию. Вторая привязка отображает `Ticks` свойство `DateTime`, тогда как другие две привязки отображения `DateTime` сам с определенным форматированием. Обратите внимание, что это `StringFormat`:

```xaml
<Label Text="{Binding StringFormat='The {{0:MMMM}} specifier produces {0:MMMM}'}" />
```

Если вам нужно отобразить левая или Правая фигурная фигурные скобки в строке форматирования, просто используйте пару из них.

Последние наборы разделе `BindingContext` значению `Math.PI` и отображает его с форматированием по умолчанию и два разных типа форматирование чисел.

Вот ее запуск на всех трех платформах:

[![Строка форматирования](string-formatting-images/stringformatting-small.png "форматирования строк")](string-formatting-images/stringformatting-large.png#lightbox "форматирования строк")

## <a name="viewmodels-and-string-formatting"></a>ViewModels и форматирования строк

При использовании `Label` и `StringFormat` для отображения значения представления, также является целевым объектом модели представления, вы можете определить привязки в представлении для `Label` или из ViewModel `Label`. В общем случае второй подход лучше, так как он проверяет, что работаете привязки между View и ViewModel.

Этот подход показан в **лучше выбора цвета** пример, который использует же ViewModel как **простой селектор цвета** программы, представленной на [ **режим привязки** ](binding-mode.md) статьи:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.BetterColorSelectorPage"
             Title="Better Color Selector">

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Slider">
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <StackLayout.BindingContext>
            <local:HslColorViewModel Color="Sienna" />
        </StackLayout.BindingContext>

        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <StackLayout Margin="10, 0">
            <Label Text="{Binding Name}" />

            <Slider Value="{Binding Hue}" />
            <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />

            <Slider Value="{Binding Saturation}" />
            <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />

            <Slider Value="{Binding Luminosity}" />
            <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
        </StackLayout>
    </StackLayout>
</ContentPage>    
```

Теперь есть три пары `Slider` и `Label` свойство в источника элементов, которые привязаны к тому же `HslColorViewModel` объекта. Единственное отличие `Label` имеет `StringFormat` свойство для отображения каждого `Slider` значение.

[![Лучше цвета выделения](string-formatting-images/bettercolorselector-small.png "лучше цвета выделения")](string-formatting-images/bettercolorselector-large.png#lightbox "лучше цвета выделения")

Вы можете спросить, как можно отобразить значения RGB (красный, зеленый, синий) в традиционных из двух цифр в шестнадцатеричном формате. Эти целочисленные значения недоступны непосредственно из `Color` структуры. Одним из решений будет вычислить целочисленные значения компонентов цветов в ViewModel и представлять их как свойства. Затем можно форматировать их с помощью `X2` форматирование спецификации.

Другой подход является более общим: можно написать *преобразователь значений привязки* как описано в последующих статьях [ **привязка преобразователей значений**](converters.md).

Тем не менее, в следующей статье исследует [ **путь привязки** ](binding-path.md) в более подробно описаны и показать, как ее можно использовать для ссылки на вложенные свойства и элементы в коллекции.


## <a name="related-links"></a>Связанные ссылки

- [Демонстрации, привязка данных (пример)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Данные привязки Глава из книги по Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
