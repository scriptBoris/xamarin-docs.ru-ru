---
title: Метка Xamarin.Forms
description: В этой статье объясняется, как использовать класс Xamarin.Forms метки для отображения одного или нескольких строк текста в приложениях.
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/16/2018
ms.openlocfilehash: b5069381126db0859508480df5596ed5ec85686f
ms.sourcegitcommit: 4c0093ee5d4aeb16c0e6f0c740c4796736971651
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39203040"
---
# <a name="xamarinforms-label"></a>Метка Xamarin.Forms

_Отображаемый текст в Xamarin.Forms_

[ `Label` ](xref:Xamarin.Forms.Label) Представление используется для отображения текста, одной или нескольких строк. Метки могут иметь пользовательские шрифты (семейств, размеры и параметры) и цветным текстом.

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>Усечение и поддерживающего перенос

Метки можно задать для обработки текста, которые не помещаются на одной строке в одном из нескольких способов, предоставляемых `LineBreakMode` свойство. [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) представляет собой перечисление со следующими значениями:

- **HeadTruncation** &ndash; усекает заголовок текста, отображение окончания.
- **CharacterWrap** &ndash; переносит текст на новую строку на границе символ.
- **MiddleTruncation** &ndash; отображает начало и конец текста, с помощью средней replace многоточие.
- **NoWrap** &ndash; не переносит текст, только отображение текста, можете помещается в одну строку.
- **TailTruncation** &ndash; показано начало текста, усечение окончания.
- **WordWrap** &ndash; переносит текст на границе слова.

## <a name="fonts"></a>Шрифты

Дополнительные сведения о задании шрифты в `Label`, см. в разделе [шрифты](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="colors"></a>Цвета

`Label`использовать пользовательский цвет с помощью привязки можно задать s [ `TextColor` ](xref:Xamarin.Forms.Label.TextColor) свойство.

Особое внимание необходимо, чтобы гарантировать, что цвета будет доступна для использования на каждой платформе. Так как каждая платформа имеет различные значения по умолчанию для цветов текста и фона, необходимо соблюдать осторожность выбрать умолчания, которое работает на каждом.

Чтобы задать цвет текста метки, используйте следующий XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="TextSample.LabelPage"
             Title="Label Demo">
    <StackLayout Padding="5,10">
      <Label TextColor="#77d065" FontSize = "20" Text="This is a label." />
    </StackLayout>
</ContentPage>
```

Ниже приведен аналогичный код C#:

```csharp
public partial class LabelPage : ContentPage
{
    public LabelPage ()
    {
        InitializeComponent ();

        var layout = new StackLayout { Padding = new Thickness(5,10) };
        var label = new Label { Text="This is a label.", TextColor = Color.FromHex("#77d065"), FontSize = 20 };
        layout.Children.Add(label);
        this.Content = layout;
    }
}
```

На следующих снимках экрана показано результат действия параметра `TextColor` свойство:

![](label-images/textcolor.png "Пример TextColor")

Дополнительные сведения о цветах см. в разделе [цвета](~/xamarin-forms/user-interface/colors.md).

<a name="Formatted_Text" />

## <a name="formatted-text"></a>Форматированный текст

Предоставлять метки [ `FormattedText` ](xref:Xamarin.Forms.Label.FormattedText) свойство, которое позволяет представления текста с использованием нескольких шрифтов и цветов в одном представлении.

`FormattedText` Свойство имеет тип [ `FormattedString` ](xref:Xamarin.Forms.FormattedString), который состоит из одного или нескольких [ `Span` ](xref:Xamarin.Forms.Span) экземпляров, настроенных с помощью [ `Spans` ](xref:Xamarin.Forms.FormattedString.Spans) свойство . Каждый `Span` обладает следующими свойствами:

- [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) — цвет фона span.
- [`Font`](xref:Xamarin.Forms.Span.Font) — шрифт для текста в элементе управления span.
- [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes) — атрибуты шрифта для текста в элементе управления span.
- [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily) — Семейство шрифтов, к которой принадлежит шрифт для текста в элементе управления span.
- [`FontSize`](xref:Xamarin.Forms.Span.FontSize) — размер шрифта для текста в элементе управления span.
- [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor) — цвет для текста в элементе управления span. Это свойство является устаревшим и будет заменен `TextColor` свойство.
- [`Style`](xref:Xamarin.Forms.Span.Style) — стиль, применяемый к нему.
- [`Text`](xref:Xamarin.Forms.Span.Text) — текст диапазона.
- [`TextColor`](xref:Xamarin.Forms.Span.TextColor) — цвет для текста в элементе управления span.

Ниже приведен пример XAML `FormattedText` свойство, которое состоит из трех `Span` экземпляров:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="TextSample.LabelPage"
             Title="Label Demo - XAML">
    <StackLayout Padding="5,10">
        ...
        <Label>
            <Label.FormattedText>
                <FormattedString>
                    <Span Text="Red Bold, " TextColor="Red" FontAttributes="Bold" />
                    <Span Text="default, " Style="{DynamicResource BodyStyle}" />
                    <Span Text="italic small." FontAttributes="Italic" FontSize="Small" />
                </FormattedString>
            </Label.FormattedText>
        </Label>
    </StackLayout>
</ContentPage>
```

Ниже приведен аналогичный код C#:

```csharp
public class LabelPageCode : ContentPage
{
    public LabelPageCode ()
    {
        var layout = new StackLayout{ Padding = new Thickness (5, 10) };
        ...
        var formattedString = new FormattedString ();
        formattedString.Spans.Add (new Span{ Text = "Red bold, ", ForegroundColor = Color.Red, FontAttributes = FontAttributes.Bold });
        formattedString.Spans.Add (new Span { Text = "default, ", Style = Device.Styles.BodyStyle });
        formattedString.Spans.Add (new Span { Text = "italic small.", FontAttributes = FontAttributes.Italic, FontSize =  Device.GetNamedSize(NamedSize.Small, typeof(Label)) });

        layout.Children.Add (new Label { FormattedText = formattedString });
        this.Content = layout;
    }
}
```

> [!NOTE]
> [ `Text` ](xref:Xamarin.Forms.Span.Text) Свойство `Span` можно задать с помощью привязки данных. Дополнительные сведения см. в разделе [привязки данных](~/xamarin-forms/app-fundamentals/data-binding/index.md).

На следующих снимках экрана показано результат действия параметра `FormattedString` свойство до трех `Span` экземпляров:

![](label-images/formattedtext.png "Пример FormattedText")

## <a name="styling-a-label"></a>Задание стиля метки

Предыдущих разделах описаны параметр [ `Label` ](xref:Xamarin.Forms.Label) свойств для каждого экземпляра. Тем не менее наборы свойств могут быть сгруппированы в один стиль, который последовательно применяется для одного или нескольких представлений. Это может повысить читаемость кода и делать изменения дизайна проще в реализации. Дополнительные сведения см. в разделе [стили](~/xamarin-forms/user-interface/text/styles.md).

## <a name="related-links"></a>Связанные ссылки

- [Создание мобильных приложений с помощью Xamarin.Forms, Глава 3](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [Текст (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [Метка API](xref:Xamarin.Forms.Label)
