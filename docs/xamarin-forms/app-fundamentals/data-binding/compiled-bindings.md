---
title: Xamarin.Forms компиляции привязки
description: В этой статье объясняется, как использовать скомпилированные привязки для повышения производительности привязки данных в приложениях Xamarin.Forms.
ms.prod: xamarin
ms.assetid: ABE6B7F7-875E-4402-A1D2-845CE374402B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2018
ms.openlocfilehash: 0b350082c834076a1d69427644259087d64bf26a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111605"
---
# <a name="xamarinforms-compiled-bindings"></a>Xamarin.Forms компиляции привязки

_Скомпилированный привязки разрешаются быстрее, чем классический привязок, таким образом улучшая производительность привязки данных в приложениях Xamarin.Forms._

Привязки данных имеют две основные проблемы:

1. Нет никаких проверок во время компиляции выражения привязки. Вместо этого привязки разрешаются во время выполнения. Таким образом любые недопустимые привязки нередко обнаруживаются только во время выполнения, когда приложение не работает должным образом или отображаются сообщения об ошибках.
1. Они не являются экономически эффективным. Привязки разрешаются во время выполнения с помощью проверки общего назначения объекта (отражение), а затраты на это зависит от платформы к платформе.

Скомпилированный привязки повышения производительности привязки данных в приложениях Xamarin.Forms, разрешая выражения привязки во время компиляции, а не среды выполнения. Кроме того эта проверка во время компиляции выражений привязки разработчики лучше устранением неполадок, так как недопустимые привязки выводятся как ошибки построения.

Процесс использования скомпилированных привязки является:

1. Включение компиляции XAML. Дополнительные сведения о компиляции XAML, см. в разделе [компиляции XAML](~/xamarin-forms/xaml/xamlc.md).
1. Задайте `x:DataType` атрибут [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) к типу объекта, `VisualElement` и его дочерних элементов будет привязан к. Обратите внимание на то, что этот атрибут можно повторно определить в любом расположении в иерархии представлений.

> [!NOTE]
> Рекомендуется задать `x:DataType` атрибут на одном уровне в иерархии представлений как [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) имеет значение.

Во время компиляции XAML любые недопустимые привязки выражения будет отображаться как ошибки построения. Тем не менее компилятор XAML только выдает ошибку построения для первого выражения недопустимую привязку, обнаружении. Любой допустимой привязки выражений, которые определены на `VisualElement` или его дочерних элементов будет скомпилировать, независимо от того, следует ли [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) установлено в XAML или коде. Компиляция выражения привязки создает скомпилированный код, который получит значение из свойства на *источника*и задайте его свойства в *целевой* , указанный в разметке. Кроме того, в зависимости от выражения привязки, созданный код может наблюдать за изменениями в значении параметра *источника* свойство и обновить *целевой* свойство и может отправить изменения из *целевой* обратно *источника*.

> [!IMPORTANT]
> Скомпилированный привязок в настоящее время отключены для любого выражения привязки, которые определяют [ `Source` ](xref:Xamarin.Forms.Binding.Source) свойство. Это обусловлено `Source` всегда будет задано значение с помощью `x:Reference` расширение разметки, которое не может быть разрешена во время компиляции.

## <a name="using-compiled-bindings"></a>Использование скомпилированных привязок

**Компиляции выбора цвета** страницы показано использование скомпилированных привязки между представлениями Xamarin.Forms и свойства ViewModel:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.CompiledColorSelectorPage"
             Title="Compiled Color Selector">
    ...
    <StackLayout x:DataType="local:HslColorViewModel">
        <StackLayout.BindingContext>
            <local:HslColorViewModel Color="Sienna" />
        </StackLayout.BindingContext>
        <BoxView Color="{Binding Color}"
                 ... />
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

Корневой [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) создает `HslColorViewModel` и инициализирует `Color` свойство в теги элемента свойства для [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) свойство. Этот корневой `StackLayout` также определяет `x:DataType` атрибута как тип модели представления, означает, что любые выражения привязки в корневом каталоге `StackLayout` Просмотр иерархии будет компилироваться. Это могут быть проверены при изменении любого из выражения привязки для привязки к несуществующее свойство ViewModel, что приведет к ошибке сборки.

> [!IMPORTANT]
> `x:DataType` Атрибут может быть повторно определено в любом месте иерархии представлений.

[ `BoxView` ](xref:Xamarin.Forms.BoxView), [ `Label` ](xref:Xamarin.Forms.Label) Элементов, и [ `Slider` ](xref:Xamarin.Forms.Slider) представления наследуют контекст привязки из [ `StackLayout` ](xref:Xamarin.Forms.StackLayout). Эти представления находятся все целевые объекты привязки, которые ссылаются на источник свойства в ViewModel. Для [ `BoxView.Color` ](xref:Xamarin.Forms.BoxView.Color) свойство и [ `Label.Text` ](xref:Xamarin.Forms.Label.Text) являются привязками данных свойств, `OneWay` — в представлении свойств в окне свойств в ViewModel. Тем не менее [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) используется в свойстве `TwoWay` привязки. Это позволит каждой `Slider` задаваемый из ViewModel, а также для ViewModel, устанавливаемое из каждого `Slider`.

При первом запуске приложения, [ `BoxView` ](xref:Xamarin.Forms.BoxView), [ `Label` ](xref:Xamarin.Forms.Label) элементов, и [ `Slider` ](xref:Xamarin.Forms.Slider) элементов имеют значение из модели представления, на основе Начальный `Color` свойство, задайте при создании экземпляра модели представления. Это показано на следующем снимке экрана:

[![Скомпилированные выбора цвета](compiled-bindings-images/compiledcolorselector-small.png "компиляции выбора цвета")](compiled-bindings-images/compiledcolorselector-large.png#lightbox "компиляции выбора цвета")

Как осуществляется ползунки, [ `BoxView` ](xref:Xamarin.Forms.BoxView) и [ `Label` ](xref:Xamarin.Forms.Label) элементы изменяются соответствующим образом.

Дополнительные сведения об этом выбора цвета, см. в разделе [ViewModels и уведомления об изменении свойства](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#viewmodels-and-property-change-notifications).

## <a name="using-compiled-bindings-in-a-datatemplate"></a>Использование скомпилированных привязок в DataTemplate

Привязки в [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) интерпретируются в контексте объекта, которому применяется шаблон. Таким образом, если с помощью скомпилирована привязок в `DataTemplate`, `DataTemplate` должен объявить тип объекта данных с помощью `x:DataType` атрибута.

**Компиляции список цветов** страницы показано использование привязок, скомпилированный в [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.CompiledColorListPage"
             Title="Compiled Color List">
    <Grid>
        ...
        <ListView x:Name="colorListView"
                  ItemsSource="{x:Static local:NamedColor.All}"
                  ... >
            <ListView.ItemTemplate>
                <DataTemplate x:DataType="local:NamedColor">
                    <ViewCell>
                        <StackLayout Orientation="Horizontal">
                            <BoxView Color="{Binding Color}"
                                     ... />
                            <Label Text="{Binding FriendlyName}"
                                   ... />
                        </StackLayout>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
        <!-- The BoxView doesn't use compiled bindings -->
        <BoxView Color="{Binding Source={x:Reference colorListView}, Path=SelectedItem.Color}"
                 ... />
    </Grid>
</ContentPage>
```

[ `ListView.ItemsSource` ](xref:Xamarin.Forms.ListView) Свойству присваивается статический `NamedColor.All` свойство. `NamedColor` Класс использует отражение .NET перечислить все открытые статические поля в [ `Color` ](xref:Xamarin.Forms.Color) структуры и для их хранения с именами в коллекции, которая доступна из статического `All` свойство. Таким образом `ListView` заполняется со всеми `NamedColor` экземпляров. Для каждого элемента в `ListView`, контекст привязки для элемента имеет значение `NamedColor` объекта. [ `BoxView` ](xref:Xamarin.Forms.BoxView) И [ `Label` ](xref:Xamarin.Forms.Label) элементов в [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) привязаны к `NamedColor` свойства.

Обратите внимание, что [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) определяет `x:DataType` атрибут, который будет `NamedColor` типа, указывающее, что все выражения в привязки `DataTemplate` Просмотр иерархии будет компилироваться. Это могут быть проверены при изменении любого из выражения привязки для привязки к несуществующей `NamedColor` свойство, которое приведет к ошибке сборки.

При первом запуске приложения, [ `ListView` ](xref:Xamarin.Forms.ListView) заполняется `NamedColor` экземпляров. При создании записи в `ListView` выбран, [ `BoxView.Color` ](xref:Xamarin.Forms.BoxView.Color) свойству присваивается цвет выбранного элемента в `ListView`:

[![Скомпилированные список цветов](compiled-bindings-images/compiledcolorlist-small.png "компиляции список цветов]")](compiled-bindings-images/compiledcolorlist-large.png#lightbox "Compiled Color List")

Выбор других элементов в [ `ListView` ](xref:Xamarin.Forms.BoxView) обновляет цвет [ `BoxView` ](xref:Xamarin.Forms.BoxView).

## <a name="combining-compiled-bindings-with-classic-bindings"></a>Объединение компиляции привязки с классической привязки

Привязка выражения компилируются только для иерархии представлений, `x:DataType` на определен атрибут. И наоборот, все представления в иерархии на котором `x:DataType` не определен атрибут будет использовать классический привязки. Таким образом можно объединить скомпилированного привязки и классическом на странице. Например, в предыдущем разделе представлениями в [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) использовать скомпилированные привязки, хотя [ `BoxView` ](xref:Xamarin.Forms.BoxView) , имеет значение цвета, выбранного в [ `ListView` ](xref:Xamarin.Forms.ListView) — нет.

Тщательное структурирование из `x:DataType` атрибутов таким образом может привести к страницы с использованием привязки скомпилированного и классической. Кроме того `x:DataType` атрибут может быть повторно определено в любой момент просмотреть иерархию, чтобы `null` с помощью `x:Null` расширение разметки. Это показывает, что любые выражения привязки в иерархии представление будет использовать классический привязки. *Смешанной привязки* страницы демонстрацией этого подхода:

```xaml
<StackLayout x:DataType="local:HslColorViewModel">
    <StackLayout.BindingContext>
        <local:HslColorViewModel Color="Sienna" />
    </StackLayout.BindingContext>
    <BoxView Color="{Binding Color}"
             VerticalOptions="FillAndExpand" />
    <StackLayout x:DataType="{x:Null}"
                 Margin="10, 0">
        <Label Text="{Binding Name}" />
        <Slider Value="{Binding Hue}" />
        <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />
        <Slider Value="{Binding Saturation}" />
        <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />
        <Slider Value="{Binding Luminosity}" />
        <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
    </StackLayout>
</StackLayout>   
```

Корневой [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) задает `x:DataType` атрибут, который будет `HslColorViewModel` типа, указывающее, что все выражение привязки в корневом каталоге `StackLayout` Просмотр иерархии будет компилироваться. Тем не менее внутреннее исключение `StackLayout` переопределяет `x:DataType` атрибут `null` с `x:Null` выражение разметки. Таким образом, в выражениях привязки внутреннего `StackLayout` использовать классический привязки. Только [ `BoxView` ](xref:Xamarin.Forms.BoxView), в корне `StackLayout` просматривать иерархию, привязки использует компиляции.

Дополнительные сведения о `x:Null` выражение разметки см. в разделе [расширение разметки x: Null](~/xamarin-forms/xaml/markup-extensions/consuming.md#null).

## <a name="performance"></a>Производительность

Скомпилированный привязки повышения производительности с различными преимущества производительности привязки данных. Модульное тестирование показывает, что:

- Скомпилированный привязку, которая использует уведомление об изменении свойства (т. е. `OneWay`, `OneWayToSource`, или `TwoWay` привязки) разрешается примерно 8 раз быстрее, чем классический привязки.
- Скомпилированный привязку, которая не использует уведомление об изменении свойства (т. е. `OneTime` привязки) разрешается примерно на 20 раз быстрее, чем классический привязки.
- Установка [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) в скомпилированной привязке, который использует свойство уведомления об изменениях (т. е. `OneWay`, `OneWayToSource`, или `TwoWay` привязки) приблизительно 5 раз быстрее, чем установка `BindingContext`в классическом привязке.
- Установка [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) скомпилированного привязки, не используйте свойство уведомления об изменениях (т. е. `OneTime` привязки) занимает примерно 7 раз быстрее, чем задано параметром `BindingContext` в классическом привязке.

Эти различия в производительности может увеличиваться на мобильных устройствах, зависящие от платформы, версии используемой операционной системы и устройства, на котором работает приложение.

## <a name="related-links"></a>Связанные ссылки

- [Демонстрации, привязка данных (пример)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
