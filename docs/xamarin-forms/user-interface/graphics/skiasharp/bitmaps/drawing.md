---
title: Создание и рисования в SkiaSharp точечных рисунков
description: Узнайте, как создать SkiaSharp точечные рисунки и нарисуйте на эти точечные рисунки, создав холст, на их основе.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 79BD3266-D457-4E50-BDDF-33450035FA0F
author: charlespetzold
ms.author: chape
ms.date: 07/17/2018
ms.openlocfilehash: fa32b2bdb95044c8171542ff4156ec3c15747372
ms.sourcegitcommit: 7f2e44e6f628753e06a5fe2a3076fc2ec5baa081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39131499"
---
# <a name="creating-and-drawing-on-skiasharp-bitmaps"></a>Создание и рисования в SkiaSharp точечных рисунков

Вы видели, как приложение может загрузить растровые изображения из Интернета, ресурсы приложения и библиотеке фотографий пользователя. Можно также создать новые растровые изображения в вашем приложении. Самый простой подход включает в себя один из конструкторов [ `SKBitmap` ](https://developer.xamarin.com/api/constructor/SkiaSharp.SKBitmap.SKBitmap/p/System.Int32/System.Int32/System.Boolean/):

```csharp
SKBitmap bitmap = new SKBitmap(width, height);
```

`width` И `height` параметры являются целыми числами и указать размеры точечного рисунка в пикселах. Этот конструктор создает цветное растровое изображение с четырьмя байтами на пиксель: один байт для красного, зеленого, синего и компоненты с альфа-канала (прозрачности). 

После создания нового растрового изображения, необходимо получить что-то в области растрового изображения. Обычно это можно сделать одним из двух способов:

- Рисование изображения на точечный рисунок с использованием стандарта `Canvas` методов рисования.
- Прямой доступ к битов пикселов.

В этой статье показано, первый подход:

![Рисование образец](drawing-images/DrawingSample.png "Рисование образца")

В этой статье рассматривается второй подход [ **доступ к пикселов точечного рисунка SkiaSharp**](pixel-bits.md).

## <a name="drawing-on-the-bitmap"></a>Рисование изображения на точечный рисунок

Рисование на поверхности растрового изображения является таким же, как рисование на дисплей. Чтобы нарисовать на дисплей, необходимо получить `SKCanvas` объекта из `PaintSurface` аргументы события. Можно рисовать точечный рисунок, создав `SKCanvas` с помощью [ `SKCanvas` ](https://developer.xamarin.com/api/constructor/SkiaSharp.SKCanvas.SKCanvas/p/SkiaSharp.SKBitmap/) конструктор:

```csharp
SKCanvas canvas = new SKCanvas(bitmap);
```

После завершения рисования изображения на точечный рисунок, можно освободить `SKCanvas` объекта. По этой причине `SKCanvas` конструктор обычно вызывается `using` инструкции:

```csharp
using (SKCanvas canvas = new SKCanvas(bitmap))
{
    ··· // call drawing function
}
```

Затем можно отобразить растровое изображение. В дальнейшем, можно создать новую программу `SKCanvas` же растрового изображения и нарисовать на нем некоторые новые на основе объекта.

**Точечного рисунка Hello** странице в **[SkiaSharpFormsDemos](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)** приложение записывает текст «Hello, точечный рисунок!» точечный рисунок и затем отображает эту несколько раз для точечных рисунков.  

Конструктор `HelloBitmapPage` начинается с создания `SKPaint` объекта для отображения текста. Он определяет размеры текстовой строки и создает точечный рисунок с этих измерений. Затем он создает `SKCanvas` объекта на его основе, вызовы `Clear`, а затем вызывает `DrawText`. Рекомендуется всегда вызывать `Clear` с точечного так как вновь созданного точечного может содержать случайных данных.

Конструктор завершает путем создания `SKCanvasView` объект для отображения растрового изображения:

```csharp
public partial class HelloBitmapPage : ContentPage
{
    const string TEXT = "Hello, Bitmap!";
    SKBitmap helloBitmap;

    public HelloBitmapPage()
    {
        Title = TEXT;

        // Create bitmap and draw on it
        using (SKPaint textPaint = new SKPaint { TextSize = 48 })
        {
            SKRect bounds = new SKRect();
            textPaint.MeasureText(TEXT, ref bounds);

            helloBitmap = new SKBitmap((int)bounds.Right,
                                       (int)bounds.Height);

            using (SKCanvas bitmapCanvas = new SKCanvas(helloBitmap))
            {
                bitmapCanvas.Clear();
                bitmapCanvas.DrawText(TEXT, 0, -bounds.Top, textPaint);
            }
        }

        // Create SKCanvasView to view result 
        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Aqua);

        for (float y = 0; y < info.Height; y += helloBitmap.Height)
            for (float x = 0; x < info.Width; x += helloBitmap.Width)
            {
                canvas.DrawBitmap(helloBitmap, x, y);
            }
    }
}
```

`PaintSurface` Обработчик отображает точечный рисунок в несколько строк и столбцов области отображения. Обратите внимание, что `Clear` метод в `PaintSurface` обработчик имеет аргумент `SKColors.Aqua`, который цвета фона поверхности отображения:

[![Привет, растрового изображения! ] (drawing-images/HelloBitmap.png "Hello, растрового изображения!")](drawing-images/HelloBitmap-Large.png#lightbox)

Внешний вид зеленовато-голубой фон показывает, что точечный рисунок является прозрачным за исключением текста.

## <a name="clearing-and-transparency"></a>Очистка и прозрачности

Отображение **точечного рисунка Hello** страница демонстрирует, что точечный рисунок, является прозрачным за исключением черный текст программу, созданную. Вот почему зеленовато-голубой цвет поверхности отображения.

В документации по `Clear` методы `SKCanvas` описывает их с помощью инструкции: «Заменяет все пиксели в текущей картинки canvas». Использовать слово «заменяет» показывает важной характеристикой эти методы: все методы рисования объекта `SKCanvas` добавить что-либо существующие поверхность отображения. `Clear` Методы _замените_ уже существует.

`Clear` существует в двух разных версиях: 

- [ `Clear` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Clear/p/SkiaSharp.SKColor/) Метод с `SKColor` параметра заменяет пикселы поверхность отображения пикселей этого цвета.

- [ `Clear` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Clear()/) Метод без параметров заменяет пиксели с [ `SKColors.Empty` ](https://developer.xamarin.com/api/property/SkiaSharp.SKColors.Empty/) цвет, являющийся цветом, в которой все компоненты (красный, зеленый, синий и альфа) присваивается нулевое значение. Этот цвет иногда называют «прозрачный черный».

Вызов `Clear` без аргументов на точечного инициализирует все растровое изображение полностью прозрачным. Обычно ничего впоследствии нарисован на растровое изображение будет непрозрачных или частично прозрачных.

Вот что-то, чтобы повторить: В **точечного рисунка Hello** страницы, замените `Clear` метод применяется к `bitmapCanvas` на следующее:

```csharp
bitmapCanvas.Clear(new SKColor(255, 0, 0, 128));
```

Порядок `SKColor` параметры конструктора красного, зеленого, синего и альфа-канал, где каждое значение может изменяться от 0 до 255. Имейте в виду, что альфа-значение 0 является прозрачным, альфа-значение 255 — непрозрачный.

Значение (255, 0, 0, 128) очищает пиксели точечного рисунка в красный пиксели с прозрачностью 50%. Это означает, что точечный рисунок фона полупрозрачным. Полупрозрачный красный фон растрового изображения сочетает с зеленовато-голубой фон области отображения, чтобы создать на сером фоне.

Попробуйте установить прозрачный черный цвет текста, поместив следующее присвоение `SKPaint` инициализатора:

```csharp
Color = new SKColor(0, 0, 0, 0)
```

Вы можете подумать, что этот Прозрачный текст создаст полностью прозрачные области растрового изображения, через который вы видите зеленовато-голубой фон поверхность отображения. Но это не так. Текст рисуется поверх уже изображения на точечный рисунок. Прозрачный текст не видны вообще.

Не `Draw` когда-либо методом точечный рисунок более прозрачным. Только `Clear` можно сделать.

## <a name="bitmap-color-types"></a>Цвет типов для точечных рисунков

Самый простой `SKBitmap` конструктор позволяет задать ширину целое число пикселей и высоту для растрового изображения. Другие `SKBitmap` конструкторы являются более сложными. Эти конструкторы требуют аргументов два типа перечисления: [ `SKColorType` ](https://developer.xamarin.com/api/type/SkiaSharp.SKColorType/) и [ `SKAlphaType` ](https://developer.xamarin.com/api/type/SkiaSharp.SKAlphaType/). Другие конструкторы используют [ `SKImageInfo` ](https://developer.xamarin.com/api/type/SkiaSharp.SKImageInfo/) структуру, которая объединяет эту информацию.

`SKColorType` Перечисление имеет 9 членов. Каждый из этих элементов описаны определенным способом хранения пиксели растрового изображения.

- `Unknown`
- `Alpha8` &mdash; Каждый пиксел имеет 8 бит, представляющий альфа — от полной прозрачности до полной непрозрачности
- `Rgb565` &mdash; Каждый пиксел занимает 16 бит, 5 бит для красного и синего и 6 для зеленого
- `Argb4444` &mdash; Каждый пиксел занимает 16 бит, 4 каждой альфа, красного, зеленого и синего
- `Rgba8888` &mdash; Каждый пиксель — 32 бита, 8 каждого для красный, зеленый, синий и альфа-канала
- `Bgra8888` &mdash; Каждый пиксель — 32 бита, 8 каждый синий, зеленый, красный и альфа-канал
- `Index8` &mdash; Каждый пиксел имеет 8 бит и представляет индекс в [`SKColorTable`](https://developer.xamarin.com/api/type/SkiaSharp.SKColorTable/)
- `Gray8` &mdash; Каждый пиксел имеет 8 бит, представляющий оттенком серого от черного к белому
- `RgbaF16` &mdash; Каждый пиксел имеет 64 бита, с помощью красного, зеленого, синего и альфа-канала в формате с плавающей запятой 16-разрядное

Два формата, где каждый пиксель — 32 пикселя (4 байта) часто называются _полноцветного_ форматы. Многие другие форматы даты на момент времени, когда сами отображает видео не поддерживает полный цвета. Точечные рисунки, ограниченный цвета были достаточно для этих экранов и разрешенные точечные рисунки и занимают меньше места в памяти. 

Наши дни программистов почти всегда используйте full цветных точечных рисунков и не трудитесь с другими форматами. Исключением является `RgbaF16` формате, что позволяет разрешения больше, чем даже форматы полноцветного. Тем не менее этот формат используется для специализированных целей, например обработка медицинских изображений и не имеет смысла при использовании стандартных отображает цветные.

Этой серии статей будет ограничивать себя `SKBitmap` цвета форматы, используемые по умолчанию, если аргумент `SKColorType` член является указанная команда. Этот формат по умолчанию зависит от базовой платформы. Для платформ, поддерживаемых Xamarin.Forms является тип цвета по умолчанию:

- `Rgba8888` для iOS и Android
- `Bgra8888` для универсальной платформы Windows

Единственная разница в порядок 4 байта в памяти, и это только становится проблемой при прямой доступ к битов пикселов. Это не становятся важными, пока не перейдете к статье [ **доступ к пикселов точечного рисунка SkiaSharp**](pixel-bits.md).

`SKAlphaType` Перечисление содержит четыре члена:

- `Unknown`
- `Opaque` &mdash; растровое изображение имеет без прозрачности
- `Premul` &mdash; цветовые компоненты предварительно умноженное альфа-компонент
- `Unpremul` &mdash; цветовые компоненты не заранее умножаются на альфа-компонент

Ниже приведен пиксель red точечного рисунка 4-байтовое с прозрачностью 50%, с количеством байтов, в порядке красный, зеленый, синий цвет, альфа-канала.

0xFF 0x00 0x00 0x80

При отображении растровое изображение, содержащее полупрозрачным пикселей на экране компоненты цвет каждого пикселя растрового изображения должен быть умножен значение альфа-компонент пикселя и компонентов цвета пикселя соответствующий поверхности отображения необходимо умножить по 255 минус альфа-значение. Затем можно объединить два пикселей. Точечный рисунок можно подготовить к просмотру быстрее цветовых компонентов пиксели точечного рисунка уже были предварительно mulitplied альфа-значением. Этот же красный пиксель, будут храниться следующим образом в формат с предварительным умножением:

0x80 0x00 0x00 0x80

Это улучшение производительности именно `SkiaSharp` растровые изображения по умолчанию создаются с помощью `Premul` формат. Но опять же, необходимо узнать это, только в том случае, когда доступ и использовать биты пикселов.

## <a name="drawing-on-existing-bitmaps"></a>Рисунок на существующих растровых изображений

Это не требуется создавать новое растровое изображение для рисования на нем. Также можно рисовать существующего растрового изображения. 

**Monkey Moustache** страница использует свой конструктор для загрузки **MonkeyFace.png** изображения. Затем он создает `SKCanvas` объекта на его основе и использует `SKPaint` и `SKPath` объектов для рисования moustache на нем:

```csharp
public partial class MonkeyMoustachePage : ContentPage
{
    SKBitmap monkeyBitmap;

    public MonkeyMoustachePage()
    {
        Title = "Monkey Moustache";

        monkeyBitmap = BitmapExtensions.LoadBitmapResource(GetType(),
            "SkiaSharpFormsDemos.Media.MonkeyFace.png");

        // Create canvas based on bitmap
        using (SKCanvas canvas = new SKCanvas(monkeyBitmap))
        {
            using (SKPaint paint = new SKPaint())
            {
                paint.Style = SKPaintStyle.Stroke;
                paint.Color = SKColors.Black;
                paint.StrokeWidth = 24;
                paint.StrokeCap = SKStrokeCap.Round;

                using (SKPath path = new SKPath())
                {
                    path.MoveTo(380, 390);
                    path.CubicTo(560, 390, 560, 280, 500, 280);

                    path.MoveTo(320, 390);
                    path.CubicTo(140, 390, 140, 280, 200, 280);

                    canvas.DrawPath(path, paint);
                }
            }
        }

        // Create SKCanvasView to view result 
        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(monkeyBitmap, info.Rect, BitmapStretch.Uniform);
    }
}
```

Конструктор завершает путем создания `SKCanvasView` которого `PaintSurface` обработчик просто отображает результат:

[![Повозиться Moustache](drawing-images/MonkeyMoustache.png "повозиться Moustache")](drawing-images/MonkeyMoustache-Large.png#lightbox)

## <a name="copying-and-modifying-bitmaps"></a>Копирование и изменение точечных рисунков

Методы `SKCanvas` , можно использовать для рисования на точечный рисунок включают `DrawBitmap`. Это означает, что можно нарисовать одного точечного рисунка на другом, обычно его изменения каким-либо образом.

Наиболее гибким способом изменить точечный рисунок вплоть до установления самих битах пикселов, тему описано в статье  **[пикселов точечного рисунка доступ к SkiaSharp](pixel-bits.md)**. Но существуют другие методы для изменения точечных рисунков, которому не требуется доступ к битов пикселов.

Следующий точечный рисунок, включенные в **[SkiaSharpFormsDemos](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)** приложения — 360 пикселей в ширину и 480 пикселов в высоту:

![Mountain Climbers](drawing-images/MountainClimbers.jpg "Mountain Climbers")

Предположим, что вы не получили разрешение из monkey слева для публикации этой фотографии. Одним из решений является скрытия лиц monkey, используя технику _pixelization_. Пиксели поверхности заменяются блоки цветов нельзя внести на возможности. Блоки цветов обычно являются производными от исходного изображения как среднее значение цвета пикселей, соответствующий этих блоков. Но не нужно выполнять усреднение самостоятельно. Это происходит автоматически при копировании в меньший размер пикселей растрового изображения. 

Слева monkey лиц занимает приблизительно 72 пикселя квадратную область с верхнего левого угла в точке (112, 238). Давайте замените 9 x 9 массив цветных блоков, каждый из которых является 8-by-8 пикселей квадрат этой области square 72 пикселя.

**Pixelize изображение** страница загружается в этот точечный рисунок и сначала создает небольшой 9 пикселей квадратный Битовая карта называется `faceBitmap`. Это место назначения для копирования только monkey лиц. Прямоугольник назначения представляет собой квадрат просто 9-пикселей, но исходный прямоугольник представляет собой квадрат на 72 пикселя. Каждый блок 8-by-8 пикселей источника консолидируются вниз, чтобы только один пиксель в вычисление среднего значения цвета.

Следующим шагом является для копирования исходного растрового изображения в растровом такого же размера, который вызывается `pixelizedBitmap`. Мало места `faceBitmap` поверх всего этого копируется с прямоугольник назначения square 72 пикселя таким образом, чтобы каждый пиксель `faceBitmap` развертывании до 8 раз размера:

```csharp
public class PixelizedImagePage : ContentPage
{
    SKBitmap pixelizedBitmap;

    public PixelizedImagePage ()
    {
        Title = "Pixelize Image";

        SKBitmap originalBitmap = BitmapExtensions.LoadBitmapResource(GetType(),
            "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

        // Create tiny bitmap for pixelized face
        SKBitmap faceBitmap = new SKBitmap(9, 9);

        // Copy subset of original bitmap to that
        using (SKCanvas canvas = new SKCanvas(faceBitmap))
        {
            canvas.Clear();
            canvas.DrawBitmap(originalBitmap,
                              new SKRect(112, 238, 184, 310),   // source
                              new SKRect(0, 0, 9, 9));          // destination

        }

        // Create full-sized bitmap for copy
        pixelizedBitmap = new SKBitmap(originalBitmap.Width, originalBitmap.Height);

        using (SKCanvas canvas = new SKCanvas(pixelizedBitmap))
        {
            canvas.Clear();

            // Draw original in full size
            canvas.DrawBitmap(originalBitmap, new SKPoint());

            // Draw tiny bitmap to cover face
            canvas.DrawBitmap(faceBitmap, 
                              new SKRect(112, 238, 184, 310));  // destination
        }

        // Create SKCanvasView to view result
        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(pixelizedBitmap, info.Rect, BitmapStretch.Uniform);
    }
}
```

Конструктор завершает путем создания `SKCanvasView` для отображения результата:

[![Изображение pixelize](drawing-images/PixelizeImage.png "Pixelize образа")](drawing-images/PixelizeImage-Large.png#lightbox)

<a name="rotating-bitmaps" />

## <a name="rotating-bitmaps"></a>Поворот точечных рисунков

Другой обычной задачей поворот точечных рисунков. Это особенно полезно при получении точечных рисунков из медиатеки iPhone или iPad. Если устройство находится в определенной ориентации только при создании снимка фотографию, изображен ногами или сторону.

При включении точечный рисунок ногами потребуется создание другого точечного рисунка тот же размер, что первый, а затем установив преобразование, поворот на 180 градусов при копировании первый, второй. Во всех примерах в этом разделе `bitmap` является `SKBitmap` , необходимый для поворота объекта:

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.RotateDegrees(180, bitmap.Width / 2, bitmap.Height / 2);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

При смене на 90 градусов, вам нужно создать Битовая карта, имеет другой размер по сравнению с исходным путем переключения высоту и ширину. К примеру Если исходный точечный рисунок 1200 пикселей в ширину и 800 пикселов в высоту, повернутый растрового изображения — 800 пикселов в ширину и 1200 пикселей в ширину. Задайте перенос и поворот, так что точечный рисунок повернутый относительно его верхнего левого угла и затем приходят к работе в представлении. (Имейте в виду, что `Translate` и `RotateDegrees` методы вызываются в порядке, обратном тому, чтобы они применялись определенным способом.) Ниже приведен код для поворот по часовой стрелке на 90 градусов:

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Height, bitmap.Width);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.Translate(bitmap.Height, 0);
    canvas.RotateDegrees(90);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```                        

А вот ту же функцию для поворота 90 градусов против часовой стрелки:

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Height, bitmap.Width);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.Translate(0, bitmap.Width);
    canvas.RotateDegrees(-90);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

Эти два метода используются в **головоломки Photo** страниц, описанное в статье [ **Обрезка точечные рисунки SkiaSharp**](cropping.md#tile-division).

Это программа, которая позволяет пользователю поворот растрового изображения на 90 градусов должен реализовать только одну функцию для поворот на 90 градусов. Пользователь затем можно вращать в любой шагом 90 градусов, повторное выполнение этого одной функции.

Программы можно также вращать Битовая карта, любой суммы. Один из простых подходов является изменение функции, которая выполняет поворот на 180 градусов, заменив 180 обобщенный `angle` переменной:

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.RotateDegrees(angle, bitmap.Width / 2, bitmap.Height / 2);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

Тем не менее в общем случае эта логика позволяет обрезать off углов повернутый растрового изображения. Более эффективный подход заключается в вычислении размера повернутый растрового изображения, используя тригонометрических вычислений для включения этих углов. 

Этот тригонометрических вычислений отображается в **Ротатор точечного рисунка** страницы. Файл XAML создает экземпляр `SKCanvasView` и `Slider` , в диапазоне от 0 до 360 градусов с `Label` отображающий текущее значение:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.BitmapRotatorPage"
             Title="Bitmap Rotator">
    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="slider"
                Maximum="360"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference slider},
                              Path=Value,
                              StringFormat='Rotate by {0:F0}&#x00B0;'}"
               HorizontalTextAlignment="Center" />

    </StackLayout>
</ContentPage>
```

Файл с выделенным кодом загружает ресурса точечного рисунка и сохраняет его в виде статического поля только для чтения с именем `originalBitmap`. Растровое изображение, отображаемое в `PaintSurface` обработчик `rotatedBitmap`, который первоначально задается `originalBitmap`:

```csharp
public partial class BitmapRotatorPage : ContentPage
{
    static readonly SKBitmap originalBitmap = 
        BitmapExtensions.LoadBitmapResource(typeof(BitmapRotatorPage),
            "SkiaSharpFormsDemos.Media.Banana.jpg");

    SKBitmap rotatedBitmap = originalBitmap;

    public BitmapRotatorPage ()
    {
        InitializeComponent ();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(rotatedBitmap, info.Rect, BitmapStretch.Uniform);
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        double angle = args.NewValue;
        double radians = Math.PI * angle / 180;
        float sine = (float)Math.Abs(Math.Sin(radians));
        float cosine = (float)Math.Abs(Math.Cos(radians));
        int originalWidth = originalBitmap.Width;
        int originalHeight = originalBitmap.Height;
        int rotatedWidth = (int)(cosine * originalWidth + sine * originalHeight);
        int rotatedHeight = (int)(cosine * originalHeight + sine * originalWidth);

        rotatedBitmap = new SKBitmap(rotatedWidth, rotatedHeight);

        using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
        {
            canvas.Clear(SKColors.LightPink);
            canvas.Translate(rotatedWidth / 2, rotatedHeight / 2);
            canvas.RotateDegrees((float)angle);
            canvas.Translate(-originalWidth / 2, -originalHeight / 2);
            canvas.DrawBitmap(originalBitmap, new SKPoint());
        }

        canvasView.InvalidateSurface();
    }
}
```

`ValueChanged` Обработчик `Slider` выполняет операции, которые создают новый `rotatedBitmap` основании угол поворота. Новая ширина и высота основаны на absolute values из синуса и косинусов исходной ширины и высоты. Преобразования, используемые для рисования исходного растрового изображения на повернутый растрового изображения переместить центре исходного растрового изображения от сервера-источника, а затем повернуть ее на указанное число градусов и преобразовать этот центр в центр повернутый растрового изображения. ( `Translate` И `RotateDegrees` методы вызываются в обратном порядке, чем как они применяются.)

Обратите внимание на использование `Clear` метод для установки фона `rotatedBitmap` Светло-розовый. Это исключительно для иллюстрации размер `rotatedBitmap` на экране:

[![Битовая карта Ротатор](drawing-images/BitmapRotator.png "Ротатор для точечных рисунков")](drawing-images/BitmapRotator-Large.png#lightbox)

Повернутый точечного рисунка достаточно большой, чтобы включить всего исходного растрового изображения, но не больше.

## <a name="flipping-bitmaps"></a>Зеркальное отображение точечных рисунков

Вызывается другая операция, типичные для выполнения на точечных рисунков _переворот_. По существу растровое изображение поворачивается в трех измерениях вокруг вертикальной или горизонтальной оси в центре растрового изображения. Вертикальное зеркальное отражение создает Зеркальное изображение.

**Флиппере точечного рисунка** странице в **[SkiaSharpFormsDemos](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)** показано приложение этих процессов. Файл XAML содержит `SKCanvasView` и две кнопки для переворот по вертикали и горизонтали:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.BitmapFlipperPage"
             Title="Bitmap Flipper">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>
        
        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Button Text="Flip Vertical"
                Grid.Row="1" Grid.Column="0"
                Margin="0, 10"
                Clicked="OnFlipVerticalClicked" />

        <Button Text="Flip Horizontal"
                Grid.Row="1" Grid.Column="1"
                Margin="0, 10"
                Clicked="OnFlipHorizontalClicked" />
    </Grid>
</ContentPage>
```

Файл с выделенным кодом реализует эти две операции в `Clicked` обработчики для кнопок: 

```csharp
public partial class BitmapFlipperPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(BitmapRotatorPage),
            "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    public BitmapFlipperPage()
    {
        InitializeComponent();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform);
    }

    void OnFlipVerticalClicked(object sender, ValueChangedEventArgs args)
    {
        SKBitmap flippedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

        using (SKCanvas canvas = new SKCanvas(flippedBitmap))
        {
            canvas.Clear();
            canvas.Scale(-1, 1, bitmap.Width / 2, 0);
            canvas.DrawBitmap(bitmap, new SKPoint());
        }

        bitmap = flippedBitmap;
        canvasView.InvalidateSurface();
    }

    void OnFlipHorizontalClicked(object sender, ValueChangedEventArgs args)
    {
        SKBitmap flippedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

        using (SKCanvas canvas = new SKCanvas(flippedBitmap))
        {
            canvas.Clear();
            canvas.Scale(1, -1, 0, bitmap.Height / 2);
            canvas.DrawBitmap(bitmap, new SKPoint());
        }

        bitmap = flippedBitmap;
        canvasView.InvalidateSurface();
    }
}
```

Вертикальная отражением достигается путем преобразования изменения масштаба с Коэффициент горизонтального масштабирования из &ndash;1. Центр масштабирования является центру растрового изображения по вертикали. Горизонтальное переворачивание является преобразование масштабирования с коэффициент вертикального масштабирования из &ndash;1. 

Как видно из обратном недоставку monkey "рубашка", зеркальное отображение не является таким же, как поворот. Но, как показано на снимке экрана универсальной платформы Windows, в правой части, зеркальное отображение, и по горизонтали и вертикали так же, как поворот на 180 градусов:

[![Битовая карта Флиппере](drawing-images/BitmapFlipper.png "Флиппере для точечных рисунков")](drawing-images/BitmapFlipper-Large.png#lightbox)

Другой распространенных задач, которая может обрабатываться с помощью сходные методы Обрезка растровое изображение, чтобы прямоугольник. Это описано в следующей статье [ **Обрезка точечные рисунки SkiaSharp**](cropping.md).

## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
