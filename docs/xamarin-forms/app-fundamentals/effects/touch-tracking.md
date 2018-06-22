---
title: Вызов события из эффектов
description: Эффект можно определить и вызвать событие сигнализации изменения в базовых собственного представления. Этой статье показано, как реализовать низкоуровневые пальцем мультисенсорные отслеживания и как создавать события, которые указывают действия касания.
ms.prod: xamarin
ms.assetid: 6A724681-55EB-45B8-9EED-7E412AB19DD2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/01/2017
ms.openlocfilehash: e363cae4dd72a25e4768395410d4e56a8db30eba
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "34050160"
---
# <a name="invoking-events-from-effects"></a>Вызов события из эффектов

_Эффект можно определить и вызвать событие сигнализации изменения в базовых собственного представления. Этой статье показано, как реализовать низкоуровневые пальцем мультисенсорные отслеживания и как создавать события, которые указывают действия касания._

Эффект, описанных в этой статье предоставляет доступ к событиям касания нижнего уровня. Эти события низкого уровня не доступны через существующий `GestureRecognizer` классов, но они необходимы для некоторых типов приложений. Например finger-paint приложения необходимо отслеживать отдельные пальцами по мере их перемещение на экране. Музыка клавиатуры должны обнаруживать касания и освобождает на отдельные ключи, а также gliding из одного раздела в другой glissando палец.

Эффект идеально подходит для пальцем мультисенсорные отслеживания, так как она может быть присоединена к любому элементу Xamarin.Forms.

## <a name="platform-touch-events"></a>События касания платформы

IOS, Android и включают в себя низкого уровня API, который позволяет приложениям обнаруживать универсальной платформы Windows touch действия. Эти платформы, которые все различать три основных типа touch событий:

- *Нажата*при касании пальцем экрана
- *Переместить*, при перемещении пальца касаясь экрана
- *Выпущена*, освобождение палец с экрана

В среде мультисенсорные несколько пальцами могут соприкасаться экрана, в то же время. Различные платформы включают код идентификатора (ID), приложения могут использовать для различения нескольких пальцами.

В iOS `UIView` класс определяет три переопределяемыми методами `TouchesBegan`, `TouchesMoved`, и `TouchesEnded` соответствующий эти три основные события. Статья [отслеживания пальцем мультисенсорные](~/ios/app-fundamentals/touch/touch-tracking.md) описывается использование этих методов. Тем не менее, приложения iOS не требуется переопределять класс, производный от `UIView` для использования этих методов. IOS `UIGestureRecognizer` также определяет эти же трех методов и можно присоединить экземпляр класса, производного от `UIGestureRecognizer` к любому `UIView` объекта.

В Android `View` класс определяет переопределяемый метод с именем `OnTouchEvent` для обработки всех операций сенсорный ввод. Тип действия касания определяется члены перечисления `Down`, `PointerDown`, `Move`, `Up`, и `PointerUp` как описано в статье [отслеживания пальцем мультисенсорные](~/android/app-fundamentals/touch/touch-tracking.md). Android `View` также определяет событие с именем `Touch` , позволяющий обработчик событий для присоединенных к любому `View` объекта.

В универсальной платформы Windows (UWP), `UIElement` класс определяет события с именем `PointerPressed`, `PointerMoved`, и `PointerReleased`. Они описаны в статье [обработки ввода указателя статье на MSDN](/windows/uwp/input-and-devices/handle-pointer-input/) и документацию по API для [ `UIElement` ](/uwp/api/windows.ui.xaml.uielement/) класса.

`Pointer` API универсальной платформы Windows предназначен для объединения мыши, прикосновения и ввода с помощью пера. По этой причине `PointerMoved` событие вызывается при перемещении указателя мыши по элементу, даже в том случае, если не нажата кнопка мыши. `PointerRoutedEventArgs` , Сопровождающее эти события имеет свойство с именем `Pointer` , имеющий свойство, именуемое `IsInContact` указывающая, если нажата кнопка мыши или палец с экрана.

Кроме того, UWP определяет две дополнительные события, с именем `PointerEntered` и `PointerExited`. Они указывают, когда мышь или палец перемещает от одного элемента в другую. Например рассмотрим два соседних элемента с именем A и B. Обработчики событий указателя установить оба элемента. При нажатии клавиши палец на A `PointerPressed` вызывается событие. Вызывается при перемещении пальца, A `PointerMoved` события. Если палец перемещается с A на B, A вызывает `PointerExited` событий и B вызывает `PointerEntered` событий. Если затем освобождается пальца, B вызывает `PointerReleased` событий.

IOS и Android платформы отличаются от UWP: представление, которое сначала получает вызов `TouchesBegan` или `OnTouchEvent` при касании пальцем представления по-прежнему получите все затрагивается действия, даже когда при перемещении пальца различными представлениями. UWP могут вести себя аналогичным образом, если приложение перехватывает указатель: В `PointerEntered` обработчик событий, вызовы элемент `CapturePointer` и затем возвращает все действия касания из этого пальцем.

UWP подход оказывается полезным для некоторых типов приложений, например, музыки клавиатуру. Каждый ключ можно обрабатывать события сенсорного ввода для этого ключа и обнаружить, когда палец вбок и фиксируется из одного раздела в другую с помощью `PointerEntered` и `PointerExited` события.

По этой причине эффект отслеживания сенсорный ввод, описанных в этой статье реализован подход UWP.

## <a name="the-touch-tracking-effect-api"></a>API отслеживания Touch эффект

[ **Touch отслеживания демонстрации эффекта** ](https://developer.xamarin.com/samples/xamarin-forms/effects/TouchTrackingEffectDemos/) образец содержит классы (и перечисления), реализующие низкого уровня отслеживания сенсорный ввод. Эти типы принадлежат к пространству имен `TouchTracking` и начинаются со слова `Touch`. **TouchTrackingEffectDemos** .NET Standard проект библиотеки содержит `TouchActionType` перечисления для типа событий сенсорного экрана:

```csharp
public enum TouchActionType
{
    Entered,
    Pressed,
    Moved,
    Released,
    Exited,
    Cancelled
}
```

Все платформы также включать событие, указывающее на отмену события касания.

`TouchEffect` Наследуется от класса в библиотеке .NET Standard `RoutingEffect` и определяет событие с именем `TouchAction` и метод с именем `OnTouchAction` , вызывающий `TouchAction` событий:

```csharp
public class TouchEffect : RoutingEffect
{
    public event TouchActionEventHandler TouchAction;

    public TouchEffect() : base("XamarinDocs.TouchEffect")
    {
    }

    public bool Capture { set; get; }

    public void OnTouchAction(Element element, TouchActionEventArgs args)
    {
        TouchAction?.Invoke(element, args);
    }
}
```

Также Обратите внимание, `Capture` свойство. Чтобы фиксировать события касания, приложение необходимо присвоить этому свойству `true` до `Pressed` события. В противном случае события касания ведут себя как в универсальной платформы Windows.

`TouchActionEventArgs` Класса в библиотеке .NET Standard содержит всю информацию, сопровождающее каждое событие:

```csharp
public class TouchActionEventArgs : EventArgs
{
    public TouchActionEventArgs(long id, TouchActionType type, Point location, bool isInContact)
    {
        Id = id;
        Type = type;
        Location = location;
        IsInContact = isInContact;
    }

    public long Id { private set; get; }

    public TouchActionType Type { private set; get; }

    public Point Location { private set; get; }

    public bool IsInContact { private set; get; }
}
```

Приложение может использовать `Id` свойства для отслеживания отдельных пальцами. Обратите внимание `IsInContact` свойство. Данное свойство всегда имеет `true` для `Pressed` событий и `false` для `Released` события. Кроме того, он всегда `true` для `Moved` события в iOS и Android. `IsInContact` Свойство может быть `false` для `Moved` событий на универсальную платформу Windows, когда программа выполняется на рабочем столе и указатель мыши перемещается без кнопки нажата.

Можно использовать `TouchEffect` класса в пользовательских приложениях, включая файл в проекте библиотеки .NET Standard решения и путем добавления экземпляра к `Effects` коллекции любого элемента Xamarin.Forms. Присоединить обработчик `TouchAction` событий для получения событий сенсорного экрана.

Для использования `TouchEffect` в приложении, необходимо также включены в реализации платформы **TouchTrackingEffectDemos** решения.

## <a name="the-touch-tracking-effect-implementations"></a>Реализации эффект отслеживания сенсорного ввода

IOS, Android и UWP реализации `TouchEffect` описанные ниже, начиная с простой реализации (UWP) и заканчивая реализацию операций ввода-вывода, так как это структурное более сложными, чем другие.

### <a name="the-uwp-implementation"></a>Реализация UWP

Реализация UWP `TouchEffect` является наиболее простым. Как обычно, является производным от класса `PlatformEffect` и включает два атрибута сборки:

```csharp
[assembly: ResolutionGroupName("XamarinDocs")]
[assembly: ExportEffect(typeof(TouchTracking.UWP.TouchEffect), "TouchEffect")]

namespace TouchTracking.UWP
{
    public class TouchEffect : PlatformEffect
    {
        ...
    }
}
```

`OnAttached` Переопределения сохраняет некоторые сведения в виде полей и присоединяет обработчики для всех событий указателя:

```csharp
public class TouchEffect : PlatformEffect
{
    FrameworkElement frameworkElement;
    TouchTracking.TouchEffect effect;
    Action<Element, TouchActionEventArgs> onTouchAction;

    protected override void OnAttached()
    {
        // Get the Windows FrameworkElement corresponding to the Element that the effect is attached to
        frameworkElement = Control == null ? Container : Control;

        // Get access to the TouchEffect class in the .NET Standard library
        effect = (TouchTracking.TouchEffect)Element.Effects.
                    FirstOrDefault(e => e is TouchTracking.TouchEffect);

        if (effect != null && frameworkElement != null)
        {
            // Save the method to call on touch events
            onTouchAction = effect.OnTouchAction;

            // Set event handlers on FrameworkElement
            frameworkElement.PointerEntered += OnPointerEntered;
            frameworkElement.PointerPressed += OnPointerPressed;
            frameworkElement.PointerMoved += OnPointerMoved;
            frameworkElement.PointerReleased += OnPointerReleased;
            frameworkElement.PointerExited += OnPointerExited;
            frameworkElement.PointerCanceled += OnPointerCancelled;
        }
    }
    ...
}    
```

`OnPointerPressed` Обработчик вызывает событие эффект путем вызова `onTouchAction` в `CommonHandler` метод:

```csharp
public class TouchEffect : PlatformEffect
{
    ...
    void OnPointerPressed(object sender, PointerRoutedEventArgs args)
    {
        CommonHandler(sender, TouchActionType.Pressed, args);

        // Check setting of Capture property
        if (effect.Capture)
        {
            (sender as FrameworkElement).CapturePointer(args.Pointer);
        }
    }
    ...
    void CommonHandler(object sender, TouchActionType touchActionType, PointerRoutedEventArgs args)
    {
        PointerPoint pointerPoint = args.GetCurrentPoint(sender as UIElement);
        Windows.Foundation.Point windowsPoint = pointerPoint.Position;  

        onTouchAction(Element, new TouchActionEventArgs(args.Pointer.PointerId,
                                                        touchActionType,
                                                        new Point(windowsPoint.X, windowsPoint.Y),
                                                        args.Pointer.IsInContact));
    }
}
```

`OnPointerPressed` также проверяет значение `Capture` свойства в классе эффект в .NET Standard библиотеку и вызывает `CapturePointer` их в случае `true`.

 Обработчики событий UWP еще проще:

```csharp
public class TouchEffect : PlatformEffect
{
    ...
    void OnPointerEntered(object sender, PointerRoutedEventArgs args)
    {
        CommonHandler(sender, TouchActionType.Entered, args);
    }
    ...
}
```

### <a name="the-android-implementation"></a>Реализация Android

Реализации Android и iOS являются обязательно более сложными, так как они должны реализовывать `Exited` и `Entered` события при перемещении пальца от одного элемента к другому. Обе реализации структурированы аналогичным образом.

Android `TouchEffect` класс устанавливает обработчик `Touch` событий:

```csharp
view = Control == null ? Container : Control;
...
view.Touch += OnTouch;
```

Этот класс также определяет два статического словаря:

```csharp
public class TouchEffect : PlatformEffect
{
    ...
    static Dictionary<Android.Views.View, TouchEffect> viewDictionary =
        new Dictionary<Android.Views.View, TouchEffect>();

    static Dictionary<int, TouchEffect> idToEffectDictionary =
        new Dictionary<int, TouchEffect>();
    ...
```

`viewDictionary` Возвращает новый объект каждый раз `OnAttached` переопределение вызывается:

```csharp
viewDictionary.Add(view, this);
```

Удалить элемент из словаря в `OnDetached`. Каждый экземпляр `TouchEffect` связан с отдельного представления эффект, к которому присоединена. Статический словарь позволяет `TouchEffect` экземпляра, для перечисления всех представлений и соответствующие им `TouchEffect` экземпляров. Это является необходимым условием для передачи событий от одного представления к другому.

Android назначает идентификатор код для события касания, позволяющая приложению для отслеживания отдельных пальцами. `idToEffectDictionary` Связывает этот код с `TouchEffect` экземпляра. Добавить элемент в этот словарь при `Touch` обработчик вызывается для печати пальцем:

```csharp
void OnTouch(object sender, Android.Views.View.TouchEventArgs args)
{
    ...
    switch (args.Event.ActionMasked)
    {
        case MotionEventActions.Down:
        case MotionEventActions.PointerDown:
            FireEvent(this, id, TouchActionType.Pressed, screenPointerCoords, true);

            idToEffectDictionary.Add(id, this);

            capture = libTouchEffect.Capture;
            break;

```

Элемент будет удален из `idToEffectDictionary` освобождения палец с экрана. `FireEvent` Метод просто накапливает все сведения, необходимые для вызова `OnTouchAction` метод:

```csharp
void FireEvent(TouchEffect touchEffect, int id, TouchActionType actionType, Point pointerLocation, bool isInContact)
{
    // Get the method to call for firing events
    Action<Element, TouchActionEventArgs> onTouchAction = touchEffect.libTouchEffect.OnTouchAction;

    // Get the location of the pointer within the view
    touchEffect.view.GetLocationOnScreen(twoIntArray);
    double x = pointerLocation.X - twoIntArray[0];
    double y = pointerLocation.Y - twoIntArray[1];
    Point point = new Point(fromPixels(x), fromPixels(y));

    // Call the method
    onTouchAction(touchEffect.formsElement,
        new TouchActionEventArgs(id, actionType, point, isInContact));
}
```

Всех прочих типов touch обрабатываются двумя различными способами: Если `Capture` свойство `true`, событий сенсорного ввода является довольно простой перевод `TouchEffect` сведения. Он становится более сложным, когда `Capture` — `false` поскольку к событиям касания может потребоваться переместить из одного представления к другому. Это является обязанностью `CheckForBoundaryHop` метод, который вызывается в процессе перемещения. Этот метод использует оба статическими словарями. Он выполняет перечисление `viewDictionary` определить представление, которое в настоящее время касается палец и использует `idToEffectDictionary` для сохранения текущего `TouchEffect` экземпляра (и следовательно, текущее представление) связанные с определенным кодом:

```csharp
void CheckForBoundaryHop(int id, Point pointerLocation)
{
    TouchEffect touchEffectHit = null;

    foreach (Android.Views.View view in viewDictionary.Keys)
    {
        // Get the view rectangle
        try
        {
            view.GetLocationOnScreen(twoIntArray);
        }
        catch // System.ObjectDisposedException: Cannot access a disposed object.
        {
            continue;
        }
        Rectangle viewRect = new Rectangle(twoIntArray[0], twoIntArray[1], view.Width, view.Height);

        if (viewRect.Contains(pointerLocation))
        {
            touchEffectHit = viewDictionary[view];
        }
    }

    if (touchEffectHit != idToEffectDictionary[id])
    {
        if (idToEffectDictionary[id] != null)
        {
            FireEvent(idToEffectDictionary[id], id, TouchActionType.Exited, pointerLocation, true);
        }
        if (touchEffectHit != null)
        {
            FireEvent(touchEffectHit, id, TouchActionType.Entered, pointerLocation, true);
        }
        idToEffectDictionary[id] = touchEffectHit;
    }
}
```

Если произошло изменение в `idToEffectionDictionary`, метод вызывает потенциально `FireEvent` для `Exited` и `Entered` для перемещения от одного представления к другому. Тем не менее, палец могла быть перемещена в область, занимаемая представления без прикрепленное `TouchEffect`, или из зоны с эффектом присоединенного к представлению.

Обратите внимание `try` и `catch` блокировки при доступе к представлению. На странице, осуществляется переход, а затем переходит назад на домашнюю страницу `OnDetached` метод не вызывается и элементы остаются в `viewDictionary` , но Android рассматривает их удаления.

### <a name="the-ios-implementation"></a>Реализация iOS

Реализация iOS схожа с Android реализацию разницей, что iOS `TouchEffect` класса необходимо создать класс, наследуемый от `UIGestureRecognizer`. Это класс в проекте iOS с именем `TouchRecognizer`. Данный класс содержит два статического словаря, хранящих `TouchRecognizer` экземпляров:

```csharp
static Dictionary<UIView, TouchRecognizer> viewDictionary =
    new Dictionary<UIView, TouchRecognizer>();

static Dictionary<long, TouchRecognizer> idToTouchDictionary =
    new Dictionary<long, TouchRecognizer>();
```

Значительная часть структуры этого `TouchRecognizer` класс похож на Android `TouchEffect` класса.

## <a name="putting-the-touch-effect-to-work"></a>Размещение эффект сенсорного ввода для работы

[ **TouchTrackingEffectDemos** ](https://developer.xamarin.com/samples/xamarin-forms/effects/TouchTrackingEffectDemos/) программа содержит пять страниц, проверка результатов отслеживания сенсорный ввод для выполнения стандартных задач.

**Перетаскивания BoxView** страницы можно добавить `BoxView` элементы `AbsoluteLayout` и перетащите их на экране. [XAML-файл](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/BoxViewDraggingPage.xaml) создаются два `Button` представлений для добавления `BoxView` элементы `AbsoluteLayout` и очистке `AbsoluteLayout`.

Метод в [файл кода программной части](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/BoxViewDraggingPage.xaml.cs) , добавляет новую `BoxView` для `AbsoluteLayout` также добавляет `TouchEffect` объект `BoxView` и присоединяет обработчик событий результат:

```csharp
void AddBoxViewToLayout()
{
    BoxView boxView = new BoxView
    {
        WidthRequest = 100,
        HeightRequest = 100,
        Color = new Color(random.NextDouble(),
                          random.NextDouble(),
                          random.NextDouble())
    };

    TouchEffect touchEffect = new TouchEffect();
    touchEffect.TouchAction += OnTouchEffectAction;
    boxView.Effects.Add(touchEffect);
    absoluteLayout.Children.Add(boxView);
}
```

`TouchAction` Обработчик событий обрабатывает все события касания для всех `BoxView` элементы, но требуется соблюдать осторожность, некоторые: он не может разрешить двух пальцев на отдельном `BoxView` так, как программа реализует только перетаскивания и будет двумя пальцами взаимодействовать друг с другом. По этой причине страницы определяет внедренный класс для каждого пальца обнаруженному:

```csharp
class DragInfo
{
    public DragInfo(long id, Point pressPoint)
    {
        Id = id;
        PressPoint = pressPoint;
    }

    public long Id { private set; get; }

    public Point PressPoint { private set; get; }
}

Dictionary<BoxView, DragInfo> dragDictionary = new Dictionary<BoxView, DragInfo>();
```

`dragDictionary` Содержит запись для каждого `BoxView` перетаскиваемых в настоящее время.

`Pressed` Касания добавляет элемент в этот словарь и `Released` действие удаляет его. `Pressed` Логики необходимо проверить, существует ли уже элемент в словаре, `BoxView`. В этом случае `BoxView` уже перетащить и новое событие является второй пальца на этом же `BoxView`. Для `Moved` и `Released` действия, обработчик событий должен установите флажок, если словарь запись для этого `BoxView` и что касание `Id` свойство, перетаскиваемый `BoxView` совпадает со структурой записи словаря:

```csharp
void OnTouchEffectAction(object sender, TouchActionEventArgs args)
{
    BoxView boxView = sender as BoxView;

    switch (args.Type)
    {
        case TouchActionType.Pressed:
            // Don't allow a second touch on an already touched BoxView
            if (!dragDictionary.ContainsKey(boxView))
            {
                dragDictionary.Add(boxView, new DragInfo(args.Id, args.Location));

                // Set Capture property to true
                TouchEffect touchEffect = (TouchEffect)boxView.Effects.FirstOrDefault(e => e is TouchEffect);
                touchEffect.Capture = true;
            }
            break;

        case TouchActionType.Moved:
            if (dragDictionary.ContainsKey(boxView) && dragDictionary[boxView].Id == args.Id)
            {
                Rectangle rect = AbsoluteLayout.GetLayoutBounds(boxView);
                Point initialLocation = dragDictionary[boxView].PressPoint;
                rect.X += args.Location.X - initialLocation.X;
                rect.Y += args.Location.Y - initialLocation.Y;
                AbsoluteLayout.SetLayoutBounds(boxView, rect);
            }
            break;

        case TouchActionType.Released:
            if (dragDictionary.ContainsKey(boxView) && dragDictionary[boxView].Id == args.Id)
            {
                dragDictionary.Remove(boxView);
            }
            break;
    }
}
```

`Pressed` Логику наборы `Capture` свойство `TouchEffect` объект `true`. Это приводит к доставки всех последующих событий для этого пальцем один и тот же обработчик событий.

`Moved` Перемещение логики `BoxView` путем изменения `LayoutBounds` вложенное свойство. `Location` Свойство аргументов события всегда имеет относительно `BoxView` перетаскиваемых и если `BoxView` перетаскивается с постоянной скоростью `Location` свойств последовательных событий будет примерно таким же. К примеру, если палец нажимает `BoxView` в его центре `Pressed` действие хранилища `PressPoint` свойство (50, 50), которая остается одинаковым для последующих событий. Если `BoxView` по диагонали перетаскивается с постоянной скоростью последующих `Location` свойства во время `Moved` операция может быть значения (55, 55), в этом случае `Moved` логики добавляет 5 горизонтальной и вертикальной позиции `BoxView`. Перемещает `BoxView` так, чтобы его центр снова непосредственно под палец.

Можно переместить несколько `BoxView` элементов одновременно с помощью разных пальцев.

[![](touch-tracking-images/boxviewdragging-small.png "Тройной снимок экрана со страницей перетаскивания BoxView")](touch-tracking-images/boxviewdragging-large.png#lightbox "тройной снимок экрана со страницей BoxView перетаскивания")

### <a name="subclassing-the-view"></a>Создание подклассов представления

Часто проще для элемента Xamarin.Forms для обработки собственных событий сенсорного ввода. **Перетаскиваемые перетаскивания BoxView** страница работает так же, как **перетаскивания BoxView** страницы, а также элементы, которые перетаскивания пользователя являются экземплярами [ `DraggableBoxView` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/DraggableBoxView.cs) класс, производный от `BoxView`:

```csharp
class DraggableBoxView : BoxView
{
    bool isBeingDragged;
    long touchId;
    Point pressPoint;

    public DraggableBoxView()
    {
        TouchEffect touchEffect = new TouchEffect
        {
            Capture = true
        };
        touchEffect.TouchAction += OnTouchEffectAction;
        Effects.Add(touchEffect);
    }

    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (!isBeingDragged)
                {
                    isBeingDragged = true;
                    touchId = args.Id;
                    pressPoint = args.Location;
                }
                break;

            case TouchActionType.Moved:
                if (isBeingDragged && touchId == args.Id)
                {
                    TranslationX += args.Location.X - pressPoint.X;
                    TranslationY += args.Location.Y - pressPoint.Y;
                }
                break;

            case TouchActionType.Released:
                if (isBeingDragged && touchId == args.Id)
                {
                    isBeingDragged = false;
                }
                break;
        }
    }
}
```

Конструктор создает и присоединяет `TouchEffect`и задает `Capture` свойства при сначала создать экземпляр этого объекта. Словарь не является обязательным, поскольку сам класс хранит `isBeingDragged`, `pressPoint`, и `touchId` значения, связанные с каждой пальцем. `Moved` Изменяет обработки `TranslationX` и `TranslationY` таким образом, чтобы логика будет работать даже в том случае, если родительский `DraggableBoxView` не `AbsoluteLayout`.

### <a name="integrating-with-skiasharp"></a>Интеграция с SkiaSharp

Следующие два демонстраций требуется графики, и они используют SkiaSharp для этой цели. Вы можете узнать о [с помощью SkiaSharp в Xamarin.Forms](~/xamarin-forms/user-interface/graphics/skiasharp/index.md) перед изучении этих примеров. Первые две статьи («Основы рисования SkiaSharp» и «SkiaSharp строки пути и») охватывает все, что вам потребуется здесь.

**Рисование эллипса** странице можно нарисовать эллипс, проведя пальца на экране. В зависимости от того, как переместить пальца, можно нарисовать эллипс из левого верхнего к нижнему правому или из другого угла в противоположный угол. Эллипс с случайных цвета и прозрачности.

[![](touch-tracking-images/ellipsedrawing-small.png "Тройной снимок экрана со страницей Рисование эллипса")](touch-tracking-images/ellipsedrawing-large.png#lightbox "тройной снимок экрана со страницей Рисование эллипса")

Если вы затем коснуться одного из эллипсов, его можно перетащить в другое место. Для этого необходимо подход, называемый «попадания,» которая включает в себя поиск графического объекта в определенной точке. Многоточие SkiaSharp не являются элементами Xamarin.Forms, поэтому они не могут выполнять собственные `TouchEffect` обработки. `TouchEffect` Необходимо применить ко всему `SKCanvasView` объекта.

[EllipseDrawPage.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/EllipseDrawingPage.xaml) создает файл `SKCanvasView` в одной ячейке `Grid`. `TouchEffect` Объект присоединяется к, `Grid`:

```xaml
<Grid x:Name="canvasViewGrid"
        Grid.Row="1"
        BackgroundColor="White">

    <skia:SKCanvasView x:Name="canvasView"
                        PaintSurface="OnCanvasViewPaintSurface" />
    <Grid.Effects>
        <tt:TouchEffect Capture="True"
                        TouchAction="OnTouchEffectAction" />
    </Grid.Effects>
</Grid>
```

В Android и универсальная платформа Windows `TouchEffect` можно подключить непосредственно к `SKCanvasView`, но на iOS, которое не работает. Обратите внимание, что `Capture` свойству `true`.

Каждый эллипса, который выполняет визуализацию SkiaSharp представляется объектом типа `EllipseDrawingFigure`:

```csharp
class EllipseDrawingFigure
{
    SKPoint pt1, pt2;

    public EllipseDrawingFigure()
    {
    }

    public SKColor Color { set; get; }

    public SKPoint StartPoint
    {
        set
        {
            pt1 = value;
            MakeRectangle();
        }
    }

    public SKPoint EndPoint
    {
        set
        {
            pt2 = value;
            MakeRectangle();
        }
    }

    void MakeRectangle()
    {
        Rectangle = new SKRect(pt1.X, pt1.Y, pt2.X, pt2.Y).Standardized;
    }

    public SKRect Rectangle { set; get; }

    // For dragging operations
    public Point LastFingerLocation { set; get; }

    // For the dragging hit-test
    public bool IsInEllipse(SKPoint pt)
    {
        SKRect rect = Rectangle;

        return (Math.Pow(pt.X - rect.MidX, 2) / Math.Pow(rect.Width / 2, 2) +
                Math.Pow(pt.Y - rect.MidY, 2) / Math.Pow(rect.Height / 2, 2)) < 1;
    }
}
```

`StartPoint` И `EndPoint` свойства используются, когда программа обрабатывает сенсорный ввод; `Rectangle` свойство используется для рисования эллипса. `LastFingerLocation` Свойство вступает в действие, при перетаскивании эллипса и `IsInEllipse` метод помогает в проверки нажатия. Метод возвращает `true` Если точка находится внутри эллипса.

[Файл кода программной части](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/EllipseDrawingPage.xaml.cs) поддерживает три коллекции:

```csharp
Dictionary<long, EllipseDrawingFigure> inProgressFigures = new Dictionary<long, EllipseDrawingFigure>();
List<EllipseDrawingFigure> completedFigures = new List<EllipseDrawingFigure>();
Dictionary<long, EllipseDrawingFigure> draggingFigures = new Dictionary<long, EllipseDrawingFigure>();
```

`draggingFigure` Словарь содержит подмножество `completedFigures` коллекции. SkiaSharp `PaintSurface` обработчик событий просто отображает объекты в эти `completedFigures` и `inProgressFigures` коллекции:

```csharp
SKPaint paint = new SKPaint
{
    Style = SKPaintStyle.Fill
};
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKCanvas canvas = args.Surface.Canvas;
    canvas.Clear();

    foreach (EllipseDrawingFigure figure in completedFigures)
    {
        paint.Color = figure.Color;
        canvas.DrawOval(figure.Rectangle, paint);
    }
    foreach (EllipseDrawingFigure figure in inProgressFigures.Values)
    {
        paint.Color = figure.Color;
        canvas.DrawOval(figure.Rectangle, paint);
    }
}
```

Является сложным в обработку touch `Pressed` обработки. Это место, куда выполняется проверка нажатия, но если код определяет эллипс под пальца пользователя, что эллипс можно перетащить только если он в настоящее время не перетаскивается с другим пальцем. Если нет эллипс под пальца пользователя, код начинает процесс рисования новый эллипса:

```csharp
case TouchActionType.Pressed:
    bool isDragOperation = false;

    // Loop through the completed figures
    foreach (EllipseDrawingFigure fig in completedFigures.Reverse<EllipseDrawingFigure>())
    {
        // Check if the finger is touching one of the ellipses
        if (fig.IsInEllipse(ConvertToPixel(args.Location)))
        {
            // Tentatively assume this is a dragging operation
            isDragOperation = true;

            // Loop through all the figures currently being dragged
            foreach (EllipseDrawingFigure draggedFigure in draggingFigures.Values)
            {
                // If there's a match, we'll need to dig deeper
                if (fig == draggedFigure)
                {
                    isDragOperation = false;
                    break;
                }
            }

            if (isDragOperation)
            {
                fig.LastFingerLocation = args.Location;
                draggingFigures.Add(args.Id, fig);
                break;
            }
        }
    }

    if (isDragOperation)
    {
        // Move the dragged ellipse to the end of completedFigures so it's drawn on top
        EllipseDrawingFigure fig = draggingFigures[args.Id];
        completedFigures.Remove(fig);
        completedFigures.Add(fig);
    }
    else // start making a new ellipse
    {
        // Random bytes for random color
        byte[] buffer = new byte[4];
        random.NextBytes(buffer);

        EllipseDrawingFigure figure = new EllipseDrawingFigure
        {
            Color = new SKColor(buffer[0], buffer[1], buffer[2], buffer[3]),
            StartPoint = ConvertToPixel(args.Location),
            EndPoint = ConvertToPixel(args.Location)
        };
        inProgressFigures.Add(args.Id, figure);
    }
    canvasView.InvalidateSurface();
    break;
```

Во втором примере SkiaSharp — **Paint пальцем** страницы. Можно выбрать цвет обводки и толщина из двух `Picker` представления и нарисуйте пальцами один или несколько:

[![](touch-tracking-images/fingerpaint-small.png "Тройной снимок экрана со страницей Paint пальцем")](touch-tracking-images/fingerpaint-large.png#lightbox "тройной снимок экрана со страницей пальцем рисования")

В этом примере также требуется отдельный класс для представления каждой строки рисования на экране:

```csharp
class FingerPaintPolyline
{
    public FingerPaintPolyline()
    {
        Path = new SKPath();
    }

    public SKPath Path { set; get; }

    public Color StrokeColor { set; get; }

    public float StrokeWidth { set; get; }
}
```

`SKPath` Объект используется для отображения каждой строки. [FingerPaint.xaml.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/FingerPaintPage.xaml.cs) файл поддерживает две коллекции этих объектов, один для этих ломаных линий в настоящее время рисования и другой для завершенного многоугольника:

```csharp
Dictionary<long, FingerPaintPolyline> inProgressPolylines = new Dictionary<long, FingerPaintPolyline>();
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

`Pressed` Обработки создает новую `FingerPaintPolyline`, вызовы `MoveTo` на объект для хранения начальной точки пути и добавляет этот объект в `inProgressPolylines` словаря. `Moved` Обработки вызовов `LineTo` пути объекта с новой позиции пальцем и `Released` обработки передает завершенного ломаной линии от `inProgressPolylines` для `completedPolylines`. Опять же фактический SkiaSharp, код отрисовки относительно проста:

```csharp
SKPaint paint = new SKPaint
{
    Style = SKPaintStyle.Stroke,
    StrokeCap = SKStrokeCap.Round,
    StrokeJoin = SKStrokeJoin.Round
};
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKCanvas canvas = args.Surface.Canvas;
    canvas.Clear();

    foreach (FingerPaintPolyline polyline in completedPolylines)
    {
        paint.Color = polyline.StrokeColor.ToSKColor();
        paint.StrokeWidth = polyline.StrokeWidth;
        canvas.DrawPath(polyline.Path, paint);
    }

    foreach (FingerPaintPolyline polyline in inProgressPolylines.Values)
    {
        paint.Color = polyline.StrokeColor.ToSKColor();
        paint.StrokeWidth = polyline.StrokeWidth;
        canvas.DrawPath(polyline.Path, paint);
    }
}
```

### <a name="tracking-view-to-view-touch"></a>Touch представления отслеживания

Приведенных выше примерах задано `Capture` свойство `TouchEffect` для `true`, либо если `TouchEffect` была создана или при `Pressed` произошло событие. Это гарантирует, что элемент с таким же получает все события, связанные с пальца, первого нажатия представления. Последний образец выполняет *не* задать `Capture` для `true`. Это вызывает другое поведение, если палец с экрана перемещается от одного элемента к другому. Элемент, из перемещении пальца получает событие с `Type` свойство `TouchActionType.Exited` и второй элемент получает событие с `Type` параметра `TouchActionType.Entered`.

Этот тип обработки touch очень полезен для клавиатуры музыки. Ключ должен иметь возможность обнаружить, когда она нажата, а также при палец слайдов из одного раздела в другой.

**Автоматической клавиатуры** страница определяет малой [ `WhiteKey` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/WhiteKey.cs) и [ `BlackKey` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/BlackKey.cs) классы, производные от [ `Key` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/Key.cs), который является производным от `BoxView`.

`Key` Класс можно будет использовать в программе музыкальные. Определяет открытого свойства с именем `IsPressed` и `KeyNumber`, который предназначен для присвоить значение установлено в стандарте MIDI код. `Key` Класс также определяет событие с именем `StatusChanged`, который вызывается при `IsPressed` изменения свойств.

Несколько пальцами допустимы для каждого ключа. По этой причине `Key` класс поддерживает `List` идентификаторов touch пальцев, в настоящее время, работая непосредственно этому разделу реестра:

```csharp
List<long> ids = new List<long>();
```

`TouchAction` Обработчик событий добавляет идентификатор к `ids` списка для обоих `Pressed` тип события и `Entered` типа, но только если `IsInContact` свойство `true` для `Entered` события. Идентификатор удаляется из `List` для `Released` или `Exited` событий:

```csharp
void OnTouchEffectAction(object sender, TouchActionEventArgs args)
{
    switch (args.Type)
    {
      case TouchActionType.Pressed:
          AddToList(args.Id);
          break;

        case TouchActionType.Entered:
            if (args.IsInContact)
            {
                AddToList(args.Id);
            }
            break;

        case TouchActionType.Moved:
            break;

        case TouchActionType.Released:
        case TouchActionType.Exited:
            RemoveFromList(args.Id);
            break;
    }
}

```

`AddToList` И `RemoveFromList` оба метода, установите флажок, если `List` изменило пустым и не пустой и если да, вызывает `StatusChanged` событий.

Различные `WhiteKey` и `BlackKey` элементы расположены на странице [XAML-файл](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/SilentKeyboardPage.xaml), который выглядит наилучшим образом при телефона удерживается в альбомном режиме:

[![](touch-tracking-images/silentkeyboard-small.png "Тройной снимок экрана со страницей автоматической клавиатуры")](touch-tracking-images/silentkeyboard-large.png#lightbox "тройной снимок экрана со страницей автоматической клавиатуры")

Если очистка палец для ключей, вы увидите с незначительными изменениями в цвет передачи событий из одного раздела в другой.

## <a name="summary"></a>Сводка

В этой статье продемонстрировал, как вызывать события в силу и записи и позволяющий реализует низкоуровневые мультисенсорные обработки.


## <a name="related-links"></a>Связанные ссылки

- [Мультисенсорные пальцем отслеживания в iOS](~/ios/app-fundamentals/touch/touch-tracking.md)
- [Мультисенсорные пальцем отслеживания в Android](~/android/app-fundamentals/touch/touch-tracking.md)
- [Эффект отслеживания сенсорного ввода (пример)](https://developer.xamarin.com/samples/xamarin-forms/effects/TouchTrackingEffectDemos/)
