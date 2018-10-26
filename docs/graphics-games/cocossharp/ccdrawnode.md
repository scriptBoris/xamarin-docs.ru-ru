---
title: Рисование геометрия с CCDrawNode
description: В этом документе описывается CCDrawNode, который предоставляет методы для рисования простые объекты, такие как линий, кругов и треугольников.
ms.prod: xamarin
ms.assetid: 46A3C3CE-74CC-4A3A-AB05-B694AE182ADB
author: conceptdev
ms.author: crdun
ms.date: 03/24/2017
ms.openlocfilehash: b910e136366c429de8bd2ba1ac959882b4d7201d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123196"
---
# <a name="drawing-geometry-with-ccdrawnode"></a>Рисование геометрия с CCDrawNode

_`CCDrawNode` Предоставляет методы для рисования объектов-примитивов, таких как линий, кругов и треугольников._

`CCDrawNode` Класс в CocosSharp предоставляет несколько методов для рисования распространенных геометрических фигур. Он наследует от `CCNode` класса и обычно добавляется `CCLayer` экземпляров. В этом руководстве описывается, как использовать `CCDrawNode` экземпляров для выполнения пользовательской отрисовки. Он также предоставляет полный список функций рисования с помощью снимков экрана и примеры кода.


## <a name="creating-a-ccdrawnode"></a>Создание CCDrawNode

`CCDrawNode` Класс может использоваться для рисования геометрических объектов, таких как круги, прямоугольники и линии. Например, в следующем образце кода показано, как создать `CCDrawNode` экземпляр, который рисует окружность в `CCLayer` реализация класса:


```csharp
public class GameLayer : CCLayer
{
    public GameLayer ()
    {
        var drawNode = new CCDrawNode ();
        this.AddChild (drawNode);
        // Origin is bottom-left of the screen. This moves
        // the drawNode 100 pixels to the right and 100 pixels up
        drawNode.PositionX = 100;
        drawNode.PositionY = 100;

        drawNode.DrawCircle (
            center: new CCPoint (0, 0),
            radius: 20,
            color: CCColor4B.White);

    }
} 
```

Этот код формирует следующие круга во время выполнения.

![](ccdrawnode-images/image1.png "Этот код создает этот круг во время выполнения")


## <a name="draw-method-details"></a>Сведения о методе Draw

Давайте рассмотрим некоторые сведения, относящиеся к рисования с `CCDrawNode`:


### <a name="draw-methods-positions-are-relative-to-the-ccdrawnode"></a>Draw методы имеют относительно CCDrawNode

Все методы рисования требуется по крайней мере одной позиции значение для рисования. Это значение позиции определяется относительно `CCDrawNode` экземпляра. Это означает, что `CCDrawNode` сам имеет положение, а все рисуйте вызовы, выполняемые для `CCDrawNode` принять одно или несколько значений позиции. Чтобы понять, как объединить эти значения, давайте рассмотрим несколько примеров.

Сначала мы рассмотрим `DrawCircle` приведенном выше примере:


```csharp
...
drawNode.PositionX = 100;
drawNode.PositionY = 100;

drawNode.DrawCircle (center: new CCPoint (0, 0),
...
```

В этом случае `CCDrawNode` расположен в (100,100), и нарисованные круг с координатами (0,0) относительно `CCDrawNode`, полученный в круге, выровненные по центру 100 пикселей до, так и справа от нижний левый угол экрана игры.

`CCDrawNode` Также может быть размещен в начале координат (нижней левой части экрана), полагаясь на окружность для смещения:


```csharp
...
drawNode.PositionX = 0;
drawNode.PositionY = 0;

drawNode.DrawCircle (center: new CCPoint (50, 60),
...
```

Приведенный выше результаты в центре круга по 50 единиц (`drawNode.PositionX` + `CCPoint.X`) справа от левой части экрана и 60 (`drawNode.PositionY` + `CCPoint.Y`) единиц выше нижнего края экрана.

После вызова метод draw, графического объекта можно изменять только `CCDrawNode.Clear` вызывается метод, поэтому любые изменения положения необходимо выполнить на `CCDrawNode` сам.

Объекты, нарисованными `CCNodes` также будут затронуты `CCNode` экземпляра `Rotation` и `Scale` свойства.


### <a name="draw-methods-do-not-need-to-be-called-every-frame"></a>Методы рисования не обязательно должны вызываться на каждом кадре

Методы рисования должен вызываться только один раз для создания постоянного визуализации. В примере выше вызов `DrawCircle` в конструкторе класса `GameLayer` — `DrawCircle` не нужно вызывать каждый кадр, повторно рисовать окружность, когда экран обновляется.

Это отличается от методы рисования в MonoGame, обычно отображающего что-то на экран для только один кадр и который должен вызываться каждого кадра.

Если вызова методов рисования каждого кадра, а затем объекты будут со временем накапливаться внутри вызывающего `CCDrawNode` экземпляра, в результате чего перетаскивания в частоте кадров как рисуются дополнительные объекты.


### <a name="each-ccdrawnode-supports-multiple-draw-calls"></a>Каждый CCDrawNode поддерживает несколько вызовов draw

`CCDrawNode` экземпляры можно нарисовать несколько фигур. Это позволяет сложных визуальных объектов для охваченными в одном объекте. Например, следующий код может использоваться для подготовки к просмотру нескольких кругов с одним `CCDrawNode`:


```csharp
for (int i = 0; i < 8; i++)
{
    drawNode.DrawCircle (
        center: new CCPoint (i*15, 0),
        radius: 20,
        color: CCColor4B.White);
} 
```

Это приводит на следующем рисунке:

![](ccdrawnode-images/image2.png "Это приводит к на этом рисунке")


## <a name="draw-call-examples"></a>Примеры вызова Draw

В доступны следующие вызовы draw `CCDrawNode`:

- [`DrawCatmullRom`](#drawcatmullrom)
- [`DrawCircle`](#drawcircle)
- [`DrawCubicBezier`](#drawcubicbezier)
- [`DrawEllipse`](#drawellipse)
- [`DrawLineList`](#drawlinelist)
- [`DrawPolygon`](#drawpolygon)
- [`DrawQuadBezier`](#drawquadbezier)
- [`DrawRect`](#drawrect)
- [`DrawSegment`](#drawsegment)
- [`DrawSolidArc`](#drawsolidarc)
- [`DrawSolidCircle`](#drawsolidcircle)
- [`DrawTriangleList`](#drawtrianglelist)


### <a name="drawcardinalspline"></a>DrawCardinalSpline

`DrawCardinalSpline` Создает изогнутой линии через переменное число точек. 

`config` Параметр определяет, какие точки сплайн будут переданы. В приведенном ниже примере показано сплайна, который проходит через четыре точки.

`tension` Элементов управления параметрами как острые или round точек на сплайн отображаются. Объект `tension` значение 0 приведет к кривой сплайн и `tension` сплайна, нарисованными прямые линии и четкие края приведет к значение 1.

Хотя сплайны кривые линии, CocosSharp рисует сплайны с прямыми линиями. `segments` Параметр контролирует, сколько сегментов, используемую для отрисовки сплайн. Большее число приводит к плавно изогнутой сплайна за счет производительности. 

Пример кода:


```csharp
var splinePoints = new List<CCPoint> ();
splinePoints.Add (new CCPoint (0, 0));
splinePoints.Add (new CCPoint (50, 70));
splinePoints.Add (new CCPoint (0, 140));
splinePoints.Add (new CCPoint (100, 210));

drawNode.DrawCardinalSpline (
    config: splinePoints,
    tension: 0,
    segments: 64,
    color:CCColor4B.Red); 
```

![](ccdrawnode-images/image3.png "Параметр \"сегменты\" определяет количество сегментов, используемую для отрисовки сплайн")


### <a name="drawcatmullrom"></a>DrawCatmullRom

`DrawCatmullRom` Создает изогнутой линии через переменное число точек, аналогичную `DrawCardinalLine`. Этот метод не поддерживает параметр натяжение.

Пример кода:

```csharp
var splinePoints = new List<CCPoint> ();
splinePoints.Add (new CCPoint (0, 0));
splinePoints.Add (new CCPoint (80, 90));
splinePoints.Add (new CCPoint (100, 0));
splinePoints.Add (new CCPoint (0, 130)); 

drawNode.DrawCatmullRom (
    points: splinePoints,
    segments: 64); 
```

![](ccdrawnode-images/image4.png "DrawCatmullRom создает изогнутой линии через переменное число точек, аналогичную DrawCardinalLine")


### <a name="drawcircle"></a>DrawCircle

`DrawCircle` Создает периметре круга из заданного `radius`.

Пример кода:

```csharp
drawNode.DrawCircle (
    center:new CCPoint (0, 0),
    radius:20,
    color:CCColor4B.Yellow); 
```

![](ccdrawnode-images/image5.png "DrawCircle создает периметра окружности определенного радиуса")


### <a name="drawcubicbezier"></a>DrawCubicBezier

`DrawCubicBezier` Рисование кривой линии между двумя точками, с помощью контрольных точек для задания пути между двумя точками.

Пример кода:

```csharp
drawNode.DrawCubicBezier (
    origin: new CCPoint (0, 0),
    control1: new CCPoint (50, 150),
    control2: new CCPoint (250, 150),
    destination: new CCPoint (170, 0),
    segments: 64,
    lineWidth: 1,
    color: CCColor4B.Green); 
```

 ![](ccdrawnode-images/image6.png "DrawCubicBezier Проведение кривой линии между двумя точками")


### <a name="drawellipse"></a>DrawEllipse

`DrawEllipse` Создает контура *эллипс*, который часто называется овала (несмотря на то, что два, геометрически не идентичны). Форму эллипса могут определяться `CCRect` экземпляра.

Пример кода:


```csharp
drawNode.DrawEllipse (
    rect: new CCRect (0, 0, 130, 90),
    lineWidth: 2,
    color: CCColor4B.Gray); 
```

![](ccdrawnode-images/image8.png "DrawEllipse создает контура эллипса, который часто называют Овал")


### <a name="drawline"></a>DrawLine

`DrawLine` подключается к точкам с помощью строки заданной ширины. Этот метод аналогичен методу `DrawSegment`, за исключением того, он создает неструктурированный конечные точки, в отличие от функции round конечных точек.

Пример кода:


```csharp
drawNode.DrawLine (
    from: new CCPoint (0, 0),
    to: new CCPoint (150, 30),
    lineWidth: 5,
    color:CCColor4B.Orange); 
```

![](ccdrawnode-images/image9.png "DrawLine подключается к точкам с помощью строки заданной ширины")


### <a name="drawlinelist"></a>DrawLineList

`DrawLineList` создает несколько строк, подключив каждая пара пунктов `CCV3F_C4B` массива. `CCV3F_C4B` Структура содержит значения для положение и цвет. `verts` Параметр всегда должен содержать четное количество точек, так как каждая строка определяется двумя точками.

Пример кода:


```csharp
CCV3F_C4B[] verts = new CCV3F_C4B[] {
    // First line:
    new CCV3F_C4B( new CCPoint(0,0), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(30,60), CCColor4B.White),
    // second line, will blend from white to red:
    new CCV3F_C4B( new CCPoint(60,0), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(120,120), CCColor4B.Red)
};

drawNode.DrawLineList (verts); 
```

 ![](ccdrawnode-images/image10.png "Параметр verts всегда должен содержать четное количество точек, так как каждая строка определяется двумя точками")




### <a name="drawpolygon"></a>DrawPolygon

`DrawPolygon` Создает многоугольник заполненными и с контуром переменной ширины и цвета.

Пример кода:


```csharp
CCPoint[] verts = new CCPoint[] {
    new CCPoint(0,0),
    new CCPoint(0, 100),
    new CCPoint(50, 150),
    new CCPoint(100, 100),
    new CCPoint(100, 0)
};

drawNode.DrawPolygon (verts,
    count: verts.Length,
    fillColor: CCColor4B.White,
    borderWidth: 5,
    borderColor: CCColor4B.Red,
    closePolygon: true); 
```

![](ccdrawnode-images/image11.png "DrawPolygon создает многоугольник заполненными и с контуром переменной ширины и цвета")


### <a name="drawquadbezier"></a>DrawQuadBezier

`DrawQuadBezier` соединяющая две точки с линией. Он ведет себя так же, как `DrawCubicBezier` , но поддерживает только одну контрольную точку.

Пример кода:


```csharp
drawNode.DrawQuadBezier (
    origin:new CCPoint (0, 0),
    control:new CCPoint (200, 0),
    destination:new CCPoint (0, 300),
    segments:64,
    lineWidth:1,
    color:CCColor4B.White);
```

![](ccdrawnode-images/image12.png "DrawQuadBezier соединяющая две точки с линией")


### <a name="drawrect"></a>DrawRect

`DrawRect` Создает прямоугольник, заполненными с переменную ширину и цвет.

Пример кода: 


```csharp
var shape = new CCRect (
    0, 0, 100, 200);
drawNode.DrawRect(shape,
    fillColor:CCColor4B.Blue,
    borderWidth: 4,
    borderColor:CCColor4B.White); 
```

![](ccdrawnode-images/image13.png "DrawRect создает прямоугольник, заполненными с контуром переменной ширины и цвета")


### <a name="drawsegment"></a>DrawSegment

`DrawSegment` соединяющая две точки с помощью строки переменную ширину и цвет. Это похоже на `DrawLine`, за исключением того, он создает round конечных точек, а не неструктурированный конечных точек.

Пример кода:


```csharp
drawNode.DrawSegment (from: new CCPoint (0, 0),
    to: new CCPoint (100, 200),
    radius: 5,
    color:new CCColor4F(1,1,1,1)); 
```

![](ccdrawnode-images/image14.png "DrawSegment соединяющая две точки с помощью строки переменной ширины и цвета")


### <a name="drawsolidarc"></a>DrawSolidArc

`DrawSolidArc` Создает Клин заполненными цвета и radius.

Пример кода:


```csharp
drawNode.DrawSolidArc(
    pos:new CCPoint(100, 100),
    radius:200,
    startAngle:0,
    sweepAngle:CCMathHelper.Pi/2, // this is in radians, clockwise
    color:CCColor4B.White); 
```

![](ccdrawnode-images/image15.png "DrawSolidArc создает Клин заполненными цвета и radius")


### <a name="drawsolidcircle"></a>DrawSolidCircle

`DrawCircle` Создает заполненными круга определенного радиуса.

Пример кода:


```csharp
drawNode.DrawSolidCircle(
    pos: new CCPoint (100, 100),
    radius: 50,
    color: CCColor4B.Yellow); 
```

![](ccdrawnode-images/image16.png "DrawCircle создается круг заполняться из определенного радиуса")


### <a name="drawtrianglelist"></a>DrawTriangleList

`DrawTriangleList` Создает список треугольников. Каждый треугольник определяется три `CCV3F_C4B` экземпляров в массиве. Количество вершин в массиве, передаваемый `verts` параметра должно быть кратно трем. Обратите внимание, что только сведения о содержащихся в `CCV3F_C4B` — это положение verts и их цвет — `DrawTriangleList` метод не поддерживает рисования треугольники текстурами.

Пример кода:


```csharp
CCV3F_C4B[] verts = new CCV3F_C4B[] {
    // First triangle:
    new CCV3F_C4B( new CCPoint(0,0), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(30,60), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(60,0), CCColor4B.White),
    // second triangle, each point has different colors:
    new CCV3F_C4B( new CCPoint(90,0), CCColor4B.Yellow),
    new CCV3F_C4B( new CCPoint(120,60), CCColor4B.Red),
    new CCV3F_C4B( new CCPoint(150,0), CCColor4B.Blue)
};

drawNode.DrawTriangleList (verts); 
```

![](ccdrawnode-images/image17.png "DrawTriangleList создает список треугольники")


## <a name="summary"></a>Сводка

В этом руководстве описывается создание `CCDrawNode` и выполнения обработки на основе примитивом. Он содержит пример каждого из вызовов draw.

## <a name="related-links"></a>Связанные ссылки

- [CCDrawNode API](https://developer.xamarin.com/api/type/CocosSharp.CCDrawNode/)
- [Полный пример](https://developer.xamarin.com/samples/mobile/CCDrawNode/)
