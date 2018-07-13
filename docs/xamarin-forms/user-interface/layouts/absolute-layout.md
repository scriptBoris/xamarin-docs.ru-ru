---
title: Xamarin.Forms AbsoluteLayout
description: В этой статье объясняется, как использовать класс Xamarin.Forms AbsoluteLayout для создания пользовательских интерфейсов идеальной. Этот класс размещает и устанавливает размер дочерних элементов пропорционально свой собственный размер и положение или абсолютные значения.
ms.prod: xamarin
ms.assetid: 01A5CCE0-AD45-4806-84FD-72C007005B38
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/25/2015
ms.openlocfilehash: 0d49b8c50db08ad07952425492591ee246de4f8b
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998352"
---
# <a name="xamarinforms-absolutelayout"></a>Xamarin.Forms AbsoluteLayout

[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) размещает и устанавливает размер дочерних элементов пропорционально свой собственный размер и положение или абсолютные значения. Дочерние представления может быть расположено и размера, с помощью значений пропорциональные или статические значения, а пропорционально и могут использоваться статические значения.

[![](absolute-layout-images/layouts-sml.png "Макеты Xamarin.Forms")](absolute-layout-images/layouts.png#lightbox "макеты Xamarin.Forms")

В этой статье рассматривается:

- **[Назначение](#Purpose)**  &ndash; распространенные варианты применения `AbsoluteLayout`.
- **[Использование](#Usage)**  &ndash; способы использования `AbsoluteLayout` для достижения нужного проекта.
  - **[Пропорциональное макеты](#Proportional_Layouts)**  &ndash; понять как пропорциональное значения работают в `AbsoluteLayout`.
  - **[Указание значений](#Specifying_Values)**  &ndash; понять, как задаются пропорциональным и абсолютные значения.
  - **[Значений пропорциональные](#Proportional_Values)**  &ndash; понять как пропорциональное значения работать.
    - **[Абсолютные значения](#Absolute_Values)**  &ndash; основные сведения о работе абсолютные значения.

<a name="Purpose" />

## <a name="purpose"></a>Цель

Из-за размещения модель `AbsoluteLayout`, макет позволяет сравнительно легко размещать элементы, которые записи на диск с любой стороны части макета или по центру. С пропорциональным размеров и позиций, элементы в `AbsoluteLayout` можно автоматически масштабировать до любого размера представления. Для элементов, где должны масштабироваться только положение, но не с размером могут содержаться значения абсолютными и пропорционально.

`AbsoluteLayout` может применяться везде, где элементы должны располагаться в представлении и особенно полезна при выравнивание элементов по краям.

<a name="Usage" />

## <a name="usage"></a>Использование

<a name="Proportional_Layouts" />

### <a name="proportional-layouts"></a>Пропорциональное макеты

`AbsoluteLayout` имеет уникальной привязки модели, при котором привязка элемента по отношению к его элемент как элемент располагается относительно макета при использовании пропорционально позиционирования. Если используется абсолютное позиционирование, привязка является с координатами (0,0) в представлении. Это имеет два важных последствий:

- Элементы не может располагаться вне экрана, с помощью значений пропорциональные.
- Элементы могут располагаться надежно по любой части макета или в центре, независимо от размера макета или устройства.

`AbsoluteLayout`, такие как `RelativeLayout`, является возможность размещения элементов с перекрытием.

Обратите внимание на следующем снимке экрана, а привязка поля — белый точка. При переходе его через макет Обратите внимание, что связь между точкой привязки и поле:

![](absolute-layout-images/anchor-start.png "Привязки в начале")
![](absolute-layout-images/anchor-center.png "привязки в центре")
![](absolute-layout-images/anchor-end.png "привязки на конце")

<a name="Specifying_Values" />

### <a name="specifying-values"></a>Указание значений

Представления в `AbsoluteLayout` устанавливаются с помощью четырех значений:

- **X** &ndash; (по горизонтали) положение представления привязки по оси x
- **Y** &ndash; положение представления привязки по оси y (вертикально)
- **Ширина** &ndash; ширины представления
- **Высота** &ndash; высоту представления

Каждое из этих значений можно задать в качестве [пропорционально](#Proportional_Values) значение или [абсолютный](#Absolute_Values) значение.

Значения указываются в виде сочетания границы и флаг. `LayoutBounds` — [ `Rectangle` ](xref:Xamarin.Forms.Rectangle) состоящий из четырех значений: `x`, `y`, `width`, `height`.

### <a name="absolutelayoutflags"></a>AbsoluteLayoutFlags

[`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags) Указывает, каким образом будут интерпретироваться значения и имеет следующие предопределенные параметры:

- **Нет** &ndash; интерпретирует все значения как абсолютные. Это значение по умолчанию, если указаны флаги не макета.
- **Все** &ndash; интерпретирует все значения как пропорционально.
- **WidthProportional** &ndash; интерпретирует `Width` значение как пропорциональным и все остальные значения как абсолютные.
- **HeightProportional** &ndash; интерпретирует только высота значение как пропорциональное со всеми другими значениями абсолютный.
- **XProportional** &ndash; интерпретирует `X` при обработке все остальные значения как абсолютное значение пропорционально,.
- **YProportional** &ndash; интерпретирует `Y` при обработке все остальные значения как абсолютное значение пропорционально,.
- **PositionProportional** &ndash; интерпретирует `X` и `Y` значений пропорциональные, пока размер значения интерпретируются как абсолютные.
- **SizeProportional** &ndash; интерпретирует `Width` и `Height` значений пропорциональные при абсолютное положение значения.

В XAML, границы и флаги устанавливаются как часть определения представлений в макете, с помощью `AbsoluteLayout.LayoutBounds` свойство. Границы задаются как разделенный запятыми список значений, `X`, `Y`, `Width`, и `Height`в этом порядке. Объявление представления в макет с помощью также указаны флаги `AbsoluteLayout.LayoutFlags` свойство. Обратите внимание на то, что флаги можно объединить в XAML, используя список с разделителями запятыми. Рассмотрим следующий пример.

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="LayoutSamples.AbsoluteLayoutExploration"
Title="Absolute Layout Exploration">
    <ContentPage.Content>
        <AbsoluteLayout>
            <Label Text="I'm centered on iPhone 4 but no other device"
                AbsoluteLayout.LayoutBounds="115,150,100,100" LineBreakMode="WordWrap"  />
            <Label Text="I'm bottom center on every device."
                AbsoluteLayout.LayoutBounds=".5,1,.5,.1" AbsoluteLayout.LayoutFlags="All"
                LineBreakMode="WordWrap"  />
            <BoxView Color="Olive"  AbsoluteLayout.LayoutBounds="1,.5, 25, 100"
                AbsoluteLayout.LayoutFlags="PositionProportional" />
            <BoxView Color="Red" AbsoluteLayout.LayoutBounds="0,.5,25,100"
                AbsoluteLayout.LayoutFlags="PositionProportional" />
            <BoxView Color="Blue" AbsoluteLayout.LayoutBounds=".5,0,100,25"
                AbsoluteLayout.LayoutFlags="PositionProportional" />
        </AbsoluteLayout>
    </ContentPage.Content>
</ContentPage>
```

![](absolute-layout-images/exploration.png "Примеры AbsoluteLayout")

Обратите внимание на следующее условия:

- Метка в центре располагается, используя абсолютный размер и положение значения. Поэтому он отображается по центру на iPhone 4S и ниже, но не центрировано на больших устройствах.
- Разместится текст в нижней части макета с помощью пропорционально размер и положение значения. Они всегда буду отображаться в центре нижней части макета, но его размер будет увеличиваться с большие размеры макета.
- Три цветных `BoxView`s располагаются верхняя, левая и правая границы экрана с использованием пропорционально положение и размер абсолютный.

Следующие достигается тот же макет, в C#:

```csharp
public class AbsoluteLayoutExplorationCode : ContentPage
{
    public AbsoluteLayoutExplorationCode ()
    {
        Title = "Absolute Layout Exploration - Code";
        var layout = new AbsoluteLayout();

        var centerLabel = new Label {
        Text = "I'm centered on iPhone 4 but no other device.",
        LineBreakMode = LineBreakMode.WordWrap};

        AbsoluteLayout.SetLayoutBounds (centerLabel, new Rectangle (115, 159, 100, 100));
        // No need to set layout flags, absolute positioning is the default

        var bottomLabel = new Label { Text = "I'm bottom center on every device.", LineBreakMode = LineBreakMode.WordWrap };
        AbsoluteLayout.SetLayoutBounds (bottomLabel, new Rectangle (.5, 1, .5, .1));
        AbsoluteLayout.SetLayoutFlags (bottomLabel, AbsoluteLayoutFlags.All);

        var rightBox = new BoxView{ Color = Color.Olive };
        AbsoluteLayout.SetLayoutBounds (rightBox, new Rectangle (1, .5, 25, 100));
        AbsoluteLayout.SetLayoutFlags (rightBox, AbsoluteLayoutFlags.PositionProportional);

        var leftBox = new BoxView{ Color = Color.Red };
        AbsoluteLayout.SetLayoutBounds (leftBox, new Rectangle (0, .5, 25, 100));
        AbsoluteLayout.SetLayoutFlags (leftBox, AbsoluteLayoutFlags.PositionProportional);

        var topBox = new BoxView{ Color = Color.Blue };
        AbsoluteLayout.SetLayoutBounds (topBox, new Rectangle (.5, 0, 100, 25));
        AbsoluteLayout.SetLayoutFlags (topBox, AbsoluteLayoutFlags.PositionProportional);

        layout.Children.Add (bottomLabel);
        layout.Children.Add (centerLabel);
        layout.Children.Add (rightBox);
        layout.Children.Add (leftBox);
        layout.Children.Add (topBox);

        Content = layout;
    }
}
```
<a name="Proportional_Values" />

### <a name="proportional-values"></a>Значений пропорциональные

Значений пропорциональные определена связь между макета и представления. Это отношение определяет положение или значение масштаба дочернее представление как пропорцию соответствующего значения родительского макета. Эти значения представляют собой `double`s со значениями от 0 до 1.

Пропорциональное значения используются для положения и размера представления в макете. Таким образом, если представления шириной отношением, итоговый ширины задается значение в пропорции, умноженное на `AbsoluteLayout`его ширины. Например, с помощью `AbsoluteLayout` от ширины `500` и пропорционально ширину.5, отображаемую ширину представления в представлении будет 250 (500 x.5).

Чтобы использовать значений пропорциональные, задайте `LayoutBounds` с помощью (x, y) пропорции и пропорционально размеры, затем установите `LayoutFlags` для `All`.

В XAML:

```xaml
<Label Text="I'm bottom center on every device."
  AbsoluteLayout.LayoutBounds=".5,1,.5,.1" AbsoluteLayout.LayoutFlags="All"  />
```

В C#:

```csharp
var label = new Label {Text = "I'm bottom center on every device."};
AbsoluteLayout.SetLayoutBounds(label, new Rectangle(.5,1,.5,.1));
AbsoluteLayout.SetLayoutFlags(label, AbsoluteLayoutFlags.All);
```

<a name="Absolute_Values" />

### <a name="absolute-values"></a>Абсолютные значения

Абсолютные значения явно определить положение представления в макете. В отличие от значений пропорциональные абсолютные значения способны изменение положения и размера представление, которое не помещается в границах элемента макета.

С помощью абсолютные значения для размещения может быть опасно, если известен размер макета. При использовании абсолютного позиционирования, элемент в центре экрана в один размер удалось смещения в любой другой размер. Важно протестировать приложение на экранах различных размеров поддерживаемых устройств.

Чтобы использовать значения абсолютный макет, задайте `LayoutBounds` с помощью (x, y) координаты и явных размеров задайте `LayoutFlags` для `None`.

В XAML:

```xaml
<Label Text="I'm centered on iPhone 4 but no other device."
  AbsoluteLayout.LayoutBounds="115,150,100,100"  />
```

В C#:

```csharp
var label = new Label {Text = "I'm centered on iPhone 4 but no other device."};
AbsoluteLayout.SetLayoutBounds(label, new Rectangle(115,150,100,100));
```

## <a name="exploring-a-complex-layout"></a>Изучение создания сложных макетов

Каждый из макетов иметь сильные и слабые стороны, для создания определенного макетов. В этой серии статей макета пример приложения будет создана с тем же макетом страницы, реализованные с помощью трех различных макетах.

Рассмотрим следующий XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TheBusinessTumble.AbsoluteLayoutPage"
Title="AbsoluteLayout">
    <ContentPage.ToolbarItems>
        <ToolbarItem Text="Save" />
    </ContentPage.ToolbarItems>
    <ContentPage.Content>
        <ScrollView>
            <AbsoluteLayout BackgroundColor="Maroon">
                <BoxView BackgroundColor="Gray" AbsoluteLayout.LayoutBounds="0
                    0,1,100" AbsoluteLayout.LayoutFlags="XProportional,YProportional,WidthProportional" />
                <Button BackgroundColor="Maroon"
                    AbsoluteLayout.LayoutBounds=".5,55,70,70" AbsoluteLayout.LayoutFlags="XProportional" BorderRadius="35" />
                <Button BackgroundColor="Red" AbsoluteLayout.LayoutBounds=".5
                    60,60,60" AbsoluteLayout.LayoutFlags="XProportional" BorderRadius="30" />
                <Label Text="User Name" FontAttributes="Bold" FontSize="26"
                    TextColor="Black" HorizontalTextAlignment="Center" AbsoluteLayout.LayoutBounds=".5,140,1,40" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                <Entry Text="Bio + Hashtags" TextColor="White"
                    BackgroundColor="Maroon" AbsoluteLayout.LayoutBounds=".5,180,1,40" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                <AbsoluteLayout BackgroundColor="White"
                        AbsoluteLayout.LayoutBounds="0, 220, 1, 50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional">
                    <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,0,.5,1" AbsoluteLayout.LayoutFlags="WidthProportional,HeightProportional">
                        <Button BackgroundColor="Black" BorderRadius="20"
                            AbsoluteLayout.LayoutBounds="5,.5,40,40" AbsoluteLayout.LayoutFlags="YProportional" />
                        <Label Text="Accent Color" TextColor="Black"
                            AbsoluteLayout.LayoutBounds="50,.55,1,25" AbsoluteLayout.LayoutFlags="YProportional,WidthProportional" />
                    </AbsoluteLayout>
                    <AbsoluteLayout AbsoluteLayout.LayoutBounds="1,0,.5,1" AbsoluteLayout.LayoutFlags="WidthProportional,HeightProportional,XProportional">
                        <Button BackgroundColor="Maroon" BorderRadius="20"
                            AbsoluteLayout.LayoutBounds="5,.5,40,40" AbsoluteLayout.LayoutFlags="YProportional" />
                        <Label Text="Primary Color" TextColor="Black"
                            AbsoluteLayout.LayoutBounds="50,.55,1,25" AbsoluteLayout.LayoutFlags="YProportional,WidthProportional" />
                    </AbsoluteLayout>
                </AbsoluteLayout>
                <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,270,1,50" AbsoluteLayout.LayoutFlags="WidthProportional" Padding="5,0,0,0">
                    <Label Text="Age:" TextColor="White"
                        AbsoluteLayout.LayoutBounds="0,25,.25,50" AbsoluteLayout.LayoutFlags="WidthProportional" />
                    <Entry Text="35" TextColor="White" BackgroundColor="Maroon"
                        AbsoluteLayout.LayoutBounds="1,10,.75,50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                </AbsoluteLayout>
                <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,320,1,50" AbsoluteLayout.LayoutFlags="WidthProportional" Padding="5,0,0,0">
                    <Label Text="Interests:" TextColor="White"
                        AbsoluteLayout.LayoutBounds="0,25,.25,50" AbsoluteLayout.LayoutFlags="WidthProportional" />
                    <Entry Text="Xamarin.Forms" TextColor="White"
                        BackgroundColor="Maroon" AbsoluteLayout.LayoutBounds="1,10,.75,50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                </AbsoluteLayout>
                <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,370,1,50" AbsoluteLayout.LayoutFlags="WidthProportional" Padding="5,0,0,0">
                    <Label Text="Ask me about:" TextColor="White"
                        AbsoluteLayout.LayoutBounds="0,25,.25,50" AbsoluteLayout.LayoutFlags="WidthProportional" />
                    <Entry Text="Xamarin, C#, .NET, Mono" TextColor="White"
                        BackgroundColor="Maroon" AbsoluteLayout.LayoutBounds="1,10,.75,50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                </AbsoluteLayout>
            </AbsoluteLayout>
        </ScrollView>
    </ContentPage.Content>
</ContentPage>
```

Приведенный выше код результатов в следующем формате:

![](absolute-layout-images/abs.png "Сложные AbsoluteLayout")

Обратите внимание, что `AbsoluteLayout`s являются вложенными, так как в некоторых случаях вложение макетов может быть проще, чем все элементы в тот же макет представления.

## <a name="related-links"></a>Связанные ссылки

- [Создание мобильных приложений с помощью Xamarin.Forms, глава 14](https://developer.xamarin.com/r/xamarin-forms/book/chapter14.pdf)
- [AbsoluteLayout](xref:Xamarin.Forms.AbsoluteLayout)
- [Макет (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [Пример BusinessTumble (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
