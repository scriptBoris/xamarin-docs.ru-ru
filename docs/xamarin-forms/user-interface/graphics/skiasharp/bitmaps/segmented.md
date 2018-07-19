---
title: Сегментированное отображение точечных рисунков SkiaSharp
description: Отображения растрового рисунка SkiaSharp, растягивается некоторые области и не являются некоторые области.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 79AE2033-C41C-4447-95A6-76D22E913D19
author: charlespetzold
ms.author: chape
ms.date: 07/17/2018
ms.openlocfilehash: f1f148f0b510b55aebb70a03e0d5beb89c4044c4
ms.sourcegitcommit: 7f2e44e6f628753e06a5fe2a3076fc2ec5baa081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39131482"
---
# <a name="segmented-display-of-skiasharp-bitmaps"></a>Сегментированное отображение точечных рисунков SkiaSharp

SkiaSharp `SKCanvas` объект определяет метод с именем `DrawBitmapNinePatch` и два метода с именем `DrawBitmapLattice` , очень похожи. Оба эти методы отрисовки растровое изображение, чтобы размер целевого прямоугольника, но вместо растягивания растровое изображение единообразно, они отображения части точечного рисунка в его размеры в пикселах и выполнить растяжение другие части точечного рисунка, так, чтобы прямоугольник:

![Примеры сегментированных](segmented-images/SegmentedSample.png "сегментированных образца")

Эти методы обычно используются для визуализация точечных рисунков, которые являются частью UI-объектов, таких как кнопки. При разработке кнопки, обычно возникает необходимость размер кнопку, чтобы быть на основе содержимого кнопки, но вы, вероятно, захотите граница кнопки, чтобы иметь одинаковую ширину независимо от ее содержимого. Это приложение идеально `DrawBitmapNinePatch`.

`DrawBitmapNinePatch` является особым случаем `DrawBitmapLattice` , но этот вариант проще из двух способов осознать и применить.

## <a name="the-nine-patch-display"></a>Отображение девять patch 

По существу [ `DrawBitmapNinePatch` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmapNinePatch/p/SkiaSharp.SKBitmap/SkiaSharp.SKRectI/SkiaSharp.SKRect/SkiaSharp.SKPaint/) делит растрового изображения на девять прямоугольники:

![Девять Patch](segmented-images/NinePatch.png "девять Patch")

Прямоугольники в четырех углах отображаются в их размеры пикселей. Как стрелки указывают, других областей по краям точечный рисунок растягивается горизонтально или вертикально в область прямоугольника назначения. Прямоугольник в центре растягивается по горизонтали и вертикали. 

Если не хватает места в прямоугольник назначения для отображения даже четырех углов в свои размеры, уменьшении доступный размер, и ничего, но отображаются четыре угла.

Для разделения растрового изображения на этих девяти прямоугольников, необходим только для указания прямоугольника в центре. Это синтаксис `DrawBitmapNinePatch` метод:

```csharp
canvas.DrawBitmapNinePatch(bitmap, centerRectangle, destRectangle, paint);
```

Center прямоугольник указывается относительно растрового изображения. Это `SKRectI` значение (целое число версию `SKRect`) и все координаты и размеры, пикселей. Прямоугольник назначения задается относительно области отображения. Аргумент `paint` не обязателен.

**Отображения Patch девяти** странице в [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) примере сначала используется статический конструктор для создания общее статическое свойство типа `SKBitmap`:

```csharp
public partial class NinePatchDisplayPage : ContentPage
{
    static NinePatchDisplayPage()
    {
        using (SKCanvas canvas = new SKCanvas(FiveByFiveBitmap))
        using (SKPaint paint = new SKPaint
        {
            Style = SKPaintStyle.Stroke,
            Color = SKColors.Red,
            StrokeWidth = 10
        })
        {
            for (int x = 50; x < 500; x += 100)
                for (int y = 50; y < 500; y += 100)
                {
                    canvas.DrawCircle(x, y, 40, paint);
                }
        }
    }

    public static SKBitmap FiveByFiveBitmap { get; } = new SKBitmap(500, 500);
    ···
}
```

Две другие страницы в этой статье используется этот же точечный рисунок. Точечный рисунок — 500 пикселей в квадратных и состоит из массива 25 кругов, и те же размер, каждый резервирует область square 100 пикселей:

![Круг на сетке](segmented-images/CircleGrid.png "круг сетки")

Создает конструктор экземпляра программы `SKCanvasView` с `PaintSurface` обработчик, который использует `DrawBitmapNinePatch` для отображения точечный рисунок растягивается на его поверхность отображения:

```csharp
public class NinePatchDisplayPage : ContentPage
{
    ···
    public NinePatchDisplayPage()
    {
        Title = "Nine-Patch Display";

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

        SKRectI centerRect = new SKRectI(100, 100, 400, 400);
        canvas.DrawBitmapNinePatch(FiveByFiveBitmap, centerRect, info.Rect);
    }
}
```

`centerRect` Прямоугольник охватывает центра массив 16 кругов. Круги в углах отображаются в их размеры, и все остальное растягивается соответствующим образом:

[![Отображение девять Patch](segmented-images/NinePatchDisplay.png "отображения девять Patch")](segmented-images/NinePatchDisplay-Large.png#lightbox)

Странице универсальной платформы Windows может быть 500 пикселей в ширину и таким образом отображает верхней и нижней строки как ряд круги такого же размера. В противном случае всех кругов, которые не находятся в углах растягиваются в форму кнопку с многоточием.

Странное отображения объектов, состоящий из объединения кругов и кнопку с многоточием попробуйте определение прямоугольника center, чтобы он перекрывает строки и столбцы кругов:

```csharp
SKRectI centerRect = new SKRectI(150, 150, 350, 350);
```

## <a name="the-lattice-display"></a>Отображение решетки

Два `DrawBitmapLattice` методы похожи на `DrawBitmapNinePatch`, обобщенная для любого числа делений горизонтальной или вертикальной. Эти разделы определяются массивы целых чисел, соответствующий пикселей. 

[ `DrawBitmapLattice` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmapLattice/p/SkiaSharp.SKBitmap/System.Int32[]/System.Int32[]/SkiaSharp.SKRect/SkiaSharp.SKPaint/) Метода с параметрами для этих массивов целых чисел не работает. [ `DrawBitmapLattice` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmapLattice/p/SkiaSharp.SKBitmap/SkiaSharp.SKLattice/SkiaSharp.SKRect/SkiaSharp.SKPaint/) Метод с параметром типа `SKLattice` работает, и который будет использоваться в приведенные ниже примеры.

[ `SKLattice` ](https://developer.xamarin.com/api/type/SkiaSharp.SKLattice/) Структура определяет четыре свойства:

- [`XDivs`](https://developer.xamarin.com/api/property/SkiaSharp.SKLattice.XDivs/), массив целых чисел
- [`YDivs`](https://developer.xamarin.com/api/property/SkiaSharp.SKLattice.YDivs/), массив целых чисел
- [`Flags`](https://developer.xamarin.com/api/property/SkiaSharp.SKLattice.Flags/), массив `SKLatticeFlags`, типом перечисления
- [`Bounds`](https://developer.xamarin.com/api/property/SkiaSharp.SKLattice.Bounds/) типа `Nullable<SKRectI>` для указания дополнительный источник прямоугольник внутри точечного рисунка

`XDivs` Массива делит ширину растрового изображения на вертикальной полосы. Первой полосковой расширяется от точки 0 в левом `XDivs[0]`. Эта лента будет отображен в его ширина в пикселях. Вторая полоса, начиная с позиции `XDivs[0]` для `XDivs[1]`и растягивается. Третья полоса, начиная с позиции `XDivs[1]` для `XDivs[2]` и готовится к просмотру в его ширина в пикселях. Панель последнего начиная с последнего элемента массива по правому краю растрового изображения. Если массив содержит четное число элементов, оно отображается в его ширина в пикселях. В противном случае она растягивается. Общее число вертикальной полосы является одним больше числа элементов в массиве.

`YDivs` Массив похож. Высота элемента массива разделяется по горизонтали полосковых линий.

Вместе `XDivs` и `YDivs` массива разделения растрового изображения в прямоугольники. Количеством прямоугольников равно произведению числа горизонтальной полосы и по вертикали полосковых линий.

В соответствии с документацией Skia `Flags` массив содержит один элемент для каждого прямоугольника, сначала верхняя строка прямоугольников, а затем во второй строке и т. д. `Flags` Массив имеет тип [ `SKLatticeFlags` ](https://developer.xamarin.com/api/type/SkiaSharp.SKLatticeFlags/), перечисление со следующими членами:

- `Default` со значением 0
- `Transparent` со значением 1

Тем не менее эти флаги непохоже, чтобы работать, как они должны и лучше всего их игнорировать. Но не устанавливайте `Flags` свойства `null`. Задайте для него значение массив `SKLatticeFlags` значений достаточно большим, чтобы охватить общее число прямоугольники.

**Patch Lattice девяти** странице использует `DrawBitmapLattice` для имитации `DrawBitmapNinePatch`. Он использует этот же рисунок, созданные в `NinePatchDisplayPage`:

```csharp
public class LatticeNinePatchPage : ContentPage
{
    SKBitmap bitmap = NinePatchDisplayPage.FiveByFiveBitmap;

    public LatticeNinePatchPage ()
    {
        Title = "Lattice Nine-Patch";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    `
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        SKLattice lattice = new SKLattice();
        lattice.XDivs = new int[] { 100, 400 };
        lattice.YDivs = new int[] { 100, 400 };
        lattice.Flags = new SKLatticeFlags[9]; 

        canvas.DrawBitmapLattice(bitmap, lattice, info.Rect);
    }
}
```

Как `XDivs` и `YDivs` свойствам присваивается массивами из двух целых чисел, разделения растрового изображения на три ленты по горизонтали и вертикали: от точки 0 до 100 (подготовке к просмотру в размер в пикселях), пикселя из пикселей от 100 до 400 пикселя (растянуть), и из точки 400 пикселей 500 (размер в пикселях). Вместе `XDivs` и `YDivs` определить в сумме 9 прямоугольников, размер из `Flags` массива. Просто создание массива достаточно создать массив `SKLatticeFlags.Default` значения.

Отображение идентична предыдущей программы:

[![Lattice девять Patch](segmented-images/LatticeNinePatch.png "Lattice девять Patch")](segmented-images/LatticeNinePatch-Large.png#lightbox)

**Отображения Lattice** страницы разделяет растровое изображение на 16 прямоугольники:

```csharp
public class LatticeDisplayPage : ContentPage
{
    SKBitmap bitmap = NinePatchDisplayPage.FiveByFiveBitmap;

    public LatticeDisplayPage()
    {
        Title = "Lattice Display";

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

        SKLattice lattice = new SKLattice();
        lattice.XDivs = new int[] { 100, 200, 400 };
        lattice.YDivs = new int[] { 100, 300, 400 };

        int count = (lattice.XDivs.Length + 1) * (lattice.YDivs.Length + 1);
        lattice.Flags = new SKLatticeFlags[count];

        canvas.DrawBitmapLattice(bitmap, lattice, info.Rect);
    }
}
```

`XDivs` И `YDivs` массивов, немного отличаются, вызывает отображение, чтобы не быть довольно симметричное, как и в предыдущих примерах:

[![Lattice отображения](segmented-images/LatticeDisplay.png "Lattice отображения")](segmented-images/LatticeDisplay-Large.png#lightbox)

В iOS и Android изображения в левой части только ними подготавливаются к просмотру в их размеры пикселей. Все остальное растягивается.

**Отображения Lattice** страницы обобщает создание `Flags` массива, что позволяет экспериментировать с `XDivs` и `YDivs` проще. В частности, вы захотите увидеть, что происходит, когда значение первого элемента `XDivs` или `YDivs` массива 0. 

## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
