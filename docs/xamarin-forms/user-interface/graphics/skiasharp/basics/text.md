---
title: Интеграция текста и графики
description: В этой статье объясняется, как определить размер отображаемого текста строки для интеграции текста с графикой SkiaSharp в Xamarin.Forms приложения и демонстрирует это с помощью примера кода.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: A0B5AC82-7736-4AD8-AA16-FE43E18D203C
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: be1524029ada79896f83517c3b439f2ad0e2c6d9
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615165"
---
# <a name="integrating-text-and-graphics"></a>Интеграция текста и графики

_Узнайте, как определить размер отображаемого текста строки для интеграции текста с графикой SkiaSharp_

В этой статье показано, как измерять текст, возможно масштабирование текст определенный размер и интегрировать с другими изображениями текст:

![](text-images/textandgraphicsexample.png "Текст, заключены в прямоугольники")

SkiaSharp `Canvas` класс также содержит методы для рисования прямоугольника ([`DrawRect`](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawRect/p/SkiaSharp.SKRect/SkiaSharp.SKPaint/)) и прямоугольник со скругленными углами ([`DrawRoundRect`](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawRoundRect/p/SkiaSharp.SKRect/System.Single/System.Single/SkiaSharp.SKPaint/)). Эти методы требуют прямоугольник определяется `SKRect` значение.

**Текст в рамке** страницы центров короткая строка текста на странице, а также для окружения с кадр состоит из пары прямоугольников со скругленными углами. [ `FramedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/FramedTextPage.cs) Класс показано, как это сделать.

В SkiaSharp используется `SKPaint` класс задать атрибуты текста и шрифта, но вы также может использовать его для получения отображаемый размер текста. Начало следующие `PaintSurface` обработчик событий вызывает два разных `MeasureText` методы. Первый [ `MeasureText` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.MeasureText/p/System.String/) вызов имеет простой `string` аргумента и возвращает ширина в пикселях текста на основе текущего шрифта атрибутов. Затем вычисляется новый `TextSize` свойство `SKPaint` объект зависимости, отображаемую ширину текущего `TextSize` свойство и ширину области отображения. Это предназначено для задания `TextSize` таким образом, чтобы строку текста для отображения на уровне 90% от ширины экрана:

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

Второй [ `MeasureText` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.MeasureText/p/System.String/SkiaSharp.SKRect@/) вызов `SKRect` аргумент, поэтому он получает ширины и высоты отображаемого текста. `Height` Свойства данного объекта `SKRect` значение зависит от наличия прописных букв, верхние выносные элементы и подстрочных элементов в текстовой строке. Различные `Height` выводятся значения параметров для строки текста «mom», «cat» и «dog», например.

`Left` И `Top` свойства `SKRect` структуры указывают координаты верхнего левого угла отображаемого текста, если текст отображается с `DrawText` вызов с позиции 0 X и Y. Например, если эта программа работает на эмулятор iPhone 7, `TextSize` присваивается значение 90.6254 в результате вычисления после первого вызова `MeasureText`. `SKRect` Значение, полученное от второй вызов `MeasureText` имеет следующие значения свойств:

- `Left` = 6
- `Top` = &ndash;68
- `Width` = 664.8214
- `Height` = 88;

Имейте в виду, что координаты X и Y можно передать `DrawText` указать метод слева от текста в базовый план. `Top` Значение указывает, что текст выходит 68 пикселей выше базового и (вычитания 68 ИТ из 88) 20 пикселей ниже линии шрифта. `Left` Значение 6 означает, что текст начинается 6 пикселей вправо значения X в `DrawText` вызова. Это позволяет выполнять обычные межзнакового. Если вы хотите отобразить текст устроено в левом верхнем углу экрана, передайте отрицательные результаты из этих `Left` и `Top` значений координаты X и Y `DrawText`, в этом примере &ndash;6 и 68.

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

`PaintSurface` Обработчик завершается два вызова `DrawRoundRect`, для которых требуется аргументы `SKRect`. Это `SKRect` определенно похожее значение `SKRect` значение, полученное от `MeasureText` метод, но не могут совпадать. Во-первых, оно должно быть немного увеличить, чтобы Скругленный прямоугольник не отображение поверх краями текста, а во-вторых, он должен быть перемещен в пространстве, чтобы `Left` и `Top` значения соответствуют верхнего левого угла, где должен быть прямоугольника линиями. Эти два задания выполняются посредством `Offset` и `Inflate` методы, определенные `SKRect`:

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

После этого в оставшейся части метода довольно прост. Он создает еще один `SKPaint` объект для границ и вызовы `DrawRoundRect` дважды. Второй вызов использует прямоугольник увеличивается за счет другой 10 точек. Первый вызов указывает радиуса скругления угла 20 пикселей; второй — радиуса скругления угла 30 пикселей, что они не должен быть:

 [![](text-images/framedtext-small.png "Тройной снимок экрана страницы в рамке текст")](text-images/framedtext-large.png#lightbox "тройной снимок экрана страницы в рамке текста")

Вы можете включить ваш телефон или повороте симулятора см. в разделе текст и увеличение размера кадра.

Если вам нужно только центрировать текст на экране, его можно сделать примерно без измерения текст, задав `TextAlign` свойство `SKPaint` для `SKTextAlign.Center`. Координаты X, указываемое в `DrawText` метод указывает положение по горизонтали по центру текста. Если передать средней части экрана, чтобы `DrawText` метод, текст будет выравниваться по горизонтали по центру и *почти* вертикально по центру, так как базовый план будет центрироваться по вертикали.

Сам текст может рассматриваться как графические намного. Один простой параметр является для отображения структуры текстовых символов, а не обычных заполненного отображения:

[![](text-images/outlinedtext-small.png "Снимок экрана страницы контурного текста утроить")](text-images/outlinedtext-large.png#lightbox "утроить снимок экрана страницы контурного текста")

Это делается простым изменением нормали `Style` свойство `SKPaint` объекта из значения по умолчанию `SKPaintStyle.Fill` для `SKPaintStyle.Stroke` и указав ширина штриха. `PaintSurface` Обработчик **контурного текста** странице показано, как это делается:

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

 В последние несколько статей что у вас есть теперь рассмотрено использование SkiaSharp для рисования текста, круги, эллипсы и прямоугольники с закругленными углами. Следующим шагом является [строки и пути SkiaSharp](~/xamarin-forms/user-interface/graphics/skiasharp/paths/paths.md) в которой вы узнаете, как для рисования линии в графический контур.


## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
