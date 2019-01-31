---
title: Стили устройства в Xamarin.Forms
description: Платформа Xamarin.Forms включает шесть динамические стили, известный как стили устройства, в классе Device.Styles. В этой статье объясняется, как использовать стили устройства в приложении Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 7FF19ED1-0822-4238-9435-AD970317A2F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: fd5181040c1805d3fdabdae4803bbe32c6bb6652
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55292237"
---
# <a name="device-styles-in-xamarinforms"></a>Стили устройства в Xamarin.Forms

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/DynamicStyles/)

_Платформа Xamarin.Forms включает шесть динамические стили, известный как стили устройства, в классе Device.Styles._

*Устройства* стили:

- [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle)
- [`CaptionStyle`](xref:Xamarin.Forms.Device.Styles.CaptionStyle)
- [`ListItemDetailTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyle)
- [`ListItemTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyle)
- [`SubtitleStyle`](xref:Xamarin.Forms.Device.Styles.SubtitleStyle)
- [`TitleStyle`](xref:Xamarin.Forms.Device.Styles.TitleStyle)

Все шесть стили могут применяться только к [ `Label` ](xref:Xamarin.Forms.Label) экземпляров. Например `Label` , отображает текст абзаца может установить его [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) свойства [ `BodyStyle` ](xref:Xamarin.Forms.Device.Styles.BodyStyle).

В следующем примере кода показано использование *устройства* стили на странице XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DeviceStylesPage" Title="Device" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="myBodyStyle" TargetType="Label"
              BaseResourceKey="BodyStyle">
                <Setter Property="TextColor" Value="Accent" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="Title style"
              Style="{DynamicResource TitleStyle}" />
            <Label Text="Subtitle text style"
              Style="{DynamicResource SubtitleStyle}" />
            <Label Text="Body style"
              Style="{DynamicResource BodyStyle}" />
            <Label Text="Caption style"
              Style="{DynamicResource CaptionStyle}" />
            <Label Text="List item detail text style"
              Style="{DynamicResource ListItemDetailTextStyle}" />
            <Label Text="List item text style"
              Style="{DynamicResource ListItemTextStyle}" />
            <Label Text="No style" />
            <Label Text="My body style"
              Style="{StaticResource myBodyStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Стили устройства ограничивается использованием `DynamicResource` расширение разметки. Динамическая природа стили можно увидеть в iOS, изменив **специальных возможностей** параметры для размера текста. Внешний вид *устройства* стили отличается на каждой платформе, как показано на следующем снимке экрана:

![](device-images/device-styles.png "Стили устройства на каждой платформе")

*Устройство* стили также могут быть производными от, задав [ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey) присваивается имя ключа для стиля устройства. В примере выше `myBodyStyle` наследует от [ `BodyStyle` ](xref:Xamarin.Forms.Device.Styles.BodyStyle) и задает цвет текста с диакритическими знаками. Дополнительные сведения о наследовании динамического стиля см. в разделе [динамического наследования стиля](~/xamarin-forms/user-interface/styles/xaml/dynamic.md#dynamic-style-inheritance).

В следующем примере кода показаны эквивалентные страницы на языке C#:

```csharp
public class DeviceStylesPageCS : ContentPage
{
    public DeviceStylesPageCS ()
    {
        var myBodyStyle = new Style (typeof(Label)) {
            BaseResourceKey = Device.Styles.BodyStyleKey,
            Setters = {
                new Setter {
                    Property = Label.TextColorProperty,
                    Value = Color.Accent
                }
            }
        };

        Title = "Device";
        Icon = "csharp.png";
        Padding = new Thickness (0, 20, 0, 0);

        Content = new StackLayout {
            Children = {
                new Label { Text = "Title style", Style = Device.Styles.TitleStyle },
                new Label { Text = "Subtitle style", Style = Device.Styles.SubtitleStyle },
                new Label { Text = "Body style", Style = Device.Styles.BodyStyle },
                new Label { Text = "Caption style", Style = Device.Styles.CaptionStyle },
                new Label { Text = "List item detail text style",
                  Style = Device.Styles.ListItemDetailTextStyle },
                new Label { Text = "List item text style", Style = Device.Styles.ListItemTextStyle },
                new Label { Text = "No style" },
                new Label { Text = "My body style", Style = myBodyStyle }
            }
        };
    }
}
```

[ `Style` ](xref:Xamarin.Forms.VisualElement.Style) Свойства каждого [ `Label` ](xref:Xamarin.Forms.Label) экземпляра имеет значение соответствующего свойства из [ `Devices.Styles` ](xref:Xamarin.Forms.Device.Styles) класса.

## <a name="accessibility"></a>Специальные возможности

*Устройства* стили учитывают настройки специальных возможностей, поэтому размеры шрифтов, изменяют, настройки специальных возможностей, изменяются на каждой платформе. Таким образом, чтобы обеспечить поддержку текста специальных возможностей, убедитесь, что *устройства* стилей используются в качестве основы для любой стили текста в приложении.

Далее на снимках экрана демонстрируют стили устройства на каждой платформе, с наименьшим размером доступного шрифта:

[![](device-images/minimum-size.png "Стили доступным небольшое устройство на каждой платформе")](device-images/minimum-size-large.png#lightbox "стили доступным небольшое устройство на каждой платформе")

Далее на снимках экрана показано стили устройства на каждой платформе, с помощью максимальный размер доступного шрифта:

![](device-images/maximum-size.png "Стили доступны большое устройство на каждой платформе")

## <a name="related-links"></a>Связанные ссылки

- [Стили текста](~/xamarin-forms/user-interface/text/styles.md)
- [Расширения разметки XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Динамические стили (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/DynamicStyles/)
- [Работа с использованием стилей (пример)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [Device.Styles](xref:Xamarin.Forms.Device.Styles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Стиль](xref:Xamarin.Forms.Style)
- [Метод задания](xref:Xamarin.Forms.Setter)
