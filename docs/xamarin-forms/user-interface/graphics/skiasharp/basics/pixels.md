---
title: Пиксели и аппаратно-независимые единицы
description: В этой статье рассматриваются различия между SkiaSharp координаты и Xamarin.Forms и демонстрирует это с помощью примера кода.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 26C25BB8-FBE8-4B77-B01D-16A163A16890
author: davidbritch
ms.author: dabritch
ms.date: 02/09/2017
ms.openlocfilehash: d4c890853cb6db334416fc31feb2205a203faa4a
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171928"
---
# <a name="pixels-and-device-independent-units"></a>Пиксели и аппаратно-независимые единицы

_Ознакомьтесь с различиями между SkiaSharp координаты и Xamarin.Forms_

В этой статье рассматриваются различия в системе координат, используемой в SkiaSharp и Xamarin.Forms. Вы можете получить сведения для преобразования между двумя системами координат и также добавлять графические объекты, в который заполнять определенной области:

![](pixels-images/screenfillexample.png "Овал, отображается экран")

Если вы занимаетесь программированием в Xamarin.Forms какое-то время, возможно, внешний для Xamarin.Forms координаты и размеры. Круга, нарисованных в двух предыдущих статьях может показаться слишком маленьким для вас.

Эти круги *являются* небольшой по сравнению с Xamarin.Forms размеров. По умолчанию SkiaSharp рисует в единицах точек, хотя Xamarin.Forms задает координаты и размеры на основе аппаратно независимая единица, установить базовой платформой. (Дополнительные сведения о системе координат Xamarin.Forms можно найти в [Глава 5. Работа с размерами](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter05.md) книги *Создание мобильных приложений с помощью Xamarin.Forms*.)

На странице в [ **SkewSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) программа под названием **размер поверхности** использует SkiaSharp выходной текст для отображения размера поверхность отображения из трех разных источников:

- Обычный Xamarin.Forms [ `Width` ](xref:Xamarin.Forms.VisualElement.Width) и [ `Height` ](xref:Xamarin.Forms.VisualElement.Height) свойства `SKCanvasView` объекта.
- [ `CanvasSize` ](xref:SkiaSharp.Views.Forms.SKCanvasView.CanvasSize) Свойство `SKCanvasView` объекта.
- [ `Size` ](xref:SkiaSharp.SKImageInfo.Size) Свойство `SKImageInfo` значение, которое совместимо с `Width` и `Height` свойств, используемых в двух предыдущих страницах.

[ `SurfaceSizePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SurfaceSizePage.cs) Класс показано, как отображать эти значения. Конструктор сохраняет `SKCanvasView` объект как поле, поэтому он доступен в `PaintSurface` обработчик событий:

```csharp
SKCanvasView canvasView;

public SurfaceSizePage()
{
    Title = "Surface Size";

    canvasView = new SKCanvasView();
    canvasView.PaintSurface += OnCanvasViewPaintSurface;
    Content = canvasView;
}
```

`SKCanvas` включает в себя шесть разных `DrawText` методы, но это [ `DrawText` ](xref:SkiaSharp.SKCanvas.DrawText(System.String,System.Single,System.Single,SkiaSharp.SKPaint)) это самый простой метод:

```csharp
public void DrawText (String text, Single x, Single y, SKPaint paint)
```

Указать текстовую строку, где текст начинается, координаты X и Y и `SKPaint` объекта. Координаты X указывает, где находится слева от текста, но Контрольные значения out: координату по оси y определяет положение *базовых показателей* текста. Если вы когда-нибудь от руки на бумаге линией, базовые показатели — важные строки какие sit символов, и более ранних версий опускаются какие подстрочных элементов (например, на буквы g, p, q и y).

`SKPaint` Объекта можно указать цвет текста, семейства шрифтов и размер текста. По умолчанию [ `TextSize` ](xref:SkiaSharp.SKPaint.TextSize) свойство имеет значение 12, что приводит к небольшой текст на устройствах с высоким разрешением, например телефонов. В замыкания на себя простой приложений, необходимо также некоторые сведения о размере текста, который будет отображаться. `SKPaint` Класс определяет [ `FontMetrics` ](xref:SkiaSharp.SKPaint.FontMetrics) свойства, а также несколько [ `MeasureText` ](xref:SkiaSharp.SKPaint.MeasureText(System.String)) методы, но меньше затейливого потребностей, [ `FontSpacing` ](xref:SkiaSharp.SKPaint.FontSpacing) свойство предоставляет рекомендуемое значение для интервала последовательных строк текста.

Следующие `PaintSurface` обработчик создает `SKPaint` для объекта `TextSize` 40 пикселей, что требуемую высоту вертикального текста в верхней части верхние выносные элементы в нижнюю часть подстрочные элементы. `FontSpacing` Значение, которое `SKPaint` объект возвращает немного больше, чем, о 47 пикселей.

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint paint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 40
    };

    float fontSpacing = paint.FontSpacing;
    float x = 20;               // left margin
    float y = fontSpacing;      // first baseline
    float indent = 100;

    canvas.DrawText("SKCanvasView Height and Width:", x, y, paint);
    y += fontSpacing;
    canvas.DrawText(String.Format("{0:F2} x {1:F2}",
                                  canvasView.Width, canvasView.Height),
                    x + indent, y, paint);
    y += fontSpacing * 2;
    canvas.DrawText("SKCanvasView CanvasSize:", x, y, paint);
    y += fontSpacing;
    canvas.DrawText(canvasView.CanvasSize.ToString(), x + indent, y, paint);
    y += fontSpacing * 2;
    canvas.DrawText("SKImageInfo Size:", x, y, paint);
    y += fontSpacing;
    canvas.DrawText(info.Size.ToString(), x + indent, y, paint);
}
```

Метод начинается первая строка текста с координату по оси X 20 (для небольшой поля слева) и координата Y `fontSpacing`, который является немного более, чем нужно отобразить полную высоту первой строки текста в верхней части области отображения. После каждого вызова `DrawText`, координата Y увеличивается на один или два приращения `fontSpacing`.

Вот ее запуск.

[![](pixels-images/surfacesize-small.png "Тройной снимок экрана страницы размер поверхности")](pixels-images/surfacesize-large.png#lightbox "тройной снимок экрана поверхности размер страницы")

Как вы видите, `CanvasSize` свойство `SKCanvasView` и `Size` свойство `SKImageInfo` значение согласованы в отчетности в пикселах. `Height` И `Width` свойства `SKCanvasView` являются свойства Xamarin.Forms и отчет, размер представления в аппаратно независимых единицах, задаваемых платформой.

Семь имитатор iOS в левой части имеет два пикселей на аппаратно независимые единицы, а Android Nexus 5 в центре трем пикселям на единицу. Вот почему простого круга, показанный ранее имеет разные размеры на разных платформах.

Если вы предпочитаете работать полностью в аппаратно независимых единицах, это можно сделать, задав `IgnorePixelScaling` свойство `SKCanvasView` для `true`. Тем не менее может не устроит результат. SkiaSharp отображает графики на поверхности устройства меньшего размера, с размером пиксель равен размеру представления в аппаратно независимых единицах. (Например, SkiaSharp используется поверхность отображения 360 x 512 пикселей на Nexus 5.) Затем он масштабирует это изображение в размер, что jaggies заметно растрового изображения.

Чтобы обеспечить одинаковое разрешение изображения, лучше создавать собственные простые функции для преобразования между двумя системами координат.

В дополнение к `DrawCircle` метод, `SKCanvas` также определяются два `DrawOval` методы, которые нарисовать эллипс. Эллипс определяется двумя радиусы, а не один radius. Они известны как *основных radius* и *незначительные radius*. `DrawOval` Метод рисует эллипс два радиусы параллельно осей X и Y. (Если вам нужно нарисовать эллипс с осями, не параллельных осей X и Y, можно использовать преобразование поворота, как описано в статье [ **преобразование поворота** ](../transforms/rotate.md) или графический контур, как описано в статья [ **три способа нарисовать дугу**](../curves/arcs.md)). Эта перегрузка [ `DrawOval` ](xref:SkiaSharp.SKCanvas.DrawOval(System.Single,System.Single,System.Single,System.Single,SkiaSharp.SKPaint)) метод имена параметров два радиусы `rx` и `ry` указывает, что параллельно осей X и Y:

```csharp
public void DrawOval (Single cx, Single cy, Single rx, Single ry, SKPaint paint)
```

Можно нарисовать эллипс, который заполняет поверхность отображения? **Заливка эллипса** странице рассказывается, каким образом. `PaintSurface` Обработчик событий в [ **EllipseFillPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/EllipseFillPage.xaml.cs) класс вычитает размером в половину ширины штриха из `xRadius` и `yRadius` значения в соответствии со всей эллипса и его структуры в поверхность отображения:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float strokeWidth = 50;
    float xRadius = (info.Width - strokeWidth) / 2;
    float yRadius = (info.Height - strokeWidth) / 2;

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = strokeWidth
    };
    canvas.DrawOval(info.Width / 2, info.Height / 2, xRadius, yRadius, paint);
}
```

Здесь выполняется:

[![](pixels-images/ellipsefill-small.png "Тройной снимок экрана страницы размер поверхности")](pixels-images/ellipsefill-large.png#lightbox "тройной снимок экрана поверхности размер страницы")

Другой [ `DrawOval` ](xref:SkiaSharp.SKCanvas.DrawOval(SkiaSharp.SKRect,SkiaSharp.SKPaint)) имеет метод [ `SKRect` ](xref:SkiaSharp.SKRect) аргументом, который представляет собой прямоугольник, определенных в терминах координаты X и Y верхнего левого угла и нижний правый угол. Овал заполняет этот прямоугольник, который предполагает, что можно использовать его в **заливка эллипса** страницы следующим образом:

```csharp
SKRect rect = new SKRect(0, 0, info.Width, info.Height);
canvas.DrawOval(rect, paint);
```

Тем не менее, усекает всех краев контура эллипса на четырех сторон. Необходимо настроить все `SKRect` на основе аргументов конструктора `strokeWidth` для выполнения этой работы справа:

```csharp
SKRect rect = new SKRect(strokeWidth / 2,
                         strokeWidth / 2,
                         info.Width - strokeWidth / 2,
                         info.Height - strokeWidth / 2);
canvas.DrawOval(rect, paint);
```


## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
