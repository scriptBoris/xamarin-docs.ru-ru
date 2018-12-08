---
title: SkiaSharp шум и составление
description: Создание шейдеров шума Перлина и объединить с другими.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 90C2D00A-2876-43EA-A836-538C3318CF93
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 4801aa12acf8eca2384cc5b41d677f7cb0bdd90d
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53052055"
---
# <a name="skiasharp-noise-and-composing"></a>SkiaSharp шум и составление

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

Для поиска в понятном и естественном обычно простой векторной графики. Прямых линий, кривых smooth и сплошным цветом не напоминать дефекты реальных объектов. При работе в графическом объекте компьютерных фильма 1982 _Tron_, компьютер по обработке и анализу Перлина Алексей приступил к разработке алгоритмы, которые используются для предоставления более реалистичный текстуры эти образы случайных процессов. В 1997 году Алексей Перлина выиграла награду Academy для технических награду. Впоследствии получило название шума Перлина начал свою работу, и он поддерживается в SkiaSharp. Ниже приведен пример:

![Пример шума Перлина](noise-images/NoiseSample.png "пример шума Перлина")

Как можно видеть, каждый пиксел не случайное значение цвета. Непрерывность от точки до пикселя приводит случайных фигур.

Поддержка шума Перлина в Skia основан на спецификации W3C для CSS и SVG. Раздел 8.20 из [ **фильтра эффекты модуль уровня 1** ](http://www.w3.org/TR/filter-effects-1/#feTurbulenceElement) включает базовых алгоритмов шума Перлина в коде C.

## <a name="exploring-perlin-noise"></a>Изучение шума Перлина

[ `SKShader` ](xref:SkiaSharp.SKShader) Класс определяет две различные статические методы для создания шума Перлина: [ `CreatePerlinNoiseFractalNoise` ](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise*) и [ `CreatePerlinNoiseTurbulence` ](xref:SkiaSharp.SKShader.CreatePerlinNoiseTurbulence*). Параметры идентичны:

```csharp
public static SkiaSharp CreatePerlinNoiseFractalNoise (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed);

public static SkiaSharp.SKShader CreatePerlinNoiseTurbulence (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed);
```

Оба метода также существовать в перегруженные версии с дополнительным `SKPointI` параметра. Разделе [ **шума Перлина мозаичное заполнение** ](#tiling-perlin-noise) рассматриваются эти перегрузки.

Два `baseFrequency` аргументы являются положительные значения, определенные в документации по SkiaSharp в диапазоне от 0 до 1, но может быть присвоено также более высокие значения. Чем выше значение, тем больше изменений в образе случайных в горизонтальном и вертикальном направлениях.

`numOctaves` Значение должно быть целым числом, равным 1 или более поздней версии. Он позволяет является фактором итерации в алгоритмах. Каждый дополнительный октавы помогает эффект равна половине предыдущих октавы, поэтому уменьшение эффекта с более высоким значением октавы.

`seed` Параметра является отправной точкой для генератора случайных чисел. Несмотря на то, что указано как значение с плавающей запятой, дробь усекается до того, как он используется 0 совпадает со значением 1.

**Шума Перлина** странице в [ **SkiaSharpFormsDemos**)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) образец позволяет экспериментировать с различными значениями `baseFrequency` и `numOctaves` аргументы. Ниже приведен файл XAML.

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.PerlinNoisePage"
             Title="Perlin Noise">

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="baseFrequencyXSlider"
                Maximum="4"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="baseFrequencyXText"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="baseFrequencyYSlider"
                Maximum="4"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="baseFrequencyYText"
               HorizontalTextAlignment="Center" />

        <StackLayout Orientation="Horizontal"
                     HorizontalOptions="Center"
                     Margin="10">

            <Label Text="{Binding Source={x:Reference octavesStepper},
                                  Path=Value,
                                  StringFormat='Number of Octaves: {0:F0}'}"
                   VerticalOptions="Center" />

            <Stepper x:Name="octavesStepper"
                     Minimum="1"
                     ValueChanged="OnStepperValueChanged" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

Он использует два `Slider` представления для двух `baseFrequency` аргументы. Чтобы расширить диапазон меньших значений, ползунки логарифмической. Файл с выделенным кодом вычисляет аргументы `SKShader`методы из степеней `Slider` значения. `Label` Представлениях отображаются вычисленные значения:

```csharp
float baseFreqX = (float)Math.Pow(10, baseFrequencyXSlider.Value - 4);
baseFrequencyXText.Text = String.Format("Base Frequency X = {0:F4}", baseFreqX);

float baseFreqY = (float)Math.Pow(10, baseFrequencyYSlider.Value - 4);
baseFrequencyYText.Text = String.Format("Base Frequency Y = {0:F4}", baseFreqY);
```

Объект `Slider` значение 1 соответствует 0,001, `Slider` значение ОС 2 соответствует 0,01, `Slider` значения 3 соответствует 0,1 и `Slider` 1 соответствует значению 4.

Ниже приведен файл с выделенным кодом, включающий этот код.

```csharp
public partial class PerlinNoisePage : ContentPage
{
    public PerlinNoisePage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnStepperValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Get values from sliders and stepper
        float baseFreqX = (float)Math.Pow(10, baseFrequencyXSlider.Value - 4);
        baseFrequencyXText.Text = String.Format("Base Frequency X = {0:F4}", baseFreqX);

        float baseFreqY = (float)Math.Pow(10, baseFrequencyYSlider.Value - 4);
        baseFrequencyYText.Text = String.Format("Base Frequency Y = {0:F4}", baseFreqY);

        int numOctaves = (int)octavesStepper.Value;

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader =
                SKShader.CreatePerlinNoiseFractalNoise(baseFreqX,
                                                       baseFreqY,
                                                       numOctaves,
                                                       0);

            SKRect rect = new SKRect(0, 0, info.Width, info.Height / 2);
            canvas.DrawRect(rect, paint);

            paint.Shader =
                SKShader.CreatePerlinNoiseTurbulence(baseFreqX,
                                                     baseFreqY,
                                                     numOctaves,
                                                     0);

            rect = new SKRect(0, info.Height / 2, info.Width, info.Height);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

Вот программу на iOS, Android и универсальной платформы Windows (UWP) устройств. Шума фрактала отображается в верхней части холста. Пропускаемые встряски — все находится в нижней половине:

[![Шума Перлина](noise-images/PerlinNoise.png "шума Перлина")](noise-images/PerlinNoise-Large.png#lightbox)

Те же аргументы всегда производят тот же шаблон, который начинается в левом верхнем углу. Эта согласованность очевидна в том случае, если вы измените ширину и высоту окна универсальной платформы Windows. Как Windows 10 перерисовывает экрана, шаблон в верхней половине холста остается неизменным.

Шум включает различные уровни прозрачности. Прозрачность становится очевидным, если задание цвета в `canvas.Clear()` вызова. Этот цвет становится заметной в шаблоне. Вы также увидите этот эффект в разделе [ **объединение нескольких шейдеры**](#combining-multiple-shaders).

Эти шаблоны шума Перлина редко используются сами по себе. Часто они являются подвергались blend режимы и цвет фильтров, описанных в последующих статьях.

## <a name="tiling-perlin-noise"></a>Мозаичное заполнение шума Перлина

Два статических `SKShader` методы для создания шума Перлина также существовать в перегруженных версий. [ `CreatePerlinNoiseFractalNoise` ](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise(System.Single,System.Single,System.Int32,System.Single,SkiaSharp.SKPointI)) И [ `CreatePerlinNoiseTurbulence` ](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise(System.Single,System.Single,System.Int32,System.Single,SkiaSharp.SKPointI)) перегрузки имеют дополнительный `SKPointI` параметр:

```csharp
public static SKShader CreatePerlinNoiseFractalNoise (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed, SKPointI tileSize);

public static SKShader CreatePerlinNoiseTurbulence (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed, SKPointI tileSize);
```

[ `SKPointI` ](xref:SkiaSharp.SKPointI) Структуры — это целое число версия знакомого [ `SKPoint` ](xref:SkiaSharp.SKPoint) структуры. `SKPointI` Определяет `X` и `Y` свойства типа `int` вместо `float`.

Эти методы создают повторяющийся шаблон указанного размера. В каждой плитке правым краем совпадает со значением левой границы и верхним краем совпадает с нижней границей. Эта особенность обсуждается в **Мозаичная шума Перлина** страницы. Этот файл XAML как в предыдущем примере, но имеет только `Stepper` представление для изменения `seed` аргумент:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.TiledPerlinNoisePage"
             Title="Tiled Perlin Noise">

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <StackLayout Orientation="Horizontal"
                     HorizontalOptions="Center"
                     Margin="10">

            <Label Text="{Binding Source={x:Reference seedStepper},
                                  Path=Value,
                                  StringFormat='Seed: {0:F0}'}"
                   VerticalOptions="Center" />

            <Stepper x:Name="seedStepper"
                     Minimum="1"
                     ValueChanged="OnStepperValueChanged" />

        </StackLayout>
    </StackLayout>
</ContentPage>
```

Файл с выделенным кодом определяет константу для размера мозаики. `PaintSurface` Обработчик создает точечный рисунок, размер и `SKCanvas` для рисования в этот точечный рисунок. `SKShader.CreatePerlinNoiseTurbulence` Метод создает построитель текстуры на языке с такой размер плитки. Этот шейдер рисуется на точечный рисунок:

```csharp
public partial class TiledPerlinNoisePage : ContentPage
{
    const int TILE_SIZE = 200;

    public TiledPerlinNoisePage()
    {
        InitializeComponent();
    }

    void OnStepperValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Get seed value from stepper
        float seed = (float)seedStepper.Value;

        SKRect tileRect = new SKRect(0, 0, TILE_SIZE, TILE_SIZE);

        using (SKBitmap bitmap = new SKBitmap(TILE_SIZE, TILE_SIZE))
        {
            using (SKCanvas bitmapCanvas = new SKCanvas(bitmap))
            {
                bitmapCanvas.Clear();

                // Draw tiled turbulence noise on bitmap
                using (SKPaint paint = new SKPaint())
                {
                    paint.Shader = SKShader.CreatePerlinNoiseTurbulence(
                                        0.02f, 0.02f, 1, seed,
                                        new SKPointI(TILE_SIZE, TILE_SIZE));

                    bitmapCanvas.DrawRect(tileRect, paint);
                }
            }

            // Draw tiled bitmap shader on canvas
            using (SKPaint paint = new SKPaint())
            {
                paint.Shader = SKShader.CreateBitmap(bitmap,
                                                     SKShaderTileMode.Repeat,
                                                     SKShaderTileMode.Repeat);
                canvas.DrawRect(info.Rect, paint);
            }

            // Draw rectangle showing tile
            using (SKPaint paint = new SKPaint())
            {
                paint.Style = SKPaintStyle.Stroke;
                paint.Color = SKColors.Black;
                paint.StrokeWidth = 2;

                canvas.DrawRect(tileRect, paint);
            }
        }
    }
}
```

Создав растрового изображения, другой `SKPaint` объект используется для создания шаблона мозаики точечный рисунок вызовом `SKShader.CreateBitmap`. Обратите внимание, что два аргумента `SKShaderTileMode.Repeat`:

```csharp
paint.Shader = SKShader.CreateBitmap(bitmap,
                                     SKShaderTileMode.Repeat,
                                     SKShaderTileMode.Repeat);
```

Этот шейдер используется для обработки на холст. Наконец другой `SKPaint` объект используется для обводки прямоугольника, показывающий размер исходного растрового изображения.

Только `seed` параметр, доступный для выбора из пользовательского интерфейса. Если же `seed` шаблон используется на каждой платформе, они отобразятся в тот же шаблон. Различные `seed` значения привести различных шаблонов:

[![Мозаичная шума Перлина](noise-images/TiledPerlinNoise.png "Мозаичная шума Перлина")](noise-images/TiledPerlinNoise-Large.png#lightbox)

Шаблон square 200 пикселей в левом верхнем углу легко перемещаются в другие плитки.

## <a name="combining-multiple-shaders"></a>Объединение нескольких шейдеров

`SKShader` Класс включает [ `CreateColor` ](xref:SkiaSharp.SKShader.CreateColor*) метод, который создает построитель текстуры на указанный сплошным цветом. Этот шейдер не очень удобен, сам по себе, так как просто можно задать цвета `Color` свойство `SKPaint` и задайте `Shader` свойство равным null.

Это `CreateColor` метод полезен в другом методе, `SKShader` определяет. Этот метод является [ `CreateCompose` ](xref:SkiaSharp.SKShader.CreateCompose(SkiaSharp.SKShader,SkiaSharp.SKShader)), который объединяет в себе два шейдеров. Ниже приведен синтаксис:

```csharp
public static SKShader CreateCompose (SKShader dstShader, SKShader srcShader);
```

`srcShader` (Источник шейдера) является фактически рисуется поверх `dstShader` (шейдера назначения). Если источник шейдера сплошным цветом и градиентом без прозрачности, шейдер назначения будут полностью скрыты.

Шейдер шума Перлина прозрачностью. Если что является источником, шейдер назначения будут показаны через прозрачные области.

**Состоящие шума Перлина** страница содержит файл XAML, который практически идентичен первому **шума Перлина** страницы. Файл с выделенным кодом осуществляется аналогичным образом. Но оригинал **шума Перлина** странице наборы `Shader` свойство `SKPaint` в шейдер, возвращаемое статическим `CreatePerlinNoiseFractalNoise` и `CreatePerlinNoiseTurbulence` методы. Это **состоящие шума Перлина** странице вызовы `CreateCompose` для сочетания шейдера. Местом назначения является сплошная синяя шейдера, созданных с помощью `CreateColor`. Источником является шейдера шума Перлина:

```csharp
public partial class ComposedPerlinNoisePage : ContentPage
{
    public ComposedPerlinNoisePage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnStepperValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Get values from sliders and stepper
        float baseFreqX = (float)Math.Pow(10, baseFrequencyXSlider.Value - 4);
        baseFrequencyXText.Text = String.Format("Base Frequency X = {0:F4}", baseFreqX);

        float baseFreqY = (float)Math.Pow(10, baseFrequencyYSlider.Value - 4);
        baseFrequencyYText.Text = String.Format("Base Frequency Y = {0:F4}", baseFreqY);

        int numOctaves = (int)octavesStepper.Value;

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateCompose(
                SKShader.CreateColor(SKColors.Blue),
                SKShader.CreatePerlinNoiseFractalNoise(baseFreqX,
                                                       baseFreqY,
                                                       numOctaves,
                                                       0));

            SKRect rect = new SKRect(0, 0, info.Width, info.Height / 2);
            canvas.DrawRect(rect, paint);

            paint.Shader = SKShader.CreateCompose(
                SKShader.CreateColor(SKColors.Blue),
                SKShader.CreatePerlinNoiseTurbulence(baseFreqX,
                                                     baseFreqY,
                                                     numOctaves,
                                                     0));

            rect = new SKRect(0, info.Height / 2, info.Width, info.Height);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

Шейдера шума фрактала размещена в верхней части; шейдер встряски — все находится в конце:

[![Состоящее шума Перлина](noise-images/ComposedPerlinNoise.png "состоящие шума Перлина")](noise-images/ComposedPerlinNoise-Large.png#lightbox)

Обратите внимание, что объем более синими, затем эти шейдеры, чем те, которые отображаются по **шума Перлина** страницы. Разница показана степень прозрачности в шейдеры шума.

Имеется также перегрузку [ `CreateCompose` ](xref:SkiaSharp.SKShader.CreateCompose(SkiaSharp.SKShader,SkiaSharp.SKShader,SkiaSharp.SKBlendMode)) метод:

```csharp
public static SKShader CreateCompose (SKShader dstShader, SKShader srcShader, SKBlendMode blendMode);
```

Последний параметр является членом `SKBlendMode` перечисления, перечисление с 29 члены, которые описаны в следующей серии статей на [ **SkiaSharp композиции и режимы наложения**](../blend-modes/index.md).

## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
