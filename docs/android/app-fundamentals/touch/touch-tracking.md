---
title: Мультисенсорные палец отслеживания в Xamarin.Android
description: В этом разделе показано, как отслеживать события касания с несколькими пальцами
ms.prod: xamarin
ms.assetid: 048D51F9-BD6C-4B44-8C53-CCEF276FC5CC
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 34a9d2d9b8acb05a1b978a70e85038507032faaa
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50104691"
---
# <a name="multi-touch-finger-tracking"></a>Мультисенсорные палец отслеживания

_В этом разделе показано, как отслеживать события касания с несколькими пальцами_

Бывают случаи, когда Мультисенсорные приложения нужно отслеживать движение, при перемещении одновременно на экране. Одним из типичных приложений является finger-paint программы. Требуется, чтобы пользователь мог для рисования с одним пальцем, но также для рисования пальцами несколько за один раз. Как приложение обрабатывает несколько событий сенсорного ввода, он должен определить, какие события соответствуют каждого пальца. Android предоставляет код для этой цели, но получение и обработка этого кода может быть немного сложным.

Для всех событий, связанных с конкретным пальцем код идентификатора остается неизменным. Код идентификатора назначается в том случае, когда палец сначала касается экрана, а также становится недействительным после поднимает палец с экрана.
Эти коды, обычно очень двухбайтовые целые числа, а Android использует их повторно для более поздних событий касания.

Почти всегда это программа, которая отслеживает движение ведение словаря для отслеживания сенсорного управления. Ключ словаря является код, определяющий конкретной точки касания. Значение словаря зависит от приложения. В [FingerPaint](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint) программа, каждый палец stroke (от сенсорного ввода для освобождения) связана с объект, содержащий всю информацию, необходимую для отображения линии, нарисованных при помощи этого палец. Программа определяет небольшой `FingerPaintPolyline` класс для этой цели:

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

Каждой ломаной линии имеет цвет, ширину штриха и Android графики [ `Path` ](https://developer.xamarin.com/api/type/Android.Graphics.Path/) объекта накапливаются и отобразить несколько точек линии, его написания.

В оставшейся части приведенный ниже код, содержащийся в `View` с именем `FingerPaintCanvasView`. Что класс поддерживает словарь объектов типа `FingerPaintPolyline` во время они активно рисуются с одним или несколькими пальцами:

```csharp
Dictionary<int, FingerPaintPolyline> inProgressPolylines = new Dictionary<int, FingerPaintPolyline>();
```

Этот словарь позволяет быстро получить представление `FingerPaintPolyline` сведения, связанные с конкретным пальцем.

`FingerPaintCanvasView` Класс также поддерживает `List` объект для ломаные линии, будут завершены:

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

Объекты в этом `List` находятся в том же порядке, что они были показаны.

`FingerPaintCanvasView` переопределяет два метода, определяемый `View`: [`OnDraw`](https://developer.xamarin.com/api/member/Android.Views.View.OnDraw/p/Android.Graphics.Canvas/)
и [ `OnTouchEvent` ](https://developer.xamarin.com/api/member/Android.Views.View.OnTouchEvent/p/Android.Views.MotionEvent/).
В его `OnDraw` переопределения, представления, рисуется завершенного ломаные линии, а затем рисуются ломаные линии выполняется.

Переопределение метода `OnTouchEvent` метод начинается с получения `pointerIndex` значение из `ActionIndex` свойство. Это `ActionIndex` значение различает нескольких пальцев, но не согласованы между несколькими событиями. По этой причине использование `pointerIndex` для получения указателя `id` значение из `GetPointerId` метод. Этот идентификатор *является* согласованы по несколько событий:

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

Обратите внимание, что переопределение использует `ActionMasked` свойство в `switch` инструкции, а не `Action` свойство. Далее описывается, почему это происходит:

При работе с мультисенсорным вводом `Action` свойство имеет значение `MotionEventsAction.Down` для первый палец касаться экрана, а затем значения `Pointer2Down` и `Pointer3Down` как пальцы второй и третий также сенсорный экран. Принимая пальцы четвертый и пятый контакта, `Action` свойство содержит числовые значения, которые даже не соответствуют членами `MotionEventsAction` перечисления! Необходимо проверить значения битовые флаги, значения, чтобы интерпретировать разъяснения к ним.

Точно так же, как пальцы оставьте контактов с экраном `Action` свойство может принимать значения от `Pointer2Up` и `Pointer3Up` для второго и третьего пальцы, и `Up` для первый палец.

`ActionMasked` Свойство принимает на меньшее число значений, так как она предназначена для использования в сочетании с `ActionIndex` свойство, чтобы различать пальцы. Когда пальцы сенсорный экран, свойство можно только равно `MotionEventActions.Down` для первый палец и `PointerDown` для последующих пальцами. Как пальцы оставьте экран `ActionMasked` значения `Pointer1Up` для последующих пальцы и `Up` для первый палец.

При использовании `ActionMasked`, `ActionIndex` коррелирующего последующих пальцы для сенсорного ввода и оставьте установленным, экран, но обычно не нужно использовать это значение, за исключением в качестве аргумента в другие методы `MotionEvent` объекта. Для multi-Touch, одним из наиболее важных из этих методов является `GetPointerId` именем в приведенном выше коде. Метод возвращает значение, можно использовать для получения ключа словаря связываемый определенного события пальцами.

`OnTouchEvent` В Переопределите [FingerPaint](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint) процессов программ `MotionEventActions.Down` и `PointerDown` события идентично путем создания нового `FingerPaintPolyline` и его добавления в словарь:

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

Обратите внимание, что `pointerIndex` также используется для получения позиции палец в пределах представления. Всю информацию сенсорных связан с `pointerIndex` значение. `id` Однозначно определяет пальцами по нескольким сообщениям, используемое для создания записи словаря.

Аналогичным образом `OnTouchEvent` также переопределить дескрипторы `MotionEventActions.Up` и `Pointer1Up` одинаково, путем передачи завершенной ломаной линии для `completedPolylines` коллекции, поэтому они могут отображаться во время `OnDraw` переопределить. Код также удаляет `id` записи из словаря:

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

Теперь для это сложная часть.

Между вниз, а также события, обычно существует много `MotionEventActions.Move` события. Они объединяются в один вызов `OnTouchEvent`, и они должны обрабатываться другим образом из `Down` и `Up` события. `pointerIndex` Значения, полученного ранее `ActionIndex` свойство должно игнорироваться. Вместо этого метода необходимо получить несколько `pointerIndex` значения заключается в циклическом между 0 и `PointerCount` свойство, а затем получать `id` для каждого из этих `pointerIndex` значения:

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

Этот тип обработки позволяет [FingerPaint](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint) программы отслеживать движение и отображать результаты на экране:

[![Снимок экрана с примером из примера FingerPaint](touch-tracking-images/image01.png)](touch-tracking-images/image01.png#lightbox)

Теперь вы знаете, как можно отслеживать отдельные пальцев на экране и различать их.


## <a name="related-links"></a>Связанные ссылки

- [Руководство по эквивалентное Xamarin iOS](~/ios/app-fundamentals/touch/touch-tracking.md)
- [FingerPaint (пример)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)
