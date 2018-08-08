---
title: Преобразования матрицы в SkiaSharp
description: В этой статье подробно рассказывает о глубже преобразование skiasharp с матрица универсальный преобразования, а также демонстрирует это с помощью примера кода.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 9EDED6A0-F0BF-4471-A9EF-E0D6C5954AE4
author: charlespetzold
ms.author: chape
ms.date: 04/12/2017
ms.openlocfilehash: 8d5f1a08f7e1bff5ca2f9b696463bc03340476af
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615435"
---
# <a name="matrix-transforms-in-skiasharp"></a>Преобразования матрицы в SkiaSharp

_Погрузитесь глубже в преобразование skiasharp с матрица универсальный преобразования_

Всех преобразований, применяемых к `SKCanvas` объекта объединены в один экземпляр [ `SKMatrix` ](https://developer.xamarin.com/api/type/SkiaSharp.SKMatrix/) структуры. Это стандартный 3 x 3 матрицу преобразования аналогична семантике во всех системах современных двумерной графики.

Как вы убедились, преобразования можно использовать в SkiaSharp не зная о преобразовании матрицы, но матрица преобразования важна с теоретической точки зрения, и очень важно при использовании преобразования для изменения пути или для обработки сложных сенсорный ввод, оба которой описаны в этой статье, а также следующие.

![](matrix-images/matrixtransformexample.png "Битовая карта распространяется аффинного преобразования")

Применяется к текущей матрице преобразования `SKCanvas` доступна в любое время, обратившись к доступной только для чтения [ `TotalMatrix` ](https://developer.xamarin.com/api/property/SkiaSharp.SKCanvas.TotalMatrix/) свойство. Можно задать новый матрицы преобразования с помощью [ `SetMatrix` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.SetMatrix/p/SkiaSharp.SKMatrix/) метод и можно восстановить данную матрицу преобразования в значения по умолчанию путем вызова [ `ResetMatrix` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ResetMatrix/).

Единственным других `SKCanvas` член, который работает непосредственно с полотна к матрице преобразования является [ `Concat` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Concat/p/SkiaSharp.SKMatrix@/) который Сцепляет двух матриц путем умножения их друг с другом.

Матрица преобразования по умолчанию — единичная матрица и состоит из 1 в диагонали ячеек и значение 0 во всех остальных:

<pre>
| 1  0  0 |
| 0  1  0 |
| 0  0  1 |
</pre>

Можно создать с помощью статического единичной матрицей [ `SKMatrix.MakeIdentity` ](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeIdentity()/) метод:

```csharp
SKMatrix matrix = SKMatrix.MakeIdentity();
```

`SKMatrix` Конструктор по умолчанию не *не* возвращают единичной матрицей. Он возвращает матрицу с все ячейки, равным нулю. Не используйте `SKMatrix` конструктор, если вы планируете вручную настроить эти ячейки.

При визуализации графического объекта SkiaSharp каждой точки (x, y) эффективно преобразуется в матрицу с 1 по 3 с 1 в третьем столбце:

<pre>
| x  y  1 |
</pre>

Эта матрица с 1 по 3 представляет точку трехмерного с значение координаты Z, равным 1. Есть математические причин, (рассматривается далее), почему Двухмерная матрица преобразования требуется работать в трех измерениях. Можно представить данной матрицы 1 x 3, представляющего точку в трехмерной системе координат, но всегда на двумерную плоскость где значение Z равно 1.

Эта матрица с 1 по 3 затем умножается матрица преобразования, а результат является точкой, отображается на холсте:

<pre>
              | 1  0  0 |
| x  y  1 | × | 0  1  0 | = | x'  y'  z' |
              | 0  0  1 |
</pre>

Используется стандартный матрицы умножение, преобразованный точки являются следующим образом:

x' = x

y "= y

z "= 1

Это преобразование по умолчанию.

При `Translate` вызывается метод `SKCanvas` объекта, `tx` и `ty` аргументы `Translate` метод становятся двух первых ячейках в третьей строке матрицы преобразования:

<pre>
|  1   0   0 |
|  0   1   0 |
| tx  ty   1 |
</pre>

Умножение является теперь следующим образом:

<pre>
              |  1   0   0 |
| x  y  1 | × |  0   1   0 | = | x'  y'  z' |
              | tx  ty   1 |
</pre>

Ниже приведены преобразования формулы.

x' = x + tx

y "= y + ty

Коэффициенты масштабирования имеет значения по умолчанию 1. При вызове `Scale` новый метод `SKCanvas` объекта содержит матрицу преобразования результирующий `sx` и `sy` аргументов в диагонали ячеек:

<pre>
              | sx   0   0 |
| x  y  1 | × |  0  sy   0 | = | x'  y'  z' |
              |  0   0   1 |
</pre>

Преобразование формул, следующим образом:

x' = [Default] sx x

y "= sy [Default] y

Матрица преобразования после вызова метода `Skew` содержит два аргумента в ячейках матрицы рядом коэффициенты масштабирования:

<pre>
              │   1   ySkew   0 │
| x  y  1 | × │ xSkew   1     0 │ = | x'  y'  z' |
              │   0     0     1 │
</pre>

Преобразование формул:

x' = x + xSkew [Default] y

y "= ySkew [Default] x + y

Для вызова `RotateDegrees` или `RotateRadians` для угол α, матрица преобразования выглядит следующим образом:

<pre>
              │  cos(α)  sin(α)  0 │
| x  y  1 | × │ –sin(α)  cos(α)  0 │ = | x'  y'  z' |
              │    0       0     1 │
</pre>

Ниже приведены преобразования формулы.

x' = cos(α) [Default] x - [Default] sin(α) y

y "= sin(α) [Default] x - [Default] cos(α) y

Α-0 градусов, при единичной матрицей. Когда α 180 градусов, матрица преобразования выглядит следующим образом:

<pre>
| –1   0   0 |
|  0  –1   0 |
|  0   0   1 |
</pre>

Поворот на 180 градусов соответствует переворот объекта по горизонтали и по вертикали, который также достигается путем установления коэффициенты масштабирования – 1.

Все эти типы преобразований классифицируются как *аффинные* преобразования. Аффинные преобразования никогда не включать в себя третий столбец матрицы, которое остается в значения по умолчанию 0, 0 и 1. Статья [Non-аффинных преобразований](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md) обсуждаются неаффинные преобразования.

## <a name="matrix-multiplication"></a>Умножение матриц

Другое значительное преимущество с помощью матрицы преобразования — что составных преобразований можно получить, Перемножение матриц, который часто упоминается в документации как SkiaSharp *объединение*. Многие методы, связанные с преобразованием в `SKCanvas` см. «предварительного объединения» или «pre-concat.» Это относится порядку умножения, что важно, поскольку умножение матриц не является коммутативной.

Например, в документации по [ `Translate` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Translate/p/System.Single/System.Single/) метод говорит, что он «Pre-concats текущей матрицы с помощью заданного сдвига» при документации для [ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/System.Single/System.Single/) метод говорит, что он «Pre-concats Текущая матрица с заданным масштабом.»

Это означает, что указаны путем вызова метода преобразования — множитель (левый операнд) и текущая матрица преобразования множитель (правый операнд).

Предположим, что `Translate` вызывается следуют `Scale`:

```csharp
canvas.Translate(tx, ty);
canvas.Scale(sx, sy);
```

`Scale` Умножается на преобразование `Translate` составным преобразованием матрицы преобразования:

<pre>
| sx   0   0 |   |  1   0   0 |   | sx   0   0 |
|  0  sy   0 | × |  0   1   0 | = |  0  sy   0 |
|  0   0   1 |   | tx  ty   1 |   | tx  ty   1 |
</pre>

`Scale` может быть вызван перед `Translate` следующим образом:

```csharp
canvas.Scale(sx, sy);
canvas.Translate(tx, ty);
```

В этом случае изменять порядок умножения и коэффициенты масштабирования эффективно применяются к translation factors:

<pre>
|  1   0   0 |   | sx   0   0 |   |  sx      0    0 |
|  0   1   0 | × |  0  sy   0 | = |   0     sy    0 |
| tx  ty   1 |   |  0   0   1 |   | tx·sx  ty·sy  1 |
</pre>

Вот `Scale` метод с точкой вращения:

```csharp
canvas.Scale(sx, sy, px, py);
```

Это эквивалентно следующие вызовы translate и масштабирования:

```csharp
canvas.Translate(px, py);
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

Три матрицы преобразования умножаются в обратном порядке из как методы отображаются в коде:

<pre>
|  1    0   0 |   | sx   0   0 |   |  1   0  0 |   |    sx         0     0 |
|  0    1   0 | × |  0  sy   0 | × |  0   1  0 | = |     0        sy     0 |
| –px  –py  1 |   |  0   0   1 |   | px  py  1 |   | px–px·sx  py–py·sy  1 |
</pre>

### <a name="the-skmatrix-structure"></a>Структура SKMatrix

`SKMatrix` Структура определяет девять свойств чтения и записи типа `float` соответствующий девять ячеек матрицы преобразования:

<pre>
│ ScaleX  SkewY   Persp0 │
│ SkewX   ScaleY  Persp1 │
│ TransX  TransY  Persp2 │
</pre>

`SKMatrix` также определяет свойство, именуемое [ `Values` ](https://developer.xamarin.com/api/property/SkiaSharp.SKMatrix.Values/) типа `float[]`. Это свойство может использоваться для задания или получения девять значений за один раз в порядке `ScaleX`, `SkewX`, `TransX`, `SkewY`, `ScaleY`, `TransY`, `Persp0`, `Persp1`, и `Persp2`.

`Persp0`, `Persp1`, И `Persp2` ячеек рассматриваются в этой статье, [Non-аффинных преобразований](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md). Если эти ячейки имеют значения по умолчанию 0, 0 и 1, преобразование умножается координаты точки следующим образом:

<pre>
              │ ScaleX  SkewY   0 │
| x  y  1 | × │ SkewX   ScaleY  0 │ = | x'  y'  z' |
              │ TransX  TransY  1 │
</pre>

x' = ScaleX [Default] x + SkewX [Default] y + TransX

y "= SkewX [Default] x + ScaleY [Default] y + TransY

z "= 1

Это полный двухмерный аффинного преобразования. Аффинные преобразования сохраняет параллельные линии, это означает, что прямоугольник никогда не преобразуются в строки, отличное от параллелограмм.

`SKMatrix` Структура определяет несколько статических методов для создания `SKMatrix` значения. Они возвращают `SKMatrix` значения:

- [`MakeTranslation`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeTranslation/p/System.Single/System.Single/)
- [`MakeScale`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeScale/p/System.Single/System.Single/)
- [`MakeScale`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeScale/p/System.Single/System.Single/System.Single/System.Single/) с точки вращения
- [`MakeRotation`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeRotation/p/System.Single/) для угол в радианах
- [`MakeRotation`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeRotation/p/System.Single/System.Single/System.Single/) для угол в радианах с точкой вращения
- [`MakeRotationDegrees`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeRotationDegrees/p/System.Single/)
- [`MakeRotationDegrees`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeRotationDegrees/p/System.Single/System.Single/System.Single/) с точки вращения
- [`MakeSkew`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeSkew/p/System.Single/System.Single/)

`SKMatrix` также определяет несколько статических методов, осуществляющие сцепление двух матриц, что означает умножать их. Эти методы называются `Concat`, `PostConcat`, и `PreConcat`, и существуют две версии каждого из них. Эти методы имеют нет возвращаемых значений; Вместо этого они ссылаются существующие `SKMatrix` значения через `ref` аргументы. В следующем примере `A`, `B`, и `R` (для «результат»), все `SKMatrix` значения.

Два `Concat` методы вызываются следующим образом:

```csharp
SKMatrix.Concat(ref R, A, B);

SKMatrix.Concat(ref R, ref A, ref B);
```

Они выполняют следующие умножения:

R = B × A

Другие методы имеют только два параметра. Первый параметр изменены и при возврате из вызова метода, содержит произведение двух матриц. Два `PostConcat` методы вызываются следующим образом:

```csharp
SKMatrix.PostConcat(ref A, B);

SKMatrix.PostConcat(ref A, ref B);
```

Эти вызовы выполнить следующую операцию:

A = B ×

Два `PreConcat` методы похожи:

```csharp
SKMatrix.PreConcat(ref A, B);

SKMatrix.PreConcat(ref A, ref B);
```

Эти вызовы выполнить следующую операцию:

A = B × A

В версиях эти вызовы методов со всеми `ref` аргументы немного более эффективны с точки вызова базовых реализаций, но он может ввести в заблуждение кто-то читает этот код и при условии, что-либо с `ref` аргумент изменить с помощью метода. Кроме того, часто бывает удобно для передачи аргумент, который является результатом одного из `Make` методы, например:

```csharp
SKMatrix result;
SKMatrix.Concat(result, SKMatrix.MakeTranslation(100, 100),
                        SKMatrix.MakeScale(3, 3));
```

Это создает приведенной ниже таблице:

<pre>
│   3    0  0 │
│   0    3  0 │
│ 100  100  1 │
</pre>

Это преобразование масштабирования, умноженное на преобразования переноса. В данном конкретном случае `SKMatrix` структура предоставляет ярлык с методом [ `SetScaleTranslate` ](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.SetScaleTranslate/p/System.Single/System.Single/System.Single/System.Single/):

```csharp
SKMatrix R = new SKMatrix();
R.SetScaleTranslate(3, 3, 100, 100);
```

Это одна из несколько раз, когда можно безопасно использовать `SKMatrix` конструктор. `SetScaleTranslate` Метод задает все девять ячеек матрицы. Также можно безопасно использовать `SKMatrix` конструктор с помощью статического `Rotate` и `RotateDegrees` методов:

```csharp
SKMatrix R = new SKMatrix();

SKMatrix.Rotate(ref R, radians);

SKMatrix.Rotate(ref R, radians, px, py);

SKMatrix.RotateDegrees(ref R, degrees);

SKMatrix.RotateDegrees(ref R, degrees, px, py);
```

Эти методы выполняют *не* сцепления поворота преобразование, существующее преобразование. Методы задать все ячейки матрицы. Они являются функционально идентичен `MakeRotation` и `MakeRotationDegrees` методы за исключением того, что они не создавайте экземпляр `SKMatrix` значение.

Предположим, что у вас есть `SKPath` объект, который будет отображаться, но вы хотите использовать, что он имеет несколько по-разному ориентации или другая центральная точка. Все координаты этот путь можно изменить, вызвав [ `Transform` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.Transform/p/SkiaSharp.SKMatrix/) метод `SKPath` с `SKMatrix` аргумент. **Путь преобразования** странице рассказывается, как это сделать. [ `PathTransform` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/PathTransformPage.cs) Класса ссылки `HendecagramPath` объекта в поле но использует свой конструктор, чтобы применить преобразование к этому пути:

```csharp
public class PathTransformPage : ContentPage
{
    SKPath transformedPath = HendecagramArrayPage.HendecagramPath;

    public PathTransformPage()
    {
        Title = "Path Transform";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        SKMatrix matrix = SKMatrix.MakeScale(3, 3);
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeRotationDegrees(360f / 22));
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(300, 300));

        transformedPath.Transform(matrix);
    }
    ...
}
```

`HendecagramPath` Объект имеет центр в (0, 0), и количеством точек звезды наружу расширения центра на 100 единиц по всем направлениям. Это означает, что путь имеет положительных и отрицательных координат. **Путь преобразования** предпочитает star трижды как большие и все координаты положительное работе страницы. Кроме того он не хочет одну точку из типа "звезда", чтобы они указывали вверх. Компания хочет вместо этого для одной точки звезды пункт вниз. (Поскольку звездочка имеет одиннадцать точек, оба правила применить невозможно.) Этот сценарий требует поворот звездочки на 360 градусов деленное 22.

Конструктор создает `SKMatrix` объект из трех отдельных операций преобразования, использующих `PostConcat` метод в следующем формате, где A, B и C являются экземплярами `SKMatrix`:

```csharp
SKMatrix matrix = A;
SKMatrix.PostConcat(ref A, B);
SKMatrix.PostConcat(ref A, C);
```

Это ряд последовательных операции умножения, поэтому результат выглядит следующим образом:

A × B × C

Последовательные операции умножения помогают лучше понять назначение каждого преобразования. Преобразование масштаба увеличивает размер координат путь с коэффициентом 3, поэтому координаты в диапазоне от –300 до 300. Преобразование циклического сдвига вращается вокруг начала координат типа "звезда". Преобразование переноса затем перемещает его с 300 пикселей вправо и вниз, поэтому все координаты, становятся положительными.

Существуют другие последовательности, которые создают и та же матрица. Ниже приведен еще один.

```csharp
SKMatrix matrix = SKMatrix.MakeRotationDegrees(360f / 22);
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(100, 100));
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeScale(3, 3));
```

Это сначала поворачивает пути вокруг его центра и затем преобразовать его 100 пикселей вправо и вниз, так что все координаты имеют положительное значение. Затем типа "звезда" увеличивается размер относительно новых верхнего левого угла, который является точкой (0, 0).

`PaintSurface` Обработчик может просто отображать этот путь:

```csharp
public class PathTransformPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Magenta;
            paint.StrokeWidth = 5;

            canvas.DrawPath(transformedPath, paint);
        }
    }
}

```

Он отображается в левом верхнем углу холста:

[![](matrix-images/pathtransform-small.png "Тройной снимок экрана страницы путь преобразования")](matrix-images/pathtransform-large.png#lightbox "тройной снимок экрана страницы путь преобразования")

Конструктор эта программа применяет матрицы к пути, вызвав:

```csharp
transformedPath.Transform(matrix);
```

Путь *не* сохранить эту матрицу как свойство. Вместо этого преобразование применяется ко всем координат пути. Если `Transform` вызывается снова, применяется преобразование и является единственным способом, вы можете вернуться, применив другой матрицу, которая отменяет преобразование. К счастью `SKMatrix` структура определяет [ `TryInverse` ](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.TryInvert/p/SkiaSharp.SKMatrix/) метод, который получает матрицу, которая отменяет заданной матрицы:

```csharp
SKMatrix inverse;
bool success = matrix.TryInverse(out inverse);
```

Вызывается метод `TryInverse` , так как не все матрицы обратимой, но матрицу необратимым вряд ли будут использоваться для преобразования графики.

Также можно применить преобразование матрицы `SKPoint` значение, массив точек, `SKRect`, или даже просто одно число в программе. `SKMatrix` Структура поддерживает эти операции с коллекцией методов, которые начинаются со слова `Map`, такие:

```csharp
SKPoint transformedPoint = matrix.MapPoint(point);

SKPoint transformedPoint = matrix.MapPoint(x, y);

SKPoint[] transformedPoints = matrix.MapPoints(pointArray);

float transformedValue = matrix.MapRadius(floatValue);

SKRect transformedRect = matrix.MapRect(rect);
```

Если вы используете последним методом, не забывайте, что `SKRect` структура не может представлять поворачивается прямоугольник. Метод имеет смысл только `SKMatrix` значение, представляющее перевода и масштабирование.

### <a name="interactive-experimentation"></a>Интерактивные службы "Экспериментирование"

Одним из способов, чтобы почувствовать аффинного преобразования является интерактивного перемещения трех углов растрового изображения по экрану и видеть, какие преобразования результатов. Это идея **Показать аффинное** страницы. Для этой страницы требуется два класса, которые также используются в других демонстрационных роликов:

[ `TouchPoint` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/TouchPoint.cs) Класс отображает прозрачные круг, который можно перетаскивать по экрану. `TouchPoint` необходимо, `SKCanvasView` либо элемент, который является родительским элементом `SKCanvasView` имеют [ `TouchEffect` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/TouchEffect.cs) подключен. Задайте для свойства `Capture` значение `true`. В `TouchAction` обработчик событий, необходимо вызвать программу `ProcessTouchEvent` метод в `TouchPoint` для каждого `TouchPoint` экземпляр. Этот метод возвращает `true` Если событие сенсорного ввода привело к перемещении сенсорной точки. Кроме того `PaintSurface` необходимо вызвать обработчик `Paint` метод в каждом `TouchPoint` экземпляра, передавая ему `SKCanvas` объекта.

`TouchPoint` Демонстрирует общий способ, что визуальный элемент SkiaSharp можно инкапсулировать в отдельном классе. В классе можно определить свойства для задания характеристики визуального элемента, и метод, именуемый `Paint` с `SKCanvas` аргумент можно визуализировать его.

`Center` Свойство `TouchPoint` возвращает расположение объекта. Это свойство может задаваться для инициализации расположение; изменения свойств, когда пользователь перетаскивает круг вокруг холста.

**Показать страницу аффинные матрицы** также требуется [ `MatrixDisplay` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/MatrixDisplay.cs) класса. Этот класс отображает ячейки `SKMatrix` объекта. Он имеет два открытых метода: `Measure` получить размеры подготовленной матрицы и `Paint` для его отображения. Этот класс содержит `MatrixPaint` свойство типа `SKPaint` , можно заменить другой размер шрифта или цвета.

[ **ShowAffineMatrixPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml) файл экземпляр `SKCanvasView` и присоединяет `TouchEffect`. [ **ShowAffineMatrixPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml.cs) создает файл с выделенным кодом трех `TouchPoint` объектов, а затем устанавливает их в соответствующих углам три растрового изображения, которые загружаются из встроенного положения ресурс:

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    SKMatrix matrix;
    SKBitmap bitmap;
    SKSize bitmapSize;

    TouchPoint[] touchPoints = new TouchPoint[3];

    MatrixDisplay matrixDisplay = new MatrixDisplay();

    public ShowAffineMatrixPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }

        touchPoints[0] = new TouchPoint(100, 100);                  // upper-left corner
        touchPoints[1] = new TouchPoint(bitmap.Width + 100, 100);   // upper-right corner
        touchPoints[2] = new TouchPoint(100, bitmap.Height + 100);  // lower-left corner

        bitmapSize = new SKSize(bitmap.Width, bitmap.Height);
        matrix = ComputeMatrix(bitmapSize, touchPoints[0].Center,
                                           touchPoints[1].Center,
                                           touchPoints[2].Center);
    }
    ...
}
```

Аффинной матрицей однозначно определяется тремя точками. Три `TouchPoint` соответствуют объектам верхнего левого, правого верхнего и нижнего левого угла растрового изображения. Так как только аффинной матрицей способен преобразование прямоугольника в параллелограмму, четвертый точки подразумевается других трех. Конструктор завершается вызов `ComputeMatrix`, которая вычисляет ячейки `SKMatrix` объект из этих трех точек.

`TouchAction` Вызовов обработчика `ProcessTouchEvent` метод каждого `TouchPoint`. `scale` Значение преобразуется из координат Xamarin.Forms пикселей:

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        bool touchPointMoved = false;

        foreach (TouchPoint touchPoint in touchPoints)
        {
            float scale = canvasView.CanvasSize.Width / (float)canvasView.Width;
            SKPoint point = new SKPoint(scale * (float)args.Location.X,
                                        scale * (float)args.Location.Y);
            touchPointMoved |= touchPoint.ProcessTouchEvent(args.Id, args.Type, point);
        }

        if (touchPointMoved)
        {
            matrix = ComputeMatrix(bitmapSize, touchPoints[0].Center,
                                               touchPoints[1].Center,
                                               touchPoints[2].Center);
            canvasView.InvalidateSurface();
        }
    }
    ...
}
```

При наличии `TouchPoint` были перемещены, а затем вызывает метод `ComputeMatrix` еще раз и делает недействительной поверхность.

`ComputeMatrix` Метод определяет, содержится в разрешении этих трех точек матрицы. Матрица называется `A` преобразования один пиксель square прямоугольника в параллелограмму на основе трех точек, хотя преобразование масштаба вызывается `S` Масштабирующее растровое изображение в один пиксель square прямоугольник. Составной матрица является `S` × `A`:

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    ...
    static SKMatrix ComputeMatrix(SKSize size, SKPoint ptUL, SKPoint ptUR, SKPoint ptLL)
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

        SKMatrix result = SKMatrix.MakeIdentity();
        SKMatrix.Concat(ref result, A, S);
        return result;
    }
    ...
}
```

Наконец `PaintSurface` метод отображает точечный рисунок, на основе на данную матрицу, отображает матрицу, в нижней части экрана и отображает точки касания в трех углов растрового изображения:

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Display the bitmap using the matrix
        canvas.Save();
        canvas.SetMatrix(matrix);
        canvas.DrawBitmap(bitmap, 0, 0);
        canvas.Restore();

        // Display the matrix in the lower-right corner
        SKSize matrixSize = matrixDisplay.Measure(matrix);

        matrixDisplay.Paint(canvas, matrix,
            new SKPoint(info.Width - matrixSize.Width,
                        info.Height - matrixSize.Height));

        // Display the touchpoints
        foreach (TouchPoint touchPoint in touchPoints)
        {
            touchPoint.Paint(canvas);
        }
    }
  }
```

На экране iOS показано растрового изображения, при первой загрузке страницы, он показан двумя другими экранами после некоторые манипуляции.

[![](matrix-images/showaffinematrix-small.png "Тройной снимок экрана страницы Показать аффинное")](matrix-images/showaffinematrix-large.png#lightbox "тройной снимок экрана страницы Показать аффинную матрицу")

Несмотря на то, что кажется, будто точки касания перетащите углы растрового изображения, — это имитация изменений. Матрица, вычисленные на основе точки касания преобразует растрового изображения, так, чтобы углы совпадало с точки касания.

Более естественно пользователям перемещать, масштабировать и поворачивать точечные рисунки не перетаскивая углов, но с помощью одним или двумя пальцами непосредственно в объекте, чтобы перетащить, сжатия и поворот. Это рассматривается в следующей статье [Touch манипуляции](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/touch.md).

### <a name="the-reason-for-the-3-by-3-matrix"></a>Причина матрица 3 x 3

Можно ожидать, что система двухмерной графики потребует только матрицу преобразования 2 x 2:

<pre>
           │ ScaleX  SkewY  │
| x  y | × │                │ = | x'  y' |
           │ SkewX   ScaleY │
</pre>

Для масштабирования, поворота и наклона даже это работает, но он не может быть основная преобразований, в которой — это преобразование.

Проблема в том, что матрица 2 x 2 представляет *линейной* преобразования в двух направлениях. Линейное преобразование сохраняет некоторые основные арифметические операции, однако его влияние на что линейного преобразования никогда не изменяет точки (0, 0). Преобразование линейной делает преобразование невозможно.

В трех измерениях матрицу линейного преобразования выглядит следующим образом:

<pre>
              │ ScaleX  SkewYX  SkewZX │
| x  y  z | × │ SkewXY  ScaleY  SkewZY │ = | x'  y'  z' |
              │ SkewXZ  SkewYZ  ScaleZ │
</pre>

Ячейки с меткой `SkewXY` означает, что значение Наклоняет координату по оси X на основе значений Y; ячейки `SkewXZ` означает, что значение Наклоняет координату по оси X на основе значений Z; и значения наклона аналогичным образом для других `Skew` ячеек.

Можно ограничить данной матрицы трехмерные преобразования в двумерной плоскости, задав `SkewZX` и `SkewZY` 0, и `ScaleZ` 1:

<pre>
              │ ScaleX  SkewYX   0 │
| x  y  z | × │ SkewXY  ScaleY   0 │ = | x'  y'  z' |
              │ SkewXZ  SkewYZ   1 │
</pre>

Если двухмерной графики отображаются полностью в плоскость в трехмерном пространстве, где значение Z равно 1, преобразование умножения выглядит следующим образом:

<pre>
              │ ScaleX  SkewYX   0 │
| x  y  1 | × │ SkewXY  ScaleY   0 │ = | x'  y'  1 |
              │ SkewXZ  SkewYZ   1 │
</pre>

Все, что остается на двумерной плоскости Z равно 1, но `SkewXZ` и `SkewYZ` ячеек приобретает коэффициенты двумерного преобразования.

Это, как трехмерные преобразования линейной служит в качестве двухмерный нелинейные преобразования. (Используя аналогию, преобразований в 3D-графики основаны на матрицу 4 на 4.)

`SKMatrix` Структуры в SkiaSharp определяет свойства для этой третьей строки:

<pre>
              │ ScaleX  SkewY   Persp0 │
| x  y  1 | × │ SkewX   ScaleY  Persp1 │ = | x'  y'  z` |
              │ TransX  TransY  Persp2 │
</pre>

Ненулевые значения `Persp0` и `Persp1` привести преобразований, которые перемещают объекты вне двумерной плоскости, где значение Z равно 1. Что произойдет, если эти объекты перемещаются назад к этой плоскости рассматривается в статье на [Non-аффинных преобразований](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md).


## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
