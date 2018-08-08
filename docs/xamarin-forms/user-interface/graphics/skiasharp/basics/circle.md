---
title: Рисование простого кружка в SkiaSharp
description: В этой статье рассматриваются основы рисования SkiaSharp, включая полотна и графический редактор, в приложениях Xamarin.Forms и демонстрирует это с помощью примера кода.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: E3A4E373-F65D-45C8-8E77-577A804AC3F8
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: e06a1310fad01da11c8d8b115df504cc19426344
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615227"
---
# <a name="drawing-a-simple-circle-in-skiasharp"></a>Рисование простого кружка в SkiaSharp

_Познакомьтесь с основами SkiaSharp документе, включая полотна и рисования_

В этой статье понятия рисованию графических элементов в Xamarin.Forms с помощью SkiaSharp, включая создание `SKCanvasView` объекта для размещения графики, обработки `PaintSurface` событий и с помощью `SKPaint` объект для указания цвета и другие графические атрибуты.

[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) программа содержит все примеры кода для этой серии статей SkiaSharp. На первой странице размещен право **простого кружка** и вызывает класс страницы [ `SimpleCirclePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs). Этот код показывает, как нарисовать круг в центре страницы с радиусом 100 пикселей. Имеет красный цвет контура круга и внутренней окружности — синий.

![](circle-images/circleexample.png "Синий круг, выделены красным")

[ `SimpleCirle` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs) Страницы класс является производным от `ContentPage` и содержит два `using` директивы для SkiaSharp пространств имен:

```csharp
using SkiaSharp;
using SkiaSharp.Views.Forms;
```

Создает следующий конструктор класса [ `SKCanvasView` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.SKCanvasView/) объекта, подключает обработчик для [ `PaintSurface` ](https://developer.xamarin.com/api/event/SkiaSharp.Views.Forms.SKCanvasView.PaintSurface/) событий и наборы `SKCanvasView` объект как содержимое страницы:

```csharp
public SimpleCirclePage()
{
    Title = "Simple Circle";

    SKCanvasView canvasView = new SKCanvasView();
    canvasView.PaintSurface += OnCanvasViewPaintSurface;
    Content = canvasView;
}
```

`SKCanvasView` Занимает всю область содержимого страницы. Кроме того, можно объединить `SKCanvasView` с помощью других Xamarin.Forms `View` производные, как можно будет увидеть в других примерах.

`PaintSurface` Обработчик событий, где сделать все прорисовки. Этот метод обычно вызывается несколько раз во время работы программы, поэтому он должен поддерживать все сведения необходимые для воссоздания отображения графики:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
}

```

[ `SKPaintSurfaceEventArgs` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs/) Объект события имеет два свойства:

- [`Info`](https://developer.xamarin.com/api/property/SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Info/) типа [`SKImageInfo`](https://developer.xamarin.com/api/type/SkiaSharp.SKImageInfo/)
- [`Surface`](https://developer.xamarin.com/api/property/SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Surface/) типа [`SKSurface`](https://developer.xamarin.com/api/type/SkiaSharp.SKSurface/)

`SKImageInfo` Структура содержит сведения об области рисования, самое главное, это ширины и высоты в пикселях. `SKSurface` Представляет самой поверхности рисования. В этой программе поверхности рисования является дисплея, но в других программах `SKSurface` объект также может представлять точечный рисунок, который используется для рисования в SkiaSharp.

Наиболее важным свойством `SKSurface` — [ `Canvas` ](https://developer.xamarin.com/api/property/SkiaSharp.SKSurface.Canvas/) типа [ `SKCanvas` ](https://developer.xamarin.com/api/type/SkiaSharp.SKCanvas/). Этот класс представляет собой графики, рисование контекст, который используется для выполнения фактического рисования. `SKCanvas` Инкапсулирует состояние графики, включающий преобразований графики, так и обрезки.

Ниже приведен типичный начале `PaintSurface` обработчик событий:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();
    ...
}

```

[ `Clear` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Clear()/) Метод очищает canvas с прозрачным цветом. Перегрузка позволяет задать цвет фона для области.

Цель — для рисования синюю заливку красный кружок. Так как этого конкретного изображения содержит два различных цветов, задание необходимо реализовать в два этапа. Первым делом для рисования контура круга. Чтобы указать цвет и другие характеристики строки, вы создали и инициализировали [ `SKPaint` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPaint/) объекта:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = Color.Red.ToSKColor(),
        StrokeWidth = 25
    };
    ...
}
```

[ `Style` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.Style/) Свойство указывает, что вы хотите *stroke* строку (в данном случае контура круга) вместо *заливки* внутренней. Три члена [ `SKPaintStyle` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPaintStyle/) перечисления являются следующим образом:

- [`Fill`](https://developer.xamarin.com/api/field/SkiaSharp.SKPaintStyle.Fill/)
- [`Stroke`](https://developer.xamarin.com/api/field/SkiaSharp.SKPaintStyle.Stroke/)
- [`StrokeAndFill`](https://developer.xamarin.com/api/field/SkiaSharp.SKPaintStyle.StrokeAndFill/)

Значение по умолчанию — `Fill`. Третий параметр для обводки линии и цвет заливки.

Задайте [ `Color` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.Color/) свойства со значением типа [ `SKColor` ](https://developer.xamarin.com/api/type/SkiaSharp.SKColor/). Один из способов получения `SKColor` значение — преобразование Xamarin.Forms `Color` значение `SKColor` значение с помощью метода расширения [ `ToSKColor` ](https://developer.xamarin.com/api/member/SkiaSharp.Views.Forms.Extensions.ToSKColor/p/Xamarin.Forms.Color/). [ `Extensions` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.Extensions/) В класс `SkiaSharp.Views.Forms` пространство имен включает в себя другие методы, которые преобразуют между Xamarin.Forms и SkiaSharp значениями.

[ `StrokeWidth` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.StrokeWidth/) Свойство указывающее толщину линии. Здесь он становится равным 25 пикселей.

Использовать `SKPaint` объекта, чтобы рисовать окружность:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);
    ...
}
```

Координаты указываются относительно левого верхнего угла поверхность отображения. X координирует вправо и увеличение координаты Y направлена вниз. В рассказе о графике часто математической нотации (x, y) используется для обозначения точки. Точка (0, 0) находится в левом верхнем углу поверхности отображения и часто называется *origin*.

Первые два аргумента из `DrawCircle` указывают координаты X и Y центра круга. Это назначенные половины ширины и высоты поверхность отображения для размещения центра круга в центре поверхности отображения. Третий аргумент указывает радиус круга и последним аргументом является `SKPaint` объекта.

Для заливки внутренней окружности, его можно изменить два свойства класса `SKPaint` и вызовите `DrawCircle` еще раз. Этот код также показывает альтернативный способ получения `SKColor` значение из одного из многих поля [ `SKColors` ](https://developer.xamarin.com/api/type/SkiaSharp.SKColors/) структуры:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    paint.Style = SKPaintStyle.Fill;
    paint.Color = SKColors.Blue;
    canvas.DrawCircle(args.Info.Width / 2, args.Info.Height / 2, 100, paint);
}
```
На этот раз `DrawCircle` вызов заполняет элемент управления circle, с помощью нового свойства `SKPaint` объекта.

Ниже приведен программу на iOS, Android и универсальной платформы Windows.

[![](circle-images/simplecircle-small.png "Тройной снимок экрана страницы простого кружка")](circle-images/simplecircle-large.png#lightbox "тройной снимок экрана страницы простого кружка")

При запуске программы самостоятельно, вы можете включить телефон или симулятор сторону, чтобы увидеть, как перерисовке рисунок. Каждый раз, когда должна быть перерисована, рисунок `PaintSurface` обработчик событий вызывается снова.

`SKPaint` Нечто большее, чем коллекция рисования свойства объекта. Эти объекты являются очень простая. Вы можете повторно использовать `SKPaint` объектов, так как эта программа не, или можно создать несколько `SKPaint` объекты для отображения свойств различных комбинаций. Можно создать и инициализировать эти объекты за пределами `PaintSurface` обработчик событий и сохраните их как поля в класс страницы.

Несмотря на то, что ширина контура круга указывается как 25 пикселей &mdash; или части радиус круга &mdash; кажется более тонкие и имеется убедительная причина для этого: Половинная ширина линии замещается синий круг. Аргументы для `DrawCircle` метод определяет абстрактный геометрические координаты круга. Синий внутренней подбирается к такому измерению к ближайшей точки, но 25 пикселей всей структуры служит связующим звеном геометрические круг &mdash; половина на внутри, а другая половина — снаружи.

В приведенном ниже примере [интеграция с Xamarin.Forms](~/xamarin-forms/user-interface/graphics/skiasharp/basics/integration.md) статья демонстрирует это визуально.


## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
