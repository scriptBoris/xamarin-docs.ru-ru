---
title: Трехмерных вращений в SkiaSharp
description: В этой статье объясняется, как использовать неаффинные преобразования для поворота 2D объектов в трехмерном пространстве, а также демонстрирует это с помощью примера кода.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: B5894EA0-C415-41F9-93A4-BBF6EC72AFB9
author: davidbritch
ms.author: dabritch
ms.date: 04/14/2017
ms.openlocfilehash: 7ac9ec458f16357ef50e23c459a9b0e1f79bdd97
ms.sourcegitcommit: c6ff24b524d025d7e87b7b9c25f04c740dd93497
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56240374"
---
# <a name="3d-rotations-in-skiasharp"></a>Трехмерных вращений в SkiaSharp

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_Используйте неаффинные преобразования для поворота 2D объектов в трехмерном пространстве._

Одно из распространенных применений неаффинные преобразования имитирует поворот двухмерного объекта в трехмерном пространстве:

![](3d-rotation-images/3drotationsexample.png "Текстовая строка, в трехмерном пространстве (повернутый)")

Это задание включает в себя, работа с трехмерных поворотов, а затем получая неаффинных `SKMatrix` преобразование, которое выполняет эти трехмерных поворотов.

Трудно разрабатывать это `SKMatrix` преобразования, которые работают исключительно в пределах двух измерений. Задание становится гораздо понятнее, когда эта матрица 3 x 3 является производной от матрицу 4 на 4, используемую в области трехмерной графики. Включает в себя SkiaSharp [ `SKMatrix44` ](xref:SkiaSharp.SKMatrix44) класс для этой цели, но опыт в области трехмерной графики необходима для понимания трехмерных вращений и матрица преобразования 4-4.

В трехмерной системе координат добавляет третьей осью вызывается Z. по существу, ось Z — угол экрана. Они обозначаются точки координат в трехмерном пространстве с помощью трех чисел: (x, y, z). В трехмерном пространстве систему координат, используемую в этой статье, увеличение значения X, справа и увеличение значений Y из строя, как в двух измерений. Увеличение положительные значения Z исходит от экрана. Источником является верхнего левого угла, как в двумерной графики. Можно считать экрана плоскости XY с оси под прямым углом к этой плоскости Z.

Это называется слева системы координат. Если вы указываете указательным пальцем для левой руки в направлении положительного X координаты (справа), и координирует пальца среднего в направление увеличения Y (вниз), затем вашей thumb точки в направление увеличения координаты Z, выходящий за пределы экран.

В области трехмерной графики преобразования основаны на матрицу 4 на 4. Вот единичной матрицы 4-4:

<pre>
|  1  0  0  0  |
|  0  1  0  0  |
|  0  0  1  0  |
|  0  0  0  1  |
</pre>

В работе с матрицу 4 на 4, удобно для определения ячейки с номерами строк и столбцов:

<pre>
|  M11  M12  M13  M14  |
|  M21  M22  M23  M24  |
|  M31  M32  M33  M34  |
|  M41  M42  M43  M44  |
</pre>

Тем не менее SkiaSharp `Matrix44` класс немного отличается. Единственный способ задания или получения значений отдельных ячеек `SKMatrix44` с помощью [ `Item` ](xref:SkiaSharp.SKMatrix44.Item(System.Int32,System.Int32)) индексатора. Индексы строки и столбца, отсчитываемый от нуля, а не от единицы, а строки и столбцы меняются местами. На схеме выше ячейки M14 осуществляется с помощью индексатора `[3, 0]` в `SKMatrix44` объекта.

В системе, 3D-графики трехмерной точки (x, y, z) преобразуется в матрицу с 1 по 4 для умножения матрица преобразования 4-4:

<pre>
                 |  M11  M12  M13  M14  |
| x  y  z  1 | × |  M21  M22  M23  M24  | = | x'  y'  z'  w' |
                 |  M31  M32  M33  M34  |
                 |  M41  M42  M43  M44  |
</pre>

Аналогом двухмерных преобразований, которые происходят в трех измерениях, трехмерными преобразованиями предполагается, что должно выполняться в четырех измерений. В четвертом измерении называется W и трехмерном пространстве предполагается, что существует в пространстве 4D, где координаты W равны 1. Преобразование формул, следующим образом:

`x' = M11·x + M21·y + M31·z + M41`

`y' = M12·x + M22·y + M32·z + M42`

`z' = M13·x + M23·y + M33·z + M43`

`w' = M14·x + M24·y + M34·z + M44`

Это очевидно из формулы преобразования, ячейки `M11`, `M22`, `M33` коэффициенты масштабирования в направлениях X, Y и Z, и `M41`, `M42`, и `M43` факторов перевода в X, Y и Z инструкции.

Чтобы преобразовать эти координаты обратно в трехмерном пространстве, где равняется 1, x, W ", y", и z «координаты все разделены w»:

`x" = x' / w'`

`y" = y' / w'`

`z" = z' / w'`

`w" = w' / w' = 1`

Этого деления, w "предоставляет точки зрения в трехмерном пространстве. Если w "значение 1, то Перспектива не происходит.

Вращения в трехмерном пространстве может быть весьма сложным, но самый простой поворотов являются те вокруг оси X, Y и Z. Поворот угол α вокруг оси X является данной матрицы:

<pre>
|  1     0       0     0  |
|  0   cos(α)  sin(α)  0  |
|  0  –sin(α)  cos(α)  0  |
|  0     0       0     1  |
</pre>

Значения X не изменяются при проверок, выполняемых в этом преобразовании. Поворот вокруг оси Y оставляет значений Y без изменений:

<pre>
|  cos(α)  0  –sin(α)  0  |
|    0     1     0     0  |
|  sin(α)  0   cos(α)  0  |
|    0     0     0     1  |
</pre>

Поворот вокруг оси Z аналогична описанной в двумерной графики:

<pre>
|  cos(α)  sin(α)  0  0  |
| –sin(α)  cos(α)  0  0  |
|    0       0     1  0  |
|    0       0     0  1  |
</pre>

Направление поворота подразумевается рабочей руки пользователя системы координат. Это левую систему, поэтому, если палец левой руки на увеличение значений для определенной оси — справа для поворота вокруг оси X, вниз для поворота вокруг оси Y, а также к себе для поворота вокруг оси Z — затем изгиб yo создают остальные пальцы указывает направление положительные значения угла поворота.

`SKMatrix44` Универсальный статический [ `CreateRotation` ](xref:SkiaSharp.SKMatrix44.CreateRotation(System.Single,System.Single,System.Single,System.Single)) и [ `CreateRotationDegrees` ](xref:SkiaSharp.SKMatrix44.CreateRotationDegrees(System.Single,System.Single,System.Single,System.Single)) методы, которые позволяют пользователю указать ось, вокруг которой производится поворот:

```csharp
public static SKMatrix44 CreateRotationDegrees (Single x, Single y, Single z, Single degrees)
```

Для поворота вокруг оси X задайте первые три аргумента 1, 0, 0. Для поворота вокруг оси Y задайте для них значение 0, 1, 0, а для поворота вокруг оси Z, задайте для них значение 0, 0, 1.

Четвертый столбец 4 на 4 является для перспективы. `SKMatrix44` Не имеет методов для создания перспективы преобразования, но можно создать один самостоятельно, используя следующий код:

```csharp
SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
perspectiveMatrix[3, 2] = -1 / depth;
```

Причина имя аргумента `depth` будет заметно чуть ниже. Этот код создает матрицы:

<pre>
|  1  0  0      0     |
|  0  1  0      0     |
|  0  0  1  -1/depth  |
|  0  0  0      1     |
</pre>

Преобразование формул привести следующий расчет w ":

`w' = –z / depth + 1`

Это обеспечивает, чтобы сократить координаты X и Y, если значения Z меньше нуля (по существу за плоскостью) и увеличить координаты X и Y положительные значения Z. Если значение равно Координата по оси Z `depth`, затем w "равно нулю, и координаты становятся бесконечной. Системы трехмерной графики строятся вокруг метафора камеры и `depth` здесь значение представляет расстояние от камеры до от начала координат. Если графический объект имеет Z координации, то есть `depth` единицы от начала координат, по существу касается объектив камеры и будет бесконечно большим.

Имейте в виду, что, вероятно, используется это `perspectiveMatrix` значение в сочетании с матрицы поворота. Если графический объект заменяемым имеет координаты X или Y, больше, чем `depth`, то угол поворота этого объекта в трехмерном пространстве вовлекает координаты Z, больше, чем `depth`. Это следует избегать! При создании `perspectiveMatrix` нужно задать `depth` значение достаточно большим для всех координат в графический объект независимо от того, как ее поворот. Это гарантирует, что не все деление на ноль.

Объединение трехмерных вращений и перспективы требуется перемножения матриц 4-4 друг с другом. Для этой цели `SKMatrix44` определяет методы объединения. Если `A` и `B` являются `SKMatrix44` объектов, то следующий код задает равенства × б:

```csharp
A.PostConcat(B);
```

Когда матрицу 4 на 4 преобразования используется в системе двумерной графики, он применяется к 2D объектов. Эти объекты являются плоскими и предполагается, что имеют координаты Z, 0. Преобразование умножения немного проще, чем преобразование, показанный ранее:

<pre>
                 |  M11  M12  M13  M14  |
| x  y  0  1 | × |  M21  M22  M23  M24  | = | x'  y'  z'  w' |
                 |  M31  M32  M33  M34  |
                 |  M41  M42  M43  M44  |
</pre>

Это значение 0, для результатов z в формулах преобразования, которые не относятся ни одной из ячеек в третьей строке матрицы:

x' = M11·x + M21·y + M41

y "= M12·x + M22·y + M42

z "= M13·x + M23·y + M43

w "= M14·x + M24·y + M44

Кроме того z "координат не имеет значения, как здесь. Когда трехмерный объект отображается в системе двумерной графики, он свернут двухмерный объект, игнорируя значения координат Z. Преобразование формул являются просто эти два:

`x" = x' / w'`

`y" = y' / w'`

Это означает, что третья строка *и* третий столбец матрицы 4-4 можно игнорировать.

Но, так ли это, почему такое матрице 4-4 необходимо в первую очередь?

Несмотря на то, что третьей строке и третьем столбце 4 на 4 неприменимы для двумерных преобразований, в третьей строке и столбце *сделать* играют роль до что при различных `SKMatrix44` умножением значения. Например предположим, умножается поворота вокруг оси Y с точки зрения преобразования:

<pre>
|  cos(α)  0  –sin(α)  0  |   |  1  0  0      0     |   |  cos(α)  0  –sin(α)   sin(α)/depth  |
|    0     1     0     0  | × |  0  1  0      0     | = |    0     1     0           0        |
|  sin(α)  0   cos(α)  0  |   |  0  0  1  -1/depth  |   |  sin(α)  0   cos(α)  -cos(α)/depth  |  
|    0     0     0     1  |   |  0  0  0      1     |   |    0     0     0           1        |
</pre>

В продукте, ячейки `M14` теперь содержит значение перспективы. Если вы хотите применить данную матрицу к 2D объектам, для преобразования его в матрицу 3 x 3 исключаются в третьей строке и столбце:

<pre>
|  cos(α)  0  sin(α)/depth  |
|    0     1       0        |
|    0     0       1        |
</pre>

Теперь его можно использовать для преобразования 2D точки.

<pre>
                |  cos(α)  0  sin(α)/depth  |
|  x  y  1  | × |    0     1       0        | = |  x'  y'  z'  |
                |    0     0       1        |
</pre>

Преобразование формул:

`x' = cos(α)·x`

`y' = y`

`z' = (sin(α)/depth)·x + 1`

Теперь все, что деления на z ":

`x" = cos(α)·x / ((sin(α)/depth)·x + 1)`

`y" = y / ((sin(α)/depth)·x + 1)`

При 2D объекты меняются с положительный угол вокруг оси Y, а затем положительное значения X направлены в фоновом режиме при отрицательное значения X поступать на передний план. Значения X, похоже, приблизиться к оси Y (определяется значением косинуса) как координаты дальше всех от оси Y становится меньшего или большего размера, при переходе от средство просмотра или ближе к пользователю.

При использовании `SKMatrix44`, выполнения всех трехмерного поворота и операций с точки зрения путем умножения различных `SKMatrix44` значения. Затем можно извлечь Двухмерная матрица 3 x 3 из 4-4 с помощью матрицы [ `Matrix` ](xref:SkiaSharp.SKMatrix44.Matrix) свойство `SKMatrix44` класса. Это свойство возвращает стандартные `SKMatrix` значение.

**Трехмерного поворота** странице позволяет экспериментировать с трехмерного поворота. [ **Rotation3DPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml) файл создает четыре ползунки для задания поворота вокруг оси X, Y и Z, а также задать значение глубины:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Transforms.Rotation3DPage"
             Title="Rotation 3D">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
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
                    <Setter Property="Maximum" Value="360" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Slider x:Name="xRotateSlider"
                Grid.Row="0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference xRotateSlider},
                              Path=Value,
                              StringFormat='X-Axis Rotation = {0:F0}'}"
               Grid.Row="1" />

        <Slider x:Name="yRotateSlider"
                Grid.Row="2"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference yRotateSlider},
                              Path=Value,
                              StringFormat='Y-Axis Rotation = {0:F0}'}"
               Grid.Row="3" />

        <Slider x:Name="zRotateSlider"
                Grid.Row="4"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference zRotateSlider},
                              Path=Value,
                              StringFormat='Z-Axis Rotation = {0:F0}'}"
               Grid.Row="5" />

        <Slider x:Name="depthSlider"
                Grid.Row="6"
                Maximum="2500"
                Minimum="250"
                ValueChanged="OnSliderValueChanged" />

        <Label Grid.Row="7"
               Text="{Binding Source={x:Reference depthSlider},
                              Path=Value,
                              StringFormat='Depth = {0:F0}'}" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="8"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

Обратите внимание, что `depthSlider` инициализируется с помощью `Minimum` значение 250. Это означает, что двумерного объекта, который будет сменен здесь имеет координаты X и Y, ограничены круга определенного радиусом 250 пикселей вокруг начала координат. Любой поворота этого объекта в трехмерном пространстве будет всегда приводить значения координат меньше, чем 250.

[ **Rotation3DPage.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml.cs) загружает файл с выделенным кодом в квадрат 300 пикселей растрового изображения:

```csharp
public partial class Rotation3DPage : ContentPage
{
    SKBitmap bitmap;

    public Rotation3DPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if (canvasView != null)
        {
            canvasView.InvalidateSurface();
        }
    }
    ...
}
```

Если трехмерные преобразования, по этим растровым изображением, затем координаты X и Y диапазон от –150 до 150, хотя углов являются 212 пикселей в центре, поэтому все, что находится в пределах radius 250 пикселей.

`PaintSurface` Обработчик создает `SKMatrix44` объекты на основании ползунки и умножает их друг с другом с помощью `PostConcat`. `SKMatrix` Значение, извлеченное из последней `SKMatrix44` , заключается в объект путем перевода преобразования, чтобы центрировать угол поворота в центре экрана:

```csharp
public partial class Rotation3DPage : ContentPage
{
    SKBitmap bitmap;

    public Rotation3DPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
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

        // Find center of canvas
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        // Translate center to origin
        SKMatrix matrix = SKMatrix.MakeTranslation(-xCenter, -yCenter);

        // Use 3D matrix for 3D rotations and perspective
        SKMatrix44 matrix44 = SKMatrix44.CreateIdentity();
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(1, 0, 0, (float)xRotateSlider.Value));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 1, 0, (float)yRotateSlider.Value));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 0, 1, (float)zRotateSlider.Value));

        SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
        perspectiveMatrix[3, 2] = -1 / (float)depthSlider.Value;
        matrix44.PostConcat(perspectiveMatrix);

        // Concatenate with 2D matrix
        SKMatrix.PostConcat(ref matrix, matrix44.Matrix);

        // Translate back to center
        SKMatrix.PostConcat(ref matrix,
            SKMatrix.MakeTranslation(xCenter, yCenter));

        // Set the matrix and display the bitmap
        canvas.SetMatrix(matrix);
        float xBitmap = xCenter - bitmap.Width / 2;
        float yBitmap = yCenter - bitmap.Height / 2;
        canvas.DrawBitmap(bitmap, xBitmap, yBitmap);
    }
}
```

Экспериментируя с четвертого "ползунок", видно, что параметры различных глубина не перемещать объект, стоящие сторону от зрителя, что вместо этого alter экстент эффектом перспективы:

[![](3d-rotation-images/rotation3d-small.png "Тройной снимок экрана страницы трехмерный поворот")](3d-rotation-images/rotation3d-large.png#lightbox "тройной снимок экрана страницы трехмерный поворот")

**Анимировать Поворот 3D** также использует `SKMatrix44` для анимации строку текста в трехмерном пространстве. `textPaint` Набор объектов, так как поле используется в конструкторе для определения границы текста:

```csharp
public class AnimatedRotation3DPage : ContentPage
{
    SKCanvasView canvasView;
    float xRotationDegrees, yRotationDegrees, zRotationDegrees;
    string text = "SkiaSharp";
    SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        TextSize = 100,
        StrokeWidth = 3,
    };
    SKRect textBounds;

    public AnimatedRotation3DPage()
    {
        Title = "Animated Rotation 3D";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Measure the text
        textPaint.MeasureText(text, ref textBounds);
    }
    ...
}
```

`OnAppearing` Переопределение определяет три Xamarin.Forms `Animation` объектов для анимации `xRotationDegrees`, `yRotationDegrees`, и `zRotationDegrees` поля с разной скоростью. Обратите внимание, что периоды эти анимации задаются к простым числовых данных (5 секунд, 7 секунд и 11 секунд) для общего сочетания только повторяется каждые 385 секунд или более 10 минут:

```csharp
public class AnimatedRotation3DPage : ContentPage
{
    ...
    protected override void OnAppearing()
    {
        base.OnAppearing();

        new Animation((value) => xRotationDegrees = 360 * (float)value).
            Commit(this, "xRotationAnimation", length: 5000, repeat: () => true);

        new Animation((value) => yRotationDegrees = 360 * (float)value).
            Commit(this, "yRotationAnimation", length: 7000, repeat: () => true);

        new Animation((value) =>
        {
            zRotationDegrees = 360 * (float)value;
            canvasView.InvalidateSurface();
        }).Commit(this, "zRotationAnimation", length: 11000, repeat: () => true);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
        this.AbortAnimation("xRotationAnimation");
        this.AbortAnimation("yRotationAnimation");
        this.AbortAnimation("zRotationAnimation");
    }
    ...
}
```

Как и в предыдущей программы `PaintCanvas` обработчик создает `SKMatrix44` значения для поворота и перспективы и умножает их друг с другом:

```csharp
public class AnimatedRotation3DPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find center of canvas
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        // Translate center to origin
        SKMatrix matrix = SKMatrix.MakeTranslation(-xCenter, -yCenter);

        // Scale so text fits
        float scale = Math.Min(info.Width / textBounds.Width,
                               info.Height / textBounds.Height);
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeScale(scale, scale));

        // Calculate composite 3D transforms
        float depth = 0.75f * scale * textBounds.Width;

        SKMatrix44 matrix44 = SKMatrix44.CreateIdentity();
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(1, 0, 0, xRotationDegrees));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 1, 0, yRotationDegrees));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 0, 1, zRotationDegrees));

        SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
        perspectiveMatrix[3, 2] = -1 / depth;
        matrix44.PostConcat(perspectiveMatrix);

        // Concatenate with 2D matrix
        SKMatrix.PostConcat(ref matrix, matrix44.Matrix);

        // Translate back to center
        SKMatrix.PostConcat(ref matrix,
            SKMatrix.MakeTranslation(xCenter, yCenter));

        // Set the matrix and display the text
        canvas.SetMatrix(matrix);
        float xText = xCenter - textBounds.MidX;
        float yText = yCenter - textBounds.MidY;
        canvas.DrawText(text, xText, yText, textPaint);
    }
}
```

Этот трехмерного поворота заключаются в нескольких двухмерных преобразований Чтобы переместить центр вращения относительно центральной части экрана, а также масштабировать размер текстовой строки, так как это одинаковую ширину экрана:

[![](3d-rotation-images/animatedrotation3d-small.png "Тройной снимок экрана страницы анимировать поворот трехмерной")](3d-rotation-images/animatedrotation3d-large.png#lightbox "тройной снимок экрана страницы анимировать Поворот 3D")


## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
