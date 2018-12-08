---
title: Три способа нарисовать дугу
description: В этой статье описывается использование SkiaSharp для определения дуги тремя различными способами и демонстрирует это с помощью примера кода.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: F1DA55E4-0182-4388-863C-5C340213BF3C
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2017
ms.openlocfilehash: fc08824ba973f5d50dbe92950d57c4ea8ecc3147
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53054016"
---
# <a name="three-ways-to-draw-an-arc"></a>Три способа нарисовать дугу

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_Сведения об использовании для определения дуги в три разных способа SkiaSharp_

Дуга — кривую на длины окружности эллипса, например скругленными части это знак бесконечности:

![](arcs-images/arcsample.png "Знак бесконечности")

Несмотря на простоту этого определения, не существует способа определить функцию рисования дуги, удовлетворяющий все потребности, и, следовательно, не консенсус между системами графики о наилучшем способе нарисовать дугу. По этой причине `SKPath` класса не ограничивает сам лишь одним из подходов.

`SKPath` Определяет [ `AddArc` ](xref:SkiaSharp.SKPath.AddArc*) метод, пять различных [ `ArcTo` ](xref:SkiaSharp.SKPath.ArcTo*) методов и два относительно [ `RArcTo` ](xref:SkiaSharp.SKPath.RArcTo*) методы. Эти методы можно разделить на три категории, представляющая три различных подхода к указанию дуги. Используемый тот, который зависит от сведений, доступных для определения дуги и как это arc дополняют другие графические, рисовании.

## <a name="the-angle-arc"></a>Угол дуги

Угол дуги способ рисования дуги, необходимо указать прямоугольник, ограничивающий эллипс. Дугу на длины окружности этого эллипса обозначается углы от центра эллипса, указывающие начало дуги и его длины. Два различных способа рисования угол дуги. Это [ `AddArc` ](xref:SkiaSharp.SKPath.AddArc(SkiaSharp.SKRect,System.Single,System.Single)) метод и [ `ArcTo` ](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKRect,System.Single,System.Single,System.Boolean)) метод:

```csharp
public void AddArc (SKRect oval, Single startAngle, Single sweepAngle)

public void ArcTo (SKRect oval, Single startAngle, Single sweepAngle, Boolean forceMoveTo)
```

Эти методы аналогичны Android [ `AddArc` ](https://developer.xamarin.com/api/member/Android.Graphics.Path.AddArc/p/Android.Graphics.RectF/System.Single/System.Single/) и [ `ArcTo` ](https://developer.xamarin.com/api/member/Android.Graphics.Path.ArcTo/p/Android.Graphics.RectF/System.Single/System.Single/System.Boolean/) методы. IOS [ `AddArc` ](https://developer.xamarin.com/api/member/CoreGraphics.CGPath.AddArc/p/System.Boolean/System.nfloat/System.nfloat/System.nfloat/System.nfloat/System.nfloat/) метод аналогичен, но будет ограничен дуг на длину окружности круга, а не обобщить как эллипс.

Оба метода начинаются с `SKRect` значение, которое определяет расположение и размер эллипса:

![](arcs-images/anglearcoval.png "Овала, начинает угол дуги")

Дуга является частью длины окружности этого эллипса.

`startAngle` Аргумент — по часовой стрелке в градусах относительно горизонтальной линии, рисуемой из центра эллипса справа. `sweepAngle` Аргумент задается по отношению к `startAngle`. Ниже приведены `startAngle` и `sweepAngle` значения 60 градусов и 100 градусов, соответственно:

![](arcs-images/anglearcangles.png "Углы, которые определяют угол дуги")

Начальный угол дуги начинается. Его длина регулируется угол поворота. Ниже приведен дуги красным цветом.

![](arcs-images/anglearchighlight.png "Выделенные угол дуги")

Кривая, добавляется к пути с `AddArc` или `ArcTo` метод — просто это часть эллипса окружности:

![](arcs-images/anglearc.png "Угол дуги сама по себе")

`startAngle` Или `sweepAngle` аргументов может быть отрицательным: дуги по часовой стрелке для положительных значений из `sweepAngle` и против часовой стрелки для отрицательных значений.

Тем не менее `AddArc` does *не* определить закрытый профиль. При вызове метода `LineTo` после `AddArc`, линия берет начало от конца дуги к моменту `LineTo` метода и это справедливо для `ArcTo`.

`AddArc` автоматически запускает новый профиль и функционально эквивалентен вызов `ArcTo` с последний аргумент `true`:

```csharp
path.ArcTo (oval, startAngle, sweepAngle, true);
```

Что вызывается последним аргументом `forceMoveTo`, и фактически вызывает `MoveTo` вызвать в начале дуги. Новый профиль, который начинается. Это не так с последним аргументом `false`:

```csharp
path.ArcTo (oval, startAngle, sweepAngle, false);
```

Эта версия `ArcTo` проводит линию из текущей позиции в начале дуги. Это означает, что дуги может быть где-то посередине профиль большего размера.

**Угол дуги** страница позволяет указать начало и углы поворота с помощью двух ползунков. Файл XAML создает два `Slider` элементов и `SKCanvasView`. `PaintCanvas` Обработчик в [ **AngleArcPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/AngleArcPage.xaml.cs) файл рисует Овал и дуги, с использованием двух `SKPaint` объекты, определенные как поля:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKRect rect = new SKRect(100, 100, info.Width - 100, info.Height - 100);
    float startAngle = (float)startAngleSlider.Value;
    float sweepAngle = (float)sweepAngleSlider.Value;

    canvas.DrawOval(rect, outlinePaint);

    using (SKPath path = new SKPath())
    {
        path.AddArc(rect, startAngle, sweepAngle);
        canvas.DrawPath(path, arcPaint);
    }
}
```

Как вы видите, начальный угол и угол поворота, выполняемых с отрицательными значениями:

[![](arcs-images/anglearc-small.png "Тройной снимок экрана страницы угол дуги")](arcs-images/anglearc-large.png#lightbox "тройной снимок экрана страницы угол дуги")

Этот подход к созданию дугу является алгоритмически наиболее простым и легко являются производными параметрические уравнения, которые описывают дуги. Зная, размер и расположение элемента эллипса и угла начала и поворота, начальную и конечную точки дуги можно вычислить по простой тригонометрических вычислений:

x = oval. MidX + (овал. Ширина / 2) * cos(angle)

y = oval. MidY + (овал. Высота / 2) * sin(angle)

`angle` Значение `startAngle` или `startAngle + sweepAngle`.

Использование двух углов для определения дугу является наилучшим образом подходит для случаев, когда вы знаете angular длина окружности, необходимый для рисования, например, чтобы убедиться в круговой диаграмме. **Разрезанная круговая диаграмма** страница демонстрирует это. [ `ExplodedPieChartPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ExplodedPieChartPage.cs) Класс использует внутренний класс для определения некоторых данных создано и цвета:

```csharp
class ChartData
{
    public ChartData(int value, SKColor color)
    {
        Value = value;
        Color = color;
    }

    public int Value { private set; get; }

    public SKColor Color { private set; get; }
}

ChartData[] chartData =
{
    new ChartData(45, SKColors.Red),
    new ChartData(13, SKColors.Green),
    new ChartData(27, SKColors.Blue),
    new ChartData(19, SKColors.Magenta),
    new ChartData(40, SKColors.Cyan),
    new ChartData(22, SKColors.Brown),
    new ChartData(29, SKColors.Gray)
};

```

`PaintSurface` Обработчик сначала перебирает элементы для вычисления `totalValues` номер. Таким образом его можно определить размер каждого элемента долях от общего количества и преобразовать их значение угла:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    int totalValues = 0;

    foreach (ChartData item in chartData)
    {
        totalValues += item.Value;
    }

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float explodeOffset = 50;
    float radius = Math.Min(info.Width / 2, info.Height / 2) - 2 * explodeOffset;
    SKRect rect = new SKRect(center.X - radius, center.Y - radius,
                             center.X + radius, center.Y + radius);

    float startAngle = 0;

    foreach (ChartData item in chartData)
    {
        float sweepAngle = 360f * item.Value / totalValues;

        using (SKPath path = new SKPath())
        using (SKPaint fillPaint = new SKPaint())
        using (SKPaint outlinePaint = new SKPaint())
        {
            path.MoveTo(center);
            path.ArcTo(rect, startAngle, sweepAngle, false);
            path.Close();

            fillPaint.Style = SKPaintStyle.Fill;
            fillPaint.Color = item.Color;

            outlinePaint.Style = SKPaintStyle.Stroke;
            outlinePaint.StrokeWidth = 5;
            outlinePaint.Color = SKColors.Black;

            // Calculate "explode" transform
            float angle = startAngle + 0.5f * sweepAngle;
            float x = explodeOffset * (float)Math.Cos(Math.PI * angle / 180);
            float y = explodeOffset * (float)Math.Sin(Math.PI * angle / 180);

            canvas.Save();
            canvas.Translate(x, y);

            // Fill and stroke the path
            canvas.DrawPath(path, fillPaint);
            canvas.DrawPath(path, outlinePaint);
            canvas.Restore();
        }

        startAngle += sweepAngle;
    }
}
```

Новый `SKPath` объекта создается для каждого среза круговой диаграммы. Путь состоит из строки из центра, то `ArcTo` для рисования дуги и еще одну строку обратно в центр результаты из `Close` вызова. Эта программа отображает «Разрезанная» секторов диаграммы, перемещая их все в центре на 50 пикселей. Эта задача требует вектор в направлении среднюю угол поворота для каждого среза:

[![](arcs-images/explodedpiechart-small.png "Тройной снимок экрана страницы Разрезанная круговая диаграмма")](arcs-images/explodedpiechart-large.png#lightbox "тройной снимок экрана страницы Разрезанная круговая диаграмма")

Чтобы увидеть, как выглядит без «развертывание», просто задокомментируем `Translate` вызова:

[![](arcs-images/explodedpiechartunexploded-small.png "Тройной снимок экрана страницы Разрезанная круговая диаграмма без развертывания")](arcs-images/explodedpiechartunexploded-large.png#lightbox "тройной снимок экрана страницы Разрезанная круговая диаграмма без развертывания")

## <a name="the-tangent-arc"></a>Касательной дуги

Второй тип дуги, поддерживаемых `SKPath` — *касательных arc*, так называемых поскольку дуги окружности круга, была касательной к две соединенные линии.

Касательной arc добавляется путь с помощью вызова [ `ArcTo` ](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,System.Single)) метод с двумя `SKPoint` параметры, или [ `ArcTo` ](xref:SkiaSharp.SKPath.ArcTo(System.Single,System.Single,System.Single,System.Single,System.Single)) перегрузка с помощью отдельных `Single` параметры для следующие моменты.

```csharp
public void ArcTo (SKPoint point1, SKPoint point2, Single radius)

public void ArcTo (Single x1, Single y1, Single x2, Single y2, Single radius)
```

Это `ArcTo` метод аналогичен методу PostScript [ `arct` ](https://www.adobe.com/products/postscript/pdfs/PLRM.pdf) функции (страницы 532) и iOS [ `AddArcToPoint` ](https://developer.xamarin.com/api/member/CoreGraphics.CGPath.AddArcToPoint/p/System.nfloat/System.nfloat/System.nfloat/System.nfloat/System.nfloat/) метод.

`ArcTo` Метод включает в себя три точки:

- Текущий точек контура или точки (0, 0), если `MoveTo` не был вызван
- Первый аргумент точки `ArcTo` метод, именуемый *углу точки*
- Второй аргумент точки `ArcTo`, который называется *конечная точка*:

![](arcs-images/tangentarcthreepoints.png "Три точки, которые начинаются касательных дуги")

Эти три точки определяют две соединенные линии:

![](arcs-images/tangentarcconnectinglines.png "Линии, соединяющие три точки касания дуги")

Если colinear три точки &mdash; то есть если они находятся в той же строке прямой &mdash; рисуется дуга.

`ArcTo` Также включает `radius` параметра. Определяет радиус круга:

![](arcs-images/tangentarccircle.png "Круг касательных дуги")

Касательной дуги для эллипс не генерализована.

Если две строки соответствует под любым углом, которым могут вставляться между эти строки, чтобы она была касательной к обе строки:

![](arcs-images/tangentarctangentcircle.png "Элемент управления circle касательных дуга между двумя строками")

Кривую, которая добавляется в профиль не затрагивает любой из точки, указанные в `ArcTo` метод. Она состоит из прямую линию с текущего момента первой точке касания и дугу, которая заканчивается на второй точке касания, здесь показано красным цветом:

![](arcs-images/tangentarchighlight.png "Выделенные касательных дуга между двумя линиями")

Ниже приведен окончательный прямой линии и дуги, который добавляется в профиль.

![](arcs-images/tangentarc.png "Выделенные касательных дуга между двумя линиями")

Профиль может быть продолжен из второй точке касания.

**Дуги тангенс** страница позволяет экспериментировать с касательной дуги. Это первое из нескольких страниц, которые являются производными от [ `InteractivePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/InteractivePage.cs), который определяет некоторые удобные `SKPaint` объектов и выполняет `TouchPoint` обработки:

```csharp
public class InteractivePage : ContentPage
{
    protected SKCanvasView baseCanvasView;
    protected TouchPoint[] touchPoints;

    protected SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 3
    };

    protected SKPaint redStrokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 15
    };

    protected SKPaint dottedStrokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 3,
        PathEffect = SKPathEffect.CreateDash(new float[] { 7, 7 }, 0)
    };

    protected void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        bool touchPointMoved = false;

        foreach (TouchPoint touchPoint in touchPoints)
        {
            float scale = baseCanvasView.CanvasSize.Width / (float)baseCanvasView.Width;
            SKPoint point = new SKPoint(scale * (float)args.Location.X,
                                        scale * (float)args.Location.Y);
            touchPointMoved |= touchPoint.ProcessTouchEvent(args.Id, args.Type, point);
        }

        if (touchPointMoved)
        {
            baseCanvasView.InvalidateSurface();
        }
    }
}
```

Класс `TangentArcPage` является производным от класса `InteractivePage`. Конструктор в [ **TangentArcPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml.cs) файл отвечает за создание и инициализация `touchPoints` массива, а параметр `baseCanvasView` (в `InteractivePage`) для `SKCanvasView` создать экземпляр объекта в [ **TangentArcPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml) файла:

```csharp
public partial class TangentArcPage : InteractivePage
{
    public TangentArcPage()
    {
        touchPoints = new TouchPoint[3];

        for (int i = 0; i < 3; i++)
        {
            TouchPoint touchPoint = new TouchPoint
            {
                Center = new SKPoint(i == 0 ? 100 : 500,
                                     i != 2 ? 100 : 500)
            };
            touchPoints[i] = touchPoint;
        }

        InitializeComponent();

        baseCanvasView = canvasView;
        radiusSlider.Value = 100;
    }

    void sliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if (canvasView != null)
        {
            canvasView.InvalidateSurface();
        }
    }
    ...
}
```

`PaintSurface` Обработчик использует `ArcTo` на основе методов, чтобы нарисовать дугу на точки касания и `Slider`, но также алгоритмически вычисляет круг, угол основан на:

```csharp
public partial class TangentArcPage : InteractivePage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Draw the two lines that meet at an angle
        using (SKPath path = new SKPath())
        {
            path.MoveTo(touchPoints[0].Center);
            path.LineTo(touchPoints[1].Center);
            path.LineTo(touchPoints[2].Center);
            canvas.DrawPath(path, dottedStrokePaint);
        }

        // Draw the circle that the arc wraps around
        float radius = (float)radiusSlider.Value;

        SKPoint v1 = Normalize(touchPoints[0].Center - touchPoints[1].Center);
        SKPoint v2 = Normalize(touchPoints[2].Center - touchPoints[1].Center);

        double dotProduct = v1.X * v2.X + v1.Y * v2.Y;
        double angleBetween = Math.Acos(dotProduct);
        float hypotenuse = radius / (float)Math.Sin(angleBetween / 2);
        SKPoint vMid = Normalize(new SKPoint((v1.X + v2.X) / 2, (v1.Y + v2.Y) / 2));
        SKPoint center = new SKPoint(touchPoints[1].Center.X + vMid.X * hypotenuse,
                                     touchPoints[1].Center.Y + vMid.Y * hypotenuse);

        canvas.DrawCircle(center.X, center.Y, radius, this.strokePaint);

        // Draw the tangent arc
        using (SKPath path = new SKPath())
        {
            path.MoveTo(touchPoints[0].Center);
            path.ArcTo(touchPoints[1].Center, touchPoints[2].Center, radius);
            canvas.DrawPath(path, redStrokePaint);
        }

        foreach (TouchPoint touchPoint in touchPoints)
        {
            touchPoint.Paint(canvas);
        }
    }

    // Vector methods
    SKPoint Normalize(SKPoint v)
    {
        float magnitude = Magnitude(v);
        return new SKPoint(v.X / magnitude, v.Y / magnitude);
    }

    float Magnitude(SKPoint v)
    {
        return (float)Math.Sqrt(v.X * v.X + v.Y * v.Y);
    }
}
```

Вот **дуги тангенс** странице под управлением:

[![](arcs-images/tangentarc-small.png "Тройной снимок экрана страницы Arc тангенс")](arcs-images/tangentarc-large.png#lightbox "тройной снимок экрана страницы тангенс дуги")

Касательной дуги идеально подходит для создания скругленных углов, например прямоугольник с закругленными углами. Так как `SKPath` уже включает в себя `AddRoundedRect` метод, **округленное Heptagon** странице рассказывается, как использовать `ArcTo` для скругление углов семь стороны многоугольника. (Код обобщается для любого регулярных многоугольника.)

`PaintSurface` Обработчик [ `RoundedHeptagonPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RoundedHeptagonPage.cs) класса содержит один `for` цикла, для которого требуется вычислить координаты семь вершины heptagon, во-вторых, для которого требуется вычислить средние точки этих семи сторон вершины. Затем эти средние точки используются для создания пути:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float cornerRadius = 100;
    int numVertices = 7;
    float radius = 0.45f * Math.Min(info.Width, info.Height);

    SKPoint[] vertices = new SKPoint[numVertices];
    SKPoint[] midPoints = new SKPoint[numVertices];

    double vertexAngle = -0.5f * Math.PI;       // straight up

    // Coordinates of the vertices of the polygon
    for (int vertex = 0; vertex < numVertices; vertex++)
    {
        vertices[vertex] = new SKPoint(radius * (float)Math.Cos(vertexAngle),
                                       radius * (float)Math.Sin(vertexAngle));
        vertexAngle += 2 * Math.PI / numVertices;
    }

    // Coordinates of the midpoints of the sides connecting the vertices
    for (int vertex = 0; vertex < numVertices; vertex++)
    {
        int prevVertex = (vertex + numVertices - 1) % numVertices;
        midPoints[vertex] = new SKPoint((vertices[prevVertex].X + vertices[vertex].X) / 2,
                                        (vertices[prevVertex].Y + vertices[vertex].Y) / 2);
    }

    // Create the path
    using (SKPath path = new SKPath())
    {
        // Begin at the first midpoint
        path.MoveTo(midPoints[0]);

        for (int vertex = 0; vertex < numVertices; vertex++)
        {
            SKPoint nextMidPoint = midPoints[(vertex + 1) % numVertices];

            // Draws a line from the current point, and then the arc
            path.ArcTo(vertices[vertex], nextMidPoint, cornerRadius);

            // Connect the arc with the next midpoint
            path.LineTo(nextMidPoint);
        }
        path.Close();

        // Render the path in the center of the screen
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = 10;

            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.DrawPath(path, paint);
        }
    }
}

```

Вот ее запуск.

[![](arcs-images/roundedheptagon-small.png "Тройной снимок экрана страницы округленное Heptagon")](arcs-images/roundedheptagon-large.png#lightbox "тройной снимок экрана страницы округленное Heptagon")

## <a name="the-elliptical-arc"></a>Эллиптической дуги

Эллиптической дуги добавляется к пути с помощью вызова [ `ArcTo` ](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPathArcSize,SkiaSharp.SKPathDirection,SkiaSharp.SKPoint)) метод, который имеет два `SKPoint` параметры, или [ `ArcTo` ](xref:SkiaSharp.SKPath.ArcTo(System.Single,System.Single,System.Single,SkiaSharp.SKPathArcSize,SkiaSharp.SKPathDirection,System.Single,System.Single)) перегрузка с отдельной X и Y координаты:

```csharp
public void ArcTo (SKPoint r, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, SKPoint xy)

public void ArcTo (Single rx, Single ry, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, Single x, Single y)
```

Эллиптической дуги согласуется с [эллиптической дуги](http://www.w3.org/TR/SVG11/paths.html#PathDataEllipticalArcCommands) входящих в масштабируемый векторный рисунок (SVG) и универсальной платформы Windows [ `ArcSegment` ](/uwp/api/Windows.UI.Xaml.Media.ArcSegment/) класса.

Эти `ArcTo` методы нарисовать дугу между двумя точками, которые являются текущей точки контура, а последний параметр `ArcTo` метод ( `xy` параметр или отдельные `x` и `y` параметров):

![](arcs-images/ellipticalarcpoints.png "Две точки, которые определены эллиптической дуги")

Первый параметр точки для `ArcTo` метод (`r`, или `rx` и `ry`) вообще не является точкой, но вместо этого задает горизонтальный и вертикальный радиусы эллипса;

![](arcs-images/ellipticalarcellipse.png "Эллипса, который определен эллиптической дуги")

`xAxisRotate` Параметр — это число градусов по часовой стрелке для поворота этого эллипса:

![](arcs-images/ellipticalarctiltedellipse.png "Мозаичный эллипса, который определен эллиптической дуги")

Если это мозаичный эллипс находится затем таким образом, чтобы он соприкасается двумя точками, точки соединяются два разных дуги:

![](arcs-images/ellipticalarcellipse1.png "Первый набор эллиптической дуги")

Можно отличить эти две дуги двумя способами: верхний дуги больше, чем нижней дуги и как дуга которого отображается слева направо, верхнем дуги по часовой стрелке хотя нижней дуга рисуется против часовой стрелки.

Можно также в соответствии с эллипса между двумя точками другим способом:

![](arcs-images/ellipticalarcellipse2.png "Второй набор эллиптической дуги")

Теперь имеется меньшего размера дуги в верхней части, которая рисуется по часовой стрелке и большего размера дуги внизу, рисуется против часовой стрелки.

Этих двух точек может быть подключено дугой определением мозаичный эллипс в общей сложности состоит из четырех способов:

![](arcs-images/ellipticalarccolors.png "Все четыре эллиптической дуги")

Эти четыре дуги различаются по четыре комбинации [ `SKPathArcSize` ](xref:SkiaSharp.SKPathArcSize) и [ `SKPathDirection` ](xref:SkiaSharp.SKPathDirection) аргументов типа перечисления в `ArcTo` метод:

- красный: SKPathArcSize.Large и SKPathDirection.Clockwise
- Зеленый: SKPathArcSize.Small и SKPathDirection.Clockwise
- Синий: SKPathArcSize.Small и SKPathDirection.CounterClockwise
- Пурпурный: SKPathArcSize.Large и SKPathDirection.CounterClockwise

Если мозаичный эллипс не достаточное для размещения между двумя точками, затем он равномерно масштабируется, пока она не будет достаточно большим. Только два уникальных дуги в этом случае подключиться двумя точками. Их можно отличить с `SKPathDirection` параметра.

Несмотря на то, что этот подход к определению дугу звучит на первом возникновении сложных, это единственный подход, который позволяет определять дуги с повернутый эллипс, и это часто самый простой подход, если вам нужно провести интеграцию с другими частями компании contour дуги.

**Эллиптической дуги** страница позволяет интерактивно установить две точки и размер и поворот эллипса. `EllipticalArcPage` Класс является производным от `InteractivePage`и `PaintSurface` обработчик в [ **EllipticalArcPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/EllipticalArcPage.xaml.cs) файл с выделенным кодом рисует четыре дуги:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPath path = new SKPath())
    {
        int colorIndex = 0;
        SKPoint ellipseSize = new SKPoint((float)xRadiusSlider.Value,
                                          (float)yRadiusSlider.Value);
        float rotation = (float)rotationSlider.Value;

        foreach (SKPathArcSize arcSize in Enum.GetValues(typeof(SKPathArcSize)))
            foreach (SKPathDirection direction in Enum.GetValues(typeof(SKPathDirection)))
            {
                path.MoveTo(touchPoints[0].Center);
                path.ArcTo(ellipseSize, rotation,
                           arcSize, direction,
                           touchPoints[1].Center);

                strokePaint.Color = colors[colorIndex++];
                canvas.DrawPath(path, strokePaint);
                path.Reset();
            }
    }

    foreach (TouchPoint touchPoint in touchPoints)
    {
        touchPoint.Paint(canvas);
    }
}

```

Здесь выполняется:

[![](arcs-images/ellipticalarc-small.png "Тройной снимок экрана страницы эллиптической дуги")](arcs-images/ellipticalarc-large.png#lightbox "тройной снимок экрана страницы эллиптической дуги")

**Бесконечность Arc** страница использует эллиптической дуги для рисования знак бесконечности. Знак бесконечности основана на два круга с радиусы 100 единиц, разделенных 100 единиц.

![](arcs-images/infinitycircles.png "Два круга")

Две строки, пересекающих друг с другом приведены касательной оба окружности.

![](arcs-images/infinitycircleslines.png "Два круга с касательные")

Знак бесконечности состоит из части этих кругах и две строки. Чтобы использовать эллиптической дуги для рисования знак бесконечности, необходимо определить координаты, где две строки представляют касательной кругов.

Создайте прямоугольник справа в один из кругов.

![](arcs-images/infinitytriangle.png "Два круга с касательные и embedded круг")

Радиус круга составляет 100 единиц и гипотенузы треугольника составляет 150 единиц, поэтому угол α арксинус 100 на 150, или 41,8 градусов (обратный синус). Длина другой стороне треугольник — 150 раз косинус 41,8 градусов или 112, который также можно вычислить по необходимости знать теорему Пифагора.

Координаты точки касания, затем может быть вычислено с помощью этой информации:

x = 112·cos(41.8) = 83

y = 112·sin(41.8) = 75

Четыре касательных приведены все необходимое для рисования знак бесконечности по центру в точке (0, 0) с радиусы круг 100.

![](arcs-images/infinitycoordinates.png "Два круга с касательные и координатами")

`PaintSurface` Обработчик в [ `ArcInfinityPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ArcInfinityPage.cs) класс размещает знак бесконечности, чтобы (0, 0) точка располагается по центру страницы и масштабирует путь, по размеру экрана:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPath path = new SKPath())
    {
        path.LineTo(83, 75);
        path.ArcTo(100, 100, 0, SKPathArcSize.Large, SKPathDirection.CounterClockwise, 83, -75);
        path.LineTo(-83, 75);
        path.ArcTo(100, 100, 0, SKPathArcSize.Large, SKPathDirection.Clockwise, -83, -75);
        path.Close();

        // Use path.TightBounds for coordinates without control points
        SKRect pathBounds = path.Bounds;

        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(Math.Min(info.Width / pathBounds.Width,
                              info.Height / pathBounds.Height));

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = 5;

            canvas.DrawPath(path, paint);
        }
    }
}
```

Код использует `Bounds` свойство `SKPath` определяет размеры бесконечность синус для его масштабирования размер холста:

[![](arcs-images/arcinfinity-small.png "Тройной снимок экрана страницы бесконечность Arc")](arcs-images/arcinfinity-large.png#lightbox "тройной снимок экрана страницы бесконечность дуги")

Результат выглядит немного мало, поэтому, предположительно, `Bounds` свойство `SKPath` сообщает, размер, превышающий путь.

На внутреннем уровне Skia аппроксимирует дуги, с помощью нескольких кривых Безье второго порядка. Эти кривые (как можно будет увидеть в следующем разделе) содержат контрольные точки, которые определяют, каким образом кривой, но не являются частью отображаемой кривой. `Bounds` Свойство включает эти контрольные точки.

Чтобы получить более тесную подходит, используйте `TightBounds` свойство, которое исключает контрольные точки. Вот программа работает в альбомном режиме и с помощью `TightBounds` свойство, чтобы получить границы путей:

[![](arcs-images/arcinfinitytightbounds-small.png "Тройной снимок экрана страницы бесконечность Arc с тесной границами")](arcs-images/arcinfinitytightbounds-large.png#lightbox "тройной снимок экрана страницы бесконечность Arc с тесной границы")

Несмотря на то, что подключения между дуги, а также прямые линии — математически smooth, может показаться немного внезапные изменение дугу на прямую линию. Знак бесконечности лучше представлены в следующей статье по [ **три типа кривых Безье**](beziers.md).

## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
