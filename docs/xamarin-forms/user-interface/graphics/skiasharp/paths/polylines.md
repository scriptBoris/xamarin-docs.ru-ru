---
title: Ломаные линии и параметрические уравнения
description: В этой статье объясняется, как для использования SkiaSharp для отрисовки любой строки можно определить с помощью параметрические уравнения и демонстрирует это с помощью примера кода.
ms.prod: xamarin
ms.assetid: 85AEBB33-E954-4364-A6E1-808FAB197BEE
ms.technology: xamarin-skiasharp
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 9118ca8e23e4c4a9023a1add89e26c4484979c8f
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615799"
---
# <a name="polylines-and-parametric-equations"></a>Ломаные линии и параметрические уравнения

_Использование SkiaSharp для отрисовки любой строки, которые можно определить с помощью параметрические уравнения_

В более поздней версии части этого руководства, вы увидите различные методы, `SKPath` определяет для отображения определенных типов кривых. Тем не менее, иногда бывает для рисования кривой, которая не поддерживается напрямую с типом `SKPath`. В этом случае можно использовать для рисования любой кривой, вы можете определить математически ломаной (коллекцию соединенных линий). Если сделать строки очень маленьким и многочисленные достаточно, результат будет выглядеть кривую. Этот спирали является фактически 3600 мало строк:

![](polylines-images/spiralexample.png "Спирали")

Как правило рекомендуется для определения кривой с точки зрения пару параметрические уравнения. Они уравнения для, координаты X и Y зависят от третьей переменной, иногда называют `t` для времени. Например, следующие параметрические уравнения определить круг с радиусом 1, по центру в точке (0, 0) для *t* от 0 до 1:

 x = cos(2πt) y = sin(2πt)

 Если радиус больше 1, можно просто умножить значения синуса и косинуса, radius и если необходимо перенести в другое расположение, в центре, добавьте эти значения:

 x = xCenter + radius·cos(2πt) y = yCenter + radius·sin(2πt)

Для эллипса с параллельными осей, чтобы по горизонтали и вертикали участвуют два радиусы.

x = xCenter + xRadius·cos(2πt) y = yCenter + yRadius·sin(2πt)

Затем можно поместить эквивалентный код SkiaSharp в цикл, который вычисляет различные области и добавляет их в путь. В следующем коде SkiaSharp создается `SKPath` объект для эллипса, который заполняет поверхность отображения. Цикл выполняет циклический переход по 360 градусов напрямую. Центр половины ширины и высоты поверхность отображения, как и две радиусы:

```csharp
SKPath path = new SKPath();

for (float angle = 0; angle < 360; angle += 1)
{
    double radians = Math.PI * angle / 180;
    float x = info.Width / 2 + (info.Width / 2) * (float)Math.Cos(radians);
    float y = info.Height / 2 + (info.Height / 2) * (float)Math.Sin(radians);

    if (angle == 0)
    {
        path.MoveTo(x, y);
    }
    else
    {
        path.LineTo(x, y);
    }
}
path.Close();
```

Это приводит к эллипс, определяемый 360 мало линиями. При визуализации, он отображается smooth.

Конечно, не нужно создавать эллипс, используя ломаной, так как `SKPath` включает в себя `AddOval` метод, который делает это автоматически. Однако при необходимости для рисования визуальный объект, который не предоставляется `SKPath`.

**Archimedean спирали** страница содержит код, аналогичный код эллипса, но с ключевым отличием. Он проходит по окружности 360 градусов 10 раз, постоянно Настройка radius:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float radius = Math.Min(center.X, center.Y);

    using (SKPath path = new SKPath())
    {
        for (float angle = 0; angle < 3600; angle += 1)
        {
            float scaledRadius = radius * angle / 3600;
            double radians = Math.PI * angle / 180;
            float x = center.X + scaledRadius * (float)Math.Cos(radians);
            float y = center.Y + scaledRadius * (float)Math.Sin(radians);
            SKPoint point = new SKPoint(x, y);

            if (angle == 0)
            {
                path.MoveTo(point);
            }
            else
            {
                path.LineTo(point);
            }
        }

        SKPaint paint = new SKPaint
        {
            Style = SKPaintStyle.Stroke,
            Color = SKColors.Red,
            StrokeWidth = 5
        };

        canvas.DrawPath(path, paint);
    }
}
```

Результат называется также *арифметические спирали* поскольку смещение между каждого цикла является постоянным:

[![](polylines-images/archimedeanspiral-small.png "Тройной снимок экрана страницы спирали Archimedean")](polylines-images/archimedeanspiral-large.png#lightbox "тройной снимок экрана страницы Archimedean спирали")

Обратите внимание, что `SKPath` создается в `using` блока. Это `SKPath` использует больше памяти, чем `SKPath` объекты в предыдущей программы, которые предлагает, `using` блок является более подходящим для удаления неуправляемых ресурсов.


## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
