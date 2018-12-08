---
title: Сведения о пути и перечисление
description: В этой статье объясняется, как получить сведения о пути SkiaSharp и перечисление содержимого и демонстрирует это с помощью примера кода.
ms.prod: xamarin
ms.assetid: 8E8C5C6A-F324-4155-8652-7A77D231B3E5
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 09/12/2017
ms.openlocfilehash: 98975d51c31b8e8c52d184c631194388cd6cfa87
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53053904"
---
# <a name="path-information-and-enumeration"></a>Сведения о пути и перечисление

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_Получение сведений о пути и перечисление содержимого_

[ `SKPath` ](xref:SkiaSharp.SKPath) Класс определяет несколько свойств и методов, которые позволяют получить сведения о пути. [ `Bounds` ](xref:SkiaSharp.SKPath.Bounds) И [ `TightBounds` ](xref:SkiaSharp.SKPath.TightBounds) свойства (и связанные методы) получить metrical размеры пути. [ `Contains` ](xref:SkiaSharp.SKPath.Contains(System.Single,System.Single)) Метод позволяет определить, если определенный момент находится в пределах контура.

Иногда полезно определить, общая длина всех линий и кривых линий, составляющих путь. Вычисление Эта длина не алгоритмически простой задачей, поэтому целый класс с именем [ `PathMeasure` ](xref:SkiaSharp.SKPathMeasure) затрачивается на него.

Полезно также иногда для получения графических операций и точки, составляющие путь. Поначалу это средство может показаться ненужной: Если приложение уже создано путь, программа уже знает содержимое. Тем не менее, вы уже видели, что пути могут также создаваться с [эффекты пути](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) и путем преобразования [строки текста в контуры](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md). Можно также получить рисования операции и точки, составляющие эти пути. Один из вариантов — применить алгоритмического преобразование для всех точек, например, чтобы обтекание полушария:

![](information-images/pathenumerationsample.png "Перенос на полушария текста")

## <a name="getting-the-path-length"></a>Получение длины пути

В этой статье [ **путей и текста** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md) вы узнали, как использовать [ `DrawTextOnPath` ](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) метод для отображения строку текста, в которых базовых показателей следует за курс пути. Но что делать, если вы хотите размер текста, таким образом, чтобы он точно соответствует путь? Рисование текста вокруг круга очень просто, так как длина окружности круга прост, для которого требуется вычислить. Но длины окружности эллипса или длина параметра кривую Безье не так просто.

[ `SKPathMeasure` ](xref:SkiaSharp.SKPathMeasure) Класс может помочь. [Конструктор](xref:SkiaSharp.SKPathMeasure.%23ctor(SkiaSharp.SKPath,System.Boolean,System.Single)) принимает `SKPath` аргумента и [ `Length` ](xref:SkiaSharp.SKPathMeasure.Length) свойство открывает его длины.

Этот класс показан в **длина пути** образца, который основан на **кривой Безье** страницы. [ **PathLengthPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml) файл является производным от `InteractivePage` и включает в себя сенсорный интерфейс:

```xaml
<local:InteractivePage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       xmlns:local="clr-namespace:SkiaSharpFormsDemos"
                       xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
                       xmlns:tt="clr-namespace:TouchTracking"
                       x:Class="SkiaSharpFormsDemos.Curves.PathLengthPage"
                       Title="Path Length">
    <Grid BackgroundColor="White">
        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction" />
        </Grid.Effects>
    </Grid>
</local:InteractivePage>
```

[ **PathLengthPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml.cs) файл с выделенным кодом позволяет переместить четыре точки касания, для определения конечных точек и контрольные точки кривой Безье третьего порядка. Три поля определяют строку текста `SKPaint` объекта и рассчитанную ширину текста:

```csharp
public partial class PathLengthPage : InteractivePage
{
    const string text = "Compute length of path";

    static SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Black,
        TextSize = 10,
    };

    static readonly float baseTextWidth = textPaint.MeasureText(text);
    ...
}
```

`baseTextWidth` Поле имеет ширину текста, на основе `TextSize` параметр, равный 10.

`PaintSurface` Обработчик рисует кривую Безье и изменяет размер текста вдоль его полная длина:

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

        // Get path length
        SKPathMeasure pathMeasure = new SKPathMeasure(path, false, 1);

        // Find new text size
        textPaint.TextSize = pathMeasure.Length / baseTextWidth * 10;

        // Draw text on path
        canvas.DrawTextOnPath(text, path, 0, 0, textPaint);
    }
    ...
}
```

`Length` Свойство созданной `SKPathMeasure` объект получает длину пути. Делится на длину пути `baseTextWidth` значение (то есть ширину текста, в соответствии с размером текста, 10) и затем умножается на размер базового шрифта 10. Результатом является новый размер текста для отображения текста по этому пути:

[![](information-images/pathlength-small.png "Тройной снимок экрана страницы длина пути")](information-images/pathlength-large.png#lightbox "тройной снимок экрана страницы длина пути")

Как для кривой Безье возвращает длинный или короткий, вы увидите изменение размера текста.

## <a name="traversing-the-path"></a>Обход путь

`SKPathMeasure` можно сделать больше, чем просто мер длину пути. Для любого значения между 0 и длину пути `SKPathMeasure` объекта можно получить позицию на путь, а касательной к кривой путь в этот момент. Тангенс доступен в виде вектора в виде `SKPoint` объекта или как поворот, инкапсулированных в `SKMatrix` объекта. Ниже приведены методы `SKPathMeasure` , получить эту информацию в различных и гибкие способы:

```csharp
Boolean GetPosition (Single distance, out SKPoint position)

Boolean GetTangent (Single distance, out SKPoint tangent)

Boolean GetPositionAndTangent (Single distance, out SKPoint position, out SKPoint tangent)

Boolean GetMatrix (Single distance, out SKMatrix matrix, SKPathMeasureMatrixFlags flag)
```

Члены [ `SKPathMeasureMatrixFlags` ](xref:SkiaSharp.SKPathMeasureMatrixFlags) перечисления являются:

- `GetPosition`
- `GetTangent`
- `GetPositionAndTangent`

**Половину канала видел** страницы анимирует Схематическое изображение на видел, кажется вперед и назад Познакомьтесь кривую Безье третьего порядка, что:

[![](information-images/unicyclehalfpipe-small.png "Тройной снимок экрана страницы половину канала видел")](information-images/unicyclehalfpipe-large.png#lightbox "тройной снимок экрана страницы видел половина-канала")

`SKPaint` Объект, используемый для Обводка половина канала и видел определяется как поле в [ `UnicycleHalfPipePage` ]() класса. Также определен `SKPath` объект для видел:

```csharp
public class UnicycleHalfPipePage : ContentPage
{
    ...
    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 3,
        Color = SKColors.Black
    };

    SKPath unicyclePath = SKPath.ParseSvgPathData(
        "M 0 0" +
        "A 25 25 0 0 0 0 -50" +
        "A 25 25 0 0 0 0 0 Z" +
        "M 0 -25 L 0 -100" +
        "A 15 15 0 0 0 0 -130" +
        "A 15 15 0 0 0 0 -100 Z" +
        "M -25 -85 L 25 -85");
    ...
}
```

Этот класс содержит стандартный переопределений `OnAppearing` и `OnDisappearing` методы для анимации. `PaintSurface` Обработчик создает путь к половине канала, а затем выводит его. `SKPathMeasure` Объект создается на основе на этом пути:

```csharp
public class UnicycleHalfPipePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath pipePath = new SKPath())
        {
            pipePath.MoveTo(50, 50);
            pipePath.CubicTo(0, 1.25f * info.Height,
                             info.Width - 0, 1.25f * info.Height,
                             info.Width - 50, 50);

            canvas.DrawPath(pipePath, strokePaint);

            using (SKPathMeasure pathMeasure = new SKPathMeasure(pipePath))
            {
                float length = pathMeasure.Length;

                // Animate t from 0 to 1 every three seconds
                TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
                float t = (float)(timeSpan.TotalSeconds % 5 / 5);

                // t from 0 to 1 to 0 but slower at beginning and end
                t = (float)((1 - Math.Cos(t * 2 * Math.PI)) / 2);

                SKMatrix matrix;
                pathMeasure.GetMatrix(t * length, out matrix,
                                      SKPathMeasureMatrixFlags.GetPositionAndTangent);

                canvas.SetMatrix(matrix);
                canvas.DrawPath(unicyclePath, strokePaint);
            }
        }
    }
}
```

`PaintSurface` Обработчик вычисляет значение `t` , переходит от 0 на 1 каждые пять секунд. Затем он использует `Math.Cos` функцию для преобразования, значение `t` которое меняется в диапазоне от 0 до 1 и значение 0, где 0 соответствует видел в начале в верхнем левом углу, при 1 соответствует видел в правом верхнем углу. Косинус вызывает скорость медленных в верхней части канала и быстрым в нижней.

Обратите внимание, что это значение из `t` должен быть умножен длина пути для первого аргумента `GetMatrix`. Она применяется к `SKCanvas` объект для рисования контура видел.

## <a name="enumerating-the-path"></a>Перечисление путь

Два встроенных классов `SKPath` позволяют перечислить содержимое пути. Эти классы являются [ `SKPath.Iterator` ](xref:SkiaSharp.SKPath.Iterator) и [ `SKPath.RawIterator` ](xref:SkiaSharp.SKPath.RawIterator). Эти два класса являются очень похожи, но `SKPath.Iterator` могут исключать элементы в пути с нулевой длиной или близко к нулевую длину. `RawIterator` Используется в следующем примере.

Вы можете получить объект типа `SKPath.RawIterator` путем вызова [ `CreateRawIterator` ](xref:SkiaSharp.SKPath.CreateRawIterator) метод `SKPath`. Перечисление путь достигается посредством вызова многократно [ `Next` ](xref:SkiaSharp.SKPath.RawIterator.Next*) метод. Передать ему массив из четырех `SKPoint` значения:

```csharp
SKPoint[] points = new SKPoint[4];
...
SKPathVerb pathVerb = rawIterator.Next(points);
```

`Next` Метод возвращает член [ `SKPathVerb` ](xref:SkiaSharp.SKPathVerb) тип перечисления. Эти значения указывают определенной команды рисования в пути. Количество допустимых точек, вставлены в массиве зависит от этой команды:

- `Move` с точки
- `Line` с двумя точками
- `Cubic` с четырьмя точками
- `Quad` с тремя точками
- `Conic` с тремя точками (а также выполнить [ `ConicWeight` ](xref:SkiaSharp.SKPath.RawIterator.ConicWeight*) метод веса)
- `Close` с одной точкой
- `Done`

`Done` Команда указывает, что полный путь перечисления.

Обратите внимание, что не `Arc` команд. Это означает, что все дуги преобразуются в кривых Безье при добавлении к пути.

Некоторые сведения в `SKPoint` массива является избыточным. Например если `Move` следуют глагол `Line` команды, а затем первый из двух точек, которые сопровождают `Line` совпадает со значением `Move` точки. На практике эта избыточность очень полезно. Если вы получили `Cubic` глагол, сопровождается все четыре точки, определяющие кривая Безье третьего порядка. Сохранить текущую позицию установленные предыдущей команды не нужно.

Проблемный команда, однако является `Close`. Эта команда рисует прямую линию от текущей позиции в начале профиль, установить ранее с помощью `Move` команды. В идеальном случае `Close` команды должен предоставить этих двух точек, а не только одну точку. Что еще хуже является то, что сопутствующий точки `Close` команда всегда является (0, 0). При перечислении по данному пути, возможно, вам потребуется сохранить `Move` точки и текущей позиции.

## <a name="enumerating-flattening-and-malforming"></a>Перечисление, выравнивание и Malforming

Для применения алгоритма иногда рекомендуется преобразовать путь к malform их каким-либо образом:

![](information-images/pathenumerationsample.png "Перенос на полушария текста")

Большинство этих букв состоят из прямых линий, но эти прямые линии было очевидно преминет кривых. Как такое возможно?

Ключом является то, что исходные прямые линии разбиваются на ряд небольших прямых линий. Затем эти меньшего размера отдельных прямых линий можно управлять различными способами, в одну кривую. 

Для оптимизации этого процесса [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) образец содержит статический [ `PathExtensions` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathExtensions.cs) класса `Interpolate` метод, который разбивает прямой линии в множество коротких строк, которые являются только одна единица в длину. Кроме того класс содержит несколько методов, которые преобразуют три типа кривых Безье на ряд мелких прямых линий, которые аппроксимируют кривой. (Параметрического формулы были представлены в статье [ **три типа кривых Безье**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/beziers.md).) Этот процесс называется _сведение_ кривой:

```csharp
static class PathExtensions
{
    ...
    static SKPoint[] Interpolate(SKPoint pt0, SKPoint pt1)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float x = (1 - t) * pt0.X + t * pt1.X;
            float y = (1 - t) * pt0.Y + t * pt1.Y;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static SKPoint[] FlattenCubic(SKPoint pt0, SKPoint pt1, SKPoint pt2, SKPoint pt3)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1) + Length(pt1, pt2) + Length(pt2, pt3));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float x = (1 - t) * (1 - t) * (1 - t) * pt0.X +
                        3 * t * (1 - t) * (1 - t) * pt1.X +
                        3 * t * t * (1 - t) * pt2.X +
                        t * t * t * pt3.X;
            float y = (1 - t) * (1 - t) * (1 - t) * pt0.Y +
                        3 * t * (1 - t) * (1 - t) * pt1.Y +
                        3 * t * t * (1 - t) * pt2.Y +
                        t * t * t * pt3.Y;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static SKPoint[] FlattenQuadratic(SKPoint pt0, SKPoint pt1, SKPoint pt2)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1) + Length(pt1, pt2));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float x = (1 - t) * (1 - t) * pt0.X + 2 * t * (1 - t) * pt1.X + t * t * pt2.X;
            float y = (1 - t) * (1 - t) * pt0.Y + 2 * t * (1 - t) * pt1.Y + t * t * pt2.Y;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static SKPoint[] FlattenConic(SKPoint pt0, SKPoint pt1, SKPoint pt2, float weight)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1) + Length(pt1, pt2));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float denominator = (1 - t) * (1 - t) + 2 * weight * t * (1 - t) + t * t;
            float x = (1 - t) * (1 - t) * pt0.X + 2 * weight * t * (1 - t) * pt1.X + t * t * pt2.X;
            float y = (1 - t) * (1 - t) * pt0.Y + 2 * weight * t * (1 - t) * pt1.Y + t * t * pt2.Y;
            x /= denominator;
            y /= denominator;
        }

        return points;
    }

    static double Length(SKPoint pt0, SKPoint pt1)
    {
        return Math.Sqrt(Math.Pow(pt1.X - pt0.X, 2) + Math.Pow(pt1.Y - pt0.Y, 2));
    }
}
```

Все эти методы ссылается на метод расширения `CloneWithTransform` также включены в этот класс и показано ниже. Этот метод создает точную копию пути, перечисление команды пути и создав новый путь на основе данных. Тем не менее, новый путь состоит только из `MoveTo` и `LineTo` вызовов. Все прямые линии и кривые уменьшаются на ряд мелких строк.

При вызове `CloneWithTransform`, передается методу `Func<SKPoint, SKPoint>`, который является функцией с `SKPaint` параметром, возвращающим `SKPoint` значение. Эта функция вызывается для каждой точки для применения пользовательского алгоритма преобразования:

```csharp
static class PathExtensions
{
    public static SKPath CloneWithTransform(this SKPath pathIn, Func<SKPoint, SKPoint> transform)
    {
        SKPath pathOut = new SKPath();

        using (SKPath.RawIterator iterator = pathIn.CreateRawIterator())
        {
            SKPoint[] points = new SKPoint[4];
            SKPathVerb pathVerb = SKPathVerb.Move;
            SKPoint firstPoint = new SKPoint();
            SKPoint lastPoint = new SKPoint();

            while ((pathVerb = iterator.Next(points)) != SKPathVerb.Done)
            {
                switch (pathVerb)
                {
                    case SKPathVerb.Move:
                        pathOut.MoveTo(transform(points[0]));
                        firstPoint = lastPoint = points[0];
                        break;

                    case SKPathVerb.Line:
                        SKPoint[] linePoints = Interpolate(points[0], points[1]);

                        foreach (SKPoint pt in linePoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[1];
                        break;

                    case SKPathVerb.Cubic:
                        SKPoint[] cubicPoints = FlattenCubic(points[0], points[1], points[2], points[3]);

                        foreach (SKPoint pt in cubicPoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[3];
                        break;

                    case SKPathVerb.Quad:
                        SKPoint[] quadPoints = FlattenQuadratic(points[0], points[1], points[2]);

                        foreach (SKPoint pt in quadPoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[2];
                        break;

                    case SKPathVerb.Conic:
                        SKPoint[] conicPoints = FlattenConic(points[0], points[1], points[2], iterator.ConicWeight());

                        foreach (SKPoint pt in conicPoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[2];
                        break;

                    case SKPathVerb.Close:
                        SKPoint[] closePoints = Interpolate(lastPoint, firstPoint);

                        foreach (SKPoint pt in closePoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        firstPoint = lastPoint = new SKPoint(0, 0);
                        pathOut.Close();
                        break;
                }
            }
        }
        return pathOut;
    }
    ...
}
```

Так как путь клонированный уменьшается до мелких прямых линий, функция преобразования имеет возможность преобразуется прямых линий и кривых.

Обратите внимание на то, что метод сохраняет первая точка каждого профиля в переменной с именем `firstPoint` и текущей позиции после каждой команды в переменной `lastPoint`. Эти переменные необходимы для создания заключительных строку при `Close` встречается команда.

**GlobularText** пример использует этот метод расширения, на первый взгляд обтекание полушария в объемные эффекты:

[![](information-images/globulartext-small.png "Тройной снимок экрана страницы Globular текст")](information-images/globulartext-large.png#lightbox "тройной снимок экрана страницы Globular текста")

[ `GlobularTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/GlobularTextPage.cs) Конструктор класса выполняет это преобразование. Он создает `SKPaint` объекта для текста, а затем получает `SKPath` объекта из `GetTextPath` метод. Это путь, передаваемый `CloneWithTransform` метод расширения, а также функцию преобразования:

```csharp
public class GlobularTextPage : ContentPage
{
    SKPath globePath;

    public GlobularTextPage()
    {
        Title = "Globular Text";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        using (SKPaint textPaint = new SKPaint())
        {
            textPaint.Typeface = SKTypeface.FromFamilyName("Times New Roman");
            textPaint.TextSize = 100;

            using (SKPath textPath = textPaint.GetTextPath("HELLO", 0, 0))
            {
                SKRect textPathBounds;
                textPath.GetBounds(out textPathBounds);

                globePath = textPath.CloneWithTransform((SKPoint pt) =>
                {
                    double longitude = (Math.PI / textPathBounds.Width) *
                                            (pt.X - textPathBounds.Left) - Math.PI / 2;
                    double latitude = (Math.PI / textPathBounds.Height) *
                                            (pt.Y - textPathBounds.Top) - Math.PI / 2;

                    longitude *= 0.75;
                    latitude *= 0.75;

                    float x = (float)(Math.Cos(latitude) * Math.Sin(longitude));
                    float y = (float)Math.Sin(latitude);

                    return new SKPoint(x, y);
                });
            }
        }
    }
    ...
}
```

Функция преобразования сначала вычисляет два значения с именем `longitude` и `latitude` диапазоне от π/2, сверху и слева от текста, π/2, справа и внизу текста. Диапазон этих значений не визуально удовлетворительные, поэтому они сокращаются путем умножения 0,75. (Попробуйте выполнить код без в настройках. Текст, становится слишком запутанные в Северной и Южной полюса и слишком тонким по сторонам.) Эти трехмерные координаты на сферической преобразуются в двухмерной `x` и `y` координаты стандартные формулы.

Новый путь хранится в виде поля. `PaintSurface` Обработчик просто должна центрирование и масштабирование путь для его отображения на экране:

```csharp
public class GlobularTextPage : ContentPage
{
    SKPath globePath;
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint pathPaint = new SKPaint())
        {
            pathPaint.Style = SKPaintStyle.Fill;
            pathPaint.Color = SKColors.Blue;
            pathPaint.StrokeWidth = 3;
            pathPaint.IsAntialias = true;

            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.Scale(0.45f * Math.Min(info.Width, info.Height));     // radius
            canvas.DrawPath(globePath, pathPaint);
        }
    }
}
```

Это очень универсальный метод. Если массив эффекты пути, описано в разделе [ **эффекты пути** ](effects.md) статье довольно не охватывают что-то показалось, должны быть включены, это способ заполнения промежутков.

## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
