---
title: Три типа кривых Безье
description: В этой статье описывается использование SkiaSharp для подготовки к просмотру кривых Безье третьего порядка, квадратичных и conic приложений Xamarin.Forms и демонстрирует это с помощью примера кода.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8FE0F6DC-16BC-435F-9626-DD1790C0145A
author: davidbritch
ms.author: dabritch
ms.date: 05/25/2017
ms.openlocfilehash: 1da0ee6155548a38057e4c7bf49ae5b90d445d79
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2018
ms.locfileid: "39615344"
---
# <a name="three-types-of-bzier-curves"></a>Три типа кривых Безье

_Узнайте, как использовать для подготовки к просмотру кривых Безье третьего порядка, квадратичных и conic SkiaSharp_

Кривая Безье именуется после Безье Пьер (1910 – 1999 г.), инженер французский автомобильные компании альянс Renault, кто использовал кривой для проектирования автоматизированных тел автомобиля.

Известно, кривых Безье подходят для интерактивной разработки: они правильно &mdash; другими словами, не существует, вызывающие кривая, становятся бесконечное или неудобным особенностей &mdash; , и они обычно эстетично :

![](beziers-images/beziersample.png "Пример кривой Безье")

Контурам символов на компьютере шрифтов, обычно определяются с помощью кривых Безье.

В статье Википедии [ **кривая Безье** ](https://en.wikipedia.org/wiki/B%C3%A9zier_curve) содержит некоторые полезные сведения. Термин *кривая Безье* на самом деле ссылается на семейство аналогичные кривых. SkiaSharp поддерживает три типа кривых Безье, называемых *третьего*, *квадратичной*и *conic*. Conic также называется *rational квадратичная*.

## <a name="the-cubic-bzier-curve"></a>Кривая Безье третьего порядка

Кривой является тип кривой Безье, большинство разработчиков считают когда предметом кривых Безье.

Вы можете добавить кривую Безье третьего порядка для `SKPath` с помощью [ `CubicTo` ](xref:SkiaSharp.SKPath.CubicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKPoint)) метод с тремя `SKPoint` параметры, или [ `CubicTo` ](xref:SkiaSharp.SKPath.CubicTo(System.Single,System.Single,System.Single,System.Single,System.Single,System.Single)) перегрузка с помощью отдельных `x` и `y` параметры:

```csharp
public void CubicTo (SKPoint point1, SKPoint point2, SKPoint point3)

public void CubicTo (Single x1, Single y1, Single x2, Single y2, Single x3, Single y3)
```

Кривая начинается в текущей точке контур. Полный кривую Безье третьего порядка определяется четырьмя точками:

- Начальная точка: текущей точки в профиль, или (0, 0), если `MoveTo` не был вызван
- Во-первых, точки управления: `point1` в `CubicTo` вызова
- Во-вторых, точки управления: `point2` в `CubicTo` вызова
- Конечная точка: `point3` в `CubicTo` вызова

Итоговый кривой начинается с начальной точки и заканчивается в конечной точке. Кривая обычно не проходит через две контрольные точки; Вместо этого элемент управления указывает функции, как магниты для извлечения кривой к их.

Лучший способ почувствовать, кривая Безье третьего порядка, службы "Экспериментирование". Это назначение **кривой Безье** страницы, который является производным от `InteractivePage`. [ **BezierCurvePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml) файл экземпляр `SKCanvasView` и `TouchEffect`. [ **BezierCurvePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml.cs) файл с выделенным кодом создается четыре `TouchPoint` объектов в конструкторе. `PaintSurface` Создает обработчик событий `SKPath` для подготовки к просмотру кривую Безье, в зависимости от четырех `TouchPoint` объектов, а также рисует пунктирную касательные из точки управления к конечным точкам:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Draw path with cubic Bezier curve
    using (SKPath path = new SKPath())
    {
        path.MoveTo(touchPoints[0].Center);
        path.CubicTo(touchPoints[1].Center,
                     touchPoints[2].Center,
                     touchPoints[3].Center);

        canvas.DrawPath(path, strokePaint);
    }

    // Draw tangent lines
    canvas.DrawLine(touchPoints[0].Center.X,
                    touchPoints[0].Center.Y,
                    touchPoints[1].Center.X,
                    touchPoints[1].Center.Y, dottedStrokePaint);

    canvas.DrawLine(touchPoints[2].Center.X,
                    touchPoints[2].Center.Y,
                    touchPoints[3].Center.X,
                    touchPoints[3].Center.Y, dottedStrokePaint);

    foreach (TouchPoint touchPoint in touchPoints)
    {
       touchPoint.Paint(canvas);
    }
}
```

Здесь выполняется на всех трех платформах:

[![](beziers-images/beziercurve-small.png "Тройной снимок экрана страницы кривой Безье")](beziers-images/beziercurve-large.png#lightbox "тройной снимок экрана страницы кривую Безье")

Математически кривая является третьего полинома. Кривая максимум пересекает прямую линию в трех местах. Начальной точки кривой всегда является касательной и в направлении, прямую линию с самого начала пункты первой контрольной точки. В конечной точке, кривая является всегда точки касания и в направлении, прямую линию от второго элемента управления в конечную точку.

Кривая Безье третьего порядка всегда связывает выпуклая четырехугольника осуществляется плавный переход подключении четырех точек. Это называется *выпуклая оболочка*. Если контрольные точки находятся на прямую линию между начальной и конечной точек, кривая Безье визуализацию как прямую линию. Но кривой также может пересекать, как показано на третьем снимке экрана.

Профиль путь может содержать несколько соединенных кривых Безье третьего, но соединение между двумя кривых Безье третьего порядка, будет smooth, только в том случае, если три аспекта colinear (то есть, находящихся на прямую линию):

- Вторая контрольная точка кривой первый
- Конечная точка первого кривой, которая также является точкой запуска второго кривой
- Первая контрольная точка кривой, вторая

В следующей статье на [ **данные пути SVG**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md), вы откроете для себя возможности для упрощения определения smooth соединенных кривых Безье.

Иногда бывает полезно знать, базовой параметрические уравнения, которые отображаются в кривую Безье третьего порядка. Для *t* от 0 до 1, параметрические уравнения таковы:

x(t) = (1 – t) ³x₀ + 3t (1 – t) ²x₁ + 3t² (1 – t) x₂ + t³x₃

y(t) = (1 – t) ³y₀ + 3t (1 – t) ²y₁ + 3t² (1 – t) y₂ + t³y₃

Самый высокий показатель степени 3 подтверждает, что они третьего polynomials. Легко убедитесь, что при `t` равен 0, точки (x₀ y₀), которая является начальной точкой и когда `t` равно 1, точку (x₃ y₃), который является конечной точкой. Практически начальную точку (для низких значениях `t`), Первая контрольная точка (x₁, y₁) имеет строгое влияет и рядом с конечной точкой (высокие значения из 't') вторая контрольная точка (x₂, y₂), существенно влияет.

## <a name="bezier-curve-approximation-to-circular-arcs"></a>Приближения кривой Безье в дуги

Иногда удобно использовать для подготовки к просмотру сегмент дуги кривую Безье. Кривая Безье третьего порядка приблизительный результат можно получить сегмент дуги очень хорошо до четверть круга, поэтому четырех соединенных кривых Безье можно определить круг целиком. Такое приближение рассматривается в две статьи, опубликованные более 25 лет назад:

> Tor Dokken, т. п., «Хорошее приближение кругов, кривых Безье кривизну непрерывное» *компьютера автоматизированного геометрические разработки 7* (1990 года), 33 – 41.

> Майкл Goldapp, «Аппроксимации дуг с третьего Polynomials» *автоматизированного проектирования геометрические 8* 227 238 (1991).

На следующей схеме показаны четыре точки с меткой `pto`, `pt1`, `pt2`, и `pt3` определение кривую Безье (показано красным цветом), соответствующий сегмент дуги:

![](beziers-images/bezierarc45.png "Приближение дуги в кривую Безье")

Строк из начальной и конечной точек для контрольных точек касательной к элементу управления circle и в кривую Безье, и они имеют длину *L*. Первая статья, обозначенной выше указывает, что кривая Безье рекомендации аппроксимирует сегмент дуги при этой длины, *L* вычисляется следующим образом:

L = 4 × tan(α / 4) / 3

На рисунке угла 45 градусов, поэтому L равно 0.265. В коде это значение будет умножено с требуемой радиус круга.

**Безье дуги** страницы позволяет экспериментировать с определением кривую Безье, для приближения дуги для углов диапазоне до 180 градусов. [ **BezierCircularArcPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml) файл экземпляр `SKCanvasView` и `Slider` для выбора угол. `PaintSurface` Обработчик событий в [ **BezierCircularArgPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml.cs) файл с выделенным кодом использует преобразование, чтобы задать точку (0, 0) относительно центральной части холста. Он рисует окружность центром в точке, для сравнения, а затем вычисляет две контрольные точки кривой Безье:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Translate to center
    canvas.Translate(info.Width / 2, info.Height / 2);

    // Draw the circle
    float radius = Math.Min(info.Width, info.Height) / 3;
    canvas.DrawCircle(0, 0, radius, blackStroke);

    // Get the value of the Slider
    float angle = (float)angleSlider.Value;

    // Calculate length of control point line
    float length = radius * 4 * (float)Math.Tan(Math.PI * angle / 180 / 4) / 3;

    // Calculate sin and cosine for half that angle
    float sin = (float)Math.Sin(Math.PI * angle / 180 / 2);
    float cos = (float)Math.Cos(Math.PI * angle / 180 / 2);

    // Find the end points
    SKPoint point0 = new SKPoint(-radius * sin, radius * cos);
    SKPoint point3 = new SKPoint(radius * sin, radius * cos);

    // Find the control points
    SKPoint point0Normalized = Normalize(point0);
    SKPoint point1 = point0 + new SKPoint(length * point0Normalized.Y,
                                          -length * point0Normalized.X);

    SKPoint point3Normalized = Normalize(point3);
    SKPoint point2 = point3 + new SKPoint(-length * point3Normalized.Y,
                                          length * point3Normalized.X);

    // Draw the points
    canvas.DrawCircle(point0.X, point0.Y, 10, blackFill);
    canvas.DrawCircle(point1.X, point1.Y, 10, blackFill);
    canvas.DrawCircle(point2.X, point2.Y, 10, blackFill);
    canvas.DrawCircle(point3.X, point3.Y, 10, blackFill);

    // Draw the tangent lines
    canvas.DrawLine(point0.X, point0.Y, point1.X, point1.Y, dottedStroke);
    canvas.DrawLine(point3.X, point3.Y, point2.X, point2.Y, dottedStroke);

    // Draw the Bezier curve
    using (SKPath path = new SKPath())
    {
        path.MoveTo(point0);
        path.CubicTo(point1, point2, point3);
        canvas.DrawPath(path, redStroke);
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

```

Начальная и конечная точки (`point0` и `point3`) из расчета за обычный параметрические уравнения круга. Так как элемент управления circle центрируется в (0, 0), эти точки могут также рассматриваться как радиального векторов в центре круга для длины окружности. Контрольные точки, в строках, которые являются касательной к элементу управления circle, поэтому они находятся под прямым углом в следующие радиального векторы. Вектор под прямым углом в другой — просто исходного вектора с местами координаты X и Y и один из них внесены отрицательное.

Ниже приведен программу на трех платформ с тремя различными углами зрения.

[![](beziers-images/beziercirculararc-small.png "Тройной снимок экрана страницы Безье дуги")](beziers-images/beziercirculararc-large.png#lightbox "тройной снимок экрана страницы дуги Безье")

Внимательно посмотрите на третий снимок экрана, и вы увидите, что кривая Безье особенно отличается от полукруга угол равен 180 градусов, когда на экране iOS показывает, что кажется под просто прекрасно квартал круг угол равен 90 градусов.

Вычисление координаты две контрольные точки достаточно прост, когда четверть круга ориентирован следующим образом:

![](beziers-images/bezierarc90.png "Приближение квартала окружности в кривую Безье")

Если радиус круга равна 100, затем *L* – 55, и это число легко запомнить.

**Возведение в квадрат круг** страницы анимирует рис между круг и квадрат. Элемент управления circle, приблизительно изобразить четыре кривых Безье, координаты которого отображаются в первом столбце данного определения массива в [ `SquaringTheCirclePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/SquaringTheCirclePage.cs) класса:

```csharp
public class SquaringTheCirclePage : ContentPage
{
    SKPoint[,] points =
    {
        { new SKPoint(   0,  100), new SKPoint(     0,    125), new SKPoint() },
        { new SKPoint(  55,  100), new SKPoint( 62.5f,  62.5f), new SKPoint() },
        { new SKPoint( 100,   55), new SKPoint( 62.5f,  62.5f), new SKPoint() },
        { new SKPoint( 100,    0), new SKPoint(   125,      0), new SKPoint() },
        { new SKPoint( 100,  -55), new SKPoint( 62.5f, -62.5f), new SKPoint() },
        { new SKPoint(  55, -100), new SKPoint( 62.5f, -62.5f), new SKPoint() },
        { new SKPoint(   0, -100), new SKPoint(     0,   -125), new SKPoint() },
        { new SKPoint( -55, -100), new SKPoint(-62.5f, -62.5f), new SKPoint() },
        { new SKPoint(-100,  -55), new SKPoint(-62.5f, -62.5f), new SKPoint() },
        { new SKPoint(-100,    0), new SKPoint(  -125,      0), new SKPoint() },
        { new SKPoint(-100,   55), new SKPoint(-62.5f,  62.5f), new SKPoint() },
        { new SKPoint( -55,  100), new SKPoint(-62.5f,  62.5f), new SKPoint() },
        { new SKPoint(   0,  100), new SKPoint(     0,    125), new SKPoint() }
    };
    ...
}
```

Второй столбец содержит координаты четырех кривых Безье, которые определяют квадрат которого области составляет приблизительно так же, как площади круга. (Рисование квадрат с *точное* область данного круга — это классический неразрешимой геометрические задача [возведение в квадрат круг](https://en.wikipedia.org/wiki/Squaring_the_circle).) Для подготовки к просмотру квадрат с кривых Безье, две контрольные точки для каждой кривой совпадают, и они colinear с начальной и конечной точками, кривая Безье отображается в виде прямой линии.

Третий столбец массива предназначен для интерполированные значения анимации. Страницы устанавливает таймер на 16 миллисекунд и `PaintSurface` обработчик вызывается такими темпами:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    canvas.Translate(info.Width / 2, info.Height / 2);
    canvas.Scale(Math.Min(info.Width / 300, info.Height / 300));

    // Interpolate
    TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
    float t = (float)(timeSpan.TotalSeconds % 3 / 3);   // 0 to 1 every 3 seconds
    t = (1 + (float)Math.Sin(2 * Math.PI * t)) / 2;     // 0 to 1 to 0 sinusoidally

    for (int i = 0; i < 13; i++)
    {
        points[i, 2] = new SKPoint(
            (1 - t) * points[i, 0].X + t * points[i, 1].X,
            (1 - t) * points[i, 0].Y + t * points[i, 1].Y);
    }

    // Create the path and draw it
    using (SKPath path = new SKPath())
    {
        path.MoveTo(points[0, 2]);

        for (int i = 1; i < 13; i += 3)
        {
            path.CubicTo(points[i, 2], points[i + 1, 2], points[i + 2, 2]);
        }
        path.Close();

        canvas.DrawPath(path, cyanFill);
        canvas.DrawPath(path, blueStroke);
    }
}
```

Точки интерполируются на основе sinusoidally oscillating значения `t`. Интерполированные точки затем используются для создания ряда из четырех соединенных кривых Безье. Вот анимацию на трех платформах, показывающее ход выполнения из круг в квадрат:

[![](beziers-images/squaringthecircle-small.png "Тройной снимок Squaring странице круг")](beziers-images/squaringthecircle-large.png#lightbox "тройной снимок Squaring странице круг")

Такой анимации бы невозможно без кривых, которые алгоритмически достаточно гибки для воспроизведения в виде прямых линий и дуг.

**Безье бесконечность** страницы также используются преимущества возможности кривой Безье, для приближения дуги окружности. Вот `PaintSurface` обработчик из [ `BezierInfinityPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierInfinityPage.cs) класса:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPath path = new SKPath())
    {
        path.MoveTo(0, 0);                                // Center
        path.CubicTo(  50,  -50,   95, -100,  150, -100); // To top of right loop
        path.CubicTo( 205, -100,  250,  -55,  250,    0); // To far right of right loop
        path.CubicTo( 250,   55,  205,  100,  150,  100); // To bottom of right loop
        path.CubicTo(  95,  100,   50,   50,    0,    0); // Back to center  
        path.CubicTo( -50,  -50,  -95, -100, -150, -100); // To top of left loop
        path.CubicTo(-205, -100, -250,  -55, -250,    0); // To far left of left loop
        path.CubicTo(-250,   55, -205,  100, -150,  100); // To bottom of left loop
        path.CubicTo( -95,  100,  -50,   50,    0,    0); // Back to center
        path.Close();

        SKRect pathBounds = path.Bounds;
        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(0.9f * Math.Min(info.Width / pathBounds.Width,
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

Это может быть хорошее упражнение для построения эти координаты на бумаге graph, чтобы увидеть, как они связаны. Знак бесконечности центрируется относительно точки (0, 0), а два цикла иметь центров (–150, 0) и (150, 0) и радиусы 100. В ряде `CubicTo` команды, вы увидите X координаты точек управления на значения –95 и –205 (эти значения являются –150 сложения и вычитания 55), 205 и 95 (150 сложения и вычитания 55), а также 250 и –250 правой и левой сторон. Единственное исключение — когда знак бесконечности пересекает самого в центре. В этом случае контрольные точки имеют координаты вместе с 50 и от – 50, чтобы выровнять кривой рядом с центром.

Ниже приведен знак бесконечности на всех трех платформах.

[![](beziers-images/bezierinfinity-small.png "Тройной снимок экрана страницы бесконечность Безье")](beziers-images/bezierinfinity-large.png#lightbox "тройной снимок экрана страницы бесконечность Безье")

Это отчасти гораздо легче к центру, чем знак бесконечности, преобразовываемый для просмотра **бесконечность Arc** странице из [ **три способа нарисовать дугу** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md) статьи.

## <a name="the-quadratic-bzier-curve"></a>Квадратичная кривая Безье

Квадратичная кривая Безье есть только одна контрольная точка и кривой определяется только трех точек: начальной точки, контрольная точка и конечную точку. Параметрические уравнения очень похожи на кривая Безье третьего порядка, за исключением того, что самый высокий показатель степени — 2, поэтому кривая является квадратичной полинома:

x(t) = (1 – t) ²x₀ + 2t (1 – t) x₁ + t²x₂

y(t) = (1 – t) ²y₀ + 2t (1 – t) y₁ + t²y₂

Для добавления квадратичной кривой Безье в путь, используйте [ `QuadTo` ](xref:SkiaSharp.SKPath.QuadTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint)) метод или [ `QuadTo` ](xref:SkiaSharp.SKPath.QuadTo(System.Single,System.Single,System.Single,System.Single)) перегрузка с помощью отдельных `x` и `y` координаты:

```csharp
public void QuadTo (SKPoint point1, SKPoint point2)

public void QuadTo (Single x1, Single y1, Single x2, Single y2)
```

Методы кривая из текущей позицией с целью `point2` с `point1` как контрольная точка.

Вы можете поэкспериментировать с кривых Безье второго порядка с **квадратичной кривой** страницу, которая очень похожа на **кривой Безье** странице, отличаясь только наличием только три точки касания. Вот `PaintSurface` обработчик в [ **QuadraticCurve.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/QuadraticCurvePage.xaml.cs) файл с выделенным кодом:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Draw path with quadratic Bezier
    using (SKPath path = new SKPath())
    {
        path.MoveTo(touchPoints[0].Center);
        path.QuadTo(touchPoints[1].Center,
                    touchPoints[2].Center);

        canvas.DrawPath(path, strokePaint);
    }

    // Draw tangent lines
    canvas.DrawLine(touchPoints[0].Center.X,
                    touchPoints[0].Center.Y,
                    touchPoints[1].Center.X,
                    touchPoints[1].Center.Y, dottedStrokePaint);

    canvas.DrawLine(touchPoints[1].Center.X,
                    touchPoints[1].Center.Y,
                    touchPoints[2].Center.X,
                    touchPoints[2].Center.Y, dottedStrokePaint);

    foreach (TouchPoint touchPoint in touchPoints)
    {
        touchPoint.Paint(canvas);
    }
}
```

И здесь он выполняется на всех трех платформах:

[![](beziers-images/quadraticcurve-small.png "Тройной снимок экрана страницы квадратичной кривой")](beziers-images/quadraticcurve-large.png#lightbox "тройной снимок экрана страницы квадратичной кривой")

Пунктирные линии располагаются по касательной к кривой в начальной и конечной точки и соответствовать в точке управления.

Квадратичных сплайнов подходит в том случае, если вам нужны кривую общие фигуры, но вы предпочитаете удобство точки только один элемент управления, а не два. Отображает более эффективно, чем любой другой кривой, именно он используется системой в Skia для подготовки к просмотру эллиптические дуги квадратичных сплайнов.

Тем не менее форму кривой Безье второго порядка не эллипса, поэтому несколько квадратичной Béziers необходимы для приближения эллиптической дуги. Вместо этого, квадратичных сплайнов – это сегмент параболы.

## <a name="the-conic-bzier-curve"></a>Conic кривую Безье

Кривая Безье conic &mdash; также называется rational квадратичная кривая Безье &mdash; — это относительно новые дополнение к семейству кривых Безье. Как квадратичная кривая Безье rational квадратичная кривая Безье включает в себя начальную точку, конечная точка и точка управления. Но также требует rational квадратичная кривая Безье *вес* значение. Он называется *rational* второго, так как коэффициенты включают параметрического формулы.

Параметрические уравнения для X и Y, коэффициенты, которые совместно используют же знаменатель. Вот уравнение знаменатель для *t* принимает значения от 0 к 1 и значение веса *w*:

d(t) = (1 – t) ² + 2wt(1 – t) + t²

В теории rational квадратичная может включать в себя три значения отдельный вес, по одной для каждого из трех условий, но их может быть упрощен до единственного значение веса в середине термина.

Параметрические уравнения для координат X и Y, аналогичны параметрические уравнения для квадратичных сплайнов, за исключением того, что термин среднего также включает в себя значение веса, и выражение делится на делитель:

x(t) = ((1 – t) ²x₀ + 2wt (1 – t) x₁ + t²x₂)) ÷ d(t)

y(t) = ((1 – t) ²y₀ + 2wt (1 – t) y₁ + t²y₂)) ÷ d(t)

Rational кривых Безье второго порядка, также называются *conics* так, как они могут точно представлять сегменты части любого раздела conic &mdash; гиперболы, параболы, эллипсов и кругов.

Чтобы добавить путь rational кривой Безье второго порядка, используйте [ `ConicTo` ](xref:SkiaSharp.SKPath.ConicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,System.Single)) метод или [ `ConicTo` ](xref:SkiaSharp.SKPath.ConicTo(System.Single,System.Single,System.Single,System.Single,System.Single)) перегрузка с помощью отдельных `x` и `y` координаты:

```csharp
public void ConicTo (SKPoint point1, SKPoint point2, Single weight)

public void ConicTo (Single x1, Single y1, Single x2, Single y2, Single weight)
```

Обратите внимание, что конечный `weight` параметра.

**Conic кривой** страница позволяет экспериментировать с этих кривых. Класс `ConicCurvePage` является производным от класса `InteractivePage`. [ **ConicCurvePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml) файл экземпляр `Slider` установите значение веса от – 2 до 2. [ **ConicCurvePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml.cs) создает файл с выделенным кодом трех `TouchPoint` объектов и `PaintSurface` обработчик просто отображает результирующий кривой с касательные к элементу управления следующие моменты.

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Draw path with conic curve
    using (SKPath path = new SKPath())
    {
        path.MoveTo(touchPoints[0].Center);
        path.ConicTo(touchPoints[1].Center,
                     touchPoints[2].Center,
                     (float)weightSlider.Value);

        canvas.DrawPath(path, strokePaint);
    }

    // Draw tangent lines
    canvas.DrawLine(touchPoints[0].Center.X,
                    touchPoints[0].Center.Y,
                    touchPoints[1].Center.X,
                    touchPoints[1].Center.Y, dottedStrokePaint);

    canvas.DrawLine(touchPoints[1].Center.X,
                    touchPoints[1].Center.Y,
                    touchPoints[2].Center.X,
                    touchPoints[2].Center.Y, dottedStrokePaint);

    foreach (TouchPoint touchPoint in touchPoints)
    {
        touchPoint.Paint(canvas);
    }
}
```

Здесь выполняется на всех трех платформах:

[![](beziers-images/coniccurve-small.png "Тройной снимок экрана страницы Conic кривой")](beziers-images/coniccurve-large.png#lightbox "тройной снимок экрана страницы Conic кривой")

Как вы видите, контрольная точка похоже на включение внесенных изменений кривой к более, если вес выше. Если вес равен нулю, кривая становится прямую линию от начальной точки в конечную точку.

В теории, разрешены отрицательных весов, а также привести к кривой возможность склонять *сейчас* из точки управления. Тем не менее, взвешивает – 1 или ниже причина знаменатель в параметрические уравнения для отрицательных значений из *t*. Возможно, по этой причине отрицательных весов учитываются в `ConicTo` методы. **Conic кривой** программа позволяет задать отрицательных весов, но как вы видите, поэкспериментировав, отрицательных весов имеют тот же эффект, что нулевой вес и привести к прямую линию к просмотру.

Это очень легко создавать производные контрольную точку и плотность `ConicTo` метод, чтобы нарисовать дугу до (но не включая) полукруга. На следующей схеме касательные из начальной и конечной точек соответствовать в точке управления.

![](beziers-images/conicarc.png "Отображают conic дуги дугу окружности")

Тригонометрические функции можно использовать для определения расстояния контрольной точки из центра: это радиус круга, деленное косинус угла половину α. Чтобы нарисовать дугу между начальной и конечной точек, присвойте весу этой же косинус половину угла. Обратите внимание на то, что если угол в 180 градусов, затем касательные никогда не соответствуют и вес равен нулю. Но для углов меньше, чем 180 градусов, математические работает нормально.

**Conic дуги** страница демонстрирует это. [ **ConicCircularArc.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml) файл экземпляр `Slider` для выбора угол. `PaintSurface` Обработчик в [ **ConicCircularArc.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml.cs) файл с выделенным кодом вычисляет контрольная точка и вес:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Translate to center
    canvas.Translate(info.Width / 2, info.Height / 2);

    // Draw the circle
    float radius = Math.Min(info.Width, info.Height) / 4;
    canvas.DrawCircle(0, 0, radius, blackStroke);

    // Get the value of the Slider
    float angle = (float)angleSlider.Value;

    // Calculate sin and cosine for half that angle
    float sin = (float)Math.Sin(Math.PI * angle / 180 / 2);
    float cos = (float)Math.Cos(Math.PI * angle / 180 / 2);

    // Find the points and weight
    SKPoint point0 = new SKPoint(-radius * sin, radius * cos);
    SKPoint point1 = new SKPoint(0, radius / cos);
    SKPoint point2 = new SKPoint(radius * sin, radius * cos);
    float weight = cos;

    // Draw the points
    canvas.DrawCircle(point0.X, point0.Y, 10, blackFill);
    canvas.DrawCircle(point1.X, point1.Y, 10, blackFill);
    canvas.DrawCircle(point2.X, point2.Y, 10, blackFill);

    // Draw the tangent lines
    canvas.DrawLine(point0.X, point0.Y, point1.X, point1.Y, dottedStroke);
    canvas.DrawLine(point2.X, point2.Y, point1.X, point1.Y, dottedStroke);

    // Draw the conic
    using (SKPath path = new SKPath())
    {
        path.MoveTo(point0);
        path.ConicTo(point1, point2, weight);
        canvas.DrawPath(path, redStroke);
    }
}
```

Как видите, нет никакой разницы между `ConicTo` путь, выделяются красным цветом и базовой круг, отображаемый для ссылки:

[![](beziers-images/coniccirculararc-small.png "Тройной снимок экрана страницы Conic дуги")](beziers-images/coniccirculararc-large.png#lightbox "тройной снимок экрана страницы Conic дугу окружности")

Но задать угол в 180 градусов, а также математика "fail".

Его в этом случае является нежелательным, `ConicTo` не поддерживает в случае отрицательных весов, поскольку теоретически (с учетом параметрические уравнения), элемент управления circle можно сделать с помощью другой вызов `ConicTo` с одной точки, но отрицательное значение веса. Это сделало бы возможным создание весь круг с помощью только два `ConicTo` на любой угол между (но не включая) основе кривых нуля градусов и 180 градусов.


## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
