---
title: Отсеченные области с помощью путей
description: В этой статье объясняется, как использовать пути SkiaSharp клипов графики для конкретной области, а также для создания областей и демонстрирует это с помощью примера кода.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8022FBF9-2208-43DB-94D8-0A4E9A5DA07F
author: charlespetzold
ms.author: chape
ms.date: 06/16/2017
ms.openlocfilehash: 0c07d68535349004eeefeaa18daa9c59b889a6a7
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615292"
---
# <a name="clipping-with-paths-and-regions"></a>Отсеченные области с помощью путей

_Использование пути к коллекции графики для конкретной области и для создания областей_

Иногда бывает необходимо ограничить отрисовки графики к определенной области. Этот процесс называется *обрезки*. Можно использовать обрезки для специальные эффекты, такие как этот образ monkey, реализуемой по принципу стенка с замочной:

![](clipping-images/clippingsample.png "Monkey через стенка с замочной")

*Области отсечения* — это область экрана, в котором отображаются графики. Все, что отображается за пределами области обрезки не подготавливается к просмотру. Обычно определяется области отсечения [ `SKPath` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath/) объект, но также можно определить область отсечения с помощью [ `SKRegion` ](https://developer.xamarin.com/api/type/SkiaSharp.SKRegion/) объекта. Эти два типа объектов на сначала показаться связанных, так как можно создать область из пути. Тем не менее, нельзя создавать путь из области, и они сильно отличаются внутренне: путь включает в себя последовательность линий и кривых, пока регион определен ряд строк развертки по горизонтали.

На рисунке выше было создано **Monkey через стенка с замочной** страницы. [ `MonkeyThroughKeyholePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/MonkeyThroughKeyholePage.cs) Класс определяет путь, используя данные SVG и использует конструктор для загрузки точечного рисунка из ресурсов программы:

```csharp
public class MonkeyThroughKeyholePage : ContentPage
{
    SKBitmap bitmap;
    SKPath keyholePath = SKPath.ParseSvgPathData(
        "M 300 130 L 250 350 L 450 350 L 400 130 A 70 70 0 1 0 300 130 Z");

    public MonkeyThroughKeyholePage()
    {
        Title = "Monkey through Keyhole";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }
    ...
}
```

Несмотря на то что `keyholePath` объект описывает очертания объекта стенка с замочной, координаты, является совершенно произвольные и что было удобно, если было придумано данные пути. По этой причине `PaintSurface` обработчик получает границы данного пути и вызовы `Translate` и `Scale` для перемещения путь относительно центральной части экрана и сделать его практически по высоте экрана:


```csharp
public class MonkeyThroughKeyholePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Set transform to center and enlarge clip path to window height
        SKRect bounds;
        keyholePath.GetTightBounds(out bounds);

        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(0.98f * info.Height / bounds.Height);
        canvas.Translate(-bounds.MidX, -bounds.MidY);

        // Set the clip path
        canvas.ClipPath(keyholePath);

        // Reset transforms
        canvas.ResetMatrix();

        // Display monkey to fill height of window but maintain aspect ratio
        canvas.DrawBitmap(bitmap,
            new SKRect((info.Width - info.Height) / 2, 0,
                       (info.Width + info.Height) / 2, info.Height));
    }
}
```

Но путь не отображаются. Вместо этого после преобразования, чтобы задать область отсечения с этим Заявлением используется путь:

```csharp
canvas.ClipPath(keyholePath);
```

`PaintSurface` Обработчик затем сбрасывает преобразований с помощью вызова `ResetMatrix` и рисует растровое изображение, чтобы расширить до полного размера экрана. Этот код предполагается, что точечный рисунок квадрат, который является конкретной точечного рисунка. Растровое изображение отображается только в пределах области, определяемой контура обрезки:

[![](clipping-images/monkeythroughkeyhole-small.png "Тройной снимок Monkey через страницу стенка с замочной")](clipping-images/monkeythroughkeyhole-large.png#lightbox "тройной снимок Monkey через страницу стенка с замочной")

Контур обрезки подчиняется преобразований в силе при `ClipPath` вызывается метод, а не для преобразований в силе при графического объекта (например, точечный рисунок) отображается. Контур обрезки — часть состояния canvas, сохраняется вместе с `Save` метод и восстанавливаться с `Restore` метод.

## <a name="combining-clipping-paths"></a>Объединение контуров обрезки

Строго говоря, области отсечения «установлен» `ClipPath` метод. Вместо этого он объединяется с существующего контура обрезки, начинающейся с прямоугольника одного размера на экран. Вы можете получить прямоугольник, ограничивающий область отсечения с помощью [ `ClipBounds` ](https://developer.xamarin.com/api/property/SkiaSharp.SKCanvas.ClipBounds/) свойство или [ `ClipDeviceBounds` ](https://developer.xamarin.com/api/property/SkiaSharp.SKCanvas.ClipDeviceBounds/) свойство. `ClipBounds` Возвращает `SKRect` значение, которое отражает любые преобразования, который может быть фактически. `ClipDeviceBounds` Возвращает `RectI` значение. Это представляет собой прямоугольник с измерениями целое число и описывает области отсечения в фактические размеры.

Любой вызов `ClipPath` уменьшает области отсечения путем объединения области отсечения с новой области. Полный синтаксис [ `ClipPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ClipPath/p/SkiaSharp.SKPath/SkiaSharp.SKClipOperation/System.Boolean/) метод:

```csharp
public void ClipPath(SKPath path, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

Имеется также [ `ClipRect` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ClipRect/p/SkiaSharp.SKRect/SkiaSharp.SKClipOperation/System.Boolean/) метод, который объединяет области отсечения прямоугольником:

```csharp
public Void ClipRect(SKRect rect, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

По умолчанию области отсечения результирующий представляют собой пересечение существующей области обрезки и `SKPath` или `SKRect` , указанный в `ClipPath` или `ClipRect` метод. Это показано в **четыре коллекции пересекаются круги** страницы. `PaintSurface` Обработчик в [ `FourCircleInteresectClipPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/FourCircleIntersectClipPage.cs) класс повторно использует же `SKPath` объект для создания четырех круга, каждый из которых уменьшает области отсечения через последовательные вызовы `ClipPath`:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float size = Math.Min(info.Width, info.Height);
    float radius = 0.4f * size;
    float offset = size / 2 - radius;

    // Translate to center
    canvas.Translate(info.Width / 2, info.Height / 2);

    using (SKPath path = new SKPath())
    {
        path.AddCircle(-offset, -offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        path.Reset();
        path.AddCircle(-offset, offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        path.Reset();
        path.AddCircle(offset, -offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        path.Reset();
        path.AddCircle(offset, offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Fill;
            paint.Color = SKColors.Blue;
            canvas.DrawPaint(paint);
        }
    }
}
```

Остается найти пересечение этих четырех кругах:

[![](clipping-images//fourcircleintersectclip-small.png "Тройной снимок экрана страницы четыре коллекции пересекаются круг")](clipping-images/fourcircleintersectclip-large.png#lightbox "тройной снимок экрана страницы четыре коллекции пересекаются круг")

[ `SKClipOperation` ](https://developer.xamarin.com/api/type/SkiaSharp.SKClipOperation/) Перечисление имеет только два члена:

- [`Difference`](https://developer.xamarin.com/api/field/SkiaSharp.SKClipOperation.Difference/) Удаляет указанный путь или прямоугольник из существующей области обрезки

- [`Intersect`](https://developer.xamarin.com/api/field/SkiaSharp.SKClipOperation.Intersect/) пересекает указанный путь или прямоугольник с существующей области обрезки

При замене четыре `SKClipOperation.Intersect` аргументов в `FourCircleIntersectClipPage` класса `SKClipOperation.Difference`, вы увидите следующее:

[![](clipping-images//fourcircledifferenceclip-small.png "Тройной снимок экрана страницы четыре коллекции пересекаются круг с помощью операции по различению")](clipping-images/fourcircledifferenceclip-large.png#lightbox "тройной снимок экрана страницы четыре коллекции пересекаются круг с помощью операции разности")

Четыре круга были удалены из области отсечения.

**Операций клипов** страницы показаны различия в этих двух операций с парой кругов. Первый круг в левой части добавляется в область отсечения с операцией обрезки по умолчанию из `Intersect`, тогда как второй круга в правой части добавляется в области отсечения с операцией обрезки, обозначается текстовую метку:

[![](clipping-images//clipoperations-small.png "Тройной снимок экрана страницы операции клипов")](clipping-images/clipoperations-large.png#lightbox "тройной снимок экрана страницы операции клипов")

[ `ClipOperationsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ClipOperationsPage.cs) Класс определяет два `SKPaint` объектов как поля и затем разделяет экран на две прямоугольной области. Эти области различаются в зависимости от того, находится ли телефон в книжном или альбомном режиме. `DisplayClipOp` Класс затем отображает текст и вызывает `ClipPath` кружок с двумя путями для иллюстрации каждого операцией обрезки:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float x = 0;
    float y = 0;

    foreach (SKClipOperation clipOp in Enum.GetValues(typeof(SKClipOperation)))
    {
        // Portrait mode
        if (info.Height > info.Width)
        {
            DisplayClipOp(canvas, new SKRect(x, y, x + info.Width, y + info.Height / 2), clipOp);
            y += info.Height / 2;
        }
        // Landscape mode
        else
        {
            DisplayClipOp(canvas, new SKRect(x, y, x + info.Width / 2, y + info.Height), clipOp);
            x += info.Width / 2;
        }
    }
}

void DisplayClipOp(SKCanvas canvas, SKRect rect, SKClipOperation clipOp)
{
    float textSize = textPaint.TextSize;
    canvas.DrawText(clipOp.ToString(), rect.MidX, rect.Top + textSize, textPaint);
    rect.Top += textSize;

    float radius = 0.9f * Math.Min(rect.Width / 3, rect.Height / 2);
    float xCenter = rect.MidX;
    float yCenter = rect.MidY;

    canvas.Save();

    using (SKPath path1 = new SKPath())
    {
        path1.AddCircle(xCenter - radius / 2, yCenter, radius);
        canvas.ClipPath(path1);

        using (SKPath path2 = new SKPath())
        {
            path2.AddCircle(xCenter + radius / 2, yCenter, radius);
            canvas.ClipPath(path2, clipOp);

            canvas.DrawPaint(fillPaint);
        }
    }

    canvas.Restore();
}
```

Вызов `DrawPaint` обычно приводит к весь холст, заполняемый `SKPaint` объекта, но в этом случае метод просто закрашивает в пределах области отсечения.

## <a name="exploring-regions"></a>Изучение области

Если вы изучили документацию по API для `SKCanvas`, вы могли заметить перегрузки `ClipPath` и `ClipRect` методы, которые похожи на методы, описанные выше, но вместо этого имеют параметр с именем [ `SKRegionOperation` ](https://developer.xamarin.com/api/type/SkiaSharp.SKRegionOperation/) вместо `SKClipOperation`. `SKRegionOperation` содержит шесть членов, в некоторой степени большая гибкость при выборе объединения путей к областям формы обрезки:

- [`Difference`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.Difference/)

- [`Intersect`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.Intersect/)

- [`Union`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.Union/)

- [`XOR`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.XOR/)

- [`ReverseDifference`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.ReverseDifference/)

- [`Replace`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.Replace/)

Тем не менее перегрузки `ClipPath` и `ClipRect` с `SKRegionOperation` параметры устарели, и они не могут использоваться.

Вы можете продолжать использовать `SKRegionOperation` перечисления, но его необходимо определить область обрезки на основе [ `SKRegion` ](https://developer.xamarin.com/api/type/SkiaSharp.SKRegion/) объекта.

Вновь созданный `SKRegion` объект описывает пустую область. Обычно является первый вызов в объекте [ `SetRect` ](https://developer.xamarin.com/api/member/SkiaSharp.SKRegion.SetRect/p/SkiaSharp.SKRectI/) таким образом, чтобы область описания прямоугольной области. Параметр `SetRect` — `SKRectI` значение &mdash; значение прямоугольника с помощью свойства целого числа. Затем можно вызвать [ `SetPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKRegion.SetPath/p/SkiaSharp.SKPath/SkiaSharp.SKRegion/) с `SKPath` объекта. При этом создается область, совпадает со значением внутреннюю часть пути, но была выполнена Обрезка начальной прямоугольной области.

`SKRegionOperation` Перечисления только вступает в действие при вызове одного из [ `Op` ](https://developer.xamarin.com/api/member/SkiaSharp.SKRegion.Op/p/SkiaSharp.SKRegion/SkiaSharp.SKRegionOperation/) перегрузок метода, например такая:

```csharp
public Boolean Op(SKRegion region, SKRegionOperation op)
```

Регион, вы вносите `Op` вызов объединяется с регионом, указанным в качестве параметра, на основе `SKRegionOperation` член. Получив наконец регион подходит для обрезки, который можно задать в качестве области отсечения canvas с помощью [ `ClipRegion` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ClipRegion/p/SkiaSharp.SKRegion/SkiaSharp.SKClipOperation/) метод `SKCanvas`:

```csharp
public void ClipRegion(SKRegion region, SKClipOperation operation = SKClipOperation.Intersect)
```

На следующем рисунке показаны области обрезки на основе операций шесть регион. Круг слева — это регион, `Op` вызывается метод, и правой круг области, передаваемый `Op` метод:

[![](clipping-images//regionoperations-small.png "Тройной снимок экрана страницы операции над областями")](clipping-images/regionoperations-large.png#lightbox "тройной снимок экрана страницы операции над областями")

Такое этих все возможные сочетания эти два круга Итоговый образ можно считать сочетания трех компонентов, что сами по себе, будут видны в `Difference`, `Intersect`, и `ReverseDifference` операций. Общее число сочетаний определяется, двух-третьей степени или восемь. Отсутствующие они исходный регион (который полученный в результате вызова не `Op` вообще) и полностью пустая область.

Он является трудным в использовании областей для обрезки, так как вам нужно сначала создать путь, а затем область по этому пути, а затем объедините несколько регионов. Общая структура **операции над областями** страница очень похожа на **операций клипов** , но [ `RegionOperationsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RegionOperationsPage.cs) класс разделяет экран на шесть категорий и Показывает дополнительной работы, необходимые для использования области для этого задания:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float x = 0;
    float y = 0;
    float width = info.Height > info.Width ? info.Width / 2 : info.Width / 3;
    float height = info.Height > info.Width ? info.Height / 3 : info.Height / 2;

    foreach (SKRegionOperation regionOp in Enum.GetValues(typeof(SKRegionOperation)))
    {
        DisplayClipOp(canvas, new SKRect(x, y, x + width, y + height), regionOp);

        if ((x += width) >= info.Width)
        {
            x = 0;
            y += height;
        }
    }
}

void DisplayClipOp(SKCanvas canvas, SKRect rect, SKRegionOperation regionOp)
{
    float textSize = textPaint.TextSize;
    canvas.DrawText(regionOp.ToString(), rect.MidX, rect.Top + textSize, textPaint);
    rect.Top += textSize;

    float radius = 0.9f * Math.Min(rect.Width / 3, rect.Height / 2);
    float xCenter = rect.MidX;
    float yCenter = rect.MidY;

    SKRectI recti = new SKRectI((int)rect.Left, (int)rect.Top,
                                (int)rect.Right, (int)rect.Bottom);

    using (SKRegion wholeRectRegion = new SKRegion())
    {
        wholeRectRegion.SetRect(recti);

        using (SKRegion region1 = new SKRegion(wholeRectRegion))
        using (SKRegion region2 = new SKRegion(wholeRectRegion))
        {
            using (SKPath path1 = new SKPath())
            {
                path1.AddCircle(xCenter - radius / 2, yCenter, radius);
                region1.SetPath(path1);
            }

            using (SKPath path2 = new SKPath())
            {
                path2.AddCircle(xCenter + radius / 2, yCenter, radius);
                region2.SetPath(path2);
            }

            region1.Op(region2, regionOp);

            canvas.Save();
            canvas.ClipRegion(region1);
            canvas.DrawPaint(fillPaint);
            canvas.Restore();
        }
    }
}
```

Здесь находится большая разница между `ClipPath` метод и `ClipRegion` метод:

> [!IMPORTANT]
> В отличие от `ClipPath` метод, `ClipRegion` метод не зависит от преобразования.

Чтобы понять основной причиной такой разницы, полезно понять, какой регион. Если предполагается о как клипов операции или операции над областями может быть реализована внутренне, вероятно, кажется слишком сложны. Несколько потенциально очень сложным траекториям объединяются и контура результирующий путь, скорее всего, является алгоритмического кошмар.

Но это задание значительно упрощена Если каждый путь сокращается в ряд строк горизонтальной развертки, например, в старом пылесоса туристического велосипеда телевизоры. Каждой строки — просто горизонтальная линия с начальной и конечной точки. Например окружность с радиусом 10 может быть разложено на 20 строк развертки по горизонтали, каждый из которых начинается в левую часть круга и заканчивается в правой части. Объединяя два круга с любой операции регион становится очень простым, так как он является просто изучение координаты начала и окончания каждой пары соответствующих строк развертки.

Вот что регион: ряда горизонтальной сканирование строк, определяющий область.

Тем не менее, когда область уменьшится до ряд сканирования "строки", эти проверки строк на основании измерения определенный пиксель. Строго говоря область не графический объект vector. Она будет ближе по своей природе в сжатый монохромный точечный рисунок чем путь. Следовательно области не может быть масштабировании или повороте без ухудшения качества и поэтому они не преобразуются при использовании для областей обрезки.

Тем не менее можно применить преобразования к регионам, для целей рисования. **Область рисования** программа наглядно демонстрирует внутреннее характер регионов. [ `RegionPaintPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RegionPaintPage.cs) Класс создает `SKRegion` на основе `SKPath` круга с 10-единица radius. Преобразования затем расширяет круг, что для заполнения страницы:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    int radius = 10;

    // Create circular path
    using (SKPath circlePath = new SKPath())
    {
        circlePath.AddCircle(0, 0, radius);

        // Create circular region
        using (SKRegion circleRegion = new SKRegion())
        {
            circleRegion.SetRect(new SKRectI(-radius, -radius, radius, radius));
            circleRegion.SetPath(circlePath);

            // Set transform to move it to center and scale up
            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.Scale(Math.Min(info.Width / 2, info.Height / 2) / radius);

            // Fill region
            using (SKPaint fillPaint = new SKPaint())
            {
                fillPaint.Style = SKPaintStyle.Fill;
                fillPaint.Color = SKColors.Orange;

                canvas.DrawRegion(circleRegion, fillPaint);
            }

            // Stroke path for comparison
            using (SKPaint strokePaint = new SKPaint())
            {
                strokePaint.Style = SKPaintStyle.Stroke;
                strokePaint.Color = SKColors.Blue;
                strokePaint.StrokeWidth = 0.1f;

                canvas.DrawPath(circlePath, strokePaint);
            }
        }
    }
}
```

`DrawRegion` Вызов заполняет область оранжевым цветом, хотя `DrawPath` вызов Обводка исходный путь синим цветом для сравнения:

[![](clipping-images//regionpaint-small.png "Тройной снимок экрана страницы области рисования")](clipping-images/regionpaint-large.png#lightbox "тройной снимок экрана страницы области рисования")

Области, очевидно, что представляет собой ряд дискретных координаты.

Если не нужно использовать преобразования в связи с вашей области обрезки, можно использовать регионов для обрезки, как **клевера конечного – четыре** страница демонстрирует. [ `FourLeafCloverPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/FourLeafCloverPage.cs) Класса составного регион из четырех регионах циклические конструкции, задает в качестве области отсечения этого составного региона и затем Рисует последовательность 360 прямых линий, вытекающих из центра страницы:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float xCenter = info.Width / 2;
    float yCenter = info.Height / 2;
    float radius = 0.24f * Math.Min(info.Width, info.Height);

    using (SKRegion wholeScreenRegion = new SKRegion())
    {
        wholeScreenRegion.SetRect(new SKRectI(0, 0, info.Width, info.Height));

        using (SKRegion leftRegion = new SKRegion(wholeScreenRegion))
        using (SKRegion rightRegion = new SKRegion(wholeScreenRegion))
        using (SKRegion topRegion = new SKRegion(wholeScreenRegion))
        using (SKRegion bottomRegion = new SKRegion(wholeScreenRegion))
        {
            using (SKPath circlePath = new SKPath())
            {
                // Make basic circle path
                circlePath.AddCircle(xCenter, yCenter, radius);

                // Left leaf
                circlePath.Transform(SKMatrix.MakeTranslation(-radius, 0));
                leftRegion.SetPath(circlePath);

                // Right leaf
                circlePath.Transform(SKMatrix.MakeTranslation(2 * radius, 0));
                rightRegion.SetPath(circlePath);

                // Make union of right with left
                leftRegion.Op(rightRegion, SKRegionOperation.Union);

                // Top leaf
                circlePath.Transform(SKMatrix.MakeTranslation(-radius, -radius));
                topRegion.SetPath(circlePath);

                // Combine with bottom leaf
                circlePath.Transform(SKMatrix.MakeTranslation(0, 2 * radius));
                bottomRegion.SetPath(circlePath);

                // Make union of top with bottom
                bottomRegion.Op(topRegion, SKRegionOperation.Union);

                // Exclusive-OR left and right with top and bottom
                leftRegion.Op(bottomRegion, SKRegionOperation.XOR);

                // Set that as clip region
                canvas.ClipRegion(leftRegion);

                // Set transform for drawing lines from center
                canvas.Translate(xCenter, yCenter);

                // Draw 360 lines
                for (double angle = 0; angle < 360; angle++)
                {
                    float x = 2 * radius * (float)Math.Cos(Math.PI * angle / 180);
                    float y = 2 * radius * (float)Math.Sin(Math.PI * angle / 180);

                    using (SKPaint strokePaint = new SKPaint())
                    {
                        strokePaint.Color = SKColors.Green;
                        strokePaint.StrokeWidth = 2;

                        canvas.DrawLine(0, 0, x, y, strokePaint);
                    }
                }
            }
        }
    }
}
```

Он не очень похоже клевера конечного – четыре, но это изображение, в противном случае может оказаться непросто для подготовки к просмотру без усечения:

[![](clipping-images//fourleafclover-small.png "Тройной снимок экрана страницы клевера конечного – четыре")](clipping-images/fourleafclover-large.png#lightbox "тройной снимок экрана клевера конечного – четыре страницы")


## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
