---
title: Неявные стили в Xamarin.Forms
description: Неявный стиль, которое используется для всех элементов управления из же TargetType, без необходимости каждый элемент управления для ссылки на стиль.
ms.prod: xamarin
ms.assetid: 02A75F3B-4389-49D4-A2F4-AFD473A4A161
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 277be51c242521f52e9b1e162226ae8137e7b133
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995518"
---
# <a name="implicit-styles-in-xamarinforms"></a>Неявные стили в Xamarin.Forms

_Неявный стиль, которое используется для всех элементов управления из же TargetType, без необходимости каждый элемент управления для ссылки на стиль._

## <a name="creating-an-implicit-style-in-xaml"></a>Создание неявного стиля в XAML

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

### <a name="creating-an-implicit-style-at-the-control-level"></a>Создание неявного стиля в элемент управления уровнем

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

## <a name="creating-an-implicit-style-in-c35"></a>Создание неявного стиля в C&#35;

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

## <a name="summary"></a>Сводка

*Неявное* стиль является одним из используемых все визуальные элементы в одной и той же [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType), не требуя от каждого элемента управления для ссылки на стиль. Объект `Style` становится *неявное* , не указывая `x:Key` атрибута. Вместо этого `x:Key` атрибут автоматически станет значение [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) свойство.



## <a name="related-links"></a>Связанные ссылки

- [Расширения разметки XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Основные стили (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [Работа с использованием стилей (пример)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Стиль](xref:Xamarin.Forms.Style)
- [Метод задания](xref:Xamarin.Forms.Setter)
