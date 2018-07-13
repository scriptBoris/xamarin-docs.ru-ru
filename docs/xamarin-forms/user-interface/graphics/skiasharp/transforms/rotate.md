---
title: Преобразование циклического сдвига
description: В этой статье рассматриваются эффекты и анимации, поддерживаемых в средстве преобразования вращения SkiaSharp и демонстрирует это с помощью примера кода.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: CBB3CD72-4377-4EA3-A768-0C4228229FC2
author: charlespetzold
ms.author: chape
ms.date: 03/23/2017
ms.openlocfilehash: cbb34fb4887fc3fa086fa9912d25addebd9b13f2
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995004"
---
# <a name="the-rotate-transform"></a>Преобразование циклического сдвига

_Изучите эффекты и анимации, поддерживаемых в средстве преобразования вращения SkiaSharp_

С помощью преобразование циклического сдвига SkiaSharp графических объектов Избавьтесь ограничения выравнивания с горизонтальной и вертикальной осей:

![](rotate-images/rotateexample.png "Текст, повернутый относительно центра")

Для поворота вокруг точки (0, 0), SkiaSharp поддерживает как графический объект [ `RotateDegrees` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RotateDegrees/p/System.Single/) метод и [ `RotateRadians` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RotateRadians/p/System.Single/) метод:

```csharp
public void RotateDegrees (Single degrees)

public Void RotateRadians (Single radians)
```

Круг 360 градусов совпадает со значением радианах 2π, что позволяет легко выполнить преобразование между двумя значениями. Используйте, какое значение удобным. Все тригонометрические функции в статическом [ `Math` ](xref:System.Math) класс использовать единицы угол в радианах.

Поворот по часовой стрелке, нацеленное на углы. (Несмотря на то, что поворота в декартовой системе координат против часовой стрелки, по соглашению, поворот по часовой стрелке согласуется с координаты Y увеличение переход вниз.) Отрицательное значение углы и углы больше, чем разрешено 360 градусов.

Преобразование формул для поворота более сложны, чем translate и масштабирования. Для углом α формулы преобразования являются следующие:

x' = x•cos(α) — y•sin(α)   

y "= x•sin(α) + y•cos(α)

**Основные повернуть** страница демонстрирует `RotateDegrees` метод. [ `BasicRotate.xaml.cs` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicRotatePage.xaml.cs) Файл выводит текст с базового плана в центре страницы и меняет его на основе `Slider` с диапазоном –360 до 360. Вот соответствующая часть `PaintSurface` обработчика:

```csharp
using (SKPaint textPaint = new SKPaint
{
    Style = SKPaintStyle.Fill,
    Color = SKColors.Blue,
    TextAlign = SKTextAlign.Center,
    TextSize = 100
})
{
    canvas.RotateDegrees((float)rotateSlider.Value);
    canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
}
```

Так как центром поворота является относительно верхнего левого угла холста, для большинства углов, задайте в этой программе, текст поворачивается на экране:

[![](rotate-images/basicrotate-small.png "Тройной снимок экрана страницы основные повернуть")](rotate-images/basicrotate-large.png#lightbox "тройной снимок экрана страницы основные поворот")

Очень часто необходимо повернуть что-то вокруг указанного вращения, с помощью этих версий [ `RotateDegrees` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RotateDegrees/p/System.Single/System.Single/System.Single/) и [ `RotateRadians` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RotateRadians/p/System.Single/System.Single/System.Single/) методы:

```csharp
public void RotateDegrees (Single degrees, Single px, Single py)

public void RotateRadians (Single radians, Single px, Single py)
```

**По центру повернуть** страницы — так же, как **основные повернуть** за исключением того, что расширенная версия `RotateDegrees` используется для задания центр вращения в ту же точку, используемый для размещения текста:

```csharp
using (SKPaint textPaint = new SKPaint
{
    Style = SKPaintStyle.Fill,
    Color = SKColors.Blue,
    TextAlign = SKTextAlign.Center,
    TextSize = 100
})
{
    canvas.RotateDegrees((float)rotateSlider.Value, info.Width / 2, info.Height / 2);
    canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
}
```

Теперь текст поворачивается вокруг точки, используемый для размещения текст, который является центра элемента базового плана текста по горизонтали:

[![](rotate-images/centeredrotate-small.png "Тройной снимок экрана страницы по центру повернуть")](rotate-images/centeredrotate-large.png#lightbox "тройной снимок экрана страницы по центру поворот")

Как и в случае с версией по центру `Scale` метод, выровненные по центру версию `RotateDegrees` вызов представляет собой ярлык:

```csharp
RotateDegrees (degrees, px, py);
```

Оно эквивалентно следующему выражению:

```csharp
canvas.Translate(px, py);
canvas.RotateDegrees(degrees);
canvas.Translate(-px, -py);
```

Вы обнаружите, что иногда можно объединить `Translate` вызывает с `Rotate` вызовов. Например, вот `RotateDegrees` и `DrawText` вызывает в **по центру повернуть** странице;

```csharp
canvas.RotateDegrees((float)rotateSlider.Value, info.Width / 2, info.Height / 2);
canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
```

`RotateDegrees` Вызов эквивалентен два `Translate` вызовы и не ориентированный на `RotateDegrees`:

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.Translate(-info.Width / 2, -info.Height / 2);
canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
```

`DrawText` Вызова для отображения текста в определенном месте эквивалентно `Translate` вызов к этому расположению, за которым следует `DrawText` в точке (0, 0):

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.Translate(-info.Width / 2, -info.Height / 2);
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.DrawText(Title, 0, 0, textPaint);
```

Два последовательных `Translate` вызовы, отменяют друг друга:

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.DrawText(Title, 0, 0, textPaint);
```

По сути два преобразования применяются в том порядке, в отличие от их отображения в коде. `DrawText` Вызов отображает текст в левом верхнем углу холста. `RotateDegrees` Вызов сменяет этот текст, относительно верхнего левого угла. Затем `Translate` вызов перемещает текст по центру холста.

Обычно существует несколько способов объединения вращения и перемещения. **Поворачивать текст** страница создает следующий результат:

[![](rotate-images/rotatedtext-small.png "Тройной снимок экрана страницы поворачивать текст")](rotate-images/rotatedtext-large.png#lightbox "тройной снимок экрана страницы текста (повернутый)")

Вот `PaintSurface` обработчик [ `RotatedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/RotatedTextPage.cs) класса:

```csharp
static readonly string text = "    ROTATE";
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 72
    })
    {
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);
        float yText = yCenter - textBounds.Height / 2 - textBounds.Top;

        for (int degrees = 0; degrees < 360; degrees += 30)
        {
            canvas.Save();
            canvas.RotateDegrees(degrees, xCenter, yCenter);
            canvas.DrawText(text, xCenter, yText, textPaint);
            canvas.Restore();
        }
    }
}

```

`xCenter` И `yCenter` значения указывают центру холста. `yText` Значение — немного смещение от. Это указывает на координату по оси y необходимо поместить текст, чтобы по-настоящему вертикально по центру страницы. `for` Цикл затем задает поворот по центру по центру холста. Поворот осуществляется с шагом 30 градусов. Текст рисуется с помощью `yText` значение. Количество пустых значений перед словом «ПОВОРОТ» в `text` эмпирически для установления соединения между эти 12 текстовые строки могут быть dodecagon было определить значение.

Чтобы упростить этот код является увеличение угол поворота на 30 градусов в каждом проходе через цикл после `DrawText` вызова. Это устраняет потребность в вызовы `Save` и `Restore`. Обратите внимание, что `degrees` переменная больше не используется в теле `for` блок:

```csharp
for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, xCenter, yText, textPaint);
    canvas.RotateDegrees(30, xCenter, yCenter);
}

```

Это также можно использовать простую форму `RotateDegrees` с предшествующим цикла с помощью вызова `Translate` перенести все относительно центральной части холста:

```csharp
float yText = -textBounds.Height / 2 - textBounds.Top;

canvas.Translate(xCenter, yCenter);

for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, 0, yText, textPaint);
    canvas.RotateDegrees(30);
}
```

Измененный `yText` вычисления больше не включает в себя `yCenter`. Теперь `DrawText` вызов Центрирует текст по вертикали в верхней части холста.

Поскольку преобразования применяются концептуально отличие их отображения в коде, чаще всего начинается с большим числом доступных преобразований, следуют дополнительные локальные преобразования. Часто это самый простой способ объединения вращения и перемещения.

Например предположим, что необходимо нарисовать графическим объектом, который вращается вокруг его центра, как глобально, вращается вокруг своей оси. Но вам нужно этот объект для вращения вокруг центральной части экрана как вращение вокруг Солнца планеты.

Это можно сделать, поместив объект в левом верхнем углу холста и затем с помощью анимации для вращения вокруг этого угла. После этого перевод объекта по горизонтали как orbital radius. Теперь можно применять второй анимированного поворота, также вокруг начала координат. Это делает базируются на угол объекта. Теперь перевод относительно центральной части холста.

Вот `PaintSurface` обработчика, который содержит эти преобразования вызовы в обратном порядке:

```csharp
float revolveDegrees, rotateDegrees;
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint fillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Red
    })
    {
        // Translate to center of canvas
        canvas.Translate(info.Width / 2, info.Height / 2);

        // Rotate around center of canvas
        canvas.RotateDegrees(revolveDegrees);

        // Translate horizontally
        float radius = Math.Min(info.Width, info.Height) / 3;
        canvas.Translate(radius, 0);

        // Rotate around center of object
        canvas.RotateDegrees(rotateDegrees);

        // Draw a square
        canvas.DrawRect(new SKRect(-50, -50, 50, 50), fillPaint);
    }
}
```

`revolveDegrees` И `rotateDegrees` поля, применяется анимация. Эта программа использует метод другой анимации на основе Xamarin.Forms `Animation` класса. (Этот класс описан в [в главе 22 книги *Создание мобильных приложений с помощью Xamarin.Forms*](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)) `OnAppearing` переопределение создает два `Animation` объектов с помощью методов обратного вызова, а затем вызывает `Commit` на них для длительности анимации:

```csharp
protected override void OnAppearing()
{
    base.OnAppearing();

    new Animation((value) => revolveDegrees = 360 * (float)value).
        Commit(this, "revolveAnimation", length: 10000, repeat: () => true);

    new Animation((value) =>
    {
        rotateDegrees = 360 * (float)value;
        canvasView.InvalidateSurface();
    }).Commit(this, "rotateAnimation", length: 1000, repeat: () => true);
}
```

Первый `Animation` анимирует объект `revolveDegrees` от 0 до 360 градусов более 10 секунд. Анимирует второй `rotateDegrees` от 0 до 360 градусов каждые 1 во-вторых, а также делает недействительным поверхность, чтобы создать еще один вызов `PaintSurface` обработчика. `OnDisappearing` Переопределение отменяет эти две анимации:

```csharp
protected override void OnDisappearing()
{
    base.OnDisappearing();
    this.AbortAnimation("revolveAnimation");
    this.AbortAnimation("rotateAnimation");
}
```

**Некрасиво аналогично часам со стрелками** программа (так называемых, так как более привлекательным аналогично часам со стрелками будут описаны в последующих статьях) использует поворота для рисования на минуту и час знаки часов и для поворота руки. Программа выводит часы, с помощью произвольных системы координат, исходя из круг, который выравнивается по центру в точке (0, 0) с радиусом 100. Она использует переноса и масштабирования для разверните и отцентрируйте этот круг на странице.

`Translate` И `Scale` вызовы применяются глобально для объекта часов, то есть первый из них следует вызывать после инициализации `SKPaint` объектов:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint strokePaint = new SKPaint())
    using (SKPaint fillPaint = new SKPaint())
    {
        strokePaint.Style = SKPaintStyle.Stroke;
        strokePaint.Color = SKColors.Black;
        strokePaint.StrokeCap = SKStrokeCap.Round;

        fillPaint.Style = SKPaintStyle.Fill;
        fillPaint.Color = SKColors.Gray;

        // Transform for 100-radius circle centered at origin
        canvas.Translate(info.Width / 2f, info.Height / 2f);
        canvas.Scale(Math.Min(info.Width / 200f, info.Height / 200f));
        ...
    }
}

```csharp
There are 60 marks of two different sizes that must be drawn in a circle around the clock. The `DrawCircle` call draws that circle at the point (0, –90), which relative to the center of the clock corresponds to 12:00. The `RotateDegrees` call increments the rotation angle by 6 degrees after every tick mark. The `angle` variable is used solely to determine if a large circle or a small circle is drawn:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
        // Hour and minute marks
        for (int angle = 0; angle < 360; angle += 6)
        {
            canvas.DrawCircle(0, -90, angle % 30 == 0 ? 4 : 2, fillPaint);
            canvas.RotateDegrees(6);
        }
    ...
    }
}
```

Наконец `PaintSurface` обработчик получает значение текущего времени и вычисляет поворот градусов для часа, минуты и второй руки. Каждой стрелки рисуется в положение, 12:00, таким образом, угол поворота относительно:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
        DateTime dateTime = DateTime.Now;

        // Hour hand
        strokePaint.StrokeWidth = 20;
        canvas.Save();
        canvas.RotateDegrees(30 * dateTime.Hour + dateTime.Minute / 2f);
        canvas.DrawLine(0, 0, 0, -50, strokePaint);
        canvas.Restore();

        // Minute hand
        strokePaint.StrokeWidth = 10;
        canvas.Save();
        canvas.RotateDegrees(6 * dateTime.Minute + dateTime.Second / 10f);
        canvas.DrawLine(0, 0, 0, -70, strokePaint);
        canvas.Restore();

        // Second hand
        strokePaint.StrokeWidth = 2;
        canvas.Save();
        canvas.RotateDegrees(6 * dateTime.Second);
        canvas.DrawLine(0, 10, 0, -80, strokePaint);
        canvas.Restore();
    }
}
```

Часы определенно работает, несмотря на то, что довольно руки:

[![](rotate-images/uglyanalogclock-small.png "Тройной снимок экрана страницы неструктурированный текст часов аналог для языка")](rotate-images/uglyanalogclock-large.png#lightbox "Triple screenshot of the Ugly Analog page")


## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
