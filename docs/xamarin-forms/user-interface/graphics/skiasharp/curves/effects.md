---
title: Эффекты путь в SkiaSharp
description: В этой статье описываются различные эффекты путь SkiaSharp, которые разрешены пути для срезом и заполнения, а это демонстрируется с примерами кода.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 95167D1F-A718-405A-AFCC-90E596D422F3
author: charlespetzold
ms.author: chape
ms.date: 07/29/2017
ms.openlocfilehash: 2071a2fb140d0e9c78d4c86d6aa70d3606dc1f98
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35244114"
---
# <a name="path-effects-in-skiasharp"></a>Эффекты путь в SkiaSharp

_Обнаружить различные пути эффекты, которые разрешены пути для срезом и заполнения_

Объект *эффект пути* является экземпляром класса [ `SKPathEffect` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathEffect/) класс, созданный с помощью одного из восьми статический `Create` методы. `SKPathEffect` Объекта присваивается значение [ `PathEffect` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.PathEffect/) свойство `SKPaint` объекта ряд интересных эффектов, например, срезом строку с небольшой реплицированных путь:

![](effects-images/patheffectsample.png "В образце связанной цепи")

Путь эффекты позволяют:

- Обводки строку с точек и тире
- Обводки строку с любой заполненный путь
- Заливка области с штриховка
- Заливка области с Мозаичная путь
- Сделать острые скругленные углы
- Добавление произвольного «флуктуаций» для прямых и кривых линий

Кроме того можно объединить две или более путь эффекты.

В этой статье также демонстрируется использование `GetFillPath` метод `SKPaint` для преобразования одного пути в другой путь путем применения свойства `SKPaint`, в том числе `StrokeWidth` и `PathEffect`. Это приводит к некоторые интересные методы, например для получения пути, являющемся контуром другой путь. `GetFillPath` Это очень удобно, если в связи с эффекты пути.

## <a name="dots-and-dashes"></a>Точек и тире

Использование [ `PathEffect.CreateDash` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateDash/p/System.Single[]/System.Single/) метод, описанный в статье [ **точек и тире**](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md). Первым аргументом метода является массив, содержащий четное число два или несколько значений, переключаясь между длины штрихов и промежутков между дефисы длины:

```csharp
public static SKPathEffect CreateDash (Single[] intervals, Single phase)
```

Эти значения являются *не* относительно ширины штриха. Например, если требуется строка состоит из квадратных штрихов и пробелов квадратный толщина штриха — 10, задать `intervals` массив {10, 10}. `phase` Аргумент указывает, где начинается строки в шаблоне. В данном примере, если строка начинается с квадратный разрыв, значение `phase` до 10.

Затронутые концов штрихов `StrokeCap` свойство `SKPaint`. Для широкой ширины, присвойте этому свойству значение очень часто `SKStrokeCap.Round` округления концов штрихов. В этом случае значения в `intervals` массива *не* включают увеличения длины, возникающие в результате округления, это означает, что циклическая точка требует указания ширины, равной нулю. Толщина 10, чтобы создать строку с циклической точек и промежутков между точками же диаметр, используйте `intervals` массив {0, 20}.

**Анимации текста с точками** страница подобна **описанные текст** страниц, описанное в статье [ **Объединение текста и графики** ](~/xamarin-forms/user-interface/graphics/skiasharp/basics/text.md) в он отображает указанные символы текста, задав `Style` свойство `SKPaint` объект `SKPaintStyle.Stroke`. Кроме того **анимации текста с точками** использует `SKPathEffect.CreateDash` для предоставления это структуры пунктирная внешний, и программа также анимирует `phase` аргумент `SKPathEffect.CreateDash` метод для установки точек по-видимому перемещаются вокруг текста символы. Вот страницы в альбомной ориентации.

[![](effects-images/animateddottedtext-small.png "Тройной снимок экрана со страницей анимации текста с точками")](effects-images/animateddottedtext-large.png#lightbox "тройной снимок экрана со страницей анимации текста с точками")

[ `AnimatedDottedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs) Класс начинается определение нескольких констант и также переопределяет `OnAppearing` и `OnDisappearing` методы для анимации:

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

`PaintSurface` Обработчик начинается с создания `SKPaint` объекта для отображения текста. `TextSize` Настройки свойства в зависимости от ширины экрана:

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

В конце метода `SKPathEffect.CreateDash` метод вызывается с помощью `dashArray` , определенный как поля и анимированных `phase` значение. `SKPathEffect` Экземпляр имеет значение `PathEffect` свойство `SKPaint` объекта для отображения текста.

Кроме того, можно задать `SKPathEffect` объект `SKPaint` объект перед измерение текст и его выравнивание по центру страницы. В этом случае Однако анимированных точек и тире вызвать небольшие различия в размер отображаемого текста и текста, как правило, немного компакт-дисков. (Попробуйте!)

Вы будете также Обратите внимание, что круг анимированных точек вокруг символов текста, определенного места в каждой секции замкнутой кривой где кажется точек pop и из него существование. Это, где путь, который определяет символ структуры начинается и заканчивается. Если длина пути не является целым числом, кратным длину штрихов (в этом случае 20 пикселей) только часть этого шаблона можно разместить в конце пути.

Можно изменить длину штрихов в соответствии с длиной пути, однако, требуется определить длину пути, метод, который будет, описанные в следующей статье.

**Точку / штрих Morph** программы анимирует сам штрихов, чтобы по-видимому, тире разделить на несколько точек, где объединяются в форме тире еще раз:

[![](effects-images/dotdashmorph-small.png "Тройной снимок экрана со страницей Morph тире точка")](effects-images/dotdashmorph-large.png#lightbox "тройной снимок экрана со страницей Morph точка тире")

[ `DotDashMorphPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs) Класса переопределения `OnAppearing` и `OnDisappearing` так же, как было в предыдущей программе, но этот класс определяет методы `SKPaint` объект как поле:

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

`PaintSurface` Обработчик создает контура эллипса исходя из размера страницы и выполняет много раздел кода, который задает `dashArray` и `phase` переменных. Анимированный переменной `t` в диапазоне от 0 до 1, `if` блоки разбить это время, в четырех кварталов и в каждом из этих кварталов `tsub` также лежит в диапазоне от 0 до 1. В самом конце программа создает `SKPathEffect` и назначает ей `SKPaint` объекта для рисования.

## <a name="from-path-to-path"></a>Из пути к пути

[ `GetFillPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetFillPath/p/SkiaSharp.SKPath/SkiaSharp.SKPath/System.Single/) Метод `SKPaint` примет один путь к другому в зависимости от параметров в `SKPaint` объекта. Чтобы увидеть, как это работает, замените `canvas.DrawPath` вызов в предыдущем программы с помощью следующего кода:

```csharp
SKPath newPath = new SKPath();
bool fill = ellipsePaint.GetFillPath(ellipsePath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);

```

В этот новый код `GetFillPath` вызова преобразует `ellipsePath` (то есть просто Овал) в `newPath`, который отображается с `newPaint`. `newPaint` Со всеми параметрами по умолчанию свойство создается объект за исключением того, `Style` является свойство на основе логического значения возвращаемое значение из `GetFillPath`.

Визуальные элементы идентичны, за исключением цвет, который задан в `ellipsePaint` , но не `newPaint`. Вместо простой эллипс, определенные в `ellipsePath`, `newPath` содержит многочисленные путь контуров, которые определяют последовательность точек и тире. Это результат применения различных свойств `ellipsePaint` — `StrokeWidth`, `StrokeCap`, и `PathEffect` — для `ellipsePath` и разместив результирующий путь в `newPath`. `GetFillPath` Метод возвращает логическое значение, указывающее ли целевой путь должен быть заполнен; в этом примере возвращается `true` для заполнения путь.

Попробуйте изменить `Style` в `newPaint` для `SKPaintStyle.Stroke` и вы увидите контуров отдельных путь линией один ширина в пикселях.

## <a name="stroking-with-a-path"></a>Срезом с путем

[ `SKPathEffect.Create1DPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.Create1DPath/p/SkiaSharp.SKPath/System.Single/System.Single/SkiaSharp.SKPath1DPathEffectStyle/) Метод аналогичен `SKPathEffect.CreateDash` за исключением того, чтобы указать путь, а не шаблон из штрихов и пробелов. Этот путь реплицируется несколько раз обводки строки или кривой.

Синтаксис выглядит следующим образом.

```csharp
public static SKPathEffect Create1DPath (SKPath path, Single advance,
                                         Single phase, SKPath1DPathEffectStyle style)
```

> [!IMPORTANT]
> Обратите особое внимание: отсутствует перегрузку `Create1DPath` , определенный с аргументом типа перечисления `SkPath1DPathEffect` строчных «k». Это имя является ошибкой, и следовательно неправильный перечисления и метод устарели, но очень легко устаревший метод становятся частью кода, что трудно точно узнать, какие.

В общем, путь, который можно передать `Create1DPath` будет небольшим и по центру относительно точки (0, 0). `advance` Указывает расстояние от центров путь как путь реплицируется в строке. Обычно этот аргумент присвоено приблизительную ширину пути. `phase` Аргумент играет той же роли здесь как это делается при `CreateDash` метод.

[ `SKPath1DPathEffectStyle` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath1DPathEffectStyle/) Имеет три члена:

- [`Translate`](https://developer.xamarin.com/api/field/SkiaSharp.SKPath1DPathEffectStyle.Translate/)
- [`Rotate`](https://developer.xamarin.com/api/field/SkiaSharp.SKPath1DPathEffectStyle.Rotate/)
- [`Morph`](https://developer.xamarin.com/api/field/SkiaSharp.SKPath1DPathEffectStyle.Morph/)

`Translate` Член вызывает путь должен оставаться в ту же ориентацию, как они реплицируются вдоль линии или формы. Для `Rotate`, путь поворачивается основании касательной кривой. Путь содержит его нормальную ориентацию для горизонтальных линий. `Morph` Аналогично `Rotate` , но сам путь также изгиб сопоставляемое кривизны вычерчивании строки.

**Эффект пути 1 D** страницы демонстрирует следующие три варианта. [ **OneDimensionalPathEffectPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml) файл Определяет палитра, содержащая три элементы, соответствующие три члена перечисления:

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
            <Picker.Items>
                <x:String>Translate</x:String>
                <x:String>Rotate</x:String>
                <x:String>Morph</x:String>
            </Picker.Items>
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

[ **OneDimensionalPathEffectPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml.cs) файл кода определяет три `SKPathEffect` объекты в виде полей. Они создаются с помощью `SKPathEffect.Create1DPath` с `SKPath` объекты, созданные с помощью `SKPath.ParseSvgPathData`. Во-первых, простое окно, второй — в форме ромбовидной фигуры и третий — прямоугольник. Эти значения используются для демонстрации три эффект стили:

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

            switch (effectStylePicker.Items[effectStylePicker.SelectedIndex])
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

`PaintSurface` Обработчик создает кривую Безье, циклы вокруг сам и обращается к палитре, чтобы определить, какие `PathEffect` должен использоваться для вычерчивания его. Следующие три параметра — `Translate`, `Rotate`, и `Morph` — отображаются слева направо:

[![](effects-images/1dpatheffect-small.png "Тройной снимок экрана со страницей эффект пути 1D")](effects-images/1dpatheffect-large.png#lightbox "тройной снимок экрана со страницей эффект пути 1 D")

Путь, указанный в `SKPathEffect.Create1DPath` метод заполняется всегда. Путь, указанный в `DrawPath` метод вычерчивается всегда, если `SKPaint` объект имеет его `PathEffect` свойством, имеющим значение 1 D эффект. Обратите внимание, что `pathPaint` объект не имеет `Style` значение обычно по умолчанию для `Fill`, но путь вычерчивается независимо от.

Поля, используемые в `Translate` пример — 20 пикселей квадратом и `advance` аргумента равно 24. Это различие вызывает разрыв между полями строке примерно: горизонтальная или вертикальная, когда поля немного перекрываются, если строка соответствует диагональный, поскольку диагонали поле 28.3 пикселей.

Ромбовидной фигуры в `Rotate` примере также составляет 20 пикселов. `advance` Равен 20, чтобы продолжить точки касания как ромб поворачивается вместе с кривизны строки.

Прямоугольника в `Morph` пример — 50 пикселей по ширине с `advance` параметра 55 выполнять небольшой разрыв между прямоугольниками, пока они погнуты вокруг кривой Безье.

Если `advance` аргумент меньше, чем размер пути, а затем могут пересекаться реплицированных пути. Это может привести к некоторые интересные эффекты. **Связанная цепочка** страница отображает ряд перекрывающихся окружности, по-видимому напоминают связанного цепи зависает различение форму catenary:

[![](effects-images/linkedchain-small.png "Тройной снимок экрана со страницей связанная цепочка")](effects-images/linkedchain-large.png#lightbox "тройной снимок экрана со страницей связанной цепи")

Найдите очень близкие и вы увидите, что это не фактически кругов. Каждая ссылка в цепочке — две дуги, изменять размеры и расположение, кажется, связь с соседними ссылки.

В форме catenary зависает цепочки или кабель универсальный вес распределения. Архитектура, встроенные в форме инвертированный catenary использует преимущества, равного распределения нагрузки на вес arch. Catenary имеет первый взгляд, простых математических Описание:

y = · Cosh(x / a)

*Cosh* является функция гиперболический косинус. Для *x* равно 0, *cosh* равен нулю и *y* равняется *a*. Это Центр catenary. Как *косинус* функция, *cosh* считается *даже*, означающее, что *cosh(–x)* равняется *cosh(x)*, и значения увеличивают повышающее аргументы положительным или отрицательным. Эти значения описывают кривых, образующих сторон catenary.

Поиск правильное значение для *a* в соответствии с catenary до размеров страницы телефона не прямой вычисления. Если *w* и *h* только ширину и высоту прямоугольника, оптимальное значение *a* удовлетворяет следующее уравнение:

Cosh (w/2 /) = 1 + h /

Следующий метод в [ `LinkedChainPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/LinkedChainPage.cs) класс включает в себя равенства, ссылаясь на два выражения слева и справа от знака равенства как `left` и `right`. Для небольших значений *a*, `left` больше, чем `right`; для больших значений *a*, `left` — меньше, чем `right`. `while` Сужается цикла в на оптимальное значение *a*:

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

`SKPath` Объекта для ссылки, созданной в конструктор класса и обработка результирующих `SKPathEffect` объекта присваивается значение `PathEffect` свойство `SKPaint` объекта, который хранится в виде поля:

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

Задание основного `PaintSurface` обработчик — создать путь для catenary, сам. После определения оптимального *a* и сохранении ее в `optA` переменной, ему также необходимо вычислить смещение от верхней части окна. Затем он может образоваться коллекцию `SKPoint` значения для catenary, включите в путь и нарисовать контур с ранее созданный `SKPaint` объекта:

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

Эта программа определяет путь, используемый в `Create1DPath` иметь его (0, 0) в центре точки. Это кажется разумным так как (0, 0) точки контура выравнивается линии или кривой, он добавляется. Тем не менее, можно использовать без центром (0, 0) точка для некоторые специальные эффекты.

**Конвейерная лента** страница создает пути, наподобие прямоугольный конвейерная лента с кривой верхнее и нижнее это подбирается по размерам окна. Этот путь вычерчивается с помощью простого `SKPaint` объекта 20 пикселей в ширину и серого цвета, а затем вычерчивании попытку с другим `SKPaint` объекта с `SKPathEffect` объекта, ссылающегося на путь, наподобие мало контейнеров:

[![](effects-images/conveyorbelt-small.png "Тройной снимок экрана со страницей конвейерная лента")](effects-images/conveyorbelt-large.png#lightbox "тройной снимок экрана со страницей конвейерная лента")

(0, 0) точка сегмента пути — дескриптор, поэтому после `phase` аргумент анимировано, контейнеров, по-видимому, связана с конвейерная лента, возможно scooping копирование воды внизу и сохранение его в верхней.

[ `ConveyorBeltPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConveyorBeltPage.cs) Класс реализует анимации с помощью переопределений `OnAppearing` и `OnDisappearing` методы. На странице конструктора задан путь для контейнера:

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

Код создания контейнеров завершается с двумя преобразованиями, упрощающие немного больше контейнеров и включить его в альбомной ориентации. Применение этих преобразований было проще, чем настройка все координаты в предыдущем примере кода.

`PaintSurface` Обработчик начинается путем определения путь для конвейерной лентой, сам. Это просто пары строк и пары с окружности, отображаются с темно серый 20 пикселей всей строки:

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

Логику для рисования конвейерная лента не работает в режиме альбомной ориентации.

Контейнеры должны расположенные о 200 пикселей друг от друга на конвейерная лента. Конвейерная лента то, скорее всего, не кратно 200 пикселей по много времени, которая означает, что `phase` аргумент `SKPathEffect.Create1DPath` — анимировано, контейнеров отобразит в действие и из существование.

По этой причине программа сначала вычисляет значение с именем `length` , длина конвейерная лента. Так как конвейерная лента состоит из прямых линий и точками окружности, это простых вычислений. Далее число контейнеров, вычисляется путем деления `length` по 200. Это округляется до ближайшего целого числа, а затем состоит номер из `length`. Результатом является интервалы для целого числа контейнеров. `phase` Аргумент является просто долю.

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

Как и в приведенном выше примере `GetFillPath`, вы увидите, что результаты будут такими же, за исключением цвет. После выполнения `GetFillPath`, `newPath` объект содержит несколько копий сегмента пути, каждая размещенная в там же, где, анимация размещено их во время вызова.

## <a name="hatching-an-area"></a>Штриховки области

[ `SKPathEffect.Create2DLines` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.Create2DLine/p/System.Single/SkiaSharp.SKMatrix/) Метод заполняет область с параллельными линиями, часто называют *штриховки строки*. Метод имеет следующий синтаксис:

```csharp
public static SKPathEffect Create2DLine (Single width, SKMatrix matrix)
```

`width` Аргумент задает толщину обводки линий штриховки. `matrix` Параметр представляет собой сочетание масштабирования и необязательные поворота. Коэффициент масштабирования указывает приращения пикселей, Skia для пространства штриховка. Разделение между строками — коэффициент масштабирования минус `width` аргумент. Если коэффициент масштабирования меньше или равно `width` значение, будут без пробела между штриховой линии и области будет отображаться для заполнения. Укажите то же значение для горизонтального и вертикального масштабирования.

По умолчанию штриховка горизонтальной. Если `matrix` параметр содержит поворота, штриховка поворачиваются по часовой стрелке.

**Штриховки заполнения** странице демонстрируется эффект этого пути. [ `HatchFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/HatchFillPage.cs) Класс определяет три пути эффекты, как поля, первый Горизонтальная штриховка с шириной 3 пикселя коэффициента масштабирования, указывающую, что они размещены через равные интервалы 6 пикселей друг от друга. Разделение строк таким образом, 3 пикселей. Второй путь действует для вертикальной штриховка шириной 6 пикселей на расстоянии друг от друга (поэтому разделение — 18 пикселей), 24 точки и третий — для штриховой диагональный строк 12 расширенный расстоянии друг от друга 36 пикселей друг от друга.

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

Обратите внимание, матрица `Multiply` метод. Так как коэффициенты вертикального и горизонтального масштабирования совпадают, умножении матрицы масштабирования и поворота порядок не имеет значения.

`PaintSurface` Обработчик использует эти три пути эффекты с тремя различными цветами в сочетании с `fillPaint` для заполнения прямоугольник с закругленными углами, подобранные по размеру страницы. `Style` Свойство, заданное для `fillPaint` игнорируется; при `SKPaint` объект включает в себя эффект пути, созданные на основе `SKPathEffect.Create2DLine`, область заполняется в любом случае:

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

Если вы внимательно посмотрите на результаты, вы увидите, что красного и синего штриховка не ограничены точно прямоугольник с закругленными углами. (Это внешне характеристикой базовый код Skia). Если неудовлетворительна, альтернативой будет показан для штриховка по диагонали зеленым цветом: Скругленный прямоугольник используется как контура обрезки и штриховой линии чертятся на всей странице.

`PaintSurface` Обработчик завершается вызов просто вычерчивания Скругленный прямоугольник, чтобы можно было видеть несоответствие с красного и синего штриховка:

[![](effects-images/hatchfill-small.png "Тройной снимок экрана со страницей штриховки заполнения")](effects-images/hatchfill-large.png#lightbox "тройной снимок экрана со страницей штриховки заполнения")

Android экрана выглядит не очень похожи: масштабирование экрана вызвало тонкой красной линией и тонкой пространства для консолидации на первый взгляд широко красной линии и шире.

## <a name="filling-with-a-path"></a>Заполнение контура

[ `SKPathEffect.Create2DPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.Create2DPath/p/SkiaSharp.SKMatrix/SkiaSharp.SKPath/) Позволяет Заливка области с путем, реплицируются по горизонтали и вертикали, фактически мозаичное заполнение области:

```csharp
public static SKPathEffect Create2DPath (SKMatrix matrix, SKPath path)
```

`SKMatrix` Коэффициенты масштабирования указывают горизонтальным и вертикальным пространством реплицированных пути. Но невозможно повернуть путь, используя это `matrix` аргумент, следует ли поворачивать, путь поворот сам путь с помощью `Transform` метода, определенного `SKPath`.

Реплицированные путь обычно выравнивается левую и верхнюю края экрана, а не заполняемая область. Это поведение можно переопределить, предоставив коэффициенты преобразования между 0 и коэффициенты масштабирования, чтобы задать горизонтальное и вертикальное смещение из сторон левую и верхнюю.

**Путь заполняет** странице демонстрируется эффект этого пути. Путь, используемый для заполнения области определяется как поле в [ `PathFileFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathTileFillPage.cs) класса. Горизонтальной и вертикальной координате диапазоном от: от -40 до 40, это означает, что этот путь квадратный 80 пикселей:

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

В `PaintSurface` обработчик, `SKPathEffect.Create2DPath` вызовы задает горизонтальным и вертикальным пространством до 64, вызвать квадратный плитки 80 пикселей перекрываются друг с другом. К счастью путь будет иметь вид листу головоломку, хорошо meshing с соседних плитки:

[![](effects-images/pathtilefill-small.png "Тройной снимок экрана со страницей заполняет путь")](effects-images/pathtilefill-large.png#lightbox "тройной снимок экрана со страницей заполняет путь")

Масштабирование из исходного снимка экрана вызывает некоторые искажения, особенно на экране Android.

Обратите внимание, что эти плитки всегда отображаются целиком и никогда не усекаются. В первых двух снимков экрана не даже очевидно, что заполняемая область является прямоугольник с закругленными углами. Если нужно усечь эти плитки для определенной области, используйте контура обрезки.

Включите параметр `Style` свойство `SKPaint` объект `Stroke`, и вы увидите отдельные элементы мозаики описанные, а не заполнен.

## <a name="rounding-sharp-corners"></a>Округление острого угла

**Округленное Heptagon** программы представлены в [ **три способа, чтобы нарисовать дугу** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md) статьи позволяет кривую точки фигуры семь стороны тангенса дуги. **Другой округленное Heptagon** странице отображаются гораздо проще подходом, использующий эффект пути, созданные на основе [ `SKPathEffect.CreateCorner` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateCorner/p/System.Single/) метод:

```csharp
public static SKPathEffect CreateCorner (Single radius)
```

Несмотря на то, что один аргумент называется `radius` ему необходимо присвоить половину радиус нужного угла. (Это характеристика основного кода Skia).

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

Этот эффект можно использовать со срезом или заполнение на основе `Style` свойство `SKPaint` объекта. Ниже приведен на всех трех платформах:

[![](effects-images/anotherroundedheptagon-small.png "Тройной снимок экрана со страницей другой округленное Heptagon")](effects-images/anotherroundedheptagon-large.png#lightbox "тройной снимок экрана со страницей другой округленное Heptagon")

Вы увидите этого прямоугольника с закругленными heptagon идентична предыдущих программы. При необходимости дополнительные убедить радиус скругления углов действительно является 100, а не 50, указываемой `SKPathEffect.CreateCorner` вызов, вы можете Раскомментировать последняя инструкция в программе, см. в разделе 100-радиус окружности наложены друг на друга в углу.

## <a name="random-jitter"></a>Случайное флуктуации

Иногда безупречной прямых компьютерной графике не довольно требуется, и требуется немного случайность. В этом случае может потребоваться повторить [ `SKPathEffect.CreateDiscrete` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateDiscrete/p/System.Single/System.Single/System.UInt32/) метод:

```csharp
public static SKPathEffect CreateDiscrete (Single segLength, Single deviation, UInt32 seedAssist)
```

Этот эффект пути можно использовать для срезом или заполнения. Строки разделяются на соединенных сегментов — приблизительное длина которых определяется `segLength` — и расширять в другом направлении. Экстент отклонение от исходных строк определяется `deviation`.

Последний аргумент является начальное значение, используемое для формирования псевдослучайных последовательность, используемая для создания эффекта. Эффекта дрожания будет отличаться для различных начальных значений. Аргумент имеет значение по умолчанию равно нулю, это означает, что тот же эффект достигается при каждом запуске программы. Если требуется другой флуктуации всякий раз, когда окрашивание экрана, можно задать начальное значение `Millisecond` свойство `DataTime.Now` значение (например).


**Флуктуаций поэкспериментировать** странице можно поэкспериментировать с различными значениями в срезом прямоугольника:

[![](effects-images/jitterexperiment-small.png "Тройное снимок экрана со страницей Флуктуаций эксперимента")](effects-images/jitterexperiment-large.png#lightbox "Triple screenshot of the JitterExperiment page")

Программе не то такую ошибку очень. [ **JitterExperimentPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml) файл создает два `Slider` элементы и `SKCanvasView`:

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

`PaintSurface` Обработчик в [ **JitterExperimentPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml.cs) файл кода программной части вызывается всякий раз, когда `Slider` изменении значения. Он вызывает `SKPathEffect.CreateDiscrete` с помощью двух `Slider` значения и использует его для вычерчивания прямоугольника:

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

Этот эффект можно использовать для заполнения, а также в этом случае очертания области, заполненные регулируется эти случайные отклонения. **Флуктуаций текст** страницы демонстрируется использование этот эффект пути для отображения текста. Большая часть кода в `PaintSurface` обработчик [ `JitterTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterTextPage.cs) класса, предназначенного для изменения размера и Центрирование текста:

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

Здесь он выполняется в альбомной ориентации на всех трех платформах:

[![](effects-images/jittertext-small.png "Тройное снимок экрана со страницей Флуктуаций текста")](effects-images/jittertext-large.png#lightbox "Triple screenshot of the JitterText page")

## <a name="path-outlining"></a>Структурирование путь

Вы уже видели два примера немного [ `GetFillPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetFillPath/p/SkiaSharp.SKPath/SkiaSharp.SKPath/System.Single/) метод `SKPaint`, который существует в [перегрузки](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetFillPath/p/SkiaSharp.SKPath/SkiaSharp.SKPath/SkiaSharp.SKRect/System.Single/):

```csharp
public Boolean GetFillPath (SKPath src, SKPath dst, Single resScale)

public Boolean GetFillPath (SKPath src, SKPath dst, SKRect cullRect, Single resScale)
```

Требуются только первые два аргумента. Метод обращается к путь ссылается `src` аргумент, изменяет путь данных в зависимости от свойств штриха в `SKPaint` объекта (включая `PathEffect` свойства) и затем записывает результаты в `dst` пути. `resScale` Позволяет понизить точность для создания небольших путь назначения и `cullRect` аргумент можно исключить контуров вне прямоугольника.

Один основное использование этого метода не подразумевает эффекты путь вообще. Если `SKPaint` объект имеет его `Style` свойство `SKPaintStyle.Stroke`и выполняет *не* имеют его `PathEffect` значение, а затем `GetFillPath` создает пути, который представляет *структуры*пути источника как если бы он был нарисован с помощью свойств рисования.

Например если `src` путь отображается в виде простого круга радиуса 500 и `SKPaint` объект задает толщину штриха, 100, то `dst` путь стал двух концентрических окружностей с радиусом 450, а другой с радиусом 550. Этот метод вызывается `GetFillPath` из-за заполнения это `dst` путь совпадает со значением срезом `src` пути. Но можно также выполнить обводку `dst` путь к видна пути.

**Коснитесь, чтобы структуры путь** это показано. `SKCanvasView` И `TapGestureRecognizer` создаются в [ **TapToOutlineThePathPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml) файла. [ **TapToOutlineThePathPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml.cs) файл кода определяет три `SKPaint` объекты, как поля, два для срезом с вычерчивания ширина 100 и 20, а третий для заполнения:

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

Круг заполняется, вычерчивании должным образом:

[![](effects-images/taptooutlinethepathnormal-small.png "Снимок экрана тройной обычной страницы коснитесь для структуры Path")](effects-images/taptooutlinethepathnormal-large.png#lightbox "тройной экрана обычной страницы коснитесь для структуры Path")

При касании экрана, `outlineThePath` равно `true`и `PaintSurface` обработчик создает новый `SKPath` объекта и использует его в качестве целевой путь в вызове `GetFillPath` на `redThickStroke` рисования объекта. Затем заполняется, вычерчивании с этого пути назначения `redThinStroke`, в следующем результате:

[![](effects-images/taptooutlinethepathoutlined-small.png "Тройной снимок экрана со страницей контурные коснитесь для структуры Path")](effects-images/taptooutlinethepathoutlined-large.png#lightbox "тройной снимок экрана со страницей контурные коснитесь для структуры Path")

Два красного круга четко указывают, что исходный путь циклические преобразования в двух контуров циклические.

Этот метод может быть удобно использовать при разработке пути для `SKPathEffect.Create1DPath` метод. Пути, заданные в этих методах всегда заполняются при репликации пути. Если вы не хотите весь путь должен быть заполнен, необходимо определить тщательно контуров.

Например, в **связанная цепочка** примера ссылки были определены последовательность из четырех дуг, каждая пара были основаны на двух радиусы описать области путь должен быть заполнен. Можно заменить код в `LinkedChainPage` класс для немного по-разному.

Во-первых, необходимо переопределить `linkRadius` констант:

```csharp
const float linkRadius = 27.5f;
const float linkThickness = 5;
```

`linkPath` Является только двух дуг, исходя из этого одного radius с требуемым запустить углы и углы поворота:

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

`outlinePath` Объект становится получателя контур `linkPath` при вычерчивании ее с помощью свойств, указанных в `strokePaint`.

Другой пример, при использовании этого метода наступит для пути, используемого `SKPathEffect.Create2DPath` методы.

## <a name="combining-path-effects"></a>Объединяя эффекты путь

Два метода создания окончательного статических `SKPathEffect` , [ `SKPathEffect.CreateSum` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateSum/p/SkiaSharp.SKPathEffect/SkiaSharp.SKPathEffect/) и [ `SKPathEffect.CreateCompose` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateCompose/p/SkiaSharp.SKPathEffect/SkiaSharp.SKPathEffect/):

```csharp
public static SKPathEffect CreateSum (SKPathEffect first, SKPathEffect second)

public static SKPathEffect CreateCompose (SKPathEffect outer, SKPathEffect inner)
```

Оба этих способа объединить два эффекта пути для создания эффекта составного пути. `CreateSum` Метод создает эффект, аналогичный эффекты два пути, применять отдельно, пока `CreateCompose` применяет эффект один путь ( `inner`), а затем применяет `outer` .

Вы уже видели как `GetFillPath` метод `SKPaint` можно преобразовать один путь к другой путь на основе `SKPaint` свойства (включая `PathEffect`), он не должен быть *слишком* загадочным как `SKPaint`объект может выполнить эту операцию дважды с эффектами два пути, указанного в `CreateSum` или `CreateCompose` методы.

Одним из очевидных использования `CreateSum` является определение `SKPaint` объект, который заполняет контур с эффектом один путь и обводки пути с другим эффектом пути. Это показано в **Cats в кадре** пример, который отображает массив cats внутри фрейма Зубчатый ребер:

[![](effects-images/catsinframe-small.png "Тройной снимок экрана со страницей Cats в кадр")](effects-images/catsinframe-large.png#lightbox "тройной снимок экрана со страницей Cats в рамки")

[ `CatsInFramePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CatsInFramePage.cs) Класса начинается с определения нескольких полей. Может распознать первого поля из [ `PathDataCatPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs) класса из [ **данные пути SVG** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md) статьи. Второй путь основано на строки и дуги для шаблона Зубцы кадра.

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

`catPath` Может использоваться в `SKPathEffect.Create2DPath` метод Если `SKPaint` объекта `Style` свойству `Stroke`. Однако если `catPath` используется непосредственно в этой программе, затем весь заголовок cat будет заполнено и ограничителей выбросов даже не будут отображаться. (Попробуйте!) Это необходимо для получения структуры этой папки и использовать эту структуру в `SKPathEffect.Create2DPath` метод.

Конструктор не этого задания. Он сначала применяет два преобразования `catPath` для перемещения (0, 0) пункты центра и масштабировать его размер. `GetFillPath` Получает все структуры контуров в `outlinedCatPath`, и этот объект используется в `SKPathEffect.Create2DPath` вызова. Масштабирование факторы `SKMatrix` значения, немного превышающее горизонтальный и вертикальный размер cat для обеспечения практически буфера между плиток, хотя были обнаружены коэффициенты преобразования производных немного эмпирически так, чтобы полный cat видимым в левый верхний угол рамки:

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

Затем конструктор вызывает `SKPathEffect.Create1DPath` Зубчатый рамки. Обратите внимание, ширину пути — 100 пикселей, но продвижение по — 75 пикселей, чтобы реплицированные путь перекрывается вокруг рамки. Последняя инструкция вызывает конструктор `SKPathEffect.CreateSum` Чтобы объединить результаты двух путь и присвоить результат `SKPaint` объекта.

Эта работа позволяет `PaintSurface` обработчик быть довольно просто. Ему нужно только определить прямоугольник и рисования с помощью `framePaint`:

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

Алгоритмы за эффекты пути всегда вызывать используется срезом или заполнение для отображения, весь путь, что может вызвать некоторые визуальные элементы вне прямоугольника. `ClipRect` До вызова `DrawRect` вызова позволяет визуальных элементов быть значительно более понятным. (Попробуйте без обрезки!)

Это обычно используется `SKPathEffect.CreateCompose` некоторые флуктуации добавляемый другой эффект пути. Определенно можно поэкспериментировать самостоятельно, но ниже приведен пример, немного отличаются:

**Пунктирных линий штриховки** заполняет штриховка, которые являются штриховая эллипса. Большая часть работы в [ `DashedHatchLinesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DashedHatchLinesPage.cs) класса выполняется непосредственно в определения полей. Эти поля определяют эффекта тире и эффекта штриховки. Они определяются как `static` так, как они будут использоваться далее в `SKPathEffect.CreateCompose` вызов в `SKPaint` определение:

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

`PaintSurface` Необходимость содержат только стандартные затраты на плюс один вызов обработчика `DrawOval`:

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

Как вам уже известно, штриховка не точно ограниченные внутренней области и в этом примере они всегда начинаются с влево на всей тире.

[![](effects-images/dashedhatchlines-small.png "Тройной снимок экрана со страницей пунктирных линий штриховки")](effects-images/dashedhatchlines-large.png#lightbox "тройной снимок экрана со страницей пунктирных линий штриховки")

Теперь, когда вы познакомились с эффекты путь, начиная от простых точек и тире необычной сочетания используйте воображения и просмотра, можно создать.



## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
