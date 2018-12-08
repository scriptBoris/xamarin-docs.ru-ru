---
title: Наследование стилей в Xamarin.Forms
description: Стили можно наследовать другие стили, чтобы сократить дублирование и включить повторное использование. В этой статье объясняется, как выполнять наследование стилей в приложении Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 67A3A39C-8CC0-446D-8162-FFA73582D3B8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 05ec368fb0dd0e1adfac3eed88c5ddd50960f9ae
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53056588"
---
# <a name="style-inheritance-in-xamarinforms"></a>Наследование стилей в Xamarin.Forms

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)

_Стили можно наследовать другие стили, чтобы сократить дублирование и включить повторное использование._

## <a name="style-inheritance-in-xaml"></a>Наследование стилей в XAML

Наследование стилей выполняется путем задания [ `Style.BasedOn` ](xref:Xamarin.Forms.Style.BasedOn) свойство к существующему [ `Style` ](xref:Xamarin.Forms.Style). В XAML, это можно сделать путем установки `BasedOn` свойства `StaticResource` расширение разметки, который ссылается на ранее созданный объект `Style`. В C# это можно сделать путем установки `BasedOn` свойства `Style` экземпляра.

Стили, которые наследуют от базового стиля могут включать [ `Setter` ](xref:Xamarin.Forms.Setter) экземпляров для новых свойств или использовать их для переопределения стили из базового стиля. Кроме того стили, которые наследуют от базового стиля должны предназначаться для одного типа или типа, производного от типа мишенью для базового стиля. Например, если предназначен для базового стиля [ `View` ](xref:Xamarin.Forms.View) экземпляров, стили, основанные на базовом стиле можно ориентироваться `View` экземпляров или типы, производные от `View` класс, например [ `Label` ](xref:Xamarin.Forms.Label) и [ `Button` ](xref:Xamarin.Forms.Button) экземпляров.

В следующем коде показано *явные* стиля наследования в страницу XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.StyleInheritancePage" Title="Inheritance" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
                <Setter Property="HorizontalOptions"
                        Value="Center" />
                <Setter Property="VerticalOptions"
                        Value="CenterAndExpand" />
            </Style>
            <Style x:Key="labelStyle" TargetType="Label"
                   BasedOn="{StaticResource baseStyle}">
                ...
                <Setter Property="TextColor" Value="Teal" />
            </Style>
            <Style x:Key="buttonStyle" TargetType="Button"
                   BasedOn="{StaticResource baseStyle}">
                <Setter Property="BorderColor" Value="Lime" />
                ...
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="These labels"
                   Style="{StaticResource labelStyle}" />
            ...
            <Button Text="So is the button"
                    Style="{StaticResource buttonStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

`baseStyle` Целевых объектов [ `View` ](xref:Xamarin.Forms.View) экземпляров и задает [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) и [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) свойства. `baseStyle` Не задано непосредственно в любом элементе управления. Вместо этого `labelStyle` и `buttonStyle` наследовать от него, параметров дополнительных связываемые свойства. `labelStyle` И `buttonStyle` применяются к [ `Label` ](xref:Xamarin.Forms.Label) экземпляров и [ `Button` ](xref:Xamarin.Forms.Button) экземпляра, установив их [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) свойства. Это приводит к появлению, показано на следующем снимке экрана:

[![](inheritance-images/style-inheritance.png)](inheritance-images/style-inheritance-large.png#lightbox)

> [!NOTE]
> Неявный стиль может быть производным от явный стиль, но явный стиль не может быть производным от неявного стиля.

### <a name="respecting-the-inheritance-chain"></a>Соблюдение цепочке наследования

Стиль может наследовать только от стили на том же уровне или выше в иерархии представлений. Это означает следующее.

- Ресурс уровня приложения может наследовать только от других ресурсов уровня приложения.
- Ресурсов на уровне страницы может наследовать от ресурсы уровня приложения и другие ресурсы уровня страницы.
- Ресурсов на уровне элемента управления могут наследовать от ресурсы уровня приложения, ресурсы на уровне страницы и другие ресурсы уровня управления.

В следующем примере кода демонстрируется эта цепочка наследования:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.StyleInheritancePage" Title="Inheritance" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style x:Key="labelStyle" TargetType="Label" BasedOn="{StaticResource baseStyle}">
                      ...
                    </Style>
                    <Style x:Key="buttonStyle" TargetType="Button" BasedOn="{StaticResource baseStyle}">
                      ...
                    </Style>
                </ResourceDictionary>
            </StackLayout.Resources>
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

В этом примере `labelStyle` и `buttonStyle` являются управления ресурсами уровня, хотя `baseStyle` является ресурсом уровня страницы. Однако, хотя `labelStyle` и `buttonStyle` наследовать от `baseStyle`, невозможно для `baseStyle` наследование `labelStyle` или `buttonStyle`из- за своих подразделениях в иерархии представлений.

## <a name="style-inheritance-in-c35"></a>Наследование стилей в C&#35;

Эквивалент C# страницы, где [ `Style` ](xref:Xamarin.Forms.Style) экземпляров назначенные непосредственно [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) необходимых элементов управления, показан в следующем примере кода:

```csharp
public class StyleInheritancePageCS : ContentPage
{
    public StyleInheritancePageCS ()
    {
        var baseStyle = new Style (typeof(View)) {
            Setters = {
                new Setter {
                    Property = View.HorizontalOptionsProperty, Value = LayoutOptions.Center    },
                ...
            }
        };

        var labelStyle = new Style (typeof(Label)) {
            BasedOn = baseStyle,
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Teal    }
            }
        };

        var buttonStyle = new Style (typeof(Button)) {
            BasedOn = baseStyle,
            Setters = {
                new Setter { Property = Button.BorderColorProperty, Value =    Color.Lime },
                ...
            }
        };
        ...

        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels", Style = labelStyle },
                ...
                new Button { Text = "So is the button", Style = buttonStyle }
            }
        };
    }
}
```

`baseStyle` Целевых объектов [ `View` ](xref:Xamarin.Forms.View) экземпляров и задает [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) и [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) свойства. `baseStyle` Не задано непосредственно в любом элементе управления. Вместо этого `labelStyle` и `buttonStyle` наследовать от него, параметров дополнительных связываемые свойства. `labelStyle` И `buttonStyle` применяются к [ `Label` ](xref:Xamarin.Forms.Label) экземпляров и [ `Button` ](xref:Xamarin.Forms.Button) экземпляра, установив их [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) свойства.

## <a name="summary"></a>Сводка

Стили можно наследовать другие стили, чтобы сократить дублирование и включить повторное использование. Наследование стилей выполняется путем задания [ `Style.BasedOn` ](xref:Xamarin.Forms.Style.BasedOn) свойство к существующему [ `Style` ](xref:Xamarin.Forms.Style).


## <a name="related-links"></a>Связанные ссылки

- [Расширения разметки XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Основные стили (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [Работа с использованием стилей (пример)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Стиль](xref:Xamarin.Forms.Style)
- [Метод задания](xref:Xamarin.Forms.Setter)
