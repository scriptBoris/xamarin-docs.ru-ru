---
title: Циклическая градиенты SkiaSharp
description: Дополнительные сведения о различных типах градиентов, исходя из кругов.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 400AE23A-6A0B-4FA8-BD6B-DE4146B04732
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: a17ddf438856600870c9bb3da60a5f4667128d57
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53056049"
---
# <a name="the-skiasharp-circular-gradients"></a>Циклическая градиенты SkiaSharp

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

[ `SKShader` ](xref:SkiaSharp.SKShader) Класс определяет статические методы для создания четырех различных типов градиентов. [ **Линейный градиент SkiaSharp** ](linear-gradient.md) статье [ `CreateLinearGradient` ](xref:SkiaSharp.SKShader.CreateLinearGradient*) метод. В этой статье рассматриваются три других типов градиентов, которые основаны на кругов.

[ `CreateRadialGradient` ](xref:SkiaSharp.SKShader.CreateRadialGradient*) Метод создает градиент, исходящее от центра круга:

![Пример радиального градиента](circular-gradients-images/RadialGradientSample.png)

[ `CreateSweepGradient` ](xref:SkiaSharp.SKShader.CreateSweepGradient*) Метод создает градиент, который выполняет очистку вокруг центра круга:

![Очистка образца градиента](circular-gradients-images/SweepGradientSample.png)

Третий тип градиента довольно необычен. Он называется коническую двух точек градиента и определяется [ `CreateTwoPointConicalGradient` ](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient*) метод. Градиент начиная с позиции один круг на другой:

![Пример коническую градиента](circular-gradients-images/ConicalGradientSample.png)

Если два круга различных размеров, градиента принимает форме конуса.

В этой статье рассматриваются эти градиентов, более подробно.

## <a name="the-radial-gradient"></a>Радиальный градиент

[ `CreateRadialGradient` ](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode)) Метод имеет следующий синтаксис:

```csharp
public static SKShader CreateRadialGradient (SKPoint center, 
                                             Single radius, 
                                             SKColor[] colors, 
                                             Single[] colorPos, 
                                             SKShaderTileMode mode)
```

Объект [ `CreateRadialGradient` ](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) перегруженная версия включает также параметр матрицы преобразования.

Первые два аргумента укажите центр круга и radius. Начинается с этого центра и расширяет возможности наружу для `radius` пикселей. Что происходит за пределами `radius` зависит от [ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode) аргумент. `colors` Параметр представляет собой массив из двух или более цветов (так же, как и в линейной градиентной методов), и `colorPos` представляет собой массив целых чисел в диапазоне от 0 до 1. Эти целые числа указывают относительные позиции цветов по, `radius` строки. Можно задать этого аргумента `null` организовать одинаковых цветов.

Если вы используете `CreateRadialGradient` для заполнения круг, можно задать центр градиента центр окружности и радиус градиента радиус круга. В этом случае `SKShaderTileMode` аргумент не влияет на отрисовку градиента. Но если область, заполняемую градиента больше, чем элемент управления circle, определяется градиента, а затем `SKShaderTileMode` аргумент имеет существенное влияние на то, что происходит за пределами круг.

Последствия `SKShaderMode` демонстрируется в **Радиальный градиент** странице в [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) образца. Создает файл XAML для этой страницы `Picker` , можно выбрать один из трех элементов `SKShaderTileMode` перечисления:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.RadialGradientPage"
             Title="Radial Gradient">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <skiaforms:SKCanvasView x:Name="canvasView"
                                Grid.Row="0"
                                PaintSurface="OnCanvasViewPaintSurface" />

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
</ContentPage>
```

Файл с выделенным кодом цвета весь холст с применением радиального градиента. Центр градиента задано относительно центральной части холста и радиус присваивается 100 пикселей. Градиент состоит из двух цветов, черно-белый.

```csharp
public partial class RadialGradientPage : ContentPage
{
    public RadialGradientPage ()
    {
        InitializeComponent ();
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

        SKShaderTileMode tileMode =
            (SKShaderTileMode)(tileModePicker.SelectedIndex == -1 ?
                                        0 : tileModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                new SKPoint(info.Rect.MidX, info.Rect.MidY),
                                100,
                                new SKColor[] { SKColors.Black, SKColors.White },
                                null,
                                tileMode);

            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Этот код создает градиент с черным в центре, постепенно плавный переход белому 100 пикселей в центре. Что происходит за пределами этого radius зависит от `SKShaderTileMode` аргумент:

[![Радиальный градиент](circular-gradients-images/RadialGradient.png "радиального градиента")](circular-gradients-images/RadialGradient-Large.png#lightbox)

Во всех трех случаях градиента заполнит весь холст. На экране iOS слева градиента за пределами радиус продолжается с последний цвет, являющийся белый. Это результат `SKShaderTileMode.Clamp`. Экран Android показан результат `SKShaderTileMode.Repeat`: 100 в пикселях в центре градиента начинается снова в первый цвет, который является черной. Градиент повторяется каждые 100 пикселей radius. 

На правой показан экран универсальной платформы Windows как `SKShaderTileMode.Mirror` вызывает градиенты альтернативный направлениям. Первый градиент — от черного в центре белым радиус 100 пикселей. Следующему — белый из радиус 100 пикселей на черный в radius 200 пикселей, а далее градиента зеркально отображается снова.

Можно использовать более двух цветов в радиальном градиенте. **Радуги Arc градиента** пример создает массив цветов, соответствующий цвета радуги и заканчивая red, а также массив из восьми значений позиции:

```csharp
public class RainbowArcGradientPage : ContentPage
{
    public RainbowArcGradientPage ()
    {
        Title = "Rainbow Arc Gradient";

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
            float rainbowWidth = Math.Min(info.Width, info.Height) / 4f;

            // Center of arc and gradient is lower-right corner
            SKPoint center = new SKPoint(info.Width, info.Height);

            // Find outer, inner, and middle radius
            float outerRadius = Math.Min(info.Width, info.Height);
            float innerRadius = outerRadius - rainbowWidth;
            float radius = outerRadius - rainbowWidth / 2;

            // Calculate the colors and positions
            SKColor[] colors = new SKColor[8];
            float[] positions = new float[8];

            for (int i = 0; i < colors.Length; i++)
            {
                colors[i] = SKColor.FromHsl(i * 360f / 7, 100, 50);
                positions[i] = (i + (7f - i) * innerRadius / outerRadius) / 7f;
            }

            // Create sweep gradient based on center and outer radius
            paint.Shader = SKShader.CreateRadialGradient(center, 
                                                         outerRadius, 
                                                         colors, 
                                                         positions, 
                                                         SKShaderTileMode.Clamp);
            // Draw a circle with a wide line
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = rainbowWidth;

            canvas.DrawCircle(center, radius, paint);
        }
    }
}
```

Предположим, что минимального значения ширины и высоты полотна равно 1000, означающее, что `rainbowWidth` значение — 250. `outerRadius` И `innerRadius` значения устанавливаются в 1000 и 750, соответственно. Эти значения используются для вычисления `positions` массив; диапазон восьми значений от 0.75f до 1. `radius` Значение используется для Обводка круг. Значение 875 средства, предоставляемые между radius 750 пикселей и радиус точек 1000 ширина мазков 250 пикселей:

[![Градиент Arc радуги](circular-gradients-images/RainbowArcGradient.png "радуги Arc градиента")](circular-gradients-images/RainbowArcGradient-Large.png#lightbox)

С этой градиента заполнения весь холст, вы увидите сообщение о том, что это красный в внутренний радиус. Это обусловлено `positions` массива не начинается с 0. Первый цвет используется для смещения от 0 до первого значения массива. Градиент — тоже красный за пределы внешним. Это результат `Clamp` режима плитки. Поскольку градиента используется для Обводка толстой линией, эти red области не отображаются.

## <a name="radial-gradients-for-masking"></a>Радиальные градиенты для маски

Как линейные градиенты радиальным градиентом можно включить цвета прозрачным или полупрозрачным. Эта функция полезна для процесса, называемого _маскирования_, которое скрывает части изображения для акцентирования другой части изображения.

**Радиального градиента маска** страницы показан пример. Программа загружает один ресурс точечных рисунков. `CENTER` И `RADIUS` поля определялись от изучения, растрового изображения и ссылаться на область, в который должны быть выделены. `PaintSurface` Обработчик запускает путем вычисления прямоугольник, чтобы отобразить точечный рисунок и отображает ее в этот прямоугольник:

```csharp
public class RadialGradientMaskPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
        typeof(RadialGradientMaskPage),
        "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

    static readonly SKPoint CENTER = new SKPoint(180, 300);
    static readonly float RADIUS = 120;

    public RadialGradientMaskPage ()
    {
        Title = "Radial Gradient Mask";

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

        // Find rectangle to display bitmap
        float scale = Math.Min((float)info.Width / bitmap.Width,
                                (float)info.Height / bitmap.Height);

        SKRect rect = SKRect.Create(scale * bitmap.Width, scale * bitmap.Height);

        float x = (info.Width - rect.Width) / 2;
        float y = (info.Height - rect.Height) / 2;
        rect.Offset(x, y);

        // Display bitmap in rectangle
        canvas.DrawBitmap(bitmap, rect);

        // Adjust center and radius for scaled and offset bitmap
        SKPoint center = new SKPoint(scale * CENTER.X + x,
                                     scale * CENTER.Y + y);
        float radius = scale * RADIUS;

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                center,
                                radius,
                                new SKColor[] { SKColors.Transparent,
                                                SKColors.White },
                                new float[] { 0.6f, 1 },
                                SKShaderTileMode.Clamp);

            // Display rectangle using that gradient
            canvas.DrawRect(rect, paint);
        }
    }
}
```

После завершения рисования точечного рисунка, преобразует немного простого кода `CENTER` и `RADIUS` для `center` и `radius`, которые относятся к выделенную область в точечном рисунке, масштабируется и сдвиг для отображения. Эти значения используются для создания этого центра и радиус радиального градиента. Два цвета начинаются с прозрачным, в центре, а также для первого 60% от радиуса. Градиент, затем выцветает до белого:

[![Радиальный градиент маски](circular-gradients-images/RadialGradientMask.png "маска радиального градиента")](circular-gradients-images/RadialGradientMask-Large.png#lightbox)

Этот подход не является лучшим способом для маскировки растрового изображения. Проблема в том, что маска главным образом имеет цвет белый цвет, который был выбран в соответствии с фоном части холста. Если некоторые другие цвета фона &mdash; или возможно градиент сам &mdash; она не будет соответствовать. В этой статье показан лучшим подходом к маски [режимы смешения SkiaSharp Портер-Duff](../blend-modes/porter-duff.md).

## <a name="radial-gradients-for-specular-highlights"></a>Радиальные градиенты для зеркального отражения

При света на сервере со скругленными поверхность она отражает свет в разных направлениях, но некоторые света будет передаваться непосредственно в средстве просмотра глаз. Это часто создает внешний вид нечеткого белую область в области с именем _зеркального отражения_.

В трехмерной графики отражающие яркие часто результатом алгоритмы, которые используются для определения света пути и заливка. В двухмерной графики зеркального отражения иногда добавляются предложить внешний вид трехмерной поверхности. Отражающий выделения можно преобразовать плоский красный кружок в round красный шар.

**Радиального отражающий выделите** страница использует Радиальный градиент выполнять указанные выше задачи. `PaintSurface` Существ обработчик, вычисляя radius для круга, а два `SKPoint` значения &mdash; `center` и `offCenter` , находится посередине между центром и левого верхнего края круг:

```csharp
public class RadialSpecularHighlightPage : ContentPage
{
    public RadialSpecularHighlightPage()
    {
        Title = "Radial Specular Highlight";

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

        float radius = 0.4f * Math.Min(info.Width, info.Height);
        SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);
        SKPoint offCenter = center - new SKPoint(radius / 2, radius / 2);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                offCenter,
                                radius / 2,
                                new SKColor[] { SKColors.White, SKColors.Red },
                                null,
                                SKShaderTileMode.Clamp);

            canvas.DrawCircle(center, radius, paint);
        }
    }
}
```

`CreateRadialGradient` Вызов создает градиент, который начинается на, `offCenter` точку с белым цветом и заканчивается red на расстоянии от половины радиуса. Вот как оно выглядит:

[![Радиальный отражающий выделения](circular-gradients-images/RadialSpecularHighlight.png "радиального отражающий выделения")](circular-gradients-images/RadialSpecularHighlight-Large.png#lightbox)

Если вы внимательно посмотрите на этот градиент, можно решить, что – порочен. Градиента центрируется по состоянию на определенный момент, и вы можете его были немного меньше симметричное в соответствии с рабочей области со скругленными. В этом случае может потребоваться, отражающий выделения, показано ниже в разделе [ **Коническую градиент для зеркального отражения**](#conical-gradients-for-specular-highlights).

## <a name="the-sweep-gradient"></a>Очистка градиента

[ `CreateSweepGradient` ](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[])) Метод имеет простой синтаксис все методы создания градиента:

```csharp
public static SKShader CreateSweepGradient (SKPoint center, 
                                            SKColor[] colors, 
                                            Single[] colorPos)
```

Это просто center, массив цветов и позиций цвет. Начинается в правой части центральной точки и выполняет очистку 360 градусов по часовой стрелке вокруг центра. Обратите внимание, что не `SKShaderTileMode` параметра.

Объект [ `CreateSweepGradient` ](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKMatrix)) перегрузка с параметром матрицы преобразования также доступна. Чтобы изменить начальную точку градиента можно применить преобразование поворота. Также можно применить преобразование масштаба, чтобы изменить направление по часовой стрелке на против часовой стрелки.

**Очистка градиента** страница использует градиент поворота на цветной круг с ширина штриха 50 пикселей:

[![Очистка градиента](circular-gradients-images/SweepGradient.png "Очистка градиента")](circular-gradients-images/SweepGradient-Large.png#lightbox)

`SweepGradientPage` Класс определяет массив цветов со значениями разных hue. Обратите внимание на то, что массив начинается и заканчивается red (hue значение 0 или 360), которая отображается в правом на снимках экрана:

```csharp
public class SweepGradientPage : ContentPage
{
    bool drawBackground;

    public SweepGradientPage ()
    {
        Title = "Sweep Gradient";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

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
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Define an array of rainbow colors
            SKColor[] colors = new SKColor[8];

            for (int i = 0; i < colors.Length; i++)
            {
                colors[i] = SKColor.FromHsl(i * 360f / 7, 100, 50);
            }

            SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);

            // Create sweep gradient based on center of canvas
            paint.Shader = SKShader.CreateSweepGradient(center, colors, null);

            // Draw a circle with a wide line
            const int strokeWidth = 50;
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = strokeWidth;

            float radius = (Math.Min(info.Width, info.Height) - strokeWidth) / 2;
            canvas.DrawCircle(center, radius, paint);

            if (drawBackground)
            {
                // Draw the gradient on the whole canvas
                paint.Style = SKPaintStyle.Fill;
                canvas.DrawRect(info.Rect, paint);
            }
        }
    }
}
```

Программа также реализует `TapGestureRecognizer` , позволяющий код в конце `PaintSurface` обработчика. Этот код использует этот же градиент для заполнения полотна.

[![Очистка градиента Full](circular-gradients-images/SweepGradientFull.png "Очистка градиента Full")](circular-gradients-images/SweepGradientFull-Large.png#lightbox)

Эти снимки экрана показано, что градиентных заливок независимо от области окрашивается цветом по его. Если градиента не начинаться и заканчиваться тот же цвет, будет существовать разрыв справа от центральной точки.

## <a name="the-two-point-conical-gradient"></a>Две точки коническую градиента

[ `CreateTwoPointConicalGradient` ](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode)) Метод имеет следующий синтаксис:

```csharp
public static SKShader CreateTwoPointConicalGradient (SKPoint startCenter, 
                                                      Single startRadius, 
                                                      SKPoint endCenter, 
                                                      Single endRadius, 
                                                      SKColor[] colors, 
                                                      Single[] colorPos, 
                                                      SKShaderTileMode mode)
```

Параметры начинаются с точками центра и радиусами для два круга, называются _запустить_ круг и _окончания_ круг. Остальные три параметра одинаковы как для `CreateLinearGradient` и `CreateRadialGradient`. Объект [ `CreateTwoPointConicalGradient` ](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) перегруженная версия включает матрицы преобразования.

Градиента начинается с начала круг и заканчивается в круг end. `SKShaderTileMode` Параметр определяет, что происходит за два круга. Две точки градиента коническую является только градиент, который не полностью заполняет область. Если два круги одного радиуса, градиента ограничен прямоугольник, ширина которого совпадает со значением диаметр кругов. Если два круга различные радиусы, градиента forms конуса.

Вполне вероятно, вы захотите поэкспериментировать с градиентом коническую двух точек, поэтому **Коническую градиента** страница является производной от `InteractivePage` разрешающее две точки касания перемещать для двух круг радиусы:

```xaml
<local:InteractivePage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       xmlns:local="clr-namespace:SkiaSharpFormsDemos"
                       xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
                       xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
                       xmlns:tt="clr-namespace:TouchTracking"
                       x:Class="SkiaSharpFormsDemos.Effects.ConicalGradientPage"
                       Title="Conical Gradient">
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

В файле кода определяется два `TouchPoint` объектов с предопределенной радиусы 50 и 100:

```csharp
public partial class ConicalGradientPage : InteractivePage
{
    const int RADIUS1 = 50;
    const int RADIUS2 = 100;

    public ConicalGradientPage ()
    {
        touchPoints = new TouchPoint[2];

        touchPoints[0] = new TouchPoint
        {
            Center = new SKPoint(100, 100),
            Radius = RADIUS1
        };

        touchPoints[1] = new TouchPoint
        {
            Center = new SKPoint(300, 300),
            Radius = RADIUS2
        };

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
            paint.Shader = SKShader.CreateTwoPointConicalGradient(touchPoints[0].Center,
                                                                  RADIUS1,
                                                                  touchPoints[1].Center,
                                                                  RADIUS2,
                                                                  colors,
                                                                  null,
                                                                  tileMode);
            canvas.DrawRect(info.Rect, paint);
        }

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
        }
    }
}
```

`colors` Массива красного, зеленого и синего. Код в конце `PaintSurface` обработчик рисует два сенсорные точки, как черный накладывающихся кольца, таким образом, чтобы они не препятствовать градиента.

Обратите внимание, что `DrawRect` вызов использует градиента для цветового весь холст. В общем случае Однако большая часть холста остается бесцветных по градиента. Вот программа, показывающая три варианта конфигурации:

[![Коническую градиента](circular-gradients-images/ConicalGradient.png "Коническую градиента")](circular-gradients-images/ConicalGradient-Large.png#lightbox)

На экране iOS слева показан результат `SKShaderTileMode` параметр `Clamp`. Градиент начинается с red внутри края меньшего размера окружности, который противоположной стороны, ближайший к второго круга. `Clamp` Значение также вызывает red продолжать точку конуса. Градиент заканчивается синего цвета на вешней границе круга большего размера, наиболее близкое к первый круг, но по-прежнему синий круг и за его пределами.

Экран Android похоже, но с `SKShaderTileMode` из `Repeat`. Теперь это яснее, что начинается внутри круга, первый и оканчивается за пределами второго круга. `Repeat` Вызывает градиента повторять попытку красным внутри круга большего размера.

На экране универсальной платформы Windows показывает, что происходит при перемещении небольших круга полностью внутри круга большего размера. Градиент перестает быть конус и вместо этого заполняет все пространство. Результат аналогичен радиального градиента, но ассиметричные круг меньшего размера не точно центрируется в круг большего размера.

Практические полезность градиента может сомневаюсь, когда один круг вложен в другой, но он идеально подходит для зеркального отражения выделения.

## <a name="conical-gradients-for-specular-highlights"></a>Коническую градиент для зеркального отражения

Ранее в этой статье вы узнали, как использовать Радиальный градиент для создания зеркального отражения выделения. Две точки коническую градиента можно также использовать для этой цели, и может потребоваться его внешний вид:

[![Коническую отражающий выделения](circular-gradients-images/ConicalSpecularHighlight.png "Коническую отражающий выделения")](circular-gradients-images/ConicalSpecularHighlight-Large.png#lightbox)

Асимметричное внешний лучше предлагает скругленными поверхности объекта. 

Код рисования в **Коническую отражающий выделите** страницы совпадает со значением **радиального отражающий выделите** страницы, за исключением шейдера:

```csharp
public class ConicalSpecularHighlightPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateTwoPointConicalGradient(
                                offCenter,
                                1,
                                center,
                                radius,
                                new SKColor[] { SKColors.White, SKColors.Red },
                                null,
                                SKShaderTileMode.Clamp);

            canvas.DrawCircle(center, radius, paint);
        }
    }
}
```

Два круга имеют центры `offCenter` и `center`. Центр круга в `center` связан с radius, который охватывает весь шар, но элемент управления circle, отцентрованного в `offCenter` имеет радиус одну точку. Градиент фактически начинается в этот момент и заканчивается на границе шара.

## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
