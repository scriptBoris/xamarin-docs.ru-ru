---
title: Анимации точечных рисунков SkiaSharp
description: Узнайте, как выполнить анимацию растрового изображения, последовательно отображение ряда точечных рисунков и Подготовка к просмотру анимированные GIF-файлы.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 97142ADC-E2FD-418C-8A09-9C561AEE5BFD
author: davidbritch
ms.author: dabritch
ms.date: 07/12/2018
ms.openlocfilehash: 78fcbae8db70a83d7d0a643e0b27f575152e9515
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112563"
---
# <a name="animating-skiasharp-bitmaps"></a>Анимации точечных рисунков SkiaSharp

Приложения, которые обычно анимация графики SkiaSharp вызывают `InvalidateSurface` на `SKCanvasView` по фиксированной ставке, часто каждые 16 миллисекунд. Стал недопустимым области активирует вызов `PaintSurface` обработчик для повторной отрисовки поверхности отображения. Как визуальные элементы перерисовываются 60 раз в секунду, они отображаются плавно анимировать.

Тем не менее если графики слишком сложны для отображения в 16 миллисекунд, анимация может стать перебои управления. Программист может потребоваться уменьшить частоту обновления до 30 раз или 15 раз в секунду, но иногда этого недостаточно. Иногда графика — настолько сложны, что они просто не может быть отображен в режиме реального времени.

Одним из решений является заранее подготовить для анимации с визуализации отдельных кадров анимации на ряд точечных рисунков. Для отображения анимации, необходим только для отображения растровых изображения последовательно 60 раз в секунду. 

Само собой, это потенциально много точечных рисунков, но это как большой бюджет 3D вносятся анимированных фильмов. 3D-графики гораздо слишком сложны для отображения в режиме реального времени. Требуется много времени обработки для отрисовки каждого кадра. Что видят при просмотре фильма является по сути ряд точечных рисунков.

Это можно сделать нечто подобное в SkiaSharp. В этой статье показано два типа анимации растрового изображения. Первый пример выполняется анимация Мандельброта:

![Пример анимации](animating-images/AnimatingSample.png "Пример анимации")

Во втором примере показано, как использовать SkiaSharp для подготовки к просмотру анимированный GIF-файл.

## <a name="bitmap-animation"></a>Анимации для точечных рисунков

Мандельброта визуально интересно, но computionally длинными. (Обсуждение Мандельброта и алгоритм, используемый здесь, см. в разделе [Глава 20 _Создание мобильных приложений с помощью Xamarin.Forms_ ](https://xamarin.azureedge.net/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf) начиная на странице 666. Со следующим описанием предполагается, что фоновые знания.)

[ **Анимации Мандельброта** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/MandelAnima/) образец использует анимации растрового изображения для эмуляции непрерывного изменения масштаба фиксированной точки Мандельброта. Увеличение следуют масштаба изображения, и затем цикл повторяется неограниченное время или до окончания работы программы. 

Программа подготавливает этой анимации путем создания до 50 растровые изображения, хранящиеся в локальном хранилище приложения. Каждый рисунок охватывает половины ширины и высоты на комплексной плоскости как предыдущих битовую карту. (В программе, эти точечные рисунки называются представляют целочисленный тип _уровни масштаба_.) Растровые изображения отображаются в последовательности. Масштабирование растрового изображения каждого анимируется для предоставления smooth последовательность от одного точечного рисунка в другой.

Как описано в главе 20 итоговую программу _Создание мобильных приложений с помощью Xamarin.Forms_, расчет Мандельброта в **анимации Мандельброта** — это асинхронный метод с восемью параметры. Параметры включают сложные центральной точки и ширину и высоту на комплексной плоскости, вокруг этой центральной точки. Следующие три параметра: ширина в пикселях и Высота растрового изображения должен быть создан и максимальное количество итераций для рекурсивных вычислений. `progress` Параметр используется для отображения хода выполнения для этого вычисления. `cancelToken` Параметр не используется в этой программе:

```csharp
static class Mandelbrot
{
    public static Task<BitmapInfo> CalculateAsync(Complex center,
                                                  double width, double height,
                                                  int pixelWidth, int pixelHeight,
                                                  int iterations,
                                                  IProgress<double> progress,
                                                  CancellationToken cancelToken)
    {
        return Task.Run(() =>
        {
            int[] iterationCounts = new int[pixelWidth * pixelHeight];
            int index = 0;

            for (int row = 0; row < pixelHeight; row++)
            {
                progress.Report((double)row / pixelHeight);
                cancelToken.ThrowIfCancellationRequested();

                double y = center.Imaginary + height / 2 - row * height / pixelHeight;

                for (int col = 0; col < pixelWidth; col++)
                {
                    double x = center.Real - width / 2 + col * width / pixelWidth;
                    Complex c = new Complex(x, y);

                    if ((c - new Complex(-1, 0)).Magnitude < 1.0 / 4)
                    {
                        iterationCounts[index++] = -1;
                    }
                    // http://www.reenigne.org/blog/algorithm-for-mandelbrot-cardioid/
                    else if (c.Magnitude * c.Magnitude * (8 * c.Magnitude * c.Magnitude - 3) < 3.0 / 32 - c.Real)
                    {
                        iterationCounts[index++] = -1;
                    }
                    else
                    {
                        Complex z = 0;
                        int iteration = 0;

                        do
                        {
                            z = z * z + c;
                            iteration++;
                        }
                        while (iteration < iterations && z.Magnitude < 2);

                        if (iteration == iterations)
                        {
                            iterationCounts[index++] = -1;
                        }
                        else
                        {
                            iterationCounts[index++] = iteration;
                        }
                    }
                }
            }
            return new BitmapInfo(pixelWidth, pixelHeight, iterationCounts);
        }, cancelToken);
    }
}
```

Метод возвращает объект типа `BitmapInfo` , предоставляющий сведения о создании точечного рисунка:

```csharp
class BitmapInfo
{
    public BitmapInfo(int pixelWidth, int pixelHeight, int[] iterationCounts)
    {
        PixelWidth = pixelWidth;
        PixelHeight = pixelHeight;
        IterationCounts = iterationCounts;
    }

    public int PixelWidth { private set; get; }

    public int PixelHeight { private set; get; }

    public int[] IterationCounts { private set; get; }
}
```

**Анимации Мандельброта** файл XAML включает две `Label` представления, `ProgressBar`и `Button` , а также `SKCanvasView`:

```csharp
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="MandelAnima.MainPage"
             Title="Mandelbrot Animation">

    <StackLayout>
        <Label x:Name="statusLabel"
               HorizontalTextAlignment="Center" />
        <ProgressBar x:Name="progressBar" />

        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <StackLayout Orientation="Horizontal"
                     Padding="5">
            <Label x:Name="storageLabel"
                   VerticalOptions="Center" />

            <Button x:Name="deleteButton"
                    Text="Delete All"
                    HorizontalOptions="EndAndExpand" 
                    Clicked="OnDeleteButtonClicked" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

Файл с выделенным кодом начинается с определения трех решающее значение константы, а также массив растровых изображений:

```csharp
public partial class MainPage : ContentPage
{
    const int COUNT = 10;           // The number of bitmaps in the animation.
                                    // This can go up to 50!

    const int BITMAP_SIZE = 1000;   // Program uses square bitmaps exclusively

    // Uncomment just one of these, or define your own
    static readonly Complex center = new Complex(-1.17651152924355, 0.298520986549558);
    //   static readonly Complex center = new Complex(-0.774693089457127, 0.124226621261617);
    //   static readonly Complex center = new Complex(-0.556624880053304, 0.634696788141351);

    SKBitmap[] bitmaps = new SKBitmap[COUNT];   // array of bitmaps
    ···
}
```

Рано или поздно вам, вероятно, захотите изменить `COUNT` значение 50, чтобы увидеть полный диапазон анимации. Значения выше 50 не используются. Вокруг уровень масштабирования — 48, или около того разрешение чисел с плавающей запятой двойной точности становится недостаточным для расчета Мандельброта. Эта проблема рассматривается на странице 684 _Создание мобильных приложений с помощью Xamarin.Forms_.

`center` Имеет большое значение. Это цель анимации масштабирования. Три значения в файле, используемые в трех окончательный снимки экрана в главе 20 _Создание мобильных приложений с помощью Xamarin.Forms_ на странице 684, но вы можете поэкспериментировать с программой в этой главе восстанавливался с одним из собственных значений. 

**Анимации Мандельброта** образец хранит их `COUNT` растровые изображения в хранилище локального приложения. Пятьдесят точечные рисунки требуют более чем 20 мегабайт памяти на устройстве, поэтому полезно знать, какой объем хранилища, занимаемого эти точечные рисунки, а в определенный момент может потребоваться удалить их все. Это назначение этих двух методов, в нижней части `MainPage` класса:

```csharp
public partial class MainPage : ContentPage
{
    ···
    void TallyBitmapSizes()
    {
        long fileSize = 0;

        foreach (string filename in Directory.EnumerateFiles(FolderPath()))
        {
            fileSize += new FileInfo(filename).Length;
        }

        storageLabel.Text = $"Total storage: {fileSize:N0} bytes";
    }

    void OnDeleteButtonClicked(object sender, EventArgs args)
    {
        foreach (string filepath in Directory.EnumerateFiles(FolderPath()))
        {
            File.Delete(filepath);
        }

        TallyBitmapSizes();
    }
}
```

Точечные рисунки в локальном хранилище можно удалить, пока программа выполняет анимацию этих же растровые изображения, так как программа сохраняет их в памяти. Но следующий раз при запуске программы, его нужно будет заново создать точечные рисунки.

Внедрить растровые изображения, хранящиеся в хранилище локального приложения `center` значение в их именах файлов, поэтому, если вы измените `center` , существующие битовые карты не будет заменена в хранилище и будет занимать пространство.

Ниже приведены методы, `MainPage` использует для создания имен файлов, а также через `MakePixel` для определения пиксельное значение на основании цветовых компонентов:

```csharp
public partial class MainPage : ContentPage
{
    ···
    // File path for storing each bitmap in local storage
    string FolderPath() => 
        Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData);

    string FilePath(int zoomLevel) => 
        Path.Combine(FolderPath(),
                     String.Format("R{0}I{1}Z{2:D2}.png", center.Real, center.Imaginary, zoomLevel));

    // Form bitmap pixel for Rgba8888 format
    uint MakePixel(byte alpha, byte red, byte green, byte blue) =>
        (uint)((alpha << 24) | (blue << 16) | (green << 8) | red);
    ···
}
```

`zoomLevel` Параметр `FilePath` лежит в диапазоне от 0 до `COUNT` константы минус 1.

`MainPage` Конструктор вызывает `LoadAndStartAnimation` метод:

```csharp
public partial class MainPage : ContentPage
{
    ···
    public MainPage()
    {
        InitializeComponent();

        LoadAndStartAnimation();
    }
    ···
}
```

`LoadAndStartAnimation` Метод отвечает за доступ к локальному хранилищу приложения загрузить любые точечные рисунки, возможно, был создан при программа была запущена ранее. Он проходит по `zoomLevel` значения от 0 до `COUNT`. Если файл существует, он загружает его в `bitmaps` массива. В противном случае необходимо создать растровое изображение для конкретного `center` и `zoomLevel` значения путем вызова `Mandelbrot.CalculateAsync`. Этот метод получает результатов итерации для каждой точки, который этот метод преобразует в цвета:

```csharp
public partial class MainPage : ContentPage
{
    ···
    async void LoadAndStartAnimation()
    {
        // Show total bitmap storage
        TallyBitmapSizes();

        // Create progressReporter for async operation
        Progress<double> progressReporter =
            new Progress<double>((double progress) => progressBar.Progress = progress);

        // Create (unused) CancellationTokenSource for async operation
        CancellationTokenSource cancelTokenSource = new CancellationTokenSource();

        // Loop through all the zoom levels
        for (int zoomLevel = 0; zoomLevel < COUNT; zoomLevel++)
        {
            // If the file exists, load it
            if (File.Exists(FilePath(zoomLevel)))
            {
                statusLabel.Text = $"Loading bitmap for zoom level {zoomLevel}";

                using (Stream stream = File.OpenRead(FilePath(zoomLevel)))
                {
                    bitmaps[zoomLevel] = SKBitmap.Decode(stream);
                }
            }
            // Otherwise, create a new bitmap
            else
            {
                statusLabel.Text = $"Creating bitmap for zoom level {zoomLevel}";

                CancellationToken cancelToken = cancelTokenSource.Token;

                // Do the (generally lengthy) Mandelbrot calculation 
                BitmapInfo bitmapInfo =
                    await Mandelbrot.CalculateAsync(center,
                                                    4 / Math.Pow(2, zoomLevel),
                                                    4 / Math.Pow(2, zoomLevel),
                                                    BITMAP_SIZE, BITMAP_SIZE,
                                                    (int)Math.Pow(2, 10), progressReporter, cancelToken);

                // Create bitmap & get pointer to the pixel bits
                SKBitmap bitmap = new SKBitmap(BITMAP_SIZE, BITMAP_SIZE, SKColorType.Rgba8888, SKAlphaType.Opaque);
                IntPtr basePtr = bitmap.GetPixels();

                // Set pixel bits to color based on iteration count
                for (int row = 0; row < bitmap.Width; row++)
                    for (int col = 0; col < bitmap.Height; col++)
                    {
                        int iterationCount = bitmapInfo.IterationCounts[row * bitmap.Width + col];
                        uint pixel = 0xFF000000;            // black

                        if (iterationCount != -1)
                        {
                            double proportion = (iterationCount / 32.0) % 1;
                            byte red = 0, green = 0, blue = 0;

                            if (proportion < 0.5)
                            {
                                red = (byte)(255 * (1 - 2 * proportion));
                                blue = (byte)(255 * 2 * proportion);
                            }
                            else
                            {
                                proportion = 2 * (proportion - 0.5);
                                green = (byte)(255 * proportion);
                                blue = (byte)(255 * (1 - proportion));
                            }

                            pixel = MakePixel(0xFF, red, green, blue);
                        }

                        // Calculate pointer to pixel
                        IntPtr pixelPtr = basePtr + 4 * (row * bitmap.Width + col);

                        unsafe     // requires compiling with unsafe flag
                        {
                            *(uint*)pixelPtr.ToPointer() = pixel;
                        }
                    }

                // Save as PNG file
                SKData data = SKImage.FromBitmap(bitmap).Encode();

                try
                {
                    File.WriteAllBytes(FilePath(zoomLevel), data.ToArray());
                }
                catch
                {
                    // Probably out of space, but just ignore
                }

                // Store in array
                bitmaps[zoomLevel] = bitmap;

                // Show new bitmap sizes
                TallyBitmapSizes();
            }

            // Display the bitmap
            bitmapIndex = zoomLevel;
            canvasView.InvalidateSurface();
        }

        // Now start the animation
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }
    ···
}
```

Обратите внимание на то, что программа сохраняет эти точечные рисунки в хранилище локального приложения, а не в библиотеке фотографий устройства. Библиотека .NET Standard 2.0 позволяет создавать с помощью привычного `File.OpenRead` и `File.WriteAllBytes` методы для выполнения этой задачи.

После создания или загрузки в память все битовые карты, этот метод начинает `Stopwatch` и вызывает `Device.StartTimer`. `OnTimerTick` Каждые 16 миллисекунд вызывается метод.

`OnTimerTick` Вычисляет `time` значение в миллисекундах, в диапазоне от 0 до 6000 раз `COUNT`, который apportions шесть секунд для отображения каждого растрового изображения. `progress` Значение использует `Math.Sin` следующие значения и создать переход анимацию, которая будет выполняться медленнее в начале цикла, и медленнее в конце, так как он меняет направление. 

`progress` Значение лежит в диапазоне от 0 до `COUNT`. Это означает, что целой части `progress` является индексом в `bitmaps` массива, а дробная часть параметра `progress` указывает значение масштаба для этого конкретного растрового изображения. Эти значения хранятся в `bitmapIndex` и `bitmapProgress` полей и их отображения с `Label` и `Slider` в файле XAML. `SKCanvasView` Становится недействительным, чтобы обновить список точечного рисунка:

```csharp
public partial class MainPage : ContentPage
{
    ···
    Stopwatch stopwatch = new Stopwatch();      // for the animation
    int bitmapIndex;
    double bitmapProgress = 0;
    ···
    bool OnTimerTick()
    {
        int cycle = 6000 * COUNT;       // total cycle length in milliseconds

        // Time in milliseconds from 0 to cycle
        int time = (int)(stopwatch.ElapsedMilliseconds % cycle);

        // Make it sinusoidal, including bitmap index and gradation between bitmaps
        double progress = COUNT * 0.5 * (1 + Math.Sin(2 * Math.PI * time / cycle - Math.PI / 2));

        // These are the field values that the PaintSurface handler uses
        bitmapIndex = (int)progress;
        bitmapProgress = progress - bitmapIndex;

        // It doesn't often happen that we get up to COUNT, but an exception would be raised
        if (bitmapIndex < COUNT)
        {
            // Show progress in UI
            statusLabel.Text = $"Displaying bitmap for zoom level {bitmapIndex}";
            progressBar.Progress = bitmapProgress;

            // Update the canvas
            canvasView.InvalidateSurface();
        }

        return true;
    }
    ···
}
```

Наконец `PaintSurface` обработчик `SKCanvasView` вычисляет прямоугольник назначения для отображения растрового изображения, как можно большего размера, сохраняя пропорции. На основе исходного прямоугольника `bitmapProgress` значение. `fraction` Здесь вычисляется значение лежит в диапазоне от 0, если `bitmapProgress` значение 0, чтобы отобразить весь точечный рисунок, 0,25, если `bitmapProgress` -1 для отображения половины ширины и высоты точечного рисунка, эффективно увеличение:

```csharp
public partial class MainPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        if (bitmaps[bitmapIndex] != null)
        {
            // Determine destination rect as square in canvas
            int dimension = Math.Min(info.Width, info.Height);
            float x = (info.Width - dimension) / 2;
            float y = (info.Height - dimension) / 2;
            SKRect destRect = new SKRect(x, y, x + dimension, y + dimension);

            // Calculate source rectangle based on fraction:
            //  bitmapProgress == 0: full bitmap
            //  bitmapProgress == 1: half of length and width of bitmap
            float fraction = 0.5f * (1 - (float)Math.Pow(2, -bitmapProgress));
            SKBitmap bitmap = bitmaps[bitmapIndex];
            int width = bitmap.Width;
            int height = bitmap.Height;
            SKRect sourceRect = new SKRect(fraction * width, fraction * height, 
                                           (1 - fraction) * width, (1 - fraction) * height);

            // Display the bitmap
            canvas.DrawBitmap(bitmap, sourceRect, destRect);
        }
    }
    ···
}
```

Вот ее запуск на всех трех платформах:

[![Анимация Мандельброта](animating-images/MandelbrotAnimation.png "Мандельброта анимации")](animating-images/MandelbrotAnimation-Large.png#lightbox)

## <a name="gif-animation"></a>Анимированный GIF

Спецификация формата GIF (Graphics Interchange) включает компонент, позволяющий единый GIF-файл должен содержать несколько последовательных кадров сцены, который может быть отображен подряд, часто в цикле. Эти файлы называются _анимированных GIF_. Веб-браузеры могут играть анимированные изображения формата GIF и SkiaSharp позволяет приложению для извлечения из анимированный GIF-файл кадры и отобразить их последовательно.

[SkiaSharpFormsDemos](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) пример с ресурсом анимационный GIF с именем **Newtons_cradle_animation_book_2.gif** созданные DemonDeLuxe и загружается из [подставки Ньютона ](https://en.wikipedia.org/wiki/Newton%27s_cradle) страницы в Википедии. **Анимированный GIF** страницы включает файл XAML, который предоставляет эту информацию и создает экземпляр `SKCanvasView`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.AnimatedGifPage"
             Title="Animated GIF">

    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <skia:SKCanvasView x:Name="canvasView" 
                           Grid.Row="0"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Label Text="GIF file by DemonDeLuxe from Wikipedia Newton's Cradle page"
               Grid.Row="1"
               Margin="0, 5"
               HorizontalTextAlignment="Center" />
    </Grid>
</ContentPage>
```

Файл с выделенным кодом не обобщается для воспроизведения анимированный GIF-файл. Он игнорирует большая часть данных, который доступен, в частности, число повторений и просто играет анимационный GIF в цикле. 

Использование SkisSharp для извлечения кадры анимированный GIF-файл не документироваться в любой точке мира, поэтому описания приведенный ниже код — более подробные, чем обычно:

Декодирование анимированный GIF-файл в конструктор страницы происходит, но она требуется `Stream` использовать для создания объекта, ссылающегося на точечный рисунок `SKManagedStream` объекта и затем [ `SKCodec` ](xref:SkiaSharp.SKCodec) объекта. [ `FrameCount` ](xref:SkiaSharp.SKCodec.FrameCount) Свойство указывает количество кадров, составляющих анимации. 

Эти кадры в конечном итоге сохраняются как отдельные точечные рисунки, поэтому конструктор использует `FrameCount` выделить массив объектов типа `SKBitmap` а также два `int` массивы накопленных в течение каждого кадра и (для упрощения логики анимации) длительность.

[ `FrameInfo` ](xref:SkiaSharp.SKCodec.FrameInfo) Свойство `SKCodec` класс представляет собой массив [ `SKCodecFrameInfo` ](xref:SkiaSharp.SKCodecFrameInfo) значения, по одному для каждого кадра, но единственное, что эта программа использует из этой структуры является [ `Duration` ](xref:SkiaSharp.SKCodecFrameInfo.Duration) кадра в миллисекундах.

`SKCodec` Определяет свойство, именуемое [ `Info` ](xref:SkiaSharp.SKCodec.Info) типа [ `SKImageInfo` ](xref:SkiaSharp.SKImageInfo), но в этом `SKImageInfo` значение указывает (по крайней мере для этого образа), является тип цвета `SKColorType.Index8`, означающее, что Каждый пиксел является индексом в тип цвета. Чтобы избежать обращения к специалистам с цветовыми таблицами, программа использует [ `Width` ](xref:SkiaSharp.SKImageInfo.Width) и [ `Height` ](xref:SkiaSharp.SKImageInfo.Height) сведения из этой структуры для построения, он является владельцем полноцветного `ImageInfo` значение. Каждый `SKBitmap` создается на основе.

`GetPixels` Метод `SKBitmap` возвращает `IntPtr` ссылки на биты пикселов из точечного рисунка. Эти биты пикселов еще не задано. Что `IntPtr` передать в один из [ `GetPixels` ](xref:SkiaSharp.SKCodec.GetPixels(SkiaSharp.SKImageInfo,System.IntPtr,SkiaSharp.SKCodecOptions)) методы `SKCodec`. Этот метод копирует кадр GIF-файл в памяти ссылается `IntPtr`. [ `SKCodecOptions` ](xref:SkiaSharp.SKCodecOptions.%23ctor(System.Int32,System.Boolean)) Конструктор указывает номер кадра:

```csharp
public partial class AnimatedGifPage : ContentPage
{
    SKBitmap[] bitmaps;
    int[] durations;
    int[] accumulatedDurations;
    int totalDuration;
    ···

    public AnimatedGifPage ()
    {
        InitializeComponent ();

        string resourceID = "SkiaSharpFormsDemos.Media.Newtons_cradle_animation_book_2.gif";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        using (SKManagedStream skStream = new SKManagedStream(stream))
        using (SKCodec codec = SKCodec.Create(skStream))
        {
            // Get frame count and allocate bitmaps
            int frameCount = codec.FrameCount;
            bitmaps = new SKBitmap[frameCount];
            durations = new int[frameCount];
            accumulatedDurations = new int[frameCount];

            // Note: There's also a RepetitionCount property of SKCodec not used here

            // Loop through the frames
            for (int frame = 0; frame < frameCount; frame++)
            {
                // From the FrameInfo collection, get the duration of each frame
                durations[frame] = codec.FrameInfo[frame].Duration;

                // Create a full-color bitmap for each frame
                SKImageInfo imageInfo = code.new SKImageInfo(codec.Info.Width, codec.Info.Height);
                bitmaps[frame] = new SKBitmap(imageInfo);

                // Get the address of the pixels in that bitmap
                IntPtr pointer = bitmaps[frame].GetPixels();

                // Create an SKCodecOptions value to specify the frame
                SKCodecOptions codecOptions = new SKCodecOptions(frame, false);

                // Copy pixels from the frame into the bitmap
                codec.GetPixels(imageInfo, pointer, codecOptions);
            }

            // Sum up the total duration
            for (int frame = 0; frame < durations.Length; frame++)
            {
                totalDuration += durations[frame];
            }

            // Calculate the accumulated durations 
            for (int frame = 0; frame < durations.Length; frame++)
            {
                accumulatedDurations[frame] = durations[frame] + 
                    (frame == 0 ? 0 : accumulatedDurations[frame - 1]);
            }
        }
    }
    ···
}
```

Несмотря на `IntPtr` значение Нет `unsafe` код не требуется, так как `IntPtr` никогда не преобразуется в значение указателя C#.

После извлечения каждого кадра, конструктор итогов вверх продолжительность всех кадров, а затем инициализирует другой массив с накопленные длительности.

В оставшейся части файла кода, предназначенный для анимации. `Device.StartTimer` Метод используется для запуска таймера будет и `OnTimerTick` использует обратный вызов `Stopwatch` объектом, чтобы определить время в миллисекундах. Перебор массива накопленные длительности достаточно найти текущего кадра:

```csharp
public partial class AnimatedGifPage : ContentPage
{
    SKBitmap[] bitmaps;
    int[] durations;
    int[] accumulatedDurations;
    int totalDuration;

    Stopwatch stopwatch = new Stopwatch();
    bool isAnimating;

    int currentFrame;
    ···
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
        int msec = (int)(stopwatch.ElapsedMilliseconds % totalDuration);
        int frame = 0;

        // Find the frame based on the elapsed time
        for (frame = 0; frame < accumulatedDurations.Length; frame++)
        {
            if (msec < accumulatedDurations[frame])
            {
                break;
            }
        }

        // Save in a field and invalidate the SKCanvasView.
        if (currentFrame != frame)
        {
            currentFrame = frame;
            canvasView.InvalidateSurface();
        }

        return isAnimating;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Black);
            
        // Get the bitmap and center it
        SKBitmap bitmap = bitmaps[currentFrame];
        canvas.DrawBitmap(bitmap,info.Rect, BitmapStretch.Uniform);
    }
}
```

Каждый раз `currentframe` изменения переменных `SKCanvasView` становится недействительным, и отображается новый кадр:

[![Анимированный GIF](animating-images/AnimatedGif.png "анимированный GIF")](animating-images/AnimatedGif-Large.png#lightbox)

Само собой, необходимо запустить программу самостоятельно, чтобы просмотреть анимацию.

## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [Анимация "Мандельброт" (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/MandelAnima/)
