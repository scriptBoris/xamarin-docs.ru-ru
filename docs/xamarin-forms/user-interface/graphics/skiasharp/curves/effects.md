---
title: Эффекты пути в SkiaSharp
description: Здесь объясняются различные эффекты пути SkiaSharp, разрешающие путей, используемых для Обводка и заполнения и демонстрирует это с помощью примера кода.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 95167D1F-A718-405A-AFCC-90E596D422F3
author: davidbritch
ms.author: dabritch
ms.date: 07/29/2017
ms.openlocfilehash: 8354539288613353dcb7a792ace335daa5dc8f27
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171148"
---
# <a name="path-effects-in-skiasharp"></a>Эффекты пути в SkiaSharp

_Обнаружить различные эффекты пути, которые позволяют путей, используемых для Обводка и заполнения_

Объект *эффект пути* является экземпляром класса [ `SKPathEffect` ](xref:SkiaSharp.SKPathEffect) класс, созданный с помощью одного из восьми статических создания методов, определенных классом. `SKPathEffect` Объект затем устанавливается [ `PathEffect` ](xref:SkiaSharp.SKPaint.PathEffect) свойство [ `SKPaint` ](xref:SkiaSharp.SKPaint) объект ряд интересных эффектов, например, Обводка линий с небольшой реплицированных путь :

![](effects-images/patheffectsample.png "Пример связанной цепочке")

Эффекты пути позволяют:

- Stroke линий с точки и тире
- Stroke строку с любой заполненной путь
- Заливка области с линиями штриховки
- Заливка области с мозаичного путь
- Сделать острые углы округляется
- Добавление случайного «дрожание:» для линий и кривых

Кроме того можно объединить две или более эффекты пути.

В этой статье также показано, как использовать [ `GetFillPath` ](xref:SkiaSharp.SKPaint.GetFillPath*) метод `SKPaint` для преобразования один путь в другой путь путем применения свойства `SKPaint`, в том числе `StrokeWidth` и `PathEffect`. Это приводит ряд интересных приемов, таких как получение путь, который представляет собой структуру другой путь. `GetFillPath` можно использовать в связи с эффекты пути.

## <a name="dots-and-dashes"></a>Точки и тире

Использование [ `PathEffect.CreateDash` ](xref:SkiaSharp.SKPathEffect.CreateDash(System.Single[],System.Single)) метод, описанный в статье [ **точек и тире**](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md). Первый аргумент метода — массив, который содержит четное число два или несколько значений, переключаясь между длин тире и длин промежутки между дефисы:

```csharp
public static SKPathEffect CreateDash (Single[] intervals, Single phase)
```

Эти значения являются *не* относительно ширины штриха. Например, если ширина мазков — 10, и требуется строка состоит из square штрихов и пробелов square, установите `intervals` массив {10, 10}. `phase` Аргумент указывает, где в шаблоне строка начинается. В этом примере, если требуется, чтобы строка начиналась с помощью square gap, задайте `phase` до 10.

Затронутые концов штрихов `StrokeCap` свойство `SKPaint`. Для значений ширины широкой, очень характерно для этого свойства задано `SKStrokeCap.Round` округляемое концов штрихов. В этом случае значения в `intervals` массива *не* включают увеличения длины, полученный в результате округления. Этот факт требует наличия циклических точка указав ширину ноль. Для ширины штриха 10, чтобы создать строку с циклическая точек и пробелов между точками же диаметр, используйте `intervals` массив {0, 20}.

**Анимированный текст точками** страница аналогична **контурного текста** страниц, описанное в статье [ **интеграция текста и графики** ](~/xamarin-forms/user-interface/graphics/skiasharp/basics/text.md) в он отображает описанные текстовых символов, задав `Style` свойство `SKPaint` объект `SKPaintStyle.Stroke`. Кроме того **анимированный текст точками** использует `SKPathEffect.CreateDash` предоставить это структуры более точечно вид, и программа также анимирует `phase` аргумент `SKPathEffect.CreateDash` метод для установки точек показаться передаваться вокруг текста символы. Ниже приведен страницу в альбомной ориентации.

[![](effects-images/animateddottedtext-small.png "Тройной снимок экрана страницы анимированный текст точками")](effects-images/animateddottedtext-large.png#lightbox "тройной снимок экрана страницы анимированный текст точками")

[ `AnimatedDottedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs) Класс начинается определение нескольких констант, а также переопределяет `OnAppearing` и `OnDisappearing` методы для анимации:

```csharp
public class AnimatedDottedTextPage : ContentPage
{
    const string text = "DOTTED";
    const float strokeWidth = 10;
    static readonly float[] dashArray = { 0, 2 * strokeWidth };

    SKCanvasView canvasView;
    bool pageIsActive;

    public AnimatedDottedTextPage()
    {
        Title = "Animated Dotted Text";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();
        pageIsActive = true;

        Device.StartTimer(TimeSpan.FromSeconds(1f / 60), () =>
        {
            canvasView.InvalidateSurface();
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

`PaintSurface` Обработчик начинается с создания `SKPaint` объекта для отображения текста. `TextSize` Свойство настраивается в зависимости от ширины экрана:

```csharp
public class AnimatedDottedTextPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Create an SKPaint object to display the text
        using (SKPaint textPaint = new SKPaint
            {
                Style = SKPaintStyle.Stroke,
                StrokeWidth = strokeWidth,
                StrokeCap = SKStrokeCap.Round,
                Color = SKColors.Blue,
            })
        {
            // Adjust TextSize property so text is 95% of screen width
            float textWidth = textPaint.MeasureText(text);
            textPaint.TextSize *= 0.95f * info.Width / textWidth;

            // Find the text bounds
            SKRect textBounds = new SKRect();
            textPaint.MeasureText(text, ref textBounds);

            // Calculate offsets to center the text on the screen
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            // Animate the phase; t is 0 to 1 every second
            TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
            float t = (float)(timeSpan.TotalSeconds % 1 / 1);
            float phase = -t * 2 * strokeWidth;

            // Create dotted line effect based on dash array and phase
            using (SKPathEffect dashEffect = SKPathEffect.CreateDash(dashArray, phase))
            {
                // Set it to the paint object
                textPaint.PathEffect = dashEffect;

                // And draw the text
                canvas.DrawText(text, xText, yText, textPaint);
            }
        }
    }
}
```

В конце метода `SKPathEffect.CreateDash` метод вызывается с помощью `dashArray` , определенная как поля, а анимированное `phase` значение. `SKPathEffect` Экземпляра назначается `PathEffect` свойство `SKPaint` объекта для отображения текста.

Кроме того, можно задать `SKPathEffect` объект `SKPaint` объекта до измерение текст и центрирование его на странице. В этом случае тем не менее, анимированный точки и тире вызвать небольшие различия в размер отображаемого текста и текст обычно немного «вибрация». (Попробуйте!)

Вы будете также Обратите внимание, что круга анимированных точек вокруг символов текста, определенного места в каждой секции замкнутой кривой где кажется pop и из него существование точек. Это, где начинается и заканчивается в путь, определяющий контур символа. Если длина пути не кратен длину шаблоне (в данном случае 20 пикселей), только часть этого шаблона можно разместить в конце пути.

Можно изменить длину штриха в соответствии с длиной пути. Однако, для которого требуется определить длину пути, это метод, который рассматривается в статье [ **сведения о пути и перечисление** ](information.md).

**Точка или тире Morph** программы анимирует шаблоне сам таким образом, тире, по-видимому, разделить точек, которые комбинировать в форме тире, еще раз:

[![](effects-images/dotdashmorph-small.png "Тройной снимок экрана страницы точка Dash Morph")](effects-images/dotdashmorph-large.png#lightbox "тройной снимок экрана страницы Morph точка тире")

[ `DotDashMorphPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs) Класса переопределения `OnAppearing` и `OnDisappearing` так же, как на аналогичную предыдущей программы, но этот класс определяет методы `SKPaint` объект как поле:

```csharp
public class DotDashMorphPage : ContentPage
{
    const float strokeWidth = 30;
    static readonly float[] dashArray = new float[4];

    SKCanvasView canvasView;
    bool pageIsActive = false;

    SKPaint ellipsePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = strokeWidth,
        StrokeCap = SKStrokeCap.Round,
        Color = SKColors.Blue
    };
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Create elliptical path
        using (SKPath ellipsePath = new SKPath())
        {
            ellipsePath.AddOval(new SKRect(50, 50, info.Width - 50, info.Height - 50));

            // Create animated path effect
            TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
            float t = (float)(timeSpan.TotalSeconds % 3 / 3);
            float phase = 0;

            if (t < 0.25f)  // 1, 0, 1, 2 --> 0, 2, 0, 2
            {
                float tsub = 4 * t;
                dashArray[0] = strokeWidth * (1 - tsub);
                dashArray[1] = strokeWidth * 2 * tsub;
                dashArray[2] = strokeWidth * (1 - tsub);
                dashArray[3] = strokeWidth * 2;
            }
            else if (t < 0.5f)  // 0, 2, 0, 2 --> 1, 2, 1, 0
            {
                float tsub = 4 * (t - 0.25f);
                dashArray[0] = strokeWidth * tsub;
                dashArray[1] = strokeWidth * 2;
                dashArray[2] = strokeWidth * tsub;
                dashArray[3] = strokeWidth * 2 * (1 - tsub);
                phase = strokeWidth * tsub;
            }
            else if (t < 0.75f) // 1, 2, 1, 0 --> 0, 2, 0, 2
            {
                float tsub = 4 * (t - 0.5f);
                dashArray[0] = strokeWidth * (1 - tsub);
                dashArray[1] = strokeWidth * 2;
                dashArray[2] = strokeWidth * (1 - tsub);
                dashArray[3] = strokeWidth * 2 * tsub;
                phase = strokeWidth * (1 - tsub);
            }
            else               // 0, 2, 0, 2 --> 1, 0, 1, 2
            {
                float tsub = 4 * (t - 0.75f);
                dashArray[0] = strokeWidth * tsub;
                dashArray[1] = strokeWidth * 2 * (1 - tsub);
                dashArray[2] = strokeWidth * tsub;
                dashArray[3] = strokeWidth * 2;
            }

            using (SKPathEffect pathEffect = SKPathEffect.CreateDash(dashArray, phase))
            {
                ellipsePaint.PathEffect = pathEffect;
                canvas.DrawPath(ellipsePath, ellipsePaint);
            }
        }
    }
}
```

`PaintSurface` Обработчик создает эллиптического пути на основе размера страницы и выполняет много раздел кода, который задает `dashArray` и `phase` переменные. Анимированный переменной `t` в диапазоне от 0 до 1, `if` блоки разбить это время в четыре квартала, а в каждом из этих кварталов `tsub` также в диапазоне от 0 до 1. В самом конце, программа создает `SKPathEffect` и заменяет его на `SKPaint` для рисования объект.

## <a name="from-path-to-path"></a>Из пути к пути

[ `GetFillPath` ](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,System.Single)) Метод `SKPaint` превращается в другой в зависимости от параметров в один путь `SKPaint` объекта. Чтобы увидеть работу образца, замените `canvas.DrawPath` вызова в предыдущей программе следующим кодом:

```csharp
SKPath newPath = new SKPath();
bool fill = ellipsePaint.GetFillPath(ellipsePath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);

```

В этом новом коде `GetFillPath` вызовите преобразует `ellipsePath` (это просто Овал) в `newPath`, который затем отображается с `newPaint`. `newPaint` Со всеми параметрами по умолчанию свойство создается объект за исключением того, что `Style` свойство имеет значение на основе логического значения возвращения значения из `GetFillPath`.

Визуальные элементы идентичны, за исключением цвет, который задается в `ellipsePaint` , но не `newPaint`. Вместо того чтобы простой эллипс, определенные в `ellipsePath`, `newPath` содержит многочисленные контуров пути, которые определяют последовательность точек и тире. Это результат применения различных свойств `ellipsePaint` (в частности, `StrokeWidth`, `StrokeCap`, и `PathEffect`) для `ellipsePath` и разместив результирующий путь в `newPath`. `GetFillPath` Метод возвращает логическое значение, указывающее, является ли целевой путь должен быть заполнен; в этом примере возвращается `true` для заполнения пути.

Попробуйте изменить `Style` в `newPaint` для `SKPaintStyle.Stroke` и вы увидите конкретного пути контуров линией один ширина в пикселях.

## <a name="stroking-with-a-path"></a>Обводка с путем

[ `SKPathEffect.Create1DPath` ](xref:SkiaSharp.SKPathEffect.Create1DPath(SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPath1DPathEffectStyle)) Метод аналогичен `SKPathEffect.CreateDash` за исключением того, чтобы указать путь, а не шаблон штрихов и пробелов. Этот путь реплицируется несколько раз для обводки линии или кривой.

Синтаксис выглядит следующим образом.

```csharp
public static SKPathEffect Create1DPath (SKPath path, Single advance,
                                         Single phase, SKPath1DPathEffectStyle style)
```

В целом, путь, который можно передать `Create1DPath` будет небольшой и выровнять по центру относительно точки (0, 0). `advance` Параметр указывает расстояние между центрами обработки пути, как путь реплицируется в строке. Обычно этот аргумент присвоено приблизительную ширину путь. `phase` Аргумент играет ту же роль здесь он делает в `CreateDash` метод.

[ `SKPath1DPathEffectStyle` ](xref:SkiaSharp.SKPath1DPathEffectStyle) Имеется три члена:

- `Translate`
- `Rotate`
- `Morph`

`Translate` Член приводит к путь должен оставаться в ту же ориентацию, как они реплицируются вдоль линий или кривых. Для `Rotate`, путь поворачивается в зависимости от касательной к кривой. Путь имеет ориентация normal для горизонтальных линий. `Morph` аналогичен `Rotate` за исключением того, что сам путь изогнут также в соответствии с кривизну линии обводкой.

**Эффект пути 1 D** страница демонстрирует этих трех вариантов. [ **OneDimensionalPathEffectPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml) файл определяет управляющий элемент выбора, содержащий три элементы, соответствующие трем членам перечисления:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Curves.OneDimensionalPathEffectPage"
             Title="1D Path Effect">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Picker x:Name="effectStylePicker"
                Title="Effect Style"
                Grid.Row="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type x:String}">
                    <x:String>Translate</x:String>
                    <x:String>Rotate</x:String>
                    <x:String>Morph</x:String>
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface"
                           Grid.Row="1" />
    </Grid>
</ContentPage>
```

[ **OneDimensionalPathEffectPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml.cs) определяет файл с выделенным кодом трех `SKPathEffect` объектов в виде поля. Они будут созданы с помощью `SKPathEffect.Create1DPath` с `SKPath` объекты, созданные с помощью `SKPath.ParseSvgPathData`. Первый представляет собой простое поле, второй — в форме ромбовидной фигуры и третий представляет собой прямоугольник. Они используются для демонстрации стили три эффект:

```csharp
public partial class OneDimensionalPathEffectPage : ContentPage
{
    SKPathEffect translatePathEffect =
        SKPathEffect.Create1DPath(SKPath.ParseSvgPathData("M -10 -10 L 10 -10, 10 10, -10 10 Z"),
                                  24, 0, SKPath1DPathEffectStyle.Translate);

    SKPathEffect rotatePathEffect =
        SKPathEffect.Create1DPath(SKPath.ParseSvgPathData("M -10 0 L 0 -10, 10 0, 0 10 Z"),
                                  20, 0, SKPath1DPathEffectStyle.Rotate);

    SKPathEffect morphPathEffect =
        SKPathEffect.Create1DPath(SKPath.ParseSvgPathData("M -25 -10 L 25 -10, 25 10, -25 10 Z"),
                                  55, 0, SKPath1DPathEffectStyle.Morph);

    SKPaint pathPaint = new SKPaint
    {
        Color = SKColors.Blue
    };

    public OneDimensionalPathEffectPage()
    {
        InitializeComponent();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        if (canvasView != null)
        {
            canvasView.InvalidateSurface();
        }
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath path = new SKPath())
        {
            path.MoveTo(new SKPoint(0, 0));
            path.CubicTo(new SKPoint(2 * info.Width, info.Height),
                         new SKPoint(-info.Width, info.Height),
                         new SKPoint(info.Width, 0));

            switch ((string)effectStylePicker.SelectedItem))
            {
                case "Translate":
                    pathPaint.PathEffect = translatePathEffect;
                    break;

                case "Rotate":
                    pathPaint.PathEffect = rotatePathEffect;
                    break;

                case "Morph":
                    pathPaint.PathEffect = morphPathEffect;
                    break;
            }

            canvas.DrawPath(path, pathPaint);
        }
    }
}
```

`PaintSurface` Обработчик создает кривую Безье, циклы вокруг сам, который обращается к палитре, чтобы определить, какие `PathEffect` следует использовать для вычерчивания его. Три варианта — `Translate`, `Rotate`, и `Morph` — отображаются слева направо:

[![](effects-images/1dpatheffect-small.png "Тройной снимок экрана страницы эффект пути 1D")](effects-images/1dpatheffect-large.png#lightbox "тройной снимок экрана страницы эффект пути 1 D")

Путь, указанный в `SKPathEffect.Create1DPath` метод всегда будет заполнено. Путь, указанный в `DrawPath` метод всегда рисуется, если `SKPaint` объект имеет его `PathEffect` , имеющим значение 1 D эффект. Обратите внимание, что `pathPaint` объект не имеет `Style` параметр, который по умолчанию обычно используется `Fill`, но путь вычерчивается независимо.

Окно, используемое в `Translate` пример — 20 пикселей квадрат и `advance` аргумента равно 24. Это различие приводит разрыв между поля при горизонтальной или вертикальной строке составляет приблизительно, но поля немного перекрываются, если строка соответствует диагональный, так как по диагонали поля — 28.3 пикселей.

Ромб в `Rotate` пример также составляет 20 пикселов. `advance` Равна 20, чтобы точки касания как ромб поворачивается вместе с кривизну строки.

Прямоугольник в `Morph` пример — 50 пикселей в ширину с `advance` параметр 55 чтобы небольшой разрыв между прямоугольниками, как они погнуты вокруг кривой Безье.

Если `advance` аргумент меньше, чем размер пути, а затем реплицированных пути могут перекрывать друг друга. Это может привести некоторые интересные эффекты. **Связанной цепочке** страница отображает ряд перекрывающихся окружности, кажется, чтобы он напоминал связанной цепи, который перестает отвечать на запросы в отдельные роли catenary:

[![](effects-images/linkedchain-small.png "Тройной снимок экрана страницы связанной цепочке")](effects-images/linkedchain-large.png#lightbox "тройной снимок экрана страницы связанной цепочке")

Найдите очень близки, и вы увидите, что это не фактически кругов. Каждая ссылка в цепочке — два дуг, изменять размеры и расположение, кажется, свяжитесь с соседними ссылки.

Цепочки или кабель универсальный вес распределения перестает отвечать на запросы в виде catenary. Arch, встроенные в виде инвертированный catenary выигрывает от равного распределения нагрузки на вес arch. Catenary имеет внешне простой математической Описание:

y = [Default] Cosh(x / a)

*Cosh* — это функция гиперболический косинус. Для *x* равно 0, *cosh* равен нулю и *y* равняется *a*. Это Центр catenary. Как *косинус* функции *cosh* считается *даже*, означающее, что *cosh(–x)* равно *cosh(x)*, и значения увеличивают нацеленное на повышение аргументы положительным или отрицательным. Эти значения описывают кривых, образующих сторон catenary.

Поиск правильное значение для *a* в соответствии с catenary до размеров страницы телефона не прямой вычисления. Если *w* и *h* только ширину и высоту прямоугольника, оптимальное значение *a* удовлетворяет следующее уравнение:

Cosh (w/2 /) = 1 + h /

Следующий метод в [ `LinkedChainPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/LinkedChainPage.cs) классе находится, равенства, ссылаясь на два выражения слева и справа от знака равенства как `left` и `right`. Для небольших значений *a*, `left` больше, чем `right`; для больших значений *a*, `left` — меньше, чем `right`. `while` Сужается цикла в на оптимальное значение *a*:

```csharp
float FindOptimumA(float width, float height)
{
    Func<float, float> left = (float a) => (float)Math.Cosh(width / 2 / a);
    Func<float, float> right = (float a) => 1 + height / a;

    float gtA = 1;         // starting value for left > right
    float ltA = 10000;     // starting value for left < right

    while (Math.Abs(gtA - ltA) > 0.1f)
    {
        float avgA = (gtA + ltA) / 2;

        if (left(avgA) < right(avgA))
        {
            ltA = avgA;
        }
        else
        {
            gtA = avgA;
        }
    }

    return (gtA + ltA) / 2;
}
```

`SKPath` Объект ссылки, созданной в конструктор класса и полученный `SKPathEffect` объект затем устанавливается `PathEffect` свойство `SKPaint` объект, который хранится в виде поля:

```csharp
public class LinkedChainPage : ContentPage
{
    const float linkRadius = 30;
    const float linkThickness = 5;

    Func<float, float, float> catenary = (float a, float x) => (float)(a * Math.Cosh(x / a));

    SKPaint linksPaint = new SKPaint
    {
        Color = SKColors.Silver
    };

    public LinkedChainPage()
    {
        Title = "Linked Chain";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Create the path for the individual links
        SKRect outer = new SKRect(-linkRadius, -linkRadius, linkRadius, linkRadius);
        SKRect inner = outer;
        inner.Inflate(-linkThickness, -linkThickness);

        using (SKPath linkPath = new SKPath())
        {
            linkPath.AddArc(outer, 55, 160);
            linkPath.ArcTo(inner, 215, -160, false);
            linkPath.Close();

            linkPath.AddArc(outer, 235, 160);
            linkPath.ArcTo(inner, 395, -160, false);
            linkPath.Close();

            // Set that path as the 1D path effect for linksPaint
            linksPaint.PathEffect =
                SKPathEffect.Create1DPath(linkPath, 1.3f * linkRadius, 0,
                                          SKPath1DPathEffectStyle.Rotate);
        }
    }
    ...
}
```

Основная задача из `PaintSurface` обработчик заключается в создании catenary, сам путь. После определения оптимального *a* и сохранении ее в `optA` переменной, ему также необходимо вычислить смещение от верхней части окна. Затем он может образоваться коллекцию `SKPoint` значения для catenary превратить это в пути и нарисуйте контур с ранее созданным `SKPaint` объекта:

```csharp
public class LinkedChainPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Black);

        // Width and height of catenary
        int width = info.Width;
        float height = info.Height - linkRadius;

        // Find the optimum 'a' for this width and height
        float optA = FindOptimumA(width, height);

        // Calculate the vertical offset for that value of 'a'
        float yOffset = catenary(optA, -width / 2);

        // Create a path for the catenary
        SKPoint[] points = new SKPoint[width];

        for (int x = 0; x < width; x++)
        {
            points[x] = new SKPoint(x, yOffset - catenary(optA, x - width / 2));
        }

        using (SKPath path = new SKPath())
        {
            path.AddPoly(points, false);

            // And render that path with the linksPaint object
            canvas.DrawPath(path, linksPaint);
        }
    }
    ...
}
```

Эта программа определяет путь, используемый в `Create1DPath` иметь его (0, 0) точки в центре. Это кажется вполне разумным так как (0, 0) точки контура выравнивается со строкой или кривую, которую он добавляется. Тем не менее, можно использовать, не ориентированные (0, 0) указывать для специальных эффектов.

**Конвейерная лента** страница создает путь в виде прямоугольный конвейерная лента с кривой верхнее и нижнее, изменяется в соответствии с размером окна. Этот путь рисуется с помощью простого `SKPaint` объекта 20 пикселей в ширину и серого цвета и затем снова с обводкой другой `SKPaint` со `SKPathEffect` объекта, ссылающегося на путь в виде небольшой сегмент:

[![](effects-images/conveyorbelt-small.png "Тройной снимок экрана страницы конвейерная лента")](effects-images/conveyorbelt-large.png#lightbox "тройной снимок экрана страницы конвейерная лента")

(0, 0) точка сегмента пути — дескриптор, поэтому, когда `phase` аргумент анимируется, контейнеров, по-видимому, связана с конвейерная лента, возможно scooping вверх воды в нижней и сохранение его в верхней.

[ `ConveyorBeltPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConveyorBeltPage.cs) Класс реализует анимации с помощью переопределения метода `OnAppearing` и `OnDisappearing` методы. Путь для контейнера определяется в конструктор страницы:

```csharp
public class ConveyorBeltPage : ContentPage
{
    SKCanvasView canvasView;
    bool pageIsActive = false;

    SKPaint conveyerPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 20,
        Color = SKColors.DarkGray
    };

    SKPath bucketPath = new SKPath();

    SKPaint bucketsPaint = new SKPaint
    {
        Color = SKColors.BurlyWood,
    };

    public ConveyorBeltPage()
    {
        Title = "Conveyor Belt";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Create the path for the bucket starting with the handle
        bucketPath.AddRect(new SKRect(-5, -3, 25, 3));

        // Sides
        bucketPath.AddRoundedRect(new SKRect(25, -19, 27, 18), 10, 10,
                                  SKPathDirection.CounterClockwise);
        bucketPath.AddRoundedRect(new SKRect(63, -19, 65, 18), 10, 10,
                                  SKPathDirection.CounterClockwise);

        // Five slats
        for (int i = 0; i < 5; i++)
        {
            bucketPath.MoveTo(25, -19 + 8 * i);
            bucketPath.LineTo(25, -13 + 8 * i);
            bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                             SKPathDirection.CounterClockwise, 65, -13 + 8 * i);
            bucketPath.LineTo(65, -19 + 8 * i);
            bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                             SKPathDirection.Clockwise, 25, -19 + 8 * i);
            bucketPath.Close();
        }

        // Arc to suggest the hidden side
        bucketPath.MoveTo(25, -17);
        bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                         SKPathDirection.Clockwise, 65, -17);
        bucketPath.LineTo(65, -19);
        bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                         SKPathDirection.CounterClockwise, 25, -19);
        bucketPath.Close();

        // Make it a little bigger and correct the orientation
        bucketPath.Transform(SKMatrix.MakeScale(-2, 2));
        bucketPath.Transform(SKMatrix.MakeRotationDegrees(90));
    }
    ...
```

Код создания контейнера завершается с двумя преобразованиями, упрощающие немного больше контейнеров и включите ее в сторону. Применение этих преобразований было проще, чем настройка все координаты в предыдущем коде.

`PaintSurface` Обработчик начинает, определив конвейерная лента, сам путь. Это просто пару строк и пары точками круги, нарисованные с линией темно серый 20 пикселей уровня:

```csharp
public class ConveyorBeltPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        float width = info.Width / 3;
        float verticalMargin = width / 2 + 150;

        using (SKPath conveyerPath = new SKPath())
        {
            // Straight verticals capped by semicircles on top and bottom
            conveyerPath.MoveTo(width, verticalMargin);
            conveyerPath.ArcTo(width / 2, width / 2, 0, SKPathArcSize.Large,
                               SKPathDirection.Clockwise, 2 * width, verticalMargin);
            conveyerPath.LineTo(2 * width, info.Height - verticalMargin);
            conveyerPath.ArcTo(width / 2, width / 2, 0, SKPathArcSize.Large,
                               SKPathDirection.Clockwise, width, info.Height - verticalMargin);
            conveyerPath.Close();

            // Draw the conveyor belt itself
            canvas.DrawPath(conveyerPath, conveyerPaint);

            // Calculate spacing based on length of conveyer path
            float length = 2 * (info.Height - 2 * verticalMargin) +
                           2 * ((float)Math.PI * width / 2);

            // Value will be somewhere around 200
            float spacing = length / (float)Math.Round(length / 200);

            // Now animate the phase; t is 0 to 1 every 2 seconds
            TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
            float t = (float)(timeSpan.TotalSeconds % 2 / 2);
            float phase = -t * spacing;

            // Create the buckets PathEffect
            using (SKPathEffect bucketsPathEffect =
                        SKPathEffect.Create1DPath(bucketPath, spacing, phase,
                                                  SKPath1DPathEffectStyle.Rotate))
            {
                // Set it to the Paint object and draw the path again
                bucketsPaint.PathEffect = bucketsPathEffect;
                canvas.DrawPath(conveyerPath, bucketsPaint);
            }
        }
    }
}
```

Логика для рисования конвейерная лента не работает в альбомной ориентации.

О 200 пикселей друг от друга на конвейерная лента контейнеров должны быть расположены с равными интервалами. Тем не менее, конвейерная лента — это скорее всего, не кратным 200 пикселей в long, что означает, что `phase` аргумент `SKPathEffect.Create1DPath` является анимировано, сегментов откроется в и из него существование.

По этой причине программа сначала вычисляет значение с именем `length` то есть длина конвейерная лента. Поскольку конвейерная лента состоит из прямых линий и точками с кругов, это простые вычисления. Затем количество сегментов, значение вычисляется путем деления `length` по 200. Это значение округляется до ближайшего целого числа, и что число затем разделяется `length`. Результатом является интервалы для целого числа сегментов. `phase` Аргумент является просто небольшую часть.

## <a name="from-path-to-path-again"></a>Из пути к пути еще раз

В нижней части `DrawSurface` обработчик в **конвейерная лента**, закомментируйте `canvas.DrawPath` вызова и замените его следующим кодом:

```csharp
SKPath newPath = new SKPath();
bool fill = bucketsPaint.GetFillPath(conveyerPath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);
```

Как и в приведенном выше примере `GetFillPath`, вы увидите, что результаты, так же, за исключением цвет. После выполнения `GetFillPath`, `newPath` объект содержит несколько копий сегмент пути, каждый столбец в там же, где что анимация расположен их во время вызова.

## <a name="hatching-an-area"></a>Штриховки область

[ `SKPathEffect.Create2DLines` ](xref:SkiaSharp.SKPathEffect.Create2DLine(System.Single,SkiaSharp.SKMatrix)) Метод заполняет область с параллельными линиями, что часто называют *штриховки строки*. Метод имеет следующий синтаксис:

```csharp
public static SKPathEffect Create2DLine (Single width, SKMatrix matrix)
```

`width` Аргумент задает толщину штриха линий штриховки. `matrix` Параметр представляет собой сочетание поворота, масштабирования и необязательные. Коэффициент масштабирования указывает число пикселей, Skia использует для пространства линиями штриховки. Разделение между линиями фактор масштабирования за вычетом `width` аргумент. Если коэффициент масштабирования меньше или равно `width` значение, будет существовать без пробела между линиями штриховки, и области будет отображаться для заполнения. Укажите то же значение для горизонтального и вертикального масштабирования.

По умолчанию штриховка по горизонтали. Если `matrix` параметр содержит поворота, линиями штриховки поворачиваются по часовой стрелке.

**Штриховки заливки** страница демонстрирует этот эффект пути. [ `HatchFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/HatchFillPage.cs) Класс определяет три эффекты пути как поля, первый Горизонтальная штриховка линию толщиной 3 пикселя с коэффициент масштабирования, указывающее, что они располагаются на 6 пикселей друг от друга. Разделение строк таким образом, трем пикселям. Второй путь действует для вертикальной штриховка с шириной шесть точек расположенные 24 пикселя друг от друга (поэтому разделение — 18 пикселей), а третий — для штриховка по диагонали "строки" 12 широкая расстоянии друг от друга 36 пикселей друг от друга.

```csharp
public class HatchFillPage : ContentPage
{
    SKPaint fillPaint = new SKPaint();

    SKPathEffect horzLinesPath = SKPathEffect.Create2DLine(3, SKMatrix.MakeScale(6, 6));

    SKPathEffect vertLinesPath = SKPathEffect.Create2DLine(6,
        Multiply(SKMatrix.MakeRotationDegrees(90), SKMatrix.MakeScale(24, 24)));

    SKPathEffect diagLinesPath = SKPathEffect.Create2DLine(12,
        Multiply(SKMatrix.MakeScale(36, 36), SKMatrix.MakeRotationDegrees(45)));

    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 3,
        Color = SKColors.Black
    };
    ...
    static SKMatrix Multiply(SKMatrix first, SKMatrix second)
    {
        SKMatrix target = SKMatrix.MakeIdentity();
        SKMatrix.Concat(ref target, first, second);
        return target;
    }
}
```

Обратите внимание, что матрица `Multiply` метод. Коэффициенты вертикального и горизонтального масштабирования совпадают, порядок, в котором при умножении масштабировании и повороте матрицы не имеет значения.

`PaintSurface` Обработчик использует эти эффекты три пути с тремя различными цветами в сочетании с `fillPaint` для заполнения прямоугольник с закругленными углами, подобранные по размеру страницы. `Style` Свойство установлено `fillPaint` игнорируется; при `SKPaint` объект включает эффекта путь, созданный из `SKPathEffect.Create2DLine`, независимо от заполнения области:

```csharp
public class HatchFillPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath roundRectPath = new SKPath())
        {
            // Create a path
            roundRectPath.AddRoundedRect(
                new SKRect(50, 50, info.Width - 50, info.Height - 50), 100, 100);

            // Horizontal hatch marks
            fillPaint.PathEffect = horzLinesPath;
            fillPaint.Color = SKColors.Red;
            canvas.DrawPath(roundRectPath, fillPaint);

            // Vertical hatch marks
            fillPaint.PathEffect = vertLinesPath;
            fillPaint.Color = SKColors.Blue;
            canvas.DrawPath(roundRectPath, fillPaint);

            // Diagonal hatch marks -- use clipping
            fillPaint.PathEffect = diagLinesPath;
            fillPaint.Color = SKColors.Green;

            canvas.Save();
            canvas.ClipPath(roundRectPath);
            canvas.DrawRect(new SKRect(0, 0, info.Width, info.Height), fillPaint);
            canvas.Restore();

            // Outline the path
            canvas.DrawPath(roundRectPath, strokePaint);
        }
    }
    ...
}
```

Если вы внимательно посмотреть на результаты, вы увидите, что красный и синий штриховка не ограничен точно Скругленный прямоугольник. (Это очевидно характеристика основного кода Skia.) Если это является неудовлетворительным, показан альтернативный подход для штриховка по диагонали зеленым цветом: Скругленный прямоугольник используется в качестве контура обрезки и штриховой линии на всей странице.

`PaintSurface` Обработчик завершается вызов просто обводки Скругленный прямоугольник, чтобы можно было видеть расхождения с линиями штриховки красного и синего:

[![](effects-images/hatchfill-small.png "Тройной снимок экрана страницы штриховки заливки")](effects-images/hatchfill-large.png#lightbox "тройной снимок экрана страницы штриховки заливки")

Экран Android выглядит действительно подобное: масштабирование на снимке экрана вызвала тонкой красной линией и тонкой пространства для консолидации в адрес более широких первый взгляд красные линии и шире.

## <a name="filling-with-a-path"></a>Заполнение контура

[ `SKPathEffect.Create2DPath` ](xref:SkiaSharp.SKPathEffect.Create2DPath(SkiaSharp.SKMatrix,SkiaSharp.SKPath)) Позволяет Заливка области с путем, который реплицируется горизонтально и вертикально, по сути мозаичного заполнения области:

```csharp
public static SKPathEffect Create2DPath (SKMatrix matrix, SKPath path)
```

`SKMatrix` Коэффициенты масштабирования указывают горизонтальным и вертикальным пространством реплицированных пути. Но не может поворачивать пути, с помощью этого `matrix` аргумент; Если необходимо, чтобы путь (повернутый), повернуть сам путь с помощью `Transform` метода, определенного `SKPath`.

Реплицированные путь обычно выравнивается по левом и верхнем края экрана, а не заполняемая область. Это поведение можно переопределить, указав коэффициенты преобразования между 0 и коэффициенты масштабирования, чтобы указать горизонтальное и вертикальное смещение из сторон левом и верхнем.

**Путь заполняет** страница демонстрирует этот эффект пути. Путь, используемый для заполнения области определяется как поле в [ `PathTileFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathTileFillPage.cs) класса. Горизонтальные и вертикальные координаты диапазоном от от -40 до 40, это означает, что этот путь является 80 пикселей в квадрат:

```csharp
public class PathTileFillPage : ContentPage
{
    SKPath tilePath = SKPath.ParseSvgPathData(
        "M -20 -20 L 2 -20, 2 -40, 18 -40, 18 -20, 40 -20, " +
        "40 -12, 20 -12, 20 12, 40 12, 40 40, 22 40, 22 20, " +
        "-2 20, -2 40, -20 40, -20 8, -40 8, -40 -8, -20 -8 Z");
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Color = SKColors.Red;

            using (SKPathEffect pathEffect =
                   SKPathEffect.Create2DPath(SKMatrix.MakeScale(64, 64), tilePath))
            {
                paint.PathEffect = pathEffect;

                canvas.DrawRoundRect(
                    new SKRect(50, 50, info.Width - 50, info.Height - 50),
                    100, 100, paint);
            }
        }
    }
}
```

В `PaintSurface` обработчик, `SKPathEffect.Create2DPath` вызовы задает горизонтальным и вертикальным пространством до 64 заставить квадратные плитки 80 пикселей к их наложению. К счастью путь будет иметь вид пазла хорошо совмещения с соседних плитки:

[![](effects-images/pathtilefill-small.png "Тройной снимок экрана страницы заполняет путь")](effects-images/pathtilefill-large.png#lightbox "тройной снимок экрана страницы заполняет путь")

Масштабирование из исходного снимка экрана вызывает некоторые искажения, особенно на экран Android.

Обратите внимание на то, что эти плитки всегда отображается целиком и никогда не усекаются. На первых двух снимков экрана не даже очевидно, что заполняемая область является прямоугольник с закругленными углами. Если вы хотите усечь эти плитки для определенной области, используйте контура обрезки.

Попробуйте установить параметр `Style` свойство `SKPaint` объект `Stroke`, и вы увидите отдельные плитки описанные, а не заполнены.

Существует также возможность Заливка области с помощью мозаичной растровым изображением, как показано в статье [ **мозаичное заполнение точечного рисунка SkiaSharp**](../effects/shaders/bitmap-tiling.md).

## <a name="rounding-sharp-corners"></a>Округление острые углы

**Округленное Heptagon** программы представлены в [ **три способа нарисовать дугу** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md) статья используется касательных дуги для кривой точек семь стороны фигуры. **Другой округленное Heptagon** странице отображается гораздо более простой подход, использующий эффект путь, созданный из [ `SKPathEffect.CreateCorner` ](xref:SkiaSharp.SKPathEffect.CreateCorner(System.Single)) метод:

```csharp
public static SKPathEffect CreateCorner (Single radius)
```

Несмотря на то, что называется одного аргумента `radius`, ему необходимо присвоить половину радиус нужного угла. (Это характеристика основного кода Skia).

Вот `PaintSurface` обработчик в [ `AnotherRoundedHeptagonPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/AnotherRoundedHeptagonPage.cs) класса:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    int numVertices = 7;
    float radius = 0.45f * Math.Min(info.Width, info.Height);
    SKPoint[] vertices = new SKPoint[numVertices];
    double vertexAngle = -0.5f * Math.PI;       // straight up

    // Coordinates of the vertices of the polygon
    for (int vertex = 0; vertex < numVertices; vertex++)
    {
        vertices[vertex] = new SKPoint(radius * (float)Math.Cos(vertexAngle),
                                       radius * (float)Math.Sin(vertexAngle));
        vertexAngle += 2 * Math.PI / numVertices;
    }

    float cornerRadius = 100;

    // Create the path
    using (SKPath path = new SKPath())
    {
        path.AddPoly(vertices, true);

        // Render the path in the center of the screen
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = 10;

            // Set argument to half the desired corner radius!
            paint.PathEffect = SKPathEffect.CreateCorner(cornerRadius / 2);

            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.DrawPath(path, paint);

            // Uncomment DrawCircle call to verify corner radius
            float offset = cornerRadius / (float)Math.Sin(Math.PI * (numVertices - 2) / numVertices / 2);
            paint.Color = SKColors.Green;
            // canvas.DrawCircle(vertices[0].X, vertices[0].Y + offset, cornerRadius, paint);
        }
    }
}
```

Этот эффект можно использовать с Обводка или заполнение на основе `Style` свойство `SKPaint` объекта. Здесь выполняется:

[![](effects-images/anotherroundedheptagon-small.png "Тройной снимок экрана страницы другой округленное Heptagon")](effects-images/anotherroundedheptagon-large.png#lightbox "тройной снимок экрана страницы другой Heptagon округляется")

Вы увидите, что это округленное heptagon идентично программу более ранней. При необходимости дополнительные убедить радиус углов по-настоящему равно 100, а не 50 указан в `SKPathEffect.CreateCorner` вызов, вы можете Раскомментировать последняя инструкция в программе и см. в разделе 100-радиус круга наложены друг на друга в углу.

## <a name="random-jitter"></a>Случайные дрожание

Иногда подавая прямых компьютерной графике не совсем то, что нужно, и требуется хоть немного случайность. В этом случае будет необходимо выполнить попытку [ `SKPathEffect.CreateDiscrete` ](xref:SkiaSharp.SKPathEffect.CreateDiscrete(System.Single,System.Single,System.UInt32)) метод:

```csharp
public static SKPathEffect CreateDiscrete (Single segLength, Single deviation, UInt32 seedAssist)
```

Этот эффект пути можно использовать для Обводка или заполнения. Строки разделены соединенных сегментов — приблизительное длина которых определяется `segLength` — и расширить в разных направлениях. Экстент отклонение от исходной строки задается `deviation`.

Последний аргумент обозначает начальное значение, используемое для формирования псевдослучайных последовательность, используемая для эффекта. Эффекта дрожания будет выглядеть немного отличается для разных начальные значения. Аргумент имеет значение по умолчанию, равным нулю, это означает, что тот же эффект достигается при каждом выполнении программы. Если требуется другой дрожание всякий раз, когда перерисовки экрана, можно задать начальное значение `Millisecond` свойство `DataTime.Now` значение (например).


**Дрожание поэкспериментировать** страница позволяет поэкспериментировать с различными значениями в Обводка прямоугольник:

[![](effects-images/jitterexperiment-small.png "Тройной снимок экрана страницы дрожание эксперимента")](effects-images/jitterexperiment-large.png#lightbox "Triple screenshot of the JitterExperiment page")

Программа проста. [ **JitterExperimentPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml) файл создает два `Slider` элементов и `SKCanvasView`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Curves.JitterExperimentPage"
             Title="Jitter Experiment">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.Resources>
            <ResourceDictionary>
                <Style TargetType="Label">
                    <Setter Property="HorizontalTextAlignment" Value="Center" />
                </Style>

                <Style TargetType="Slider">
                    <Setter Property="Margin" Value="20, 0" />
                    <Setter Property="Minimum" Value="0" />
                    <Setter Property="Maximum" Value="100" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Slider x:Name="segLengthSlider"
                Grid.Row="0"
                ValueChanged="sliderValueChanged" />

        <Label Text="{Binding Source={x:Reference segLengthSlider},
                              Path=Value,
                              StringFormat='Segment Length = {0:F0}'}"
               Grid.Row="1" />

        <Slider x:Name="deviationSlider"
                Grid.Row="2"
                ValueChanged="sliderValueChanged" />

        <Label Text="{Binding Source={x:Reference deviationSlider},
                              Path=Value,
                              StringFormat='Deviation = {0:F0}'}"
               Grid.Row="3" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="4"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

`PaintSurface` Обработчик в [ **JitterExperimentPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml.cs) файл с выделенным кодом вызывается всякий раз, когда `Slider` изменении значения. Он вызывает `SKPathEffect.CreateDiscrete` с помощью двух `Slider` значения и использует его для обводки прямоугольника:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float segLength = (float)segLengthSlider.Value;
    float deviation = (float)deviationSlider.Value;

    using (SKPaint paint = new SKPaint())
    {
        paint.Style = SKPaintStyle.Stroke;
        paint.StrokeWidth = 5;
        paint.Color = SKColors.Blue;

        using (SKPathEffect pathEffect = SKPathEffect.CreateDiscrete(segLength, deviation))
        {
            paint.PathEffect = pathEffect;

            SKRect rect = new SKRect(100, 100, info.Width - 100, info.Height - 100);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

Этот эффект можно использовать для заполнения, а также в этом случае регулируется по этим изменениям случайных контур заполненной области. **Дрожание: текст** страницы показано использование этого путь для отображения текста. Большая часть кода, приведенного в `PaintSurface` обработчик [ `JitterTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterTextPage.cs) класса, предназначенного для изменения размера и Центрирование текста:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string text = "FUZZY";

    using (SKPaint textPaint = new SKPaint())
    {
        textPaint.Color = SKColors.Purple;
        textPaint.PathEffect = SKPathEffect.CreateDiscrete(3f, 10f);

        // Adjust TextSize property so text is 95% of screen width
        float textWidth = textPaint.MeasureText(text);
        textPaint.TextSize *= 0.95f * info.Width / textWidth;

        // Find the text bounds
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);

        // Calculate offsets to center the text on the screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        canvas.DrawText(text, xText, yText, textPaint);
    }
}
```

Здесь он работает в альбомном режиме:

[![](effects-images/jittertext-small.png "Тройной снимок экрана страницы дрожание: текст")](effects-images/jittertext-large.png#lightbox "Triple screenshot of the JitterText page")

## <a name="path-outlining"></a>Структурирование путь

Вы уже видели два примера небольшой [ `GetFillPath` ](xref:SkiaSharp.SKPaint.GetFillPath*) метод `SKPaint`, что существует две версии:

```csharp
public Boolean GetFillPath (SKPath src, SKPath dst, Single resScale = 1)

public Boolean GetFillPath (SKPath src, SKPath dst, SKRect cullRect, Single resScale = 1)
```

Требуются только первые два аргумента. Метод получает доступ к пути ссылается `src` аргумент, изменяет данные пути, на основе свойство stroke в `SKPaint` объекта (включая `PathEffect` свойство) и записывает результаты в `dst` путь. `resScale` Параметр позволяет понизить точность для создания небольших конечный путь и `cullRect` аргумент можно исключить контуров за пределами прямоугольника.

Один базовое использование этого метода не включает эффекты пути вообще: Если `SKPaint` объект имеет его `Style` свойство значение `SKPaintStyle.Stroke`и выполняет *не* у его `PathEffect` , заданы `GetFillPath` создает путь, представляющий *структуры* пути источника так, как если бы он был нарисован с помощью свойств paint.

Например если `src` путь отображается в виде простого круга радиуса 500 и `SKPaint` объект определяет ширину штриха 100, то `dst` путь стал двух концентрических окружностей, один с радиусом 450, а другой с радиусом 550. Этот метод вызывается `GetFillPath` так, как заполнить это `dst` путь совпадает со значением Обводка `src` путь. Но можно также выполнить обводку `dst` путь к см. в разделе контуров пути.

**Коснитесь, чтобы структуры путь** демонстрирует это. `SKCanvasView` И `TapGestureRecognizer` создаются в [ **TapToOutlineThePathPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml) файла. [ **TapToOutlineThePathPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml.cs) определяет файл с выделенным кодом трех `SKPaint` объектов в виде полей, два для Обводка с обводки ширину 100 и 20, а третий для заполнения:

```csharp
public partial class TapToOutlineThePathPage : ContentPage
{
    bool outlineThePath = false;

    SKPaint redThickStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 100
    };

    SKPaint redThinStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 20
    };

    SKPaint blueFill = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue
    };

    public TapToOutlineThePathPage()
    {
        InitializeComponent();
    }

    void OnCanvasViewTapped(object sender, EventArgs args)
    {
        outlineThePath ^= true;
        (sender as SKCanvasView).InvalidateSurface();
    }
    ...
}
```

Если экран не прикосновения `PaintSurface` обработчик использует `blueFill` и `redThickStroke` рисования объектов для подготовки к просмотру на кольцевую траекторию:

```csharp
public partial class TapToOutlineThePathPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath circlePath = new SKPath())
        {
            circlePath.AddCircle(info.Width / 2, info.Height / 2,
                                 Math.Min(info.Width / 2, info.Height / 2) -
                                 redThickStroke.StrokeWidth);

            if (!outlineThePath)
            {
                canvas.DrawPath(circlePath, blueFill);
                canvas.DrawPath(circlePath, redThickStroke);
            }
            else
            {
                using (SKPath outlinePath = new SKPath())
                {
                    redThickStroke.GetFillPath(circlePath, outlinePath);

                    canvas.DrawPath(outlinePath, blueFill);
                    canvas.DrawPath(outlinePath, redThinStroke);
                }
            }
        }
    }
}
```

Элемент управления circle заполняется и обводкой, как и следовало ожидать:

[![](effects-images/taptooutlinethepathnormal-small.png "Тройной снимок экрана страницы обычный коснитесь для структуры Path")](effects-images/taptooutlinethepathnormal-large.png#lightbox "тройной снимок экрана страницы обычный коснитесь для структуры Path")

При касании экрана, `outlineThePath` присваивается `true`и `PaintSurface` обработчик создает новый `SKPath` объекта и использует его в качестве пути назначения в вызове `GetFillPath` на `redThickStroke` рисования объекта. Затем заполняется и обводке с помощью этого пути назначения `redThinStroke`, в следующем результате:

[![](effects-images/taptooutlinethepathoutlined-small.png "Тройной снимок экрана страницы контурные коснитесь для структуры Path")](effects-images/taptooutlinethepathoutlined-large.png#lightbox "тройной снимок экрана страницы контурные коснитесь для структуры Path")

Два красными кружками четкого указания, что исходный путь циклическая была преобразована в двух контуров циклическая.

Этот метод может быть полезным при разработке пути должны использоваться в `SKPathEffect.Create1DPath` метод. Указанных в этих методах путей, всегда заполнены при репликации пути. Если вы не хотите весь путь должен быть заполнен, необходимо тщательно определить контуров.

Например, в **связанной цепочке** образца ссылки были определены с помощью четырех дуг, каждая пара из которых были основаны на двух радиусы показано области заполнения пути. Можно заменить код в `LinkedChainPage` класса, чтобы сделать его немного по-другому.

Во-первых, необходимо переопределить `linkRadius` константы:

```csharp
const float linkRadius = 27.5f;
const float linkThickness = 5;
```

`linkPath` Сейчас только двух дуги, исходя из этого одного radius с требуемым запустите углы и углы поворота:

```csharp
using (SKPath linkPath = new SKPath())
{
    SKRect rect = new SKRect(-linkRadius, -linkRadius, linkRadius, linkRadius);
    linkPath.AddArc(rect, 55, 160);
    linkPath.AddArc(rect, 235, 160);

    using (SKPaint strokePaint = new SKPaint())
    {
        strokePaint.Style = SKPaintStyle.Stroke;
        strokePaint.StrokeWidth = linkThickness;

        using (SKPath outlinePath = new SKPath())
        {
            strokePaint.GetFillPath(linkPath, outlinePath);

            // Set that path as the 1D path effect for linksPaint
            linksPaint.PathEffect =
                SKPathEffect.Create1DPath(outlinePath, 1.3f * linkRadius, 0,
                                          SKPath1DPathEffectStyle.Rotate);

        }

    }
}
```

`outlinePath` Объект затем является получателем контура `linkPath` когда оно рисуется с помощью свойств, указанных в `strokePaint`.

Еще один пример, с помощью этой методики ждет для пути, используемого в методе.

## <a name="combining-path-effects"></a>Объединяя эффекты пути

Два метода создания окончательного статический `SKPathEffect` являются [ `SKPathEffect.CreateSum` ](xref:SkiaSharp.SKPathEffect.CreateSum(SkiaSharp.SKPathEffect,SkiaSharp.SKPathEffect)) и [ `SKPathEffect.CreateCompose` ](xref:SkiaSharp.SKPathEffect.CreateCompose(SkiaSharp.SKPathEffect,SkiaSharp.SKPathEffect)):

```csharp
public static SKPathEffect CreateSum (SKPathEffect first, SKPathEffect second)

public static SKPathEffect CreateCompose (SKPathEffect outer, SKPathEffect inner)
```

Оба эти метода объединить два эффекты пути для создания эффекта составного пути. `CreateSum` Метод создает эффект, аналогичную эффекты два пути, применять отдельно, хотя `CreateCompose` применяет эффект один путь ( `inner`), а затем применяет `outer` к этому.

Вы уже видели как `GetFillPath` метод `SKPaint` можно преобразовать один путь к другой путь, на основе `SKPaint` свойства (включая `PathEffect`), он не должен быть *слишком* загадочным, как `SKPaint`объект может выполнить эту операцию дважды с эффектами два пути, указанного в `CreateSum` или `CreateCompose` методы.

Один очевидный использования `CreateSum` является определение `SKPaint` объект, который заполняет путь с эффектом один путь, а Обводка путь с эффектом другой путь. Это показано в **кошки в кадре** образца, который отображает массив кошки в течение периода с Зубчатый границ:

[![](effects-images/catsinframe-small.png "Тройной снимок экрана страницы кошки в кадр")](effects-images/catsinframe-large.png#lightbox "тройной снимок экрана страницы кошки в кадр")

[ `CatsInFramePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CatsInFramePage.cs) Класса начинается с определения нескольких полей. Может распознать первого поля из [ `PathDataCatPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs) класса из [ **данные пути SVG** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md) статьи. Второй путь основан на строки и дуги для шаблона Зубцы кадра:

```csharp
public class CatsInFramePage : ContentPage
{
    // From PathDataCatPage.cs
    SKPath catPath = SKPath.ParseSvgPathData(
        "M 160 140 L 150 50 220 103" +              // Left ear
        "M 320 140 L 330 50 260 103" +              // Right ear
        "M 215 230 L 40 200" +                      // Left whiskers
        "M 215 240 L 40 240" +
        "M 215 250 L 40 280" +
        "M 265 230 L 440 200" +                     // Right whiskers
        "M 265 240 L 440 240" +
        "M 265 250 L 440 280" +
        "M 240 100" +                               // Head
        "A 100 100 0 0 1 240 300" +
        "A 100 100 0 0 1 240 100 Z" +
        "M 180 170" +                               // Left eye
        "A 40 40 0 0 1 220 170" +
        "A 40 40 0 0 1 180 170 Z" +
        "M 300 170" +                               // Right eye
        "A 40 40 0 0 1 260 170" +
        "A 40 40 0 0 1 300 170 Z");

    SKPaint catStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 5
    };

    SKPath scallopPath =
        SKPath.ParseSvgPathData("M 0 0 L 50 0 A 60 60 0 0 1 -50 0 Z");

    SKPaint framePaint = new SKPaint
    {
        Color = SKColors.Black
    };
    ...
}
```

`catPath` Может использоваться в `SKPathEffect.Create2DPath` метод Если `SKPaint` объект `Style` свойству `Stroke`. Тем не менее если `catPath` используется непосредственно в этой программе, затем весь заголовок cat будет заполнено и ограничителей выбросов даже не будет отображаться. (Попробуйте!) Это необходимо для получения структуры этого пути и использования этой структуры в `SKPathEffect.Create2DPath` метод.

Конструктор выполняет это задание. Он сначала применяет два преобразования для `catPath` для перемещения (0, 0) указать центр обработки и уменьшать его размер. `GetFillPath` Получает все структуры контуров в `outlinedCatPath`, и этот объект используется в `SKPathEffect.Create2DPath` вызова. Масштабирование учитываются `SKMatrix` немного больше, чем горизонтальное значение а вертикальный размер окна cat для обеспечения мало буфера между плиток, несмотря на translation factors производным немного эмпирически таким образом, полный cat видимым в верхнего левого угла кадра:

```csharp
public class CatsInFramePage : ContentPage
{
    ...
    public CatsInFramePage()
    {
        Title = "Cats in Frame";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Move (0, 0) point to center of cat path
        catPath.Transform(SKMatrix.MakeTranslation(-240, -175));

        // Now catPath is 400 by 250
        // Scale it down to 160 by 100
        catPath.Transform(SKMatrix.MakeScale(0.40f, 0.40f));

        // Get the outlines of the contours of the cat path
        SKPath outlinedCatPath = new SKPath();
        catStroke.GetFillPath(catPath, outlinedCatPath);

        // Create a 2D path effect from those outlines
        SKPathEffect fillEffect = SKPathEffect.Create2DPath(
            new SKMatrix { ScaleX = 170, ScaleY = 110,
                           TransX = 75, TransY = 80,
                           Persp2 = 1 },
            outlinedCatPath);

        // Create a 1D path effect from the scallop path
        SKPathEffect strokeEffect =
            SKPathEffect.Create1DPath(scallopPath, 75, 0, SKPath1DPathEffectStyle.Rotate);

        // Set the sum the effects to frame paint
        framePaint.PathEffect = SKPathEffect.CreateSum(fillEffect, strokeEffect);
    }
    ...
}
```

Затем конструктор вызывает `SKPathEffect.Create1DPath` Зубчатый фрейма. Обратите внимание, что ширина путь — 100 пикселей, аванс — 75 пикселей, таким образом, чтобы реплицированные путь перекрывается вокруг frame. Последняя инструкция конструктор вызывает `SKPathEffect.CreateSum` объединить эффекты два пути и присвоить результат `SKPaint` объекта.

Вся эта работа позволяет `PaintSurface` обработчик должен быть довольно простым. Вполне достаточно для определения прямоугольника и рисования с помощью `framePaint`:

```csharp
public class CatsInFramePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect rect = new SKRect(50, 50, info.Width - 50, info.Height - 50);
        canvas.ClipRect(rect);
        canvas.DrawRect(rect, framePaint);
    }
}
```

Алгоритмы эффекты пути всегда вызывать весь путь используется для Обводка или заполнение должны отображаться, что может вызвать некоторые визуальные элементы вне прямоугольника. `ClipRect` Вызывать до `DrawRect` вызова позволяет визуальных элементов быть значительно более понятным. (Попробуйте без обрезки!)

Это часто используется `SKPathEffect.CreateCompose` дрожание добавляемый другой эффект пути. Определенно можно поэкспериментировать самостоятельно, но ниже приведен пример, немного отличаются:

**Пунктирных линий штриховки** осуществляется заливка эллипса с линиями штриховки, которые являются пунктирная. Большая часть работы в [ `DashedHatchLinesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DashedHatchLinesPage.cs) класса выполняется непосредственно в определениях полей. Эти поля определяют эффекта тире и эффекта штриховки. Они определяются как `static` так, как они указываются в `SKPathEffect.CreateCompose` вызов в `SKPaint` определение:

```csharp
public class DashedHatchLinesPage : ContentPage
{
    static SKPathEffect dashEffect =
        SKPathEffect.CreateDash(new float[] { 30, 30 }, 0);

    static SKPathEffect hatchEffect = SKPathEffect.Create2DLine(20,
        Multiply(SKMatrix.MakeScale(60, 60),
                 SKMatrix.MakeRotationDegrees(45)));

    SKPaint paint = new SKPaint()
    {
        PathEffect = SKPathEffect.CreateCompose(dashEffect, hatchEffect),
        StrokeCap = SKStrokeCap.Round,
        Color = SKColors.Blue
    };
    ...
    static SKMatrix Multiply(SKMatrix first, SKMatrix second)
    {
        SKMatrix target = SKMatrix.MakeIdentity();
        SKMatrix.Concat(ref target, first, second);
        return target;
    }
}
```

`PaintSurface` Обработчик должен содержать только стандартные затраты, а также один вызов `DrawOval`:

```csharp
public class DashedHatchLinesPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        canvas.DrawOval(info.Width / 2, info.Height / 2,
                        0.45f * info.Width, 0.45f * info.Height,
                        paint);
    }
    ...
}
```

Как вам уже известно, штриховка не точно ограниченные внутренней области, и в этом примере они всегда начинаются с левого входа с всей тире:

[![](effects-images/dashedhatchlines-small.png "Тройной снимок экрана страницы Пунктирная штриховка")](effects-images/dashedhatchlines-large.png#lightbox "тройной снимок экрана страницы Пунктирная штриховка")

Теперь, когда вы увидели эффекты пути, от простых точек и тире странное сочетаниям использовать свое воображение и см. в разделе, можно создать.



## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
