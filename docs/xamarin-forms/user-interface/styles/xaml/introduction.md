---
title: Введение в стили Xamarin.Forms
description: Стили позволяют внешний вид визуальных элементов для специальной настройки. Стили определяются для конкретного типа и содержит значения для свойств, доступных для этого типа.
ms.prod: xamarin
ms.assetid: 3FF899C0-6CFB-4C1D-837D-9E9E10181967
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 6f5af5fde46676cf669e6c02fb83f4aac5d31c46
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55292094"
---
# <a name="introduction-to-xamarinforms-styles"></a>Введение в стили Xamarin.Forms

_Стили позволяют внешний вид визуальных элементов для специальной настройки. Стили определяются для конкретного типа и содержит значения для свойств, доступных для этого типа._

Приложения Xamarin.Forms часто содержат несколько элементов управления, имеющих идентичным внешним видом. Например, приложение может иметь несколько [ `Label` ](xref:Xamarin.Forms.Label) экземпляров, которые имеют одинаковые параметры шрифта и параметры макета, как показано в следующем примере кода XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    x:Class="Styles.NoStylesPage"
    Title="No Styles"
    Icon="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="These labels"
                   HorizontalOptions="Center"
                   VerticalOptions="CenterAndExpand"
                   FontSize="Large" />
            <Label Text="are not"
                   HorizontalOptions="Center"
                   VerticalOptions="CenterAndExpand"
                   FontSize="Large" />
            <Label Text="using styles"
                   HorizontalOptions="Center"
                   VerticalOptions="CenterAndExpand"
                   FontSize="Large" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

В следующем примере кода создается эквивалентная страница на языке C#:

```csharp
public class NoStylesPageCS : ContentPage
{
    public NoStylesPageCS ()
    {
        Title = "No Styles";
        Icon = "csharp.png";
        Padding = new Thickness (0, 20, 0, 0);

        Content = new StackLayout {
            Children = {
                new Label {
                    Text = "These labels",
                    HorizontalOptions = LayoutOptions.Center,
                    VerticalOptions = LayoutOptions.CenterAndExpand,
                    FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label))
                },
                new Label {
                    Text = "are not",
                    HorizontalOptions = LayoutOptions.Center,
                    VerticalOptions = LayoutOptions.CenterAndExpand,
                    FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label))
                },
                new Label {
                    Text = "using styles",
                    HorizontalOptions = LayoutOptions.Center,
                    VerticalOptions = LayoutOptions.CenterAndExpand,
                    FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label))
                }
            }
        };
    }
}
```

Каждый [ `Label` ](xref:Xamarin.Forms.Label) экземпляр имеет одинаковые значения свойств для внешний вид текста, отображаемого элементом управления `Label`. Это приводит к появлению, показано на следующем снимке экрана:

[![](introduction-images/no-styles.png "Метка внешний вид, без учета стилей")](introduction-images/no-styles-large.png#lightbox "метки внешний вид, без учета стилей")

Настройка внешнего вида каждого отдельного элемента управления может быть повторяющихся и подвержено ошибкам. Вместо этого стиля могут создаваться, определяет внешний вид, а затем применяются к необходимыми элементами управления.

## <a name="create-a-style"></a>Создание стиля

[ `Style` ](xref:Xamarin.Forms.Style) Класс группирует коллекцию значений свойств в один объект, который затем можно применить к нескольким экземплярам визуальный элемент. Это позволяет сократить повтора разметки и позволяет внешний вид приложения, проще изменять.

Несмотря на то, что стили предназначены главным образом для приложений на основе XAML, они также могут создаваться в C#:

- [`Style`](xref:Xamarin.Forms.Style) экземпляры, созданные в XAML обычно определяются в [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) , назначенный [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) коллекцию элемента управления, странице или к [ `Resources` ](xref:Xamarin.Forms.Application.Resources) коллекции приложения.
- [`Style`](xref:Xamarin.Forms.Style) экземпляры, созданные на языке C# обычно определяются в классе страницы или в классе, который может осуществляться глобально.

От того, где определен шаблон [`Style`](xref:Xamarin.Forms.Style), зависит то, где его можно использовать.

- [`Style`](xref:Xamarin.Forms.Style) экземпляров, определенные на уровне управления может применяться только к элементу управления и с его дочерними элементами.
- [`Style`](xref:Xamarin.Forms.Style) экземпляров, определенные на уровне страниц может применяться только к странице и с его дочерними элементами.
- [`Style`](xref:Xamarin.Forms.Style) экземпляры, определенные на уровне приложения можно применять в приложении.

Каждый [ `Style` ](xref:Xamarin.Forms.Style) экземпляр содержит коллекцию из одного или нескольких [ `Setter` ](xref:Xamarin.Forms.Setter) объектов, каждый `Setter` необходимости [ `Property` ](xref:Xamarin.Forms.Setter.Property) и [`Value`](xref:Xamarin.Forms.Setter.Value). `Property` Имя привязываемые свойства элемента применяется стиль, и `Value` является значением, которое применяется к свойству.

Каждый [ `Style` ](xref:Xamarin.Forms.Style) экземпляр может быть *явные*, или *неявное*:

- *Явные* [ `Style` ](xref:Xamarin.Forms.Style) экземпляр определяется путем указания [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) и `x:Key` значение, а также задать целевого элемента [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) свойства `x:Key` ссылки. Дополнительные сведения о *явные* стили, см. в разделе [явные стили](~/xamarin-forms/user-interface/styles/explicit.md).
- *Неявное* [ `Style` ](xref:Xamarin.Forms.Style) экземпляр определяется путем указания только [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType). `Style` Экземпляр затем автоматически применяются ко всем элементам этого типа. Обратите внимание, подклассы `TargetType` , не получают автоматически `Style` применения. Дополнительные сведения о *неявное* стили, см. в разделе [неявные стили](~/xamarin-forms/user-interface/styles/implicit.md).

При создании [ `Style` ](xref:Xamarin.Forms.Style), [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) свойство всегда является обязательным. В следующем коде показано в примере *явные* стиля (Примечание `x:Key`) создан в XAML:

```xaml
<Style x:Key="labelStyle" TargetType="Label">
    <Setter Property="HorizontalOptions" Value="Center" />
    <Setter Property="VerticalOptions" Value="CenterAndExpand" />
    <Setter Property="FontSize" Value="Large" />
</Style>
```

Чтобы применить `Style`, целевой объект должен иметь [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) , соответствующий [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) значение свойства `Style`, как показано в следующем примере кода XAML:

```xaml
<Label Text="Demonstrating an explicit style" Style="{StaticResource labelStyle}" />
```

Стили более низкого уровня в иерархии представлений имеют приоритет над определенных выше вверх. Например, установка [ `Style` ](xref:Xamarin.Forms.Style) , задает [ `Label.TextColor` ](xref:Xamarin.Forms.Label.TextColor) для `Red` приложения будут переопределены уровень стиль уровня страницы, который задает `Label.TextColor` для `Green`. Аналогичным образом стиль уровня будут переопределены уровня стиль элемента управления. Кроме того Если `Label.TextColor` задано напрямую на свойства элемента управления, имеет приоритет над любые стили.

Статьи в этом разделе демонстрируют и объясняется, как создать и применить *явные* и *неявное* стили, как создать глобальные стили, стиль наследования, как реагировать на изменения стиля во время выполнения и как использовать встроенные стили, включенные в Xamarin.Forms.

> [!NOTE]
> **Что такое StyleId?**
>
> Предшествующих версии 2.2 Xamarin.Forms [ `StyleId` ](xref:Xamarin.Forms.Element.StyleId) свойство используется для идентификации отдельных элементов в приложении для идентификации при тестировании пользовательского интерфейса, а также в тему движков, таких как Pixate. Тем не менее, представила Xamarin.Forms 2.2 [ `AutomationId` ](xref:Xamarin.Forms.Element.AutomationId) свойство, которое используется для [ `StyleId` ](xref:Xamarin.Forms.Element.StyleId) свойство. Дополнительные сведения см. в разделе [Xamarin.Forms, автоматизировать тестирование с помощью Xamarin.UITest и тестовое облако](~/xamarin-forms/deploy-test/uitest-and-test-cloud.md).

## <a name="related-links"></a>Связанные ссылки

- [Расширения разметки XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Стиль](xref:Xamarin.Forms.Style)
- [Метод задания](xref:Xamarin.Forms.Setter)
