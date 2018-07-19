---
title: Манипуляции сенсорного ввода
description: В этой статье объясняется, как использовать преобразования матрицы для реализации перетаскивания сенсорного ввода, как уменьшение и поворота и демонстрирует это с помощью примера кода.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: A0B8DD2D-7392-4EC5-BFB0-6209407AD650
author: charlespetzold
ms.author: chape
ms.date: 04/03/2018
ms.openlocfilehash: 2de5b9a3a6bf0d36330212a52ba5c7278b970efc
ms.sourcegitcommit: 7f2e44e6f628753e06a5fe2a3076fc2ec5baa081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39130911"
---
# <a name="touch-manipulations"></a>Манипуляции сенсорного ввода

_Используйте матрицы преобразования для реализации перетаскивания сенсорного ввода, как уменьшение и поворота_

В мультисенсорной средах, таких как браузеры для мобильных устройств пользователи часто используют пальцами для управления объектами на экране. Распространенные жестов, например перетаскивание один палец и двухпальцевый можно переместить и масштабирование объектов или даже поворота. Эти жесты обычно реализуются с помощью матрицы преобразования, а в этой статье показано, как это сделать.

![](touch-images/touchmanipulationsexample.png "Битовая карта распространяется перевода, масштабирования и поворота")

## <a name="manipulating-one-bitmap"></a>Обработка одного точечного рисунка

**Touch манипуляции** страница демонстрирует манипуляции сенсорного ввода на одном точечном рисунке.
В этом примере используется эффект touch отслеживания, представленные в этой статье [вызов события из эффекты](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md).

Поддерживает несколько файлов **Touch манипуляции** страницы. Во-первых, [ `TouchManipulationMode` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationMode.cs) перечисления, который указывает различные типы реализации с помощью кода, будут встречаться манипуляции сенсорного ввода:

```csharp
enum TouchManipulationMode
{
    None,
    PanOnly,
    IsotropicScale,     // includes panning
    AnisotropicScale,   // includes panning
    ScaleRotate,        // implies isotropic scaling
    ScaleDualRotate     // adds one-finger rotation
}
```

`PanOnly` — Это один палец перетаскивания, который реализуется с помощью преобразования. Все последующие параметры также включают панорамирование, но включают в себя два пальца: `IsotropicScale` является операцией жестом сжатия, которая приводит объект масштабирование одинаково в горизонтальном и вертикальном направлениях. `AnisotropicScale` позволяет добавлять элементы не равны.

`ScaleRotate` Параметр предназначен для двух пальцев масштабировании и повороте. Масштабирование — это мощности. Реализация двух пальцев поворота анизотропная масштабирования представляет проблему, поскольку перемещений палец по сути одинаковые.

`ScaleDualRotate` Параметр добавляет один пальцем. Когда одним пальцем перетаскивает объект, перетаскиваемый объект сначала поворачивается вокруг его центра, так, чтобы центр объекта с помощью перетаскивания вектора.

[ **TouchManipulationPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml) файл включает `Picker` с элементами `TouchManipulationMode` перечисления:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Transforms.TouchManipulationPage"
             Title="Touch Manipulation">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Picker Title="Touch Mode"
                Grid.Row="0"
                SelectedIndexChanged="OnTouchModePickerSelectedIndexChanged">
            <Picker.Items>
                <x:String>None</x:String>
                <x:String>PanOnly</x:String>
                <x:String>IsotropicScale</x:String>
                <x:String>AnisotropicScale</x:String>
                <x:String>ScaleRotate</x:String>
                <x:String>ScaleDualRotate</x:String>
            </Picker.Items>
            <Picker.SelectedIndex>
                4
            </Picker.SelectedIndex>
        </Picker>

        <Grid BackgroundColor="White"
              Grid.Row="1">

            <skia:SKCanvasView x:Name="canvasView"
                               PaintSurface="OnCanvasViewPaintSurface" />
            <Grid.Effects>
                <tt:TouchEffect Capture="True"
                                TouchAction="OnTouchEffectAction" />
            </Grid.Effects>
        </Grid>
    </Grid>
</ContentPage>
```

Нижней — `SKCanvasView` и `TouchEffect` подключенные к одной ячейки `Grid` , в котором он.

[ **TouchManipulationPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml.cs) имеет файл с выделенным кодом `bitmap` поле, но он не относится к типу `SKBitmap`. Тип — `TouchManipulationBitmap` (класс вскоре вы увидите):

```csharp
public partial class TouchManipulationPage : ContentPage
{
    TouchManipulationBitmap bitmap;
    ...

    public TouchManipulationPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.MountainClimbers.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            SKBitmap bitmap = SKBitmap.Decode(stream);
            this.bitmap = new TouchManipulationBitmap(bitmap);
            this.bitmap.TouchManager.Mode = TouchManipulationMode.ScaleRotate;
        }
    }
    ...
}
```

Конструктор создает `TouchManipulationBitmap` объекта, передав конструктору `SKBitmap` получен из внедренного ресурса. Конструктор завершает, задав `Mode` свойство `TouchManager` свойство `TouchManipulationBitmap` , который является членом `TouchManipulationMode` перечисления.

`SelectedIndexChanged` Обработчик для `Picker` также задает это `Mode` свойство:

```csharp
public partial class TouchManipulationPage : ContentPage
{
    ...
    void OnTouchModePickerSelectedIndexChanged(object sender, EventArgs args)
    {
        if (bitmap != null)
        {
            Picker picker = (Picker)sender;
            TouchManipulationMode mode;
            Enum.TryParse(picker.Items[picker.SelectedIndex], out mode);
            bitmap.TouchManager.Mode = mode;
        }
    }
    ...
}
```

`TouchAction` Обработчик `TouchEffect` создан в файле XAML вызывает два метода в `TouchManipulationBitmap` с именем `HitTest` и `ProcessTouchEvent`:

```csharp
public partial class TouchManipulationPage : ContentPage
{
    ...
    List<long> touchIds = new List<long>();
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point =
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (bitmap.HitTest(point))
                {
                    touchIds.Add(args.Id);
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    break;
                }
                break;

            case TouchActionType.Moved:
                if (touchIds.Contains(args.Id))
                {
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (touchIds.Contains(args.Id))
                {
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    touchIds.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;
        }
    }
    ...
}
```

Если `HitTest` возвращает `true` &mdash; это означает, что палец коснулось экране в пределах области, растровое изображение &mdash; touch ID добавляется к `TouchIds` коллекции. Этот идентификатор представляет последовательность событий сенсорного ввода для этого палец, пока не поднимает палец с экрана. Если нескольких пальцев touch точечного рисунка, а затем `touchIds` touch ID для каждого пальца в коллекцию.

`TouchAction` Также вызывает обработчик `ProcessTouchEvent` в класс `TouchManipulationBitmap`. Именно здесь некоторые (но не все) из реальных touch происходит обработка.

[ `TouchManipulationBitmap` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationBitmap.cs) Класс является оболочкой для класса для `SKBitmap` , содержащий код для визуализации точечного рисунка и обработки событий сенсорного ввода. Работает в сочетании с более обобщенного кода в `TouchManipulationManager` класс (который мы рассмотрим чуть ниже).

`TouchManipulationBitmap` Конструктор сохраняет `SKBitmap` и создает два свойства `TouchManager` свойство типа `TouchManipulationManager` и `Matrix` свойство типа `SKMatrix`:

```csharp
class TouchManipulationBitmap
{
    SKBitmap bitmap;
    ...

    public TouchManipulationBitmap(SKBitmap bitmap)
    {
        this.bitmap = bitmap;
        Matrix = SKMatrix.MakeIdentity();

        TouchManager = new TouchManipulationManager
        {
            Mode = TouchManipulationMode.ScaleRotate
        };
    }

    public TouchManipulationManager TouchManager { set; get; }

    public SKMatrix Matrix { set; get; }
    ...
}
```

Это `Matrix` свойство является накопленные преобразования, полученный в результате все действия сенсорного ввода. Как можно будет увидеть, каждое событие сенсорного ввода разрешается в матрицу, которое затем объединяется с `SKMatrix` значение, хранящееся в `Matrix` свойство.

`TouchManipulationBitmap` Объект рисует себя в его `Paint` метод. Аргумент является `SKCanvas` объекта. Это `SKCanvas` могут уже иметь преобразованием, примененным к нему, поэтому `Paint` метод Сцепляет `Matrix` свойство связанные с растровым изображением, к преобразованию существующих и восстанавливает canvas, после завершения:

```csharp
class TouchManipulationBitmap
{
    ...
    public void Paint(SKCanvas canvas)
    {
        canvas.Save();
        SKMatrix matrix = Matrix;
        canvas.Concat(ref matrix);
        canvas.DrawBitmap(bitmap, 0, 0);
        canvas.Restore();
    }
    ...
}
```

`HitTest` Возвращает метод `true` Если пользователь касается экрана в момент времени в пределах границ растрового изображения. Как пользователь управляет точечного рисунка, растрового изображения могут быть повернуты или даже (благодаря сочетанию анизотропная масштабировании и повороте) находиться в форму параллелограмм. Может опасения, `HitTest` метод необходимо реализовать в этом случае довольно сложной аналитическую геометрию.

Тем не менее ярлык доступен:

Определение, находится точка в пределах преобразованный прямоугольник совпадает со значением ли обратная преобразованная точка находится внутри границы подвергнутый прямоугольника. Это гораздо проще, вычисление и его можно использовать удобный `Contains` метода, определенного `SKRect`:

```csharp
class TouchManipulationBitmap
{
    ...
    public bool HitTest(SKPoint location)
    {
        // Invert the matrix
        SKMatrix inverseMatrix;

        if (Matrix.TryInvert(out inverseMatrix))
        {
            // Transform the point using the inverted matrix
            SKPoint transformedPoint = inverseMatrix.MapPoint(location);

            // Check if it's in the untransformed bitmap rectangle
            SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
            return rect.Contains(transformedPoint);
        }
        return false;
    }
    ...
}
```

Второй открытый метод в `TouchManipulationBitmap` является `ProcessTouchEvent`. При вызове этого метода, он уже установлено событие сенсорного ввода принадлежит определенной точечного рисунка. Метод ведение словаря [ `TouchManipulationInfo` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationInfo.cs) объекты, которые просто предыдущей точки и новая точка каждого пальца:

```csharp
class TouchManipulationInfo
{
    public SKPoint PreviousPoint { set; get; }

    public SKPoint NewPoint { set; get; }
}
```

Здесь представлен словарь и `ProcessTouchEvent` сам метод:

```csharp
class TouchManipulationBitmap
{
    ...
    Dictionary<long, TouchManipulationInfo> touchDictionary =
        new Dictionary<long, TouchManipulationInfo>();
    ...
    public void ProcessTouchEvent(long id, TouchActionType type, SKPoint location)
    {
        switch (type)
        {
            case TouchActionType.Pressed:
                touchDictionary.Add(id, new TouchManipulationInfo
                {
                    PreviousPoint = location,
                    NewPoint = location
                });
                break;

            case TouchActionType.Moved:
                TouchManipulationInfo info = touchDictionary[id];
                info.NewPoint = location;
                Manipulate();
                info.PreviousPoint = info.NewPoint;
                break;

            case TouchActionType.Released:
                touchDictionary[id].NewPoint = location;
                Manipulate();
                touchDictionary.Remove(id);
                break;

            case TouchActionType.Cancelled:
                touchDictionary.Remove(id);
                break;
        }
    }
    ...
}
```

В `Moved` и `Released` события, вызывает метод `Manipulate`. В таких случаях `touchDictionary` содержит один или несколько `TouchManipulationInfo` объектов. Если `touchDictionary` содержит один элемент, вполне вероятно, `PreviousPoint` и `NewPoint` значения не равны и представляют перемещение пальца. Если несколько пальца касаются растрового изображения, словарь содержит более одного элемента, но только один из этих элементов имеет другие `PreviousPoint` и `NewPoint` значения. Все остальные, имеют `PreviousPoint` и `NewPoint` значения.

Это важно: `Manipulate` метод можно предположить, что он обрабатывает перемещение только одним пальцем. Во время этого вызова другие пальцы нет скользящее, а если они действительно при перемещении (как, вероятнее всего), в будущих вызовах будет обрабатываться перемещения `Manipulate`.

`Manipulate` Метод сначала копирует словаря в массив для удобства. Игнорирует ничего, кроме первых двух записей. Если более чем двумя пальцами пытаетесь управлять точечного рисунка, остальные игнорируются. `Manipulate` Окончательный членом `TouchManipulationBitmap`:

```csharp
class TouchManipulationBitmap
{
    ...
    void Manipulate()
    {
        TouchManipulationInfo[] infos = new TouchManipulationInfo[touchDictionary.Count];
        touchDictionary.Values.CopyTo(infos, 0);
        SKMatrix touchMatrix = SKMatrix.MakeIdentity();

        if (infos.Length == 1)
        {
            SKPoint prevPoint = infos[0].PreviousPoint;
            SKPoint newPoint = infos[0].NewPoint;
            SKPoint pivotPoint = Matrix.MapPoint(bitmap.Width / 2, bitmap.Height / 2);

            touchMatrix = TouchManager.OneFingerManipulate(prevPoint, newPoint, pivotPoint);
        }
        else if (infos.Length >= 2)
        {
            int pivotIndex = infos[0].NewPoint == infos[0].PreviousPoint ? 0 : 1;
            SKPoint pivotPoint = infos[pivotIndex].NewPoint;
            SKPoint newPoint = infos[1 - pivotIndex].NewPoint;
            SKPoint prevPoint = infos[1 - pivotIndex].PreviousPoint;

            touchMatrix = TouchManager.TwoFingerManipulate(prevPoint, newPoint, pivotPoint);
        }

        SKMatrix matrix = Matrix;
        SKMatrix.PostConcat(ref matrix, touchMatrix);
        Matrix = matrix;
    }
}
```

Если один палец с этим растровым изображением, `Manipulate` вызовы `OneFingerManipulate` метод `TouchManipulationManager` объекта. Двумя пальцами, она вызывает `TwoFingerManipulate`. Аргументы для этих методов совпадают: `prevPoint` и `newPoint` аргументы представляют палец, которые перемещаются. Но `pivotPoint` аргумента отличается для двух вызовов:

Для манипуляции один палец `pivotPoint` представляет собой центр растрового изображения. Это позволяет обеспечить для поворота один палец. Для манипуляции двумя пальцами, данное событие обозначает, перемещение только одним пальцем, чтобы `pivotPoint` является пальцу, который не перемещается.

В обоих случаях `TouchManipulationManager` возвращает `SKMatrix` значение, которое объединяет метод с текущим `Matrix` свойство, `TouchManipulationPage` использует для визуализации точечного рисунка.

`TouchManipulationManager` обобщается и использует никаких других файлов, за исключением `TouchManipulationMode`. Возможно, вы сможете использовать этот класс без изменений в собственных приложениях. Он определяет единственное свойство типа `TouchManipulationMode`:

```csharp
class TouchManipulationManager
{
    public TouchManipulationMode Mode { set; get; }
    ...
}
```


Тем не менее, возможно, стоит во избежание `AnisotropicScale` параметр. Это очень просто с этот параметр, чтобы управлять растровое изображение, чтобы один из коэффициенты масштабирования становится равным нулю. В результате исчезают, никогда не для возврата растрового изображения. Если вам действительно нужно анизотропная масштабирование, будет необходимо расширить логику, чтобы избежать нежелательных результатов.

`TouchManipulationManager` использует векторов, но так как она не `SKVector` структуры в SkiaSharp, `SKPoint` вместо него используется. `SKPoint` поддерживает оператор вычитания, а результат можно рассматривать как вектор. Логика только конкретных вектора, необходимо было добавить `Magnitude` вычисления:

```csharp
class TouchManipulationManager
{
    ...
    float Magnitude(SKPoint point)
    {
        return (float)Math.Sqrt(Math.Pow(point.X, 2) + Math.Pow(point.Y, 2));
    }
}
```

Каждый раз, когда был выбран поворота, оба метода один палец и двух пальцев обработку сначала обработайте поворот. При обнаружении любого поворота координату поворота фактически удаляется. Что же сохраняется, интерпретируется как панорамирования и масштабирования.

Вот `OneFingerManipulate` метод. Если один пальцем не была включена, то логика проста &mdash; он просто использует предыдущей точки и новой точки для построения вектора с именем `delta` , точно соответствует перевода. С поворотом один палец включен метод использует углы от точки вращения (центр растрового изображения) к предыдущей точки и новую точку для создания матрицы поворота:

```csharp
class TouchManipulationManager
{
    public TouchManipulationMode Mode { set; get; }

    public SKMatrix OneFingerManipulate(SKPoint prevPoint, SKPoint newPoint, SKPoint pivotPoint)
    {
        if (Mode == TouchManipulationMode.None)
        {
            return SKMatrix.MakeIdentity();
        }

        SKMatrix touchMatrix = SKMatrix.MakeIdentity();
        SKPoint delta = newPoint - prevPoint;

        if (Mode == TouchManipulationMode.ScaleDualRotate)  // One-finger rotation
        {
            SKPoint oldVector = prevPoint - pivotPoint;
            SKPoint newVector = newPoint - pivotPoint;

            // Avoid rotation if fingers are too close to center
            if (Magnitude(newVector) > 25 && Magnitude(oldVector) > 25)
            {
                float prevAngle = (float)Math.Atan2(oldVector.Y, oldVector.X);
                float newAngle = (float)Math.Atan2(newVector.Y, newVector.X);

                // Calculate rotation matrix
                float angle = newAngle - prevAngle;
                touchMatrix = SKMatrix.MakeRotation(angle, pivotPoint.X, pivotPoint.Y);

                // Effectively rotate the old vector
                float magnitudeRatio = Magnitude(oldVector) / Magnitude(newVector);
                oldVector.X = magnitudeRatio * newVector.X;
                oldVector.Y = magnitudeRatio * newVector.Y;

                // Recalculate delta
                delta = newVector - oldVector;
            }
        }

        // Multiply the rotation matrix by a translation matrix
        SKMatrix.PostConcat(ref touchMatrix, SKMatrix.MakeTranslation(delta.X, delta.Y));

        return touchMatrix;
    }
    ...
}
```

В `TwoFingerManipulate` , вращения является положение пальца, не входящего в этом событии специфический сенсорный. Поворот очень похожа на один палец поворот, и затем именованного вектора `oldVector` (в зависимости от предыдущей точки) настраивается для поворота. Оставшиеся перемещение интерпретируется как масштабирование:

```csharp
class TouchManipulationManager
{
    ...
    public SKMatrix TwoFingerManipulate(SKPoint prevPoint, SKPoint newPoint, SKPoint pivotPoint)
    {
        SKMatrix touchMatrix = SKMatrix.MakeIdentity();
        SKPoint oldVector = prevPoint - pivotPoint;
        SKPoint newVector = newPoint - pivotPoint;

        if (Mode == TouchManipulationMode.ScaleRotate ||
            Mode == TouchManipulationMode.ScaleDualRotate)
        {
            // Find angles from pivot point to touch points
            float oldAngle = (float)Math.Atan2(oldVector.Y, oldVector.X);
            float newAngle = (float)Math.Atan2(newVector.Y, newVector.X);

            // Calculate rotation matrix
            float angle = newAngle - oldAngle;
            touchMatrix = SKMatrix.MakeRotation(angle, pivotPoint.X, pivotPoint.Y);

            // Effectively rotate the old vector
            float magnitudeRatio = Magnitude(oldVector) / Magnitude(newVector);
            oldVector.X = magnitudeRatio * newVector.X;
            oldVector.Y = magnitudeRatio * newVector.Y;
        }

        float scaleX = 1;
        float scaleY = 1;

        if (Mode == TouchManipulationMode.AnisotropicScale)
        {
            scaleX = newVector.X / oldVector.X;
            scaleY = newVector.Y / oldVector.Y;

        }
        else if (Mode == TouchManipulationMode.IsotropicScale ||
                 Mode == TouchManipulationMode.ScaleRotate ||
                 Mode == TouchManipulationMode.ScaleDualRotate)
        {
            scaleX = scaleY = Magnitude(newVector) / Magnitude(oldVector);
        }

        if (!float.IsNaN(scaleX) && !float.IsInfinity(scaleX) &&
            !float.IsNaN(scaleY) && !float.IsInfinity(scaleY))
        {
            SKMatrix.PostConcat(ref touchMatrix,
                SKMatrix.MakeScale(scaleX, scaleY, pivotPoint.X, pivotPoint.Y));
        }

        return touchMatrix;
    }
    ...
}
```

Вы заметите, что имеется без явного преобразования в этом методе. Тем не менее, обе `MakeRotation` и `MakeScale` методы зависят от точки вращения и включает неявные преобразования. Если вы используете два пальца на растровое изображение и перетащить их в одном направлении, `TouchManipulation` получат последовательность событий сенсорного ввода, переключаясь между двумя пальцами. Как и каждый палец движется по отношению к другим, масштабирования или поворота результаты, однако он сделал движение других пальцев и результат преобразования.

Только оставшуюся часть **Touch манипуляции** страница `PaintSurface` обработчик в `TouchManipulationPage` файл с выделенным кодом. Это вызывает `Paint` метод `TouchManipulationBitmap`, которому применяется матрица, представляющая действие накопленные сенсорного ввода:

```csharp
public partial class TouchManipulationPage : ContentPage
{
    ...
    MatrixDisplay matrixDisplay = new MatrixDisplay();
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Display the bitmap
        bitmap.Paint(canvas);

        // Display the matrix in the lower-right corner
        SKSize matrixSize = matrixDisplay.Measure(bitmap.Matrix);

        matrixDisplay.Paint(canvas, bitmap.Matrix,
            new SKPoint(info.Width - matrixSize.Width,
                        info.Height - matrixSize.Height));
    }
}
```

`PaintSurface` Завершении работы обработчика, отображая `MatrixDisplay` объекта отображаются в матрице накопленные сенсорного ввода:

[![](touch-images/touchmanipulation-small.png "Тройной снимок экрана страницы Touch манипуляции")](touch-images/touchmanipulation-large.png#lightbox "тройной снимок экрана страницы Touch манипуляции")

## <a name="manipulating-multiple-bitmaps"></a>Управление несколькими точечных рисунков

Одно из преимуществ изоляции код для обработки сенсорного ввода в классах, таких как `TouchManipulationBitmap` и `TouchManipulationManager` является возможность повторного использования этих классов в программе, которая позволяет пользователю управлять несколько точечных рисунков.

**Представление точечной диаграммы точечного рисунка** странице рассказывается, как это делается. Вместо того чтобы поле типа `TouchManipulationBitmap`, [ `BitmapScatterPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BitmapScatterViewPage.xaml.cs) класс определяет `List` растрового изображения объектов:

```csharp
public partial class BitmapScatterViewPage : ContentPage
{
    List<TouchManipulationBitmap> bitmapCollection =
        new List<TouchManipulationBitmap>();
    ...
    public BitmapScatterViewPage()
    {
        InitializeComponent();

        // Load in all the available bitmaps
        Assembly assembly = GetType().GetTypeInfo().Assembly;
        string[] resourceIDs = assembly.GetManifestResourceNames();
        SKPoint position = new SKPoint();

        foreach (string resourceID in resourceIDs)
        {
            if (resourceID.EndsWith(".png") ||
                resourceID.EndsWith(".jpg"))
            {
                using (Stream stream = assembly.GetManifestResourceStream(resourceID))
                {
                    SKBitmap bitmap = SKBitmap.Decode(stream);
                    bitmapCollection.Add(new TouchManipulationBitmap(bitmap)
                    {
                        Matrix = SKMatrix.MakeTranslation(position.X, position.Y),
                    });
                    position.X += 100;
                    position.Y += 100;
                }
            }
        }
    }
    ...
}
```

Конструктор загружает во всех растровых изображений, доступных как внедренные ресурсы и добавляет их в `bitmapCollection`. Обратите внимание, что `Matrix` свойство инициализируется на каждом `TouchManipulationBitmap` объекта, поэтому смещения верхнего левого угла каждого растрового изображения на 100 пикселей.

`BitmapScatterView` Страницы также необходимо обрабатывать события касания для нескольких растровых изображений. Вместо того чтобы `List` точки касания управлять идентификаторы `TouchManipulationBitmap` этой программы требуется словарь объектов:

```csharp
public partial class BitmapScatterViewPage : ContentPage
{
    ...
    Dictionary<long, TouchManipulationBitmap> bitmapDictionary =
       new Dictionary<long, TouchManipulationBitmap>();
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point =
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                for (int i = bitmapCollection.Count - 1; i >= 0; i--)
                {
                    TouchManipulationBitmap bitmap = bitmapCollection[i];

                    if (bitmap.HitTest(point))
                    {
                        // Move bitmap to end of collection
                        bitmapCollection.Remove(bitmap);
                        bitmapCollection.Add(bitmap);

                        // Do the touch processing
                        bitmapDictionary.Add(args.Id, bitmap);
                        bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                        canvasView.InvalidateSurface();
                        break;
                    }
                }
                break;

            case TouchActionType.Moved:
                if (bitmapDictionary.ContainsKey(args.Id))
                {
                    TouchManipulationBitmap bitmap = bitmapDictionary[args.Id];
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (bitmapDictionary.ContainsKey(args.Id))
                {
                    TouchManipulationBitmap bitmap = bitmapDictionary[args.Id];
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    bitmapDictionary.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;
        }
    }
    ...
}
```

Обратите внимание, что как `Pressed` логика просматривает `bitmapCollection` в обратном порядке. Точечные рисунки часто перекрываются друг с другом. Точечные рисунки более поздней версии в коллекции визуально лежат одна на растровые изображения в коллекции. При наличии нескольких точечные рисунки в разделе палец, нажимает клавишу на экране, верхний должен быть тем, которое управляется этой палец.

Также Обратите внимание, что `Pressed` помещает это растровое изображение в конец коллекции, чтобы он перемещался визуально в верхнюю часть накопление других точечных рисунков.

В `Moved` и `Released` события, `TouchAction` вызовов обработчика `ProcessingTouchEvent` метод в `TouchManipulationBitmap` так же, как более ранних программы.

Наконец `PaintSurface` вызовов обработчика `Paint` метод каждого `TouchManipulationBitmap` объекта:

```csharp
public partial class BitmapScatterViewPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKCanvas canvas = args.Surface.Canvas;
        canvas.Clear();

        foreach (TouchManipulationBitmap bitmap in bitmapCollection)
        {
            bitmap.Paint(canvas);
        }
    }
}
```

Код выполняет цикл по коллекции и отображает накопление точечных рисунков в начале коллекции в конец:

[![](touch-images/bitmapscatterview-small.png "Тройной снимок экрана страницы представление точечной диаграммы точечного рисунка")](touch-images/bitmapscatterview-large.png#lightbox "тройной снимок экрана страницы представление точечной диаграммы растрового изображения")

## <a name="single-finger-scaling"></a>Масштабирование касания одним пальцем

Выполняется операция масштабирования обычно требует жест жестом сжатия, с помощью двух пальцев. Тем не менее это можно реализовать, масштабирование с одним пальцем благодаря наличию палец переместить углов растрового изображения.

Это показано в **одном масштабируемом углу палец** страницы. Так как в этом примере используется несколько другой тип масштабирования, который реализован в `TouchManipulationManager` класса, она не использует этот класс или `TouchManipulationBitmap` класса. Вместо этого вся логика сенсорного ввода находится в файле кода. Это довольно простой логики, чем обычно, так как он отслеживает только одним пальцем одновременно и просто игнорирует любой дополнительный пальцами, которые могут прикосновения к экрану.

[ **SingleFingerCornerScale.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SingleFingerCornerScalePage.xaml) создает страницу `SKCanvasView` класса и создает `TouchEffect` для отслеживания событий сенсорного экрана:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Transforms.SingleFingerCornerScalePage"
             Title="Single Finger Corner Scale">

    <Grid BackgroundColor="White"
          Grid.Row="1">

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction"   />
        </Grid.Effects>
    </Grid>
</ContentPage>
```

[ **SingleFingerCornerScalePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SingleFingerCornerScalePage.xaml.cs) файл для загрузки ресурса точечного рисунка из **мультимедиа** каталога и отображается с использованием `SKMatrix` объект, определенный как поле:

```csharp
public partial class SingleFingerCornerScalePage : ContentPage
{
    SKBitmap bitmap;
    SKMatrix currentMatrix = SKMatrix.MakeIdentity();
    ···

    public SingleFingerCornerScalePage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        canvas.SetMatrix(currentMatrix);
        canvas.DrawBitmap(bitmap, 0, 0);
    }
    ···
}
```

Это `SKMatrix` объект изменяется с помощью сенсорного ввода логики, показано ниже.

Остальная часть файла кода является `TouchEffect` обработчик событий. Он начинает путем преобразования в настоящий момент палец для `SKPoint` значение. Для `Pressed` тип действия, обработчик проверяет, что нет другой палец касается экрана, и что палец находится в границах растрового изображения.

Является ключевой частью кода `if` инструкции, относящейся к два вызова `Math.Pow` метод. Это математические проверяет, является ли расположение палец за пределы эллипса, который заполняет растрового изображения. Если Да, то это операции масштабирования. Если палец находится рядом с одним из углов растрового изображения, а точки вращения определяется, что это противоположного угла. Если точка касания находится в пределах этого эллипса, это регулярное панорамирования операции:

```csharp
public partial class SingleFingerCornerScalePage : ContentPage
{
    SKBitmap bitmap;
    SKMatrix currentMatrix = SKMatrix.MakeIdentity();

    // Information for translating and scaling
    long? touchId = null;
    SKPoint pressedLocation;
    SKMatrix pressedMatrix;

    // Information for scaling
    bool isScaling;
    SKPoint pivotPoint;
    ···

    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point =
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                // Track only one finger
                if (touchId.HasValue)
                    return;

                // Check if the finger is within the boundaries of the bitmap
                SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
                rect = currentMatrix.MapRect(rect);
                if (!rect.Contains(point))
                    return;

                // First assume there will be no scaling
                isScaling = false;

                // If touch is outside interior ellipse, make this a scaling operation
                if (Math.Pow((point.X - rect.MidX) / (rect.Width / 2), 2) +
                    Math.Pow((point.Y - rect.MidY) / (rect.Height / 2), 2) > 1)
                {
                    isScaling = true;
                    float xPivot = point.X < rect.MidX ? rect.Right : rect.Left;
                    float yPivot = point.Y < rect.MidY ? rect.Bottom : rect.Top;
                    pivotPoint = new SKPoint(xPivot, yPivot);
                }

                // Common for either pan or scale
                touchId = args.Id;
                pressedLocation = point;
                pressedMatrix = currentMatrix;
                break;

            case TouchActionType.Moved:
                if (!touchId.HasValue || args.Id != touchId.Value)
                    return;

                SKMatrix matrix = SKMatrix.MakeIdentity();

                // Translating
                if (!isScaling)
                {
                    SKPoint delta = point - pressedLocation;
                    matrix = SKMatrix.MakeTranslation(delta.X, delta.Y);
                }
                // Scaling
                else
                {
                    float scaleX = (point.X - pivotPoint.X) / (pressedLocation.X - pivotPoint.X);
                    float scaleY = (point.Y - pivotPoint.Y) / (pressedLocation.Y - pivotPoint.Y);
                    matrix = SKMatrix.MakeScale(scaleX, scaleY, pivotPoint.X, pivotPoint.Y);
                }

                // Concatenate the matrices
                SKMatrix.PreConcat(ref matrix, pressedMatrix);
                currentMatrix = matrix;
                canvasView.InvalidateSurface();
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                touchId = null;
                break;
        }
    }
}
```

`Moved` Тип действия вычисляет матрицу, соответствующее действие касания с момента палец нажата экрана вплоть до этого времени. Он объединяет данную матрицу с матрицы в силе во время палец первого нажатия растрового изображения. Операции масштабирования всегда является относительно угла отличие, затронутых палец.

Для небольших или прямоугольный растровых изображений эллипса внутренних может занимают большую часть точечного рисунка и оставьте очень маленький областей в углах масштабирование растрового изображения. Может потребоваться, чтобы немного другой подход, в этом случае вы можете заменить этот всего `if` блок, который задает `isScaling` для `true` следующим кодом:

```csharp
float halfHeight = rect.Height / 2;
float halfWidth = rect.Width / 2;

// Top half of bitmap
if (point.Y < rect.MidY)
{
    float yRelative = (point.Y - rect.Top) / halfHeight;

    // Upper-left corner
    if (point.X < rect.MidX - yRelative * halfWidth)
    {
        isScaling = true;
        pivotPoint = new SKPoint(rect.Right, rect.Bottom);
    }
    // Upper-right corner
    else if (point.X > rect.MidX + yRelative * halfWidth)
    {
        isScaling = true;
        pivotPoint = new SKPoint(rect.Left, rect.Bottom);
    }
}
// Bottom half of bitmap
else
{
    float yRelative = (point.Y - rect.MidY) / halfHeight;

    // Lower-left corner
    if (point.X < rect.Left + yRelative * halfWidth)
    {
        isScaling = true;
        pivotPoint = new SKPoint(rect.Right, rect.Top);
    }
    // Lower-right corner
    else if (point.X > rect.Right - yRelative * halfWidth)
    {
        isScaling = true;
        pivotPoint = new SKPoint(rect.Left, rect.Top);
    }
}
```

Этот код фактически делит области растрового изображения в внутренних ромбовидной фигуры и четыре треугольника в углах. Это позволяет гораздо больших областях в углах взять и масштабирование растрового изображения.

## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [Вызов событий из эффектов](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
