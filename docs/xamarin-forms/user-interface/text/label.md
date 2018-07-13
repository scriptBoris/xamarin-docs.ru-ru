---
title: Метка Xamarin.Forms
description: В этой статье объясняется, как использовать класс Xamarin.Forms метки для отображения одного или нескольких строк текста в приложениях.
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: ce602a84ea1024dc22298a3ec1567a9a34ad4a82
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995970"
---
# <a name="xamarinforms-label"></a>Метка Xamarin.Forms

_Отображаемый текст в Xamarin.Forms_

`Label` Представление используется для отображения текста, одной или нескольких строк. Метки могут иметь пользовательские шрифты (семейств, размеры и параметры) и цветным текстом. В этой статье рассматриваются следующие темы:

- **[Усечение и поддерживающего перенос](#Truncation_and_Wrapping)**  &ndash; усечение и параметры для обработки ситуации, когда текст не помещается на одной строке переноса.
- **[Шрифт](#Font)**  &ndash; параметры шрифта.
- **[Цвет](#Color)**  &ndash; текст метки параметры цвета.
- **[Форматированный текст](#Formatted_Text)**  &ndash; варианты отображения текста с помощью нескольких встроенных форматов и стили.

## <a name="styling-label"></a>Метка стиля

В следующих разделах описаны свойства параметра `Label` вручную на основе каждого экземпляра. Обратите внимание, что наборов свойств могут быть сгруппированы в один стиль, который последовательно применяется для одного или нескольких представлений. Это может повысить читаемость кода и делать изменения дизайна проще в реализации. См. в разделе [стили](~/xamarin-forms/user-interface/text/styles.md) Дополнительные сведения.

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>Усечение и поддерживающего перенос

Метки можно задать для обработки текста, которые не помещаются на одной строке в одном из нескольких способов, предоставляемых `LineBreakMode` свойство. [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) Это перечисление из следующих вариантов:

- **HeadTruncation** &ndash; усекает заголовок текста, отображение окончания.
- **CharacterWrap** &ndash; переносит текст на новую строку на границе символ.
- **MiddleTruncation** &ndash; отображает начало и конец текста, с помощью средней replace многоточие.
- **NoWrap** &ndash; не переносит текст, только отображение текста, можете помещается в одну строку.
- **TailTruncation** &ndash; показано начало текста, усечение окончания.
- **WordWrap** &ndash; переносит текст на границе слова.

## <a name="font"></a>Шрифт

См. в разделе [работа со шрифтами](~/xamarin-forms/user-interface/text/fonts.md) Дополнительные сведения.

## <a name="color"></a>Цвет

`Label`использовать пользовательский цвет с помощью привязки можно задать s `TextColor` свойство.

Особое внимание необходимо, чтобы гарантировать, что цвета будет доступна для использования на каждой платформе. Так как каждая платформа имеет различные значения по умолчанию для цветов текста и фона, необходимо соблюдать осторожность выбрать умолчания, которое работает на каждом.

Чтобы задать цвет текста метки, используйте следующий код:

В коде:

```csharp
public partial class LabelPage : ContentPage
{
    public LabelPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        var label = new Label { Text="This is a label.", TextColor = Color.FromHex("#77d065"), FontSize = 20 };
        layout.Children.Add(label);
    }
}
```

В XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.LabelPage"
Title="Label Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
      <Label TextColor="#77d065" FontSize = "20" Text="This is a label." />
    </StackLayout>
  </ContentPage.Content>
</ContentPage>
```

![](label-images/textcolor.png "Пример TextColor")

<a name="Formatted_Text" />

## <a name="formatted-text"></a>Форматированный текст

Предоставлять метки `FormattedText` свойство, которое позволяет отображать текст с использованием нескольких шрифтов и цветов в одном представлении.

`FormattedText` Свойство имеет тип [ `FormattedString` ](xref:Xamarin.Forms.FormattedString). Форматированные строки состоят из одного или нескольких `Span`s, каждый из них со следующими свойствами:

- **BackgroundColor** &ndash; может использоваться для задания фонового цвета, например для создания эффекта выделения.
- **FontAttributes** &ndash; может быть набор полужирный, курсив или ни одного.
- **FontFamily** &ndash; задает шрифт для использования.
- **FontSize** &ndash; задает размер текста.
- **ForegroundColor** &ndash; задает цвет текста.
- **Текст** &ndash; текст представления.

Следующий код C# демонстрирует метки, где первое слово отображается полужирным шрифтом, и последнее слово отображается красным цветом:

```csharp
public partial class LabelPage : ContentPage
{
    public LabelPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
    var label = new Label { FontSize = 20 };
    var s = new FormattedString ();
    s.Spans.Add (new Span{ Text = "Red Bold", FontAttributes = FontAttributes.Bold });
    s.Spans.Add (new Span{ Text = "Default" });
    s.Spans.Add (new Span{ Text = "italic small", FontSize =  Device.GetNamedSize(NamedSize.Small, typeof(Label)), FontAttributes = FontAttributes.Italic});
    label.FormattedText = s;
        layout.Children.Add(label);
    }
}
```

В XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.LabelPage"
Title="Label Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
      <Label FontSize=20>
        <Label.FormattedText>
          <FormattedString>
            <Span Text="Red Bold" ForegroundColor="Red" FontAttributes="Bold" />
            <Span Text="Default" />
            <Span Text="italic small" FontAttributes="Italic" FontSize="Small" />
          </FormattedString>
        </Label.FormattedText>
      </Label>
    </StackLayout>
  </ContentPage.Content>
</ContentPage>
```

![](label-images/formattedtext.png "Пример FormattedText")


## <a name="related-links"></a>Связанные ссылки

- [Создание мобильных приложений с помощью Xamarin.Forms, Глава 3](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [Текст (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [Метка API](xref:Xamarin.Forms.Label)
