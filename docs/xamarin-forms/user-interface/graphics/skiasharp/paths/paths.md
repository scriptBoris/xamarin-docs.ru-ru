---
title: Основные сведения о пути в SkiaSharp
description: В этой статье исследует объект SkiaSharp SKPath объединения соединенных линий и кривых и демонстрирует это с помощью примера кода.
ms.prod: xamarin
ms.assetid: A7EDA6C2-3921-4021-89F3-211551E430F1
ms.technology: xamarin-skiasharp
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 3c07614c12fb503638d3d5e63b24eb5367ba691a
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615539"
---
# <a name="path-basics-in-skiasharp"></a>Основные сведения о пути в SkiaSharp

_Изучение объекта SkiaSharp SKPath объединения соединенных линий и кривых_

Одним из наиболее важных возможностей пути графики является возможность определять, когда должны быть подключены несколько строк, и когда они не должны быть подключены. Разница может быть довольно отображается, как показывают верхние края этих двух треугольниках:

![](paths-images/connectedlinesexample.png "Двух треугольниках, с различиями между строками в подключенном и автономном")

Графический путь инкапсулирован с [ `SKPath` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath/) объекта. Путь состоит из одного или нескольких *контуров*. Каждый профиль — это коллекция *подключенных* прямых и изогнутых линий. Профили не подключены друг к другу, но они могут визуально перекрываться. Иногда один профиль может перекрывать сам себя.

Профиль, обычно начинается с вызова следующих метода `SKPath`:

- `MoveTo` Чтобы начать новый профиль

Аргумент этого метода является единственной точкой, который можно выразить как `SKPoint` значение или как отдельный X и Y координаты. `MoveTo` Вызов устанавливает точку в начале контур и первоначальный *текущей точки*. Можно вызвать следующие методы для продолжения профиль с линий или кривых из текущего расположения на момент, указанный в методе, которая становится новой текущей точки:

- `LineTo` Чтобы добавить прямой путь
- `ArcTo` для добавления дуги, который является строкой в окружности эллипса или круга
- `CubicTo` Чтобы добавить сплайна Безье третьего порядка
- `QuadTo` для добавления квадратичной кривой Безье
- `ConicTo` Чтобы добавить rational квадратичная кривая Безье, которой можно точно визуализировать conic разделы (кнопку с многоточием, параболы и гиперболы)

Эти пять методов не содержат все сведения, необходимые для описания линий или кривых. Каждый из этих пяти методов работает совместно с текущей точкой установить непосредственно перед вызовом метода. Например `LineTo` метод добавляет прямой путь профиль на основе текущей точки, поэтому параметра `LineTo` является единой точкой отказа.

`SKPath` Класс также определяет методы, которые имеют те же имена, что эти шесть методов, но с `R` в начале:

- `RMoveTo`
- `RLineTo`
- `RArcTo`
- `RCubicTo`
- `RQuadTo`
- `RConicTo`

`R` Расшифровывается *относительный*. Они имеют тот же синтаксис, что и соответствующие методы без `R` , но являются относительно текущей точки. Это удобно для рисования аналогичные части пути в методе, который можно вызывать несколько раз.

Профиль заканчивается другой вызов `MoveTo` или `RMoveTo`, начинается новый профиль, или вызов `Close`, который закрывает контур. `Close` Метод автоматически добавляет прямую линию из текущего расположения до первой точки контура и отмечает путь как закрытые, это означает, что он будет отображен без любой концы штрихов.

Разница между замкнутые и незамкнутые контуры показана в **двух контуров треугольник** странице, которая использует `SKPath` объекта с помощью двух контуров для подготовки к просмотру двух треугольниках. Первый профиль является открытым и закрывается второй. Вот [ `TwoTriangleContours` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/TwoTriangleContoursPage.cs) класса:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Create the path
    SKPath path = new SKPath();

    // Define the first contour
    path.MoveTo(0.5f * info.Width, 0.1f * info.Height);
    path.LineTo(0.2f * info.Width, 0.4f * info.Height);
    path.LineTo(0.8f * info.Width, 0.4f * info.Height);
    path.LineTo(0.5f * info.Width, 0.1f * info.Height);

    // Define the second contour
    path.MoveTo(0.5f * info.Width, 0.6f * info.Height);
    path.LineTo(0.2f * info.Width, 0.9f * info.Height);
    path.LineTo(0.8f * info.Width, 0.9f * info.Height);
    path.Close();

    // Create two SKPaint objects
    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Magenta,
        StrokeWidth = 50
    };

    SKPaint fillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Cyan
    };

    // Fill and stroke the path
    canvas.DrawPath(path, fillPaint);
    canvas.DrawPath(path, strokePaint);
}
```

Первый профиль состоит из вызова [ `MoveTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.MoveTo/p/System.Single/System.Single/) с помощью координат X и Y, а не `SKPoint` значения с трех вызовов [ `LineTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.LineTo/p/System.Single/System.Single/) для рисуется три стороны треугольник. Второй профиль содержит только два вызова `LineTo` , но ее завершения профиль с помощью вызова [ `Close` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.Close()/), который закрывает контур. Различие важно:

[![](paths-images/twotrianglecontours-small.png "Тройной снимок экрана страницы двух контуров треугольник")](paths-images/twotrianglecontours-large.png#lightbox "тройной снимок экрана страницы двух контуров треугольник")

Как вы видите, первый профиль очевидно, что представляет собой ряд три соединенных линий, но окончания не сможет подключиться с самого начала. В верхней перекрываться две строки. Второй профиль, очевидно, что закрыта и осуществлялось с помощью одного меньше `LineTo` вызывает, поскольку `Close` метод автоматически добавляет последней строке, чтобы закрыть контур.

`SKCanvas` определяет только один [ `DrawPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawPath/p/SkiaSharp.SKPath/SkiaSharp.SKPaint/) метод, который в этой демонстрации вызывается дважды для заполнения и обводки путь. Все профили заполняются, даже те, которые не закрыты. В целях заполнения незамкнутых прямой предполагается, что существует между начальной и конечной точек из контуров. При удалении последнего `LineTo` из первого профиль, или удалите `Close` вызов из второй профиль, а каждый профиль будет иметь только двух сторон но будет заполняться, как если бы он был треугольника.

`SKPath` Определяет множество методов и свойств. Следующие методы позволяют добавлять контуров весь путь, который может быть закрыт или не закрывается в зависимости от метода:

- `AddRect`
- [`AddRoundedRect`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddRoundedRect/p/SkiaSharp.SKRect/System.Single/System.Single/SkiaSharp.SKPathDirection/)
- [`AddCircle`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddCircle/p/System.Single/System.Single/System.Single/SkiaSharp.SKPathDirection/)
- [`AddOval`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddOval/p/SkiaSharp.SKRect/SkiaSharp.SKPathDirection/)
- [`AddArc`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddArc/p/SkiaSharp.SKRect/System.Single/System.Single/) Чтобы добавить кривую на длины окружности эллипс
- `AddPath` Чтобы добавить другой путь к текущему пути
- [`AddPathReverse`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddPathReverse/p/SkiaSharp.SKPath/) Чтобы добавить другой путь в обратном порядке

Имейте в виду, что `SKPath` объект определяет только геометрического объекта &mdash; последовательность точек и подключений. Только тогда, когда `SKPath` объединяется с `SKPaint` объект — это путь к просмотру с определенным цветом, толщина и т. д. Кроме того, имейте в виду, что `SKPaint` объект, передаваемый в `DrawPath` метод определяет характеристики весь путь. Если вы хотите нарисуйте что-нибудь, требующих несколько цветов, необходимо использовать отдельный путь для каждого цвета.

Определяется так же, как внешний вид начало и конец строки определяется наконечник штриха, внешний вид соединения между двумя строками *соединение штриха*. Пользователь указывает задавая [ `StrokeJoin` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.StrokeJoin/) свойство `SKPaint` члену [ `SKStrokeJoin` ](https://developer.xamarin.com/api/type/SkiaSharp.SKStrokeJoin/) перечисления:

- [`Miter`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeJoin.Miter/) Маленькие умные соединения
- [`Round`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeJoin.Round/) для соединения со скругленными
- [`Bevel`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeJoin.Bevel/) для объединения отрезать off

**Соединения Stroke** странице показан этих трех обводки соединения с кодом, аналогичную **концы штрихов** страницы. Это `PaintSurface` обработчик событий в [ `StrokeJoinsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/StrokeJoinsPage.cs) класса:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 75,
        TextAlign = SKTextAlign.Right
    };

    SKPaint thickLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Orange,
        StrokeWidth = 50
    };

    SKPaint thinLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 2
    };

    float xText = info.Width - 100;
    float xLine1 = 100;
    float xLine2 = info.Width - xLine1;
    float y = 2 * textPaint.FontSpacing;
    string[] strStrokeJoins = { "Miter", "Round", "Bevel" };

    foreach (string strStrokeJoin in strStrokeJoins)
    {
        // Display text
        canvas.DrawText(strStrokeJoin, xText, y, textPaint);

        // Get stroke-join value
        SKStrokeJoin strokeJoin;
        Enum.TryParse(strStrokeJoin, out strokeJoin);

        // Create path
        SKPath path = new SKPath();
        path.MoveTo(xLine1, y - 80);
        path.LineTo(xLine1, y + 80);
        path.LineTo(xLine2, y + 80);

        // Display thick line
        thickLinePaint.StrokeJoin = strokeJoin;
        canvas.DrawPath(path, thickLinePaint);

        // Display thin line
        canvas.DrawPath(path, thinLinePaint);
        y += 3 * textPaint.FontSpacing;
    }
}
```

Ниже приведен программу на трех платформ.

[![](paths-images/strokejoins-small.png "Тройной снимок экрана страницы соединяет Stroke")](paths-images/strokejoins-large.png#lightbox "тройной снимок экрана страницы соединяет штриха")

Фацетное соединение состоит из sharp точки, где строки подключения. Если две строки присоединяются к небольшой углом, фацетное соединение может быть довольно длинными. Чтобы предотвратить слишком длинные фацетного соединения, длина фацетное соединение ограничивается значение [ `StrokeMiter` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.StrokeMiter/) свойство `SKPaint`. Фацетного соединения, в которых превышает длину исчезает станет скошенное соединение.


## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
