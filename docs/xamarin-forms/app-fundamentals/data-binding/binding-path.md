---
title: Путь привязки Xamarin.Forms
description: В этой статье объясняется, как использовать привязки данных в Xamarin.Forms для доступа к подчиненные свойства и элементы коллекции со свойством путь класса привязки.
ms.prod: xamarin
ms.assetid: 3CF721A5-E157-468B-AD3A-DA0A45E58E8D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 887a20f1791a190c182e6d179cfabb46c6e0eb48
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998951"
---
# <a name="xamarinforms-binding-path"></a>Путь привязки Xamarin.Forms

Во всех предыдущих примерах привязки данных [ `Path` ](xref:Xamarin.Forms.Binding.Path) свойство `Binding` класс (или [ `Path` ](xref:Xamarin.Forms.Xaml.BindingExtension.Path) свойство `Binding` расширение разметки) установлен к одному свойству. Фактически можно задать `Path` для *подсвойство* (свойство свойства), или с элементом коллекции.

Например, предположим, что страница содержит `TimePicker`:

```xaml
<TimePicker x:Name="timePicker">
```

`Time` Свойство `TimePicker` имеет тип `TimeSpan`, но возможно, вы хотите создать привязку данных, который ссылается на `TotalSeconds` , свойство `TimeSpan` значение. Ниже приведен привязки данных.

```xaml
{Binding Source={x:Reference timePicker},
         Path=Time.TotalSeconds}
```

`Time` Свойство имеет тип `TimeSpan`, который имеет `TotalSeconds` свойства. `Time` И `TotalSeconds` свойства являются просто подключается с периодом. Элементы в `Path` строки всегда относятся к свойствам, а не типы этих свойств.

Пример и несколько других отображаются в **вариации путь** страницы:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:globe="clr-namespace:System.Globalization;assembly=mscorlib"
             x:Class="DataBindingDemos.PathVariationsPage"
             Title="Path Variations"
             x:Name="page">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="FontSize" Value="Large" />
                <Setter Property="HorizontalTextAlignment" Value="Center" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10, 0">
        <TimePicker x:Name="timePicker" />

        <Label Text="{Binding Source={x:Reference timePicker},
                              Path=Time.TotalSeconds,
                              StringFormat='{0} total seconds'}" />

        <Label Text="{Binding Source={x:Reference page},
                              Path=Content.Children.Count,
                              StringFormat='There are {0} children in this StackLayout'}" />

        <Label Text="{Binding Source={x:Static globe:CultureInfo.CurrentCulture},
                              Path=DateTimeFormat.DayNames[3],
                              StringFormat='The middle day of the week is {0}'}" />

        <Label>
            <Label.Text>
                <Binding Path="DateTimeFormat.DayNames[3]"
                         StringFormat="The middle day of the week in France is {0}">
                    <Binding.Source>
                        <globe:CultureInfo>
                            <x:Arguments>
                                <x:String>fr-FR</x:String>
                            </x:Arguments>
                        </globe:CultureInfo>
                    </Binding.Source>
                </Binding>
            </Label.Text>
        </Label>

        <Label Text="{Binding Source={x:Reference page},
                              Path=Content.Children[1].Text.Length,
                              StringFormat='The first Label has {0} characters'}" />
    </StackLayout>
</ContentPage>
```

Во втором `Label`, источника привязки является самой страницы. `Content` Свойство имеет тип `StackLayout`, который имеет `Children` свойство типа `IList<View>`, который имеет `Count` свойство, указывающее количество дочерних элементов.

## <a name="paths-with-indexers"></a>Пути с помощью индексаторов

Привязка в третьем `Label` в **вариации путь** страниц ссылки [ `CultureInfo` ](xref:System.Globalization.CultureInfo) в класс `System.Globalization` пространства имен:

```xaml
<Label Text="{Binding Source={x:Static globe:CultureInfo.CurrentCulture},
                      Path=DateTimeFormat.DayNames[3],
                      StringFormat='The middle day of the week is {0}'}" />
```

Настройка источника статического `CultureInfo.CurrentCulture` свойство, которое является объектом типа `CultureInfo`. Что класс определяет свойство с именем `DateTimeFormat` типа [ `DateTimeFormatInfo` ](xref:System.Globalization.DateTimeFormatInfo) , содержащий `DayNames` коллекции. Индекс выбирает четвертый элемент.

Четвертый `Label` делает нечто подобное, но для региональных параметров, связанных с Франция. `Source` Привязки задано значение `CultureInfo` объекта конструктор без параметров:

```xaml
<Label>
    <Label.Text>
        <Binding Path="DateTimeFormat.DayNames[3]"
                 StringFormat="The middle day of the week in France is {0}">
            <Binding.Source>
                <globe:CultureInfo>
                    <x:Arguments>
                        <x:String>fr-FR</x:String>
                    </x:Arguments>
                </globe:CultureInfo>
            </Binding.Source>
        </Binding>
    </Label.Text>
</Label>
```

См. в разделе [передав аргументы конструктора](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments) Дополнительные сведения об указании аргументы конструктора в XAML.

Наконец, последний пример аналогичен второй, за исключением того, что он ссылается на один из дочерних элементов `StackLayout`:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content.Children[1].Text.Length,
                      StringFormat='The first Label has {0} characters'}" />
```

Этот дочерний элемент является `Label`, который имеет `Text` свойство типа `String`, который имеет `Length` свойства. Первый `Label` отчеты `TimeSpan` в `TimePicker`, поэтому при изменении текста, последний `Label` также изменения.

Вот ее запуск на всех трех платформах:

[![Варианты путь](binding-path-images/pathvariations-small.png "вариации путь")](binding-path-images/pathvariations-large.png#lightbox "вариации путь")

## <a name="debugging-complex-paths"></a>Отладка сложных контуров

Определения сложных путь может быть сложно создать: вам нужно знать тип каждого вложенного свойства или тип элементов в коллекции, чтобы правильно добавить Далее подчиненных свойств, но сами типы не отображаются в пути. Хорошим способом — инкрементное построение пути и посмотреть на промежуточных результатов. Для последнего примера, можно начать с нет `Path` определения всех:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      StringFormat='{0}'}" />
```

Отображающий тип источника привязки, или `DataBindingDemos.PathVariationsPage`. Вы знаете `PathVariationsPage` является производным от `ContentPage`, поэтому он `Content` свойство:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content,
                      StringFormat='{0}'}" />
```

Тип `Content` свойство теперь получит доступ к быть `Xamarin.Forms.StackLayout`. Добавить `Children` свойства `Path` и тип `Xamarin.Forms.ElementCollection'1[Xamarin.Forms.View]`, — это класс, внутренними для Xamarin.Forms, но очевидно, что тип коллекции. Добавьте индекс, а тип — `Xamarin.Forms.Label`. По-прежнему таким образом.

По мере Xamarin.Forms обработки путь привязки, он устанавливает `PropertyChanged` обработчик для любого объекта в пути, который реализует `INotifyPropertyChanged` интерфейс. Например, последний привязки реагирует на изменения в первом `Label` поскольку `Text` изменения свойств.

Если свойство в пути привязки не реализует `INotifyPropertyChanged`, любые изменения этого свойства будет игнорироваться. Некоторые изменения полностью может сделать недействительным путь привязки, поэтому этот метод следует использовать только в том случае, если строка свойства и подсвойства никогда не становятся недействительными.



## <a name="related-links"></a>Связанные ссылки

- [Демонстрации, привязка данных (пример)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Данные привязки Глава из книги по Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
