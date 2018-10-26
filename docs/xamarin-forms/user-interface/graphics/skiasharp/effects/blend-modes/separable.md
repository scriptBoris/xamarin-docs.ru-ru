---
title: Режимы отделяемых blend
description: Режимы отделяемых наложения используйте для изменения красного, зеленого и синего цветов.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 66D1A537-A247-484E-B5B9-FBCB7838FBE9
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: c064b7fd5e436092593a194aee5d7498aef6c057
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50132127"
---
# <a name="the-separable-blend-modes"></a>Режимы отделяемых blend

Как было показано в этой статье [ **режимы смешения SkiaSharp Портер-Duff**](porter-duff.md), режимов смешения Портер Duff обычно выполняют операции обрезки. Режимы отделяемых blend различаются. Режимы отделяемых alter компонентов отдельных красного, зеленого и синего цвета изображения. Режимы отделяемых blend можно смешивать цвета, чтобы продемонстрировать, что сочетание красного, зеленого и синего действительно белый:

![Основных цветов](separable-images/SeparableSample.png "основных цветов")

## <a name="lighten-and-darken-two-ways"></a>Осветление и темнее два способа 

Это часто используются Битовая карта, немного слишком темная или слишком света. Можно использовать режимы отделяемых наложения светлее или темнее imabe.  Действительно, два из режимы отделяемых blend [ `SKBlendMode` ](xref:SkiaSharp.SKBlendMode) перечисления называются `Lighten` и `Darken`. 

В этих двух режимов демонстрируются **«Замена светлым» и «замена темным»** страницы. Файл XAML создает два `SKCanvasView` объектов и два `Slider` представления:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.LightenAndDarkenPage"
             Title="Lighten and Darken">
    <StackLayout>
        <skia:SKCanvasView x:Name="lightenCanvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="lightenSlider"
                Margin="10"
                ValueChanged="OnSliderValueChanged" />

        <skia:SKCanvasView x:Name="darkenCanvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="darkenSlider"
                Margin="10"
                ValueChanged="OnSliderValueChanged" />
    </StackLayout>
</ContentPage>
```

Первый `SKCanvasView` и `Slider` демонстрации `SKBlendMode.Lighten` и демонстрирует, Вторая пара `SKBlendMode.Darken`. Два `Slider` представления одного и того же `ValueChanged` обработчик, а два `SKCanvasView` одного и того же `PaintSurface` обработчика. Оба проверка обработчики событий, какой он генерирует события:

```csharp
public partial class LightenAndDarkenPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                typeof(SeparableBlendModesPage),
                "SkiaSharpFormsDemos.Media.Banana.jpg");

    public LightenAndDarkenPage ()
    {
        InitializeComponent ();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if ((Slider)sender == lightenSlider)
        {
            lightenCanvasView.InvalidateSurface();
        }
        else
        {
            darkenCanvasView.InvalidateSurface();
        }
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find largest size rectangle in canvas
        float scale = Math.Min((float)info.Width / bitmap.Width,
                               (float)info.Height / bitmap.Height);
        SKRect rect = SKRect.Create(scale * bitmap.Width, scale * bitmap.Height);
        float x = (info.Width - rect.Width) / 2;
        float y = (info.Height - rect.Height) / 2;
        rect.Offset(x, y);

        // Display bitmap
        canvas.DrawBitmap(bitmap, rect);

        // Display gray rectangle with blend mode
        using (SKPaint paint = new SKPaint())
        {
            if ((SKCanvasView)sender == lightenCanvasView)
            {
                byte value = (byte)(255 * lightenSlider.Value);
                paint.Color = new SKColor(value, value, value);
                paint.BlendMode = SKBlendMode.Lighten;
            }
            else
            {
                byte value = (byte)(255 * (1 - darkenSlider.Value));
                paint.Color = new SKColor(value, value, value);
                paint.BlendMode = SKBlendMode.Darken;
            }

            canvas.DrawRect(rect, paint);
        }
    }
}
```

`PaintSurface` Обработчик вычисляет прямоугольника, подходящий для растрового изображения. Обработчик, отображает этот точечный рисунок, а затем отображает прямоугольник растрового изображения, используя `SKPaint` объект с его `BlendMode` свойство значение `SKBlendMode.Lighten` или `SKBlendMode.Darken`. `Color` Свойство является оттенком серого, на основе `Slider`. Для `Lighten` режим, цветовые диапазоны от черного к белому, но для `Darken` его диапазон от белого к черному режим.

Снимки экрана в направлении слева направо Показать все большей `Slider` значений по верхнему изображению получает светлее и нижнее изображение получает темнее:

[![Осветление и который затемняется](separable-images/LightenAndDarken.png "Осветление и темнее")](separable-images/LightenAndDarken-Large.png#lightbox)

Эта программа демонстрирует, как в обычной используемые режимы отделяемых blend: назначения — это образ какого-либо рода, очень часто растрового изображения. Источник представляет собой прямоугольник, отображается с помощью `SKPaint` объект с его `BlendMode` свойства задан режим отделяемых blend. Прямоугольник может быть сплошным цветом (как показано ниже) или градиент. Прозрачность — _не_ обычно используется с режимами отделяемых blend.

Во время экспериментов с этой программой, вы обнаружите, что эти два blend не светлее и изображение единообразно. Вместо этого `Slider` кажется Установите пороговое значение какого-либо рода. Например, с увеличением `Slider` для `Lighten` режим, более темные области изображения произойти свет сначала светлые области не изменяются.

Для `Lighten` режим, если конечная точка является значение цвета RGB (БД для аварийного восстановления, группе рассылки,), а пикселей источника является цветом (Sr Sg, Sb), а затем выходные данные (или Og Ob) вычисляется следующим образом:

 "Или" = max (аварийного восстановления, Sr) Og = max (Dg, Sg) Ob = max (база данных, Sb)

Для красного, зеленого и синего отдельно, результатом является более источника и назначения. Этот сценарий создает эффект сначала Осветление темные области назначения.

`Darken` Режим похож, за исключением того, что результат, равно меньшему из источника и назначения:

 "Или" = Og min (аварийного восстановления, Sr) = Ob min (Dg, Sg) = min (база данных, Sb)

Красного, зеленого и синего компонентов, каждый обрабатываются отдельно, поэтому эти режимы смешения, называются _отделяемых_ режимы смешения. По этой причине аббревиатур **Dc** и **Sc** может использоваться для назначения и исходного цвета и понятно, что вычисления применить к каждому из красного, зеленого и синего компонентов отдельно.

Ниже приведены все режимы отделяемых наложения с краткий перечень их предназначение. Во втором — исходного цвета, никаких изменений не происходит:

| Режим наложения   | Без изменений | Операция |
| ------------ | --------- | --------- |
| `Plus`       | Черный     | Осветляет, добавив цвета: Sc + контроллера домена |
| `Modulate`   | Белый     | Затемняет путем умножения цвета: Sc· Контроллер домена | 
| `Screen`     | Черный     | Дополняет произведение дополняет: Sc + Dc &ndash; Sc· Контроллер домена |
| `Overlay`    | Серый      | Обратное значение `HardLight` |
| `Darken`     | Белый     | Минимальное цветов: min (Sc, контроллер домена) |
| `Lighten`    | Черный     | Максимальное число цветов: max (Sc, контроллер домена) |
| `ColorDodge` | Черный     | Черным назначения на основе источника |
| `ColorBurn`  | Белый     | Затемняет назначения на основе источника | 
| `HardLight`  | Серый      | Аналогичную последствия Харша spotlight |
| `SoftLight`  | Серый      | Аналогичную последствия Мягкий прожектор | 
| `Difference` | Черный     | Вычитает темнее из более светлой: Abs (Dc &ndash; Sc) | 
| `Exclusion`  | Черный     | Аналогичную `Difference` но понизить контрастности |
| `Multiply`   | Белый     | Затемняет путем умножения цвета: Sc· Контроллер домена |

Более подробные алгоритмы можно найти в W3C [ **композиции и наложения уровня 1** ](https://www.w3.org/TR/compositing-1/) спецификации и Skia [ **ссылку SkBlendMode** ](https://skia.org/user/api/SkBlendMode_Reference), несмотря на то, что нотация в этих двух источников, не совпадает. Имейте в виду, что `Plus` часто рассматривается как режим наложения Duff Портер, и `Modulate` не является частью спецификации W3C.

Если источником является прозрачным, затем для всех разделяемых режимы смешения за исключением `Modulate`, режим наложения не оказывает влияния. Как вы видели ранее, `Modulate` режим blend включает в себя альфа-канал в умножения. В противном случае `Modulate` имеет тот же эффект, что `Multiply`. 

Обратите внимание, что два режима с именем `ColorDodge` и `ColorBurn`. Слова _избавиться от_ и _записать_ была создана в фотографический фотолаборатория методики. Лупа делает фотографический печати, проливая свет через отрицательное. С помощью отсутствии света печати будет белым. Печать получает более темный оттенок, так как дополнительные свет ложится на печать для более длительного периода времени. Печать принимающих часто используется для блокировки некоторых света попала в определенной части печати, что делает более светлые области вручную или маленьких объектов. Этот процесс называется _Осветление_. И наоборот, Непрозрачный материал отверстия в ее (или блокировки большую часть света руки) может использоваться для направления дополнительные свет в определенной точке на который затемняется, он вызывается _записи_.

**Избавиться от и Burn** программой очень похожа на **«Замена светлым» и «замена темным»**. Файл XAML является структурированные прежним, но с именами различных элементов и файл с выделенным кодом довольно аналогично, но последствия этих режимов два blend отличается:

[![Избавиться от и Burn](separable-images/DodgeAndBurn.png "избавиться от и Burn")](separable-images/DodgeAndBurn-Large.png#lightbox)

Для небольших `Slider` значения, `Lighten` режим осветляет "темных" областях во-первых, хотя `ColorDodge` осветляет более равномерно.

Обработка изображений приложения часто позволяют Осветление и быть доступны только для конкретной области, так же, как в фотолаборатория записи. Это можно сделать, градиентов или растрового изображения с помощью различных оттенков серого.

## <a name="exploring-the-separable-blend-modes"></a>Изучение режимы отделяемых blend

**Разделяемых режимах Blend** страница позволяет изучить все режимы отделяемых blend. Он отображает точечный рисунок назначения и источником цветным прямоугольником, используя один из режимов смешения. 

В файле XAML определяется `Picker` (чтобы выбрать режим наложения) и четыре ползунка. Первые три ползунков можно будет задать красного, зеленого и синего компонентов источника. Четвертый ползунок должен переопределить эти значения, задав оттенком серого. Отдельные ползунки не определены, но цвета обозначают их функции:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaviews="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.SeparableBlendModesPage"
             Title="Separable Blend Modes">

    <StackLayout>
        <skiaviews:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="blendModePicker"
                Title="Blend Mode"
                Margin="10, 0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKBlendMode}">
                    <x:Static Member="skia:SKBlendMode.Plus" />
                    <x:Static Member="skia:SKBlendMode.Modulate" />
                    <x:Static Member="skia:SKBlendMode.Screen" />
                    <x:Static Member="skia:SKBlendMode.Overlay" />
                    <x:Static Member="skia:SKBlendMode.Darken" />
                    <x:Static Member="skia:SKBlendMode.Lighten" />
                    <x:Static Member="skia:SKBlendMode.ColorDodge" />
                    <x:Static Member="skia:SKBlendMode.ColorBurn" />
                    <x:Static Member="skia:SKBlendMode.HardLight" />
                    <x:Static Member="skia:SKBlendMode.SoftLight" />
                    <x:Static Member="skia:SKBlendMode.Difference" />
                    <x:Static Member="skia:SKBlendMode.Exclusion" />
                    <x:Static Member="skia:SKBlendMode.Multiply" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Slider x:Name="redSlider"
                MinimumTrackColor="Red"
                MaximumTrackColor="Red"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="greenSlider"
                MinimumTrackColor="Green"
                MaximumTrackColor="Green"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="blueSlider"
                MinimumTrackColor="Blue"
                MaximumTrackColor="Blue"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="graySlider"
                MinimumTrackColor="Gray"
                MaximumTrackColor="Gray"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="colorLabel"
               HorizontalTextAlignment="Center" />

    </StackLayout>
</ContentPage>
```

Файл с выделенным кодом загружает один из ресурсов точечного рисунка и отображает его дважды, один раз в верхней половине холста, а затем снова в нижней половине холста:

```csharp
public partial class SeparableBlendModesPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(SeparableBlendModesPage),
                        "SkiaSharpFormsDemos.Media.Banana.jpg"); 

    public SeparableBlendModesPage()
    {
        InitializeComponent();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs e)
    {
        if (sender == graySlider)
        {
            redSlider.Value = greenSlider.Value = blueSlider.Value = graySlider.Value;
        }

        colorLabel.Text = String.Format("Color = {0:X2} {1:X2} {2:X2}",
                                        (byte)(255 * redSlider.Value),
                                        (byte)(255 * greenSlider.Value),
                                        (byte)(255 * blueSlider.Value));

        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Draw bitmap in top half
        SKRect rect = new SKRect(0, 0, info.Width, info.Height / 2);
        canvas.DrawBitmap(bitmap, rect, BitmapStretch.Uniform);

        // Draw bitmap in bottom halr
        rect = new SKRect(0, info.Height / 2, info.Width, info.Height);
        canvas.DrawBitmap(bitmap, rect, BitmapStretch.Uniform);

        // Get values from XAML controls
        SKBlendMode blendMode =
            (SKBlendMode)(blendModePicker.SelectedIndex == -1 ?
                                        0 : blendModePicker.SelectedItem);

        SKColor color = new SKColor((byte)(255 * redSlider.Value),
                                    (byte)(255 * greenSlider.Value),
                                    (byte)(255 * blueSlider.Value));

        // Draw rectangle with blend mode in bottom half
        using (SKPaint paint = new SKPaint())
        {
            paint.Color = color;
            paint.BlendMode = blendMode;
            canvas.DrawRect(rect, paint);
        }
    }
}
```

В конце `PaintSurface` обработчик, прямоугольника рисуется через второй растровое изображение с режим выбранного наложения и выбранного цвета. Вы можете сравнить измененный растрового изображения в нижней исходное растровое изображение в верхней:

[![Режимы отделяемых наложения](separable-images/SeparableBlendModes.png "режимы отделяемых Blend")](separable-images/SeparableBlendModes-Large.png#lightbox)

## <a name="additive-and-subtractive-primary-colors"></a>Сложения и вычитания основных цветов

**Основных цветов** страницы Рисует три круга красный, зеленый и синий:

[![Аддитивные основных цветов](separable-images/PrimaryColors-Additive.png "аддитивные основных цветов")](separable-images/PrimaryColors-Additive.png#lightbox)

Они являются аддитивными основных цветов. Создавать сочетания любыми двумя голубой, пурпурный и желтый и сочетание всех трех белый.

Эти три круга рисуются с `SKBlendMode.Plus` режим, но можно также использовать `Screen`, `Lighten`, или `Difference` для тот же эффект. Вот программы:

```csharp
public class PrimaryColorsPage : ContentPage
{
    bool isSubtractive;

    public PrimaryColorsPage ()
    {
        Title = "Primary Colors";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;

        // Switch between additive and subtractive primaries at tap
        TapGestureRecognizer tap = new TapGestureRecognizer();
        tap.Tapped += (sender, args) =>
        {
            isSubtractive ^= true;
            canvasView.InvalidateSurface();
        };
        canvasView.GestureRecognizers.Add(tap);

        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);
        float radius = Math.Min(info.Width, info.Height) / 4;
        float distance = 0.8f * radius;     // from canvas center to circle center
        SKPoint center1 = center + 
            new SKPoint(distance * (float)Math.Cos(9 * Math.PI / 6),
                        distance * (float)Math.Sin(9 * Math.PI / 6));
        SKPoint center2 = center +
            new SKPoint(distance * (float)Math.Cos(1 * Math.PI / 6),
                        distance * (float)Math.Sin(1 * Math.PI / 6));
        SKPoint center3 = center +
            new SKPoint(distance * (float)Math.Cos(5 * Math.PI / 6),
                        distance * (float)Math.Sin(5 * Math.PI / 6));

        using (SKPaint paint = new SKPaint())
        {
            if (!isSubtractive)
            {
                paint.BlendMode = SKBlendMode.Plus; 
                System.Diagnostics.Debug.WriteLine(paint.BlendMode);

                paint.Color = SKColors.Red;
                canvas.DrawCircle(center1, radius, paint);

                paint.Color = SKColors.Lime;    // == (00, FF, 00)
                canvas.DrawCircle(center2, radius, paint);

                paint.Color = SKColors.Blue;
                canvas.DrawCircle(center3, radius, paint);
            }
            else
            {
                paint.BlendMode = SKBlendMode.Multiply
                System.Diagnostics.Debug.WriteLine(paint.BlendMode);

                paint.Color = SKColors.Cyan;
                canvas.DrawCircle(center1, radius, paint);

                paint.Color = SKColors.Magenta;
                canvas.DrawCircle(center2, radius, paint);

                paint.Color = SKColors.Yellow;
                canvas.DrawCircle(center3, radius, paint);
            }
        }
    }
}
```

Программа содержит `TabGestureRecognizer`. Если вы коснитесь или щелкните экран, программа использует `SKBlendMode.Multiply` для отображения три первичные реплики для вычитания:

[![Субтрактивным основных цветов](separable-images/PrimaryColors-Subtractive.png "субтрактивным основных цветов")](separable-images/PrimaryColors-Subtractive-Large.png#lightbox)

`Darken` Режим также подходит для этого же эффекта.

## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)