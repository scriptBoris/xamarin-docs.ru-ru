---
title: Стили текста Xamarin.Forms
description: В этой статье объясняется, как выделение текста в приложениях Xamarin.Forms. Стили могут быть определен один раз и использовать несколько представлений, но стиль может использоваться только с представлениями одного типа.
ms.prod: xamarin
ms.assetid: 57C0CFD6-A568-46B8-ADA1-BF25681893CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: f70ab3faf6984720e395f5a41b4cabb63fe03053
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53056935"
---
# <a name="xamarinforms-text-styles"></a>Стили текста Xamarin.Forms

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)

_Выделение текста в Xamarin.Forms_

Стили позволяют настроить внешний вид меток, записи и редакторов. Стили могут быть определен один раз и использовать несколько представлений, но стиль может использоваться только с представлениями одного типа.
Стили можно предоставить `Key` и применить выборочное использование с конкретным элементом управления `Style` свойство.

<a name="Built-In_Styles" />

## <a name="built-in-styles"></a>Встроенные стили

Платформа Xamarin.Forms включает несколько [встроенные](xref:Xamarin.Forms.Device.Styles) стили для распространенных сценариев:

- `BodyStyle`
- `CaptionStyle`
- `ListItemDetailTextStyle`
- `ListItemTextStyle`
- `SubtitleStyle`
- `TitleStyle`

Для применения одного из встроенных стилей используйте `DynamicResource` расширения разметки для задания стиля:

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

В C#, встроенные стили выбираются из `Device.Styles`:

```csharp
label.Style = Device.Styles.TitleStyle;
```

![](styles-images/builtinstyles.png "Пример стили устройства")

<a name="Custom_Styles" />

## <a name="custom-styles"></a>Пользовательские стили

Стили состоят из методов задания и методов задания состоят из свойств и значений свойств будет указано значение.

В C# пользовательского стиля для метки с нашего красного текста размера 30 будет определяться следующим образом:

```csharp
var LabelStyle = new Style (typeof(Label)) {
    Setters = {
        new Setter {Property = Label.TextColorProperty, Value = Color.Red},
        new Setter {Property = Label.FontSizeProperty, Value = 30}
    }
};

var label = new Label { Text = "Check out my style.", Style = LabelStyle };
```

В XAML:

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <Style x:Key="LabelStyle" TargetType="Label">
            <Setter Property="TextColor" Value="Red"/>
            <Setter Property="FontSize" Value="30"/>
        </Style>
    </ResourceDictionary>
</ContentPage.Resources>

<ContentPage.Content>
    <StackLayout>
        <Label Text="Check out my style." Style="{StaticResource LabelStyle}" />
    </StackLayout>
</ContentPage.Content>
```

Обратите внимание, что ресурсы (включая все стили) определяются в `ContentPage.Resources`, который является одноуровневым элементом знакомую `ContentPage.Content` элемент.

![](styles-images/customstyle.png "Пример пользовательских стилей")

<a name="Applying_Styles" />

## <a name="applying-styles"></a>Применение стилей

После создания стиля, он может применяться к любой соответствующей представление его `TargetType`.

В XAML, пользовательские стили применяются к представлениям, указав их `Style` свойство с `StaticResource` расширение разметки, нужный стиль ссылки:

```xaml
<Label Text="Check out my style." Style="{StaticResource LabelStyle}" />
```

В C#, стили можно либо применяться непосредственно к представлению или добавить и получать на странице `ResourceDictionary`. Чтобы добавить напрямую:

```csharp
var label = new Label { Text = "Check out my style.", Style = LabelStyle };
```

Чтобы добавить и получить на странице `ResourceDictionary`:

```csharp
this.Resources.Add ("LabelStyle", LabelStyle);
label.Style = (Style)Resources["LabelStyle"];
```

Встроенные стили применяются по-разному, так как они должны отвечать на параметры специальных возможностей. Для применения встроенных стилей в XAML, `DynamicResource` используется расширение разметки:

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

В C#, встроенные стили выбираются из `Device.Styles`:

```csharp
label.Style = Device.Styles.TitleStyle;
```

## <a name="accessibility"></a>Специальные возможности

Встроенные стили, которые облегчают учитывают настройки специальных возможностей. При использовании любой из встроенных стилей, размеры шрифтов автоматически увеличивается, если пользователь устанавливает их настройки специальных возможностей, соответствующим образом.

Рассмотрим следующий пример той же страницы представлений, для которого установлен с помощью встроенных стилей Проговорить включенных и отключенных:

Отключено:

![](styles-images/pre-access.png "Стили устройства с отключенной специальных возможностей")

Включено:

![](styles-images/post-access.png "Стили устройства с поддержкой специальных возможностей")

Для обеспечения доступности, убедитесь, что встроенные стили основой для используются все стили, относящихся к тексту в приложении, и что вы используете стили согласованно. См. в разделе [стили](~/xamarin-forms/user-interface/styles/index.md) Дополнительные сведения о расширении и работы со стилями, в целом.


## <a name="related-links"></a>Связанные ссылки

- [Создание мобильных приложений с помощью Xamarin.Forms, Глава 12](https://developer.xamarin.com/r/xamarin-forms/book/chapter12.pdf)
- [Стили](~/xamarin-forms/user-interface/styles/index.md)
- [Текст (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [Стиль](xref:Xamarin.Forms.Style)
