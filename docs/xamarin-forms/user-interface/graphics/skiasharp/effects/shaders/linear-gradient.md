---
title: Линейный градиент SkiaSharp
description: Узнайте, как для вычерчивания линий или Заливка области с градиентом, состоящие из постепенное сочетание двух цветов.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 20A2A8C4-FEB7-478D-BF57-C92E26117B6A
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: de563c4780a57e439abab61378919501ce88b11b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50131847"
---
# <a name="the-skiasharp-linear-gradient"></a>Линейный градиент SkiaSharp

[ `SKPaint` ](xref:SkiaSharp.SKPaint) Класс определяет [ `Color` ](xref:SkiaSharp.SKPaint.Color) свойство, используемое для вычерчивания линий или заполнения области сплошным цветом. Можно также обводки линии или Заливка области с _градиенты_, которые являются постепенное смешения цветов:

![Пример линейного градиента](linear-gradient-images/LinearGradientSample.png "образца линейного градиента")

Самый простой тип градиента является _линейной_ градиента. Происходит смешения цветов в строке (вызывается _градиентной линией_) из одной точки в другую. Одинаковый цвет у линии, перпендикулярной линии градиента. Создать линейный градиент с помощью одного из двух статических [ `SKShader.CreateLinearGradient` ](xref:SkiaSharp.SKShader.CreateLinearGradient*) методы. Разница между двумя перегрузками является один входит преобразование матрицы и другой — нет. 

Эти методы возвращают объект типа [ `SKShader` ](xref:SkiaSharp.SKShader) , задаваемый для [ `Shader` ](xref:SkiaSharp.SKPaint.Shader) свойство `SKPaint`. Если `Shader` свойства не равно null, оно переопределяет `Color` свойство. Любой строкой, которая рисуется или любой области, который заполняется с помощью этого `SKPaint` основана на градиента, а не сплошным цветом.

> [!NOTE]
> `Shader` Свойство учитывается при включении `SKPaint` объекта в `DrawBitmap` вызова. Можно использовать `Color` свойство `SKPaint` для установки уровня прозрачности для отображения растрового изображения (как описано в статье [растровые изображения отображение SkiaSharp](../../bitmaps/displaying.md#displaying-in-pixel-dimensions)), но нельзя использовать `Shader` свойство для отображения Битовая карта с градиентной прозрачности. Другие функции доступны для отображения растровых изображений с помощью градиента пленки: они описаны в статьях [циклическая градиенты SkiaSharp](circular-gradients.md#radial-gradients-for-masking) и [SkiaSharp композиции и режимы наложения](../blend-modes/porter-duff.md#gradient-transparency-and-transitions).

## <a name="corner-to-corner-gradients"></a>Градиенты углу верхним углом

Часто линейный градиент расширяется от один из углов прямоугольника в другой. Если начальная точка верхнего левого угла прямоугольника, можно расширить градиента:

- по вертикали относительно левого нижнего угла
- по горизонтали в правом верхнем углу
- по диагонали, чтобы нижний правый угол

Диагональный линейный градиент демонстрируется на первой странице в **SkiaSharp построители текстуры и другие эффекты** раздел [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) образца. **Градиента угла к другому** страница создает `SKCanvasView` в своем конструкторе. `PaintSurface` Обработчик создает `SKPaint` объекта в `using` инструкцию и затем определяет 300 пикселей square прямоугольника с центром на холсте:

```csharp
public class CornerToCornerGradientPage : ContentPage
{
    ···
    public CornerToCornerGradientPage ()
    {
        Title = "Corner-to-Corner Gradient";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
        ···
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Create 300-pixel square centered rectangle
            float x = (info.Width - 300) / 2;
            float y = (info.Height - 300) / 2;
            SKRect rect = new SKRect(x, y, x + 300, y + 300);

            // Create linear gradient from upper-left to lower-right
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(rect.Left, rect.Top),
                                new SKPoint(rect.Right, rect.Bottom),
                                new SKColor[] { SKColors.Red, SKColors.Blue },
                                new float[] { 0, 1 },
                                SKShaderTileMode.Repeat);

            // Draw the gradient on the rectangle
            canvas.DrawRect(rect, paint);
            ···
        }
    }
}
```

`Shader` Свойство `SKPaint` назначается `SKShader` возвращаемое значение из статического `SKShader.CreateLinearGradient` метод. Ниже приведены пять аргументов.

- Начальную точку градиента, заданные в верхний левый угол прямоугольника
- Конечную точку градиента, заданные в правый нижний угол прямоугольника
- Массив несколько цветов, способствующих градиента
- Массив `float` значения, указывающие, относительное положение цветов в градиентной линией
- Является членом [ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode) перечисления, указывающее, как ведет себя градиента за пределами конца линии градиента

После создания объект градиентной `DrawRect` метод рисует прямоугольник square 300 пикселей с помощью `SKPaint` объект, который включает в себя шейдера. Здесь он работает в iOS, Android и универсальной платформы Windows (UWP):

[![Градиент угла к другому](linear-gradient-images/CornerToCornerGradient.png "угла к другому градиента")](linear-gradient-images/CornerToCornerGradient-Large.png#lightbox)

Линии градиента определяется двумя точками, заданный как первые два аргумента. Обратите внимание, что эти точки являются относительно _холст_ и _не_ в графический объект отображается с помощью градиента. Вдоль линии градиента цвет постепенно переходит от красного в левом верхнем углу на синий в правом нижнем углу. Любую строку, перпендикулярно линии градиента имеет постоянный цвет.

Массив `float` значения, указанные как четвертый аргумент имеют однозначное соответствие с массив цветов. Значения указывают относительное положение вдоль линии градиента возникновения этих цветов. Здесь, значение 0 означает, что `Red` возникает в начале линии градиента, а 1 означает, что `Blue` происходит в конце строки. Цифры должны быть отсортированы по возрастанию и должно быть в диапазоне от 0 до 1. Если их нет в этом диапазоне, они будут корректироваться должны находиться в диапазоне.

Два значения в массиве можно задать на что-нибудь, отличный от 0 до 1. Попробуйте выполнить следующий код:

```csharp
new float[] { 0.25f, 0.75f }
```

Теперь совершенно первого квартала линии градиента является чисто red, которая последний квартал чисто синий цвет. Сочетание красного и синего предоставляется только в центральной части линии градиента.

Как правило вы захотите пространства эти значения позиции одинаково от 0 до 1. Если это так, можно просто указать `null` как четвертый аргумент `CreateLinearGradient`.

Несмотря на то, что этот градиента определяется между двумя углов прямоугольника square 300 пикселей, он не ограничен заполнения этого прямоугольника. **Градиента угла к другому** страница содержит дополнительный код, который реагирует на касания и щелчки мышью на странице. `drawBackground` Поле переключается между `true` и `false` каждой кнопки. Если значение равно `true`, а затем `PaintSurface` обработчик использует тот же `SKPaint` объект на весь холст, а затем выводит черный прямоугольник, указывающий область меньшего размера: 

```csharp
public class CornerToCornerGradientPage : ContentPage
{
    bool drawBackground;

    public CornerToCornerGradientPage ()
    {
        ···
        TapGestureRecognizer tap = new TapGestureRecognizer();
        tap.Tapped += (sender, args) =>
        {
            drawBackground ^= true;
            canvasView.InvalidateSurface();
        };
        canvasView.GestureRecognizers.Add(tap);
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        using (SKPaint paint = new SKPaint())
        {
            ···
            if (drawBackground)
            {
                // Draw the gradient on the whole canvas
                canvas.DrawRect(info.Rect, paint);

                // Outline the smaller rectangle
                paint.Shader = null;
                paint.Style = SKPaintStyle.Stroke;
                paint.Color = SKColors.Black;
                canvas.DrawRect(rect, paint);
            }
        }
    }
}
```

Вот, что вы увидите после нажатия на экране:

[![Full градиента угла к другому](linear-gradient-images/CornerToCornerGradientFull.png "градиента Full углу верхним углом")](linear-gradient-images/CornerToCornerGradientFull-Large.png#lightbox)

Обратите внимание на то, что градиент повторяется в один и тот же шаблон за пределами точки, определяющие линии градиента. Это повторение происходит потому, что последний аргумент `CreateLinearGradient` является `SKShaderTileMode.Repeat`. (Вскоре вы увидите другие варианты.)

Также Обратите внимание на то, что точки, которые позволяют указать линии градиента не уникальным. Линии, перпендикулярной линии градиента имеют один и тот же цвет, поэтому существует бесконечное число градиента строки, которые можно указать для тот же эффект. Например при заполнении прямоугольника с помощью горизонтальный градиент, можно указать углов верхнего левого и правого верхнего или нижнего левого и правого нижнего углов или двух точках, даже с и параллельно эти строки.

## <a name="interactively-experiment"></a>Интерактивно экспериментов

Вы можете интерактивно поэкспериментировать с линейным градиентом с **интерактивных линейного градиента** страницы. Эта страница использует `InteractivePage` класса представлен в статье [ **три способа нарисовать дугу**](../../curves/arcs.md). `InteractivePage` дескрипторы [ `TouchEffect` ](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md) события для поддержания коллекцию `TouchPoint` объекты, которые можно перемещать с помощью мыши или пальца.

Присоединяет файл XAML `TouchEffect` для у родительского элемента `SKCanvasView` , а также `Picker` , можно выбрать один из трех элементов [ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode) перечисления:

```xaml
<local:InteractivePage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       xmlns:local="clr-namespace:SkiaSharpFormsDemos"
                       xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
                       xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
                       xmlns:tt="clr-namespace:TouchTracking"
                       x:Class="SkiaSharpFormsDemos.Effects.InteractiveLinearGradientPage"
                       Title="Interactive Linear Gradient">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid BackgroundColor="White"
              Grid.Row="0">
            <skiaforms:SKCanvasView x:Name="canvasView"
                                    PaintSurface="OnCanvasViewPaintSurface" />
            <Grid.Effects>
                <tt:TouchEffect Capture="True"
                                TouchAction="OnTouchEffectAction" />
            </Grid.Effects>
        </Grid>

        <Picker x:Name="tileModePicker" 
                Grid.Row="1"
                Title="Shader Tile Mode" 
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKShaderTileMode}">
                    <x:Static Member="skia:SKShaderTileMode.Clamp" />
                    <x:Static Member="skia:SKShaderTileMode.Repeat" />
                    <x:Static Member="skia:SKShaderTileMode.Mirror" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>
    </Grid>
</local:InteractivePage>
```

Конструктор в файле кода создает два `TouchPoint` объекты для начальную и конечную точки линейного градиента. `PaintSurface` Обработчик определяет массив из трех цветов (для градиент от красный зеленый, синий) и получает текущий `SKShaderTileMode` из `Picker`:

```csharp
public partial class InteractiveLinearGradientPage : InteractivePage
{
    public InteractiveLinearGradientPage ()
    {
        InitializeComponent ();

        touchPoints = new TouchPoint[2];

        for (int i = 0; i < 2; i++)
        { 
            touchPoints[i] = new TouchPoint
            {
                Center = new SKPoint(100 + i * 200, 100 + i * 200)
            };
        }

        InitializeComponent();
        baseCanvasView = canvasView;
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKColor[] colors = { SKColors.Red, SKColors.Green, SKColors.Blue };
        SKShaderTileMode tileMode =
            (SKShaderTileMode)(tileModePicker.SelectedIndex == -1 ?
                                        0 : tileModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateLinearGradient(touchPoints[0].Center,
                                                         touchPoints[1].Center,
                                                         colors,
                                                         null,
                                                         tileMode);
            canvas.DrawRect(info.Rect, paint);
        }
        ···
    }
}
```

`PaintSurface` Обработчик создает `SKShader` объекта на основе этих сведений и использует его для цвета весь холст. Массив `float` значениям присваивается `null`. В противном случае для одинаковые расстояния между трех цветов, этому параметру будет присвоено массив, содержащий значения 0, 0.5 и 1.

Основная часть `PaintSurface` обработчика, предназначенного для отображения нескольких объектов: точки касания кругов, линии градиента и строки, перпендикулярно градиента строки в точки касания:

```csharp
public partial class InteractiveLinearGradientPage : InteractivePage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        // Display the touch points here rather than by TouchPoint
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Black;
            paint.StrokeWidth = 3;

            foreach (TouchPoint touchPoint in touchPoints)
            {
                canvas.DrawCircle(touchPoint.Center, touchPoint.Radius, paint);
            }

            // Draw gradient line connecting touchpoints
            canvas.DrawLine(touchPoints[0].Center, touchPoints[1].Center, paint);

            // Draw lines perpendicular to the gradient line
            SKPoint vector = touchPoints[1].Center - touchPoints[0].Center;
            float length = (float)Math.Sqrt(Math.Pow(vector.X, 2) +
                                            Math.Pow(vector.Y, 2));
            vector.X /= length;
            vector.Y /= length;
            SKPoint rotate90 = new SKPoint(-vector.Y, vector.X);
            rotate90.X *= 200;
            rotate90.Y *= 200;

            canvas.DrawLine(touchPoints[0].Center, 
                            touchPoints[0].Center + rotate90, 
                            paint);

            canvas.DrawLine(touchPoints[0].Center,
                            touchPoints[0].Center - rotate90,
                            paint);

            canvas.DrawLine(touchPoints[1].Center,
                            touchPoints[1].Center + rotate90,
                            paint);

            canvas.DrawLine(touchPoints[1].Center,
                            touchPoints[1].Center - rotate90,
                            paint);
        }
    }
}
```

Легко для рисования градиента линию, соединяющую две точки соприкосновения, но перпендикулярной линии требуют дополнительной работы. Линии градиента преобразуется в вектор, нормализованы, чтобы длина за одну единицу и затем повернут на 90 градусов. Этот вектор предоставляется длина составляет 200 пикселей. Он используется для рисования четыре строки, которые исходят из точки касания с градиентной линией.

Перпендикулярной линии совпадают с начало и конец градиента. Что происходит за пределами этих строк зависит от параметра `SKShaderTileMode` перечисления:

[![Интерактивный линейный градиент](linear-gradient-images/InteractiveLinearGradient.png "интерактивных линейного градиента")](linear-gradient-images/InteractiveLinearGradient-Large.png#lightbox)

Три снимка экранов показаны результаты трех различных значений [ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode). На снимке экрана показан iOS `SKShaderTileMode.Clamp`, дополняющем просто цвета на границе градиента. `SKShaderTileMode.Repeat` Параметр Android снимке экрана показано, как градиента шаблон повторяется. `SKShaderTileMode.Mirror` Параметр на снимке экрана универсальной платформы Windows также повторяет шаблон, но шаблон противоположен каждый раз приводит к без прерывания цвет.

## <a name="gradients-on-gradients"></a>Градиенты на градиенты

`SKShader` Класс определяет не открытые свойства или методы, за исключением `Dispose`. `SKShader` Объекты, созданные с его статические методы таким образом являются неизменяемыми. Даже если вы используете этот же градиент для двух разных объектов, вполне вероятно, вы захотите могут незначительно отличаться в градиенте. Чтобы сделать это, необходимо создать новую `SKShader` объекта.

**Текста градиента** страница отображает текст и фоновом, выделяются с помощью похожих градиента:

[![Текста градиента](linear-gradient-images/GradientText.png "текста градиента")](linear-gradient-images/GradientText-Large.png#lightbox)

Единственное различие градиенты, начальную и конечную точки. Градиента, используемого для отображения текста основана на двух точек на углах ограничивающего прямоугольника для текста. Для фона две точки основаны на весь холст. Ниже приведен код:

```csharp
public class GradientTextPage : ContentPage
{
    const string TEXT = "GRADIENT";

    public GradientTextPage ()
    {
        Title = "Gradient Text";

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
            // Create gradient for background
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(0, 0),
                                new SKPoint(info.Width, info.Height),
                                new SKColor[] { new SKColor(0x40, 0x40, 0x40),
                                                new SKColor(0xC0, 0xC0, 0xC0) },
                                null,
                                SKShaderTileMode.Clamp);

            // Draw background
            canvas.DrawRect(info.Rect, paint);

            // Set TextSize to fill 90% of width
            paint.TextSize = 100;
            float width = paint.MeasureText(TEXT);
            float scale = 0.9f * info.Width / width;
            paint.TextSize *= scale;

            // Get text bounds
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            // Calculate offsets to center the text on the screen
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            // Shift textBounds by that amount
            textBounds.Offset(xText, yText);

            // Create gradient for text
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(textBounds.Left, textBounds.Top),
                                new SKPoint(textBounds.Right, textBounds.Bottom),
                                new SKColor[] { new SKColor(0x40, 0x40, 0x40),
                                                new SKColor(0xC0, 0xC0, 0xC0) },
                                null,
                                SKShaderTileMode.Clamp);

            // Draw text
            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

`Shader` Свойство `SKPaint` объекта сначала настроен на отображение градиент для фона. Точки градиента задаются для верхнего левого и правого нижнего углов части холста.

В коде устанавливается `TextSize` свойство `SKPaint` таким образом, чтобы текст отображается на уровне 90% от ширины полотна. Границы текста используются для расчета `xText` и `yText` значения для передачи `DrawText` метод текста по центру.

Тем не менее точки градиента для второй `CreateLinearGradient` вызов должен ссылаться на верхнего левого и правого нижнего угла текста относительно canvas, когда она появится. Это достигается путем перехода `textBounds` прямоугольник тем же `xText` и `yText` значения:

```csharp
textBounds.Offset(xText, yText);
```

Теперь верхнего левого и правого нижнего углов прямоугольника можно задать начальную и конечную точки градиента.

## <a name="animating-a-gradient"></a>Анимация градиент

Для анимации градиент несколькими способами. Один подход заключается в том, чтобы анимировать начальную и конечную точки. **Градиента анимации** страницы перемещается двумя точками в круге, который выравнивается по центру на холсте. Радиус этот круг является половины ширины или высоты полотна, какое значение меньше. Начальные и конечные точки являются противоположными друг с другом на этот круг, и градиента переходит от белого черный с `Mirror` режима плитки:

[![Градиента анимации](linear-gradient-images/GradientAnimation.png "градиента анимации")](linear-gradient-images/GradientAnimation-Large.png#lightbox)

Конструктор создает `SKCanvasView`. `OnAppearing` И `OnDisappearing` методы обрабатывают логику анимации:

```csharp
public class GradientAnimationPage : ContentPage
{
    SKCanvasView canvasView;
    bool isAnimating;
    double angle;
    Stopwatch stopwatch = new Stopwatch();

    public GradientAnimationPage()
    {
        Title = "Gradient Animation";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();

        isAnimating = true;
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();

        stopwatch.Stop();
        isAnimating = false;
    }

    bool OnTimerTick()
    {
        const int duration = 3000;
        angle = 2 * Math.PI * (stopwatch.ElapsedMilliseconds % duration) / duration;
        canvasView.InvalidateSurface();

        return isAnimating;
    }
    ···
}
```

`OnTimerTick` Метод вычисляет `angle` значение, которое анимируется от 0 до 2π каждые 3 секунды. 

Вот один из способов, для которого требуется вычислить две точки градиента. `SKPoint` Значение с именем `vector` вычисляется для расширения в центре холста в точку на радиус круга. Направление данного вектора, зависит от значения синуса и косинуса угла. Затем вычисляется двумя точками противоположной градиента: одна точка вычисляется путем вычитания, вектор из центральной точки, а другие точки получается путем прибавления вектор к центральной точке:

```csharp
public class GradientAnimationPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);
            int radius = Math.Min(info.Width, info.Height) / 2;
            SKPoint vector = new SKPoint((float)(radius * Math.Cos(angle)),
                                         (float)(radius * Math.Sin(angle)));

            paint.Shader = SKShader.CreateLinearGradient(
                                center - vector,
                                center + vector,
                                new SKColor[] { SKColors.White, SKColors.Black },
                                null,
                                SKShaderTileMode.Mirror);

            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Немного другой подход требует меньшего объема кода. Этот подход использует [ `SKShader.CreateLinearGradient` ](xref:SkiaSharp.SKShader.CreateLinearGradient(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) перегрузки метода преобразования матрицы в качестве последнего аргумента. Этот подход является версией в [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) пример:

```csharp
public class GradientAnimationPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(0, 0),
                                info.Width < info.Height ? new SKPoint(info.Width, 0) : 
                                                           new SKPoint(0, info.Height),
                                new SKColor[] { SKColors.White, SKColors.Black },
                                new float[] { 0, 1 },
                                SKShaderTileMode.Mirror,
                                SKMatrix.MakeRotation((float)angle, info.Rect.MidX, info.Rect.MidY));

            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Если ширину холста меньше, чем высота, а затем присваивается две точки градиента (0, 0) и (`info.Width`, 0). Преобразование поворота, переданный в качестве последнего аргумента для `CreateLinearGradient` фактически меняет эти две точки, вокруг центра экрана.

Обратите внимание, что если угол равен 0, отсутствие поворота, и двумя моментами градиента являются верхних левом и правом углов части холста. Эти точки не вычисляется, как показано в предыдущем же градиента точки `CreateLinearGradient` вызова. Но эти точки _параллельных_ к горизонтальной линии градиента, bisects центру холста, и они связаны с идентичными градиента.

**Радуги градиента**

**Радуги градиента** страницы рисует rainbow от левого верхнего угла холста в правом нижнем углу. Но этот градиент радуги не как реальные радуги. Это прямой, а не криволинейные, но она основана на восемь цвета HSL (оттенок насыщенность яркость), которые определяются циклически оттенок значения от 0 до 360:

```csharp
SKColor[] colors = new SKColor[8];

for (int i = 0; i < colors.Length; i++)
{
    colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
}
```

Что код является частью `PaintSurface` обработчик, показано ниже. Обработчик начинается с создания путь, который определяет шести стороны многоугольника, начиная с верхнего левого угла холста в правом нижнем углу:

```csharp
public class RainbowGradientPage : ContentPage
{
    public RainbowGradientPage ()
    {
        Title = "Rainbow Gradient";

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

        using (SKPath path = new SKPath())
        {
            float rainbowWidth = Math.Min(info.Width, info.Height) / 2f;

            // Create path from upper-left to lower-right corner
            path.MoveTo(0, 0);
            path.LineTo(rainbowWidth / 2, 0);
            path.LineTo(info.Width, info.Height - rainbowWidth / 2);
            path.LineTo(info.Width, info.Height);
            path.LineTo(info.Width - rainbowWidth / 2, info.Height);
            path.LineTo(0, rainbowWidth / 2);
            path.Close();

            using (SKPaint paint = new SKPaint())
            {
                SKColor[] colors = new SKColor[8];

                for (int i = 0; i < colors.Length; i++)
                {
                    colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
                }

                paint.Shader = SKShader.CreateLinearGradient(
                                    new SKPoint(0, rainbowWidth / 2), 
                                    new SKPoint(rainbowWidth / 2, 0),
                                    colors,
                                    null,
                                    SKShaderTileMode.Repeat);

                canvas.DrawPath(path, paint);
            }
        }
    }
}
```

Две точки градиента в `CreateLinearGradient` метод основаны на две точки, которые определяют этот путь: обе точки близкие к верхнего левого угла. Во-первых, на верхней границе холста, а второй — в левой части холста. Ниже приведен результат.

[![Градиент радуги неисправного](linear-gradient-images/RainbowGradientFaulty.png "радуги градиента неисправного")](linear-gradient-images/RainbowGradientFaulty-Large.png#lightbox)

Это интересный образ, но это не совсем цель. Проблема в том, что при создании линейный градиент, строки постоянный цвет перпендикулярная линии градиента. Линии градиента зависит от точки, где рисунке касается верхней и левой границ, и эту строку не перпендикулярной к краям фигуры, которые отображают в нижнем правом углу. Этот подход будет работать только в том случае, если холст были square.

Для создания правильного радуги градиента, должен быть перпендикулярной к краю радуги линии градиента. Это более сложного вычисления. Вектор должен быть определен, параллельное длинной стороны фигуры. Вектор — Повернуть на 90 градусов, так как это перпендикулярной к этой стороне. Он затем удлиняется, чтобы быть ширину рисунка путем умножения `rainbowWidth`. Две точки градиента вычисляются на основе точки на стороне рисунке, и, которые указывают плюс вектора. Ниже приведен код, который отображается в **радуги градиента** странице в [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) пример:

```csharp
public class RainbowGradientPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        using (SKPath path = new SKPath())
        {
            ···
            using (SKPaint paint = new SKPaint())
            {
                ···
                // Vector on lower-left edge, from top to bottom 
                SKPoint edgeVector = new SKPoint(info.Width - rainbowWidth / 2, info.Height) - 
                                     new SKPoint(0, rainbowWidth / 2);

                // Rotate 90 degrees counter-clockwise:
                SKPoint gradientVector = new SKPoint(edgeVector.Y, -edgeVector.X);

                // Normalize
                float length = (float)Math.Sqrt(Math.Pow(gradientVector.X, 2) +
                                                Math.Pow(gradientVector.Y, 2));
                gradientVector.X /= length;
                gradientVector.Y /= length;

                // Make it the width of the rainbow
                gradientVector.X *= rainbowWidth;
                gradientVector.Y *= rainbowWidth;

                // Calculate the two points
                SKPoint point1 = new SKPoint(0, rainbowWidth / 2);
                SKPoint point2 = point1 + gradientVector;

                paint.Shader = SKShader.CreateLinearGradient(point1,
                                                             point2,
                                                             colors,
                                                             null,
                                                             SKShaderTileMode.Repeat);

                canvas.DrawPath(path, paint);
            }
        }
    }
}
```

Теперь цвета радуги выравнивается рисунке:

[![Градиент радуги](linear-gradient-images/RainbowGradient.png "радуги градиента")](linear-gradient-images/RainbowGradient-Large.png#lightbox)

**Цвета бесконечности**

Градиент радуги также используется в **цвета бесконечность** страницы. Эта страница Рисует знак бесконечности, используя объект пути, описанные в статье [ **три типа кривых Безье**](../../curves/beziers.md#bezier-curve-approximation-to-circular-arcs). Затем изображение окрашивается градиентом анимированных радуги, которая постоянно выполняет очистку по изображению.

Конструктор создает `SKPath` описывающий знак бесконечности. После создания пути конструктор можно также получить прямоугольник, ограничивающий путь. Затем вычисляется значение с именем `gradientCycleLength`. Если градиент зависит от верхнего левого и правого нижнего углов `pathBounds` прямоугольника, это `gradientCycleLength` значение — общая ширина горизонтального градиента шаблона:

```csharp
public class InfinityColorsPage : ContentPage
{
    ···
    SKCanvasView canvasView;

    // Path information 
    SKPath infinityPath;
    SKRect pathBounds;
    float gradientCycleLength;

    // Gradient information
    SKColor[] colors = new SKColor[8];
    ···

    public InfinityColorsPage ()
    {
        Title = "Infinity Colors";

        // Create path for infinity sign
        infinityPath = new SKPath();
        infinityPath.MoveTo(0, 0);                                  // Center
        infinityPath.CubicTo(  50,  -50,   95, -100,  150, -100);   // To top of right loop
        infinityPath.CubicTo( 205, -100,  250,  -55,  250,    0);   // To far right of right loop
        infinityPath.CubicTo( 250,   55,  205,  100,  150,  100);   // To bottom of right loop
        infinityPath.CubicTo(  95,  100,   50,   50,    0,    0);   // Back to center  
        infinityPath.CubicTo( -50,  -50,  -95, -100, -150, -100);   // To top of left loop
        infinityPath.CubicTo(-205, -100, -250,  -55, -250,    0);   // To far left of left loop
        infinityPath.CubicTo(-250,   55, -205,  100, -150,  100);   // To bottom of left loop
        infinityPath.CubicTo( -95,  100, - 50,   50,    0,    0);   // Back to center
        infinityPath.Close();

        // Calculate path information 
        pathBounds = infinityPath.Bounds;
        gradientCycleLength = pathBounds.Width +
            pathBounds.Height * pathBounds.Height / pathBounds.Width;

        // Create SKColor array for gradient
        for (int i = 0; i < colors.Length; i++)
        {
            colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
        }

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ···
}
```

Этот конструктор также создает `colors` массива для радуги и `SKCanvasView` объекта.

Переопределяет из `OnAppearing` и `OnDisappearing` методы выполняют дополнительные издержки для анимации. `OnTimerTick` Анимирует метод `offset` поле от 0 до `gradientCycleLength` каждые две секунды:

```csharp
public class InfinityColorsPage : ContentPage
{
    ···
    // For animation
    bool isAnimating;
    float offset;
    Stopwatch stopwatch = new Stopwatch();
    ···

    protected override void OnAppearing()
    {
        base.OnAppearing();

        isAnimating = true;
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();

        stopwatch.Stop();
        isAnimating = false;
    }

    bool OnTimerTick()
    {
        const int duration = 2;     // seconds
        double progress = stopwatch.Elapsed.TotalSeconds % duration / duration;
        offset = (float)(gradientCycleLength * progress);
        canvasView.InvalidateSurface();

        return isAnimating;
    }
    ···
}
```

Наконец `PaintSurface` обработчик отображает знак бесконечности. Так как путь содержит положительные и отрицательные координаты вокруг центральной точки (0, 0), `Translate` преобразование на холсте позволяет сместить его в центр. Преобразование переноса сопровождается `Scale` преобразование, применяемое коэффициент масштабирования, делает знак бесконечности как можно большего размера по-прежнему остаются в пределах 95% от ширины и высоты полотна. 

Обратите внимание, что `STROKE_WIDTH` константа добавляется ширину и высоту прямоугольника, ограничивающего пути. Путь будет быть нарисован с использованием строки эта ширина, готовый для просмотра бесконечность размер увеличивается на половинной ширины, что все четыре стороны:

```csharp
public class InfinityColorsPage : ContentPage
{
    const int STROKE_WIDTH = 50;
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Set transforms to shift path to center and scale to canvas size
        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(0.95f * 
            Math.Min(info.Width / (pathBounds.Width + STROKE_WIDTH),
                     info.Height / (pathBounds.Height + STROKE_WIDTH)));

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = STROKE_WIDTH;
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(pathBounds.Left, pathBounds.Top),
                                new SKPoint(pathBounds.Right, pathBounds.Bottom),
                                colors,
                                null,
                                SKShaderTileMode.Repeat,
                                SKMatrix.MakeTranslation(offset, 0));

            canvas.DrawPath(infinityPath, paint);
        }
    }
}
```

Рассмотрим точек, переданный в качестве первых двух аргументов `SKShader.CreateLinearGradient`. Эти точки основаны на исходный путь, ограничивающий прямоугольник. Первый заключается в (&ndash;250, &ndash;100), а второй — (250, 100). Внутри SkiaSharp, эти точки них распространяется текущего преобразования canvas, они были согласованы правильно входа отображается бесконечность.

Без последний аргумент `CreateLinearGradient`, вы увидите радуги градиент, начиная с верхнего левого от знака бесконечности вниз и вправо. (На самом деле, градиента расширяется от верхнего левого угла в правый нижний угол ограничивающего прямоугольника. Знак бесконечности, готовый для просмотра больше, чем ограничивающий прямоугольник в два раза `STROKE_WIDTH` значение для всех сторон. Поскольку градиента отображается красным цветом, в начале и конце, а градиент, созданный с помощью `SKShaderTileMode.Repeat`, не заметное различие.)

С помощью этого последний аргумент `CreateLinearGradient`, шаблон градиента постоянно выполняет очистку по изображению:

[![Цвета бесконечность](linear-gradient-images/InfinityColors.png "бесконечность цвета")](linear-gradient-images/InfinityColors-Large.png#lightbox)

## <a name="transparency-and-gradients"></a>Прозрачности и градиентов

Цвета, способствующих градиента можно включить прозрачности. Вместо градиент, который уменьшает от одного цвета к другому градиента можно скрывать из цвета прозрачным. 

Этот метод можно использовать для некоторых интересных эффектов. Один из примеров классической показано графического объекта с помощью его отражения:

[![Отражение градиентной](linear-gradient-images/ReflectionGradient.png "отражения градиента")](linear-gradient-images/ReflectionGradient-Large.png#lightbox)

Текст, который ногами окрашивается с градиентом, 50%, прозрачного вверху, чтобы полностью прозрачный внизу. Эти уровни прозрачности связаны с альфа-значения 0x80 и 0.

`PaintSurface` Обработчик в **отражения градиента** страницы масштабирует размер текста до 90% от ширины полотна. Затем вычисляется `xText` и `yText` значения для размещения текста по горизонтали по центру, но пребыванию их на план, соответствующий по вертикали по центру страницы:

```csharp
public class ReflectionGradientPage : ContentPage
{
    const string TEXT = "Reflection";

    public ReflectionGradientPage ()
    {
        Title = "Reflection Gradient";

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
            // Set text color to blue
            paint.Color = SKColors.Blue;

            // Set text size to fill 90% of width
            paint.TextSize = 100;
            float width = paint.MeasureText(TEXT);
            float scale = 0.9f * info.Width / width;
            paint.TextSize *= scale;

            // Get text bounds
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            // Calculate offsets to position text above center
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2;

            // Draw unreflected text
            canvas.DrawText(TEXT, xText, yText, paint);

            // Shift textBounds to match displayed text
            textBounds.Offset(xText, yText);

            // Use those offsets to create a gradient for the reflected text
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(0, textBounds.Top),
                                new SKPoint(0, textBounds.Bottom),
                                new SKColor[] { paint.Color.WithAlpha(0),
                                                paint.Color.WithAlpha(0x80) },
                                null,
                                SKShaderTileMode.Clamp);

            // Scale the canvas to flip upside-down around the vertical center
            canvas.Scale(1, -1, 0, yText);

            // Draw reflected text
            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

Те `xText` и `yText` значения являются те же значения, используемый для отображения текста, отраженные в `DrawText` вызов в нижней части `PaintSurface` обработчика. Непосредственно перед этот код, тем не менее, вы увидите вызов `Scale` метод `SKCanvas`. Это `Scale` метод обеспечивает горизонтальное масштабирование на 1 (которая не выполняет никаких действий) но вертикально по &ndash;1, что фактически все ногами отражение. Центр вращения является укажите точки (0, `yText`), где `yText` является по вертикали по центру холста, изначально вычисляется как `info.Height` поделенную на 2.

Имейте в виду, что Skia использует градиента для цветового графических объектов до преобразования canvas. После рисования unreflected текста, `textBounds` прямоугольник сдвигается, поэтому он соответствует отображаемого текста:

```csharp
textBounds.Offset(xText, yText);
```

`CreateLinearGradient` Вызова определяет градиент от верхней части этого прямоугольника до нижней. Градиент представляет собой абсолютно прозрачно синий (`paint.Color.WithAlpha(0)`) для прозрачного синим 50% (`paint.Color.WithAlpha(0x80)`). Преобразование canvas зеркальное отражение текста вверх ногами, прозрачного синюю 50% начинается с базового плана, поэтому становится прозрачной в верхней части текста.

## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
