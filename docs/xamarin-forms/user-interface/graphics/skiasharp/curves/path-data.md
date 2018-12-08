---
title: Данные пути SVG в SkiaSharp
description: В этой статье объясняется, как определять пути SkiaSharp, с помощью текстовых строк в формате масштабируемой векторной графики, а также демонстрирует это с помощью примера кода.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 1D53067B-3502-4D74-B89D-7EC496901AE2
author: davidbritch
ms.author: dabritch
ms.date: 05/24/2017
ms.openlocfilehash: 690d3c15d7ad2aad06be5b499bae1a94107414f4
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53053628"
---
# <a name="svg-path-data-in-skiasharp"></a>Данные пути SVG в SkiaSharp

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_Определение путей, используя текстовые строки в формате масштабируемой векторной графики_

[ `SKPath` ](xref:SkiaSharp.SKPath) Класс поддерживает определение объектов весь путь из текстовых строк в формате, установленные в спецификации масштабируемый векторный рисунок (SVG). Вы увидите далее в этой статье как может представлять весь путь следующего вида в текстовой строке:

![](path-data-images/pathdatasample.png "Пример пути, определенного с помощью данные пути SVG")

Использование SVG рекомендовано графики в формате XML, язык программирования для веб-страниц. Так как SVG необходимо разрешить пути должна определяться в разметке, а не серии вызовов функций, стандартная SVG-ФАЙЛ содержит очень краткий способ указания пути всей графики в виде текстовой строки.

В SkiaSharp этот формат называется «Данные пути SVG-.» Формат также поддерживается в программирования средах на базе Windows XAML, включая Windows Presentation Foundation и универсальной платформы Windows, где она известна под именем [синтаксис разметки пути](/dotnet/framework/wpf/graphics-multimedia/path-markup-syntax) или [перемещения и нарисуйте синтаксис команды](/windows/uwp/xaml-platform/move-draw-commands-syntax/). Его также можно использовать в качестве формата обмена для векторных изображений графики, особенно в текстовые файлы, такие как XML.

[ `SKPath` ](xref:SkiaSharp.SKPath) Класс определяет два метода с слова `SvgPathData` в именах:

```csharp
public static SKPath ParseSvgPathData(string svgPath)

public string ToSvgPathData()
```

Статический [ `ParseSvgPathData` ](xref:SkiaSharp.SKPath.ParseSvgPathData(System.String)) метод преобразует строку в `SKPath` объекта, хотя [ `ToSvgPathData` ](xref:SkiaSharp.SKPath.ToSvgPathData) преобразует `SKPath` объект в строку.

Вот строку SVG для пяти указывает типа "звезда" центром в точке (0, 0) с радиусом 100:

```
"M 0 -100 L 58.8 90.9, -95.1 -30.9, 95.1 -30.9, -58.8 80.9 Z"
```

Буквы представляют собой команды, которые занимаются `SKPath` объект: `M` указывает `MoveTo` вызвать, `L` — `LineTo`, и `Z` является `Close` закрыть профиль. Каждой паре номеров предоставляет точки по оси X и Y. Обратите внимание, что `L` после команды несколько точек, разделенных запятыми. В серии координат и точки, запятые и пробелы обрабатываются одинаково. Некоторые программисты предпочитают размещать запятые между координатами X и Y, а не между точками, но только необходимые для устранения неоднозначности, запятые или пробелы. Это вполне допустимо:

```
"M0-100L58.8 90.9-95.1-30.9 95.1-30.9-58.8 80.9Z"
```

Синтаксис данные пути SVG формально задокументированных в [8.3 разделе спецификации SVG](http://www.w3.org/TR/SVG11/paths.html#PathData). Ниже приведена сводка:

## <a name="moveto"></a>**MoveTo**

```
M x y
```

Таким образом вы сможете новый профиль в пути, задав текущей позиции. Данные пути всегда должно начинаться с `M` команды.

## <a name="lineto"></a>**LineTo**

```
L x y ...
```

Эта команда добавляет к нему прямой линией (или строки) и задает новой текущей позиции до конца последней строки. Можно выполнить `L` с нескольких пар *x* и *y* координаты.

## <a name="horizontal-lineto"></a>**Горизонтальная LineTo**

```
H x ...
```

Эта команда добавляет горизонтальную линию к пути и задает новой текущей позиции до конца строки. Можно выполнить `H` команды с несколькими *x* координаты, но он не имеет смысла.

## <a name="vertical-line"></a>**Вертикальная линия**

```
V y ...
```

Эта команда добавляет вертикальную линию в путь и задает новой текущей позиции до конца строки.

## <a name="close"></a>**Закрыть**

```
Z
```

`C` Команда закрывает профиль, добавив прямую линию от текущей позиции в начале контур.

## <a name="arcto"></a>**ArcTo**

Команду, чтобы добавить дугу эллипса к профиль является наиболее сложным в всю спецификацию данные пути SVG команда. Это только команда, в котором чисел может представлять, отличные от значения координат:

```
A rx ry rotation-angle large-arc-flag sweep-flag x y ...
```

*Rx* и *ыстрой* параметры являются горизонтальный и вертикальный радиусы эллипса. *Угол поворота* — по часовой стрелке в градусах.

Задайте *больших arc флаг* 1 для больших дуги, или 0 для небольших дуги.

Задайте *флаг очистки* 1 для по часовой стрелке и 0 для против часовой стрелки.

К точке рисуется дуга (*x*, *y*), которая становится новой текущей позиции.

## <a name="cubicto"></a>**CubicTo**

```
C x1 y1 x2 y2 x3 y3 ...
```

Эта команда добавляет кривую Безье третьего порядка с текущей позицией с целью (*x3*, *y3*), которая становится новой текущей позиции. Точки (*x1*, *y1*) и (*x2*, *y2*) — это точки управления.

Можно указать несколько кривых Безье одним `C` команды. Количество точек должно быть кратно 3.

Имеется также «smooth» команда рисования кривой Безье:

```
S x2 y2 x3 y3 ...
```

Эта команда должны соответствовать обычной командной Безье (несмотря на то, что, не обязательно). Команда гладкой Безье вычисляет первой контрольной точки таким образом, чтобы он является отражением второй контрольной точки предыдущей Безье вокруг их взаимной точки. Эти три точки, следовательно, colinear, а соединение между двумя кривых Безье smooth.

## <a name="quadto"></a>**QuadTo**

```
Q x1 y1 x2 y2 ...
```

Для квадратичных кривых Безье количество точек должно быть кратным 2. Контрольная точка совпадает (*x1*, *y1*) и конечной точки (и новая текущая позиция) является (*x2*, *y2*)

Также имеется команда smooth квадратичной кривой:

```
T x2 y2 ...
```

Контрольная точка, рассчитывается на основании контрольной точки предыдущей квадратичной кривой.

Все эти команды также доступны в версиях «относительное», где координаты точек являются относительно текущей позиции. Эти относительные команды начинаются с букв, например `c` вместо `C` относительный версии команда Безье третьего порядка.

Это расширение определения данные пути SVG. Не существует средств для повторяющейся группы команд или для выполнения любого типа вычисления. Команды для `ConicTo` или другие виды характеристики дуга недоступны.

Статический [ `SKPath.ParseSvgPathData` ](xref:SkiaSharp.SKPath.ParseSvgPathData(System.String)) метод ожидает допустимую строку команд SVG. При обнаружении ошибок синтаксиса, метод возвращает `null`. Это значение, указывающее только ошибки.

[ `ToSvgPathData` ](xref:SkiaSharp.SKPath.ToSvgPathData) Метод удобно использовать для получения данные пути SVG из существующего `SKPath` объект для передачи в другую программу, или для хранения в виде текстового файла, например XML. ( `ToSvgPathData` Метод не показано в примере кода в этой статье.) Сделать *не* ожидать `ToSvgPathData` для возврата строки, точно соответствующие вызовы методов, которые созданы пути. В частности, вы обнаружите, что дуги преобразуются к нескольким `QuadTo` команды, и это, как они указаны в пути данных, возвращаемых из `ToSvgPathData`.

**Пути данных Hello** странице разных слово «HELLO» с помощью данные пути SVG. Как `SKPath` и `SKPaint` объекты определяются в виде полей [ `PathDataHelloPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataHelloPage.cs) класса:

```csharp
public class PathDataHelloPage : ContentPage
{
    SKPath helloPath = SKPath.ParseSvgPathData(
        "M 0 0 L 0 100 M 0 50 L 50 50 M 50 0 L 50 100" +                // H
        "M 125 0 C 60 -10, 60 60, 125 50, 60 40, 60 110, 125 100" +     // E
        "M 150 0 L 150 100, 200 100" +                                  // L
        "M 225 0 L 225 100, 275 100" +                                  // L
        "M 300 50 A 25 50 0 1 0 300 49.9 Z");                           // O

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = SKStrokeCap.Round,
        StrokeJoin = SKStrokeJoin.Round
    };
    ...
}
```

Путь, определяющий текстовая строка начинается в левом верхнем углу в точке (0, 0). Каждая буква обозначает 50 единиц в ширину и высоту 100 единиц и буквы разделяются другой 25 единиц, это означает, что весь путь будет 350 единиц в ширину.

«H», «Hello» состоит из трех профилей одной строки, при двух соединенных кривых Безье третьего 'E'. Обратите внимание, что `C` после команды шесть точек, и две контрольные точки имеют координаты Y –10 и 110, который помещает их вне диапазона значений координаты Y остальные буквы. «L» представляет две соединенные линии, а "о — эллипс, который отрисовывается с помощью `A` команды.

Обратите внимание, что `M` команда начала последнего профиль задает положение точки (350, 50), то есть по вертикали по центру левой стороны "о. Как указано в первой следующие номера `A` команды эллипс имеет Горизонтальный радиус 25 и Вертикальный радиус 50. Конечная точка указывается последняя пара чисел в `A` команду, которая представляет точку (300, 49,9). Это просто намеренно немного отличается от начальной точки. Если конечная точка задана равной начальную точку, будет отрисовываться дуги. Чтобы нарисовать эллипс, частью завершения, необходимо задать конечную точку близко к (но не равно) начальной точки, или же необходимо использовать два или более `A` команды, в каждой части эллипс, частью завершения.

Может потребоваться добавьте следующую инструкцию в конструктор страницы и затем установите точку останова для изучения результирующая строка:

```csharp
string str = helloPath.ToSvgPathData();
```

Вы обнаружите, что дуга была заменена серию `Q` команд для поэтапного приближения дуги, с помощью кривых Безье второго порядка.

`PaintSurface` Обработчик получает тесной границы пути, который не содержит контрольные точки для «E» и "о кривых. Три преобразования перейти центра контура к точке (0, 0), масштабирование путь к размер холста (но также принимая ширина мазков) и переместите центра контура к центру холста:

```csharp
public class PathDataHelloPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect bounds;
        helloPath.GetTightBounds(out bounds);

        canvas.Translate(info.Width / 2, info.Height / 2);

        canvas.Scale(info.Width / (bounds.Width + paint.StrokeWidth),
                     info.Height / (bounds.Height + paint.StrokeWidth));

        canvas.Translate(-bounds.MidX, -bounds.MidY);

        canvas.DrawPath(helloPath, paint);
    }
}
```

Путь заполняет холст, который выглядит более разумно, при просмотре в альбомном режиме:

[![](path-data-images/pathdatahello-small.png "Тройной снимок экрана страницы путь данных Hello")](path-data-images/pathdatahello-large.png#lightbox "тройной снимок экрана страницы путь данных Hello")

**Пути данных Cat** страницы похож. Путь и рисования объектов определены как поля в [ `PathDataCatPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs) класса:

```csharp
public class PathDataCatPage : ContentPage
{
    SKPath catPath = SKPath.ParseSvgPathData(
        "M 160 140 L 150 50 220 103" +              // Left ear
        "M 320 140 L 330 50 260 103" +              // Right ear
        "M 215 230 L 40 200" +                      // Left whiskers
        "M 215 240 L 40 240" +
        "M 215 250 L 40 280" +
        "M 265 230 L 440 200" +                     // Right whiskers
        "M 265 240 L 440 240" +
        "M 265 250 L 440 280" +
        "M 240 100" +                               // Head
        "A 100 100 0 0 1 240 300" +
        "A 100 100 0 0 1 240 100 Z" +
        "M 180 170" +                               // Left eye
        "A 40 40 0 0 1 220 170" +
        "A 40 40 0 0 1 180 170 Z" +
        "M 300 170" +                               // Right eye
        "A 40 40 0 0 1 260 170" +
        "A 40 40 0 0 1 300 170 Z");

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Orange,
        StrokeWidth = 5
    };
    ...
}
```

Head кота отображается в виде круга, и здесь он отображается с двумя `A` команд, каждая из которых рисует полукруга. Оба `A` команды для головного определяют горизонтальный и вертикальный радиусы 100. Первая дуга начинается с (240, 100) и заканчивается (240, 300), которая становится начальную точку для второй дуги, который заканчивается на (240, 100).

С двумя также отображаются два глаза `A` команды и с помощью head cat второй `A` команда завершает в той же точке как начало первого `A` команды. Тем не менее эти пары `A` команд не определяют эллипса. С каждой дуги 40 единиц и радиус 40 единиц, это означает, что эти дуги не полный semicircles.

`PaintSurface` Обработчик выполняет аналогичные преобразования, что и предыдущая, но устанавливает один `Scale` фактор, который пропорции и обеспечивает небольшой поля, поэтому ограничителей выбросов cat не будут затронуты сторон экрана:

```csharp
public class PathDataCatPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Black);

        SKRect bounds;
        catPath.GetBounds(out bounds);

        canvas.Translate(info.Width / 2, info.Height / 2);

        canvas.Scale(0.9f * Math.Min(info.Width / bounds.Width,
                                     info.Height / bounds.Height));

        canvas.Translate(-bounds.MidX, -bounds.MidY);

        canvas.DrawPath(catPath, paint);
    }
}
```

Вот ее запуск.

[![](path-data-images/pathdatacat-small.png "Тройной снимок экрана страницы путь данных Cat")](path-data-images/pathdatacat-large.png#lightbox "тройной снимок экрана страницы путь данных Cat")

Обычно, когда `SKPath` объект определяется как поле, контуров пути должны быть определены в конструкторе или другого метода. При использовании данные пути SVG, тем не менее, было показано, что путь можно задать только в определении поля.

Раннюю **некрасиво часов аналоговый** в [ **преобразование поворота** ](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/rotate.md) статье отображена стрелки часов как простой строки. **Довольно аналоговый часы** программы ниже заменяет эти строки с `SKPath` объекты, определенные в виде полей [ `PrettyAnalogClockPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PrettyAnalogClockPage.cs) класса вместе с `SKPaint` объектов:

```csharp
public class PrettyAnalogClockPage : ContentPage
{
    ...
    // Clock hands pointing straight up
    SKPath hourHandPath = SKPath.ParseSvgPathData(
        "M 0 -60 C   0 -30 20 -30  5 -20 L  5   0" +
                "C   5 7.5 -5 7.5 -5   0 L -5 -20" +
                "C -20 -30  0 -30  0 -60 Z");

    SKPath minuteHandPath = SKPath.ParseSvgPathData(
        "M 0 -80 C   0 -75  0 -70  2.5 -60 L  2.5   0" +
                "C   2.5 5 -2.5 5 -2.5   0 L -2.5 -60" +
                "C 0 -70  0 -75  0 -80 Z");

    SKPath secondHandPath = SKPath.ParseSvgPathData(
        "M 0 10 L 0 -80");

    // SKPaint objects
    SKPaint handStrokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 2,
        StrokeCap = SKStrokeCap.Round
    };

    SKPaint handFillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Gray
    };
    ...
}
```

Теперь стрелки часов и минут вложенных областей. Чтобы сделать эти руки отличались друг от друга, они отображаются с и черный контур и с помощью заливки, серую `handStrokePaint` и `handFillPaint` объектов.

В более ранней **некрасиво часов аналоговый** образец, маленький накладывающихся кольца, который отмечен как часы и минуты, нарисованные в цикле. В этом **довольно аналоговый часы** образца используется совершенно другой подход: час и минуту метки представляют собой пунктирных линий, нарисованных при помощи `minuteMarkPaint` и `hourMarkPaint` объектов:

```csharp
public class PrettyAnalogClockPage : ContentPage
{
    ...
    SKPaint minuteMarkPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 3,
        StrokeCap = SKStrokeCap.Round,
        PathEffect = SKPathEffect.CreateDash(new float[] { 0, 3 * 3.14159f }, 0)
    };

    SKPaint hourMarkPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 6,
        StrokeCap = SKStrokeCap.Round,
        PathEffect = SKPathEffect.CreateDash(new float[] { 0, 15 * 3.14159f }, 0)
    };
    ...
}
```

[ **Точек и тире** ](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md) статье описано, как можно использовать [ `SKPathEffect.CreateDash` ](xref:SkiaSharp.SKPathEffect.CreateDash*) метод для создания пунктирной линией. Первым аргументом является `float` массив, который обычно состоит из двух элементов: первый элемент — длина дефисы, а второй элемент — разрыв между дефисы. Когда `StrokeCap` свойству `SKStrokeCap.Round`, а затем скругленными конце штриха эффективно удлинить длина штриха, ширина мазков на обеих сторонах штриха. Таким образом установка первого элемента массива 0 создает пунктирной линией.

Расстояние между этими точками определяется второй элемент массива. Как вы вскоре увидите, эти два `SKPaint` объекты используются для создания кругов с радиусом 90 единиц. Длины окружности этот круг, таким образом, является 180π, это означает, что метки 60 минут должно отображаться каждые 3π единиц, который является второе значение в `float` массива в `minuteMarkPaint`. 12 часовые метки должны располагаться каждые 15π единиц, это значение во второй `float` массива.

`PrettyAnalogClockPage` Класс устанавливает таймер, чтобы сделать недействительным области каждые 16 миллисекунд и `PaintSurface` обработчик вызывается такими темпами. Более ранние определения `SKPath` и `SKPaint` объекты позволяют очень качественным, код:

```csharp
public class PrettyAnalogClockPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Transform for 100-radius circle in center
        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(Math.Min(info.Width / 200, info.Height / 200));

        // Draw circles for hour and minute marks
        SKRect rect = new SKRect(-90, -90, 90, 90);
        canvas.DrawOval(rect, minuteMarkPaint);
        canvas.DrawOval(rect, hourMarkPaint);

        // Get time
        DateTime dateTime = DateTime.Now;

        // Draw hour hand
        canvas.Save();
        canvas.RotateDegrees(30 * dateTime.Hour + dateTime.Minute / 2f);
        canvas.DrawPath(hourHandPath, handStrokePaint);
        canvas.DrawPath(hourHandPath, handFillPaint);
        canvas.Restore();

        // Draw minute hand
        canvas.Save();
        canvas.RotateDegrees(6 * dateTime.Minute + dateTime.Second / 10f);
        canvas.DrawPath(minuteHandPath, handStrokePaint);
        canvas.DrawPath(minuteHandPath, handFillPaint);
        canvas.Restore();

        // Draw second hand
        double t = dateTime.Millisecond / 1000.0;

        if (t < 0.5)
        {
            t = 0.5 * Easing.SpringIn.Ease(t / 0.5);
        }
        else
        {
            t = 0.5 * (1 + Easing.SpringOut.Ease((t - 0.5) / 0.5));
        }

        canvas.Save();
        canvas.RotateDegrees(6 * (dateTime.Second + (float)t));
        canvas.DrawPath(secondHandPath, handStrokePaint);
        canvas.Restore();
    }
}
```

Кое-что особое делается с помощью секундной стрелки, тем не менее. Поскольку часы обновляются каждые 16 миллисекунд `Millisecond` свойство `DateTime` значение потенциально может использоваться для анимации Очистка стороны во-вторых, вместо одного, которое перемещает в дискретных переходов от секунды до секунды. Но этот код не допускает перемещение беспрепятственной. Вместо этого он использует Xamarin.Forms [ `SpringIn` ](xref:Xamarin.Forms.Easing.SpringIn) и [ `SpringOut` ](xref:Xamarin.Forms.Easing.SpringOut) анимации, функции для разных видов перемещения плавности. Эти функции плавности вызвать секундной стрелки для перемещения по принципу jerkier &mdash; отзыва немного перед перемещается, а затем немного чрезмерно прокрутить его конечный эффект, к сожалению невозможно воспроизвести эти статического на снимках экрана:

[![](path-data-images/prettyanalogclock-small.png "Тройной снимок экрана страницы довольно аналоговый часы")](path-data-images/prettyanalogclock-large.png#lightbox "тройной снимок экрана страницы довольно аналоговый часов")


## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
