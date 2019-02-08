---
title: Метка Xamarin.Forms
description: В этой статье объясняется, как использовать класс Xamarin.Forms метки для отображения одного или нескольких строк текста в приложениях.
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2018
ms.openlocfilehash: ce1ba235a309e2388bd5eea7d70a1d72852fc615
ms.sourcegitcommit: 93c9fe61eb2cdfa530960b4253eb85161894c882
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55831863"
---
# <a name="xamarinforms-label"></a>Метка Xamarin.Forms

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)

_Отображаемый текст в Xamarin.Forms_

[ `Label` ](xref:Xamarin.Forms.Label) Представление используется для отображения текста, одной или нескольких строк. Метки можно иметь оформление текста, цветным текстом и использовать пользовательские шрифты (семейств, размеры и параметры).

## <a name="text-decorations"></a>Оформление текста

Оформление текста подчеркивания и зачеркивания могут применяться к [ `Label` ](xref:Xamarin.Forms.Label) экземпляров, задав `Label.TextDecorations` свойство к одному или нескольким `TextDecorations` членов перечисления:

- `None`
- `Underline`
- `Strikethrough`

В следующем примере XAML показано параметр `Label.TextDecorations` свойство:

```xaml
<Label Text="This is underlined text." TextDecorations="Underline"  />
<Label Text="This is text with strikethrough." TextDecorations="Strikethrough" />
<Label Text="This is underlined text with strikethrough." TextDecorations="Underline, Strikethrough" />
```

Ниже приведен аналогичный код C#:

```csharp
var underlineLabel = new Label { Text = "This is underlined text.", TextDecorations = TextDecorations.Underline };
var strikethroughLabel = new Label { Text = "This is text with strikethrough.", TextDecorations = TextDecorations.Strikethrough };
var bothLabel = new Label { Text = "This is underlined text with strikethrough.", TextDecorations = TextDecorations.Underline | TextDecorations.Strikethrough };
```

Ниже показаны снимки экрана `TextDecorations` членов перечисления, применяется к [ `Label` ](xref:Xamarin.Forms.Label) экземпляров:

![](label-images/label-textdecorations.png "Метки с оформление текста")

> [!NOTE]
> Оформление текста могут также применяться к [ `Span` ](xref:Xamarin.Forms.Span) экземпляров. Дополнительные сведения о `Span` , представлена в разделе [текст в формате](#Formatted_Text).

## <a name="colors"></a>Цвета

Метки, которые можно задать использование пользовательского текста цвета с помощью привязки [ `TextColor` ](xref:Xamarin.Forms.Label.TextColor) свойство.

Особое внимание необходимо, чтобы гарантировать, что цвета будет доступна для использования на каждой платформе. Так как каждая платформа имеет различные значения по умолчанию для цветов текста и фона, необходимо соблюдать осторожность выбрать умолчания, которое работает на каждом.

В следующем примере XAML задает цвет текста `Label`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="TextSample.LabelPage"
             Title="Label Demo">
    <StackLayout Padding="5,10">
      <Label TextColor="#77d065" FontSize = "20" Text="This is a green label." />
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
        var label = new Label { Text="This is a green label.", TextColor = Color.FromHex("#77d065"), FontSize = 20 };
        layout.Children.Add(label);
        this.Content = layout;
    }
}
```

На следующих снимках экрана показано результат действия параметра `TextColor` свойство:

![](label-images/textcolor.png "Пример TextColor")

Дополнительные сведения о цветах см. в разделе [цвета](~/xamarin-forms/user-interface/colors.md).

## <a name="fonts"></a>Шрифты

Дополнительные сведения о задании шрифты в `Label`, см. в разделе [шрифты](~/xamarin-forms/user-interface/text/fonts.md).

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>Усечение и поддерживающего перенос

Метки можно задать для обработки текста, которые не помещаются на одной строке в одном из нескольких способов, предоставляемых `LineBreakMode` свойство. [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) представляет собой перечисление со следующими значениями:

- **HeadTruncation** &ndash; усекает заголовок текста, отображение окончания.
- **CharacterWrap** &ndash; переносит текст на новую строку на границе символ.
- **MiddleTruncation** &ndash; отображает начало и конец текста, с помощью средней replace многоточие.
- **NoWrap** &ndash; не переносит текст, только отображение текста, можете помещается в одну строку.
- **TailTruncation** &ndash; показано начало текста, усечение окончания.
- **WordWrap** &ndash; переносит текст на границе слова.

## <a name="displaying-a-specific-number-of-lines"></a>Отображение определенное количество строк

Количество строк, отображаемых по [ `Label` ](xref:Xamarin.Forms.Label) можно указать, задав `Label.MaxLines` свойства `int` значение:

- Когда `MaxLines` равно 0, `Label` учитывает значение [ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode) свойство, чтобы показывать только одну строку, возможно, обрезанное, либо или всех строк с помощью весь текст.
- Когда `MaxLines` -1, результат идентичен параметр [ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode) свойства [ `NoWrap` ](xref:Xamarin.Forms.LineBreakMode), [ `HeadTruncation` ](xref:Xamarin.Forms.LineBreakMode), [ `MiddleTruncation` ](xref:Xamarin.Forms.LineBreakMode), или [ `TailTruncation` ](xref:Xamarin.Forms.LineBreakMode). Тем не менее `Label` будет учитывать значение [ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode) свойство по отношению к размещение многоточие, если применимо.
- При `MaxLines` больше 1, `Label` будет отображаться до указанного числа строк, соблюдая значение [ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode) свойство по отношению к размещение многоточие, если применимо. Однако можно присвоить `MaxLines` свойства со значением больше 1 не оказывает влияния [ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode) свойству [ `NoWrap` ](xref:Xamarin.Forms.LineBreakMode).

В следующем примере XAML показано параметр `MaxLines` свойство [ `Label` ](xref:Xamarin.Forms.Label):

```xaml
<Label Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus."
       LineBreakMode="WordWrap"
       MaxLines="2" />
```

Ниже приведен аналогичный код C#:

```csharp
var label =
{
  Text = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus.", LineBreakMode = LineBreakMode.WordWrap,
  MaxLines = 2
};
```

На следующих снимках экрана показано результат действия параметра `MaxLines` значение 2, когда текст достаточно длинным, чтобы занимать более чем на две строки:

![](label-images/label-maxlines.png "Пример MaxLines метки")

<a name="Formatted_Text" />

## <a name="formatted-text"></a>Форматированный текст

Предоставлять метки [ `FormattedText` ](xref:Xamarin.Forms.Label.FormattedText) свойство, которое позволяет представления текста с использованием нескольких шрифтов и цветов в одном представлении.

`FormattedText` Свойство имеет тип [ `FormattedString` ](xref:Xamarin.Forms.FormattedString), который состоит из одного или нескольких [ `Span` ](xref:Xamarin.Forms.Span) экземпляров, настроенных с помощью [ `Spans` ](xref:Xamarin.Forms.FormattedString.Spans) свойство . Следующие `Span` свойства можно использовать, чтобы задать внешний вид:

- [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) — цвет фона span.
- [`Font`](xref:Xamarin.Forms.Span.Font) — шрифт для текста в элементе управления span.
- [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes) — атрибуты шрифта для текста в элементе управления span.
- [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily) — Семейство шрифтов, к которой принадлежит шрифт для текста в элементе управления span.
- [`FontSize`](xref:Xamarin.Forms.Span.FontSize) — размер шрифта для текста в элементе управления span.
- [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor) — цвет для текста в элементе управления span. Это свойство является устаревшим и будет заменен `TextColor` свойство.
- [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight) — Множитель, который следует применить к высота строки по умолчанию диапазона. Дополнительные сведения см. в разделе [высота строки](#line-height).
- [`Style`](xref:Xamarin.Forms.Span.Style) — стиль, применяемый к нему.
- [`Text`](xref:Xamarin.Forms.Span.Text) — текст диапазона.
- [`TextColor`](xref:Xamarin.Forms.Span.TextColor) — цвет для текста в элементе управления span.
- `TextDecorations` -оформление для применения к тексту в элементе управления span. Дополнительные сведения см. в разделе [оформление текста](#text-decorations).

Кроме того [ `GestureRecognizers` ](xref:Xamarin.Forms.GestureElement.GestureRecognizers) свойство может использоваться для определения коллекции распознавателей жестов, которые будет отвечать на жесты на [ `Span` ](xref:Xamarin.Forms.Span).

Ниже приведен пример XAML `FormattedText` свойство, которое состоит из трех [ `Span` ](xref:Xamarin.Forms.Span) экземпляров:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="TextSample.LabelPage"
             Title="Label Demo - XAML">
    <StackLayout Padding="5,10">
        ...
        <Label LineBreakMode="WordWrap">
            <Label.FormattedText>
                <FormattedString>
                    <Span Text="Red Bold, " TextColor="Red" FontAttributes="Bold" />
                    <Span Text="default, " Style="{DynamicResource BodyStyle}">
                        <Span.GestureRecognizers>
                            <TapGestureRecognizer Command="{Binding TapCommand}" />
                        </Span.GestureRecognizers>
                    </Span>
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

        var span = new Span { Text = "default, " };
        span.GestureRecognizers.Add(new TapGestureRecognizer { Command = new Command(async () => await DisplayAlert("Tapped", "This is a tapped Span.", "OK")) });
        formattedString.Spans.Add(span);
        formattedString.Spans.Add (new Span { Text = "italic small.", FontAttributes = FontAttributes.Italic, FontSize =  Device.GetNamedSize(NamedSize.Small, typeof(Label)) });

        layout.Children.Add (new Label { FormattedText = formattedString });
        this.Content = layout;
    }
}
```

> [!IMPORTANT]
> [ `Text` ](xref:Xamarin.Forms.Span.Text) Свойство `Span` можно задать с помощью привязки данных. Дополнительные сведения см. в разделе [привязки данных](~/xamarin-forms/app-fundamentals/data-binding/index.md).

Обратите внимание, что [ `Span` ](xref:Xamarin.Forms.Span) также может отвечать на все жесты, которые добавляются в диапазон [ `GestureRecognizers` ](xref:Xamarin.Forms.GestureElement.GestureRecognizers) коллекции. Например [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) был добавлен второй `Span` в приведенных выше примерах кода. Таким образом, если это `Span` шифрованию `TapGestureRecognizer` ответит, выполнив `ICommand` определяется [ `Command` ](xref:Xamarin.Forms.TapGestureRecognizer.Command) свойство. Дополнительные сведения о распознавателей жестов, см. в разделе [Xamarin.Forms жесты](~/xamarin-forms/app-fundamentals/gestures/index.md).

На следующих снимках экрана показано результат действия параметра `FormattedString` свойство до трех `Span` экземпляров:

![](label-images/formattedtext.png "Пример FormattedText")

## <a name="line-height"></a>Высота линии

Высоты [ `Label` ](xref:Xamarin.Forms.Label) и [ `Span` ](xref:Xamarin.Forms.Span) можно настроить, задав [ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight) свойство или [ `Span.LineHeight` ](xref:Xamarin.Forms.Span.LineHeight) для `double` значение. В iOS и Android эти значения являются множители исходная высота строки, что и на универсальной платформе Windows (UWP) `Label.LineHeight` свойство имеет значение множителя от размера шрифта метки.

> [!NOTE]
> - В iOS [ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight) и [ `Span.LineHeight` ](xref:Xamarin.Forms.Span.LineHeight) свойства изменить высоту строки текста, которое может уместиться на одной строке и текст, который переносится на несколько строк.
> - В Android [ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight) и [ `Span.LineHeight` ](xref:Xamarin.Forms.Span.LineHeight) свойства только изменить высоту строки текста, который переносится на несколько строк.
> - На UWP [ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight) свойство изменяет высоту строки текста, который переносится на несколько строк, и [ `Span.LineHeight` ](xref:Xamarin.Forms.Span.LineHeight) не оказывает никакого влияния.

В следующем примере XAML показано параметр [ `LineHeight` ](xref:Xamarin.Forms.Label.LineHeight) свойство [ `Label` ](xref:Xamarin.Forms.Label):

```xaml
<Label Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus."
       LineBreakMode="WordWrap"
       LineHeight="1.8" />
```

Ниже приведен аналогичный код C#:

```csharp
var label =
{
  Text = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus.", LineBreakMode = LineBreakMode.WordWrap,
  LineHeight = 1.8
};
```

На следующих снимках экрана показано результат действия параметра [ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight) свойство до версии 1.8:

![](label-images/label-lineheight.png "Пример LineHeight метки")

В следующем примере XAML показано параметр [ `LineHeight` ](xref:Xamarin.Forms.Span.LineHeight) свойство [ `Span` ](xref:Xamarin.Forms.Span):

```xaml
<Label LineBreakMode="WordWrap">
    <Label.FormattedText>
        <FormattedString>
            <Span Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit. In a tincidunt sem. Phasellus mollis sit amet turpis in rutrum. Sed aliquam ac urna id scelerisque. "
                  LineHeight="1.8"/>
            <Span Text="Nullam feugiat sodales elit, et maximus nibh vulputate id."
                  LineHeight="1.8" />
        </FormattedString>
    </Label.FormattedText>
</Label>
```

Ниже приведен аналогичный код C#:

```csharp
var formattedString = new FormattedString();
formattedString.Spans.Add(new Span
{
  Text = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. In a tincidunt sem. Phasellus mollis sit amet turpis in rutrum. Sed aliquam ac urna id scelerisque. ",
  LineHeight = 1.8
});
formattedString.Spans.Add(new Span
{
  Text = "Nullam feugiat sodales elit, et maximus nibh vulputate id.",
  LineHeight = 1.8
});
var label = new Label
{
  FormattedText = formattedString,
  LineBreakMode = LineBreakMode.WordWrap
};
```

На следующих снимках экрана показано результат действия параметра [ `Span.LineHeight` ](xref:Xamarin.Forms.Span.LineHeight) свойство до версии 1.8:

![](label-images/span-lineheight.png "Пример span LineHeight")

## <a name="hyperlinks"></a>Гиперссылки

Текст, отображаемый элементом [ `Label` ](xref:Xamarin.Forms.Label) и [ `Span` ](xref:Xamarin.Forms.Span) экземпляров можно превратить в гиперссылки с следующий подход:

1. Задайте `TextColor` и `TextDecoration` свойства [ `Label` ](xref:Xamarin.Forms.Label) или [ `Span` ](xref:Xamarin.Forms.Span).
1. Добавить [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) для [ `GestureRecognizers` ](xref:Xamarin.Forms.GestureElement.GestureRecognizers) коллекцию [ `Label` ](xref:Xamarin.Forms.Label) или [ `Span` ](xref:Xamarin.Forms.Span), чей [ `Command` ](xref:Xamarin.Forms.TapGestureRecognizer.Command) свойство привязывается к `ICommand`, а [ `CommandParameter` ](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) свойство содержит URL-адрес, чтобы открыть.
1. Определение `ICommand` , будет выполняться [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer).
1. Написать код, который будет выполняться `ICommand`.

В следующем примере кода, взятом из [демонстрации Hyperlink](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/HyperlinkDemos) образце показано [ `Label` ](xref:Xamarin.Forms.Label) , содержимое которого имеет значение из нескольких [ `Span` ](xref:Xamarin.Forms.Span) экземпляров:

```xaml
<Label>
    <Label.FormattedText>
        <FormattedString>
            <Span Text="Alternatively, click " />
            <Span Text="here"
                  TextColor="Blue"
                  TextDecorations="Underline">
                <Span.GestureRecognizers>
                    <TapGestureRecognizer Command="{Binding TapCommand}"
                                          CommandParameter="https://docs.microsoft.com/xamarin/" />
                </Span.GestureRecognizers>
            </Span>
            <Span Text=" to view Xamarin documentation." />
        </FormattedString>
    </Label.FormattedText>
</Label>
```

В этом примере, первый и третий [ `Span` ](xref:Xamarin.Forms.Span) одинакового текста, а второй `Span` представляет tappable гиперссылку. Он имеет цвет изменится на синий, а подчеркивание текста. Это создает гиперссылки, как показано на следующем снимке экрана:

[![Гиперссылки](label-images/hyperlinks-small.png "гиперссылки")](label-images/hyperlinks-large.png#lightbox)

При выборе элемента гиперссылки, [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) ответит, выполнив `ICommand` определяется его [ `Command` ](xref:Xamarin.Forms.TapGestureRecognizer.Command) свойство. Кроме того, URL-адрес, определяемое [ `CommandParameter` ](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) свойства будут передаваться `ICommand` как параметр.

Содержит код программной части для страницы XAML `TapCommand` реализации:

```csharp
public partial class MainPage : ContentPage
{
    public ICommand TapCommand => new Command<string>(OpenBrowser);

    public MainPage()
    {
        InitializeComponent();
        BindingContext = this;
    }

    void OpenBrowser(string url)
    {
        Device.OpenUri(new Uri(url));
    }
}
```

`TapCommand` Выполняет `OpenBrowser` , передавая [ `TapGestureRecognizer.CommandParameter` ](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) значение свойства в качестве параметра. В свою очередь, вызывает этот метод [ `Device.OpenUri` ](xref:Xamarin.Forms.Device.OpenUri*) метод, чтобы открыть URL-адрес в веб-браузере. Таким образом общий эффект, что при гиперссылки шифрованию на странице, откроется браузер и осуществляется переход URL-адрес, связанный с гиперссылкой.

### <a name="creating-a-reusable-hyperlink-class"></a>Создание многократно используемых hyperlink класса

Предыдущий подход к созданию гиперссылки требуется написание повторяющегося кода, каждый раз, чтобы требовать гиперссылки в приложении. Тем не менее, обе [ `Label` ](xref:Xamarin.Forms.Label) и [ `Span` ](xref:Xamarin.Forms.Span) классы могут быть подкласса для создания `HyperlinkLabel` и `HyperlinkSpan` классов, в которой распознаватель жестов и форматированием текста код также добавлена туда.

В следующем примере кода, взятом из [демонстрации Hyperlink](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/HyperlinkDemos) образце показано `HyperlinkSpan` класса:

```csharp
public class HyperlinkSpan : Span
{
    public static readonly BindableProperty UrlProperty =
        BindableProperty.Create(nameof(Url), typeof(string), typeof(HyperlinkSpan), null);

    public string Url
    {
        get { return (string)GetValue(UrlProperty); }
        set { SetValue(UrlProperty, value); }
    }

    public HyperlinkSpan()
    {
        TextDecorations = TextDecorations.Underline;
        TextColor = Color.Blue;
        GestureRecognizers.Add(new TapGestureRecognizer
        {
            Command = new Command(() => Device.OpenUri(new Uri(Url)))
        });
    }
}
```

`HyperlinkSpan` Класс определяет `Url` свойство и связанные с ней [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty), и конструктор задает внешний вид гиперссылок и [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) , будут отвечать При выборе элемента гиперссылки. При `HyperlinkSpan` шифрованию, `TapGestureRecognizer` ответит, выполнив [ `Device.OpenUri` ](xref:Xamarin.Forms.Device.OpenUri*) метод, чтобы открыть URL-адрес, определяемое `Url` свойство, в веб-браузере.

`HyperlinkSpan` Класса может быть поглощен добавления экземпляра класса в XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:HyperlinkDemo"
             x:Class="HyperlinkDemo.MainPage">
    <StackLayout>
        ...
        <Label>
            <Label.FormattedText>
                <FormattedString>
                    <Span Text="Alternatively, click " />
                    <local:HyperlinkSpan Text="here"
                                         Url="https://docs.microsoft.com/appcenter/" />
                    <Span Text=" to view AppCenter documentation." />
                </FormattedString>
            </Label.FormattedText>
        </Label>
    </StackLayout>
</ContentPage>
```

## <a name="styling-labels"></a>Стиль метки

Предыдущих разделах описаны параметр [ `Label` ](xref:Xamarin.Forms.Label) и [ `Span` ](xref:Xamarin.Forms.Span) свойств для каждого экземпляра. Тем не менее наборы свойств могут быть сгруппированы в один стиль, который последовательно применяется для одного или нескольких представлений. Это может повысить читаемость кода и делать изменения дизайна проще в реализации. Дополнительные сведения см. в разделе [стили](~/xamarin-forms/user-interface/text/styles.md).

## <a name="related-links"></a>Связанные ссылки

- [Текст (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [Гиперссылки (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Hyperlinks)
- [Создание мобильных приложений с помощью Xamarin.Forms, Глава 3](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [Метка API](xref:Xamarin.Forms.Label)
- [Диапазон API](xref:Xamarin.Forms.Span)
