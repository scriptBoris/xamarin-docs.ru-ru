---
title: Режимы наложения Duff Портер
description: Используйте режимы Портер Duff blend для создания сцены на основе образов источника и назначения.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 57F172F8-BA03-43EC-A215-ED6B78696BB5
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: ebd4db28b2c20bd2b9e1d93e03dd101ebc5da663
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50132062"
---
# <a name="porter-duff-blend-modes"></a>Режимы наложения Duff Портер

Режимы наложения Портер Duff названы Томас Портер и Tom Duff, являющейся разработчиком алгебры компоновки, работая в Lucasfilm. Свой документ [ _цифровых изображений композиции_ ](https://graphics.pixar.com/library/Compositing/paper.pdf) была опубликована в выпуске за июль 1984 _компьютерной графике_, страницы 253 для 259 знаков. Эти режимы смешения крайне важны для композиции, в которой сборка различных изображений составного сцены:

![Пример Портер Duff](porter-duff-images/PorterDuffSample.png "пример Duff Портер")

## <a name="porter-duff-concepts"></a>Основные понятия Duff Портер

Предположим, что прямоугольник коричневом занимает левом и верхнем две трети поверхность отображения:

![Назначение Портер Duff](porter-duff-images/PorterDuffDst.png "Портер Duff назначения")

Эта область называется _назначения_ или иногда _фона_ или _backdrop_.

Требуется нарисовать следующие прямоугольник, который соответствует размеру массива назначения. Прямоугольник является прозрачным за исключением голубовато область, которая занимает вправо и вниз на две трети:

![Источник Портер Duff](porter-duff-images/PorterDuffSrc.png "Портер Duff источника")

Это называется _источника_ или иногда _переднего плана_.

При отображении источник на сервере назначения, вот что нужно:

![Исходный код Портер Duff](porter-duff-images/PorterDuffSrcOver.png "Портер Duff источник над")

Прозрачных точек источника разрешить фона видны сквозь, хотя голубовато исходные точки скрывать фона. Это обычный так, и он упоминается в SkiaSharp как `SKBlendMode.SrcOver`. Значение по умолчанию `BlendMode` свойство при `SKPaint` сначала создается экземпляр объекта.

Тем не менее можно указать режим разных наложения для другой эффект. Если указать `SKBlendMode.DstOver`, а затем в области пересечения исходного и конечного назначения отображается вместо источника:

![Назначения Портер Duff над](porter-duff-images/PorterDuffDstOver.png "Портер Duff назначения через")

`SKBlendMode.DstIn` Blend режиме отображается только область, источника и назначения пересечения с помощью назначения цвета:

![Назначения Портер Duff в](porter-duff-images/PorterDuffDstIn.png "Портер Duff назначения в")

Режим наложения `SKBlendMode.Xor` (исключающее или) вызывает ничего не отображается, перекрытия двух областей:

![Портер Duff исключающего или](porter-duff-images/PorterDuffXor.png "Портер Duff исключающего или")

Цветных прямоугольников источника и назначения эффективно разделить поверхность отображения четыре уникальных областей, которые можно окрасить различными способами, соответствующий наличие прямоугольников источника и назначения:

![Портер Duff](porter-duff-images/PorterDuff.png "Duff Портер")

Прямоугольники верхний правый и левый нижний всегда будут пустыми, так как в исходном и целевом прозрачны в этих областях. Конечный цвет занимает область верхнего левого, таким образом, чтобы область можно окрасить, либо с цветом назначения или вообще не. Аналогичным образом исходного цвета занимает область нижний правый, таким образом, чтобы область будут выделены цветом с цветом источника или вообще не. Пересечение источника в середине и назначения будут выделены цветом с цветом назначения, цвет источника или вообще не.

Общее число комбинаций — 2 (для левом верхнем углу), время 2 (внизу справа) раз 3 (для центра) или 12. Ниже приведены 12 основных режима Портер Duff композиции.

Ближе к концу _цифровых изображений композиции_ (страница 256), Портер и Duff Добавление 13-й режима называется _, а также_ (соответствующий SkiaSharp `SKBlendMode.Plus` член и W3C _светлее_  режим (которого не следует путать с W3C _«Замена светлым»_ режиме.) Это `Plus` режим добавляет исходного и конечного цветов, процесс, который описывается более подробно чуть ниже.

Skia добавляет имеется 14-й режим `Modulate` это очень похоже на `Plus` за исключением того, что при умножении исходного и конечного цветов. Он может рассматриваться как дополнительный режим наложения Duff Портер.

Ниже приведены режимы 14 Duff Портер, как определено в SkiaSharp. В таблице показано, как они цвет каждого из трех областей непустой на схеме выше:

| Режим       | Назначение | Пересечение | Исходный код |
| ---------- |:-----------:|:------------:|:------:|
| `Clear`    |             |              |        |
| `Src`      |             | Исходный код       | X      |
| `Dst`      | X           | Назначение  |        |
| `SrcOver`  | X           | Исходный код       | X      |
| `DstOver`  | X           | Назначение  | X      |
| `SrcIn`    |             | Исходный код       |        |
| `DstIn`    |             | Назначение  |        |
| `SrcOut`   |             |              | X      |
| `DstOut`   | X           |              |        |
| `SrcATop`  | X           | Исходный код       |        |
| `DstATop`  |             | Назначение  | X      |
| `Xor`      | X           |              | X      |
| `Plus`     | X           | Sum          | X      |
| `Modulate` |             | Продукт      |        | 

Эти режимы смешения являются симметричными. Может быть передано источник и назначение и все режимы по-прежнему доступны.

Соглашение об именовании из режимов следует за несколько простых правил:

- **Src** или **перехода на летнее время** сама по себе означает, что только пиксели источника или назначения являются видимыми.
- **Через** суффикс указывает, что отображается на пересечении. Исходных или целевых рисуется «по».
- **В** суффикс означает, что только пересечение окрашивается. Выходные данные ограничено только часть источнике или назначении «в» другой.
- **Out** суффикс означает, что пересечение не окрашивается. Выводится только часть источника или назначения, «out» пересечения.
- **Поверх** суффикс представляет собой объединение **в** и **Out**. Он включает в себя область, в котором источник или назначение «поверх» другого.

Обратите внимание на разницу с `Plus` и `Modulate` режимы. Эти режимы выполнении пиксели исходного и целевого другой тип вычисления. Они описаны более подробно чуть ниже.

**Портер Duff сетки** странице перечислены все 14 режимы на одном экране в виде сетки. В каждом режиме — это отдельный экземпляр `SKCanvasView`. По этой причине класс, производный от `SKCanvasView` с именем `PorterDuffCanvasView`. Статический конструктор создает два точечные рисунки такого же размера, один коричневом прямоугольник, в его левом верхнем области, а другой голубовато прямоугольником.

```csharp
class PorterDuffCanvasView : SKCanvasView
{
    static SKBitmap srcBitmap, dstBitmap;

    static PorterDuffCanvasView()
    {
        dstBitmap = new SKBitmap(300, 300);
        srcBitmap = new SKBitmap(300, 300);

        using (SKPaint paint = new SKPaint())
        {
            using (SKCanvas canvas = new SKCanvas(dstBitmap))
            {
                canvas.Clear();
                paint.Color = new SKColor(0xC0, 0x80, 0x00);
                canvas.DrawRect(new SKRect(0, 0, 200, 200), paint);
            }
            using (SKCanvas canvas = new SKCanvas(srcBitmap))
            {
                canvas.Clear();
                paint.Color = new SKColor(0x00, 0x80, 0xC0);
                canvas.DrawRect(new SKRect(100, 100, 300, 300), paint);
            }
        }
    }
    ···
}
```

Конструктор экземпляра имеет параметр типа `SKBlendMode`. Она сохраняет этот параметр в поле. 

```csharp
class PorterDuffCanvasView : SKCanvasView
{
    ···
    SKBlendMode blendMode;

    public PorterDuffCanvasView(SKBlendMode blendMode)
    {
        this.blendMode = blendMode;
    }

    protected override void OnPaintSurface(SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find largest square that fits
        float rectSize = Math.Min(info.Width, info.Height);
        float x = (info.Width - rectSize) / 2;
        float y = (info.Height - rectSize) / 2;
        SKRect rect = new SKRect(x, y, x + rectSize, y + rectSize);

        // Draw destination bitmap
        canvas.DrawBitmap(dstBitmap, rect);

        // Draw source bitmap
        using (SKPaint paint = new SKPaint())
        {
            paint.BlendMode = blendMode;
            canvas.DrawBitmap(srcBitmap, rect, paint);
        }

        // Draw outline
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Black;
            paint.StrokeWidth = 2;
            rect.Inflate(-1, -1);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

`OnPaintSurface` Переопределение рисует два точечные рисунки. Первый рисуется обычно:

```csharp
canvas.DrawBitmap(dstBitmap, rect);
```

С помощью которого нарисован второй `SKPaint` объекта где `BlendMode` свойство значение аргумента конструктора:

```csharp
using (SKPaint paint = new SKPaint())
{
    paint.BlendMode = blendMode;
    canvas.DrawBitmap(srcBitmap, rect, paint);
}
```

В оставшейся части `OnPaintSurface` переопределение рисует прямоугольник вокруг растровое изображение, чтобы указать их размер.

`PorterDuffGridPage` Класс создает экземпляры Четырнадцать `PorterDurffCanvasView`, один для каждого члена `blendModes` массива. Порядок `SKBlendModes` элементов в массиве немного отличается от имени таблицы для расположения аналогичные режимы рядом друг с другом. 14 экземпляров `PorterDuffCanvasView` упорядочены с метками в `Grid`:

```csharp
public class PorterDuffGridPage : ContentPage
{
    public PorterDuffGridPage()
    {
        Title = "Porter-Duff Grid";

        SKBlendMode[] blendModes =
        {
            SKBlendMode.Src, SKBlendMode.Dst, SKBlendMode.SrcOver, SKBlendMode.DstOver,
            SKBlendMode.SrcIn, SKBlendMode.DstIn, SKBlendMode.SrcOut, SKBlendMode.DstOut,
            SKBlendMode.SrcATop, SKBlendMode.DstATop, SKBlendMode.Xor, SKBlendMode.Plus,
            SKBlendMode.Modulate, SKBlendMode.Clear
        };

        Grid grid = new Grid
        {
            Margin = new Thickness(5)
        };

        for (int row = 0; row < 4; row++)
        {
            grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Auto });
            grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Star });
        }

        for (int col = 0; col < 3; col++)
        {
            grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Star });
        }

        for (int i = 0; i < blendModes.Length; i++)
        {
            SKBlendMode blendMode = blendModes[i];
            int row = 2 * (i / 4);
            int col = i % 4;

            Label label = new Label
            {
                Text = blendMode.ToString(),
                HorizontalTextAlignment = TextAlignment.Center
            };
            Grid.SetRow(label, row);
            Grid.SetColumn(label, col);
            grid.Children.Add(label);

            PorterDuffCanvasView canvasView = new PorterDuffCanvasView(blendMode);

            Grid.SetRow(canvasView, row + 1);
            Grid.SetColumn(canvasView, col);
            grid.Children.Add(canvasView);
        }

        Content = grid;
    }
}
```

Ниже приведен результат.

[![Сетка Портер Duff](porter-duff-images/PorterDuffGrid.png "Портер Duff сетки")](porter-duff-images/PorterDuffGrid-Large.png#lightbox)

Необходимо убедиться, что прозрачность крайне важны для правильной работы режимов смешения Портер Duff. `PorterDuffCanvasView` Класс содержит всего три вызова `Canvas.Clear` метод. Все из них используют метод без параметров, который задает все пиксели прозрачным:

```csharp
canvas.Clear();
```

Попробуйте изменении любого из этих вызовов, таким образом, пиксели непрозрачный белый.

```csharp
canvas.Clear(SKColors.White);
```

После этого изменения некоторые из режимов смешения будет казаться, что для работы, а другие нет. Если задать фон исходного растрового изображения на белый, а затем `SrcOver` режиме не работает, так как в исходное растровое изображение, чтобы разрешить назначение видны сквозь нет прозрачных точек. Если задать фон точечный рисунок назначения или холста, чтобы белого, затем `DstOver` не работает, так как назначение не имеет любой прозрачных точек.

Возможно, есть возможность замены точечных рисунков в **Портер Duff сетки** страницы с простой `DrawRect` вызовы. Который будет работать для целевого прямоугольника, но не для исходного прямоугольника. Исходный прямоугольник должен охватывать не только области сине цветные. Исходный прямоугольник должен включать прозрачной области, соответствующий цветная область назначения. Только после этого они будут blend режимы работы.

## <a name="using-mattes-with-porter-duff"></a>Использование при задании с Duff Портер

**Упираетесь в стену композиции** страницы является примером задачи классической композиции: изображения должен быть составлена из нескольких частей, включая растрового изображения на фоне, который необходимо удалить. Вот **SeatedMonkey.jpg** растровое изображение со проблемных фоном:

![Сидели Monkey](porter-duff-images/SeatedMonkey.jpg "сидели Monkey")

В процессе подготовки к композиции, соответствующий _матовой_ был создан, это другой точечного рисунка, который является черной, где требуется для изображения и прозрачно, в противном случае. Этот файл называется **SeatedMonkeyMatte.png** и в том числе ресурсы в **мультимедиа** папку в [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) образца :

![Сидели матовой Monkey](porter-duff-images/SeatedMonkeyMatte.png "сидели матовой Monkey")

Это _не_ продукте созданный подложки. Лучше всего подложки должна содержать частично прозрачных точек вокруг области черных пикселей, и этот матовой — нет.

Файл XAML для **Упираетесь в стену композиции** создает страницу `SKCanvasView` и `Button` , проводит пользователя через процесс создания окончательного образа:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.BrickWallCompositingPage"
             Title="Brick-Wall Compositing">

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Button Text="Show sitting monkey"
                HorizontalOptions="Center"
                Margin="0, 10"
                Clicked="OnButtonClicked" />

    </StackLayout>
</ContentPage>
```

Файл с выделенным кодом загружает два точечные рисунки, которые требуются и обрабатывает `Clicked` событие `Button`. Для каждого `Button` нажмите кнопку, `step` поле увеличивается и запускается новый `Text` задано для `Button`. Когда `step` достигает 5, он снова установить значение 0:

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    SKBitmap monkeyBitmap = BitmapExtensions.LoadBitmapResource(
        typeof(BrickWallCompositingPage), 
        "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    SKBitmap matteBitmap = BitmapExtensions.LoadBitmapResource(
        typeof(BrickWallCompositingPage), 
        "SkiaSharpFormsDemos.Media.SeatedMonkeyMatte.png");

    int step = 0;

    public BrickWallCompositingPage ()
    {
        InitializeComponent ();
    }

    void OnButtonClicked(object sender, EventArgs args)
    {
        Button btn = (Button)sender;
        step = (step + 1) % 5;

        switch (step)
        {
            case 0: btn.Text = "Show sitting monkey"; break;
            case 1: btn.Text = "Draw matte with DstIn"; break;
            case 2: btn.Text = "Draw sidewalk with DstOver"; break;
            case 3: btn.Text = "Draw brick wall with DstOver"; break;
            case 4: btn.Text = "Reset"; break;
        }

        canvasView.InvalidateSurface();
    }
    
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        ···
    }
}
```

При первом запуске программы, ничто не является видимым, за исключением `Button`:

[![Упираетесь в стену независимую 0](porter-duff-images/BrickWallCompositing0.png "Упираетесь в стену независимую 0")](porter-duff-images/BrickWallCompositing0-Large.png#lightbox)

Нажав клавишу `Button` раза возникает `step` увеличивается на 1 и `PaintSurface` обработчик теперь отображает **SeatedMonkey.jpg**:

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        float x = (info.Width - monkeyBitmap.Width) / 2;
        float y = info.Height - monkeyBitmap.Height;

        // Draw monkey bitmap
        if (step >= 1)
        {
            canvas.DrawBitmap(monkeyBitmap, x, y);
        }
        ···
    }
}
```

Существует не `SKPaint` объекта и, следовательно режим без наложения. Точечный рисунок отображается в нижней части экрана:

[![Упираетесь в стену независимую 1](porter-duff-images/BrickWallCompositing1.png "Упираетесь в стену независимую 1")](porter-duff-images/BrickWallCompositing1-Large.png#lightbox)

Нажмите клавишу `Button` еще раз и `step` с шагом 2. Это наиболее важную операцию отображения **SeatedMonkeyMatte.png** файла:

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        // Draw matte to exclude monkey's surroundings
        if (step >= 2)
        {
            using (SKPaint paint = new SKPaint())
            {
                paint.BlendMode = SKBlendMode.DstIn;
                canvas.DrawBitmap(matteBitmap, x, y, paint);
            }
        }
        ···
    }
}
```

Режим blend — `SKBlendMode.DstIn`, что означает, что назначение будет сохранен в области, соответствующие непрозрачные области источника. В оставшейся части прямоугольник назначения, соответствующего исходного растрового изображения становится прозрачной:

[![Упираетесь в стену независимую 2](porter-duff-images/BrickWallCompositing2.png "Упираетесь в стену независимую 2")](porter-duff-images/BrickWallCompositing2-Large.png#lightbox)

Фон был удален. 

Следующим шагом является рисование прямоугольника, похожий на тротуара, на тебя monkey. Внешний вид этого тротуара основан на сочетание двух шейдеры: шейдера сплошным цветом и шейдера шума Перлина:

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        const float sidewalkHeight = 80;
        SKRect rect = new SKRect(info.Rect.Left, info.Rect.Bottom - sidewalkHeight,
                                 info.Rect.Right, info.Rect.Bottom);

        // Draw gravel sidewalk for monkey to sit on
        if (step >= 3)
        {
            using (SKPaint paint = new SKPaint())
            {
                paint.Shader = SKShader.CreateCompose(
                                    SKShader.CreateColor(SKColors.SandyBrown),
                                    SKShader.CreatePerlinNoiseTurbulence(0.1f, 0.3f, 1, 9));

                paint.BlendMode = SKBlendMode.DstOver;
                canvas.DrawRect(rect, paint);
            }
        }
        ···
    }
}
```

Так как этот тротуара должны проходить за monkey, режим blend — `DstOver`. Назначение появляется, только когда фон будет прозрачным:

[![Упираетесь в стену независимую 3](porter-duff-images/BrickWallCompositing3.png "Упираетесь в стену независимую 3")](porter-duff-images/BrickWallCompositing3-Large.png#lightbox)

Последним шагом является добавление упираетесь в стену. Программа использует доступные плитку точечного рисунка упираетесь в стену как статическое свойство `BrickWallTile` в `AlgorithmicBrickWallPage` класса. Добавляется преобразованию `SKShader.CreateBitmap` вызова необходимо сдвинуть плитки, таким образом, а нижняя строка — это полный Плитка:

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        // Draw bitmap tiled brick wall behind monkey
        if (step >= 4)
        {
            using (SKPaint paint = new SKPaint())
            {
                SKBitmap bitmap = AlgorithmicBrickWallPage.BrickWallTile;
                float yAdjust = (info.Height - sidewalkHeight) % bitmap.Height;

                paint.Shader = SKShader.CreateBitmap(bitmap,
                                                     SKShaderTileMode.Repeat,
                                                     SKShaderTileMode.Repeat,
                                                     SKMatrix.MakeTranslation(0, yAdjust));
                paint.BlendMode = SKBlendMode.DstOver;
                canvas.DrawRect(info.Rect, paint);
            }
        }
    }
}
```

Для удобства `DrawRect` вызовов будет отображен этот шейдер весь холст, но `DstOver` режим ограничивает выходные данные только в области холста, по-прежнему прозрачное:

[![Упираетесь в стену независимую 4](porter-duff-images/BrickWallCompositing4.png "Упираетесь в стену независимую 4")](porter-duff-images/BrickWallCompositing4-Large.png#lightbox)

Очевидно, что существуют другие способы создания этой сценой. Его можно построить и запуск в фоновом режиме и выполняется на переднем плане. Но использование режимов blend обеспечивает большую гибкость. В частности использование подложки позволяет фона растрового изображения должны быть исключены из составных сцены.

Как было указано в статье [отсеченные области с помощью путей](../../curves/clipping.md), `SKCanvas` класс определяет три вида обрезки, соответствующий [ `ClipRect` ](xref:SkiaSharp.SKCanvas.ClipRect*), [ `ClipPath` ](xref:SkiaSharp.SKCanvas.ClipPath*), и [ `ClipRegion` ](xref:SkiaSharp.SKCanvas.ClipRegion*) методы. Режимы наложения Портер Duff добавьте еще один тип обрезки, что позволяет ограничить изображения для все, что можно рисовать, включая точечные рисунки. Матовый, используемых в **Упираетесь в стену композиции** фактически определяет области отсечения.

## <a name="gradient-transparency-and-transitions"></a>Градиента прозрачности и переходы

Примеры Портер-Duff blend режимы, показанный ранее в этой статье все использовали образы, которые состоял из непрозрачными и прозрачных точек, но не частично прозрачных точек. Для этих пикселей, а также определения функций режим наложения. В следующей таблице приведен более формальное определение режимов смешения Duff Портер, представление, в Skia [ **ссылку SkBlendMode**](https://skia.org/user/api/SkBlendMode_Reference). (Поскольку **SkBlendMode ссылка** является ссылкой на Skia, используется синтаксис C++.)

По существу красного, зеленого, синего и альфа-компоненты каждого пикселя преобразуются из байтов в числа с плавающей запятой в диапазоне от 0 до 1. Для альфа-канала 0 — полностью прозрачное, а 1 — полностью непрозрачный

В следующей таблице используются следующие сокращения:

- **DA** альфа-канал назначения
- **Контроллер домена** является местом назначения цвета RGB
- **Sa** является исходный альфа-канал
- **SC** является источником цвета RGB

Цвета RGB предварительно умноженное альфа-значение. Например если **Sc** представляет чистые red, но **Sa** равно 0x80, является цвета RGB **(0x80, 0, 0)**. Если **Sa** равно 0, то все компоненты RGB также равны нулю.

Результат отображается в квадратных скобках с альфа-канала и содержать запятую цвета RGB: **[альфа-канал, цвет]**. Цвета Расчет выполняется отдельно для красного, зеленого и синего компонентов:

| Режим       | Операция |
| ---------- | --------- |
| `Clear`    | [0, 0]    |
| `Src`      | [Sa, Sc]  |
| `Dst`      | [Da, контроллер домена]  |
| `SrcOver`  | [Sa + Da· (1 — Sa), Sc + Dc· (1 — Sa) | 
| `DstOver`  | [Da + Sa· (1 — Да), Dc + Sc· (1 — Da) |
| `SrcIn`    | [Sa· DA Sc· DA] |
| `DstIn`    | [Da· SA, Dc· SA] |
| `SrcOut`   | [Sa· (1 — Да), Sc· (1 — Da)] |
| `DstOut`   | [Da· (1 — Sa), Dc· (1 — Sa)] |
| `SrcATop`  | [Da, Sc· DA + Dc· (1 — Sa)] |
| `DstATop`  | [Sa, Dc· SA + Sc· (1 — Da)] |
| `Xor`      | [Sa + Da — 2· SA· DA Sc· (1 — Da) + Dc· (1 — Sa)] |
| `Plus`     | [Sa + Da, Sc + Dc] |
| `Modulate` | [Sa· DA Sc· Контроллер домена] | 

Эти операции удобны для анализа, когда **Da** и **Sa** являются 0 или 1. Например, значение по умолчанию `SrcOver` режим, если **Sa** равна 0, то **Sc** является также 0, а результат — **[Da, контроллер домена]**, альфа-канал назначения и цвет. Если **Sa** имеет значение 1, то результат тоже **[Sa, Sc]**, цвет и альфа-версия источника или **[1, Sc]**.

`Plus` И `Modulate` режимы немного отличаются от других тем, что новые цвета может быть результатом сочетания источника и назначения. `Plus` Режим может интерпретироваться с помощью байтов компоненты или компоненты с плавающей запятой. В **сетки Портер Duff** страницы, приведенной выше, является цветом назначения **(0xC0, 0x80, 0x00)** и цвет источника **(0x00, 0x80, 0xC0)**. Каждая пара компонентов добавляется, но сумма присваивается в 0xFF. Результатом является цвет **(0xC0, 0xFF, 0xC0)**. Это показано на пересечении цвет.

Для `Modulate` режиме, необходимо преобразовать к с плавающей запятой значения RGB. Конечный цвет **(0,75, 0,5, 0)** , а источник **(0, 0,5, 0,75)**. RGB компонентами являются каждого умножением и в результате **(0, 0,25, 0)**. Это показано на пересечении цвет **Портер Duff сетки** страницы для этого режима.

**Портер Duff прозрачности** страница позволяет изучить принципы работы режимов смешения Портер Duff на графические объекты, которые частично прозрачных точек. Файл XAML включает `Picker` с режимами Портер Duff:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaviews="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.PorterDuffTransparencyPage"
             Title="Porter-Duff Transparency">

    <StackLayout>
        <skiaviews:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="blendModePicker"
                Title="Blend Mode"
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKBlendMode}">
                    <x:Static Member="skia:SKBlendMode.Clear" />
                    <x:Static Member="skia:SKBlendMode.Src" />
                    <x:Static Member="skia:SKBlendMode.Dst" />
                    <x:Static Member="skia:SKBlendMode.SrcOver" />
                    <x:Static Member="skia:SKBlendMode.DstOver" />
                    <x:Static Member="skia:SKBlendMode.SrcIn" />
                    <x:Static Member="skia:SKBlendMode.DstIn" />
                    <x:Static Member="skia:SKBlendMode.SrcOut" />
                    <x:Static Member="skia:SKBlendMode.DstOut" />
                    <x:Static Member="skia:SKBlendMode.SrcATop" />
                    <x:Static Member="skia:SKBlendMode.DstATop" />
                    <x:Static Member="skia:SKBlendMode.Xor" />
                    <x:Static Member="skia:SKBlendMode.Plus" />
                    <x:Static Member="skia:SKBlendMode.Modulate" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                3
            </Picker.SelectedIndex>
        </Picker>
    </StackLayout>
</ContentPage>
```

Файл с выделенным кодом заполняет два прямоугольника одного размера с помощью линейного градиента. Градиент назначения представляет собой из верхней прямо в нижнем левом углу. Коричневом в правом верхнем углу, но затем к центру начинается постепенное исчезновение прозрачным и прозрачно в левом нижнем углу. 

Исходного прямоугольника имеет градиент от левого верхнего угла до правого нижнего угла. Верхнего левого угла голубовато, но снова выцветает до прозрачным и прозрачно в правом нижнем углу. 

```csharp
public partial class PorterDuffTransparencyPage : ContentPage
{
    public PorterDuffTransparencyPage()
    {
        InitializeComponent();
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

        // Make square display rectangle smaller than canvas
        float size = 0.9f * Math.Min(info.Width, info.Height);
        float x = (info.Width - size) / 2;
        float y = (info.Height - size) / 2;
        SKRect rect = new SKRect(x, y, x + size, y + size);

        using (SKPaint paint = new SKPaint())
        {
            // Draw destination
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(rect.Right, rect.Top),
                                new SKPoint(rect.Left, rect.Bottom),
                                new SKColor[] { new SKColor(0xC0, 0x80, 0x00),
                                                new SKColor(0xC0, 0x80, 0x00, 0) },
                                new float[] { 0.4f, 0.6f },
                                SKShaderTileMode.Clamp);

            canvas.DrawRect(rect, paint);

            // Draw source
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(rect.Left, rect.Top),
                                new SKPoint(rect.Right, rect.Bottom),
                                new SKColor[] { new SKColor(0x00, 0x80, 0xC0), 
                                                new SKColor(0x00, 0x80, 0xC0, 0) },
                                new float[] { 0.4f, 0.6f },
                                SKShaderTileMode.Clamp);

            // Get the blend mode from the picker
            paint.BlendMode = blendModePicker.SelectedIndex == -1 ? 0 :
                                    (SKBlendMode)blendModePicker.SelectedItem;

            canvas.DrawRect(rect, paint);

            // Stroke surrounding rectangle
            paint.Shader = null;
            paint.BlendMode = SKBlendMode.SrcOver;
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Black;
            paint.StrokeWidth = 3;
            canvas.DrawRect(rect, paint);
        }
    }
}
```

В этой программе демонстрируется использование режимов смешения Портер Duff со графических объектов, отличных от растровых изображений. Тем не менее источник должен включать прозрачной области. Это обусловлено тем, в данном случае градиентные заливки прямоугольника, но часть градиента является прозрачным.

Ниже приведены три примера:

[![Прозрачность Портер Duff](porter-duff-images/PorterDuffTransparency.png "Портер Duff прозрачности")](porter-duff-images/PorterDuffTransparency-Large.png#lightbox)

Конфигурация источника и назначения является очень похожа на схемы, показанный на странице 255 исходного Портер-Duff [ _цифровых изображений композиции_ ](https://graphics.pixar.com/library/Compositing/paper.pdf) бумаги, но эта страница показывает, что режимы наложения хорошо работающие для областей частичную прозрачность.

Для некоторых различные эффекты можно использовать прозрачный градиентов. Один из вариантов использование масок, аналогичный метод, описанный в [ **Радиальный градиент для маскирования** ](../shaders/circular-gradients.md#radial-gradients-for-masking) раздел **SkiaSharp циклическая градиенты страницы**. Значительная часть **маска композиции** страница аналогична этой более ранней программы. Он загружает ресурса точечного рисунка и определяет прямоугольник, в котором для его отображения. Радиальный градиент будет создан на основе предварительно определенный центра и радиус:

```csharp
public class CompositingMaskPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
        typeof(CompositingMaskPage),
        "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

    static readonly SKPoint CENTER = new SKPoint(180, 300);
    static readonly float RADIUS = 120;

    public CompositingMaskPage ()
    {
        Title = "Compositing Mask";

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
                                new SKColor[] { SKColors.Black,
                                                SKColors.Transparent },
                                new float[] { 0.6f, 1 },
                                SKShaderTileMode.Clamp);

            paint.BlendMode = SKBlendMode.DstIn;

            // Display rectangle using that gradient and blend mode
            canvas.DrawRect(rect, paint);
        }

        canvas.DrawColor(SKColors.Pink, SKBlendMode.DstOver);
    }
}
```

С этой программой разница в том что начинается с черный цвет в центре градиента и заканчивается прозрачности. Он отображается в точечный рисунок с режимом blend `DstIn`, который показывает назначение только в областях источника, которые не являются прозрачными.

После `DrawRect` вызова, всю поверхность элемента canvas является прозрачным за исключением круга, определенного радиального градиента. Последний вызов:

```csharp
canvas.DrawColor(SKColors.Pink, SKBlendMode.DstOver);
```

Прозрачные области холста розовым:

[![Маска композиции](porter-duff-images/CompositingMask.png "композиции маски")](porter-duff-images/CompositingMask-Large.png#lightbox)

Также можно Портер Duff режимы и частично прозрачный градиентов для переходов из одного образа на другой. **Градиента переходы** страница содержит `Slider` чтобы указать уровень ход выполнения при переходе от 0 до 1 и `Picker` Выбор типа перехода:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.GradientTransitionsPage"
             Title="Gradient Transitions">
    
    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="progressSlider"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference progressSlider},
                              Path=Value,
                              StringFormat='Progress = {0:F2}'}"
               HorizontalTextAlignment="Center" />

        <Picker x:Name="transitionPicker" 
                Title="Transition" 
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged" />
        
    </StackLayout>
</ContentPage>
```

Файл с выделенным кодом загружает два ресурса точечного рисунка для демонстрации перехода. Это же двух изображений, используемых в **исчезают точечного рисунка** ранее в этой статье. Код также определяет перечисление с тремя элементами, соответствующие трем типам градиенты &mdash; линейной, радиальной и очистки. Эти значения будут загружены в `Picker`:

```csharp
public partial class GradientTransitionsPage : ContentPage
{
    SKBitmap bitmap1 = BitmapExtensions.LoadBitmapResource(
        typeof(GradientTransitionsPage),
        "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    SKBitmap bitmap2 = BitmapExtensions.LoadBitmapResource(
        typeof(GradientTransitionsPage),
        "SkiaSharpFormsDemos.Media.FacePalm.jpg");

    enum TransitionMode
    {
        Linear,
        Radial,
        Sweep
    };

    public GradientTransitionsPage ()
    {
        InitializeComponent ();

        foreach (TransitionMode mode in Enum.GetValues(typeof(TransitionMode)))
        {
            transitionPicker.Items.Add(mode.ToString());
        }

        transitionPicker.SelectedIndex = 0;
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }
    ···
}
```

Создает файл с выделенным кодом трех `SKPaint` объектов. `paint0` Объекта не использует режим наложения. Этот объект paint используется для рисования прямоугольника с градиент, идущий от черного к прозрачным, как указано в `colors` массива. `positions` Массива основан на положение `Slider`, но немного изменены. Если `Slider` в его минимума и максимума, `progress` значениями являются 0 или 1, и один из двух растровые изображения должен быть полностью отображены. `positions` Массива должны быть настроены с учетом этих значений.

Если `progress` значение равно 0, то `positions` — значения -0.1, а значение 0. Первое значение равно 0, это означает, что градиент представляет собой черный только с 0 и прозрачный настроит SkiaSharp в противном случае. Когда `progress` равен 0,5, то массив содержит значения 0,45 и 0,55. Градиента черный от 0 к 0,45, а затем переходит к прозрачности, а полностью прозрачный 0,55 1. Когда `progress` -1, `positions` массив — 1 и 1.1, которое означает, что градиент представляет собой черный от 0 до 1.

`colors` И `position` массивы используются в трех методов `SKShader` , Создание градиента. Только один из этих шейдеры создается на основе `Picker` выбора: 

```csharp
public partial class GradientTransitionsPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Assume both bitmaps are square for display rectangle
        float size = Math.Min(info.Width, info.Height);
        SKRect rect = SKRect.Create(size, size);
        float x = (info.Width - size) / 2;
        float y = (info.Height - size) / 2;
        rect.Offset(x, y);

        using (SKPaint paint0 = new SKPaint())
        using (SKPaint paint1 = new SKPaint())
        using (SKPaint paint2 = new SKPaint())
        {
            SKColor[] colors = new SKColor[] { SKColors.Black,
                                               SKColors.Transparent };

            float progress = (float)progressSlider.Value;

            float[] positions = new float[]{ 1.1f * progress - 0.1f,
                                             1.1f * progress };

            switch ((TransitionMode)transitionPicker.SelectedIndex)
            {
                case TransitionMode.Linear:
                    paint0.Shader = SKShader.CreateLinearGradient(
                                        new SKPoint(rect.Left, 0),
                                        new SKPoint(rect.Right, 0),
                                        colors,
                                        positions,
                                        SKShaderTileMode.Clamp);
                    break;

                case TransitionMode.Radial:
                    paint0.Shader = SKShader.CreateRadialGradient(
                                        new SKPoint(rect.MidX, rect.MidY),
                                        (float)Math.Sqrt(Math.Pow(rect.Width / 2, 2) +
                                                         Math.Pow(rect.Height / 2, 2)),
                                        colors,
                                        positions,
                                        SKShaderTileMode.Clamp);
                    break;

                case TransitionMode.Sweep:
                    paint0.Shader = SKShader.CreateSweepGradient(
                                        new SKPoint(rect.MidX, rect.MidY),
                                        colors,
                                        positions);
                    break;
            }

            canvas.DrawRect(rect, paint0);

            paint1.BlendMode = SKBlendMode.SrcOut;
            canvas.DrawBitmap(bitmap1, rect, paint1);

            paint2.BlendMode = SKBlendMode.DstOver;
            canvas.DrawBitmap(bitmap2, rect, paint2);
        }
    }
}
```

Этот градиент отображается в прямоугольнике без режима наложения. После этого `DrawRect` вызов, холста просто содержит градиент от черного к прозрачности. Выше возрастает объем черный `Slider` значения.

В окончательной четыре операторы `PaintSurface` обработчик, отображаются два точечные рисунки. `SrcOut` Blend режим означает, что первый точечный рисунок отображается только в прозрачные области фона. `DstOver` Режим для второго растрового изображения означает, что второй точечный рисунок отображается только в этих областях, где первый растровое изображение не отображается.

На следующих снимках экрана показано три типа различных переходов, каждый на отметке 50%:

[![Переходы градиента](porter-duff-images/GradientTransitions.png "градиента переходы")](porter-duff-images/GradientTransitions-Large.png#lightbox)

## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)