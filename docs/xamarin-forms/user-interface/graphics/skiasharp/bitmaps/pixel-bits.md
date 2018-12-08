---
title: Доступ к биты пикселов точечного рисунка SkiaSharp
description: Обнаружить различные методы для доступа и изменения битов пикселов точечных рисунков SkiaSharp.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: DBB58522-F816-4A8C-96A5-E0236F16A5C6
author: davidbritch
ms.author: dabritch
ms.date: 07/11/2018
ms.openlocfilehash: cd7c8484827a038bbcf11180296547ea6fedf929
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53059208"
---
# <a name="accessing-skiasharp-bitmap-pixel-bits"></a>Доступ к биты пикселов точечного рисунка SkiaSharp

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

Как было показано в этой статье [ **SkiaSharp сохранение растровых изображений в файлы**](saving.md), растровые изображения обычно хранятся в файлах в сжатом формате, например, JPEG или PNG. В отличие от SkiaSharp растрового изображения, хранящиеся в памяти не сжимаются. Они хранятся как ряд последовательных точках. Этот формат без сжатия упрощает перенос растровые изображения на поверхность отображения.

Блок памяти, занятый Битовая карта SkiaSharp организована очень простым способом: он начинается с первой строки пикселей слева направо, а затем продолжает операции со второй строки. Для full цветных точечных рисунков каждый пиксель состоит из четырех байтов это означает, что общий объем памяти пространства, необходимого растрового изображения четыре раза произведение высоту и ширину.

В этой статье описывается, как приложения могут получить доступ к пикселов, либо непосредственно открыть блок памяти пикселей растрового изображения, или косвенно. В некоторых случаях программа может потребоваться проанализировать пикселей изображения и создать гистограмму какого-либо рода. Обычно приложения можно создать уникальных образов алгоритмически, создав пикселей растрового изображения:

![Пиксель битов примеры](pixel-bits-images/PixelBitsSample.png "пикселей битов образца")

## <a name="the-techniques"></a>Методы

SkiaSharp предоставляет несколько методов для доступа к биты пикселов точечного рисунка. Какой из них выбран обычно связано с неким объектой удобства (что относится к обслуживанию и Простота отладки) и производительности. В большинстве случаев, вы будете использовать одно из следующих методов и свойств `SKBitmap` для доступа к пиксели:

- `GetPixel` И `SetPixel` методы позволят вам получить или задать цвет одного пикселя.
- `Pixels` Свойство получает массив цвета пикселя для всего растрового изображения, или задает массив цветов.
- `GetPixels` Возвращает адрес памяти пикселей растрового изображения.
- `SetPixels` Заменяет адрес памяти пикселей растрового изображения.

Можно представить первые две методики, как «высокий уровень» и Вторые два как «низкий» уровень. Некоторые методы и свойства, которые можно использовать, но они наиболее полезны.

Чтобы можно было увидеть разницу в производительности между этими способами [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) приложение содержит страницу с именем **градиента точечного рисунка** , Создает точечный рисунок с пикселей, в которых сочетаются оттенки красного и синего для создания градиента. Программа создает восемь разных копий растрового изображения, все с помощью различных методов для настройки пиксели растрового изображения. Каждый из этих восьми точечные рисунки создается в отдельный метод, который также задает краткое описание этого метода и вычисляет время, необходимое для установки всех пикселей. Каждый метод перебирает логики пикселях 100 раз получить лучшую оценку производительности.

### <a name="the-setpixel-method"></a>Метод SetPixel

Если требуется задать или получить несколько отдельных пикселях [ `SetPixel` ](xref:SkiaSharp.SKBitmap.SetPixel(System.Int32,System.Int32,SkiaSharp.SKColor)) и [ `GetPixel` ](xref:SkiaSharp.SKBitmap.GetPixel(System.Int32,System.Int32)) идеально подходят методы. Для каждого из этих двух методов Укажите целое число столбцов и строк. Независимо от формата пикселей, эти два метода позволяют получать и устанавливать пикселя `SKColor` значение:

```csharp
bitmap.SetPixel(col, row, color);

SKColor color = bitmap.GetPixel(col, row);
```

`col` Аргумент должен находиться в диапазоне от 0 к одному меньше, чем `Width` свойство растрового изображения, и `row` лежит в диапазоне от 0 к одному меньше, чем `Height` свойство.

Ниже приведен метод **градиента точечного рисунка** , задает содержимое растрового изображения с помощью `SetPixel` метод. Точечный рисунок бывает 256 x 256 пикселей, а `for` циклы жестко закодированы с диапазоном значений:

```csharp
public class GradientBitmapPage : ContentPage
{
    const int REPS = 100;

    Stopwatch stopwatch = new Stopwatch();
    ···
    SKBitmap FillBitmapSetPixel(out string description, out int milliseconds)
    {
        description = "SetPixel";
        SKBitmap bitmap = new SKBitmap(256, 256);

        stopwatch.Restart();

        for (int rep = 0; rep < REPS; rep++)
            for (int row = 0; row < 256; row++)
                for (int col = 0; col < 256; col++)
                {
                    bitmap.SetPixel(col, row, new SKColor((byte)col, 0, (byte)row));
                }

        milliseconds = (int)stopwatch.ElapsedMilliseconds;
        return bitmap;
    }
    ···
}
```

Набор для каждого пикселя цветов имеет красный компонент которой равен столбцу растрового изображения и синего компонента, равным строке. Получающийся точечный рисунок черным в левом верхнем углу, красным в правом верхнем углу, синий в левом нижнем и пурпурный в правом нижнем углу, с градиентом в другом месте.

`SetPixel` Метода вызываться 65 536 раз и независимо от того, как эффективные этот метод может быть, это обычно не рекомендуется выполнить многие вызовы API, если доступен альтернативы. К счастью существует несколько вариантов.

### <a name="the-pixels-property"></a>Свойство пикселей

`SKBitmap` Определяет [ `Pixels` ](xref:SkiaSharp.SKBitmap.Pixels) свойство, которое возвращает массив `SKColor` значения для всего растрового изображения. Можно также использовать `Pixels` задать массив значений цвета для растрового изображения:

```csharp
SKColor[] pixels = bitmap.Pixels;

bitmap.Pixels = pixels;
```

Пиксели, упорядочены в массиве, начиная с первой строки, слева направо, затем второй строки и так далее. Общее число цветов в массиве равно произведению высоту и ширину растрового изображения.

Несмотря на то, что это свойство появляется в том, чтобы быть эффективным, имейте в виду, копируются пикселов из точечного рисунка в массив, а также из массива обратно в точечный рисунок и пиксели преобразуются и `SKColor` значения.

Ниже приведен метод `GradientBitmapPage` класс, который задает растровое изображение с помощью `Pixels` свойство. Метод выделяет `SKColor` массива требуемый размер, но мог бы использовать `Pixels` свойства для создания массива:

```csharp
SKBitmap FillBitmapPixelsProp(out string description, out int milliseconds)
{
    description = "Pixels property";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    SKColor[] pixels = new SKColor[256 * 256];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                pixels[256 * row + col] = new SKColor((byte)col, 0, (byte)row);
            }

    bitmap.Pixels = pixels;

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

Обратите внимание, что индекс `pixels` массива должно вычисляться из `row` и `col` переменные. Строки умножается на количество точек в каждой строке (256 в данном случае), а затем будет добавлен столбец.

`SKBitmap` также определяет аналогичный `Bytes` свойство, который возвращает массив байтов для всего растрового изображения, но это более громоздкий способ для full цветных точечных рисунков.

### <a name="the-getpixels-pointer"></a>Указатель GetPixels

Потенциально является наиболее мощным средством для доступа к пиксели точечного рисунка [ `GetPixels` ](xref:SkiaSharp.SKBitmap.GetPixels), не следует путать с `GetPixel` метод или `Pixels` свойство. Вы сразу же увидеть разницу с `GetPixels` тем, что возвращает что-то очень часто программирования на C#:

```csharp
IntPtr pixelsAddr = bitmap.GetPixels();
```

.NET [ `IntPtr` ](xref:System.IntPtr) тип представляет указатель. Он называется `IntPtr` так как это длина целочисленного значения на собственный процессора компьютера, в которой выполняется программа, обычно 32-разрядный или 64 бита в длину. `IntPtr` , `GetPixels` Возвращает — это адрес фактическое блока памяти, который использует для хранения его пиксели объекта точечного рисунка.

Вы можете преобразовать `IntPtr` в C# указатель типа с помощью [ `ToPointer` ](xref:System.IntPtr.ToPointer) метод. Указатель синтаксис C# является таким же, как C и C++:

```csharp
byte* ptr = (byte*)pixelsAddr.ToPointer();
```

`ptr` Переменная имеет тип _указателя байта_. Это `ptr` переменной предоставляет доступ к отдельным числа байт памяти, которые используются для хранения пиксели. Считывает байт из памяти или запись байта в память использовать следующий код:

```csharp
byte pixelComponent = *ptr;

*ptr = pixelComponent;
```

В этом контексте звездочка является C# _оператор косвенного обращения_ и используется для ссылки на содержимое памяти, на которые указывают `ptr`. Изначально `ptr` точки до получения первого байта первого пикселя первой строки точечного рисунка, но его можно выполнить над `ptr` переменной, чтобы переместить его в другое место внутри точечного рисунка.

Недостатком является то, что можно использовать это `ptr` переменную только в блоке кода, отмеченные `unsafe` ключевое слово. Кроме того сборки должен быть помечен как допускающий небезопасные блоки. Для этого в свойствах проекта.

С использованием указателей в C# — очень мощный, но также очень опасно. Необходимо соблюдать осторожность, что не доступа к памяти за то, что указатель должен ссылаться на. Именно по этой причине используйте указатель связан со словом «небезопасный».

Ниже приведен метод `GradientBitmapPage` класс, который использует `GetPixels` метод. Обратите внимание, что `unsafe` блок, который охватывает весь код, используя указатель байтов:

```csharp
SKBitmap FillBitmapBytePtr(out string description, out int milliseconds)
{
    description = "GetPixels byte ptr";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    IntPtr pixelsAddr = bitmap.GetPixels();

    unsafe
    {
        for (int rep = 0; rep < REPS; rep++)
        {
            byte* ptr = (byte*)pixelsAddr.ToPointer();

            for (int row = 0; row < 256; row++)
                for (int col = 0; col < 256; col++)
                {
                    *ptr++ = (byte)(col);   // red
                    *ptr++ = 0;             // green
                    *ptr++ = (byte)(row);   // blue
                    *ptr++ = 0xFF;          // alpha
                }
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

Когда `ptr` переменной сначала извлекается из `ToPointer` метод, он указывает на первый байт, крайнего левого пикселя первой строки растрового изображения. `for` Циклы для `row` и `col` настраиваются таким образом, чтобы `ptr` может быть увеличен с `++` оператор после каждого байта каждого пикселя. Для других 99 перебирает пиксели `ptr` должно иметь значение обратно в начало растрового изображения.

Каждый пиксель — четыре числа байтов памяти, поэтому каждый байт должен устанавливаться отдельно. Здесь предполагается, что байты в порядке красный, зеленый, синий и альфа-канал, который согласуется с `SKColorType.Rgba8888` тип цвета. Как вы помните, что это тип цвета по умолчанию для iOS и Android, но не для универсальной платформы Windows. По умолчанию UWP создает точечные рисунки с `SKColorType.Bgra8888` тип цвета. По этой причине ожидать получение различных результатов на этой платформе!

Это можно выполнить приведение значения, возвращенного `ToPointer` для `uint` указатель вместо `byte` указатель. Это позволяет пиксель для доступа в одной инструкции. Применение `++` оператор для этого указателя увеличивает его на четыре байта, чтобы он указывал на следующую точку:

```csharp
public class GradientBitmapPage : ContentPage
{
    ···
    SKBitmap FillBitmapUintPtr(out string description, out int milliseconds)
    {
        description = "GetPixels uint ptr";
        SKBitmap bitmap = new SKBitmap(256, 256);

        stopwatch.Restart();

        IntPtr pixelsAddr = bitmap.GetPixels();

        unsafe
        {
            for (int rep = 0; rep < REPS; rep++)
            {
                uint* ptr = (uint*)pixelsAddr.ToPointer();

                for (int row = 0; row < 256; row++)
                    for (int col = 0; col < 256; col++)
                    {
                        *ptr++ = MakePixel((byte)col, 0, (byte)row, 0xFF);
                    }
            }
        }

        milliseconds = (int)stopwatch.ElapsedMilliseconds;
        return bitmap;
    }
    ···
    [MethodImpl(MethodImplOptions.AggressiveInlining)]
    uint MakePixel(byte red, byte green, byte blue, byte alpha) =>
            (uint)((alpha << 24) | (blue << 16) | (green << 8) | red);
    ···
}
```

Пиксель задается с помощью `MakePixel` метод, который создает целое число пикселей от красного, зеленого, синего и альфа-компонентов. Имейте в виду, что `SKColorType.Rgba8888` формат имеет байт упорядочение следующим образом:

RR, GG BB AA

Но целое число, соответствующее этих байтов:

AABBGGRR

Соответствии с архитектурой с прямым порядком байтов первым сохраняется наименее значащим байтом целого числа. Это `MakePixel` метод будет работать неправильно для растровых изображений с `Bgra8888` тип цвета.

`MakePixel` Метод помечается как содержащий [ `MethodImplOptions.AggressiveInlining` ](xref:System.Runtime.CompilerServices.MethodImplOptions) параметр, чтобы стимулировать компилятора, чтобы избежать создания это отдельный метод, а чтобы скомпилировать код, где вызывается метод. Это должно повысить производительность.

Что интересно `SKColor` структура определяет явное преобразование из `SKColor` в целое число без знака, которое означает, что `SKColor` значения могут быть созданы и преобразование `uint` может использоваться вместо `MakePixel`:

```csharp
SKBitmap FillBitmapUintPtrColor(out string description, out int milliseconds)
{
    description = "GetPixels SKColor";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    IntPtr pixelsAddr = bitmap.GetPixels();

    unsafe
    {
        for (int rep = 0; rep < REPS; rep++)
        {
            uint* ptr = (uint*)pixelsAddr.ToPointer();

            for (int row = 0; row < 256; row++)
                for (int col = 0; col < 256; col++)
                {
                    *ptr++ = (uint)new SKColor((byte)col, 0, (byte)row);
                }
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

Только вопрос состоит в следующем: целое число формат `SKColor` значение порядка `SKColorType.Rgba8888` тип, цвета или `SKColorType.Bgra8888` тип цвета, или это что-то еще полностью? Ответ на этот вопрос должны стать доступными чуть ниже.

### <a name="the-setpixels-method"></a>Метод SetPixels

`SKBitmap` также определяет метод с именем [ `SetPixels` ](xref:SkiaSharp.SKBitmap.SetPixels(System.IntPtr)), который называется следующим образом:

```csharp
bitmap.SetPixels(intPtr);
```

Помните, что `GetPixels` получает `IntPtr` ссылки на блок памяти, используемая для хранения его пиксели растрового изображения. `SetPixels` Вызвать _заменяет_ этот блок памяти, добавив блок памяти, который ссылается `IntPtr` указанный в виде `SetPixels` аргумент. Точечный рисунок освобождает блок памяти, который использовался ранее. В следующий раз `GetPixels` является именем, он получает блока памяти, набор с `SetPixels`.

Поначалу кажется, как если бы `SetPixels` дает больше мощности и производительности, чем `GetPixels` не очень удобно. С помощью `GetPixels` получить блок памяти растрового изображения и получить к нему доступ. С помощью `SetPixels` выделить и получить доступ к памяти и укажите ее в качестве блока памяти растрового изображения.

Однако применение `SetPixels` предлагает синтаксические очевидное преимущество: он позволяет получить доступ к с помощью массива битов пикселов точечного рисунка. Ниже приведен метод `GradientBitmapPage` , демонстрирующий этот способ. Метод сначала определяет многомерный массив, соответствующих байтам пиксели. Первого измерения является строкой, во втором измерении — столбец и соответствует третьему измерению для каждого пикселя четырех компонентов:

```csharp
SKBitmap FillBitmapByteBuffer(out string description, out int milliseconds)
{
    description = "SetPixels byte buffer";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    byte[,,] buffer = new byte[256, 256, 4];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                buffer[row, col, 0] = (byte)col;   // red
                buffer[row, col, 1] = 0;           // green
                buffer[row, col, 2] = (byte)row;   // blue
                buffer[row, col, 3] = 0xFF;        // alpha
            }

    unsafe
    {
        fixed (byte* ptr = buffer)
        {
            bitmap.SetPixels((IntPtr)ptr);
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

Затем после массив заполняется пикселях `unsafe` блока и `fixed` инструкция используется для получения указателя байта, который указывает на этот массив. Этот указатель байтов затем может быть приведен к `IntPtr` для передачи `SetPixels`.

Массив, созданный не должен быть массивом байтов. Это может быть массив целых чисел с помощью только двух измерений для строк и столбцов:

```csharp
SKBitmap FillBitmapUintBuffer(out string description, out int milliseconds)
{
    description = "SetPixels uint buffer";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    uint[,] buffer = new uint[256, 256];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                buffer[row, col] = MakePixel((byte)col, 0, (byte)row, 0xFF);
            }

    unsafe
    {
        fixed (uint* ptr = buffer)
        {
            bitmap.SetPixels((IntPtr)ptr);
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

`MakePixel` Метод снова используется для объединения компонентов цвета в 32-разрядных пиксель.

Для полноты ниже приведен тот же код, но с `SKColor` значение приводится к целое число без знака:

```csharp
SKBitmap FillBitmapUintBufferColor(out string description, out int milliseconds)
{
    description = "SetPixels SKColor";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    uint[,] buffer = new uint[256, 256];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                buffer[row, col] = (uint)new SKColor((byte)col, 0, (byte)row);
            }

    unsafe
    {
        fixed (uint* ptr = buffer)
        {
            bitmap.SetPixels((IntPtr)ptr);
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

### <a name="comparing-the-techniques"></a>Сравнение методов

Конструктор **цвет градиента** страницы вызывает все восемь методов, показанных выше и сохраняет результаты:

```csharp
public class GradientBitmapPage : ContentPage
{
    ···
    string[] descriptions = new string[8];
    SKBitmap[] bitmaps = new SKBitmap[8];
    int[] elapsedTimes = new int[8];

    SKCanvasView canvasView;

    public GradientBitmapPage ()
    {
        Title = "Gradient Bitmap";

        bitmaps[0] = FillBitmapSetPixel(out descriptions[0], out elapsedTimes[0]);
        bitmaps[1] = FillBitmapPixelsProp(out descriptions[1], out elapsedTimes[1]);
        bitmaps[2] = FillBitmapBytePtr(out descriptions[2], out elapsedTimes[2]);
        bitmaps[4] = FillBitmapUintPtr(out descriptions[4], out elapsedTimes[4]);
        bitmaps[6] = FillBitmapUintPtrColor(out descriptions[6], out elapsedTimes[6]);
        bitmaps[3] = FillBitmapByteBuffer(out descriptions[3], out elapsedTimes[3]);
        bitmaps[5] = FillBitmapUintBuffer(out descriptions[5], out elapsedTimes[5]);
        bitmaps[7] = FillBitmapUintBufferColor(out descriptions[7], out elapsedTimes[7]);

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ···
}
```

Конструктор завершает путем создания `SKCanvasView` Показать итоговые растровые изображения. `PaintSurface` Обработчик делит свою поверхность, на восемь прямоугольников и вызывает метод `Display` для отображения каждого из них:

```csharp
public class GradientBitmapPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        int width = info.Width;
        int height = info.Height;

        canvas.Clear();

        Display(canvas, 0, new SKRect(0, 0, width / 2, height / 4));
        Display(canvas, 1, new SKRect(width / 2, 0, width, height / 4));
        Display(canvas, 2, new SKRect(0, height / 4, width / 2, 2 * height / 4));
        Display(canvas, 3, new SKRect(width / 2, height / 4, width, 2 * height / 4));
        Display(canvas, 4, new SKRect(0, 2 * height / 4, width / 2, 3 * height / 4));
        Display(canvas, 5, new SKRect(width / 2, 2 * height / 4, width, 3 * height / 4));
        Display(canvas, 6, new SKRect(0, 3 * height / 4, width / 2, height));
        Display(canvas, 7, new SKRect(width / 2, 3 * height / 4, width, height));
    }

    void Display(SKCanvas canvas, int index, SKRect rect)
    {
        string text = String.Format("{0}: {1:F1} msec", descriptions[index],
                                    (double)elapsedTimes[index] / REPS);

        SKRect bounds = new SKRect();

        using (SKPaint textPaint = new SKPaint())
        {
            textPaint.TextSize = (float)(12 * canvasView.CanvasSize.Width / canvasView.Width);
            textPaint.TextAlign = SKTextAlign.Center;
            textPaint.MeasureText("Tly", ref bounds);

            canvas.DrawText(text, new SKPoint(rect.MidX, rect.Bottom - bounds.Bottom), textPaint);
            rect.Bottom -= bounds.Height;
            canvas.DrawBitmap(bitmaps[index], rect, BitmapStretch.Uniform);
        }
    }
}
```

Чтобы разрешить компилятору оптимизировать код, эта страница выполнялся **выпуска** режим. Вот эту страницу, на эмулятор iPhone 8 MacBook Pro, телефоне Nexus 5 Android и Surface Pro 3 под управлением Windows 10. Из-за различия оборудования избежать сравнения временных показателей производительности между устройствами, но вместо этого посмотреть относительное время на каждом устройстве:

[![Точечный рисунок градиента](pixel-bits-images/GradientBitmap.png "градиента растрового изображения")](pixel-bits-images/GradientBitmap-Large.png#lightbox)

Ниже приведена таблица, объединяющий времени выполнения в миллисекундах:

| API       | Тип данных | iOS  | Android | UWP  |
| --------- | --------- | ----:| -------:| ----:|
| SetPixel  |           | 3.17 |   10.77 | 3.49 |
| Перекрытых    |           | 0.32 |    1,23 | 0,07 |
| GetPixels | byte      | 0,09 |    0.24 | 0.10 |
|           | uint      | 0.06 |    0,26 | 0,05 |
|           | SKColor   | 0.29 |    0,99 | 0,07 |
| SetPixels | byte      | 1.33 |    6.78 | 0,11 |
|           | uint      | 0,14 |    0.69 | 0.06 |
|           | SKColor   | 0.35 |    1.93 | 0.10 |

Как и ожидалось, вызов `SetPixel` 65 536 раз используется как минимум effeicient способ задать пикселов точечного рисунка. Заполнение `SKColor` массива и параметр `Pixels` свойство гораздо лучше и даже выигрывает по сравнению с некоторыми `GetPixels` и `SetPixels` методы. Работа с `uint` пиксельных значениях обычно быстрее, чем отдельный параметр `byte` компоненты и преобразование `SKColor` значение в целое число без знака увеличивает непроизводительные затраты ресурсов к процессу.

Это также интересно сравнить различные градиенты: верхних строк для каждой платформы совпадают и Показать градиента, как предполагалось. Это означает, что `SetPixel` метод и `Pixels` свойство правильно создать пикселов из цветов, независимо от того, базовый формат пикселей.

Следующие две строки, iOS и Android снимки экрана являются также одинаковыми, который подтверждает, что маленький `MakePixel` правильно определен метод по умолчанию `Rgba8888` формат пикселей для этих платформ.

Нижняя строка iOS и Android снимки экрана является обратном направлении, который указывает, что целое число без знака, получить путем приведения `SKColor` значение указывается в формате:

AARRGGBB

Байты располагаются в порядке:

AA BB GG RR

Это `Bgra8888` упорядочение, а не `Rgba8888` упорядочение. `Brga8888` Формат по умолчанию для универсальных Windows платформы, поэтому градиенты в последней строке этого снимка совпадают с первой строки. Но средней две строки являются неправильными, так как предполагается, что код, создание этих маленьких точечных изображений `Rgba8888` упорядочение.

Если вы хотите использовать тот же код для доступа к биты пикселов на каждой платформе, можно явно создать `SKBitmap` одним `Rgba8888` или `Bgra8888` формат. Если вы хотите привести `SKColor` значения пикселов точечного рисунка, используют `Bgra8888`.

## <a name="random-access-of-pixels"></a>Произвольный доступ пикселей

`FillBitmapBytePtr` И `FillBitmapUintPtr` методы в **градиента точечного рисунка** применяющих страницы `for` циклы, предназначенных для заполнения растрового изображения последовательно, верхней строке в нижней строке, а в каждой строки в направлении слева направо. Пиксель может быть установлена с той же инструкции, которые увеличивается указатель.

Иногда бывает необходимо получить доступ к пиксели случайным образом, а не последовательно. Если вы используете `GetPixels` подход, необходимо вычислить указатели, основанные на пересечении строки и столбца. Это показано в **синус радуги** страницу, которая создает точечный рисунок отображение радуги в виде одного цикла синус кривой.

Цвета радуги — проще всего создать с помощью модели цвета HSL (оттенок, насыщенность, яркость). `SKColor.FromHsl` Метод создает `SKColor` значение, используя оттенок значения в диапазоне от 0 до 360 (например, углы круг, но переход от красного, зеленого и синего цветов и обратно red), а также насыщенность и яркость значения от 0 до 100. Для цветов радуги насыщенность должно быть присвоено более 100 и яркость к промежуточной точке 50.

**Синус радуги** создает этот образ, перебор строк растрового изображения и затем циклически 360 оттенок значения. Из каждого значения оттенка он вычисляет столбец точечного рисунка, который также зависит от значения синуса:

```csharp
public class RainbowSinePage : ContentPage
{
    SKBitmap bitmap;

    public RainbowSinePage()
    {
        Title = "Rainbow Sine";

        bitmap = new SKBitmap(360 * 3, 1024, SKColorType.Bgra8888, SKAlphaType.Unpremul);

        unsafe
        {
            // Pointer to first pixel of bitmap
            uint* basePtr = (uint*)bitmap.GetPixels().ToPointer();

            // Loop through the rows
            for (int row = 0; row < bitmap.Height; row++)
            {
                // Calculate the sine curve angle and the sine value
                double angle = 2 * Math.PI * row / bitmap.Height;
                double sine = Math.Sin(angle);

                // Loop through the hues
                for (int hue = 0; hue < 360; hue++)
                {
                    // Calculate the column
                    int col = (int)(360 + 360 * sine + hue);

                    // Calculate the address
                    uint* ptr = basePtr + bitmap.Width * row + col;

                    // Store the color value
                    *ptr = (uint)SKColor.FromHsl(hue, 100, 50);
                }
            }
        }

        // Create the SKCanvasView
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
        canvas.DrawBitmap(bitmap, info.Rect);
    }
}
```

Обратите внимание на то, что конструктор создает точечный рисунок, на основе `SKColorType.Bgra8888` формат:

```csharp
bitmap = new SKBitmap(360 * 3, 1024, SKColorType.Bgra8888, SKAlphaType.Unpremul);
```

Это позволяет программе использовать преобразование `SKColor` значения в `uint` пикселей без проблем. Несмотря на то, что он не играют роль в этой программы, при использовании `SKColor` преобразование в набор точек, необходимо также указать `SKAlphaType.Unpremul` поскольку `SKColor` не умножения его компонентов цвета на альфа-значение.

Затем конструктор использует `GetPixels` метод, чтобы получить указатель на первый пикселей растрового изображения:

```csharp
uint* basePtr = (uint*)bitmap.GetPixels().ToPointer();
```

Для любой конкретной строки и столбца, необходимо добавить значение смещения `basePtr`. Это смещение является строкой времени Ширина растрового изображения, а также столбец:

```csharp
uint* ptr = basePtr + bitmap.Width * row + col;
```

`SKColor` Значение хранится в памяти, с помощью этого указателя:

```csharp
*ptr = (uint)SKColor.FromHsl(hue, 100, 50);
```

В `PaintSurface` обработчик `SKCanvasView`, точечный рисунок растягивается для заполнения области отображения:

[![Синус радуги](pixel-bits-images/RainbowSine.png "радуги синус")](pixel-bits-images/RainbowSine-Large.png#lightbox)

## <a name="from-one-bitmap-to-another"></a>Из одного точечного рисунка в другой

Очень многие задачи обработки изображений в том числе изменить пикселей, так как они передаются из одного точечного рисунка в другую. Этот подход продемонстрирован в **настройку цвета** страницы. Страница загружает один из ресурсов растрового изображения и затем позволяет изменить образ, с использованием трех `Slider` представления:

[![Цветовой коррекции](pixel-bits-images/ColorAdjustment.png "цветовой коррекции")](pixel-bits-images/ColorAdjustment-Large.png#lightbox)

Для каждого цвета пикселя первой `Slider` добавляет значение от 0 до 360 тона, но затем использует оператор, чтобы сохранить результат в диапазоне от 0 до 360, остатка от деления эффективно следует сдвиг цветов по спектра (как показано на снимке экрана универсальной платформы Windows). Второй `Slider` позволяет выбирать между 0,5 и 2 для применения к насыщенности, а третий мультипликативный множитель `Slider` делает то же самое, для яркость, как показано на снимке экрана Android.

Программа поддерживает два растровых изображений, исходного исходное растровое изображение с именем `srcBitmap` и скорректированное точечный рисунок назначения с именем `dstBitmap`. Каждый раз `Slider` перемещается, программа вычисляет все пиксели новых `dstBitmap`. Само собой, пользователям будет поэкспериментировать, переместив `Slider` просматривает очень быстро, поэтому рекомендуется наилучшей производительности, вы можете управлять. Это включает в себя `GetPixels` метод для растровых изображений, источник и назначение.

**Настройку цвета** страницы не контролирует формат цвета точечных рисунков, источника и назначения. Вместо этого он содержит немного другой логикой для `SKColorType.Rgba8888` и `SKColorType.Bgra8888` форматы. Источник и назначение могут быть различные форматы и программа по-прежнему будет работать.

Вот программы, исключая решающее значение `TransferPixels` метод, который передает пиксели форме источника в место назначения. Конструктор присваивает `dstBitmap` равным `srcBitmap`. `PaintSurface` Обработчик отображает `dstBitmap`:

```csharp
public partial class ColorAdjustmentPage : ContentPage
{
    SKBitmap srcBitmap =
        BitmapExtensions.LoadBitmapResource(typeof(FillRectanglePage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    SKBitmap dstBitmap;

    public ColorAdjustmentPage()
    {
        InitializeComponent();

        dstBitmap = new SKBitmap(srcBitmap.Width, srcBitmap.Height);
        OnSliderValueChanged(null, null);
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        float hueAdjust = (float)hueSlider.Value;
        hueLabel.Text = $"Hue Adjustment: {hueAdjust:F0}";

        float saturationAdjust = (float)Math.Pow(2, saturationSlider.Value);
        saturationLabel.Text = $"Saturation Adjustment: {saturationAdjust:F2}";

        float luminosityAdjust = (float)Math.Pow(2, luminositySlider.Value);
        luminosityLabel.Text = $"Luminosity Adjustment: {luminosityAdjust:F2}";

        TransferPixels(hueAdjust, saturationAdjust, luminosityAdjust);
        canvasView.InvalidateSurface();
    }
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(dstBitmap, info.Rect, BitmapStretch.Uniform);
    }
}
```

`ValueChanged` Обработчик для `Slider` представления рассчитывает корректирующие значения и вызовы `TransferPixels`.

Всего `TransferPixels` метод помечен как `unsafe`. Он начинает с получения байтовые указатели для битов пикселов точечных рисунков, оба и затем циклически проходит все строки и столбцы. Из исходного растрового изображения метод получает четыре байта для каждого пикселя. Это могут быть либо `Rgba8888` или `Bgra8888` заказа. Проверка позволяет тип цвета `SKColor` значение должен быть создан. Компоненты HSL затем извлекаются, корректируется и используются для повторного создания `SKColor` значение. В зависимости от того, является ли точечный рисунок назначения `Rgba8888` или `Bgra8888`, байты хранятся в bitmp назначения:

```csharp
public partial class ColorAdjustmentPage : ContentPage
{
    ···
    unsafe void TransferPixels(float hueAdjust, float saturationAdjust, float luminosityAdjust)
    {
        byte* srcPtr = (byte*)srcBitmap.GetPixels().ToPointer();
        byte* dstPtr = (byte*)dstBitmap.GetPixels().ToPointer();

        int width = srcBitmap.Width;       // same for both bitmaps
        int height = srcBitmap.Height;

        SKColorType typeOrg = srcBitmap.ColorType;
        SKColorType typeAdj = dstBitmap.ColorType;

        for (int row = 0; row < height; row++)
        {
            for (int col = 0; col < width; col++)
            {
                // Get color from original bitmap
                byte byte1 = *srcPtr++;         // red or blue
                byte byte2 = *srcPtr++;         // green
                byte byte3 = *srcPtr++;         // blue or red
                byte byte4 = *srcPtr++;         // alpha

                SKColor color = new SKColor();

                if (typeOrg == SKColorType.Rgba8888)
                {
                    color = new SKColor(byte1, byte2, byte3, byte4);
                }
                else if (typeOrg == SKColorType.Bgra8888)
                {
                    color = new SKColor(byte3, byte2, byte1, byte4);
                }

                // Get HSL components
                color.ToHsl(out float hue, out float saturation, out float luminosity);

                // Adjust HSL components based on adjustments
                hue = (hue + hueAdjust) % 360;
                saturation = Math.Max(0, Math.Min(100, saturationAdjust * saturation));
                luminosity = Math.Max(0, Math.Min(100, luminosityAdjust * luminosity));

                // Recreate color from HSL components
                color = SKColor.FromHsl(hue, saturation, luminosity);

                // Store the bytes in the adjusted bitmap
                if (typeAdj == SKColorType.Rgba8888)
                {
                    *dstPtr++ = color.Red;
                    *dstPtr++ = color.Green;
                    *dstPtr++ = color.Blue;
                    *dstPtr++ = color.Alpha;
                }
                else if (typeAdj == SKColorType.Bgra8888)
                {
                    *dstPtr++ = color.Blue;
                    *dstPtr++ = color.Green;
                    *dstPtr++ = color.Red;
                    *dstPtr++ = color.Alpha;
                }
            }
        }
    }
    ···
}
```

Вполне вероятно, производительность этого метода может быть еще более повысить, создавая отдельные методы для различных сочетаний значений типы цвета исходного и конечного точечных рисунков что избежать проверки типа для каждого пикселя. Другой вариант — создать несколько `for` циклы для `col` переменной на основе цвета типа.

## <a name="posterization"></a>Постеризации

Является другой распространенных задание, которое включает в себя доступ к биты пикселов _постеризации_. Номер, если цвета кодируются в пикселей растрового изображения уменьшается таким образом, результат имеет примерно плакат нарисованные от руки с помощью ограниченной цветовую палитру.

**«Постеризовать»** страница выполняет этот процесс на один из образов monkey:

```csharp
public class PosterizePage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(FillRectanglePage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public PosterizePage()
    {
        Title = "Posterize";

        unsafe
        {
            uint* ptr = (uint*)bitmap.GetPixels().ToPointer();
            int pixelCount = bitmap.Width * bitmap.Height;

            for (int i = 0; i < pixelCount; i++)
            {
                *ptr++ &= 0xE0E0E0FF;
            }
        }

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
        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform;
    }
}
```

Код в конструктор обращается к каждого пикселя, выполняет побитовую операцию AND со значением 0xE0E0E0FF и затем сохраняет результат обратно в битовой карте. Значения 0xE0E0E0FF отслеживает старшие 3 разряда каждого компонента цвета и задает младшие 5 битов 0. Вместо того чтобы 2<sup>24</sup> или 16 777 216 цветов, растрового изображения уменьшается до 2<sup>9</sup> или 512 цвета:

[![Постеризация](pixel-bits-images/Posterize.png "постеризация")](pixel-bits-images/постеризация-Large.png#lightbox)

## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
