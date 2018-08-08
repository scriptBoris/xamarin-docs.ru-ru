---
title: Обрезка точечные рисунки SkiaSharp
description: Сведения об использовании SkiaSharp для проектирования пользовательского интерфейса для интерактивного описывающего прямоугольника обрезки.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 0A79AB27-C69F-4376-8FFE-FF46E4783F30
author: charlespetzold
ms.author: chape
ms.date: 07/17/2018
ms.openlocfilehash: 3dd9011d19e77f52d1fe89a37e4d992c23c72ab1
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615552"
---
# <a name="cropping-skiasharp-bitmaps"></a>Обрезка точечные рисунки SkiaSharp

[ **Создание и точечные рисунки SkiaSharp Рисование** ](drawing.md) статье описано, как `SKBitmap` объект может быть передан `SKCanvas` конструктор. Любой рисования методу, вызванному для причины, чтобы холст рисунок для отображения изображения на точечный рисунок. К ним методов рисования относятся `DrawBitmap`, что означает, что этот метод можно использовать передача всех или части одного точечного рисунка для другого точечного рисунка, возможно с помощью преобразований.

Этот метод можно использовать для обрезки точечный рисунок вызовом [ `DrawBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmap/p/SkiaSharp.SKBitmap/SkiaSharp.SKRect/SkiaSharp.SKRect/SkiaSharp.SKPaint/) метод с исходного и конечного прямоугольника:

```csharp
canvas.DrawBitmap(bitmap, sourceRect, destRect);
```

Тем не менее приложения, реализующие Обрезка часто предоставляют интерфейс для пользователю интерактивно выбирать прямоугольника обрезки:

![Обрезка образец](cropping-images/CroppingSample.png "Обрезка образца")

Эта статья посвящена этот интерфейс.

## <a name="encapsulating-the-cropping-rectangle"></a>Инкапсуляция прямоугольника обрезки

Это полезно для изоляции некоторых логик обрезки в классе с именем `CroppingRectangle`. Параметры конструктора включают максимальное прямоугольник, который обычно является размер растрового изображения, и обрезается, и необязательный пропорции. Конструктор сначала определяет исходного прямоугольника обрезки, что он делает открытый в `Rect` свойство типа `SKRect`. Этот исходного прямоугольника обрезки — это 80% от ширины и высоты прямоугольника точечного рисунка, но затем производится корректировка указываемое пропорции:

```csharp
class CroppingRectangle
{
    ···
    SKRect maxRect;             // generally the size of the bitmap
    float? aspectRatio;

    public CroppingRectangle(SKRect maxRect, float? aspectRatio = null)
    {
        this.maxRect = maxRect;
        this.aspectRatio = aspectRatio;

        // Set initial cropping rectangle
        Rect = new SKRect(0.9f * maxRect.Left + 0.1f * maxRect.Right,
                          0.9f * maxRect.Top + 0.1f * maxRect.Bottom,
                          0.1f * maxRect.Left + 0.9f * maxRect.Right,
                          0.1f * maxRect.Top + 0.9f * maxRect.Bottom);

        // Adjust for aspect ratio
        if (aspectRatio.HasValue)
        {
            SKRect rect = Rect;
            float aspect = aspectRatio.Value;

            if (rect.Width > aspect * rect.Height)
            {
                float width = aspect * rect.Height;
                rect.Left = (maxRect.Width - width) / 2;
                rect.Right = rect.Left + width;
            }
            else
            {
                float height = rect.Width / aspect;
                rect.Top = (maxRect.Height - height) / 2;
                rect.Bottom = rect.Top + height;
            }

            Rect = rect;
        }
    }
    
    public SKRect Rect { set; get; }
    ···
}
```

Один полезный фрагмент информации, `CroppingRectangle` также делает доступным представляет собой массив `SKPoint` значения, соответствующие четыре угла прямоугольника обрезки, а в том порядке, верхний левый, верхний правый, нижний правый, нижний левый:

```csharp
class CroppingRectangle
{
    ···
    public SKPoint[] Corners
    {
        get
        {
            return new SKPoint[]
            {
                new SKPoint(Rect.Left, Rect.Top),
                new SKPoint(Rect.Right, Rect.Top),
                new SKPoint(Rect.Right, Rect.Bottom),
                new SKPoint(Rect.Left, Rect.Bottom)
            };
        }
    }
    ···
}
```

Этот массив используется в следующий метод, который называется `HitTest`. `SKPoint` Параметр — это точка, соответствующий Прикосновение пальцем или мышью. Этот метод возвращает индекс (0, 1, 2 или 3) соответствующий угол, затронутых указатель мыши или палец в пределах расстояния, выданный `radius` параметр: 

```csharp
class CroppingRectangle
{
    ···
    public int HitTest(SKPoint point, float radius)
    {
        SKPoint[] corners = Corners;

        for (int index = 0; index < corners.Length; index++)
        {
            SKPoint diff = point - corners[index];
                
            if ((float)Math.Sqrt(diff.X * diff.X + diff.Y * diff.Y) < radius)
            {
                return index;
            }
        }

        return -1;
    }
    ···
}
```

Если точка касания или мышь не было в `radius` единиц любой угол, метод возвращает &ndash;1.

Последний метод в `CroppingRectangle` вызывается `MoveCorner`, который вызывается в ответ на касание, или перемещения мыши. Два параметра указывают индекс перемещаемого угла, и новое расположение, угла. В первой половине метод корректирует прямоугольника обрезки, исходя из нового расположения угла, но всегда в пределах `maxRect`, равным размеру растрового изображения. Эта логика также учитывает `MINIMUM` поля во избежание свертывание прямоугольника обрезки в nothing:

```csharp
class CroppingRectangle
{
    const float MINIMUM = 10;   // pixels width or height
    ···
    public void MoveCorner(int index, SKPoint point)
    {
        SKRect rect = Rect;

        switch (index)
        {
            case 0: // upper-left
                rect.Left = Math.Min(Math.Max(point.X, maxRect.Left), rect.Right - MINIMUM);
                rect.Top = Math.Min(Math.Max(point.Y, maxRect.Top), rect.Bottom - MINIMUM);
                break;

            case 1: // upper-right
                rect.Right = Math.Max(Math.Min(point.X, maxRect.Right), rect.Left + MINIMUM);
                rect.Top = Math.Min(Math.Max(point.Y, maxRect.Top), rect.Bottom - MINIMUM);
                break;

            case 2: // lower-right
                rect.Right = Math.Max(Math.Min(point.X, maxRect.Right), rect.Left + MINIMUM);
                rect.Bottom = Math.Max(Math.Min(point.Y, maxRect.Bottom), rect.Top + MINIMUM);
                break;

            case 3: // lower-left
                rect.Left = Math.Min(Math.Max(point.X, maxRect.Left), rect.Right - MINIMUM);
                rect.Bottom = Math.Max(Math.Min(point.Y, maxRect.Bottom), rect.Top + MINIMUM);
                break;
        }

        // Adjust for aspect ratio
        if (aspectRatio.HasValue)
        {
            float aspect = aspectRatio.Value;

            if (rect.Width > aspect * rect.Height)
            {
                float width = aspect * rect.Height;

                switch (index)
                {
                    case 0:
                    case 3: rect.Left = rect.Right - width; break;
                    case 1:
                    case 2: rect.Right = rect.Left + width; break;
                }
            }
            else
            {
                float height = rect.Width / aspect;

                switch (index)
                {
                    case 0:
                    case 1: rect.Top = rect.Bottom - height; break;
                    case 2:
                    case 3: rect.Bottom = rect.Top + height; break;
                }
            }
        }

        Rect = rect;
    }
}
```

Вторая часть метода сохраняющей пропорции необязательно.

Имейте в виду, что все, что в этом классе измеряется в пикселях.

## <a name="a-canvas-view-just-for-cropping"></a>Холст представления только для обрезки

`CroppingRectangle` Класс, вы увидели используется `PhotoCropperCanvasView` класс, который является производным от `SKCanvasView`. Этот класс отвечает за отображение точечный рисунок и прямоугольника обрезки, а также обработка событий мыши или сенсорного ввода для изменения прямоугольника обрезки.

`PhotoCropperCanvasView` Конструктор требует растрового изображения. Пропорции является необязательным. Конструктор создает объект типа `CroppingRectangle` на основе этого точечного рисунка и пропорции и сохраняет его в качестве поля:

```csharp
class PhotoCropperCanvasView : SKCanvasView
{
    ···
    SKBitmap bitmap;
    CroppingRectangle croppingRect;
    ···
    public PhotoCropperCanvasView(SKBitmap bitmap, float? aspectRatio = null)
    {
        this.bitmap = bitmap;

        SKRect bitmapRect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
        croppingRect = new CroppingRectangle(bitmapRect, aspectRatio);
        ···
    }
    ···
}
```

Так как этот класс является производным от `SKCanvasView`, ничего не нужно установить обработчик для `PaintSurface` событий. Вместо этого его можно переопределить его `OnPaintSurface` метод. Метод отображает точечный рисунок и использует несколько `SKPaint` объекты, сохраненные как поля для отрисовки текущего прямоугольника обрезки:

```csharp
class PhotoCropperCanvasView : SKCanvasView
{
    const int CORNER = 50;      // pixel length of cropper corner
    ···
    SKBitmap bitmap;
    CroppingRectangle croppingRect;
    SKMatrix inverseBitmapMatrix;
    ···
    // Drawing objects
    SKPaint cornerStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.White,
        StrokeWidth = 10
    };

    SKPaint edgeStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.White,
        StrokeWidth = 2
    };
    ···
    protected override void OnPaintSurface(SKPaintSurfaceEventArgs args)
    {
        base.OnPaintSurface(args);

        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Gray);

        // Calculate rectangle for displaying bitmap 
        float scale = Math.Min((float)info.Width / bitmap.Width, (float)info.Height / bitmap.Height);
        float x = (info.Width - scale * bitmap.Width) / 2;
        float y = (info.Height - scale * bitmap.Height) / 2;
        SKRect bitmapRect = new SKRect(x, y, x + scale * bitmap.Width, y + scale * bitmap.Height);
        canvas.DrawBitmap(bitmap, bitmapRect);

        // Calculate a matrix transform for displaying the cropping rectangle
        SKMatrix bitmapScaleMatrix = SKMatrix.MakeIdentity();
        bitmapScaleMatrix.SetScaleTranslate(scale, scale, x, y);

        // Display rectangle
        SKRect scaledCropRect = bitmapScaleMatrix.MapRect(croppingRect.Rect);
        canvas.DrawRect(scaledCropRect, edgeStroke);

        // Display heavier corners
        using (SKPath path = new SKPath())
        {
            path.MoveTo(scaledCropRect.Left, scaledCropRect.Top + CORNER);
            path.LineTo(scaledCropRect.Left, scaledCropRect.Top);
            path.LineTo(scaledCropRect.Left + CORNER, scaledCropRect.Top);

            path.MoveTo(scaledCropRect.Right - CORNER, scaledCropRect.Top);
            path.LineTo(scaledCropRect.Right, scaledCropRect.Top);
            path.LineTo(scaledCropRect.Right, scaledCropRect.Top + CORNER);

            path.MoveTo(scaledCropRect.Right, scaledCropRect.Bottom - CORNER);
            path.LineTo(scaledCropRect.Right, scaledCropRect.Bottom);
            path.LineTo(scaledCropRect.Right - CORNER, scaledCropRect.Bottom);

            path.MoveTo(scaledCropRect.Left + CORNER, scaledCropRect.Bottom);
            path.LineTo(scaledCropRect.Left, scaledCropRect.Bottom);
            path.LineTo(scaledCropRect.Left, scaledCropRect.Bottom - CORNER);

            canvas.DrawPath(path, cornerStroke);
        }

        // Invert the transform for touch tracking
        bitmapScaleMatrix.TryInvert(out inverseBitmapMatrix);
    }
    ···
}
```

Код в `CroppingRectangle` класс выполняет прямоугольника обрезки на размер растрового изображения в пикселях. Тем не менее отображение растрового изображения по `PhotoCropperCanvasView` класс масштабируется на основе размера области отображения. `bitmapScaleMatrix` Вычисляемые в `OnPaintSurface` переопределить сопоставляется из пикселей растрового изображения размер и положение растрового изображения, которое будет отображаться. Эта матрица затем используется для преобразования прямоугольника обрезки, что его можно отображать относительно растрового изображения.

В последней строке `OnPaintSurface` переопределение принимает обратное `bitmapScaleMatrix` и сохраняет его как `inverseBitmapMatrix` поля. Используется для обработки сенсорного ввода.

Объект `TouchEffect` объект создается как поле, а конструктор присоединяет обработчик к `TouchAction` событий, но `TouchEffect` должен быть добавлен к `Effects` коллекцию _родительского_ из `SKCanvasView`класс, производный от, таким образом, закончится `OnParentSet` переопределить:

```csharp
class PhotoCropperCanvasView : SKCanvasView
{
    ···
    const int RADIUS = 100;     // pixel radius of touch hit-test
    ···
    CroppingRectangle croppingRect;
    SKMatrix inverseBitmapMatrix;

    // Touch tracking 
    TouchEffect touchEffect = new TouchEffect();
    struct TouchPoint
    {
        public int CornerIndex { set; get; }
        public SKPoint Offset { set; get; }
    }

    Dictionary<long, TouchPoint> touchPoints = new Dictionary<long, TouchPoint>();
    ···
    public PhotoCropperCanvasView(SKBitmap bitmap, float? aspectRatio = null)
    {
        ···
        touchEffect.TouchAction += OnTouchEffectTouchAction;
    }
    ···
    protected override void OnParentSet()
    {
        base.OnParentSet();

        // Attach TouchEffect to parent view
        Parent.Effects.Add(touchEffect);
    }
    ···
    void OnTouchEffectTouchAction(object sender, TouchActionEventArgs args)
    {
        SKPoint pixelLocation = ConvertToPixel(args.Location);
        SKPoint bitmapLocation = inverseBitmapMatrix.MapPoint(pixelLocation);

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                // Convert radius to bitmap/cropping scale
                float radius = inverseBitmapMatrix.ScaleX * RADIUS;

                // Find corner that the finger is touching
                int cornerIndex = croppingRect.HitTest(bitmapLocation, radius);

                if (cornerIndex != -1 && !touchPoints.ContainsKey(args.Id))
                {
                    TouchPoint touchPoint = new TouchPoint
                    {
                        CornerIndex = cornerIndex,
                        Offset = bitmapLocation - croppingRect.Corners[cornerIndex]
                    };

                    touchPoints.Add(args.Id, touchPoint);
                }
                break;

            case TouchActionType.Moved:
                if (touchPoints.ContainsKey(args.Id))
                {
                    TouchPoint touchPoint = touchPoints[args.Id];
                    croppingRect.MoveCorner(touchPoint.CornerIndex, 
                                            bitmapLocation - touchPoint.Offset);
                    InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (touchPoints.ContainsKey(args.Id))
                {
                    touchPoints.Remove(args.Id);
                }
                break;
        }
    }

    SKPoint ConvertToPixel(Xamarin.Forms.Point pt)
    {
        return new SKPoint((float)(CanvasSize.Width * pt.X / Width),
                           (float)(CanvasSize.Height * pt.Y / Height));
    }
}
```

События касания, обрабатываемых `TouchAction` обработчик находятся в аппаратно независимых единицах. Они сначала должны преобразовываться в пикселях с помощью `ConvertToPixel` метод в нижней части класса и затем преобразуется в `CroppingRectangle` единицы используя `inverseBitmapMatrix`.

Для `Pressed` события, `TouchAction` вызовов обработчика `HitTest` метод `CroppingRectangle`. Если он возвращает отличное от индекса &ndash;1, затем один из углов прямоугольника обрезки выполняется манипуляция. Индекс и смещение фактическое сенсорной точки в углу хранится в `TouchPoint` объекта и добавляемый `touchPoints` словаря.

Для `Moved` событий, `MoveCorner` метод `CroppingRectangle` вызывается для перемещения в угол, учитывая возможные поправки для пропорций.

В любое время программы с помощью `PhotoCropperCanvasView` можно получить доступ к `CroppedBitmap` свойство. Это свойство использует `Rect` свойство `CroppingRectangle` для создания точечного обрезанное размера. Версия `DrawBitmap` с исходного и конечного прямоугольников затем извлекает подмножество исходного растрового изображения:

```csharp
class PhotoCropperCanvasView : SKCanvasView
{
    ···
    SKBitmap bitmap;
    CroppingRectangle croppingRect;
    ···
    public SKBitmap CroppedBitmap
    {
        get
        {
            SKRect cropRect = croppingRect.Rect;
            SKBitmap croppedBitmap = new SKBitmap((int)cropRect.Width, 
                                                  (int)cropRect.Height);
            SKRect dest = new SKRect(0, 0, cropRect.Width, cropRect.Height);
            SKRect source = new SKRect(cropRect.Left, cropRect.Top, 
                                       cropRect.Right, cropRect.Bottom);

            using (SKCanvas canvas = new SKCanvas(croppedBitmap))
            {
                canvas.DrawBitmap(bitmap, source, dest);
            }

            return croppedBitmap;
        }
    }
    ···
}
```

## <a name="hosting-the-photo-cropper-canvas-view"></a>Размещение представлении photo cropper canvas

С помощью этих двух классов, обрабатывающих обрезки **Обрезка Photo** странице в **[SkiaSharpFormsDemos](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)** приложение имеет очень мало работы. Файл XAML создает экземпляр `Grid` узел `PhotoCropperCanvasView` и **сделать** кнопки:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SkiaSharpFormsDemos.Bitmaps.PhotoCroppingPage"
             Title="Photo Cropping">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid x:Name="canvasViewHost"
              Grid.Row="0"
              BackgroundColor="Gray"
              Padding="5" />

        <Button Text="Done"
                Grid.Row="1"
                HorizontalOptions="Center"
                Margin="5"
                Clicked="OnDoneButtonClicked" />
    </Grid>
</ContentPage>
```

`PhotoCropperCanvasView` Не может быть создан в файле XAML, поскольку она требует параметр типа `SKBitmap`.

Вместо этого `PhotoCropperCanvasView` создается в конструкторе класса файла кода, с помощью одного из ресурсов растровых изображений:

```csharp
public partial class PhotoCroppingPage : ContentPage
{
    PhotoCropperCanvasView photoCropper;
    SKBitmap croppedBitmap;

    public PhotoCroppingPage ()
    {
        InitializeComponent ();

        SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(GetType(),
            "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

        photoCropper = new PhotoCropperCanvasView(bitmap);
        canvasViewHost.Children.Add(photoCropper);
    }

    void OnDoneButtonClicked(object sender, EventArgs args)
    {
        croppedBitmap = photoCropper.CroppedBitmap;

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
        canvas.DrawBitmap(croppedBitmap, info.Rect, BitmapStretch.Uniform);
    }
}
```

Пользователь затем может управлять прямоугольника обрезки:

[![Фото Cropper 1](cropping-images/PhotoCropping1.png "фото Cropper 1")](cropping-images/PhotoCropping1-Large.png#lightbox)

При хорошей прямоугольника обрезки был определен, нажмите кнопку **сделать** кнопки. `Clicked` Обработчик получает обрезанное точечного рисунка из `CroppedBitmap` свойство `PhotoCropperCanvasView`и заменяет все содержимое страницы с новым `SKCanvasView` объекта, который отображает обрезанное точечного рисунка:

[![Фото Cropper 2](cropping-images/PhotoCropping2.png "фото Cropper 2")](cropping-images/PhotoCropping2-Large.png#lightbox)

Попробуйте установить второй аргумент `PhotoCropperCanvasView` для 1.78f (например):

```csharp
photoCropper = new PhotoCropperCanvasView(bitmap, 1.78f);
```

Вы увидите прямоугольника обрезки, ограничены характеристик высокой четкости соотношение сторон 16 до 9.

<a name="tile-division" />

## <a name="dividing-a-bitmap-into-tiles"></a>Разделение на плитки растрового изображения

Xamarin.Forms версию знаменитый головоломки 14-15 появился в главе 22 книги [ _Создание мобильных приложений с помощью Xamarin.Forms_ ](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) и можно загрузить как [  **XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle). Тем не менее, головоломки становится интереснее (и часто более сложной задачей) когда он основан на изображение из библиотеке фотографий.

Эта версия 14-15 головоломки является частью **[SkiaSharpFormsDemos](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)** приложения и состоит из ряда страниц под названием **головоломки Photo**.

**PhotoPuzzlePage1.xaml** файл состоит из `Button`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SkiaSharpFormsDemos.Bitmaps.PhotoPuzzlePage1"
             Title="Photo Puzzle">
    
    <Button Text="Pick a photo from your library"
            VerticalOptions="CenterAndExpand" 
            HorizontalOptions="CenterAndExpand"
            Clicked="OnPickButtonClicked"/>
    
</ContentPage>
```

Файл с выделенным кодом реализует `Clicked` обработчик, который использует `IPhotoLibrary` службы зависимостей позволяют выбрать снимок из медиатеки:

```csharp
public partial class PhotoPuzzlePage1 : ContentPage
{
    public PhotoPuzzlePage1 ()
    {
        InitializeComponent ();
    }

    async void OnPickButtonClicked(object sender, EventArgs args)
    {
        IPhotoLibrary photoLibrary = DependencyService.Get<IPhotoLibrary>();
        using (Stream stream = await photoLibrary.PickPhotoAsync())
        {
            if (stream != null)
            {
                SKBitmap bitmap = SKBitmap.Decode(stream);

                await Navigation.PushAsync(new PhotoPuzzlePage2(bitmap));
            }
        }
    }
}
```

Метод, который затем переходит к `PhotoPuzzlePage2`, передав в конструктор выбранного растрового изображения.

Это возможно, фотографии, выбранные из библиотеки, не является ориентированной, так как он были доступны в библиотеке фотографий, но повернуть или ногами. (Это особенно проблемы с устройствами iOS.) По этой причине `PhotoPuzzlePage2` позволяет поворачивать изображение желаемой ориентацией. Файл XAML содержит три кнопки с надписью **90&#x00B0; справа** (по часовой стрелке) это означает, что **90&#x00B0; слева** (против часовой стрелки), и **сделать**.

Файл с выделенным кодом реализует логику поворот точечного рисунка, показанный в статье  **[создания и рисования в SkiaSharp точечные рисунки](drawing.md#rotating-bitmaps)**. Пользователь может сменить изображение на 90 градусов по часовой стрелке или против часовой стрелки любое количество раз: 

```csharp
public partial class PhotoPuzzlePage2 : ContentPage
{
    SKBitmap bitmap;

    public PhotoPuzzlePage2 (SKBitmap bitmap)
    {
        this.bitmap = bitmap;

        InitializeComponent ();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform);
    }

    void OnRotateRightButtonClicked(object sender, EventArgs args)
    {
        SKBitmap rotatedBitmap = new SKBitmap(bitmap.Height, bitmap.Width);

        using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
        {
            canvas.Clear();
            canvas.Translate(bitmap.Height, 0);
            canvas.RotateDegrees(90);
            canvas.DrawBitmap(bitmap, new SKPoint());
        }

        bitmap = rotatedBitmap;
        canvasView.InvalidateSurface();
    }

    void OnRotateLeftButtonClicked(object sender, EventArgs args)
    {
        SKBitmap rotatedBitmap = new SKBitmap(bitmap.Height, bitmap.Width);

        using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
        {
            canvas.Clear();
            canvas.Translate(0, bitmap.Width);
            canvas.RotateDegrees(-90);
            canvas.DrawBitmap(bitmap, new SKPoint());
        }

        bitmap = rotatedBitmap;
        canvasView.InvalidateSurface();
    }

    async void OnDoneButtonClicked(object sender, EventArgs args)
    {
        await Navigation.PushAsync(new PhotoPuzzlePage3(bitmap));
    }
}
```

Когда пользователь щелкает **сделать** кнопки, `Clicked` обработчик переходит к `PhotoPuzzlePage3`, передачи окончательного повернутый растрового изображения в конструктор страницы.

`PhotoPuzzlePage3` позволяет фотографию, чтобы обрезаться. Программе требуется square растровое изображение, чтобы разделить в сетку 4 x 4 из плиток.

**PhotoPuzzlePage3.xaml** файл содержит `Label`, `Grid` узел `PhotoCropperCanvasView`и другой **сделать** кнопки:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SkiaSharpFormsDemos.Bitmaps.PhotoPuzzlePage3"
             Title="Photo Puzzle">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Label Text="Crop the photo to a square"
               Grid.Row="0"
               FontSize="Large"
               HorizontalTextAlignment="Center"
               Margin="5" />

        <Grid x:Name="canvasViewHost"
              Grid.Row="1"
              BackgroundColor="Gray"
              Padding="5" />

        <Button Text="Done"
                Grid.Row="2"
                HorizontalOptions="Center"
                Margin="5"
                Clicked="OnDoneButtonClicked" />
    </Grid>
</ContentPage>
```

Создает файл с выделенным кодом `PhotoCropperCanvasView` с растровым изображением, переданного в конструктор. Обратите внимание, что 1 передается в качестве второго аргумента `PhotoCropperCanvasView`. Это соотношение сторон 1 заставляет прямоугольника обрезки быть квадрат:

```csharp
public partial class PhotoPuzzlePage3 : ContentPage
{
    PhotoCropperCanvasView photoCropper;

    public PhotoPuzzlePage3(SKBitmap bitmap)
    {
        InitializeComponent ();

        photoCropper = new PhotoCropperCanvasView(bitmap, 1f);
        canvasViewHost.Children.Add(photoCropper);
    }

    async void OnDoneButtonClicked(object sender, EventArgs args)
    {
        SKBitmap croppedBitmap = photoCropper.CroppedBitmap;
        int width = croppedBitmap.Width / 4;
        int height = croppedBitmap.Height / 4;

        ImageSource[] imgSources = new ImageSource[15];

        for (int row = 0; row < 4; row++)
        {
            for (int col = 0; col < 4; col++)
            {
                // Skip the last one!
                if (row == 3 && col == 3)
                    break;

                // Create a bitmap 1/4 the width and height of the original
                SKBitmap bitmap = new SKBitmap(width, height);
                SKRect dest = new SKRect(0, 0, width, height);
                SKRect source = new SKRect(col * width, row * height, (col + 1) * width, (row + 1) * height);

                // Copy 1/16 of the original into that bitmap
                using (SKCanvas canvas = new SKCanvas(bitmap))
                {
                    canvas.DrawBitmap(croppedBitmap, source, dest);
                }

                imgSources[4 * row + col] = (SKBitmapImageSource)bitmap;
            }
        }

        await Navigation.PushAsync(new PhotoPuzzlePage4(imgSources));
    }
}
```

**Сделать** обработчик кнопки Получает ширину и высоту обрезанное точечного рисунка (эти два значения должны совпадать), а затем разбивает его на 15 отдельных растровые изображения, каждое из которых является 1/4 ширину и высоту исходного. (Последняя из 16 возможных точечные рисунки не создан). `DrawBitmap` Метод с прямоугольником источника и назначения позволяет растровое изображение, чтобы создать зависимости на подмножестве точечного изображения большего размера.

## <a name="converting-to-xamarinforms-bitmaps"></a>Преобразование в растровые изображения Xamarin.Forms

В `OnDoneButtonClicked` , массив, созданный для растровых изображений, 15 является типа [ `ImageSource` ](xref:Xamarin.Forms.ImageSource):

```csharp
ImageSource[] imgSources = new ImageSource[15];
```

`ImageSource` является базовым типом Xamarin.Forms, который инкапсулирует точечный рисунок. К счастью SkiaSharp позволяет преобразование из точечных рисунков SkiaSharp в Xamarin.Forms точечных рисунков. **SkiaSharp.Views.Forms** определяет сборку [ `SKBitmapImageSource` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.SKBitmapImageSource/) класс, производный от `ImageSource` , но могут создаваться в зависимости от SkiaSharp `SKBitmap` объекта. `SKBitmapImageSource` даже определены преобразования между `SKBitmapImageSource` и `SKBitmap`и что все как `SKBitmap` объекты хранятся в виде массива, как растровые изображения Xamarin.Forms:

```csharp
imgSources[4 * row + col] = (SKBitmapImageSource)bitmap;
```

Этот массив точечных рисунков, передается в качестве конструктора для `PhotoPuzzlePage4`. Эта страница является полностью Xamarin.Forms и не использует любой SkiaSharp. Это очень похоже на [ **XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle), поэтому он не описанные здесь, но отображается выбранной фотографии разделить на 15 квадратные плитки:

[![Фото головоломки 1](cropping-images/PhotoPuzzle1.png "фото головоломки 1")](cropping-images/PhotoPuzzle1-Large.png#lightbox)

Нажав клавишу **Randomize** кнопку смешаны вверх все плитки:

[![Фото головоломки 2](cropping-images/PhotoPuzzle2.png "фото головоломки 2")](cropping-images/PhotoPuzzle2-Large.png#lightbox)

Теперь можно поместить их обратно в правильном порядке. Чтобы переместить их в пустой квадратик можно отвода никаких карточек в одной и той же строке или столбца в виде пустого квадрата. 

## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
