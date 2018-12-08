---
title: Преобразование масштаба
description: Thhis статья исследует преобразование SkiaSharp масштабирования для масштабирования объектов для различных размеров и демонстрирует это с помощью примера кода.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 54A43F3D-9DA8-44A7-9AE4-7E3025129A0B
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2017
ms.openlocfilehash: 9bc320273df192f9daf2520f451601335731e7b0
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53061356"
---
# <a name="the-scale-transform"></a>Преобразование масштаба

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_Обнаружение преобразование SkiaSharp масштабирования для масштабирования объектов для различных размеров_

Как вы убедились в [ **перевести преобразование** ](translate.md) статье преобразования переноса можно переместить графического объекта из одного расположения в другое. Напротив преобразование масштаба изменяет размер графического объекта:

![](scale-images/scaleexample.png "Высота слово свертываться, размер")

Преобразование масштаба также часто вызывает ошибки в координаты графики для перемещения, так как они были сделаны большего размера.

Вы уже видели две формулы преобразования, которые описаны последствия коэффициентов перевода `dx` и `dy`:

x' = x + dx

y "= y + dy

Масштабирование коэффициентов `sx` и `sy` являются умножения вместо сложения:

x' = [Default] sx x

y "= sy [Default] y

Значения по умолчанию факторов translate равны 0; коэффициенты масштабирования значения по умолчанию — 1.

`SKCanvas` Класс определяет четыре `Scale` методы. Первый [ `Scale` ](xref:SkiaSharp.SKCanvas.Scale(System.Single)) метод является для вынести в случаях, когда требуется же горизонтальное и вертикальное масштабирование:

```csharp
public void Scale (Single s)
```

Этот процесс называется *мощности* масштабирование &mdash; масштабирование то же, в обоих направлениях. Масштабирование мощности сохраняет пропорции объекта.

Второй [ `Scale` ](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single)) метод позволяет указывать разные значения для горизонтального и вертикального масштабирования:

```csharp
public void Scale (Single sx, Single sy)
```

В результате *анизотропная* масштабирования.
Третий [ `Scale` ](xref:SkiaSharp.SKCanvas.Scale(SkiaSharp.SKPoint)) метод объединяет два коэффициенты масштабирования в одном `SKPoint` значение:

```csharp
public void Scale (SKPoint size)
```

Четвертый `Scale` метод будут описаны чуть позже.

**Простой масштабируемый** страница демонстрирует `Scale` метод. [ **BasicScalePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml) файл содержит два `Slider` элементы, которые позволяют выбрать коэффициенты вертикального и горизонтального масштабирования от 0 до 10. [ **BasicScalePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml.cs) файл кода программной части использует эти значения для вызова `Scale` до отображения прямоугольник с закругленными углами заштриховывать с пунктирной линией и уместиться какой-нибудь текст в левом верхнем углу уголок части холста.

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear(SKColors.SkyBlue);

    using (SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 3,
        PathEffect = SKPathEffect.CreateDash(new float[] {  7, 7 }, 0)
    })
    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 50
    })
    {
        canvas.Scale((float)xScaleSlider.Value,
                     (float)yScaleSlider.Value);

        SKRect textBounds = new SKRect();
        textPaint.MeasureText(Title, ref textBounds);

        float margin = 10;
        SKRect borderRect = SKRect.Create(new SKPoint(margin, margin), textBounds.Size);
        canvas.DrawRoundRect(borderRect, 20, 20, strokePaint);
        canvas.DrawText(Title, margin, -textBounds.Top + margin, textPaint);
    }
}
```

Может возникнуть вопрос: как коэффициенты масштабирования влияют на значение, возвращаемое из `MeasureText` метод `SKPaint`? Ответ: вообще не. `Scale` — Это метод `SKCanvas`. Он не влияет на все с `SKPaint` объекта, пока не будет использовать этот объект для отрисовки, что-то на холсте.

Как вы видите, все, что отображается после `Scale` вызова увеличивается пропорционально:

[![](scale-images/basicscale-small.png "Тройной снимок экрана страницы простой масштабируемый")](scale-images/basicscale-large.png#lightbox "тройной снимок экрана страницы простой масштабируемый")

Текст, ширина пунктирная линия, длина дефисы в этой строке, округление углов и полями шириной 10 пикселей между краями левом и верхнем полотна и скругленный прямоугольник подвержены все же коэффициенты масштабирования.

> [!IMPORTANT]
> Универсальная платформа Windows не отображаться должным образом anisotropicly масштабированного текста.

Анизотропная масштабирование причины ширина мазков станет разных линий, согласован с горизонтальной и вертикальной осей. (Это также очевидно из первому изображению на этой странице.) Если вы не хотите ширины штриха с коэффициенты масштабирования, присвойте ей значение 0, и всегда будет иметь одному пикселю, вне зависимости от `Scale` параметр.

Масштабирование — это относительно левого верхнего угла холста. Это может оказаться именно то, что нужно, но не всегда. Предположим, что нужно поместить текст и прямоугольник в другое место на холсте для его масштабирования относительно ее центра. В этом случае можно использовать в четвертой версии [ `Scale` ](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single)) метод, который входят два дополнительных параметра для указания центра масштабирования:

```csharp
public void Scale (Single sx, Single sy, Single px, Single py)
```

`px` И `py` параметры определяют точку, которая иногда называется *масштабирование center* , но в SkiaSharp документации называется *вращения*. Это точка относительно левого верхнего угла холста, который не зависит от масштабирования. Все операции масштабирования происходит относительно этого центра.

[ **По центру шкалы** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/CenteredScalePage.xaml.cs) странице показано, как это работает. `PaintSurface` Обработчик аналогичен **простой масштабируемый** программы за одним исключением `margin` значение вычисляется текста по центру по горизонтали, что подразумевает, что программа, лучше всего работает в книжной ориентации:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear(SKColors.SkyBlue);

    using (SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 3,
        PathEffect = SKPathEffect.CreateDash(new float[] { 7, 7 }, 0)
    })
    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 50
    })
    {
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(Title, ref textBounds);
        float margin = (info.Width - textBounds.Width) / 2;

        float sx = (float)xScaleSlider.Value;
        float sy = (float)yScaleSlider.Value;
        float px = margin + textBounds.Width / 2;
        float py = margin + textBounds.Height / 2;

        canvas.Scale(sx, sy, px, py);

        SKRect borderRect = SKRect.Create(new SKPoint(margin, margin), textBounds.Size);
        canvas.DrawRoundRect(borderRect, 20, 20, strokePaint);
        canvas.DrawText(Title, margin, -textBounds.Top + margin, textPaint);
    }
}
```

Расположение верхнего левого угла прямоугольника с закругленными углами `margin` пикселей от левого края холста и `margin` пикселей ниже верхней границы. Последние два аргумента для `Scale` метод заданы эти значения, а также ширину и высоту текста, который также ширину и высоту прямоугольника с закругленными углами. Это означает, что все операции масштабирования относительно центра этого прямоугольника:

[![](scale-images/centeredscale-small.png "Тройной снимок экрана страницы по центру шкалы")](scale-images/centeredscale-large.png#lightbox "тройной снимок экрана масштабирование по центру страницы")

`Slider` Элементы в этой программе имеют ряд &ndash;10 до 10. Как вы видите, отрицательные значения вертикальное масштабирование (например, в Android, экран в центре) вызвать объекты для зеркального отображения относительно горизонтальной оси, проходящих через Центр масштабирования. Отрицательные значения по горизонтали, масштабирование (например, как показано на экране UWP справа) приводят к перевернуть вокруг вертикальной оси, проходящих через Центр масштабирования объектов.

Версия [ `Scale` ](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single)) метод с точками pivot является ярлыком для серии из трех `Translate` и `Scale` вызовов. Вы можете увидеть, как это работает, заменив `Scale` метод в **по центру шкалы** страницы со следующими:

```csharp
canvas.Translate(-px, -py);
```

Это отрицательных координат точек pivot.

Запустите программу повторно. Вы увидите, что прямоугольник и текстовый сдвигаются, чтобы центр находится в левом верхнем углу холста. Его можно практически невозможно просматривать. Ползунки не работают. конечно Теперь программа не масштабируется вообще.

Теперь добавьте базовый `Scale` вызова (без масштабирования center) *перед* , `Translate` вызова:

```csharp
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

Если вы знакомы с этого упражнения в других системах программирования графики, может показаться, что это не так, но не завершает работу. Skia обрабатывает вызовы последовательных преобразование немного по-разному из что вы можете быть знакомы с.

С помощью последовательных `Scale` и `Translate` вызовы, центр прямоугольника с закругленными углами, находится в левом верхнем углу, но теперь вы можете масштабировать его относительно верхнего левого угла canvas, который также является центр прямоугольника с закругленными углами.

Теперь, до этого `Scale` вызывать, добавьте еще один `Translate` вызовите центровки значениями:

```csharp
canvas.Translate(px, py);
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

Это перемещает масштабированный результат обратно в исходное положение. Эти три вызовы эквивалентны:

```csharp
canvas.Scale(sx, sy, px, py);
```

Преобразования отдельных актуальны и таким образом, формула Общее преобразование:

 x' = [Default] sx (x – пкс) + px

 y "= sy [Default] (y — py) + py

Имейте в виду, что значения по умолчанию `sx` и `sy` равны 1. Это просто чтобы убедиться, что точки вращения (px, py), эти формулы не применяется. Она остается в том же расположении относительно холста.

При объединении `Translate` и `Scale` вызовы, порядок имеет значение. Если `Translate` следует после `Scale`, translation factors эффективно масштабируется с коэффициенты масштабирования. Если `Translate` предшествует `Scale`, коэффициенты преобразования не масштабируются. Этот процесс несколько яснее (хотя и более математические) Если вводится предметом матрицы преобразования.

`SKPath` Класс определяет доступное только для чтения [ `Bounds` ](xref:SkiaSharp.SKPath.Bounds) свойство, которое возвращает `SKRect` определение пространства координат в пути. Например, если `Bounds` свойство получено из пути hendecagram, созданную ранее, `Left` и `Top` Свойства прямоугольника — – примерно 100 `Right` и `Bottom` свойства приблизительно 100 и `Width` и `Height` свойства — около 200. (Большинство фактические значения, немного меньше, так как точки звездочек определяются круг с радиусом 100, но только верхней точки — параллельное выполнение с помощью горизонтальной или вертикальной оси.)

Доступность этих сведений подразумевает, что она должна быть возможность являются производными масштабирования и перевод факторов, подходящий для масштабирования путь к размер холста. [ **Анизотропная масштабирование** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicScalingPage.cs) страница демонстрирует это с 11 указывает звездочкой. *Анизотропная* масштабирования означает, что не эквивалентны в горизонтальном и вертикальном направлениях, что означает, что звездочка не сохранит исходных пропорций. Ниже приведен соответствующий код в `PaintSurface` обработчика:

```csharp
SKPath path = HendecagramPage.HendecagramPath;
SKRect pathBounds = path.Bounds;

using (SKPaint fillPaint = new SKPaint
{
    Style = SKPaintStyle.Fill,
    Color = SKColors.Pink
})
using (SKPaint strokePaint = new SKPaint
{
    Style = SKPaintStyle.Stroke,
    Color = SKColors.Blue,
    StrokeWidth = 3,
    StrokeJoin = SKStrokeJoin.Round
})
{
    canvas.Scale(info.Width / pathBounds.Width,
                 info.Height / pathBounds.Height);
    canvas.Translate(-pathBounds.Left, -pathBounds.Top);

    canvas.DrawPath(path, fillPaint);
    canvas.DrawPath(path, strokePaint);
}
```

`pathBounds` Извлекается в верхней части этого кода и затем используется с ширины и высоты полотна в прямоугольник `Scale` вызова. Что вызов сам по себе масштабируется координаты пути, при его отрисовке `DrawPath` звонок, а звездочка будет выравниваться по центру в правом верхнем углу холста. Он должен быть перемещен вниз и влево. Это задание из `Translate` вызова. Эти два свойства класса `pathBounds` находятся – примерно 100, поэтому translation factors примерно 100. Так как `Translate` вызывается после `Scale` вызвать, эти значения эффективно масштабируется с коэффициенты масштабирования, перемещать их центре звезды относительно центральной части холста:

[![](scale-images/anisotropicscaling-small.png "Тройной снимок экрана страницы анизотропная масштабирование")](scale-images/anisotropicscaling-large.png#lightbox "тройной снимок экрана страницы анизотропная масштабирование")

Другим способом, можно представить себе `Scale` и `Translate` вызовов является для определения эффекта в обратном порядке: `Translate` вызов сдвигает путь, поэтому он становится полностью видимым, но ориентирована в левом верхнем углу холста. `Scale` Метод затем увеличивает этого типа "звезда" относительно верхнего левого угла.

На самом деле похоже, что звездочки немного больше, чем холста. Проблема заключается в ширину штриха. `Bounds` Свойство `SKPath` указывает размеры координаты в кодировке в пути, и это, программа использует для его масштабирования. При подготовке к просмотру путь с определенной толщина, готовый для просмотра путь больше, чем холста.

Чтобы устранить эту проблему необходимо компенсировать. Один простой подход в этой программе является добавление следующие инструкции непосредственно перед `Scale` вызова:

```csharp
pathBounds.Inflate(strokePaint.StrokeWidth / 2,
                   strokePaint.StrokeWidth / 2);
```

Это увеличивает `pathBounds` прямоугольник с 1,5 единицам для всех четырех сторон. Это разумное решение, только в том случае, когда соединение штриха округляется. Фацетное соединение может быть больше времени и трудно вычислить.

Можно также использовать подобный прием с текстом, заданным как **анизотропная текст** страница демонстрирует. Вот соответствующая часть `PaintSurface` обработчик из [ `AnisotropicTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicTextPage.cs) класса:

```csharp
using (SKPaint textPaint = new SKPaint
{
    Style = SKPaintStyle.Stroke,
    Color = SKColors.Blue,
    StrokeWidth = 0.1f,
    StrokeJoin = SKStrokeJoin.Round
})
{
    SKRect textBounds = new SKRect();
    textPaint.MeasureText("HELLO", ref textBounds);

    // Inflate bounds by the stroke width
    textBounds.Inflate(textPaint.StrokeWidth / 2,
                       textPaint.StrokeWidth / 2);

    canvas.Scale(info.Width / textBounds.Width,
                 info.Height / textBounds.Height);
    canvas.Translate(-textBounds.Left, -textBounds.Top);

    canvas.DrawText("HELLO", 0, 0, textPaint);
}
```

Это аналогичная логика, и текст расширяется до размера страницы, на основании прямоугольник границы текста, возвращаемый из `MeasureText` (это немного больше, чем фактический текст):

[![](scale-images/anisotropictext-small.png "Тройной снимок анизотропная тестовой страницы")](scale-images/anisotropictext-large.png#lightbox "тройной снимок анизотропная тестовой страницы")

Если необходимо выполнить сохранение пропорций графических объектов, стоит использовать масштабирование мощности. **Мощности масштабирование** страница демонстрирует это для типа "звезда" указывает 11. По существу приведены шаги для отображения графического объекта в центре страницы мощности масштабирования.

- Перевод центр графического объекта в левом верхнем углу.
- Изменять масштаб объекта, на основании минимального размеры страницы горизонтальные и вертикальные разделить по измерениям графического объекта.
- Перевод center масштабируемого объекта относительно центральной части страницы.

[ `IsotropicScalingPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/skia-sharp-forms/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/IsotropicScalingPage.cs) Выполняет следующие действия в обратном порядке перед выводом типа "звезда":

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPath path = HendecagramArrayPage.HendecagramPath;
    SKRect pathBounds = path.Bounds;

    using (SKPaint fillPaint = new SKPaint())
    {
        fillPaint.Style = SKPaintStyle.Fill;

        float scale = Math.Min(info.Width / pathBounds.Width,
                               info.Height / pathBounds.Height);

        for (int i = 0; i <= 10; i++)
        {
            fillPaint.Color = new SKColor((byte)(255 * (10 - i) / 10),
                                          0,
                                          (byte)(255 * i / 10));
            canvas.Save();
            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.Scale(scale);
            canvas.Translate(-pathBounds.MidX, -pathBounds.MidY);
            canvas.DrawPath(path, fillPaint);
            canvas.Restore();

            scale *= 0.9f;
        }
    }
}
```

Код также отображает 10 типа "звезда" несколько раз, каждый раз, уменьшение масштабирования factor, 10% и постепенно изменения цвета от красного к синему:

[![](scale-images/isotropicscaling-small.png "Тройной снимок экрана страницы мощности масштабирование")](scale-images/isotropicscaling-large.png#lightbox "тройной снимок экрана страницы мощности масштабирование")


## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
