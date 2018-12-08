---
title: Интеграция текста и графики
description: В этой статье объясняется, как определить размер отображаемого текста строки для интеграции текста с графикой SkiaSharp в Xamarin.Forms приложения и демонстрирует это с помощью примера кода.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: A0B5AC82-7736-4AD8-AA16-FE43E18D203C
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 995291c438bdb510536294d4c3bb0e4e37ac737d
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53051601"
---
# <a name="integrating-text-and-graphics"></a>Интеграция текста и графики

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_Узнайте, как определить размер отображаемого текста строки для интеграции текста с графикой SkiaSharp_

В этой статье показано, как измерять текста, масштабирования текста определенный размер и интегрировать с другими изображениями текста:

![](text-images/textandgraphicsexample.png "Текст, заключены в прямоугольники")

Также включает в себя прямоугольник с закругленными углами. SkiaSharp `Canvas` класс включает [ `DrawRect` ](xref:SkiaSharp.SKCanvas.DrawRect*) методы, чтобы нарисовать прямоугольник и [ `DrawRoundRect` ](xref:SkiaSharp.SKCanvas.DrawRoundRect*) методы для рисования прямоугольника с скругленные углы. Эти методы позволяют прямоугольник определяется `SKRect` значение или другими способами.

**Текст в рамке** страницы центров короткая строка текста на странице, а также для окружения с кадр состоит из пары прямоугольников со скругленными углами. [ `FramedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/FramedTextPage.cs) Класс показано, как это сделать.

В SkiaSharp, используется `SKPaint` класс задать атрибуты текста и шрифта, но вы также может использовать его для получения отображаемый размер текста. Начало следующие `PaintSurface` обработчик событий вызывает два разных `MeasureText` методы. Первый [ `MeasureText` ](xref:SkiaSharp.SKPaint.MeasureText(System.String)) вызов имеет простой `string` аргумента и возвращает ширина в пикселях текста на основе текущего шрифта атрибутов. Затем вычисляется новый `TextSize` свойство `SKPaint` объект зависимости, отображаемую ширину текущего `TextSize` свойство и ширину области отображения. Этот расчет предназначена для задания `TextSize` таким образом, чтобы строку текста для отображения на уровне 90% от ширины экрана:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string str = "Hello SkiaSharp!";

    // Create an SKPaint object to display the text
    SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Chocolate
    };

    // Adjust TextSize property so text is 90% of screen width
    float textWidth = textPaint.MeasureText(str);
    textPaint.TextSize = 0.9f * info.Width * textPaint.TextSize / textWidth;

    // Find the text bounds
    SKRect textBounds = new SKRect();
    textPaint.MeasureText(str, ref textBounds);
    ...
}
```

Второй [ `MeasureText` ](xref:SkiaSharp.SKPaint.MeasureText(System.String,SkiaSharp.SKRect@)) вызов `SKRect` аргумент, поэтому он получает ширины и высоты отображаемого текста. `Height` Свойства данного объекта `SKRect` значение зависит от наличия прописных букв, верхние выносные элементы и подстрочных элементов в текстовой строке. Различные `Height` выводятся значения параметров для строки текста «mom», «cat» и «dog», например.

`Left` И `Top` свойства `SKRect` структуры указывают координаты верхнего левого угла отображаемого текста, если текст отображается с `DrawText` вызов с позиции 0 X и Y. Например, если эта программа работает на эмулятор iPhone 7, `TextSize` присваивается значение 90.6254 в результате вычисления после первого вызова `MeasureText`. `SKRect` Значение, полученное от второй вызов `MeasureText` имеет следующие значения свойств:

- `Left` = 6
- `Top` = &ndash;68
- `Width` = 664.8214
- `Height` = 88;

Имейте в виду, что координаты X и Y можно передать `DrawText` указать метод слева от текста в базовый план. `Top` Значение указывает, что текст выходит 68 пикселей выше базового и (вычитания 68 ИТ из 88) 20 пикселей ниже линии шрифта. `Left` Значение 6 указывает, что текст начинается шесть точек справа от значения X в `DrawText` вызова. Это позволяет выполнять обычные межзнакового. Если вы хотите отобразить текст устроено в левом верхнем углу экрана, передайте отрицательные результаты из этих `Left` и `Top` значений координаты X и Y `DrawText`, в этом примере &ndash;6 и 68.

`SKRect` Структура определяет несколько удобных свойств и методов, некоторые из которых используются в оставшейся части `PaintSurface` обработчика. `MidX` И `MidY` значения указывают координаты центра прямоугольника. (В примере iPhone 7. Эти значения являются 338.4107 и &ndash;24.) Следующий код использует эти значения для простой расчет координаты, чтобы центрировать текст с экрана:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    // Calculate offsets to center the text on the screen
    float xText = info.Width / 2 - textBounds.MidX;
    float yText = info.Height / 2 - textBounds.MidY;

    // And draw the text
    canvas.DrawText(str, xText, yText, textPaint);
    ...
}
```

`SKImageInfo` Структуры данных также определяет [ `Rect` ](xref:SkiaSharp.SKImageInfo.Rect) свойство типа `SKRect`, поэтому можно также вычислять `xText` и `yText` следующим образом:

```csharp
float xText = info.Rect.MidX - textBounds.MidX;
float yText = info.Rect.MidY - textBounds.MidY;
```

`PaintSurface` Обработчик завершается два вызова `DrawRoundRect`, для которых требуется аргументы `SKRect`. Это `SKRect` значение основано на `SKRect` значение, полученное от `MeasureText` метод, но не могут совпадать. Во-первых необходимо иметь немного увеличить, чтобы Скругленный прямоугольник не отображение поверх границы текста. Во-вторых, он должен быть перемещен в пространстве, чтобы `Left` и `Top` значения соответствуют верхнего левого угла, где будет размещаться прямоугольника. Эти два задания выполняются посредством [ `Offset` ](xref:SkiaSharp.SKRect.Offset*) и [ `Inflate` ](xref:SkiaSharp.SKRect.Inflate*) методы, определенные `SKRect`:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    // Create a new SKRect object for the frame around the text
    SKRect frameRect = textBounds;
    frameRect.Offset(xText, yText);
    frameRect.Inflate(10, 10);

    // Create an SKPaint object to display the frame
    SKPaint framePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 5,
        Color = SKColors.Blue
    };

    // Draw one frame
    canvas.DrawRoundRect(frameRect, 20, 20, framePaint);

    // Inflate the frameRect and draw another
    frameRect.Inflate(10, 10);
    framePaint.Color = SKColors.DarkBlue;
    canvas.DrawRoundRect(frameRect, 30, 30, framePaint);
}
```

После этого в оставшейся части метода довольно прост. Он создает еще один `SKPaint` объект для границ и вызовы `DrawRoundRect` дважды. Второй вызов использует прямоугольник увеличивается за счет другой 10 точек. Первый вызов указывает радиуса скругления угла 20 пикселей. Второй — радиуса скругления угла 30 пикселей, что они не должен быть:

 [![](text-images/framedtext-small.png "Тройной снимок экрана страницы в рамке текст")](text-images/framedtext-large.png#lightbox "тройной снимок экрана страницы в рамке текста")

Вы можете включить ваш телефон или повороте симулятора см. в разделе текст и увеличение размера кадра.

Если вам нужно только центрировать текст на экране, это можно сделать приблизительно без измерения текст. Вместо этого задайте [ `TextAlign` ](xref:SkiaSharp.SKPaint.TextAlign) свойство `SKPaint` член перечисления [ `SKTextAlign.Center` ](xref:SkiaSharp.SKTextAlign). Координаты X, указываемое в `DrawText` метод указывает положение по горизонтали по центру текста. Если передать средней части экрана, чтобы `DrawText` метод, текст будет выравниваться по горизонтали по центру и *почти* вертикально по центру, так как базовый план будет центрироваться по вертикали.

Текст может рассматриваться как любой другой графический объект намного. Один из простых вариантов — отображения структуры текстовых символов:

[![](text-images/outlinedtext-small.png "Тройное снимок экрана страницы контурного текста")](text-images/outlinedtext-large.png#lightbox "Triple screenshot of the Outlined Text page")

Это достигается простым изменением нормали `Style` свойство `SKPaint` объекта из значения по умолчанию `SKPaintStyle.Fill` для `SKPaintStyle.Stroke`и путем указания ширины штриха. `PaintSurface` Обработчик **контурного текста** странице показано, как это делается:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string text = "OUTLINE";

    // Create an SKPaint object to display the text
    SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 1,
        FakeBoldText = true,
        Color = SKColors.Blue
    };

    // Adjust TextSize property so text is 95% of screen width
    float textWidth = textPaint.MeasureText(text);
    textPaint.TextSize = 0.95f * info.Width * textPaint.TextSize / textWidth;

    // Find the text bounds
    SKRect textBounds = new SKRect();
    textPaint.MeasureText(text, ref textBounds);

    // Calculate offsets to center the text on the screen
    float xText = info.Width / 2 - textBounds.MidX;
    float yText = info.Height / 2 - textBounds.MidY;

    // And draw the text
    canvas.DrawText(text, xText, yText, textPaint);
}
```

Другой распространенных графический объект является растрового изображения. — Это обширная тема, зачисление подробно рассматривается в разделе [ **точечные рисунки SkiaSharp**](../bitmaps/index.md), но в следующей статье [ **основы растрового изображения в SkiaSharp**](bitmaps.md), содержит введение более компактно.

## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
