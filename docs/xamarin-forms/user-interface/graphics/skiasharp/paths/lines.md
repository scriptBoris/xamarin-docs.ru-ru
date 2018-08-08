---
title: Линии и концы штрихов
description: В этой статье описывается использование SkiaSharp для рисования линий с концы штрихов различных приложений Xamarin.Forms и демонстрирует это с помощью примера кода.
ms.prod: xamarin
ms.assetid: 1F854DDD-5D1B-4DE4-BD2D-584439429FDB
ms.technology: xamarin-skiasharp
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 000bf24c1b06baab892f0b165c8b9eeebebce49d
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615266"
---
# <a name="lines-and-stroke-caps"></a>Линии и концы штрихов

_Узнайте, как рисование линий с концы штрихов в другой с помощью SkiaSharp_

В SkiaSharp Подготовка к просмотру на одной строке существенно отличается от отрисовки ряд соединенных прямых линий. Даже при рисовании единичные строки, тем не менее, часто бывает предоставляют строки определенный толщина и тем шире строки, тем большую важность становится внешний вид конечных строк, вызывается *наконечник*:

![](lines-images/strokecapsexample.png "Параметры caps три штриха")

Для рисования отдельных строк `SKCanvas` обеспечивает простую [ `DrawLine` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawLine/p/System.Single/System.Single/System.Single/System.Single/SkiaSharp.SKPaint/) метода, аргументы которых указывать начальные и конечные координаты линии с `SKPaint` объекта:

```csharp
canvas.DrawLine (x0, y0, x1, y1, paint);
```

По умолчанию `StrokeWidth` свойство вновь созданным экземпляром `SKPaint` объекта равно 0, который имеет тот же эффект, что значение 1 при отрисовке строки на один пиксель в толщины. Это выглядит очень тонкий в высоком разрешении устройств, таких как телефоны, поэтому, возможно, вы захотите присвоить `StrokeWidth` для него большее значение. Но как только вы начнете Рисование линий расширяемой толщины, вызывающего событие еще одна проблема: как должны начала и окончания этих толстых строк отображаться?

Внешний вид начала и окончания строк называется *завершение отрезка* или в Skia, *наконечник*. Слово «политики авторизации подключений» в этом контексте ссылается на тип hat &mdash; то, что находится в конце строки. Можно задать [ `StrokeCap` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.StrokeCap/) свойство `SKPaint` одному из следующих членов [ `SKStrokeCap` ](https://developer.xamarin.com/api/type/SkiaSharp.SKStrokeCap/) перечисления:

- [`Butt`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeCap.Butt/) (по умолчанию)
- [`Square`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeCap.Round/)
- [`Round`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeCap.Round/)

Лучше всего они показаны с помощью примера программы. Во второй части домашней страницы [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) программа начинает с страницы под названием **концы штрихов** на основе [ `StrokeCapsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/StrokeCapsPage.cs) класса. Эта страница определяет `PaintSurface` обработчик событий, который проходит по трем членам `SKStrokeCap` перечисления, отображение имени члена перечисления и Рисование линии с помощью этого наконечник штриха:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 75,
        TextAlign = SKTextAlign.Center
    };

    SKPaint thickLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Orange,
        StrokeWidth = 50
    };

    SKPaint thinLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 2
    };

    float xText = info.Width / 2;
    float xLine1 = 100;
    float xLine2 = info.Width - xLine1;
    float y = textPaint.FontSpacing;

    foreach (SKStrokeCap strokeCap in Enum.GetValues(typeof(SKStrokeCap)))
    {
        // Display text
        canvas.DrawText(strokeCap.ToString(), xText, y, textPaint);
        y += textPaint.FontSpacing;

        // Display thick line
        thickLinePaint.StrokeCap = strokeCap;
        canvas.DrawLine(xLine1, y, xLine2, y, thickLinePaint);

        // Display thin line
        canvas.DrawLine(xLine1, y, xLine2, y, thinLinePaint);
        y += 2 * textPaint.FontSpacing;
    }
}
```

Для каждого члена `SKStrokeCap` перечисления, обработчик выводит две строки, одна с толщина штриха 50 пикселей и еще одну строку, расположенный в верхней части с толщина штриха 2 пикселя. Это вторая строка предназначен для демонстрации геометрические начало и конец строки, независимо от толщины линии и наконечник штриха:

[![](lines-images/strokecaps-small.png "Тройной снимок экрана страницы концы штрихов")](lines-images/strokecaps-large.png#lightbox "тройной снимок экрана страницы концы штрихов")

Как вы видите, `Square` и `Round` концы штрихов эффективно расширить длину строки, размером в половину ширины штриха в начале строки и снова в конце. Это расширение важно в тех случаях, когда это необходимо для определения измерений, готовый для просмотра графического объекта.

`SKCanvas` Класс также содержит другой метод для отображения нескольких линий, несколько специфических:

```csharp
DrawPoints (SKPointMode mode, points, paint)
```

`points` Параметр представляет собой массив `SKPoint` значения и `mode` является членом [ `SKPointMode` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPointMode/) перечисления, который имеет три члена:

- [`Points`](https://developer.xamarin.com/api/field/SkiaSharp.SKPointMode.Points/) для отображения отдельных точек
- [`Lines`](https://developer.xamarin.com/api/field/SkiaSharp.SKPointMode.Lines/) для каждой парой точек подключения
- [`Polygon`](https://developer.xamarin.com/api/field/SkiaSharp.SKPointMode.Polygon/) для всех последовательных точек подключения

**Несколько строк** страница демонстрирует этот метод. [ `MultipleLinesPage` Файл XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/MultipleLinesPage.xaml) создаются два `Picker` представления, которые позволяют выбрать членом `SKPointMode` перечисления и членом `SKStrokeCap` перечисления:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.MultipleLinesPage"
             Title="Multiple Lines">
    <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Picker x:Name="pointModePicker"
                Title="Point Mode"
                Grid.Row="0"
                Grid.Column="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.Items>
                <x:String>Points</x:String>
                <x:String>Lines</x:String>
                <x:String>Polygon</x:String>
            </Picker.Items>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Picker x:Name="strokeCapPicker"
                Title="Stroke Cap"
                Grid.Row="0"
                Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.Items>
                <x:String>Butt</x:String>
                <x:String>Round</x:String>
                <x:String>Square</x:String>
            </Picker.Items>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface"
                           Grid.Row="1"
                           Grid.Column="0"
                           Grid.ColumnSpan="2" />
    </Grid>
</ContentPage>
```

`SelectedIndexChanged` Обработчик для обоих `Picker` представления просто делает недействительным `SKCanvasView` объекта:

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs args)
{
    if (canvasView != null)
    {
        canvasView.InvalidateSurface();
    }
}
```

Этот обработчик должен проверять существование `SKCanvasView` объект так, как первый обработчик событий вызывается, когда `SelectedIndex` свойство `Picker` имеет значение 0 в файле XAML, и, наступает раньше `SKCanvasView` был создан экземпляр.

`PaintSurface` Обработчик получает доступ к универсального метода для получения двух выбранных пунктов из `Picker` представления и их преобразования в значения перечисления:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Create an array of points scattered through the page
    SKPoint[] points = new SKPoint[10];

    for (int i = 0; i < 2; i++)
    {
        float x = (0.1f + 0.8f * i) * info.Width;

        for (int j = 0; j < 5; j++)
        {
            float y = (0.1f + 0.2f * j) * info.Height;
            points[2 * j + i] = new SKPoint(x, y);
        }
    }

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.DarkOrchid,
        StrokeWidth = 50,
        StrokeCap = GetPickerItem<SKStrokeCap>(strokeCapPicker)
    };

    // Render the points by calling DrawPoints
    SKPointMode pointMode = GetPickerItem<SKPointMode>(pointModePicker);
    canvas.DrawPoints(pointMode, points, paint);
}

T GetPickerItem<T>(Picker picker)
{
    if (picker.SelectedIndex == -1)
    {
        return default(T);
    }
    return (T)Enum.Parse(typeof(T), picker.Items[picker.SelectedIndex]);
}
```

На снимке экрана показаны различные `Picker` выбранные параметры на всех трех платформах:

[![](lines-images/multiplelines-small.png "Тройной снимок экрана страницы многострочный")](lines-images/multiplelines-large.png#lightbox "тройной снимок экрана страницы несколько строк")

IPhone в левой показана как `SKPointMode.Points` вызывает член перечисления `DrawPoints` для подготовки к просмотру каждой из точек в `SKPoint` массив как квадрат, если завершение отрезка — `Butt` или `Square`. Круги подготавливаются к просмотру, если завершение отрезка `Round`.

При использовании вместо `SKPointMode.Lines`, как показано на экран Android в центре `DrawPoints` метод рисует линию между каждой парой `SKPoint` значения, с помощью указанного отрезка, в данном случае `Round`.

На снимке экрана универсальной платформы Windows показывает результат `SKPointMode.Polygon` значение. Линия проводится между последовательных точек в массиве, но если вы очень внимательно посмотрите, вы увидите, что эти строки не подключены. Каждая из этих отдельных строк начинается и заканчивается указанного отрезка. При выборе `Round` caps, могут отображаться строки должен быть подключен, но они действительно не подключены.

Являются ли строки, подключен или не подключен является важным аспектом работа с контурами графики.


## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
