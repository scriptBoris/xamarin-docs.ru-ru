---
title: Неявные стили в Xamarin.Forms
description: Неявный стиль, которое используется для всех элементов управления из же TargetType, без необходимости каждый элемент управления для ссылки на стиль.
ms.prod: xamarin
ms.assetid: 02A75F3B-4389-49D4-A2F4-AFD473A4A161
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/30/2019
ms.openlocfilehash: 67b8bac62cacb091323d084e1c7cec9accc30844
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55291977"
---
# <a name="implicit-styles-in-xamarinforms"></a>Неявные стили в Xamarin.Forms

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)

_Неявный стиль, которое используется для всех элементов управления из же TargetType, без необходимости каждый элемент управления для ссылки на стиль._

## <a name="create-an-implicit-style-in-xaml"></a>Создание неявного стиля в XAML

Чтобы объявить [ `Style` ](xref:Xamarin.Forms.Style) на уровне страницы, [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) должны добавляться к странице и затем одному или нескольким `Style` объявления могут быть включены в `ResourceDictionary`. Объект `Style` становится *неявное* , не указывая `x:Key` атрибута. Затем будет применен стиль к визуальным элементам, которые соответствуют `TargetType` точно, но не к элементам, которые являются производными от `TargetType` значение.

В следующем коде показано в примере *неявное* стиль, объявленных в XAML, на странице `ResourceDictionary`и применяется к странице [ `Entry` ](xref:Xamarin.Forms.Entry) экземпляров:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Styles;assembly=Styles" x:Class="Styles.ImplicitStylesPage" Title="Implicit" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Entry">
                <Setter Property="HorizontalOptions" Value="Fill" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
                <Setter Property="BackgroundColor" Value="Yellow" />
                <Setter Property="FontAttributes" Value="Italic" />
                <Setter Property="TextColor" Value="Blue" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Entry Text="These entries" />
            <Entry Text="are demonstrating" />
            <Entry Text="implicit styles," />
            <Entry Text="and an implicit style override" BackgroundColor="Lime" TextColor="Red" />
            <local:CustomEntry Text="Subclassed Entry is not receiving the style" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) Определяет одно *неявное* стиль, применяемый к странице [ `Entry` ](xref:Xamarin.Forms.Entry) экземпляров. `Style` Используется для отображения синий текст на желтом фоне, а для параметра другие параметры оформления. `Style` Добавляется на страницу [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) без указания `x:Key` атрибута. Таким образом `Style` применяется ко всем `Entry` экземпляров неявно, так как они соответствуют [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) свойство `Style` точно. Тем не менее `Style` не применяется к `CustomEntry` экземпляра, то есть, выведенных в подклассы `Entry`. Это приводит к появлению, показано на следующем снимке экрана:

[![](implicit-images/implicit-styles.png "Пример неявные стили")](implicit-images/implicit-styles-large.png#lightbox "пример неявные стили")

Кроме того, четвертый [ `Entry` ](xref:Xamarin.Forms.Entry) переопределяет [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor) и [ `TextColor` ](xref:Xamarin.Forms.Entry.TextColor) свойства неявный стиль для разных `Color`значения.

### <a name="create-an-implicit-style-at-the-control-level"></a>Создать неявный стиль на уровне управления

В дополнение к созданию *неявное* стили на уровне страницы, они также могут создаваться на уровне управления, как показано в следующем примере кода:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Styles;assembly=Styles" x:Class="Styles.ImplicitStylesPage" Title="Implicit" Icon="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style TargetType="Entry">
                        <Setter Property="HorizontalOptions" Value="Fill" />
                        ...
                    </Style>
                </ResourceDictionary>
            </StackLayout.Resources>
            <Entry Text="These entries" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

В этом примере *неявное* [ `Style` ](xref:Xamarin.Forms.Style) назначается [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) коллекцию [ `StackLayout` ](xref:Xamarin.Forms.StackLayout)элемента управления. *Неявное* стиль затем могут применяться в элемент управления и его дочерних элементов.

Сведения о создании стилей в приложения [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), см. в разделе [глобальные стили](~/xamarin-forms/user-interface/styles/application.md).

## <a name="create-an-implicit-style-in-c35"></a>Создание неявного стиля в C&#35;

[`Style`](xref:Xamarin.Forms.Style) экземпляры можно добавить на страницу [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) коллекции в C# путем создания нового [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)и затем добавив `Style` экземпляры `ResourceDictionary`, как показано на Следующий пример кода:

```csharp
public class ImplicitStylesPageCS : ContentPage
{
    public ImplicitStylesPageCS ()
    {
        var entryStyle = new Style (typeof(Entry)) {
            Setters = {
                ...
                new Setter { Property = Entry.TextColorProperty, Value = Color.Blue }
            }
        };

        ...
        Resources = new ResourceDictionary ();
        Resources.Add (entryStyle);

        Content = new StackLayout {
            Children = {
                new Entry { Text = "These entries" },
                new Entry { Text = "are demonstrating" },
                new Entry { Text = "implicit styles," },
                new Entry { Text = "and an implicit style override", BackgroundColor = Color.Lime, TextColor = Color.Red },
                new CustomEntry  { Text = "Subclassed Entry is not receiving the style" }
            }
        };
    }
}
```

Конструктор определяет одно *неявное* стиль, применяемый к странице [ `Entry` ](xref:Xamarin.Forms.Entry) экземпляров. `Style` Используется для отображения синий текст на желтом фоне, а для параметра другие параметры оформления. `Style` Добавляется на страницу [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) без указания `key` строка. Таким образом `Style` применяется ко всем `Entry` экземпляров неявно, так как они соответствуют [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) свойство `Style` точно. Тем не менее `Style` не применяется к `CustomEntry` экземпляра, то есть, выведенных в подклассы `Entry`.

## <a name="apply-a-style-to-derived-types"></a>Применение стиля к производным типам

[ `Style.ApplyToDerivedTypes` ](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) Свойство позволяет стиль, применяемый к элементам управления, которые являются производными от базового типа, который ссылается [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) свойство. Таким образом, этому свойству присвоить `true` включает один стиль для различных типов, при условии, что типы являются производными от базового типа, указанного в `TargetType` свойство.

В следующем примере показано неявный стиль, который задает цвет фона [ `Button` ](xref:Xamarin.Forms.Button) экземпляров red:

```xaml
<Style TargetType="Button"
       ApplyToDerivedTypes="True">
    <Setter Property="BackgroundColor"
            Value="Red" />
</Style>
```

Поместив этот стиль на уровне страницы [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) приведет к его применяется ко всем [ `Button` ](xref:Xamarin.Forms.Button) экземпляров на странице, а также любых элементов управления, которые являются производными от `Button`. Тем не менее если [ `ApplyToDerivedTypes` ](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) свойство отображается как не задано, стиль будет применяться только к `Button` экземпляров.

Ниже приведен аналогичный код C#:

```csharp
var buttonStyle = new Style(typeof(Button))
{
    ApplyToDerivedTypes = true,
    Setters =
    {
        new Setter
        {
            Property = VisualElement.BackgroundColorProperty,
            Value = Color.Red
        }
    }
};

Resources = new ResourceDictionary { buttonStyle };
```

## <a name="related-links"></a>Связанные ссылки

- [Расширения разметки XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Основные стили (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [Работа с использованием стилей (пример)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Стиль](xref:Xamarin.Forms.Style)
- [Метод задания](xref:Xamarin.Forms.Setter)
