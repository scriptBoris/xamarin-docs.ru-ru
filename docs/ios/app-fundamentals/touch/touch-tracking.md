---
title: Мультисенсорные палец отслеживания в Xamarin.iOS
description: В этом документе описывается, как отслеживать движение в мультисенсорные жесты в приложении Xamarin.iOS. Оно основано на красочное примера приложения.
ms.prod: xamarin
ms.assetid: 48E8B20D-0833-43D2-976A-0605DDB386E3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: a1ddcda84d51b5a8a9220558ddaf9476a2321ee8
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105055"
---
# <a name="multi-touch-finger-tracking-in-xamarinios"></a>Мультисенсорные палец отслеживания в Xamarin.iOS

_В этом документе показано, как отслеживать события касания с несколькими пальцами_

Бывают случаи, когда Мультисенсорные приложения нужно отслеживать движение, при перемещении одновременно на экране. Одним из типичных приложений является finger-paint программы. Требуется, чтобы пользователь мог для рисования с одним пальцем, но также для рисования пальцами несколько за один раз. Как приложение обрабатывает несколько событий сенсорного ввода, ему необходимо различать эти пальцами.

При первом касании пальцем экрана, iOS создает [ `UITouch` ](https://developer.xamarin.com/api/type/UIKit.UITouch/) объект для этого палец. Этот объект остается равным палец перемещается на экране, а затем поднимает на экране, после чего удаляется объект. Для отслеживания пальцами, программа не храните это `UITouch` объекта напрямую. Вместо этого можно использовать [ `Handle` ](https://developer.xamarin.com/api/property/Foundation.NSObject.Handle/) свойство типа `IntPtr` для уникальной идентификации этих `UITouch` объектов.

Почти всегда это программа, которая отслеживает движение ведение словаря для отслеживания сенсорного управления. Для приложения iOS является ключом словаря `Handle` значение, определяющее конкретной точки касания. Значение словаря зависит от приложения. В [FingerPaint](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint) программа, каждый палец stroke (от сенсорного ввода для освобождения) связана с объект, содержащий всю информацию, необходимую для отображения линии, нарисованных при помощи этого палец. Программа определяет небольшой `FingerPaintPolyline` класс для этой цели:

```csharp
class FingerPaintPolyline
{
    public FingerPaintPolyline()
    {
        Path = new CGPath();
    }

    public CGColor Color { set; get; }

    public float StrokeWidth { set; get; }

    public CGPath Path { private set; get; }
}
```

Каждой ломаной линии имеет цвет, ширину штриха и графики iOS [ `CGPath` ](https://developer.xamarin.com/api/type/CoreGraphics.CGPath/) объекта накапливаются и отобразить несколько точек линии, его написания.


Все остальные приведенный ниже код, содержащийся в `UIView` с именем `FingerPaintCanvasView`. Что класс поддерживает словарь объектов типа `FingerPaintPolyline` во время они активно рисуются с одним или несколькими пальцами:

```csharp
Dictionary<IntPtr, FingerPaintPolyline> inProgressPolylines = new Dictionary<IntPtr, FingerPaintPolyline>();
```

Этот словарь позволяет быстро получить представление `FingerPaintPolyline` на основе сведений, связанных с каждого пальца `Handle` свойство `UITouch` объекта.

`FingerPaintCanvasView` Класс также поддерживает `List` объект для ломаные линии, будут завершены:

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

Объекты в этом `List` находятся в том же порядке, что они были показаны.

`FingerPaintCanvasView` переопределяет пять методов, определенных `View`:

- [`TouchesBegan`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesBegan/p/Foundation.NSSet/UIKit.UIEvent/)
- [`TouchesMoved`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesMoved/p/Foundation.NSSet/UIKit.UIEvent/)
- [`TouchesEnded`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesEnded/p/Foundation.NSSet/UIKit.UIEvent/)
- [`TouchesCancelled`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesCancelled/p/Foundation.NSSet/UIKit.UIEvent/)
- [`Draw`](https://developer.xamarin.com/api/member/UIKit.UIView.Draw/p/CoreGraphics.CGRect/)

Различные `Touches` переопределения накапливать точки, составляющие ломаные линии.

[`Draw`] Переопределение рисует завершенного ломаные линии, а затем выполняется ломаные линии:

```csharp
public override void Draw(CGRect rect)
{
    base.Draw(rect);

    using (CGContext context = UIGraphics.GetCurrentContext())
    {
        // Stroke settings
        context.SetLineCap(CGLineCap.Round);
        context.SetLineJoin(CGLineJoin.Round);

        // Draw the completed polylines
        foreach (FingerPaintPolyline polyline in completedPolylines)
        {
            context.SetStrokeColor(polyline.Color);
            context.SetLineWidth(polyline.StrokeWidth);
            context.AddPath(polyline.Path);
            context.DrawPath(CGPathDrawingMode.Stroke);
        }

        // Draw the in-progress polylines
        foreach (FingerPaintPolyline polyline in inProgressPolylines.Values)
        {
            context.SetStrokeColor(polyline.Color);
            context.SetLineWidth(polyline.StrokeWidth);
            context.AddPath(polyline.Path);
            context.DrawPath(CGPathDrawingMode.Stroke);
        }
    }
}
```

Каждый из `Touches` переопределения потенциально сообщает действия нескольких пальцев, указанном по одному или нескольким `UITouch` объектов, хранящихся в `touches` аргумента метода. `TouchesBegan` Переопределения перебор этих объектов. Для каждого `UITouch` объекта, метод создает и инициализирует новую задачу `FingerPaintPolyline` объекта, включая хранение первоначального расположения элемента палец, полученный из `LocationInView` метод. Это `FingerPaintPolyline` объект добавляется `InProgressPolylines` словаря с помощью `Handle` свойство `UITouch` объекта в качестве ключа словаря:

```csharp
public override void TouchesBegan(NSSet touches, UIEvent evt)
{
    base.TouchesBegan(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        // Create a FingerPaintPolyline, set the initial point, and store it
        FingerPaintPolyline polyline = new FingerPaintPolyline
        {
            Color = StrokeColor,
            StrokeWidth = StrokeWidth,
        };

        polyline.Path.MoveToPoint(touch.LocationInView(this));
        inProgressPolylines.Add(touch.Handle, polyline);
    }
    SetNeedsDisplay();
}
```

Метод завершается путем вызова `SetNeedsDisplay` для создания вызова `Draw` переопределить и обновление экрана.

Перемещении пальца или пальцев на экране `View` получает несколько вызовов его `TouchesMoved` переопределить. Это переопределение аналогичным образом просматривает `UITouch` объектов, хранящихся в `touches` аргумента и добавляет текущее расположение палец графический контур:

```csharp
public override void TouchesMoved(NSSet touches, UIEvent evt)
{
    base.TouchesMoved(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        // Add point to path
        inProgressPolylines[touch.Handle].Path.AddLineToPoint(touch.LocationInView(this));
    }
    SetNeedsDisplay();
}
```

`touches` Коллекция содержит только те `UITouch` объектов, которые были перемещены с момента последнего вызова пальцы `TouchesBegan` или `TouchesMoved`. При обращении в службу `UITouch` объектов, соответствующий *все* пальцами, в настоящее время с экрана, эти сведения можно получить через `AllTouches` свойство `UIEvent` аргумента метода.

`TouchesEnded` Переопределение выполняет следующие задания. Он должен добавить точки графический контур и передачи `FingerPaintPolyline` объекта из `inProgressPolylines` словаря `completedPolylines` списка:

```csharp
public override void TouchesEnded(NSSet touches, UIEvent evt)
{
    base.TouchesEnded(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        // Get polyline from dictionary and remove it from dictionary
        FingerPaintPolyline polyline = inProgressPolylines[touch.Handle];
        inProgressPolylines.Remove(touch.Handle);

        // Add final point to path and save with completed polylines
        polyline.Path.AddLineToPoint(touch.LocationInView(this));
        completedPolylines.Add(polyline);
    }
    SetNeedsDisplay();
}
```

`TouchesCancelled` Переопределение обрабатывается просто оставления `FingerPaintPolyline` объекта в словаре:

```csharp
public override void TouchesCancelled(NSSet touches, UIEvent evt)
{
    base.TouchesCancelled(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        inProgressPolylines.Remove(touch.Handle);
    }
    SetNeedsDisplay();
}
```

В общей сложности эта обработка позволяет [FingerPaint](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint) программы отслеживать движение и отображать результаты на экране:

[![](touch-tracking-images/image01.png "Отслеживание движение и рисование на экране результатов")](touch-tracking-images/image01.png#lightbox)

Теперь вы знаете, как можно отслеживать отдельные пальцев на экране и различать их.



## <a name="related-links"></a>Связанные ссылки

- [Эквивалентное руководство по Xamarin Android](~/android/app-fundamentals/touch/touch-tracking.md)
- [FingerPaint (пример)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint)
