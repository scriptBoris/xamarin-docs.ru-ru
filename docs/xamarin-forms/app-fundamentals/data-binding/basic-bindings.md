---
title: Привязки Xamarin.Forms Basic
description: 'В этой статье описывается использование Xamarin.Forms привязки данных, которые связывают паре свойств: между двумя объектами, по крайней мере один из которых обычно является объект пользовательского интерфейса. Эти два объекта, называются целевой объект и источник.'
ms.prod: xamarin
ms.assetid: 96553DF7-12EA-4FB2-AE85-3D1D59382B40
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: e31cba5c61624b0bca03443262b95d7497564750
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675202"
---
# <a name="xamarinforms-basic-bindings"></a>Привязки Xamarin.Forms Basic

Привязка данных Xamarin.Forms связывает паре свойств: между двумя объектами, по крайней мере один из которых обычно является объект пользовательского интерфейса. Эти два объекта, называются *целевой* и *источника*:

- *Целевой* является объект (и свойства), на которой задана привязка данных.
- *Источника* — объект (и свойства), ссылается на привязку данных.

Это различие иногда может быть немного сбить с толку: В самом простом случае потоки данных из источника в целевой объект, это означает, что целевое свойство имеет значение из значения свойства источника. Однако в некоторых случаях можно также потока данных от целевого объекта к источнику или в обоих направлениях. Чтобы избежать путаницы, имейте в виду, что целевой объект всегда является объект, на которой задана привязка данных, даже в том случае, если он предоставление данных, а не получения данных.

## <a name="bindings-with-a-binding-context"></a>Привязки с контекстом привязки

Несмотря на то, что данные привязки обычно указываются полностью в XAML, полезно просмотреть привязки данных в коде. **Базовый код привязки** страница содержит файл XAML с `Label` и `Slider`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.BasicCodeBindingPage"
             Title="Basic Code Binding">
    <StackLayout Padding="10, 0">
        <Label x:Name="label"
               Text="TEXT"
               FontSize="48"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

`Slider` Имеет значение для диапазона от 0 до 360. Цель этой программы состоит в том, чтобы повернуть `Label` , управляя `Slider`.

Без привязки данных, необходимо установить `ValueChanged` событие `Slider` обработчику событий, который обращается к `Value` свойство `Slider` и задает это значение `Rotation` свойство `Label`. Привязка данных позволяет автоматизировать задания; обработчик событий и код в ней более не нужны.

Можно задать привязку на экземпляр любого класса, производного от [ `BindableObject` ](xref:Xamarin.Forms.BindableObject), который включает `Element`, `VisualElement`, `View`, и `View` производные от него.  Привязка всегда имеет значение в целевом объекте. Привязка ссылается на исходный объект. Чтобы настроить привязку данных, используйте следующие два члена класса цели:

- [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) Свойство задает исходный объект.
- [ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) Метод указывает целевое свойство и свойство источника.

В этом примере `Label` является целевым объектом привязки и `Slider` собой источник привязки. Изменения в `Slider` источника влияют на угол поворота `Label` целевой объект. Потоки данных от источника к целевому объекту.

`SetBinding` Метода, определенного `BindableObject` имеет аргумент типа [ `BindingBase` ](xref:Xamarin.Forms.BindingBase) откуда [ `Binding` ](xref:Xamarin.Forms.Binding) класс является производным, но есть другие `SetBinding` методы определяется [ `BindableObjectExtensions` ](xref:Xamarin.Forms.BindableObjectExtensions) класса. Файл с выделенным кодом в **базовый код привязки** образце используется более простой [ `SetBinding` ](xref:Xamarin.Forms.BindableObjectExtensions.SetBinding*) метод расширения из этого класса.

```csharp
public partial class BasicCodeBindingPage : ContentPage
{
    public BasicCodeBindingPage()
    {
        InitializeComponent();

        label.BindingContext = slider;
        label.SetBinding(Label.RotationProperty, "Value");
    }
}
```

`Label` Объекта является целевым объектом привязки, поэтому, — это объект, на котором это свойство имеет значение и на котором вызывается метод. `BindingContext` Свойство указывает источник привязки, который является `Slider`.

`SetBinding` Метод вызывается для целевого объекта привязки, но указывает целевое свойство и свойство источника. Целевое свойство указывается как `BindableProperty` объект: `Label.RotationProperty`. Свойство источника указан как строка и указывает `Value` свойство `Slider`.

`SetBinding` Метод раскрывает одно из наиболее важных правил привязки данных:

*Целевое свойство должно поддерживаться может быть привязано.*

Это правило предполагает, что целевой объект должен представлять собой экземпляр класса, производного от `BindableObject`. См. в разделе [ **привязываемые свойства** ](~/xamarin-forms/xaml/bindable-properties.md) статью для связывания объектов и связываемые свойства.

Нет такого правила для свойства источника, который указан как строка. На внутреннем уровне отражение используется для доступа к Фактическое свойство. В данном случае, однако `Value` свойство поддерживается также может быть привязано.

Код может быть немного упрощенное: `RotationProperty` определяется свойство, используемое `VisualElement`и наследуется `Label` и `ContentPage` , поэтому имя класса, которые не обязательно в `SetBinding` вызова:

```csharp
label.SetBinding(RotationProperty, "Value");
```

Тем не менее включая имя класса является хорошим напоминание целевого объекта.

Во время работы `Slider`, `Label` поворачивает соответствующим образом:

[![Базовый код привязки](basic-bindings-images/basiccodebinding-small.png "базовый код привязки")](basic-bindings-images/basiccodebinding-large.png#lightbox "базовый код привязки")

**Простая привязка Xaml** страница идентична **основной код привязки** за исключением того, что все данные привязки в XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.BasicXamlBindingPage"
             Title="Basic XAML Binding">
    <StackLayout Padding="10, 0">
        <Label Text="TEXT"
               FontSize="80"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand"
               BindingContext="{x:Reference Name=slider}"
               Rotation="{Binding Path=Value}" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

Так же, как в коде, привязка данных задается в целевом объекте, который является `Label`. Участвуют два расширения разметки XAML. Ниже приведены мгновенно запоминающееся фигурную скобку.

- `x:Reference` Требуется расширение разметки для ссылки на исходный объект, который является `Slider` с именем `slider`.
- `Binding` Ссылок расширения разметки `Rotation` свойство `Label` для `Value` свойство `Slider`.

См. в статье [расширения разметки XAML](~/xamarin-forms/xaml/markup-extensions/index.md) Дополнительные сведения о расширениях разметки XAML. `x:Reference` Расширение разметки поддерживается [ `ReferenceExtension` ](xref:Xamarin.Forms.Xaml.ReferenceExtension) класса; `Binding` поддерживается [ `BindingExtension` ](xref:Xamarin.Forms.Xaml.BindingExtension) класса. Как XML указать префиксы пространств имен, `x:Reference` является частью спецификации XAML 2009, хотя `Binding` является частью Xamarin.Forms. Обратите внимание на то, что кавычки отображаются в фигурные скобки.

Это легко забыть `x:Reference` расширения разметки при задании `BindingContext`. Очень часто ошибочно присвоено свойство непосредственно имя источника привязки к следующим образом:

```xaml
BindingContext="slider"
```

Но это не так. Эта разметка задает `BindingContext` свойства `string` объекта, символы которой написано «ползунок»!

Обратите внимание, что свойство источника указано с [ `Path` ](xref:Xamarin.Forms.Xaml.BindingExtension.Path) свойство `BindingExtension`, который соответствует [ `Path` ](xref:Xamarin.Forms.Binding.Path) свойство [ `Binding` ](xref:Xamarin.Forms.Binding) класса.

Разметка, показанная на **основные привязки XAML** страницы можно упростить: расширения разметки XAML, такие как `x:Reference` и `Binding` может иметь *свойства содержимого* определенные атрибуты, что для XAML расширения разметки означает, что имя свойства не нужно отображать. `Name` Свойство является свойством содержимого из `x:Reference`и `Path` свойство является свойством содержимого из `Binding`, что означает, что их можно удалить из выражения:

```xaml
<Label Text="TEXT"
       FontSize="80"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       BindingContext="{x:Reference slider}"
       Rotation="{Binding Value}" />
```

## <a name="bindings-without-a-binding-context"></a>Привязки без контекста привязки

`BindingContext` Свойство является важным компонентом привязок данных, но это не всегда необходимо. Вместо этого исходного объекта можно указать в `SetBinding` вызова или `Binding` расширение разметки.

Это показано в **привязки кода вместо** образца. Файл XAML аналогичен **базовый код привязки** пример за одним исключением `Slider` определен элемент управления `Scale` свойство `Label`. По этой причине `Slider` имеет значение для диапазона &ndash;2 до 2:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.AlternativeCodeBindingPage"
             Title="Alternative Code Binding">
    <StackLayout Padding="10, 0">
        <Label x:Name="label"
               Text="TEXT"
               FontSize="40"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Minimum="-2"
                Maximum="2"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

Файл с выделенным кодом задает привязку с [ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) метода, определенного `BindableObject`. Аргумент является [конструктор](xref:Xamarin.Forms.Binding.%23ctor(System.String,Xamarin.Forms.BindingMode,Xamarin.Forms.IValueConverter,System.Object,System.String,System.Object)) для [ `Binding` ](xref:Xamarin.Forms.Binding) класса:

```csharp
public partial class AlternativeCodeBindingPage : ContentPage
{
    public AlternativeCodeBindingPage()
    {
        InitializeComponent();

        label.SetBinding(Label.ScaleProperty, new Binding("Value", source: slider));
    }
}
```

`Binding` Конструктор имеет 6 параметров, поэтому `source` параметр указан с именованным аргументом. Аргумент является `slider` объекта.

Выполнение программы может быть немного неожиданным:

[![Альтернативный код привязки](basic-bindings-images/alternativecodebinding-small.png "альтернативный код привязки")](basic-bindings-images/alternativecodebinding-large.png#lightbox "альтернативный код привязки")

На экране iOS слева показано, как выглядит экран, при первом открытии страницы. Где является `Label`?

Проблема в том, что `Slider` начальное значение 0. В результате `Scale` свойство `Label` быть также установлен в 0, переопределяя значение по умолчанию 1. В результате `Label` , первоначально невидимо. Как показано на снимках экрана Android и универсальной платформы Windows (UWP), можно управлять `Slider` вносить `Label` отображаются снова, но его начальной исчезновение неприятна.

Вы можете узнать из [следующей статье](binding-mode.md) как избежать этой проблемы, инициализация `Slider` из значения по умолчанию `Scale` свойство.

> [!NOTE]
> [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) Класс также определяет [ `ScaleX` ](xref:Xamarin.Forms.VisualElement.ScaleX) и [ `ScaleY` ](xref:Xamarin.Forms.VisualElement.ScaleY) свойства, которые можно масштабировать `VisualElement` по-разному в горизонтальном и вертикальном направлениях.

**Привязки XAML альтернатива** странице отображаются эквивалентное привязки полностью в XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.AlternativeXamlBindingPage"
             Title="Alternative XAML Binding">
    <StackLayout Padding="10, 0">
        <Label Text="TEXT"
               FontSize="40"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand"
               Scale="{Binding Source={x:Reference slider},
                               Path=Value}" />

        <Slider x:Name="slider"
                Minimum="-2"
                Maximum="2"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

Теперь `Binding` расширение разметки имеет два свойства, заданные, `Source` и `Path`, разделенные запятыми. Если вы предпочитаете, они могут отображаться на одной строке:

```xaml
Scale="{Binding Source={x:Reference slider}, Path=Value}" />
```

`Source` Свойству на встроенный `x:Reference` расширение разметки, в противном случае имеет тот же синтаксис, как параметр `BindingContext`. Обратите внимание, что кавычки отображаются в фигурные скобки, и что эти два свойства должны быть разделены запятыми.

Свойство содержимого `Binding` расширение разметки `Path`, но `Path=` частью расширения разметки можно удалить, только если это первое свойство в выражении. Чтобы исключить `Path=` часть, необходимо заменить два свойства:

```xaml
Scale="{Binding Value, Source={x:Reference slider}}" />
```

Несмотря на то, что расширения разметки XAML обычно разделены с помощью фигурных скобок, они также может быть выражен как элементы объекта:

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand">
    <Label.Scale>
        <Binding Source="{x:Reference slider}"
                 Path="Value" />
    </Label.Scale>
</Label>
```

Теперь `Source` и `Path` свойства являются обычные атрибуты XAML: значения заключать в кавычки и атрибуты не разделяются запятыми. `x:Reference` Расширение разметки может стать объектного элемента:

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand">
    <Label.Scale>
        <Binding Path="Value">
            <Binding.Source>
                <x:Reference Name="slider" />
            </Binding.Source>
        </Binding>
    </Label.Scale>
</Label>
```

Этот синтаксис не стандартным, но иногда бывает при использовании сложных объектов.

В примерах, показанных до сих задается `BindingContext` свойство и `Source` свойство `Binding` для `x:Reference` расширения разметки для ссылки на другое представление, на странице. Эти два свойства имеют тип `Object`, и их можно задать для любого объекта, который содержит свойства, которые подходят для источников привязки.

В статьях вперед, вы обнаружите, что можно установить `BindingContext` или `Source` свойства `x:Static` расширения разметки для ссылки на значение статического свойства или поля, или `StaticResource` расширения разметки для ссылки на объект, хранящийся в словарь ресурсов, или непосредственно на объект, который является обычно (но не всегда) экземпляра модели представления.

`BindingContext` Свойство также может быть присвоено `Binding` объекта, чтобы `Source` и `Path` свойства `Binding` определяют контекст привязки.

## <a name="binding-context-inheritance"></a>Наследование контекста привязки

В этой статье вы увидели, что можно указать объект источника с помощью `BindingContext` свойство или `Source` свойство `Binding` объекта. Если заданы оба, `Source` свойство `Binding` имеет приоритет над `BindingContext`.

`BindingContext` Свойство имеет очень важной характеристикой:

*Параметр `BindingContext` свойство наследуется от визуального дерева.*

Как вы увидите, это может оказаться очень удобным, для упрощения выражения привязки, а в некоторых случаях &mdash; особенно в сценариях Model-View-ViewModel (MVVM) &mdash; очень важно.

**Наследование контекста привязки** образец является простая демонстрация наследования в контексте привязки:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.BindingContextInheritancePage"
             Title="BindingContext Inheritance">
    <StackLayout Padding="10">

        <StackLayout VerticalOptions="FillAndExpand"
                     BindingContext="{x:Reference slider}">

            <Label Text="TEXT"
                   FontSize="80"
                   HorizontalOptions="Center"
                   VerticalOptions="EndAndExpand"
                   Rotation="{Binding Value}" />

            <BoxView Color="#800000FF"
                     WidthRequest="180"
                     HeightRequest="40"
                     HorizontalOptions="Center"
                     VerticalOptions="StartAndExpand"
                     Rotation="{Binding Value}" />
        </StackLayout>

        <Slider x:Name="slider"
                Maximum="360" />

    </StackLayout>
</ContentPage>
```

`BindingContext` Свойство `StackLayout` присваивается `slider` объекта. Оба наследуют этот контекст привязки `Label` и `BoxView`, оба из которых их `Rotation` свойства значения `Value` свойство `Slider`:

[![Привязка наследование контекста](basic-bindings-images/bindingcontextinheritance-small.png "привязки наследование контекста")](basic-bindings-images/bindingcontextinheritance-large.png#lightbox "привязки контекста наследования")

В [следующей статье](binding-mode.md), вы увидите как *режим привязки* можно изменить поток данных между исходной и целевой объекты.


## <a name="related-links"></a>Связанные ссылки

- [Демонстрации, привязка данных (пример)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Данные привязки Глава из книги по Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
