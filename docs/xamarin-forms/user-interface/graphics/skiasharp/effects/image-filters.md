---
title: Фильтры образа SkiaSharp
description: Сведения об использовании фильтра образа для создания размытия и тени.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 173E7B22-AEC8-4F12-B657-1C0CEE01AD63
author: davidbritch
ms.author: dabritch
ms.date: 08/27/2018
ms.openlocfilehash: b55067f7b4df66ccce23a7409281f4b8bbc4e9e9
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111641"
---
# <a name="skiasharp-image-filters"></a>Фильтры образа SkiaSharp

Фильтры образа, эффекты, которые работают на всех битов цвет пикселей, составляющих изображение. Они являются более гибкими, чем маска фильтров, которые работают только в альфа-канала, как описано в статье [ **фильтры маска SkiaSharp**](mask-filters.md). Чтобы использовать изображение фильтр, установите [ `ImageFilter` ](xref:SkiaSharp.SKPaint.ImageFilter) свойство `SKPaint` для объекта типа [ `SKImageFilter` ](xref:SkiaSharp.SKImageFilter) вы создали путем вызова одного из статических методов класса.

Лучший способ ознакомиться с фильтрами маска — Экспериментируя с этими статическими методами. Можно использовать маску фильтра размытия весь точечный рисунок:

![Размытие пример](image-filters-images/ImageFilterExample.png "Размытие пример")

В этой статье также демонстрируется использование фильтра образа для создания перетаскивания тени, а также для тиснение и Утопленный текст; эффекты.

## <a name="blurring-vector-graphics-and-bitmaps"></a>Размытие векторные и растровые изображения

Эффект размытия, созданные [ `SKImageFilter.CreateBlur` ](xref:SkiaSharp.SKImageFilter.CreateBlur*) статический метод имеет значительное преимущество по сравнению с методами размытия в [ `SKMaskFilter` ](xref:SkiaSharp.SKMaskFilter) класса: фильтра образа можно размыть всего точечного рисунка. Метод имеет следующий синтаксис:

```csharp
public static SkiaSharp.SKImageFilter CreateBlur (float sigmaX, float sigmaY, 
                                                  SKImageFilter input = null, 
                                                  SKImageFilter.CropRect cropRect = null);
```

Этот метод имеет два значения «сигма» &mdash; первый экстент размытия в горизонтальном направлении, а второй для по вертикали. Фильтры образа каскадирование, указав другой фильтр изображения в качестве необязательный третий аргумент. Можно также указать прямоугольника обрезки.

**Изображение размытия поэкспериментировать** странице в [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) включает две `Slider` представления, которые позволяют экспериментировать с Настройка различных уровней размытия:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.ImageBlurExperimentPage"
             Title="Image Blur Experiment">
    
    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="sigmaXSlider"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaXSlider},
                              Path=Value,
                              StringFormat='Sigma X = {0:F1}'}"
               HorizontalTextAlignment="Center" />
        
        <Slider x:Name="sigmaYSlider"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaYSlider},
                              Path=Value,
                              StringFormat='Sigma Y = {0:F1}'}"
               HorizontalTextAlignment="Center" />
    </StackLayout>
</ContentPage>
```

В файле кода используются два `Slider` значения для вызова `SKImageFilter.CreateBlur` для `SKPaint` объект, используемый для отображения текста и точечный рисунок:


```csharp
public partial class ImageBlurExperimentPage : ContentPage
{
    const string TEXT = "Blur My Text";

    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                            typeof(MaskBlurExperimentPage),
                            "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    public ImageBlurExperimentPage ()
    {
        InitializeComponent ();
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

        // Get values from sliders
        float sigmaX = (float)sigmaXSlider.Value;
        float sigmaY = (float)sigmaYSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            // Set SKPaint properties
            paint.TextSize = (info.Width - 100) / (TEXT.Length / 2);
            paint.ImageFilter = SKImageFilter.CreateBlur(sigmaX, sigmaY);

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

Три снимка экранов Показать различные параметры для `sigmaX` и `sigmaY` параметры:

[![Изображение эксперимента размытия](image-filters-images/ImageBlurExperiment.png "изображения размытия эксперимента")](image-filters-images/ImageBlurExperiment-Large.png#lightbox)

Для вывода размытия почтовыми различные размеры и разрешения необходимо установить `sigmaX` и `sigmaY` значения, которые будут указывать размер в пикселях, готовый для просмотра изображения, применяемый к размытия.

## <a name="drop-shadow"></a>Тень

[ `SKImageFilter.CreateDropShadow` ](xref:SkiaSharp.SKImageFilter.CreateDropShadow*) Статический метод создает `SKImageFilter` объект тени:

```csharp
public static SKImageFilter CreateDropShadow (float dx, float dy, 
                                              float sigmaX, float sigmaY, 
                                              SKColor color, 
                                              SKDropShadowImageFilterShadowMode shadowMode, 
                                              SKImageFilter input = null, 
                                              SKImageFilter.CropRect cropRect = null);
```

Значение этого объекта `ImageFilter` свойство `SKPaint` объекта и ничего с этого объекта будет иметь тень за ней.

`dx` И `dy` параметра указывают горизонтальное и вертикальное смещение тени в пикселях от графического объекта. Соглашения для двумерной графики в предположении, что источник света, поступающих от левого верхнего угла, который подразумевает, что оба эти аргумента должно быть положительным, чтобы изменить расположение тени под и справа от графического объекта.

`sigmaX` И `sigmaY` параметры жизнью людей размываются факторы тени.

`color` Параметр является цвет тени. Это `SKColor` значение может содержать прозрачности. Один из вариантов является значение цвета `SKColors.Black.WithAlpha(0x80)` затемнения любой цвет фона. 

Последние два параметра являются необязательными.

**Drop поэкспериментировать тени** программы позволяет поэкспериментировать со значениями `dx`, `dy`, `sigmaX`, и `sigmaY` для отображения строку текста с тенью. Файл XAML создает четыре `Slider` представления для задания этих значений:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.DropShadowExperimentPage"
             Title="Drop Shadow Experiment">
    <ContentPage.Resources>
        <Style TargetType="Slider">
            <Setter Property="Margin" Value="10, 0" />
        </Style>

        <Style TargetType="Label">
            <Setter Property="HorizontalTextAlignment" Value="Center" />
        </Style>
    </ContentPage.Resources>

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="dxSlider"
                Minimum="-20"
                Maximum="20"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference dxSlider},
                              Path=Value,
                              StringFormat='Horizontal offset = {0:F1}'}" />

        <Slider x:Name="dySlider"
                Minimum="-20"
                Maximum="20"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference dySlider},
                              Path=Value,
                              StringFormat='Vertical offset = {0:F1}'}" />

        <Slider x:Name="sigmaXSlider"
                Maximum="10"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaXSlider},
                              Path=Value,
                              StringFormat='Sigma X = {0:F1}'}" />

        <Slider x:Name="sigmaYSlider"
                Maximum="10"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaYSlider},
                              Path=Value,
                              StringFormat='Sigma Y = {0:F1}'}" />
    </StackLayout>
</ContentPage>
```

Файл с выделенным кодом использует эти значения для создания red тени на строку синий текст:

```csharp
public partial class DropShadowExperimentPage : ContentPage
{
    const string TEXT = "Drop Shadow";

    public DropShadowExperimentPage ()
    {
        InitializeComponent ();
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

        canvas.Clear();

        // Get values from sliders
        float dx = (float)dxSlider.Value;
        float dy = (float)dySlider.Value;
        float sigmaX = (float)sigmaXSlider.Value;
        float sigmaY = (float)sigmaYSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            // Set SKPaint properties
            paint.TextSize = info.Width / 7;
            paint.Color = SKColors.Blue;
            paint.ImageFilter = SKImageFilter.CreateDropShadow(
                                    dx,
                                    dy,
                                    sigmaX,
                                    sigmaY,
                                    SKColors.Red,
                                    SKDropShadowImageFilterShadowMode.DrawShadowAndForeground); 

            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            // Center the text in the display rectangle
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

Вот ее запуск на всех трех платформах:

[![Удалить эксперимент тени](image-filters-images/DropShadowExperiment.png "Drop эксперимента тени")](image-filters-images/DropShadowExperiment-Large.png#lightbox)

Отрицательные значения на снимке экрана универсальной платформы Windows на правом вызвать тени отображается сверху и слева от текста. Это предполагает источника света в правом нижнем углу, который не является соглашением для компьютерной графике. Но это не кажется неправильный любым способом, возможно потому, что кажется более украшения, чем большинство тени, также становится очень нечетким тени.

## <a name="lighting-effects"></a>Эффекты освещения

`SKImageFilter` Класс определяет шесть методов, которые имеют аналогичные имена и параметры, перечисленные здесь, в порядке возрастания сложности:

- [`CreateDistantLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreateDistantLitDiffuse*)
- [`CreateDistantLitSpecular`](xref:SkiaSharp.SKImageFilter.CreateDistantLitSpecular*)
- [`CreatePointLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreatePointLitDiffuse*)
- [`CreatePointLitSpecular`](xref:SkiaSharp.SKImageFilter.CreatePointLitSpecular*)
- [`CreateSpotLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreateSpotLitDiffuse*)
- [`CreateSpotLitSpecular`](xref:SkiaSharp.SKImageFilter.CreateSpotLitSpecular*)

Эти методы создают изображения фильтры, которые имитируют влияние различные виды освещения на трехмерной поверхности. Фильтр результирующий образ освещает двухмерный объекты, как если бы они существовали в трехмерном пространстве, что может привести к этим объектам отображаются с повышенными правами или утопленная, или с помощью зеркального отражения.

`Distant` Света методы предполагают, что загорается от дальней расстояния. Для целей illuminating объектов света предполагается, что точки в одном направлении, согласованных в трехмерном пространстве, как Sun в небольшой области Земли. `Point` Света методы имитации лампочка в трехмерном пространстве, которое выдает свет во всех направлениях. `Spot` Имеет свет, положение и направлении, как фонарик. 

Расположения и указаниям, приведенным в трехмерном пространстве указываются с помощью значения [ `SKPoint3` ](xref:SkiaSharp.SKPoint3) структуру, которая аналогична `SKPoint` , но с тремя свойствами с именем `X`, `Y`, и `Z`. 

Количество и сложность параметры для этих методов усложняют экспериментов с ними. Чтобы приступить к работе, **отдаленных поэкспериментировать свет** странице позволяет экспериментировать с параметрами, чтобы `CreateDistantLightDiffuse` метод:

```csharp
public static SKImageFilter CreateDistantLitDiffuse (SKPoint3 direction, 
                                                     SKColor lightColor, 
                                                     float surfaceScale, 
                                                     float kd, 
                                                     SKImageFilter input = null, 
                                                     SKImageFilter.CropRect cropRect = null);
```

Страницы не использует последние два необязательных параметров. 

Три `Slider` представления в XAML-файла позволяют выбрать `Z` координата `SKPoint3` значение, `surfaceScale` параметра и `kd` параметр, который определен в документации по API, как «константа диффузным освещением»:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaLightExperiment.MainPage"
             Title="Distant Light Experiment">

    <StackLayout>

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface"
                           VerticalOptions="FillAndExpand" />

        <Slider x:Name="zSlider" 
                Minimum="-10"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference zSlider},
                              Path=Value,
                              StringFormat='Z = {0:F0}'}"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="surfaceScaleSlider" 
                Minimum="-1"
                Maximum="1"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference surfaceScaleSlider},
                              Path=Value,
                              StringFormat='Surface Scale = {0:F1}'}"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="lightConstantSlider" 
                Minimum="-1"
                Maximum="1"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference lightConstantSlider},
                              Path=Value,
                              StringFormat='Light Constant = {0:F1}'}"
               HorizontalTextAlignment="Center" />
    </StackLayout>
</ContentPage>
```

Файл с выделенным кодом получает эти три значения и использует их для создания фильтра изображение для отображения текстовой строки:

```csharp
public partial class DistantLightExperimentPage : ContentPage
{
    const string TEXT = "Lighting";

    public DistantLightExperimentPage()
    {
        InitializeComponent();
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

        canvas.Clear();

        float z = (float)zSlider.Value;
        float surfaceScale = (float)surfaceScaleSlider.Value;
        float lightConstant = (float)lightConstantSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            paint.IsAntialias = true;

            // Size text to 90% of canvas width
            paint.TextSize = 100;
            float textWidth = paint.MeasureText(TEXT);
            paint.TextSize *= 0.9f * info.Width / textWidth;

            // Find coordinates to center text
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            float xText = info.Rect.MidX - textBounds.MidX;
            float yText = info.Rect.MidY - textBounds.MidY;

            // Create distant light image filter
            paint.ImageFilter = SKImageFilter.CreateDistantLitDiffuse(
                                    new SKPoint3(2, 3, z),
                                    SKColors.White,
                                    surfaceScale,
                                    lightConstant);

            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

Первый аргумент `SKImageFilter.CreateDistantLitDiffuse` направлению источника света. Положительная X и Y координаты указать, что индикатор указывает вправо и вниз. Положительная Z координаты точки на экран. Файл XAML позволяет выбрать отрицательные значения Z, но это только в том случае, чтобы можно было видеть что произойдет: по сути, отрицательных координат Z вызвать света для указания выходит из экрана. Сведения обо всех проблемах других затем небольшой отрицательные значения эффекта освещения перестает работать.

`surfaceScale` Аргумент могут варьироваться от – 1 до 1. (Выше или ниже значения имеют не влияет). Это относительные значения по оси Z, указывающих смещение графического объекта (в данном случае это текстовая строка) из области холста. Используйте отрицательные значения для вызова текстовую строку выше области полотна и положительные значения мгновенное его на холст.

`lightConstant` Значение должно быть положительным. (Программа позволяет отрицательные значения, чтобы можно было видеть, что они смогут нанести эффект, перестают работать.) Более высокие значения привести к более интенсивного света.

Эти факторы могут сбалансированы для получения выступающая действует, если `surfaceScale` является отрицательным (как в iOS и Android снимки экрана) и Утопленный действует, если `surfaceScale` положительно, как с экрана UWP справа:

[![Отдаленных света эксперимента](image-filters-images/DistantLightExperiment.png "отдаленных света эксперимента")](image-filters-images/DistantLightExperiment-Large.png#lightbox)

Снимок экрана Android имеет значение Z 0, означающее, что источник света только указывает вниз и вправо. Не освещаются в фоновом режиме и не освещаются в рабочей области со строкой текста либо. Свет касается только границы текста очень нелегко выявить эффект.

Альтернативный подход к текст с эффектом приподнятости и Утопленный приведенного ранее в этой статье [перевод преобразования](../transforms/translate.md): текстовая строка отображается дважды с различными цветами, которые смещения немного друг от друга.

## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
