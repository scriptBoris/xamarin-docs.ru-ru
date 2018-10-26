---
title: Неаффинные преобразования
description: В этой статье объясняется, как создать перспективы и конический эффекты с третьего столбца матрицы преобразования, а также демонстрирует это с помощью примера кода.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 785F4D13-7430-492E-B24E-3B45C560E9F1
author: davidbritch
ms.author: dabritch
ms.date: 04/14/2017
ms.openlocfilehash: da5306ed9c301a7229d2fc5e913a4217e844bbba
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116151"
---
# <a name="non-affine-transforms"></a>Неаффинные преобразования

_Создание перспективы и конический эффекты с помощью третий столбец матрицы преобразования_

Перевода, масштабирования, поворота и наклона классифицируются как *аффинные* преобразования. Аффинные преобразования сохраняют параллельные линии. Если две строки параллельных до преобразования, остаются параллельных после преобразования. Прямоугольники, всегда преобразуются в parallelograms.

Тем не менее SkiaSharp способен также неаффинные преобразования, которые имеют возможность преобразования прямоугольник в любой выпуклая четырехугольника осуществляется плавный переход:

![](non-affine-images/nonaffinetransformexample.png "Битовая карта преобразуются в выпуклая четырехугольника осуществляется плавный переход")

Выпуклая четырехугольника осуществляется плавный переход — это фигура, четырехсторонняя, с помощью внутренних углов, всегда меньше, чем 180 градусов и частях, которые не пересекаются.

Аффинные не преобразует результат, когда третья строка матрицы преобразования имеет значение для значений, отличных от 0, 0 и 1. Полный `SKMatrix` умножение является:

<pre>
              │ ScaleX  SkewY   Persp0 │
| x  y  1 | × │ SkewX   ScaleY  Persp1 │ = | x'  y'  z' |
              │ TransX  TransY  Persp2 │
</pre>

Приведены итоговые Преобразование формул.

x' = ScaleX·x + SkewX·y + TransX

y "= SkewY·x + ScaleY·y + TransY

z "= Persp0·x + Persp1·y + Persp2

Основным правилом с помощью матрицу 3 x 3 для двумерных преобразований является то, что все, что остается в плоскости Z равно 1. Если не `Persp0` и `Persp1` равны 0, и `Persp2` имеет значение 1, то преобразование перемещено координаты Z, плоскость.

Чтобы восстановить это двумерного преобразования, координаты необходимо переместить обратно к этой плоскости. Еще один шаг является обязательным. X ", y", и z «значения необходимо разделить на z»:

x» = x "/ z"

y» = y "/ z"

z» = z "/ z" = 1

Они известны как *гомогенными координатами* и они были разработаны с по имени математика Джорджа Ferdinand Möbius августа, гораздо лучше известные для его топологическом странной, панель Möbius.

Если z "равно 0, результаты деления в бесконечную координатах. Фактически один из его Möbius создавались для гомогенными координатами была возможность бесконечный значений ограниченная чисел.

Для отображения графических объектов, тем не менее, вы хотите избежать отрисовки, что-то с координатами, преобразующих бесконечные значения. Эти координаты не будут отображены. Все, что вблизи эти координаты будет очень большой и визуально скорее всего, не согласовано.

В этой формуле требуется значение z "становится нулем:

z "= Persp0·x + Persp1·y + Persp2

Следовательно эти значения имеют некоторые практические ограничения. 

`Persp2` Ячейки может быть ноль или отлично от нуля. Если `Persp2` равно нулю, то z "равно нулю, точки (0, 0), а это обычно не желательно потому, что этого достаточно распространены в двухмерной графики. Если `Persp2` не равно нулю, то не происходит потери универсальности, если `Persp2` составляет 1. Например, если выяснится, что `Persp2` должно быть 5, то можно просто разделить всех ячеек в матрице, 5, что делает `Persp2` равным 1, а результат будет таким же.

По этим причинам `Persp2` часто составит 1, это же значение в единичной матрицей.

Как правило `Persp0` и `Persp1` — небольшого числа. Предположим, например, приступить к работе с единичной матрицей, но набор `Persp0` 0.01:

<pre>
| 1  0   0.01 |
| 0  1    0   |
| 0  0    1   |
</pre>

Преобразование формул:

x' = x / (0.01·x + 1)

y "= y / (0.01·x + 1)

Теперь можно используйте это преобразование для подготовки к просмотру 100 пикселей квадрат, расположенный в начале координат. Вот, как преобразуются планеты.

(0, 0) "→" (0, 0)

(0, 100) "→" (0, 100)

(100, 0) "→" ("50", "0")

(100, 100) → (50, 50)

При значении x, 100, а затем z "знаменатель равен 2, поэтому координаты x и y являются фактически вдвое. Справа от поля становится короче, чем левой стороны:

![](non-affine-images/nonaffinetransform.png "Бокс, распространяется неаффинных преобразования")

`Persp` Часть этих имен ячейки ссылается на «перспективы», так как ракурс предполагает, что поле теперь наклона с правой стороны от средства просмотра.

**Перспективы "Тест"** страницы позволяет экспериментировать со значениями `Persp0` и `Pers1` чтобы почувствовать, как они работают. Приемлемые значения этих ячеек матрицы так малы, `Slider` в универсальной платформы Windows не может правильно их обработки. В соответствии с универсальной платформы Windows проблемы, два `Slider` элементов в [ **TestPerspective.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TestPerspectivePage.xaml) должны инициализироваться в диапазоне от – 1, 1:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Transforms.TestPerspectivePage"
             Title="Test Perpsective">
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
                    <Setter Property="Minimum" Value="-1" />
                    <Setter Property="Maximum" Value="1" />
                    <Setter Property="Margin" Value="20, 0" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Slider x:Name="persp0Slider"
                Grid.Row="0"
                ValueChanged="OnPersp0SliderValueChanged" />

        <Label x:Name="persp0Label"
               Text="Persp0 = 0.0000"
               Grid.Row="1" />

        <Slider x:Name="persp1Slider"
                Grid.Row="2"
                ValueChanged="OnPersp1SliderValueChanged" />

        <Label x:Name="persp1Label"
               Text="Persp1 = 0.0000"
               Grid.Row="3" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="4"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

Обработчики событий для ползунков в [ `TestPerspectivePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TestPerspectivePage.xaml.cs) файл с выделенным кодом деления значения 100, чтобы они допустимы значения от –0.01 и 0,01. Кроме того конструктор загружает в точечном рисунке:

```csharp
public partial class TestPerspectivePage : ContentPage
{
    SKBitmap bitmap;

    public TestPerspectivePage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }

    void OnPersp0SliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        Slider slider = (Slider)sender;
        persp0Label.Text = String.Format("Persp0 = {0:F4}", slider.Value / 100);
        canvasView.InvalidateSurface();
    }

    void OnPersp1SliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        Slider slider = (Slider)sender;
        persp1Label.Text = String.Format("Persp1 = {0:F4}", slider.Value / 100);
        canvasView.InvalidateSurface();
    }
    ...
}
```

`PaintSurface` Вычисляет обработчик `SKMatrix` значение с именем `perspectiveMatrix` на основе значений этих двух ползунков разделено на 100. Он объединяется с двумя перевод преобразований, которые добавляют центре это преобразование в центре растрового изображения:

```csharp
public partial class TestPerspectivePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Calculate perspective matrix
        SKMatrix perspectiveMatrix = SKMatrix.MakeIdentity();
        perspectiveMatrix.Persp0 = (float)persp0Slider.Value / 100;
        perspectiveMatrix.Persp1 = (float)persp1Slider.Value / 100;

        // Center of screen
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        SKMatrix matrix = SKMatrix.MakeTranslation(-xCenter, -yCenter);
        SKMatrix.PostConcat(ref matrix, perspectiveMatrix);
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(xCenter, yCenter));

        // Coordinates to center bitmap on canvas
        float x = xCenter - bitmap.Width / 2;
        float y = yCenter - bitmap.Height / 2;

        canvas.SetMatrix(matrix);
        canvas.DrawBitmap(bitmap, x, y);
    }
}
```

Ниже приведены некоторые примеры изображений.

[![](non-affine-images/testperspective-small.png "Тройной снимок экрана страницы перспективы \"Тест\"")](non-affine-images/testperspective-large.png#lightbox "тройной снимок экрана страницы перспективы \"Тест\"")

Во время экспериментов с ползунки, вы обнаружите, что значения за пределы 0.0066 или ниже –0.0066 привести к внезапно становятся части и некорректные изображения. Преобразуемого растрового изображения представляет квадрат 300 пикселей. Он преобразуется относительно ее центра, поэтому координаты растрового изображения в диапазоне от –150 до 150. Помните, что значение z "является:

z "= Persp0·x + Persp1·y + 1

Если `Persp0` или `Persp1` больше, чем 0.0066 или ниже –0.0066, затем всегда есть некоторые координат точечного рисунка, который приводит к z "нулевое значение. Вызывающий исключение деления на ноль, а отрисовку становится все не так. При использовании неаффинные преобразования, вы хотите избежать создания ничего с координатами, вызывающие деления на ноль.

Как правило, вы не параметр `Persp0` и `Persp1` изолированно. Также часто бывает необходимо задать других ячеек в матрице для достижения определенных типов неаффинные преобразования.

Один такой неаффинных преобразование является *конический преобразования*. Этот тип не аффинного преобразования сохраняет общие размеры прямоугольника, но истончается стороне "один":

![](non-affine-images/tapertransform.png "Бокс, распространяется конический преобразования")

[ `TaperTransform` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TaperTransform.cs) Класс выполняет вычисление обобщенный неаффинных преобразования на основе этих параметров:

- Прямоугольная размер изображения преобразуемого,
- Перечисление, указывающее на стороне прямоугольника, истончается,
- другой перечисления, указывающее, каким образом истончается, и
- Ослабление экстент.

Ниже приведен код:

```csharp
enum TaperSide { Left, Top, Right, Bottom }

enum TaperCorner { LeftOrTop, RightOrBottom, Both }

static class TaperTransform
{
    public static SKMatrix Make(SKSize size, TaperSide taperSide, TaperCorner taperCorner, float taperFraction)
    {
        SKMatrix matrix = SKMatrix.MakeIdentity();

        switch (taperSide)
        {
            case TaperSide.Left:
                matrix.ScaleX = taperFraction;
                matrix.ScaleY = taperFraction;
                matrix.Persp0 = (taperFraction - 1) / size.Width;

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewY = size.Height * matrix.Persp0;
                        matrix.TransY = size.Height * (1 - taperFraction);
                        break;

                    case TaperCorner.Both:
                        matrix.SkewY = (size.Height / 2) * matrix.Persp0;
                        matrix.TransY = size.Height * (1 - taperFraction) / 2;
                        break;
                }
                break;

            case TaperSide.Top:
                matrix.ScaleX = taperFraction;
                matrix.ScaleY = taperFraction;
                matrix.Persp1 = (taperFraction - 1) / size.Height;

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewX = size.Width * matrix.Persp1;
                        matrix.TransX = size.Width * (1 - taperFraction);
                        break;

                    case TaperCorner.Both:
                        matrix.SkewX = (size.Width / 2) * matrix.Persp1;
                        matrix.TransX = size.Width * (1 - taperFraction) / 2;
                        break;
                }
                break;

            case TaperSide.Right:
                matrix.ScaleX = 1 / taperFraction;
                matrix.Persp0 = (1 - taperFraction) / (size.Width * taperFraction);

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewY = size.Height * matrix.Persp0;
                        break;

                    case TaperCorner.Both:
                        matrix.SkewY = (size.Height / 2) * matrix.Persp0;
                        break;
                }
                break;

            case TaperSide.Bottom:
                matrix.ScaleY = 1 / taperFraction;
                matrix.Persp1 = (1 - taperFraction) / (size.Height * taperFraction);

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewX = size.Width * matrix.Persp1;
                        break;

                    case TaperCorner.Both:
                        matrix.SkewX = (size.Width / 2) * matrix.Persp1;
                        break;
                }
                break;
        }
        return matrix;
    }
}
```

Этот класс используется в **конический преобразования** страницы. Файл XAML создает два `Picker` элементов для выбора значений перечисления и `Slider` по выбору конический дробь. [ `PaintSurface` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TaperTransformPage.xaml.cs#L55) Объединяет обработчик конический преобразования с двумя перевести преобразование, которое требуется сделать преобразование относительно верхнего левого угла растрового изображения:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    TaperSide taperSide = (TaperSide)taperSidePicker.SelectedItem;
    TaperCorner taperCorner = (TaperCorner)taperCornerPicker.SelectedItem;
    float taperFraction = (float)taperFractionSlider.Value;

    SKMatrix taperMatrix =
        TaperTransform.Make(new SKSize(bitmap.Width, bitmap.Height),
                            taperSide, taperCorner, taperFraction);

    // Display the matrix in the lower-right corner
    SKSize matrixSize = matrixDisplay.Measure(taperMatrix);

    matrixDisplay.Paint(canvas, taperMatrix,
        new SKPoint(info.Width - matrixSize.Width,
                    info.Height - matrixSize.Height));

    // Center bitmap on canvas
    float x = (info.Width - bitmap.Width) / 2;
    float y = (info.Height - bitmap.Height) / 2;

    SKMatrix matrix = SKMatrix.MakeTranslation(-x, -y);
    SKMatrix.PostConcat(ref matrix, taperMatrix);
    SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(x, y));

    canvas.SetMatrix(matrix);
    canvas.DrawBitmap(bitmap, x, y);
}
```

Далее приводятся некоторые примеры.

[![](non-affine-images/tapertransform-small.png "Тройной снимок экрана страницы конический преобразования")](non-affine-images/tapertransform-large.png#lightbox "тройной снимок экрана страницы конический преобразования")

Другой тип универсального неаффинные преобразования — это трехмерного поворота, как показано в следующей статье, [ **трехмерных вращений**](3d-rotation.md).

Неаффинных преобразование можно преобразовать прямоугольник в любой выпуклая четырехугольника осуществляется плавный переход. Это демонстрируется путем **Показать Non-аффинное** страницы. Это очень похоже на **Показать аффинное** странице из [ **матрица преобразует** ](matrix.md) статьи, за исключением того, что он имеет четвертый `TouchPoint` для управления четвертый уголок растрового изображения.

[![](non-affine-images/shownonaffinematrix-small.png "Тройной снимок экрана страницы Показать Non-аффинное")](non-affine-images/shownonaffinematrix-large.png#lightbox "тройной снимок экрана страницы Показать Non-аффинное")

До тех пор, пока вы не попытаетесь создайте внутреннюю угол один из углов точечного рисунка больше 180 градусов или две стороны пересекать друг друга, успешно вычисляется преобразование с помощью данного метода из [ `ShowNonAffineMatrixPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowNonAffineMatrixPage.xaml.cs) класса:

```csharp
static SKMatrix ComputeMatrix(SKSize size, SKPoint ptUL, SKPoint ptUR, SKPoint ptLL, SKPoint ptLR)
{
    // Scale transform
    SKMatrix S = SKMatrix.MakeScale(1 / size.Width, 1 / size.Height);

    // Affine transform
    SKMatrix A = new SKMatrix
    {
        ScaleX = ptUR.X - ptUL.X,
        SkewY = ptUR.Y - ptUL.Y,
        SkewX = ptLL.X - ptUL.X,
        ScaleY = ptLL.Y - ptUL.Y,
        TransX = ptUL.X,
        TransY = ptUL.Y,
        Persp2 = 1
    };

    // Non-Affine transform
    SKMatrix inverseA;
    A.TryInvert(out inverseA);
    SKPoint abPoint = inverseA.MapPoint(ptLR);
    float a = abPoint.X;
    float b = abPoint.Y;

    float scaleX = a / (a + b - 1);
    float scaleY = b / (a + b - 1);

    SKMatrix N = new SKMatrix
    {
        ScaleX = scaleX,
        ScaleY = scaleY,
        Persp0 = scaleX - 1,
        Persp1 = scaleY - 1,
        Persp2 = 1
    };

    // Multiply S * N * A
    SKMatrix result = SKMatrix.MakeIdentity();
    SKMatrix.PostConcat(ref result, S);
    SKMatrix.PostConcat(ref result, N);
    SKMatrix.PostConcat(ref result, A);

    return result;
}
```

Для упрощения расчетов этот метод получает общее преобразование, как продукт три отдельных преобразований, в которых являются отображаемый здесь со стрелками, показывающий, как эти преобразования изменяют четыре угла растрового изображения:

(0, 0) → "→" (0, 0) "→" (0, 0) (x 0, y0) (сверху слева)

(0, H) (0, 1) "→" → "→" (0, 1) (x1, y1) (слева)

(W, 0) → "→" (1, 0) "→" (1, 0) (x 2, y2) (верхний правый)

("W", "H") → → (a, b) "→" (1, 1) (x 3 y3) (правый нижний)

Окончательный координаты справа указываются четыре точки, связанные с четырьмя сенсорных точек. Это окончательный координаты углов растрового изображения.

Ширина и высота представляют ширина и Высота растрового изображения. Первое `S` просто Масштабирующее растровое изображение в квадрат 1 пиксель. Преобразование второй является преобразование неаффинных `N`, а третий — аффинного преобразования `A`. Это преобразование аффинным основывается на три точки, поэтому это так же как ранее аффинные [ `ComputeMatrix` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml.cs#L68) метод и не включает четвертой строке с (a, b) точки.

`a` И `b` значения вычисляются так, что третий преобразование аффинным. Код получает обратное преобразование аффинным и затем использует его для сопоставления в правом нижнем углу. Это точка (a, b).

Другой неаффинные преобразования используется для имитации трехмерной графики. В следующей статье [ **трехмерных вращений** ](3d-rotation.md) вы см. в разделе о смене двухмерной графики в трехмерном пространстве.


## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
