---
title: Мультисенсорные пальцем отслеживания в Xamarin.Android
description: В этом разделе показано, как отслеживать события касания из нескольких пальцами
ms.prod: xamarin
ms.assetid: 048D51F9-BD6C-4B44-8C53-CCEF276FC5CC
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/25/2018
ms.openlocfilehash: 97e848e74a4513c55c0bf50258621c010b347fcd
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32436610"
---
# <a name="multi-touch-finger-tracking"></a>Отслеживание мультисенсорные пальцем

_В этом разделе показано, как отслеживать события касания из нескольких пальцами_

Бывают случаи, когда приложения мультисенсорные должна отслеживать отдельных пальцами при перемещении одновременно на экране. Одно приложение обычно представляет собой программу finger-paint. Необходимо, чтобы пользователь могли для рисования с одним пальцем, но также для рисования сразу несколько пальцами. Как приложение обрабатывает несколько событий сенсорного экрана, он должен определить, какие события соответствуют каждого пальца. Android предоставляет код для этой цели, но получения и обработки кода может быть немного сложным.

Все события, связанные с определенной пальцем код идентификатора остается неизменным. Идентификатор кода назначается во время палец первой касается экрана и становится недействительным после отрывает палец с экрана.
Эти коды, как правило, очень двухбайтовые целые числа, а Android использует их для более поздних событий сенсорного экрана.

Почти всегда программу, которая отслеживает отдельных пальцами поддерживают словарь отслеживания сенсорного ввода. Ключ словаря представляет код, определяющий определенного палец. Значение словаря зависит от приложения. В [FingerPaint](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint) программы, мазка пальцем (из касания для освобождения) связан с объектом, который содержит все сведения, необходимые для подготовки к просмотру линия, проведенная с этой пальцем. Программа определяет небольшой `FingerPaintPolyline` класса для этой цели:

```csharp
class FingerPaintPolyline
{
    public FingerPaintPolyline()
    {
        Path = new Path();
    }

    public Color Color { set; get; }

    public float StrokeWidth { set; get; }

    public Path Path { private set; get; }
}
```

Имеет каждой ломаной линии, цвета, толщину обводки и Android графики [ `Path` ](https://developer.xamarin.com/api/type/Android.Graphics.Path/) объекта накапливаются и просмотру нескольких точек линии его написания.

В оставшейся части приведенный ниже код содержится в `View` производный класс с именем `FingerPaintCanvasView`. Что класса поддерживают словарь объектов типа `FingerPaintPolyline` во время их активно отображаются с одного или нескольких пальцами:

```csharp
Dictionary<int, FingerPaintPolyline> inProgressPolylines = new Dictionary<int, FingerPaintPolyline>();
```

Этот словарь позволяет быстро получить представление `FingerPaintPolyline` сведения, связанные с определенной палец.

`FingerPaintCanvasView` Также поддерживает класс `List` объекта для завершенных многоугольника:

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

Объекты в этом `List` находятся в том же порядке, что они были отображены.

`FingerPaintCanvasView` переопределения двух методов, определенных `View`: [ `OnDraw` ](https://developer.xamarin.com/api/member/Android.Views.View.OnDraw/p/Android.Graphics.Canvas/) и [ `OnTouchEvent` ](https://developer.xamarin.com/api/member/Android.Views.View.OnTouchEvent/p/Android.Views.MotionEvent/).
В его `OnDraw` переопределение представление рисуется завершенного многоугольника, а затем рисуются многоугольника в процессе выполнения.

Переопределение метода `OnTouchEvent` метода начинается с получения `pointerIndex` значение из `ActionIndex` свойство. Это `ActionIndex` значение различает несколько пальцами, но не согласованы между несколькими событиями. По этой причине использование `pointerIndex` для получения указателя `id` значение из `GetPointerId` метод. Этот идентификатор *—* согласованы по нескольких событий:

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // Get the pointer index
    int pointerIndex = args.ActionIndex;

    // Get the id to identify a finger over the course of its progress
    int id = args.GetPointerId(pointerIndex);

    // Use ActionMasked here rather than Action to reduce the number of possibilities
    switch (args.ActionMasked)
    {
        // ...
    }

    // Invalidate to update the view
    Invalidate();

    // Request continued touch input
    return true;
}
```

Обратите внимание, что переопределенный метод использует `ActionMasked` свойство в `switch` инструкции, а не `Action` свойство. Далее описывается, почему это происходит:

При работе с несколькими touch `Action` свойство имеет значение `MotionEventsAction.Down` для первый палец коснуться экрана, а затем значения `Pointer2Down` и `Pointer3Down` как второй и третий пальцы также сенсорного экрана. Как сделать пальцами четвертый и пятый контакта, `Action` свойство содержит числовые значения, которые даже не соответствуют членам `MotionEventsAction` перечисления! Необходимо проверить значения битовых флагов в значениях интерпретировать их значения.

Точно так же, как пальцы привести контакта на экране `Action` свойство может принимать значения от `Pointer2Up` и `Pointer3Up` для второго и третьего пальцы, и `Up` для первый палец.

`ActionMasked` Свойство принимает на меньшее число значений, так как он предназначен для использования в сочетании с `ActionIndex` свойство для различения нескольких пальцами. Когда пальцами сенсорного экрана, свойство может равняться только `MotionEventActions.Down` для первый палец и `PointerDown` для последующих пальцами. Как пальцы закрыть окно, `ActionMasked` содержит значения `Pointer1Up` для последующих пальцев и `Up` для первый палец.

При использовании `ActionMasked`, `ActionIndex` коррелирующего последующих пальцы сенсорный ввод и оставьте экрана, но обычно не нужно использовать это значение, за исключением того, в качестве аргумента в другие методы `MotionEvent` объекта. Для мультисенсорные, одним из наиболее важным из этих методов является `GetPointerId` вызывается в приведенном выше коде. Метод возвращает значение, можно использовать для ключа словаря для связывания конкретных событий пальцами.

`OnTouchEvent` Переопределить в [FingerPaint](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint) процессов программ `MotionEventActions.Down` и `PointerDown` события идентично путем создания нового `FingerPaintPolyline` объекта и его добавления в словарь:

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // Get the pointer index
    int pointerIndex = args.ActionIndex;

    // Get the id to identify a finger over the course of its progress
    int id = args.GetPointerId(pointerIndex);

    // Use ActionMasked here rather than Action to reduce the number of possibilities
    switch (args.ActionMasked)
    {
        case MotionEventActions.Down:
        case MotionEventActions.PointerDown:

            // Create a Polyline, set the initial point, and store it
            FingerPaintPolyline polyline = new FingerPaintPolyline
            {
                Color = StrokeColor,
                StrokeWidth = StrokeWidth
            };

            polyline.Path.MoveTo(args.GetX(pointerIndex),
                                 args.GetY(pointerIndex));

            inProgressPolylines.Add(id, polyline);
            break;
        // ...
    }
    // ...        
}
```

Обратите внимание, что `pointerIndex` также используется для получения позиции пальцем в пределах представления. Все данные сенсорного ввода, связанный с `pointerIndex` значение. `id` Однозначно определяет пальцами по нескольким сообщениям, поэтому используемый для создания записи словаря.

Аналогичным образом `OnTouchEvent` также переопределять дескрипторы `MotionEventActions.Up` и `Pointer1Up` идентично путем передачи завершенной ломаной линии для `completedPolylines` коллекции, поэтому они могут отображаться во время `OnDraw` переопределения. Код также удаляет `id` входа из словаря:

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // ...
    switch (args.ActionMasked)
    {
        // ...
        case MotionEventActions.Up:
        case MotionEventActions.Pointer1Up:

            inProgressPolylines[id].Path.LineTo(args.GetX(pointerIndex),
                                                args.GetY(pointerIndex));

            // Transfer the in-progress polyline to a completed polyline
            completedPolylines.Add(inProgressPolylines[id]);
            inProgressPolylines.Remove(id);
            break;

        case MotionEventActions.Cancel:
            inProgressPolylines.Remove(id);
            break;
    }
    // ...        
}
```

Теперь для самое сложное.

Между поля со стрелками вниз и вверх события обычно существует много `MotionEventActions.Move` события. Они поставляются в рамках одного вызова `OnTouchEvent`, и они должны обрабатываться другим образом из `Down` и `Up` события. `pointerIndex` Значение полученный ранее из `ActionIndex` свойство должно игнорироваться. Вместо этого метода необходимо получить несколько `pointerIndex` значениями циклически переключается между 0 и `PointerCount` свойство и получение `id` для каждого из них `pointerIndex` значения:

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // ...
    switch (args.ActionMasked)
    {
        // ...
        case MotionEventActions.Move:

            // Multiple Move events are bundled, so handle them differently
            for (pointerIndex = 0; pointerIndex < args.PointerCount; pointerIndex++)
            {
                id = args.GetPointerId(pointerIndex);

                inProgressPolylines[id].Path.LineTo(args.GetX(pointerIndex),
                                                    args.GetY(pointerIndex));
            }
            break;
        // ...
    }
    // ...        
}
```

Этот тип обработки позволяет [FingerPaint](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint) программы, отслеживать отдельные пальцев и отображать результаты на экране:

[![Снимок экрана примера из примера FingerPaint](touch-tracking-images/image01.png)](touch-tracking-images/image01.png#lightbox)

Теперь вы знаете, как можно отслеживать отдельные пальца на экране и различать их.


## <a name="related-links"></a>Связанные ссылки

- [Эквивалент руководство Xamarin iOS](~/ios/app-fundamentals/touch/touch-tracking.md)
- [FingerPaint (пример)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)
