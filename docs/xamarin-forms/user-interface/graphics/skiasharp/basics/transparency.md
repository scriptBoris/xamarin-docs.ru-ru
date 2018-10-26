---
title: Прозрачность SkiaSharp
description: Используйте прозрачность для объединения нескольких объектов в одной сцене.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: B62F9487-C30E-4C63-BAB1-4C091FF50378
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: f99c5c2b60dec091e07ef11e62955de399d12164
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50132026"
---
# <a name="skiasharp-transparency"></a>Прозрачность SkiaSharp

Как вы убедились, [ `SKPaint` ](xref:SkiaSharp.SKPaint) класс включает [ `Color` ](xref:SkiaSharp.SKPaint.Color) свойство типа [ `SKColor` ](xref:SkiaSharp.SKColor). `SKColor` включает в себя альфа-канал, поэтому все, что можно цвет с `SKColor` значение может быть частично прозрачными. 

Некоторые прозрачности было продемонстрировано в [ **простая анимация в SkiaSharp** ](animation.md) статьи. В этой статье рассматриваются несколько глубже прозрачности для объединения нескольких объектов в одной сценой, это методика, которую иногда называют _наложения_. Более сложные методы наложения рассматриваются в статьях [ **шейдеры SkiaSharp** ](../effects/shaders/index.md) раздел.

При первом создании цвет с использованием четыре параметра можно задать уровень прозрачности [ `SKColor` ](xref:SkiaSharp.SKColor.%23ctor(System.Byte,System.Byte,System.Byte,System.Byte)) конструктор:

```csharp
SKColor (byte red, byte green, byte blue, byte alpha);
```

Альфа-значение 0 соответствует полной прозрачности и альфа-значение 0xFF полностью непрозрачен. Значения между этими крайними создавать частично прозрачные цвета.

Кроме того `SKColor` определяет удобное [ `WithAlpha` ](xref:SkiaSharp.SKColor.WithAlpha*) метод, который создает новый цвет из существующего цвета, но с указанным уровнем альфа-канала:

```csharp
SKColor halfTransparentBlue = SKColors.Blue.WithAlpha(0x80);
```

Демонстрируется использование частично прозрачный текста в **более кода** странице в [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) образца. Эта страница исчезает две текстовые строки и включив прозрачности в `SKColor` значения:

```csharp
public class CodeMoreCodePage : ContentPage
{
    SKCanvasView canvasView;
    bool isAnimating;
    Stopwatch stopwatch = new Stopwatch();
    double transparency;

    public CodeMoreCodePage ()
    {
        Title = "Code More Code";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();

        isAnimating = true;
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();

        stopwatch.Stop();
        isAnimating = false;
    }

    bool OnTimerTick()
    {
        const int duration = 5;     // seconds
        double progress = stopwatch.Elapsed.TotalSeconds % duration / duration;
        transparency = 0.5 * (1 + Math.Sin(progress * 2 * Math.PI));
        canvasView.InvalidateSurface();

        return isAnimating;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        const string TEXT1 = "CODE";
        const string TEXT2 = "MORE";

        using (SKPaint paint = new SKPaint())
        {
            // Set text width to fit in width of canvas
            paint.TextSize = 100;
            float textWidth = paint.MeasureText(TEXT1);
            paint.TextSize *= 0.9f * info.Width / textWidth;

            // Center first text string
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT1, ref textBounds);

            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * (1 - transparency)));
            canvas.DrawText(TEXT1, xText, yText, paint);

            // Center second text string
            textBounds = new SKRect();
            paint.MeasureText(TEXT2, ref textBounds);

            xText = info.Width / 2 - textBounds.MidX;
            yText = info.Height / 2 - textBounds.MidY;

            paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * transparency));
            canvas.DrawText(TEXT2, xText, yText, paint);
        }
    }
}
```

`transparency` Поле анимируется до изменяться от 0 до 1, а затем опять в переход такт. Первая текстовая строка отображается с альфа-значение рассчитывается путем вычитания `transparency` значение от 1:

```csharp
paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * (1 - transparency)));
```

[ `WithAlpha` ](xref:SkiaSharp.SKColor.WithAlpha*) Метод задает альфа-компонент на существующий цвет, являющийся здесь `SKColors.Blue`. Вторая текстовая строка использует альфа-значение вычисляется на основе `transparency` само значение:

```csharp
paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * transparency));
```

Анимация переключается между двумя словами, понуждая пользователя «больше кода» (или возможно запросе «дополнительные кода»):

[![Дополнительные код](transparency-images/CodeMoreCode.png "дополнительные код")](transparency-images/CodeMoreCode-Large.png#lightbox)


В предыдущей статье на [ **основы растрового изображения в SkiaSharp**](bitmaps.md), было продемонстрировано отображение точечных рисунков с помощью одного из [ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap*) методы `SKCanvas`. Все `DrawBitmap` методы включают в себя `SKPaint` объект в качестве последнего параметра. По умолчанию этот параметр имеет значение `null` и ее можно игнорировать. 

Кроме того, можно задать `Color` свойства данного объекта `SKPaint` объект для отображения растровое изображение со некоторый уровень прозрачности. Установка уровня прозрачности в `Color` свойство `SKPaint` позволяет точечные рисунки и исчезновения или исчезают одного точечного рисунка в другой. 

Демонстрируется прозрачности точечного рисунка в **исчезают точечного рисунка** страницы. Файл XAML создает экземпляр `SKCanvasView` и `Slider`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.BitmapDissolvePage"
             Title="Bitmap Dissolve">
    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="progressSlider"
                Margin="10"
                ValueChanged="OnSliderValueChanged" />
    </StackLayout>
</ContentPage>
```

Файл с выделенным кодом загружает два ресурса точечного рисунка. Эти точечные рисунки не имеют одинаковый размер, но они же соотношение сторон:

```csharp
public partial class BitmapDissolvePage : ContentPage
{
    SKBitmap bitmap1;
    SKBitmap bitmap2;

    public BitmapDissolvePage()
    {
        InitializeComponent();

        // Load two bitmaps
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(
                                "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg"))
        {
            bitmap1 = SKBitmap.Decode(stream);
        }
        using (Stream stream = assembly.GetManifestResourceStream(
                                "SkiaSharpFormsDemos.Media.FacePalm.jpg"))
        {
            bitmap2 = SKBitmap.Decode(stream);
        }
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

        // Find rectangle to fit bitmap
        float scale = Math.Min((float)info.Width / bitmap1.Width,
                                (float)info.Height / bitmap1.Height);
        SKRect rect = SKRect.Create(scale * bitmap1.Width,
                                    scale * bitmap1.Height);
        float x = (info.Width - rect.Width) / 2;
        float y = (info.Height - rect.Height) / 2;
        rect.Offset(x, y);

        // Get progress value from Slider
        float progress = (float)progressSlider.Value;

        // Display two bitmaps with transparency
        using (SKPaint paint = new SKPaint())
        {
            paint.Color = paint.Color.WithAlpha((byte)(0xFF * (1 - progress)));
            canvas.DrawBitmap(bitmap1, rect, paint);

            paint.Color = paint.Color.WithAlpha((byte)(0xFF * progress));
            canvas.DrawBitmap(bitmap2, rect, paint);
        }
    }
}
```

`Color` Свойство `SKPaint` присваивается два уровня взаимодополняющие альфа-канала для двух растровых изображений. При использовании `SKPaint` с растровыми изображениями, неважно, какие остальная часть `Color` значение. Имеет значение только альфа-канала. Приведенный здесь код просто вызывает `WithAlpha` метод на значение по умолчанию `Color` свойство.

Перемещение `Slider` растворяющееся между одного точечного рисунка, а другой:

[![Битовая карта Растворение](transparency-images/BitmapDissolve.png "Растворение для точечных рисунков")](transparency-images/BitmapDissolve-Large.png#lightbox)

В последние несколько статей вы узнали, как использовать SkiaSharp для рисования текста, круги, эллипсы, прямоугольников со скругленными углами и точечные рисунки. Следующим шагом является [строки и пути SkiaSharp](../paths/index.md) в которой вы узнаете, как для рисования линии в графический контур.

## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)