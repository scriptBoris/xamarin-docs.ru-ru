---
title: Фильтры цвет SkiaSharp
description: Используйте фильтры цвет для преобразования цветов с помощью преобразований или таблиц.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 774E7B55-AEC8-4F12-B657-1C0CEE01AD63
author: davidbritch
ms.author: dabritch
ms.date: 08/28/2018
ms.openlocfilehash: 71c0495520a5dd596be2e9cafec6b63e316fb627
ms.sourcegitcommit: c6ff24b524d025d7e87b7b9c25f04c740dd93497
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56240322"
---
# <a name="skiasharp-color-filters"></a>Фильтры цвет SkiaSharp

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

Фильтры цвет может выполнить преобразование цветов в точечный рисунок (или другого изображения) другие цвета для эффектов, например постеризации:

![Пример фильтров цвет](color-filters-images/ColorFiltersExample.png "пример фильтров цвет")

Чтобы использовать фильтр цвета, задайте [ `ColorFilter` ](xref:SkiaSharp.SKPaint.ColorFilter) свойство `SKPaint` для объекта типа [ `SKColorFilter` ](xref:SkiaSharp.SKColorFilter) создаваемыми одним из статических методов в этом классе. В этой статье показано: 

- преобразование цвета созданные с помощью [ `CreateColorMatrix` ](xref:SkiaSharp.SKColorFilter.CreateColorMatrix*) метод.
- Таблица цветов содержит [ `CreateTable` ](xref:SkiaSharp.SKColorFilter.CreateTable*) метод.

## <a name="the-color-transform"></a>Преобразование цвета

Включает в себя преобразование цвета с помощью матрицы для изменения цвета. Как и большинство систем двумерной графики, SkiaSharp используются матрицы, главным образом для преобразования координат точек как iscussed в этой статье [ **матрицы преобразований в SkiaSharp**](../transforms/matrix.md). [ `SKColorFilter` ](xref:SkiaSharp.SKColorFilter) Также поддерживает преобразования матрицы, но преобразования матрицы цветов RGB. Ознакомиться с основными понятиями матрицы необходима для понимания этих преобразований цвет. 

Матрица преобразования цвет имеет измерение из четырех строк и пять столбцов:

<pre>
| M11 M12 M13 M14 M15 |
| M21 M22 M23 M24 M25 |
| M31 M32 M33 M34 M35 |
| M41 M42 M43 M44 M45 |
</pre>

Он преобразует исходный цвет RGB (R, G, B, A) на конечный цвет (R ", G", B ', '). 

В процессе подготовки для умножения матриц исходного цвета преобразуется в матрицу 5 × 1:

<pre>
| R |
| G |
| B |
| A |
| 1 |
</pre>

Эти значения R, G, B и A приведены исходные байты, от 0 до 255. Они являются _не_ нормализованы в значения с плавающей запятой в диапазоне от 0 до 1.

Дополнительная ячейка является обязательным для коэффициент преобразования. Это аналогично использованию матрицу 3 × 3, чтобы преобразование двумерных координат точек, как описано в разделе [ **причину матрица 3 x 3** ](../transforms/matrix.md#the-reason-for-the-3-by-3-matrix) в статье об использовании матриц для преобразования координат точек.

Матрицу 4 × 5 умножается матрица 5 × 1, и этот продукт является матрицу 4 × 1 преобразованные цветом:

<pre>
| M11 M12 M13 M14 M15 |    | R |   | R' |
| M21 M22 M23 M24 M25 |    | G |   | G' |
| M31 M32 M33 M34 M35 |  × | B | = | B' |
| M41 M42 M43 M44 M45 |    | A |   | A' |
                           | 1 |
</pre>

Ниже приведены отдельные формулы для R ", G", B и A':

`R' = M11·R + M12·G + M13·B + M14·A + M15` 

`G' = M21·R + M22·G + M23·B + M24·A + M25` 

`B' = M31·R + M32·G + M33·B + M34·A + M35` 

`A' = M41·R + M42·G + M43·B + M44·A + M45` 

Большая часть матрицы состоит из умножения факторов, которые обычно находятся в диапазоне от 0 до 2. Тем не менее последний столбец (м-15 через M45) содержит значения, которые добавляются в формулах. Эти значения обычно диапазоне от 0 до 255. Результаты являются ограниченными от значения от 0 до 255.

Единичная матрица является:

<pre>
| 1 0 0 0 0 |
| 0 1 0 0 0 |
| 0 0 1 0 0 |
| 0 0 0 1 0 |
</pre>

В результате изменения не цвета. Преобразование формул:

`R' = R` 

`G' = G`

`B' = B`

`A' = A`

Ячейка M44 очень важно, так как она поддерживает прозрачность. Это обычно так, что M41 M42 и M43 равны нулю, и все, так как не хотелось непрозрачность заливки на основе значений красного, зеленого и синего. Но если M44 равно нулю, то объект "должен быть равен нулю, и ничего не отображается.

Один из самых распространенных способов использования цветовой матрице является преобразование цветовой битовой карты в растровое изображение на шкале оттенков серого. Это включает в себя формулу для взвешенное среднее значений красного, зеленого и синего. Для видео отображается с помощью пространстве цветов sRGB («стандартные красный зеленый синий») — следующую формулу:

оттенок серого = 0.2126· R + 0.7152· G + 0.0722· B

Для преобразования цветовой битовой карты в растровое изображение на шкале оттенков серого, R ", G", "и" результаты B необходимо все равно это же значение. Матрица является:

<pre>
| 0.21 0.72 0.07 0 0 |
| 0.21 0.72 0.07 0 0 |
| 0.21 0.72 0.07 0 0 |
| 0    0    0    1 0 |
</pre>

Отсутствует тип данных SkiaSharp, соответствующий данной матрицы. Вместо этого необходимо представить как массив 20 матрицы `float` значения порядку строк: первой строки, а затем второй строки и т. д.

Статический [ `SKColorFilter.CreateColorMatrix` ](xref:SkiaSharp.SKColorFilter.CreateColorMatrix*) метод имеет следующий синтаксис:

```csharp
public static SKColorFilter CreateColorMatrix (float[] matrix);
```

где `matrix` представляет собой массив 20 `float` значения. При создании массива в C#, легко форматирование чисел, поэтому они напоминают матрицу 4 × 5. Это показано в **матрицы черно-белая** странице в [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) пример:

```csharp
public class GrayScaleMatrixPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(CenteredTilesPage),
                        "SkiaSharpFormsDemos.Media.Banana.jpg");

    public GrayScaleMatrixPage()
    {
        Title = "Gray-Scale Matrix";

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

        using (SKPaint paint = new SKPaint())
        {
            paint.ColorFilter =
                SKColorFilter.CreateColorMatrix(new float[]
                {
                    0.21f, 0.72f, 0.07f, 0, 0,
                    0.21f, 0.72f, 0.07f, 0, 0,
                    0.21f, 0.72f, 0.07f, 0, 0,
                    0,     0,     0,     1, 0
                });

            canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform, paint: paint);
        }
    }
}
```

`DrawBitmap` Метод, используемый в этом коде — от **BitmapExtension.cs** состав файл [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) образца. 

Ниже приведен результат, под управлением iOS, Android и универсальной платформы Windows.

[![Черно белая матрицы](color-filters-images/GrayScaleMatrix.png "матрицы на шкале оттенков серого")](color-filters-images/GrayScaleMatrix-Large.png#lightbox)

Обратите особое внимание на значение в четвертой строке и четвертом столбце. Это фактор, умноженное на значение A исходный цвет для типа "значение преобразованные цвета. Если этой ячейки равно нулю, ничего не отображается, и проблема может быть трудно найти.

Экспериментируя с использованием матриц цветов, вы можете рассматривать преобразование с точки зрения источника или назначения точки зрения. Как красный пикселей источника для передачи красного, зеленого и синего точкам назначения? Определяется значениями в первом _столбец_ матрицы. Кроме того как точки назначения red повлияет красного, зеленого и синего пиксели источника? Определенное в первом _строки_ матрицы.

Некоторые мысли о том, как использовать преобразования цветов см. в разделе [ **Перекрашивание изображений** ](https://docs.microsoft.com/dotnet/framework/winforms/advanced/recoloring-images) страниц. Относится к Windows Forms и матрица является другой формат, но принципы остаются неизменными.

**Pastel матрицы** вычисляет точки назначения red, attenuating пикселей источника красный и немного выделения пиксели красного и зеленого. Этот процесс происходит аналогичным образом для зеленого и синего пикселов:

```csharp
public class PastelMatrixPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(PastelMatrixPage),
                        "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

    public PastelMatrixPage()
    {
        Title = "Pastel Matrix";

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

        using (SKPaint paint = new SKPaint())
        {
            paint.ColorFilter =
                SKColorFilter.CreateColorMatrix(new float[]
                {
                    0.75f, 0.25f, 0.25f, 0, 0,
                    0.25f, 0.75f, 0.25f, 0, 0,
                    0.25f, 0.25f, 0.75f, 0, 0,
                    0, 0, 0, 1, 0
                });

            canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform, paint: paint);
        }
    }
}
```

Результатом является для выключения насыщенность цвета, как показано здесь:

[![Пастельные матрицы](color-filters-images/PastelMatrix.png "пастельные матрицы")](color-filters-images/PastelMatrix-Large.png#lightbox)

## <a name="color-tables"></a>Таблицы цветов

Статический [ `SKColorFilter.CreateTable` ](xref:SkiaSharp.SKColorFilter.CreateTable*) метод имеется две версии:

```csharp
public static SKColorFilter CreateTable (byte[] table);

public static SKColorFilter CreateTable (byte[] tableA, byte[] tableR, byte[] tableG, byte[] tableB);
```

Массивы всегда содержать 256 записи. В `CreateTable` метод с одной таблицей, той же таблицы используется для красного, зеленого и синего компонентов. Это таблица простого поиска: Если исходного цвета (R, G, B), а конечный цвет (R ", B", "G"), а затем компонентов назначения получаются путем индексирования `table` с компоненты источника:

`R' = table[R]`

`G' = table[G]`

`B' = table[B]`

Во втором методе каждый из четырех компонентов цвета может содержать таблицу отдельных цветов или же цветовыми таблицами может совместно использоваться между двумя или более компонентов.

Если вы хотите задать один из аргументов второй `CreateTable` метод таблица цветов, который содержит значения от 0 до 255 в последовательности, можно использовать `null` вместо этого. Очень часто `CreateTable` вызов `null` первый аргумент для альфа-канала.

В разделе, посвященном **постеризации** в статье о [биты пикселов точечного рисунка доступ к SkiaSharp](../bitmaps/pixel-bits.md#posterization), вы узнали, как изменить биты растровое изображение, чтобы уменьшить их разрешение цвет одного пикселя. Это называется методом _постеризации_. 

Можно также постеризация Битовая карта с цветовой таблицы. Конструктор **постеризация таблицы** страница создает таблицу цветов, сопоставляет его индекс в байт по нижнему 6 бит, имеет нулевое значение:

```csharp
public class PosterizeTablePage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(PosterizeTablePage),
                        "SkiaSharpFormsDemos.Media.MonkeyFace.png");

    byte[] colorTable = new byte[256];

    public PosterizeTablePage()
    {
        Title = "Posterize Table";

        // Create color table
        for (int i = 0; i < 256; i++)
        {
            colorTable[i] = (byte)(0xC0 & i);
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

        using (SKPaint paint = new SKPaint())
        {
            paint.ColorFilter =
                SKColorFilter.CreateTable(null, null, colorTable, colorTable);

            canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform, paint: paint);
        }
    }
}
```

Программа выбирает для использования этой таблице цвета только для каналов, зеленого и синего. Красный канал по-прежнему имеет полное разрешение:

[![Постеризация таблицы](color-filters-images/PosterizeTable.png "постеризация таблицы")](color-filters-images/PosterizeTable-Large.png#lightbox)

Можно использовать различные таблицы цветов для различные цветовые каналы для различных эффектов. 

## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
