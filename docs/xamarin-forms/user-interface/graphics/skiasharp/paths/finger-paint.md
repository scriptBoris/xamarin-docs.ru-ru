---
title: Рисование пальцами в SkiaSharp
description: В этой статье объясняется, как использовать пальцы для рисования на холсте SkiaSharp в приложении Xamarin.Forms и демонстрирует это с помощью примера кода.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 56929D74-8F2C-44C6-90E6-3FBABCDC0A4B
author: charlespetzold
ms.author: chape
ms.date: 04/05/2017
ms.openlocfilehash: b0f28cd3e8a928a6da3169dee96ec089178a64e2
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615825"
---
# <a name="finger-painting-in-skiasharp"></a>Рисование пальцами в SkiaSharp

_Используйте пальцы для рисования на холсте._

`SKPath` Постоянно обновляться и отображения объекта. Эта функция позволяет путь используется для интерактивного рисунком, таких как в программе красочное.

![](finger-paint-images/fingerpaintsample.png "Получить с помощью Рисование пальцами")

Поддержка сенсорного ввода в Xamarin.Forms не позволяет отслеживания отдельных пальцев на экране, чтобы эффект Xamarin.Forms touch отслеживания был разработан для обеспечения поддержки дополнительных сенсорного ввода. В этой статье описан этот эффект [ **вызов события из эффекты**](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md). Пример программы [ **демонстрации эффекта Touch отслеживания** ](https://developer.xamarin.com/samples/xamarin-forms/Effects/TouchTrackingEffectDemos/) включает в себя две страницы, использующие SkiaSharp, включая красочное программу.

[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) решение включает в себя это событие отслеживания сенсорного ввода. Включает в себя проект библиотеки .NET Standard `TouchEffect` класс, `TouchActionType` перечисления, `TouchActionEventHandler` делегата и `TouchActionEventArgs` класса. Каждый из проектов платформы включают `TouchEffect` класса для данной платформы; проект iOS также содержит `TouchRecognizer` класса.

**Рисование пальцами** странице в **SkiaSharpFormsDemos** представляет собой упрощенную реализацию Рисование пальцем. Не разрешить выбор цвет и ширина штриха, он никак не может очистить холст и Конечно не удается сохранить иллюстрации.

[ **FingerPaintPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/FingerPaintPage.xaml) файл помещает `SKCanvasView` в одной ячейке `Grid` и присоединяет `TouchEffect` , `Grid`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Paths.FingerPaintPage"
             Title="Finger Paint">

    <Grid BackgroundColor="White">
        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction" />
        </Grid.Effects>
    </Grid>
</ContentPage>
```

Присоединение `TouchEffect` непосредственно к `SKCanvasView` не работает в группе всех платформ.

[ **FingerPaintPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/FingerPaintPage.xaml.cs) файл с выделенным кодом определяет две коллекции для хранения `SKPath` объектов, а также `SKPaint` объектов для подготовки к просмотру эти пути:

```csharp
public partial class FingerPaintPage : ContentPage
{
    Dictionary<long, SKPath> inProgressPaths = new Dictionary<long, SKPath>();
    List<SKPath> completedPaths = new List<SKPath>();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = SKStrokeCap.Round,
        StrokeJoin = SKStrokeJoin.Round
    };

    public FingerPaintPage()
    {
        InitializeComponent();
    }
    ...
}
```

Как по названию `inProgressPaths` словарь хранит путей, которые в настоящее время рисуются с одним или несколькими пальцами. Ключ словаря — идентификатор сенсорного ввода, прилагаемый к события касания. `completedPaths` Поле — это коллекция путей, которые были после завершения рисования контура снято с экрана пальцем.

`TouchAction` Обработчик управляет этих двух коллекций. При первом касании пальцем экрана, новый `SKPath` добавляется `inProgressPaths`. При перемещении пальца, дополнительные точки добавляются к пути. При выпуске пальца передается путь `completedPaths` коллекции. Можно рисовать с помощью нескольких пальцев одновременно. После каждого изменения к одному из путей или коллекций `SKCanvasView` становится недействительным:

```csharp
public partial class FingerPaintPage : ContentPage
{
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (!inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = new SKPath();
                    path.MoveTo(ConvertToPixel(args.Location));
                    inProgressPaths.Add(args.Id, path);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Moved:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = inProgressPaths[args.Id];
                    path.LineTo(ConvertToPixel(args.Location));
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    completedPaths.Add(inProgressPaths[args.Id]);
                    inProgressPaths.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Cancelled:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    inProgressPaths.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;
        }
    }
    ...
    SKPoint ConvertToPixel(Point pt)
    {
        return new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                           (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));
    }
}
```

Точки, сопровождающие события отслеживания сенсорного ввода являются координаты Xamarin.Forms; они должны преобразовываться в SkiaSharp координаты, которые являются пиксели. Это назначение `ConvertToPixel` метод.

`PaintSurface` Обработчик просто выводит обе коллекции путей. Ранее завершенного пути отображаются под путей в ход выполнения:

```csharp
public partial class FingerPaintPage : ContentPage
{
    ,,,
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKCanvas canvas = args.Surface.Canvas;
        canvas.Clear();

        foreach (SKPath path in completedPaths)
        {
            canvas.DrawPath(path, paint);
        }

        foreach (SKPath path in inProgressPaths.Values)
        {
            canvas.DrawPath(path, paint);
        }
    }
    ...
}
```

Ваш картин палец ограничиваются только свои таланты:

[![](finger-paint-images/fingerpaint-small.png "Тройной снимок экрана страницы Рисование пальцами")](finger-paint-images/fingerpaint-large.png#lightbox "тройной снимок экрана страницы Рисование пальцами")


## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [Демонстрации эффекта отслеживания сенсорного ввода (пример)](https://developer.xamarin.com/samples/xamarin-forms/Effects/TouchTrackingEffectDemos/)
- [Вызов событий из эффектов](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
