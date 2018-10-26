---
title: Преобразование наклона
description: В этой статье объясняется, как создать преобразование наклона мозаичный графических объектов в SkiaSharp и демонстрирует это с помощью примера кода.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: FDD16186-E3B7-4FF6-9BC2-8A2974BFF616
author: davidbritch
ms.author: dabritch
ms.date: 03/20/2017
ms.openlocfilehash: ecb07c69b7720f77401bf9bf454ee4b0248ad238
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113824"
---
# <a name="the-skew-transform"></a>Преобразование наклона

_См. в разделе, как создать преобразование наклона мозаичный графических объектов в SkiaSharp_

В SkiaSharp преобразование наклона tilts графические объекты, такие как тени на этом изображении:

![](skew-images/skewexample.png "Примером наклон по программе наклон тени текста")

Неравномерность прямоугольник преобразуется в параллелограмм, но неравномерные эллипса по-прежнему эллипса.

Несмотря на то, что Xamarin.Forms определяет свойства для перевода, масштабирования и вращения, нет соответствующего свойства в Xamarin.Forms, отклонения.

[ `Skew` ](xref:SkiaSharp.SKCanvas.Skew(System.Single,System.Single)) Метод `SKCanvas` принимает два аргумента для наклон по горизонтали и вертикали наклон:

```csharp
public void Skew (Single xSkew, Single ySkew)
```

Секунды [ `Skew` ](xref:SkiaSharp.SKCanvas.Skew(SkiaSharp.SKPoint)) метод сочетает в себе эти аргументы в одном `SKPoint` значение:

```csharp
public void Skew (SKPoint skew)
```

Однако маловероятно, что используется один из этих двух методов в изоляции.

**Наклон поэкспериментировать** странице позволяет экспериментировать с неравномерное распределение значений в диапазоне от –10 до 10. Текстовая строка находится в левом верхнем углу страницы, с неравномерного распределения значений, полученных из двух `Slider` элементов. Вот `PaintSurface` обработчик в [ `SkewExperimentPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewExperimentPage.xaml.cs) класса:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 200
    })
    {
        string text = "SKEW";
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);

        canvas.Skew((float)xSkewSlider.Value, (float)ySkewSlider.Value);
        canvas.DrawText(text, 0, -textBounds.Top, textPaint);
    }
}
```

Значения `xSkew` аргумент shift нижней части текста справа для положительных значений или слева для отрицательных значений. Значения `ySkew` Сдвинуть вниз справа от текста, для положительных значений или для отрицательных значений:

[![](skew-images/skewexperiment-small.png "Тройной снимок экрана страницы наклон эксперимента")](skew-images/skewexperiment-large.png#lightbox "тройной снимок экрана страницы наклон эксперимента")

Если `xSkew` значение является отрицательной `ySkew` значение, результат поворота, но также масштабировать немного как указывает отображение универсальной платформы Windows.

Преобразование формул, следующим образом:

x' = x + xSkew [Default] y

y "= ySkew [Default] x + y

Например, положительное `xSkew` значение, преобразованное `x'` увеличивается значение `y` увеличивается. Это, что вызывает наклона.

Если треугольник 200 пикселей в ширину и 100 пикселов высотой располагается с его верхнего левого угла в точке (0, 0) и отображается с `xSkew` значение 1,5, параллелограмм следующие результаты:

![](skew-images/skeweffect.png "Воздействие на прямоугольник преобразование наклона")

Координаты нижнего края должны `y` значения 100, так что это сдвигаются 150 пикселей вправо.

Для значения нуля `xSkew` или `ySkew`только точке (0, 0) остается неизменным. Эту точку можно считать центре наклона. Если вам требуется центр наклон быть что-то другое (что обычно происходит), не `Skew` метод, который предоставляет. Необходимо явным образом объединить `Translate` вызывает с `Skew` вызова. Чтобы центрировать искажения в `px` и `py`, выполняете следующие вызовы:

```csharp
canvas.Translate(px, py);
canvas.Skew(xSkew, ySkew);
canvas.Translate(-px, -py);
```

Ниже приведены формулы составным преобразованием.

x' = x + xSkew [Default] (y — py)

y "= ySkew [Default] (x – пкс) + y

Если `ySkew` равен нулю, то `px` значение не используется. Значение не играет роли и аналогично для `ySkew` и `py`.

Вам может удобнее указание наклона как угол наклона в градусах, таких как угол α на этой схеме:

![](skew-images/skewangleeffect.png "Указывает последствия преобразование наклона прямоугольник с угол наклона")

Отношение shift 150 пикселов на 100 пикселей по вертикали составляет тангенс, угла, в этом примере 56.3 градусов.

Файл XAML от **эксперимента угол наклона** страница аналогична **угол наклона** странице за одним исключением `Slider` элементов в диапазоне от – 90 градусов до 90 градусов. [ `SkewAngleExperiment` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewAngleExperimentPage.xaml.cs) Файл с выделенным кодом Центрирует текст на странице и использует `Translate` задать центр наклон в центре страницы. Short `SkewDegrees` метод в нижней части кода преобразует угол наклона значения:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 200
    })
    {
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        string text = "SKEW";
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);
        float xText = xCenter - textBounds.MidX;
        float yText = yCenter - textBounds.MidY;

        canvas.Translate(xCenter, yCenter);
        SkewDegrees(canvas, xSkewSlider.Value, ySkewSlider.Value);
        canvas.Translate(-xCenter, -yCenter);
        canvas.DrawText(text, xText, yText, textPaint);
    }
}

void SkewDegrees(SKCanvas canvas, double xDegrees, double yDegrees)
{
    canvas.Skew((float)Math.Tan(Math.PI * xDegrees / 180),
                (float)Math.Tan(Math.PI * yDegrees / 180));
}
```

По мере приближения углом 90 градусов, положительным или отрицательным тангенс приближается к бесконечность, но углы до примерно 80 градусов или около того, используются:

[![](skew-images/skewangleexperiment-small.png "Тройной снимок экрана страницы эксперимента угол наклона")](skew-images/skewangleexperiment-large.png#lightbox "тройной снимок экрана страницы эксперимента угол наклона")

Небольшой отрицательное горизонтальное отклонение может имитировать курсивом или наклонный текст, как **наклонный текст** страница демонстрирует. [ `ObliqueTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ObliqueTextPage.cs) Класс показано, как это делается:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint()
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Maroon,
        TextAlign = SKTextAlign.Center,
        TextSize = info.Width / 8       // empirically determined
    })
    {
        canvas.Translate(info.Width / 2, info.Height / 2);
        SkewDegrees(canvas, -20, 0);
        canvas.DrawText(Title, 0, 0, textPaint);
    }
}

void SkewDegrees(SKCanvas canvas, double xDegrees, double yDegrees)
{
    canvas.Skew((float)Math.Tan(Math.PI * xDegrees / 180),
                (float)Math.Tan(Math.PI * yDegrees / 180));
}
```

`TextAlign` Свойство `SKPaint` присваивается `Center`. Без каких-либо преобразований `DrawText` вызов с координаты (0, 0) поместит текста по горизонтали по центру базовых показателей в левом верхнем углу. `SkewDegrees` Наклоняет текст по горизонтали 20 градусов относительно базового плана. `Translate` Вызов перемещает центра элемента базового плана текста по горизонтали относительно центральной части холста:

[![](skew-images/obliquetext-small.png "Тройной снимок экрана страницы наклонный текст")](skew-images/obliquetext-large.png#lightbox "тройной снимок экрана страницы наклонный текст")

**Наклон тени текста** странице рассказывается, как использовать сочетание 45 градусов наклона и вертикального масштабирования, чтобы сделать тень текста, который tilts от текста. Вот соответствующие часть `PaintSurface` обработчика:

```csharp
using (SKPaint textPaint = new SKPaint())
{
    textPaint.Style = SKPaintStyle.Fill;
    textPaint.TextSize = info.Width / 6;   // empirically determined

    // Common to shadow and text
    string text = "Shadow";
    float xText = 20;
    float yText = info.Height / 2;

    // Shadow
    textPaint.Color = SKColors.LightGray;
    canvas.Save();
    canvas.Translate(xText, yText);
    canvas.Skew((float)Math.Tan(-Math.PI / 4), 0);
    canvas.Scale(1, 3);
    canvas.Translate(-xText, -yText);
    canvas.DrawText(text, xText, yText, textPaint);
    canvas.Restore();

    // Text
    textPaint.Color = SKColors.Blue;
    canvas.DrawText(text, xText, yText, textPaint);
}
```

Прозрачность тени — сначала отображается, а затем текст:

[![](skew-images/skewshadowtext1-small.png "Тройной снимок экрана страницы наклон тени текста")](skew-images/skewshadowtext1-large.png#lightbox "тройной снимок экрана страницы наклон тени текста")

Вертикальная координата, передаваемый `DrawText` метод указывает расположение текста относительно базового плана. Это же Вертикальная координата, используемая для центра наклона. Этот способ не сработает, если текстовая строка содержит подстрочные элементы. Например, вместо «Теневой» используется слово «причудливым», и результат:

[![](skew-images/skewshadowtext2-small.png "Тройной снимок экрана страницы наклон тени текста альтернативное слово с подстрочные элементы")](skew-images/skewshadowtext2-large.png#lightbox "тройной снимок экрана страницы наклон тени текста альтернативное слово с подстрочных элементов")

Тени и текст по-прежнему выравниваются по базовой линии, но эффект просто выглядит не так. Чтобы устранить проблему, вам потребуется получить границы текста.

```csharp
SKRect textBounds = new SKRect();
textPaint.MeasureText(text, ref textBounds);
```

`Translate` Вызовы должны указываться по высоте подстрочных элементов:

```csharp
canvas.Translate(xText, yText + textBounds.Bottom);
canvas.Skew((float)Math.Tan(-Math.PI / 4), 0);
canvas.Scale(1, 3);
canvas.Translate(-xText, -yText - textBounds.Bottom);
```

Теперь тени расширяет в нижней части этих подстрочных элементов:

[![](skew-images/skewshadowtext3-small.png "Тройной снимок экрана страницы наклон Тень текста с корректировки для подстрочных элементов")](skew-images/skewshadowtext3-large.png#lightbox "тройной снимок экрана страницы наклон Тень текста с корректировки для подстрочных элементов")


## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
