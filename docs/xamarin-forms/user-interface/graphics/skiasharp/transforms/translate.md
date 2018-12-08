---
title: Преобразование переноса
description: В этой статье рассматривается использование преобразования переноса необходимо сдвинуть графики SkiaSharp в приложениях Xamarin.Forms и демонстрирует это с помощью примера кода.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: BD28ADA1-49F9-44E2-A548-46024A29882F
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: a51a441aeacf265093b82ddb65237887b0a30719
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53053838"
---
# <a name="the-translate-transform"></a>Преобразование переноса

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_Сведения об использовании преобразования переноса необходимо сдвинуть графики SkiaSharp_

Самый простой тип преобразования в SkiaSharp *перевод* или *перевода* преобразования. Это преобразование сдвигает графическими объектами в горизонтальном и вертикальном направлениях. В некотором смысле перевод — наиболее ненужные преобразования, так как обычно это можно выполнить тот же эффект, просто изменив координаты, которые вы используете в функции рисования. При отображении контура, тем не менее, все координаты инкапсулированы в пути, поэтому гораздо проще применения преобразования переноса необходимо сдвинуть весь путь.

Преобразование удобно использовать для анимации, а также для простых текстовых эффектов:

![](translate-images/translateexample.png "Тень текста, утопленный текст; и тиснение с преобразованием")

[ `Translate` ](xref:SkiaSharp.SKCanvas.Translate(System.Single,System.Single)) Метод в `SKCanvas` имеет два параметра, вызывающие объекты впоследствии графических элементов должны сдвигаться по горизонтали и вертикали:

```csharp
public void Translate (Single dx, Single dy)
```

Эти аргументы может быть отрицательным. Секунды [ `Translate` ](xref:SkiaSharp.SKCanvas.Translate(SkiaSharp.SKPoint)) метод объединяет два преобразования значений в одном `SKPoint` значение:

```csharp
public void Translate (SKPoint point)
```

**Накапливаются перевод** странице [ **SkiaSharpForms** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) пример программы показано, что несколько вызовов из `Translate` метода являются накопительными. [ `AccumulatedTranslatePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AccumulatedTranslatePage.cs) Класс отображает 20 версии же прямоугольника, каждого из них смещение от предыдущей прямоугольника ровно настолько, чтобы они растянуть по диагонали. Вот `PaintSurface` обработчик событий:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint strokePaint = new SKPaint())
    {
        strokePaint.Color = SKColors.Black;
        strokePaint.Style = SKPaintStyle.Stroke;
        strokePaint.StrokeWidth = 3;

        int rectangleCount = 20;
        SKRect rect = new SKRect(0, 0, 250, 250);
        float xTranslate = (info.Width - rect.Width) / (rectangleCount - 1);
        float yTranslate = (info.Height - rect.Height) / (rectangleCount - 1);

        for (int i = 0; i < rectangleCount; i++)
        {
            canvas.DrawRect(rect, strokePaint);
            canvas.Translate(xTranslate, yTranslate);
        }
    }
}
```

Последовательные прямоугольников унаследованы от страницы:

[![](translate-images/accumulatedtranslate-small.png "Тройной снимок экрана страницы накапливаются перевод")](translate-images/accumulatedtranslate-large.png#lightbox "тройной снимок экрана страницы накапливаются перевод")

Если накопленные translation factors `dx` и `dy`, и точка, указываемое в функцию рисования (`x`, `y`), то графический объект выводится в точке (`x'`, `y'`), где:

x' = x + dx

y "= y + dy

Они известны как *Преобразование формул* для перевода. Значения по умолчанию `dx` и `dy` новый `SKCanvas` равны 0.

Использование преобразования переноса для эффектов тени и сходные методы, как часто **преобразования текстовых эффектов** страница демонстрирует. Вот соответствующая часть `PaintSurface` обработчик в [ `TranslateTextEffectsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TranslateTextEffectsPage.cs) класса:

```csharp
float textSize = 150;

using (SKPaint textPaint = new SKPaint())
{
    textPaint.Style = SKPaintStyle.Fill;
    textPaint.TextSize = textSize;
    textPaint.FakeBoldText = true;

    float x = 10;
    float y = textSize;

    // Shadow
    canvas.Translate(10, 10);
    textPaint.Color = SKColors.Black;
    canvas.DrawText("SHADOW", x, y, textPaint);
    canvas.Translate(-10, -10);
    textPaint.Color = SKColors.Pink;
    canvas.DrawText("SHADOW", x, y, textPaint);

    y += 2 * textSize;

    // Engrave
    canvas.Translate(-5, -5);
    textPaint.Color = SKColors.Black;
    canvas.DrawText("ENGRAVE", x, y, textPaint);
    canvas.ResetMatrix();
    textPaint.Color = SKColors.White;
    canvas.DrawText("ENGRAVE", x, y, textPaint);

    y += 2 * textSize;

    // Emboss
    canvas.Save();
    canvas.Translate(5, 5);
    textPaint.Color = SKColors.Black;
    canvas.DrawText("EMBOSS", x, y, textPaint);
    canvas.Restore();
    textPaint.Color = SKColors.White;
    canvas.DrawText("EMBOSS", x, y, textPaint);
}
```

В каждом из трех примеров `Translate` вызывается для отображения текста для его смещение от расположение, указанное параметром `x` и `y` переменные. Затем текст снова отображается другим цветом, без влияния перевода:

[![](translate-images/translatetexteffects-small.png "Тройной снимок экрана страницы преобразования текстовых эффектов")](translate-images/translatetexteffects-large.png#lightbox "тройной снимок экрана страницы преобразования текстовых эффектов")

Каждый из трех примерах показан другой способ Инверсия `Translate` вызова:

Первый пример просто вызывает `Translate` еще раз, но с отрицательными значениями. Так как `Translate` вызовы являются накопительными, эта последовательность вызовов просто восстанавливает Общее преобразование значения по умолчанию 0.

Второй пример вызывает [ `ResetMatrix` ](xref:SkiaSharp.SKCanvas.ResetMatrix). В результате всех операций преобразования, чтобы вернуться к состоянию по умолчанию.

Третий пример сохраняет состояние `SKCanvas` объекта с помощью вызова [ `Save` ](xref:SkiaSharp.SKCanvas.Save) и затем восстанавливает среду с помощью вызова [ `Restore` ](xref:SkiaSharp.SKCanvas.Restore). Это наиболее универсальный способ управления преобразований для ряда операций рисования. Эти `Save` и `Restore` вызывает функцию как стек: вы можете вызвать `Save` несколько раз, а затем вызовите `Restore` в обратном последовательности, чтобы вернуться к предыдущим состояниям. `Save` Метод возвращает целое число, и вы можете передать целого числа для [ `RestoreToCount` ](xref:SkiaSharp.SKCanvas.RestoreToCount*) эффективно вызывать `Restore` несколько раз. [ `SaveCount` ](xref:SkiaSharp.SKCanvas.SaveCount) Свойство возвращает число состояний, сохраненных в стеке.

Можно также использовать [ `SKAutoCanvasRestore` ](xref:SkiaSharp.SKAutoCanvasRestore) класс для восстанавливаемого состояния canvas. Конструктор этого класса предназначен для вызова `using` оператор; холста состояние восстанавливается автоматически в конце `using` блока. 

Тем не менее, не нужно беспокоиться о преобразованиях, переносятся из одного вызова из `PaintSurface` обработчик к следующему. Каждый новый вызов `PaintSurface` предоставляет новый `SKCanvas` объекта с преобразованием данных по умолчанию.

Также часто используется для `Translate` преобразования является для отрисовки визуального объекта, изначально создана с помощью координат, которые удобны для рисования. Например может потребоваться указать координаты аналогично часам со стрелками с центром в точке (0, 0). Затем можно использовать преобразования для отображения часы где вам удобно. Этот подход продемонстрирован в [**Hendecagram массива**] страницы. [ `HendecagramArrayPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramPage.cs) Класса начинается с создания `SKPath` объект для star указывает 11. `HendecagramPath` Объект определяется как открытый, статический и только для чтения, таким образом, может осуществляться из других демонстрационных программ. Она создается в статическом конструкторе:

```csharp
public class HendecagramArrayPage : ContentPage
{
    ...
    public static readonly SKPath HendecagramPath;

    static HendecagramArrayPage()
    {
        // Create 11-pointed star
        HendecagramPath = new SKPath();
        for (int i = 0; i < 11; i++)
        {
            double angle = 5 * i * 2 * Math.PI / 11;
            SKPoint pt = new SKPoint(100 * (float)Math.Sin(angle),
                                    -100 * (float)Math.Cos(angle));
            if (i == 0)
            {
                HendecagramPath.MoveTo(pt);
            }
            else
            {
                HendecagramPath.LineTo(pt);
            }
        }
        HendecagramPath.Close();
    }
}
```

Если центре звезды точки (0, 0), все точки, звезды, круг вокруг этой точки. Каждая точка представляет собой сочетание значения синуса и косинуса угла, увеличивающейся на 5/11ths 360 градусов. (Это также можно создать star указывает 11, увеличив угол, на 2/11-й, 3/11ths или 4/11-й окружности.) Радиус окружности, имеет значение 100.

Если этот путь готовится к просмотру без каких-либо преобразований, центр будет располагаться в левом верхнем углу `SKCanvas`и только одной четвертой будут видны. `PaintSurface` Обработчик `HendecagramPage` вместо этого использует `Translate` для мозаичного canvas с нескольких копий звезды, каждый из них случайным образом цвета:

```csharp
public class HendecagramArrayPage : ContentPage
{
    Random random = new Random();
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            for (int x = 100; x < info.Width + 100; x += 200)
                for (int y = 100; y < info.Height + 100; y += 200)
                {
                    // Set random color
                    byte[] bytes = new byte[3];
                    random.NextBytes(bytes);
                    paint.Color = new SKColor(bytes[0], bytes[1], bytes[2]);

                    // Display the hendecagram
                    canvas.Save();
                    canvas.Translate(x, y);
                    canvas.DrawPath(HendecagramPath, paint);
                    canvas.Restore();
                }
        }
    }
}

```

Ниже приведен результат.

[![](translate-images/hendecagramarray-small.png "Тройной снимок экрана страницы массива Hendecagram")](translate-images/hendecagramarray-large.png#lightbox "тройной снимок экрана страницы Hendecagram массива")

Анимации часто включают в себя преобразований. **Hendecagram анимации** страницы перемещается звездочка указывает 11 в кружке. [ `HendecagramAnimationPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramAnimationPage.cs) Класс начинается с некоторые поля и переопределяет из `OnAppearing` и `OnDisappearing` методы для запуска и остановки таймера Xamarin.Forms:

```csharp
public class HendecagramAnimationPage : ContentPage
{
    const double cycleTime = 5000;      // in milliseconds

    SKCanvasView canvasView;
    Stopwatch stopwatch = new Stopwatch();
    bool pageIsActive;
    float angle;

    public HendecagramAnimationPage()
    {
        Title = "Hedecagram Animation";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();
        pageIsActive = true;
        stopwatch.Start();

        Device.StartTimer(TimeSpan.FromMilliseconds(33), () =>
        {
            double t = stopwatch.Elapsed.TotalMilliseconds % cycleTime / cycleTime;
            angle = (float)(360 * t);
            canvasView.InvalidateSurface();

            if (!pageIsActive)
            {
                stopwatch.Stop();
            }

            return pageIsActive;
        });
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
        pageIsActive = false;
    }
    ...
}
```

`angle` Поле анимируется от 0 до 360 градусов каждые 5 секунд. `PaintSurface` Обработчик использует `angle` свойство двумя способами: указать цветового тона цвета в `SKColor.FromHsl` метод и в качестве аргумента для `Math.Sin` и `Math.Cos` методы для управления расположение типа "звезда":

```csharp
public class HendecagramAnimationPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.Translate(info.Width / 2, info.Height / 2);
        float radius = (float)Math.Min(info.Width, info.Height) / 2 - 100;

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Fill;
            paint.Color = SKColor.FromHsl(angle, 100, 50);

            float x = radius * (float)Math.Sin(Math.PI * angle / 180);
            float y = -radius * (float)Math.Cos(Math.PI * angle / 180);
            canvas.Translate(x, y);
            canvas.DrawPath(HendecagramPage.HendecagramPath, paint);
        }
    }
}
```

`PaintSurface` Вызовов обработчика `Translate` метод дважды, сначала следует преобразовать в центре холста, а затем следует преобразовать в окружности круга вокруг (0, 0). Радиус круга задано быть как можно большего размера, сохраняя при этом типа "звезда" в пределах страницы:

[![](translate-images/hendecagramanimation-small.png "Тройной снимок экрана страницы анимации Hendecagram")](translate-images/hendecagramanimation-large.png#lightbox "тройной снимок экрана страницы Hendecagram анимации")

Обратите внимание на то, что типа "звезда" сохраняет ориентацию же, как он вращается вокруг центра страницы. Объект не поворачивается вообще. Это задание для преобразования вращения.


## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
