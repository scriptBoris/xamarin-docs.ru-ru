---
title: Типы заполнения пути
description: В этой статье рассматриваются различные возможные эффекты с типы заполнения пути SkiaSharp и демонстрирует это с помощью примера кода.
ms.prod: xamarin
ms.assetid: 57103A7A-49A2-46AE-894C-7C2664682644
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: d16f6f6023c1db0223d5d5863e19116147f948d1
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2018
ms.locfileid: "39615474"
---
# <a name="the-path-fill-types"></a>Типы заполнения пути

_Обнаружение различных эффектов, поддерживаемых в средстве типы заполнения пути SkiaSharp_

Возможно перекрытие двух контуров в пути и строки, которые составляют единый профиль могут перекрывать друг друга. Любой замкнутой области потенциально может быть заполнен, но может не потребоваться заполнить все замкнутые области. Ниже приведен пример:

![](fill-types-images/filltypeexample.png "Указывает пяти звездочек частично filles")

Вы располагаете полным контролем над это. Алгоритм заполнение регулируется [ `SKFillType` ](xref:SkiaSharp.SKPath.FillType) свойство `SKPath`, необходимо присвоить значение является членом [ `SKPathFillType` ](xref:SkiaSharp.SKPathFillType) перечисления:

- `Winding`, значение по умолчанию
- `EvenOdd`
- `InverseWinding`
- `InverseEvenOdd`

Поворота и четный алгоритмы определить, является любой замкнутой области заполнения или не заполнено на основании гипотетической линию от этой области до бесконечности. Эту строку пересекает одну или несколько границ строк, составляющих путь. В режиме поворота Если число границ линий, нарисованных в одном направлении баланс out количество линий, рисуемых в обратном направлении, а затем области не заполнены. В противном случае область заполняется. Алгоритм четный Заливка области, если нечетное число границ строк.

С помощью многих стандартных путей поворота алгоритма часто заполняет все замкнутые области пути. Алгоритм четный обычно получаются более интересные результаты.

Классический пример — указывает пяти типа "звезда", как показано в **Five-Pointed типа "звезда"** страницы. [ **FivePointedStarPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/FivePointedStarPage.xaml) файл создает два `Picker` представления, чтобы выделить контур заполнения типа и обводкой или заполнить путь или оба и в каком порядке:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Paths.FivePointedStarPage"
             Title="Five-Pointed Star">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <Picker x:Name="fillTypePicker"
                Title="Path Fill Type"
                Grid.Row="0"
                Grid.Column="0"
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKPathFillType}">
                    <x:Static Member="skia:SKPathFillType.Winding" />
                    <x:Static Member="skia:SKPathFillType.EvenOdd" />
                    <x:Static Member="skia:SKPathFillType.InverseWinding" />
                    <x:Static Member="skia:SKPathFillType.InverseEvenOdd" />
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Picker x:Name="drawingModePicker"
                Title="Drawing Mode"
                Grid.Row="0"
                Grid.Column="1"
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type x:String}">
                    <x:String>Fill only</x:String>
                    <x:String>Stroke only</x:String>
                    <x:String>Stroke then Fill</x:String>
                    <x:String>Fill then Stroke</x:String>
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skiaforms:SKCanvasView x:Name="canvasView"
                                Grid.Row="1"
                                Grid.Column="0"
                                Grid.ColumnSpan="2"
                                PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

Файл с выделенным кодом использует оба `Picker` значения для рисования на который указывает пяти типа "звезда":

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float radius = 0.45f * Math.Min(info.Width, info.Height);

    SKPath path = new SKPath
    {
        FillType = (SKPathFillType)fillTypePicker.SelectedItem
    };
    path.MoveTo(info.Width / 2, info.Height / 2 - radius);

    for (int i = 1; i < 5; i++)
    {
        // angle from vertical
        double angle = i * 4 * Math.PI / 5;
        path.LineTo(center + new SKPoint(radius * (float)Math.Sin(angle), 
                                        -radius * (float)Math.Cos(angle)));
    }
    path.Close();

    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 50,
        StrokeJoin = SKStrokeJoin.Round
    };

    SKPaint fillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue
    };

    switch ((string)drawingModePicker.SelectedItem)
    {
        case "Fill only":
            canvas.DrawPath(path, fillPaint);
            break;

        case "Stroke only":
            canvas.DrawPath(path, strokePaint);
            break;

        case "Stroke then Fill":
            canvas.DrawPath(path, strokePaint);
            canvas.DrawPath(path, fillPaint);
            break;

        case "Fill then Stroke":
            canvas.DrawPath(path, fillPaint);
            canvas.DrawPath(path, strokePaint);
            break;
    }
}
```

Как правило, должна влиять на тип заливки путь только заливки "и" не штрихов, однако два `Inverse` влияет на режимы, заливки и обводки. Для заливки, два `Inverse` типов заполнения области противоположно, чтобы заполнить область за пределами типа "звезда". Для штрихов, два `Inverse` типы цвета всем, кроме штриха. Использование этих типов обратный заполнения может привести некоторые нечетное эффекты, как показано на снимке экрана iOS:

[![](fill-types-images/fivepointedstar-small.png "Тройной снимок экрана страницы Five-Pointed типа \"звезда\"")](fill-types-images/fivepointedstar-large.png#lightbox "тройной снимок экрана страницы Five-Pointed типа \"звезда\"")

На снимках экрана Android и UWP демонстрируют типичный эффект четный и поворота, но порядок штриха и заливки также влияет на результаты.

Алгоритм поворота зависит от направления, что линии. Обычно при создании пути, вы можете управлять этом направлении, как указано, что линии из одной точки в другую. Тем не менее `SKPath` класс также определяет методы, такие как `AddRect` и `AddCircle` , рисовать всю контуров. Чтобы контролировать, как рисуются эти объекты, методы включают параметр типа [ `SKPathDirection` ](xref:SkiaSharp.SKPathDirection), который имеет два члена:

- `Clockwise`
- `CounterClockwise`

Методы в `SKPath` , которые включают `SKPathDirection` параметр присвойте ему значение по умолчанию `Clockwise`.

**Перекрывающихся окружностей** страница создает путь с четырьмя кружками перекрывающиеся с типом четный путь заполнения:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float radius = Math.Min(info.Width, info.Height) / 4;

    SKPath path = new SKPath
    {
        FillType = SKPathFillType.EvenOdd
    };

    path.AddCircle(center.X - radius / 2, center.Y - radius / 2, radius);
    path.AddCircle(center.X - radius / 2, center.Y + radius / 2, radius);
    path.AddCircle(center.X + radius / 2, center.Y - radius / 2, radius);
    path.AddCircle(center.X + radius / 2, center.Y + radius / 2, radius);

    SKPaint paint = new SKPaint()
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Cyan
    };

    canvas.DrawPath(path, paint);

    paint.Style = SKPaintStyle.Stroke;
    paint.StrokeWidth = 10;
    paint.Color = SKColors.Magenta;

    canvas.DrawPath(path, paint);
}
```

Это интересный образ создается с минимальным объемом кода:

[![](fill-types-images/overlappingcircles-small.png "Тройной снимок экрана страницы перекрывающихся окружностей")](fill-types-images/overlappingcircles-large.png#lightbox "тройной снимок экрана страницы перекрывающихся окружностей")


## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
