---
title: Мозаичное заполнение точечного рисунка SkiaSharp
description: Мозаичного заполнения области с помощью точечных рисунков, повторяться по горизонтали и вертикали.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 9ED14E07-4DC8-4B03-8A33-772838BF51EA
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 5bd063f82cc1d09c6b2e9100429889a23a2eda7f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111588"
---
# <a name="skiasharp-bitmap-tiling"></a>Мозаичное заполнение точечного рисунка SkiaSharp

Как вы уже видели в двух предыдущих статьях, [ `SKShader` ](xref:SkiaSharp.SKShader) класс создает линейные и циклическая градиенты. Эта статья посвящена `SKShader` объект, который использует растровое изображение для заполнения области. Битовая карта может повторяться по горизонтали и вертикали, либо в исходной ориентации или в качестве альтернативы перевернуто по горизонтали и вертикали. Отражение позволяет избежать перебоям в работе между плитки:

![Пример мозаичного заполнения Bitmap](bitmap-tiling-images/BitmapTilingSample.png "мозаичное заполнение пример растрового изображения")

Статический [ `SKShader.CreateBitmap` ](xref:SkiaSharp.SKShader.CreateBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKShaderTileMode,SkiaSharp.SKShaderTileMode)) имеет метод, который создает этот шейдер `SKBitmap` параметр и два члена [ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode) перечисления:

```csharp
public static SKShader CreateBitmap (SKBitmap src, SKShaderTileMode tmx, SKShaderTileMode tmy)
```

Два параметра указывают режимов, используемых для мозаичного заполнения горизонтальной и вертикальной мозаичное заполнение. Это то же самое `SKShaderTileMode` перечисления, который используется с методами градиента.

Объект [ `CreateBitmap` ](xref:SkiaSharp.SKShader.CreateBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKShaderTileMode,SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) перегруженная версия включает `SKMatrix` аргумент провести преобразование мозаичной растровых изображений:

```csharp
public static SKShader CreateBitmap (SKBitmap src, SKShaderTileMode tmx, SKShaderTileMode tmy, SKMatrix localMatrix)
```

Эта статья содержит несколько примеров использования этого преобразования матрицы с помощью мозаичной растровые изображения.

## <a name="exploring-the-tile-modes"></a>Изучение мозаичные режимы

Первая программа в **мозаичное заполнение растрового изображения** раздел **построители текстуры и другие эффекты** странице [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) образца демонстрируется действие двух `SKShaderTileMode` аргументы. **Режимы перевернуть растрового изображения мозаики** файл XAML создает экземпляр `SKCanvasView` и два `Picker` представления, которые позволяют выбрать `SKShaderTilerMode` значение для горизонтальных и вертикальных мозаичное заполнение. Обратите внимание, что массив `SKShaderTileMode` члены, определенные в `Resources` разделе:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.BitmapTileFlipModesPage"
             Title="Bitmap Tile Flip Modes">

    <ContentPage.Resources>
        <x:Array x:Key="tileModes"
                 Type="{x:Type skia:SKShaderTileMode}">
            <x:Static Member="skia:SKShaderTileMode.Clamp" />
            <x:Static Member="skia:SKShaderTileMode.Repeat" />
            <x:Static Member="skia:SKShaderTileMode.Mirror" />
        </x:Array>
    </ContentPage.Resources>

    <StackLayout>
        <skiaforms:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="xModePicker"
                Title="Tile X Mode"
                Margin="10, 0"
                ItemsSource="{StaticResource tileModes}"
                SelectedIndex="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged" />

        <Picker x:Name="yModePicker"
                Title="Tile Y Mode"
                Margin="10, 10"
                ItemsSource="{StaticResource tileModes}"
                SelectedIndex="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged" />

    </StackLayout>
</ContentPage>
```

Конструктор файла кода загружается в ресурса точечного рисунка, показывающий monkey сидите. Сначала обрезает образ с помощью [ `ExtractSubset` ](xref:SkiaSharp.SKBitmap.ExtractSubset(SkiaSharp.SKBitmap,SkiaSharp.SKRectI)) метод `SKBitmap` таким образом, head и футов касаются краев точечного рисунка. Затем конструктор использует [ `Resize` ](xref:SkiaSharp.SKBitmap.Resize(SkiaSharp.SKImageInfo,SkiaSharp.SKBitmapResizeMethod)) метод для создания другого точечного рисунка половины размера. Эти изменения делают точечного рисунка, немного больше подходит для мозаичного заполнения:

```csharp
public partial class BitmapTileFlipModesPage : ContentPage
{
    SKBitmap bitmap;

    public BitmapTileFlipModesPage ()
    {
        InitializeComponent ();

        SKBitmap origBitmap = BitmapExtensions.LoadBitmapResource(
            GetType(), "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

        // Define cropping rect
        SKRectI cropRect = new SKRectI(5, 27, 296, 260);

        // Get the cropped bitmap
        SKBitmap croppedBitmap = new SKBitmap(cropRect.Width, cropRect.Height);
        origBitmap.ExtractSubset(croppedBitmap, cropRect);

        // Resize to half the width and height
        SKImageInfo info = new SKImageInfo(cropRect.Width / 2, cropRect.Height / 2);
        bitmap = croppedBitmap.Resize(info, SKBitmapResizeMethod.Box);
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Get tile modes from Pickers
        SKShaderTileMode xTileMode =
            (SKShaderTileMode)(xModePicker.SelectedIndex == -1 ?
                                        0 : xModePicker.SelectedItem);
        SKShaderTileMode yTileMode =
            (SKShaderTileMode)(yModePicker.SelectedIndex == -1 ?
                                        0 : yModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateBitmap(bitmap, xTileMode, yTileMode);
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

`PaintSurface` Обработчик получает `SKShaderTileMode` параметры из двух `Picker` представлений и создает `SKShader` объекта на основе точечного рисунка и эти два значения. Этот шейдер используется для заполнения полотна.

[![Режимы Переворачивающейся плитки для точечных рисунков](bitmap-tiling-images/BitmapTileFlipModes.png "режимы Переворачивающейся плитки для точечных рисунков")](bitmap-tiling-images/BitmapTileFlipModes-Large.png#lightbox)

На экране iOS слева показывает эффект от значения по умолчанию `SKShaderTileMode.Clamp`. Растровое изображение размещается в левом верхнем углу. Ниже точечного рисунка нижняя строка пикселей повторяется вниз. В правой части точечного рисунка этого не происходило пикселей, которое повторяется вплоть для. В оставшейся части холста окрашивается темно-коричневой пиксель в правом нижнем углу точечного рисунка. Должно быть очевидно, `Clamp` практически никогда не используется с параметром мозаичное заполнение растрового изображения!

Экран Android в центре показывает результат `SKShaderTileMode.Repeat` обоих аргументов. Плитка будет повторяться по горизонтали и вертикали. Универсальная платформа Windows экрана показывает `SKShaderTileMode.Mirror`. Плитки повторить, но можно также перевернуто по горизонтали и вертикали. Этот параметр удобен тем что существуют не перебоям в работе между плитки.

Имейте в виду, что можно использовать различные параметры для повторения горизонтальный и вертикальный. Можно указать `SKShaderTileMode.Mirror` как второй аргумент `CreateBitmap` , но `SKShaderTileMode.Repeat` качестве третьего аргумента. В каждой строке кодеры, по-прежнему переключайтесь между обычный образ и Зеркальное изображение, но ни один из кодеры ногами.

## <a name="patterned-backgrounds"></a>Узора фона

Мозаичное заполнение растрового изображения обычно используется для создания узора фона из относительно небольшой растрового изображения. Классическим примером является упираетесь в стену.

**Алгоритмического Упираетесь в стену** страница создает небольшое растровое изображение, похожий на всей модуля и две части модуля, разделенных Базовые составляющие. Так как в следующем примере также используется этот модуль, его создания, статический конструктор, сделан общим с помощью статического свойства:

```csharp
public class AlgorithmicBrickWallPage : ContentPage
{
    static AlgorithmicBrickWallPage()
    {
        const int brickWidth = 64;
        const int brickHeight = 24;
        const int morterThickness = 6;
        const int bitmapWidth = brickWidth + morterThickness;
        const int bitmapHeight = 2 * (brickHeight + morterThickness);

        SKBitmap bitmap = new SKBitmap(bitmapWidth, bitmapHeight);

        using (SKCanvas canvas = new SKCanvas(bitmap))
        using (SKPaint brickPaint = new SKPaint())
        {
            brickPaint.Color = new SKColor(0xB2, 0x22, 0x22);

            canvas.Clear(new SKColor(0xF0, 0xEA, 0xD6));
            canvas.DrawRect(new SKRect(morterThickness / 2,
                                       morterThickness / 2,
                                       morterThickness / 2 + brickWidth,
                                       morterThickness / 2 + brickHeight),
                                       brickPaint);

            int ySecondBrick = 3 * morterThickness / 2 + brickHeight;

            canvas.DrawRect(new SKRect(0,
                                       ySecondBrick,
                                       bitmapWidth / 2 - morterThickness / 2,
                                       ySecondBrick + brickHeight),
                                       brickPaint);

            canvas.DrawRect(new SKRect(bitmapWidth / 2 + morterThickness / 2,
                                       ySecondBrick,
                                       bitmapWidth,
                                       ySecondBrick + brickHeight),
                                       brickPaint);
        }

        // Save as public property for other programs
        BrickWallTile = bitmap;
    }

    public static SKBitmap BrickWallTile { private set; get; }
    ···
}
```

Получающийся точечный рисунок — 70 пикселей в ширину и 60 пикселей:

![Алгоритмического модуля Wall плитки](bitmap-tiling-images/AlgorithmicBrickWallTile.png "алгоритмического модуля Wall плитки")

Остальная часть **алгоритмического Упираетесь в стену** страница создает `SKShader` объект, повторяющийся это изображение по горизонтали и вертикали:

```csharp
public class AlgorithmicBrickWallPage : ContentPage
{
    ···
    public AlgorithmicBrickWallPage ()
    {
        Title = "Algorithmic Brick Wall";

        // Create SKCanvasView
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
            // Create bitmap tiling
            paint.Shader = SKShader.CreateBitmap(BrickWallTile,
                                                 SKShaderTileMode.Repeat,
                                                 SKShaderTileMode.Repeat);
            // Draw background
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Ниже приведен результат.

[![Алгоритмического Упираетесь в стену](bitmap-tiling-images/AlgorithmicBrickWall.png "алгоритмического Упираетесь в стену")](bitmap-tiling-images/AlgorithmicBrickWall-Large.png#lightbox)

Вы можете что-нибудь немного более реалистичной. В этом случае можно воспользоваться фотографии и фактическое упираетесь в стену и затем обрезать. Этот рисунок — 300 пикселей в ширину и 150 пикселей:

![Граница Wall плитки](bitmap-tiling-images/BrickWallTile.jpg "модуля Wall плитки")

Это растровое изображение используется в **фотографический Упираетесь в стену** страницы:

```csharp
public class PhotographicBrickWallPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(PhotographicBrickWallPage),
                        "SkiaSharpFormsDemos.Media.BrickWallTile.jpg");

    public PhotographicBrickWallPage()
    {
        Title = "Photographic Brick Wall";

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
            // Create bitmap tiling
            paint.Shader = SKShader.CreateBitmap(bitmap,
                                                 SKShaderTileMode.Mirror,
                                                 SKShaderTileMode.Mirror);
            // Draw background
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Обратите внимание, что `SKShaderTileMode` аргументы `CreateBitmap` оба `Mirror`. Этот параметр обычно необходим при использовании плиток, созданных из образов в реальных условиях. Зеркальное отображение плитки позволяет избежать перебоям в работе:

[![Фотографический Упираетесь в стену](bitmap-tiling-images/PhotographicBrickWall.png "фотографический Упираетесь в стену")](bitmap-tiling-images/PhotographicBrickWall-Large.png#lightbox)

Некоторые действия, необходимые для получения подходящих растрового изображения для мозаичного элемента. Это не работает очень хорошо, так как более темные модуля, выделяется среди слишком много. Он регулярно отображается в повторных образов, отображая тот факт, что упираетесь в стену был создан из небольших растрового изображения.

**Мультимедиа** папке [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) образец также входит этот образ камень стене:

![Плитка Wall камне](bitmap-tiling-images/StoneWallTile.jpg "камне Wall плитки")

Тем не менее исходного растрового изображения немного слишком велик для плитки. Их можно изменять, но `SKShader.CreateBitmap` метод можно также изменить размер плитки, применяя преобразование к нему. Этот вариант рассматривается в **камне стены** страницы:

```csharp
public class StoneWallPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(StoneWallPage),
                        "SkiaSharpFormsDemos.Media.StoneWallTile.jpg");

    public StoneWallPage()
    {
        Title = "Stone Wall";

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
            // Create scale transform
            SKMatrix matrix = SKMatrix.MakeScale(0.5f, 0.5f);

            // Create bitmap tiling
            paint.Shader = SKShader.CreateBitmap(bitmap,
                                                 SKShaderTileMode.Mirror,
                                                 SKShaderTileMode.Mirror,
                                                 matrix);
            // Draw background
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

`SKMatrix` Создается значение для масштабирования изображения половины исходный размер:

[![Выстрелом стену](bitmap-tiling-images/StoneWall.png "Выстрелом по часам")](bitmap-tiling-images/StoneWall-Large.png#lightbox)

Выполняет преобразование операций исходное растровое изображение, используемое в `CreateBitmap` метод? Или преобразовать результирующий массив плитки? 

Простой способ ответить на этот вопрос, можно включить поворот в процессе преобразования.

```csharp
SKMatrix matrix = SKMatrix.MakeScale(0.5f, 0.5f);
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeRotationDegrees(15));
```

Если преобразование применяется к отдельным плитки, затем осуществляется изображению повторяющиеся плитки и результат будет содержать много перебоям в работе. Но это очевидно из на этом снимке экрана, что преобразуется составной массив плиток:

[![Выстрелом по часам (повернутый)](bitmap-tiling-images/StoneWallRotated.png "Выстрелом по часам (повернутый)")](bitmap-tiling-images/StoneWallRotated-Large.png#lightbox)

В разделе [ **плитку выравнивание**](#tile-alignment), вы увидите пример преобразования переноса к шейдера.

Автономной [ **часы Cat** ](https://developer.xamarin.com/samples/xamarin-forms/CatClock) пример (не является частью **SkiaSharpFormsDemos**) имитирует фона древесина детализации помощи дробления растрового изображения, в соответствии с этим растровым изображением square 240 пикселей:

![Деревянные детализации](bitmap-tiling-images/WoodGrain.png "деревянные детализации")

Это фотографию обшивка floor. `SKShaderTileMode.Mirror` Параметр позволяет отобразить его как гораздо большего размера области дерева:

[![CAT часы](bitmap-tiling-images/CatClock.png "Cat часов")](bitmap-tiling-images/CatClock-Large.png#lightbox)

## <a name="tile-alignment"></a>Выравнивание плитки

Все примеры, приведенные в данный момент использовали шейдера, созданные `SKShader.CreateBitmap` чтобы охватить весь холст. В большинстве случаев вы будете использовать мозаичное заполнение точечного рисунка для хранения небольших областей или (более редко) для заполнения толстой линии. Ниже приведен фотографический модуля wall плитки, используемый для меньшего размера прямоугольника.

[![Плитку выравнивание](bitmap-tiling-images/TileAlignment.png "плитку выравнивания")](bitmap-tiling-images/TileAlignment-Large.png#lightbox)

Это может выглядеть нормально, или нет. Возможно, вы беспокоить, что шаблон мозаики не начинается с полного модуля в левом верхнем углу прямоугольника. Том, что шейдеры выравниваются с canvas и не они Декорирование графического объекта.

Исправление довольно простое. Создание `SKMatrix` значение, основанное на преобразованию. Преобразование эффективно сдвигает шаблон заполнения кнопку мыши, в котором левом верхнем углу плитки, чтобы быть выровнены. Этот подход демонстрируется в **плитки выравнивание** странице, создавшего образ, невыровненных элементов выше:

```csharp
public class TileAlignmentPage : ContentPage
{
    bool isAligned;

    public TileAlignmentPage()
    {
        Title = "Tile Alignment";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;

        // Add tap handler
        TapGestureRecognizer tap = new TapGestureRecognizer();
        tap.Tapped += (sender, args) =>
        {
            isAligned ^= true;
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

        using (SKPaint paint = new SKPaint())
        {
            SKRect rect = new SKRect(info.Width / 7,
                                     info.Height / 7,
                                     6 * info.Width / 7,
                                     6 * info.Height / 7);

            // Get bitmap from other program
            SKBitmap bitmap = AlgorithmicBrickWallPage.BrickWallTile;

            // Create bitmap tiling
            if (!isAligned)
            {
                paint.Shader = SKShader.CreateBitmap(bitmap,
                                                     SKShaderTileMode.Repeat,
                                                     SKShaderTileMode.Repeat);
            }
            else
            {
                SKMatrix matrix = SKMatrix.MakeTranslation(rect.Left, rect.Top);

                paint.Shader = SKShader.CreateBitmap(bitmap,
                                                     SKShaderTileMode.Repeat,
                                                     SKShaderTileMode.Repeat,
                                                     matrix);
            }

            // Draw rectangle
            canvas.DrawRect(rect, paint);
        }
    }
}
```

**Плитки выравнивание** страница содержит `TapGestureRecognizer`. Коснитесь или щелкните на экране и командной строки для `SKShader.CreateBitmap` метод с `SKMatrix` аргумент. Это преобразование сдвигает шаблон таким образом, что верхнего левого угла полный модуля:

[![Плитку выравнивание касание](bitmap-tiling-images/TileAlignmentTapped.png "плитку выравнивание касание")](bitmap-tiling-images/TileAlignmentTapped-Large.png#lightbox)

Этот метод позволяет убедиться, что шаблон мозаики растровое изображение по центру в пределах области, которая рисует его. В **по центру плитки** странице `PaintSurface` обработчик сначала вычисляет координаты, как если бы он будет отображен один точечный рисунок в центре части холста. Затем он использует эти координаты для преобразования переноса для создания `SKShader.CreateBitmap`. Это преобразование сдвигает весь шаблон, чтобы плитки выравнивается по центру:

```csharp
public class CenteredTilesPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(CenteredTilesPage),
                        "SkiaSharpFormsDemos.Media.monkey.png");

    public CenteredTilesPage ()
    {
        Title = "Centered Tiles";

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

        // Find coordinates to center bitmap in canvas...
        float x = (info.Width - bitmap.Width) / 2f;
        float y = (info.Height - bitmap.Height) / 2f;

        using (SKPaint paint = new SKPaint())
        {
            // ... but use them to create a translate transform
            SKMatrix matrix = SKMatrix.MakeTranslation(x, y);
            paint.Shader = SKShader.CreateBitmap(bitmap, 
                                                 SKShaderTileMode.Repeat, 
                                                 SKShaderTileMode.Repeat, 
                                                 matrix);

            // Use that tiled bitmap pattern to fill a circle
            canvas.DrawCircle(info.Rect.MidX, info.Rect.MidY,
                              Math.Min(info.Width, info.Height) / 2,
                              paint);
        }
    }
}
```

`PaintSurface` Завершении работы обработчика, нарисовав круг в центре части холста. Очевидно, что одна плиток точно в центре круга, а остальные упорядочены в шаблоне симметричный:

[![По центру плитки](bitmap-tiling-images/CenteredTiles.png "по центру плитки")](bitmap-tiling-images/CenteredTiles-Large.png#lightbox)

Фактически другой центровки подход немного проще. Вместо создания преобразования переноса, которое позволяет поместить плитку в центре, можно центрировать углу шаблон заполнения. В `SKMatrix.MakeTranslation` вызвать, используйте аргументы для центра холста:

```csharp
SKMatrix matrix = SKMatrix.MakeTranslation(info.Rect.MidX, info.Rect.MidY);
```

Шаблон по-прежнему выровненные по центру и симметричное, но плитки не находится в центре:

[![По центру альтернативного плитки](bitmap-tiling-images/CenteredTilesAlternate.png "по центру альтернативного плитки")](bitmap-tiling-images/CenteredTilesAlternate-Large.png#lightbox)

## <a name="simplification-through-rotation"></a>Упрощение через поворота

Иногда с помощью преобразования вращения в `SKShader.CreateBitmap` метод можно упростить плитку растрового изображения. Это становится очевидным, при попытке определить плитки для компоновки цепочки барьер. **ChainLinkTile.cs** файл создает плитки, показано ниже (с Розовый фон для большей ясности):

![Плитка жесткой связи цепочки](bitmap-tiling-images/HardChainLinkTile.png "плитки цепочки жесткой связи")

Элемент должен включать две ссылки, таким образом, чтобы код делит плитки на четырех квадрантов. Квадрантах верхнего левого и правого нижнего одинаковы, но не выполнены. Проводов имеют небольшой деления, которые должны быть обработаны с некоторых дополнительных рисования в верхний правый и левый нижний квадрантах. Файл, который выполняет всю эту работу — 174 строк.

Дело в том станет намного проще для создания этой плитки:

![Проще плитки компоновки цепочки](bitmap-tiling-images/EasierChainLinkTile.png "проще компоновки цепочки плитки")

Если шейдер растрового изображения плитки Повернуть на 90 градусов, визуальные элементы являются почти одинакова.

Код для создания плитки проще компоновки цепочки является частью **плитку компоновки цепочки** страницы. Конструктор определяет размер мозаики, в зависимости от типа устройства под управлением программы, а затем вызывает `CreateChainLinkTile`, который рисует изображения на точечный рисунок с помощью строки, пути и шейдеры градиента:

```csharp
public class ChainLinkFencePage : ContentPage
{
    ···
    SKBitmap tileBitmap;

    public ChainLinkFencePage ()
    {
        Title = "Chain-Link Fence";

        // Create bitmap for chain-link tiling
        int tileSize = Device.Idiom == TargetIdiom.Desktop ? 64 : 128;
        tileBitmap = CreateChainLinkTile(tileSize);

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    SKBitmap CreateChainLinkTile(int tileSize)
    {
        tileBitmap = new SKBitmap(tileSize, tileSize);
        float wireThickness = tileSize / 12f;

        using (SKCanvas canvas = new SKCanvas(tileBitmap))
        using (SKPaint paint = new SKPaint())
        {
            canvas.Clear();
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = wireThickness;
            paint.IsAntialias = true;

            // Draw straight wires first
            paint.Shader = SKShader.CreateLinearGradient(new SKPoint(0, 0),
                                                         new SKPoint(0, tileSize),
                                                         new SKColor[] { SKColors.Silver, SKColors.Black },
                                                         new float[] { 0.4f, 0.6f },
                                                         SKShaderTileMode.Clamp);

            canvas.DrawLine(0, tileSize / 2,
                            tileSize / 2, tileSize / 2 - wireThickness / 2, paint);

            canvas.DrawLine(tileSize, tileSize / 2,
                            tileSize / 2, tileSize / 2 + wireThickness / 2, paint);

            // Draw curved wires
            using (SKPath path = new SKPath())
            {
                path.MoveTo(tileSize / 2, 0);
                path.LineTo(tileSize / 2 - wireThickness / 2, tileSize / 2);
                path.ArcTo(wireThickness / 2, wireThickness / 2,
                           0,
                           SKPathArcSize.Small,
                           SKPathDirection.CounterClockwise,
                           tileSize / 2, tileSize / 2 + wireThickness / 2);

                paint.Shader = SKShader.CreateLinearGradient(new SKPoint(0, 0),
                                                             new SKPoint(0, tileSize),
                                                             new SKColor[] { SKColors.Silver, SKColors.Black },
                                                             null,
                                                             SKShaderTileMode.Clamp);
                canvas.DrawPath(path, paint);

                path.Reset();
                path.MoveTo(tileSize / 2, tileSize);
                path.LineTo(tileSize / 2 + wireThickness / 2, tileSize / 2);
                path.ArcTo(wireThickness / 2, wireThickness / 2,
                           0,
                           SKPathArcSize.Small,
                           SKPathDirection.CounterClockwise,
                           tileSize / 2, tileSize / 2 - wireThickness / 2);

                paint.Shader = SKShader.CreateLinearGradient(new SKPoint(0, 0),
                                                             new SKPoint(0, tileSize),
                                                             new SKColor[] { SKColors.White, SKColors.Silver },
                                                             null,
                                                             SKShaderTileMode.Clamp);
                canvas.DrawPath(path, paint);
            }
            return tileBitmap;
        }
    }
    ···
}
```

За исключением провода Плитка является прозрачным, означающее, которую можно отобразить поверх что-то еще. Программа загружает в один из ресурсов точечного рисунка, отображает его на холст, а затем выводит шейдера в верхней части:

```csharp
public class ChainLinkFencePage : ContentPage
{
    SKBitmap monkeyBitmap = BitmapExtensions.LoadBitmapResource(
        typeof(ChainLinkFencePage), "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");
    ···

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        canvas.DrawBitmap(monkeyBitmap, info.Rect, BitmapStretch.UniformToFill, 
                            BitmapAlignment.Center, BitmapAlignment.Start);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateBitmap(tileBitmap, 
                                                 SKShaderTileMode.Repeat,
                                                 SKShaderTileMode.Repeat,
                                                 SKMatrix.MakeRotationDegrees(45));
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Обратите внимание на то, что шейдер равен поворачивается на 45 градусов, поэтому его компонент ориентирован как барьер реальных цепочка:

[![Граница компоновки цепочки](bitmap-tiling-images/ChainLinkFence.png "граница компоновки цепочки")](bitmap-tiling-images/ChainLinkFence-Large.png#lightbox)

## <a name="animating-bitmap-tiles"></a>Анимации плитки растрового изображения

Это шаблон всей плитке точечного рисунка можно анимировать, анимация матрицы преобразования. Возможно, вам необходимо шаблоне для вертикального или горизонтального перемещения или оба. Это выполняется путем создания на основе координат сдвига преобразования.

Можно также для рисования с небольшими изображениями, или для работы биты пикселов точечного рисунка в размере 60 раз в секунду. Затем этот точечный рисунок можно использовать для заполнения, а весь шаблон заполнения может показаться анимировать. 

**Анимировано плитки точечного рисунка** страница демонстрирует этот подход. Битовая карта создается в виде поля должно быть квадрат 64 пикселя. Конструктор вызывает `DrawBitmap` присвоить ей исходный вид. Если `angle` поле является ноль (так как это при первом вызове метода), то Битовая карта содержит две строки, пересекающиеся как X. Строки выполняются достаточно длинным, всегда достигал к границе растрового изображения вне зависимости от `angle` значение: 

```csharp
public class AnimatedBitmapTilePage : ContentPage
{
    const int SIZE = 64;

    SKCanvasView canvasView;
    SKBitmap bitmap = new SKBitmap(SIZE, SIZE);
    float angle;
    ···

    public AnimatedBitmapTilePage ()
    {
        Title = "Animated Bitmap Tile";

        // Initialize bitmap prior to animation
        DrawBitmap();

        // Create SKCanvasView 
        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ···
    void DrawBitmap()
    {
        using (SKCanvas canvas = new SKCanvas(bitmap))
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = SIZE / 8;

            canvas.Clear();
            canvas.Translate(SIZE / 2, SIZE / 2);
            canvas.RotateDegrees(angle);
            canvas.DrawLine(-SIZE, -SIZE, SIZE, SIZE, paint);
            canvas.DrawLine(-SIZE, SIZE, SIZE, -SIZE, paint);
        }
    }
    ···
}
```

Непроизводительные анимации в `OnAppearing` и `OnDisappearing` переопределяет. `OnTimerTick` Анимирует метод `angle` значение от 0 до 360 градусов каждые 10 секунд для поворота на рисунке X внутри точечного рисунка:

```csharp
public class AnimatedBitmapTilePage : ContentPage
{
    ···
    // For animation
    bool isAnimating;
    Stopwatch stopwatch = new Stopwatch();
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
        const int duration = 10;     // seconds
        angle = (float)(360f * (stopwatch.Elapsed.TotalSeconds % duration) / duration);
        DrawBitmap();
        canvasView.InvalidateSurface();

        return isAnimating;
    }
    ···
}
```

Из-за симметрии на рисунке X, это так же, как поворот `angle` значение от 0 до 90 градусов каждые 2,5 секунды.

`PaintSurface` Обработчик создает шейдера из растрового изображения и использует объект paint для цвета весь холст:

```csharp
public class AnimatedBitmapTilePage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateBitmap(bitmap,
                                                 SKShaderTileMode.Mirror,
                                                 SKShaderTileMode.Mirror);
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

`SKShaderTileMode.Mirror` Параметры убедитесь, что вооружений «X» в каждый рисунок присоединена с Х в смежных точечные рисунки, чтобы создать общую анимированный шаблон, который кажется практически сложнее, чем простая анимация предлагаем:

[![Анимированные плитки растрового изображения](bitmap-tiling-images/AnimatedBitmapTile.png "анимировано плитки растрового изображения")](bitmap-tiling-images/AnimatedBitmapTile-Large.png#lightbox)

## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [CatClock (пример)](https://developer.xamarin.com/samples/xamarin-forms/CatClock/)
