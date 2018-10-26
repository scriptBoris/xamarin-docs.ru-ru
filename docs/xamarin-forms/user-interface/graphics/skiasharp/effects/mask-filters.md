---
title: Фильтры маска SkiaSharp
description: Узнайте, как использовать фильтр для создания размытия и другие эффекты альфа-канала.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 940422A1-8BC0-4039-8AD7-26C61320F858
author: davidbritch
ms.author: dabritch
ms.date: 08/27/2018
ms.openlocfilehash: 524ca31b6687709245507bce0baeb82e12a1b726
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111688"
---
# <a name="skiasharp-mask-filters"></a>Фильтры маска SkiaSharp

Маска располагаются эффекты, которые управляют геометрии и альфа-канал графических объектов. Чтобы использовать фильтр, задайте [ `MaskFilter` ](xref:SkiaSharp.SKPaint.MaskFilter) свойство `SKPaint` для объекта типа [ `SKMaskFilter` ](xref:SkiaSharp.SKMaskFilter) вы создали путем вызова одного из `SKMaskFilter` статические методы.

Лучший способ ознакомиться с фильтрами маска — Экспериментируя с этими статическими методами. Наиболее полезных фильтр создает размытия.

![Размытие пример](mask-filters-images/MaskFilterExample.png "Размытие пример")

Это единственная функция фильтра маски, описанные в этой статье. В следующей статье [ **фильтры образа SkiaSharp** ](image-filters.md) также описывается эффект размытия, который вы предпочитаете такой. 

Статический [ `SKMaskFilter.CreateBlur` ](xref:SkiaSharp.SKMaskFilter.CreateBlur(SkiaSharp.SKBlurStyle,System.Single)) метод имеет следующий синтаксис:

```csharp
public static SKMaskFilter CreateBlur (SKBlurStyle blurStyle, float sigma);
```

Перегрузки позволяют, задающее флаги для алгоритма, используемого для создания размытия и прямоугольник, чтобы избежать Размытие в областях, которые будут рассматриваться с других графических объектов.

[`SKBlurStyle`](xref:SkiaSharp.SKBlurStyle) представляет собой перечисление со следующими членами:

- `Normal`
- `Solid`
- `Outer`
- `Inner`

В приведенных ниже примерах показаны последствия этих стилей. `sigma` Параметр задает экстент размытия. В предыдущих версиях набора Skia со значением радиуса указанную степень размытия. Значение радиуса является предпочтительным для вашего приложения, есть ли статический [ `SKMaskFilter.ConvertRadiusToSigma` ](xref:SkiaSharp.SKMaskFilter.ConvertRadiusToSigma*) метод, который может быть преобразован из одного в другой. Этот метод умножает радиус 0.57735 и добавит 0,5.

**Маска размытия поэкспериментировать** странице в [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) пример позволяет экспериментировать с стили размытия и значения «сигма». Файл XAML создает экземпляр `Picker` четырьмя `SKBlurStyle` члены перечисления и `Slider` для задания значения «сигма»:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.MaskBlurExperimentPage"
             Title="Mask Blur Experiment">
    
    <StackLayout>
        <skiaforms:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="blurStylePicker" 
                Title="Filter Blur Style" 
                Margin="10, 0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKBlurStyle}">
                    <x:Static Member="skia:SKBlurStyle.Normal" />
                    <x:Static Member="skia:SKBlurStyle.Solid" />
                    <x:Static Member="skia:SKBlurStyle.Outer" />
                    <x:Static Member="skia:SKBlurStyle.Inner" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Slider x:Name="sigmaSlider"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaSlider},
                              Path=Value,
                              StringFormat='Sigma = {0:F1}'}"
               HorizontalTextAlignment="Center" />
    </StackLayout>
</ContentPage>
```

Файл с выделенным кодом использует эти значения для создания `SKMaskFilter` объект и присвойте ему значение `MaskFilter` свойство `SKPaint` объекта. Это `SKPaint` объект используется для рисования строку текста и растровое изображение:

```csharp
public partial class MaskBlurExperimentPage : ContentPage
{
    const string TEXT = "Blur My Text";

    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                            typeof(MaskBlurExperimentPage), 
                            "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    public MaskBlurExperimentPage ()
    {
        InitializeComponent ();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Pink);

        // Get values from XAML controls
        SKBlurStyle blurStyle =
            (SKBlurStyle)(blurStylePicker.SelectedIndex == -1 ?
                                        0 : blurStylePicker.SelectedItem);

        float sigma = (float)sigmaSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            // Set SKPaint properties
            paint.TextSize = (info.Width - 100) / (TEXT.Length / 2);
            paint.MaskFilter = SKMaskFilter.CreateBlur(blurStyle, sigma);

            // Get text bounds and calculate display rectangle
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);
            SKRect textRect = new SKRect(0, 0, info.Width, textBounds.Height + 50);

            // Center the text in the display rectangle
            float xText = textRect.Width / 2 - textBounds.MidX;
            float yText = textRect.Height / 2 - textBounds.MidY;

            canvas.DrawText(TEXT, xText, yText, paint);

            // Calculate rectangle for bitmap
            SKRect bitmapRect = new SKRect(0, textRect.Bottom, info.Width, info.Height);
            bitmapRect.Inflate(-50, -50);

            canvas.DrawBitmap(bitmap, bitmapRect, BitmapStretch.Uniform, paint: paint);
        }
    }
}
```

Вот программу на iOS, Android и универсальной платформы Windows (UWP) с `Normal` Размытие, стиля и повышая `sigma` уровней:

[![Маскировать размытия эксперимента: обычный](mask-filters-images/MaskBlurExperiment-Normal.png "маска размытия эксперимента - норм.")](mask-filters-images/MaskBlurExperiment-Normal-Large.png#lightbox)

Вы заметите, что только краев точечного рисунка подвержены размытия. `SKMaskFilter` Класс не является правильно эффект, используйте, если вы хотите размытие изображения весь точечный рисунок. Для этого появится желание использовать [ `SKImageFilter` ](xref:SkiaSharp.SKImageFilter) класса, как описано в следующей статье на [ **фильтры образа SkiaSharp**](image-filters.md).

Текст является более размытой с увеличением значения `sigma` аргумент. Экспериментировать с этой программой, вы заметите, что для какого-либо `sigma` значение, размытия более невероятных на рабочем столе Windows 10. Это различие связано меньше плотности пикселей на настольный монитор, чем на мобильных устройствах, и таким образом уменьшается высота текста в пикселях. `sigma` Значение пропорционально в степени размытия, в пикселях, так для данного `sigma` значение, действует более невероятных на вывод разделов ниже разрешения. В рабочем приложении, возможно, нужно будет вычислить `sigma` значение, которое пропорционально размеру изображения. 

Попробуйте несколько значений, прежде чем остановиться на уровне размытия, которое ищет лучшие приложения. Например, в **эксперимента размытия маска** странице, попробуйте задать `sigma` следующим образом:

```csharp
sigma = paint.TextSize / 18;
paint.MaskFilter = SKMaskFilter.CreateBlur(blurStyle, sigma);
```

Теперь `Slider` не оказывает влияния, но степень размытия согласуется различных платформ:

[![Маскировать эксперимента размытия - согласованного](mask-filters-images/MaskBlurExperiment-Consistent.png "маскировать размытия эксперимента: согласованный")](mask-filters-images/MaskBlurExperiment-Consistent-Large.png#lightbox)

Все снимки экрана до сих показали Размытие, создаваемое `SKBlurStyle.Normal` член перечисления. На следующих снимках экрана показано влияние `Solid`, `Outer`, и `Inner` Размытие стили:

[![Маскировать эксперимента размытия](mask-filters-images/MaskBlurExperiment.png "маскировать размытия эксперимента")](mask-filters-images/MaskBlurExperiment-Large.png#lightbox)

На снимке экрана показан iOS `Solid` стиль: текстовые знаки по-прежнему присутствуют как сплошной черной strokes и размытия добавляется снаружи эти символы. 

Android экрана в среднем отображает `Outer` стиль: символ штрихов, сами исключаются (как растровое изображение) и размытия окружает пустое место, где один раз применялось текстовых символов. 

На снимке экрана универсальной платформы Windows на справа показывает `Inner` стиля. Размытие поддерживается только для области обычно, текстовых символов.

[ **Линейный градиент SkiaSharp** ](shaders/linear-gradient.md#transparency-and-gradients) описано в статье **отражения градиента** программа, которая используется для моделирования отражения текстовой строки линейного градиента и преобразования:

[![Отражение градиентной](shaders/linear-gradient-images/ReflectionGradient.png "отражения градиента")](shaders/linear-gradient-images/ReflectionGradient-Large.png#lightbox)

**Нечетким отражения** страницы этот код добавляет одну инструкцию:

```csharp
public class BlurryReflectionPage : ContentPage
{
    const string TEXT = "Reflection";

    public BlurryReflectionPage()
    {
        Title = "Blurry Reflection";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Set text color to blue
            paint.Color = SKColors.Blue;

            // Set text size to fill 90% of width
            paint.TextSize = 100;
            float width = paint.MeasureText(TEXT);
            float scale = 0.9f * info.Width / width;
            paint.TextSize *= scale;

            // Get text bounds
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            // Calculate offsets to position text above center
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2;

            // Draw unreflected text
            canvas.DrawText(TEXT, xText, yText, paint);

            // Shift textBounds to match displayed text
            textBounds.Offset(xText, yText);

            // Use those offsets to create a gradient for the reflected text
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(0, textBounds.Top),
                                new SKPoint(0, textBounds.Bottom),
                                new SKColor[] { paint.Color.WithAlpha(0),
                                                paint.Color.WithAlpha(0x80) },
                                null,
                                SKShaderTileMode.Clamp);

            // Create a blur mask filter
            paint.MaskFilter = SKMaskFilter.CreateBlur(SKBlurStyle.Normal, paint.TextSize / 36);

            // Scale the canvas to flip upside-down around the vertical center
            canvas.Scale(1, -1, 0, yText);

            // Draw reflected text
            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

Новая инструкция добавляет фильтр размытия для отраженного текста, основанный на размер текста:

```csharp
paint.MaskFilter = SKMaskFilter.CreateBlur(SKBlurStyle.Normal, paint.TextSize / 36);
```

Этот фильтр размытия вызывает отражение, чтобы показаться гораздо более реалистичным.

[![Нечетким отражения](mask-filters-images/BlurryReflection.png "нечетким отражения")](mask-filters-images/BlurryReflection-Large.png#lightbox)

## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
