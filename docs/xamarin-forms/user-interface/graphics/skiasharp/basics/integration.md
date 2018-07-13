---
title: Интеграция с Xamarin.Forms
description: В этой статье объясняется, как создать SkiaSharp графики, которая реагирует на сенсорный и Xamarin.Forms элементов и демонстрирует это с помощью примера кода.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 288224F1-7AEE-4148-A88D-A70C03F83D7A
author: charlespetzold
ms.author: chape
ms.date: 02/09/2017
ms.openlocfilehash: 35aede1a541d0ff62f6a4a5b57256c389e5a8640
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997534"
---
# <a name="integrating-with-xamarinforms"></a>Интеграция с Xamarin.Forms

_Создание графических объектов SkiaSharp, ответить на касания и элементы Xamarin.Forms_

SkiaSharp графики можно интегрировать с остальной частью Xamarin.Forms несколькими способами. Можно комбинировать SkiaSharp canvas и Xamarin.Forms на одной странице и даже позиции Xamarin.Forms элементы на основе холста SkiaSharp:

![](integration-images/integrationexample.png "Выбор цвета с помощью ползунков")

Другой подход к созданию интерактивной графики SkiaSharp в Xamarin.Forms осуществляется через сенсорного ввода.
На второй странице в [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) программы имеет право **коснитесь переключателя заполнения**. Выполняется рисование простого круга два способа &mdash; без заливки и с заливкой &mdash; переключаться нажатием. [ `TapToggleFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml.cs) Класс показано, как можно изменить графики SkiaSharp в ответ на ввод данных пользователем.

Для этой страницы `SKCanvasView` создается экземпляр класса в [TapToggleFill.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml) файл, который также задает Xamarin.Forms [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) представления:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.TapToggleFillPage"
             Title="Tap Toggle Fill">

    <skia:SKCanvasView PaintSurface="OnCanvasViewPaintSurface">
        <skia:SKCanvasView.GestureRecognizers>
            <TapGestureRecognizer Tapped="OnCanvasViewTapped" />
        </skia:SKCanvasView.GestureRecognizers>
    </skia:SKCanvasView>
</ContentPage>
```

Обратите внимание, что `skia` объявление пространства имен XML.

`Tapped` Обработчик для `TapGestureRecognizer` объект просто Инвертирует логическое поле и вызывает метод [ `InvalidateSurface` ](https://developer.xamarin.com/api/member/SkiaSharp.Views.Forms.SKCanvasView.InvalidateSurface()/) метод `SKCanvasView`:

```csharp
bool showFill = true;
...
void OnCanvasViewTapped(object sender, EventArgs args)
{
    showFill ^= true;
    (sender as SKCanvasView).InvalidateSurface();
}
```

Вызов `InvalidateSurface` фактически создает вызов метода `PaintSurface` обработчик, который использует `showFill` поля для заполнения или не заполняет элемент управления circle:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = Color.Red.ToSKColor(),
        StrokeWidth = 50
    };
    canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);

    if (showFill)
    {
        paint.Style = SKPaintStyle.Fill;
        paint.Color = SKColors.Blue;
        canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);
    }
}
```

`StrokeWidth` Свойство значение 50 сильнее выделила различие. Также вы увидите ширины всей строки путем рисования внутренней во-первых и затем контура. По умолчанию графики сама определяет, отображаемого в `PaintSurface` обработчик событий скрывать их ранее в обработчике.

**Изучение цвет** странице показано, как можно интегрировать SkiaSharp графики с другими элементами Xamarin.Forms, а также демонстрирует разницу между два альтернативных способа определения цветов в SkiaSharp. Статический [ `SKColor.FromHsl` ](https://developer.xamarin.com/api/member/SkiaSharp.SKColor.FromHsl/p/System.Single/System.Single/System.Single/System.Byte/) метод создает `SKColor` значение на основе оттенок-насыщенность-освещенность модели:

```csharp
public static SKColor FromHsl (Single h, Single s, Single l, Byte a)
```

Статический [ `SKColor.FromHsv` ](https://developer.xamarin.com/api/member/SkiaSharp.SKColor.FromHsv/p/System.Single/System.Single/System.Single/System.Byte/) метод создает `SKColor` значение, основанное на аналогичную модель оттенок-насыщенность-Value:

```csharp
public static SKColor FromHsv (Single h, Single s, Single v, Byte a)
```

В обоих случаях `h` аргумента в диапазоне от 0 до 360. `s`, `l`, И `v` аргументы в диапазоне от 0 до 100. `a` (Альфа-канал или непрозрачность) аргумента в диапазоне от 0 до 255.

[ **ColorExplorePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml) файл создает два `SKCanvasView` объекты в `StackLayout` side-by-side, с помощью `Slider` и `Label` представления, которые позволяют пользователю выбрать HSL и Значения цветов HSV:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Basics.ColorExplorePage"
             Title="Color Explore">
    <StackLayout>
        <!-- Hue slider -->
        <Slider x:Name="hueSlider"
                Maximum="360"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference hueSlider},
                              Path=Value,
                              StringFormat='Hue = {0:F0}'}" />

        <!-- Saturation slider -->
        <Slider x:Name="saturationSlider"
                Maximum="100"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference saturationSlider},
                              Path=Value,
                              StringFormat='Saturation = {0:F0}'}" />

        <!-- Lightness slider -->
        <Slider x:Name="lightnessSlider"
                Maximum="100"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference lightnessSlider},
                              Path=Value,
                              StringFormat='Lightness = {0:F0}'}" />

        <!-- HSL canvas view -->
        <Grid VerticalOptions="FillAndExpand">
            <skia:SKCanvasView x:Name="hslCanvasView"
                               PaintSurface="OnHslCanvasViewPaintSurface" />

            <Label x:Name="hslLabel"
                   HorizontalOptions="Center"
                   VerticalOptions="Center"
                   BackgroundColor="Black"
                   TextColor="White" />
        </Grid>

        <!-- Value slider -->
        <Slider x:Name="valueSlider"
                Maximum="100"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference valueSlider},
                              Path=Value,
                              StringFormat='Value = {0:F0}'}" />

        <!-- HSV canvas view -->
        <Grid VerticalOptions="FillAndExpand">
            <skia:SKCanvasView x:Name="hsvCanvasView"
                               PaintSurface="OnHsvCanvasViewPaintSurface" />

            <Label x:Name="hsvLabel"
                   HorizontalOptions="Center"
                   VerticalOptions="Center"
                   BackgroundColor="Black"
                   TextColor="White" />
        </Grid>
    </StackLayout>
</ContentPage>
```

Два `SKCanvasView` элементы находятся в одной ячейке `Grid` с `Label` находятся в верхней части для отображения результирующее значение цвета RGB.

[ **ColorExplorePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml.cs) файл с выделенным кодом приложения относительно прост. Общий `ValueChanged` обработчик для трех `Slider` элементы просто делает недействительным оба `SKCanvasView` элементов. `PaintSurface` Обработчики очистить холст с цветом обозначены `Slider` элементов и также задать `Label` находятся в верхней части `SKCanvasView` элементов:

```csharp
public partial class ColorExplorePage : ContentPage
{
    public ColorExplorePage()
    {
        InitializeComponent();

        hueSlider.Value = 0;
        saturationSlider.Value = 100;
        lightnessSlider.Value = 50;
        valueSlider.Value = 100;
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        hslCanvasView.InvalidateSurface();
        hsvCanvasView.InvalidateSurface();
    }

    void OnHslCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKColor color = SKColor.FromHsl((float)hueSlider.Value,
                                        (float)saturationSlider.Value,
                                        (float)lightnessSlider.Value);
        args.Surface.Canvas.Clear(color);

        hslLabel.Text = String.Format(" RGB = {0:X2}-{1:X2}-{2:X2} ",
                                      color.Red, color.Green, color.Blue);
    }

    void OnHsvCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKColor color = SKColor.FromHsv((float)hueSlider.Value,
                                        (float)saturationSlider.Value,
                                        (float)valueSlider.Value);
        args.Surface.Canvas.Clear(color);

        hsvLabel.Text = String.Format(" RGB = {0:X2}-{1:X2}-{2:X2} ",
                                      color.Red, color.Green, color.Blue);
    }
}
```

В моделях цвета HSL и HSV значение оттенка в диапазоне от 0 до 360 и указывает главный цветового тона цвета. Это традиционный цвета радуги: красный, оранжевый, желтый, зеленый, синий, indigo, фиолетовый и обратно в круге в red.

В модели HSL нулевое значение для яркости всегда черной, и значение 100 всегда является белым. Если значение насыщенности равно 0, освещенность значения от 0 до 100: оттенков серого цвета. Увеличение насыщенность добавляет дополнительные цвета. Чистые цвета (которые значений RGB, с помощью одного компонента, равными 255, другой равен 0, а третий от 0 до 255) возникают, если насыщенность — 100 и яркости — 50.

В модели HSV чистого цвета привести при насыщенность и значение 100. Если значение равно 0, независимо от других параметров, черный цвет. Когда насыщенность 0 и значение в диапазоне от 0 до 100, то происходит оттенков серого.

Но лучший способ почувствовать эти две модели для экспериментов с ними:

[![](integration-images/colorexplore-large.png "Тройной снимок экрана страницы изучение цвет")](integration-images/colorexplore-small.png#lightbox "тройной снимок экрана страницы изучение цвет")


## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
