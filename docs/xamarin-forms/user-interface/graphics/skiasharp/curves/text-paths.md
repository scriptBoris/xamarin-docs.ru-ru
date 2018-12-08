---
title: Путей и текста в SkiaSharp
description: В этой статье рассматриваются пересечение SkiaSharp путей и текста и демонстрирует это с помощью примера кода.
ms.prod: xamarin
ms.assetid: C14C07F6-4A84-4A8C-BDB4-CD61FBF0F79B
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 08/01/2017
ms.openlocfilehash: 366a6e9585817c5a47ba5bec14fb2f238ab23a6b
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53050204"
---
# <a name="paths-and-text-in-skiasharp"></a>Путей и текста в SkiaSharp

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_Изучите пересечение путей и текста_

В современных графических систем шрифты, представляют собой коллекции контуров символов, обычно определяется кривых Безье второго порядка. Следовательно во многих современных графических системах включают средства для преобразования символов текста в графический контур.

Вы уже видели, что вы можете обводки контуров символов текста как также их заполнения. Благодаря этому можно для отображения этих контуров символов, с помощью определенного толщина и даже эффект, как описано в разделе [ **эффекты пути** ](effects.md) статьи. Но это также можно преобразовать строку символов в `SKPath` объекта. Это означает, что контуры текста может использоваться для обрезки с помощью методик, которые были описаны в [ **отсеченные области с помощью путей** ](clipping.md) статьи.

Наряду с использованием эффект для вычерчивания контур символа, вы также можете создать эффекты пути, основанных на путь, который является производным от строку символов, и можно даже комбинировать два эффекта:

![](text-paths-images/pathsandtextsample.png "Текстовый эффект путь")

В предыдущей статье на [ **эффекты пути**](effects.md), вы узнали, как [ `GetFillPath` ](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,SkiaSharp.SKRect,System.Single)) метод `SKPaint` можно получить контур обведенные пути. Этот метод также можно использовать с путями, производным от контуров символов.

Наконец, в этой статье демонстрируется другой пересечение путей и текста: [ `DrawTextOnPath` ](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) метод `SKCanvas` позволяет отображать строку текста, чтобы базовый план текста соответствует изогнутого пути.

## <a name="text-to-path-conversion"></a>Текст для преобразования пути

[ `GetTextPath` ](xref:SkiaSharp.SKPaint.GetTextPath(System.String,System.Single,System.Single)) Метод `SKPaint` преобразует символьную строку в `SKPath` объекта:

```csharp
public SKPath GetTextPath (String text, Single x, Single y)
```

`x` И `y` аргументы указания начальной точки базовой линии слева от текста. Они играют ту же роль здесь в качестве `DrawText` метод `SKCanvas`. В пути базовой линии слева от текста будет иметь координаты (x, y).

`GetTextPath` Метод является избыточным, если вы просто хотите, чтобы заполнить или обводки результирующий путь. Нормали `DrawText` метод позволяет это сделать. `GetTextPath` Метод более полезен для других задач, связанных с пути.

Одной из этих задач обрезки. **Обрезки текста** страница создает контура обрезки, в зависимости от контур символа слова «Код». Этот путь будет растянуто, чтобы размер страницы, чтобы обрезать Битовая карта, содержащий образ **обрезки текста** исходный код:

[![](text-paths-images/clippingtext-small.png "Тройной снимок экрана страницы обрезки текста")](text-paths-images/clippingtext-large.png#lightbox "тройной снимок экрана страницы обрезки текста")

[ `ClippingTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ClippingTextPage.cs) Конструктор класса загружает точечный рисунок, который хранится в виде внедренного ресурса в **мультимедиа** папки решения:

```csharp
public class ClippingTextPage : ContentPage
{
    SKBitmap bitmap;

    public ClippingTextPage()
    {
        Title = "Clipping Text";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        string resourceID = "SkiaSharpFormsDemos.Media.PageOfCode.png";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }
    ...
}
```

`PaintSurface` Обработчик начинается с создания `SKPaint` подходит для текста объекта. `Typeface` Свойство имеет значение, а также `TextSize`, несмотря на то что для этого конкретного приложения `TextSize` произвольно и вызвано лишь свойство. Также Обратите внимание, что не `Style` параметр.

`TextSize` И `Style` параметры свойств не обязательны так как это `SKPaint` объект используется исключительно для `GetTextPath` вызов с использованием текстовая строка «Код». Обработчик затем измеряет результирующего `SKPath` объект и область применения трех преобразований, чтобы отцентрировать ее увеличить или уменьшить масштаб по размеру страницы. Путь можно затем задать в качестве контура обрезки:

```csharp
public class ClippingTextPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Blue);

        using (SKPaint paint = new SKPaint())
        {
            paint.Typeface = SKTypeface.FromFamilyName(null, SKTypefaceStyle.Bold);
            paint.TextSize = 10;

            using (SKPath textPath = paint.GetTextPath("CODE", 0, 0))
            {
                // Set transform to center and enlarge clip path to window height
                SKRect bounds;
                textPath.GetTightBounds(out bounds);

                canvas.Translate(info.Width / 2, info.Height / 2);
                canvas.Scale(info.Width / bounds.Width, info.Height / bounds.Height);
                canvas.Translate(-bounds.MidX, -bounds.MidY);

                // Set the clip path
                canvas.ClipPath(textPath);
            }
        }

        // Reset transforms
        canvas.ResetMatrix();

        // Display bitmap to fill window but maintain aspect ratio
        SKRect rect = new SKRect(0, 0, info.Width, info.Height);
        canvas.DrawBitmap(bitmap,
            rect.AspectFill(new SKSize(bitmap.Width, bitmap.Height)));
    }
}
```

После задания контура обрезки растрового изображения могут отображаться, и он будет обрезан к контурам символов. Обратите внимание на использование [ `AspectFill` ](xref:SkiaSharp.SKRect.AspectFill(SkiaSharp.SKSize)) метод `SKRect` , который вычисляет прямоугольник для заполнения страницы при сохранении пропорций.

**Текстового эффекта: путь** страницы преобразует символ амперсанда путь для создания эффекта пути 1 D. Объект paint с эффектом этот путь используется для вычерчивания контура большую версию этого же символа:

[![](text-paths-images/textpatheffect-small.png "Тройной снимок экрана текстового эффекта: путь страницы")](text-paths-images/textpatheffect-large.png#lightbox "тройной снимок экрана текстового эффекта: путь страницы")

Значительная часть работы в [ `TextPathEffectPath` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TextPathEffectPage.cs) класс происходит в поля и конструктор. Два `SKPaint` объекты, определенные как поля используются для двух различных целей: первый (с именем `textPathPaint`) используется для преобразования амперсанда с `TextSize` 50 в путь для эффект пути 1 D. Второй (`textPaint`) используется для отображения большую версию амперсанд эффект этого пути. По этой причине `Style` этой второй paint объекта задано значение `Stroke`, но `StrokeWidth` свойство не задано, так как это свойство не является обязательным при использовании эффект 1 D:

```csharp
public class TextPathEffectPage : ContentPage
{
    const string character = "@";
    const float littleSize = 50;

    SKPathEffect pathEffect;

    SKPaint textPathPaint = new SKPaint
    {
        TextSize = littleSize
    };

    SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black
    };

    public TextPathEffectPage()
    {
        Title = "Text Path Effect";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Get the bounds of textPathPaint
        SKRect textPathPaintBounds = new SKRect();
        textPathPaint.MeasureText(character, ref textPathPaintBounds);

        // Create textPath centered around (0, 0)
        SKPath textPath = textPathPaint.GetTextPath(character,
                                                    -textPathPaintBounds.MidX,
                                                    -textPathPaintBounds.MidY);
        // Create the path effect
        pathEffect = SKPathEffect.Create1DPath(textPath, littleSize, 0,
                                               SKPath1DPathEffectStyle.Translate);
    }
    ...
}
```

Конструктор сначала использует `textPathPaint` объект для измерения амперсанда с `TextSize` 50. Далее передаются отрицательных координат центра этого прямоугольника `GetTextPath` метод для преобразования текста в путь. Результирующий путь имеет (0, 0) точки в центре символа, который идеально подходит для эффекта пути 1D.

Может показаться, что `SKPathEffect` объект, созданный в конце конструктора может быть присвоено `PathEffect` свойство `textPaint` , а не сохранен в виде поля. Но это не очень хорошо работать, так как он искажено результаты `MeasureText` вызов в `PaintSurface` обработчика:

```csharp
public class TextPathEffectPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Set textPaint TextSize based on screen size
        textPaint.TextSize = Math.Min(info.Width, info.Height);

        // Do not measure the text with PathEffect set!
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(character, ref textBounds);

        // Coordinates to center text on screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        // Set the PathEffect property and display text
        textPaint.PathEffect = pathEffect;
        canvas.DrawText(character, xText, yText, textPaint);
    }
}
```

Что `MeasureText` вызов используется для размещения в центре символов на странице. Чтобы избежать проблем, `PathEffect` свойству объекта paint после для замеряли текст, но перед его отображением.

## <a name="outlines-of-character-outlines"></a>Контуры контуров символов

Обычно [ `GetFillPath` ](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,SkiaSharp.SKRect,System.Single)) метод `SKPaint` преобразует один путь в другой, применяя свойства paint, особенно stroke ширины и путь эффект. При использовании без эффекты пути `GetFillPath` фактически создает путь, описывающий другой путь. Это было продемонстрировано в **коснитесь, чтобы структуры путь** странице в [ **эффекты пути** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) статьи.

Можно также вызвать `GetFillPath` на пути, возвращаемая из `GetTextPath` , но вначале вы может быть не совсем уверены какие должен выглядеть следующим образом.

**Контуров символов структуры** странице показано, как. Весь соответствующий код находится в `PaintSurface` обработчик [ `CharacterOutlineOutlinesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CharacterOutlineOutlinesPage.cs) класса.

Конструктор начинается с создания `SKPaint` объект с именем `textPaint` с `TextSize` свойство зависимости от размера страницы. Это преобразуется в пути, используя `GetTextPath` метод. Аргументы, координат `GetTextPath` эффективно центру пути на экране:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint())
    {
        // Set Style for the character outlines
        textPaint.Style = SKPaintStyle.Stroke;

        // Set TextSize based on screen size
        textPaint.TextSize = Math.Min(info.Width, info.Height);

        // Measure the text
        SKRect textBounds = new SKRect();
        textPaint.MeasureText("@", ref textBounds);

        // Coordinates to center text on screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        // Get the path for the character outlines
        using (SKPath textPath = textPaint.GetTextPath("@", xText, yText))
        {
            // Create a new path for the outlines of the path
            using (SKPath outlinePath = new SKPath())
            {
                // Convert the path to the outlines of the stroked path
                textPaint.StrokeWidth = 25;
                textPaint.GetFillPath(textPath, outlinePath);

                // Stroke that new path
                using (SKPaint outlinePaint = new SKPaint())
                {
                    outlinePaint.Style = SKPaintStyle.Stroke;
                    outlinePaint.StrokeWidth = 5;
                    outlinePaint.Color = SKColors.Red;

                    canvas.DrawPath(outlinePath, outlinePaint);
                }
            }
        }
    }
}
```

`PaintSurface` Обработчик создает новый путь с именем `outlinePath`. Это становится пути назначения в вызове `GetFillPath`. `StrokeWidth` Свойство из 25 причин `outlinePath` для описания структуры пути 25 пикселей уровня Обводка текстовых символов. Этот путь отображается красным цветом ширина штриха 5:

[![](text-paths-images/characteroutlineoutlines-small.png "Тройной снимок экрана страницы контуров символов структуры")](text-paths-images/characteroutlineoutlines-large.png#lightbox "тройной снимок экрана страницы контуров символов структуры")

Внимательно проверьте, и вы увидите перекрытия, где контура делает острого угла. Это обычный артефакты этого процесса.

## <a name="text-along-a-path"></a>Текста вдоль пути

Текст обычно отображается на план по горизонтали. Текст, которые могут быть повернуты для запуска по вертикали или по диагонали, но базовый план по-прежнему представляет собой прямую линию.

Бывают случаи, тем не менее, когда требуется текст, пройдите кривую. Это назначение [ `DrawTextOnPath` ](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) метод `SKCanvas`:

```csharp
public Void DrawTextOnPath (String text, SKPath path, Single hOffset, Single vOffset, SKPaint paint)
```

Текст, заданный в первом аргументе производится Пройдите путь, указанный в качестве второго аргумента. Можно начать текст со смещением в начале пути с `hOffset` аргумент. Обычно путь forms базового плана текста: текст верхние выносные элементы находятся на одной стороне пути, а подстрочные элементы текста находятся в другой. Но можно смещать базового плана текста из пути с `vOffset` аргумент.

Этот метод не содержит средств по обеспечению параметр `TextSize` свойство `SKPaint` текст размера идеально для выполнения в начале пути до конца. Иногда вы можете выяснить, размер текста самостоятельно. В других случаях, вам потребуется использовать функции измерения путь для описанных в следующей статье на [ **сведения о пути и перечисление**](information.md).

**Циклическая текст** программы переносит текст вокруг круга. Это легко определить длину окружности круга, что позволяет легко выбрать размер текста, чтобы точно соответствовать. `PaintSurface` Обработчик [ `CircularTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CircularTextPage.cs) класс вычисляет радиус круга на основе размера страницы. Этот круг становится `circularPath`:

```csharp
public class CircularTextPage : ContentPage
{
    const string text = "xt in a circle that shapes the te";
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath circularPath = new SKPath())
        {
            float radius = 0.35f * Math.Min(info.Width, info.Height);
            circularPath.AddCircle(info.Width / 2, info.Height / 2, radius);

            using (SKPaint textPaint = new SKPaint())
            {
                textPaint.TextSize = 100;
                float textWidth = textPaint.MeasureText(text);
                textPaint.TextSize *= 2 * 3.14f * radius / textWidth;

                canvas.DrawTextOnPath(text, circularPath, 0, 0, textPaint);
            }
        }
    }
}
```

`TextSize` Свойство `textPaint` затем корректируются таким образом, чтобы ширина текста соответствует длины окружности круга:

[![](text-paths-images/circulartext-small.png "Тройной снимок экрана страницы циклическая текст")](text-paths-images/circulartext-large.png#lightbox "тройной снимок экрана страницы циклическая текста")

Сам текст был выбран для также быть в некоторой степени циклическая: слово «circle» является предметом предложения и объект предложная фразы.

## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
