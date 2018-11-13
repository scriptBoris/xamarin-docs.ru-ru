---
title: Вызов событий из эффектов
description: Эффект можно определить и вызвать событие, сигнализация изменения в базовом представлении собственного. В этой статье показано, как реализовать низкоуровневые палец мультисенсорное отслеживание и как для создания событий, которые указывают действия сенсорного ввода.
ms.prod: xamarin
ms.assetid: 6A724681-55EB-45B8-9EED-7E412AB19DD2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/01/2017
ms.openlocfilehash: 0a5e2c1a7a7807da91fd98e617467ea251a25bc0
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/11/2018
ms.locfileid: "51527408"
---
# <a name="invoking-events-from-effects"></a>Вызов событий из эффектов

_Эффект можно определить и вызвать событие, сигнализация изменения в базовом представлении собственного. В этой статье показано, как реализовать низкоуровневые палец мультисенсорное отслеживание и как для создания событий, которые указывают действия сенсорного ввода._

Эффект, описанные в этой статье предоставляет доступ к событиям касания низкого уровня. Эти низкоуровневые события недоступны через существующие `GestureRecognizer` классы, но они очень важны для некоторых типов приложений. Например finger-paint приложение должно отслеживать движение, при переходе на экране. Клавиатура музыки должны обнаруживать касания и освобождает на отдельные ключи, а также палец gliding с одного ключа на другой в глиссандо.

Эффект идеально подходит для мультисенсорного палец отслеживания, так как его можно будет присоединить к любому элементу Xamarin.Forms.

## <a name="platform-touch-events"></a>События касания платформы

IOS, Android и универсальной платформы Windows, все включают в себя низкоуровневые API, который позволяет приложениям обнаруживать touch действия. События касания этих платформ, которые все различать три основных типа:

- *Нажата*, при касании пальцем экрана
- *Переместить*, когда перемещает палец, касающийся экрана
- *Выпущен*, при выпуске пальца на экране

В среде мультисенсорные нескольких пальцев можно коснитесь экрана в то же время. Различные платформы включают код идентификатора (ID), который приложения могут использовать для различения нескольких пальцев.

В iOS `UIView` класс определяет три переопределяемых методов, `TouchesBegan`, `TouchesMoved`, и `TouchesEnded` соответствующий этих трех основных событий. Статья [Мультисенсорное отслеживание палец](~/ios/app-fundamentals/touch/touch-tracking.md) описывается использование этих методов. Тем не менее, приложения iOS не требуется переопределять класс, производный от `UIView` для использования этих методов. IOS `UIGestureRecognizer` также определяет эти же трех методов и можно присоединить экземпляр класса, производного от `UIGestureRecognizer` к любому `UIView` объекта.

В Android `View` класс определяет переопределяемый метод с именем `OnTouchEvent` для обработки всех операций сенсорного ввода. Тип действия касания определяется членов перечисления `Down`, `PointerDown`, `Move`, `Up`, и `PointerUp` как описано в статье [Мультисенсорное отслеживание палец](~/android/app-fundamentals/touch/touch-tracking.md). Android `View` также определяет событие, именуемое `Touch` , позволяющий обработчик событий для присоединения к любому `View` объекта.

В универсальной платформы Windows (UWP), `UIElement` класс определяет события с именем `PointerPressed`, `PointerMoved`, и `PointerReleased`. Они описаны в статье [обработки ввода указателя статье на MSDN](/windows/uwp/input-and-devices/handle-pointer-input/) и документацию по API для [ `UIElement` ](/uwp/api/windows.ui.xaml.uielement/) класса.

`Pointer` API в универсальной платформы Windows предназначен для объединения мыши, прикосновения и с помощью пера. По этой причине `PointerMoved` событие вызывается при перемещении указателя мыши по элементу, даже в том случае, если кнопка мыши отпущена. `PointerRoutedEventArgs` Объект, который прилагается к эти события имеет свойство с именем `Pointer` , имеет свойство, именуемое `IsInContact` которого указывает, если нажата кнопка мыши или палец с экрана.

Кроме того, универсальной платформы Windows определяет два дополнительные события с именем `PointerEntered` и `PointerExited`. Они указывают, когда курсор мыши или палец перемещает от одного элемента к другому. Например рассмотрим два соседних элемента с именем A и B. Оба элемента установить обработчики для событий указателя. При нажатии клавиши палец на объект, `PointerPressed` вызывается событие. При перемещении пальца, вызывающий объект `PointerMoved` события. Если палец движется от A до B, A вызывает `PointerExited` событий и B вызывает `PointerEntered` событий. Если затем выпуске пальца B вызывает `PointerReleased` событий.

IOS и Android платформы отличаются от UWP: представление, которое сначала получает вызов `TouchesBegan` или `OnTouchEvent` продолжается при касании пальцем представления для получения всех действий касания, даже если перемещает палец в различные представления. UWP может вести себя точно так же, если приложение перехватывает указатель: В `PointerEntered` обработчик событий, вызывает элемент `CapturePointer` и затем возвращает все операции сенсорного ввода из этого палец.

UWP подход оказывается очень полезным для некоторых типов приложений, например, клавиатура музыки. Каждый ключ может обрабатывать события касания для этого ключа, а также определять, когда палец перемещаются с одного ключа в другой с помощью `PointerEntered` и `PointerExited` события.

По этой причине эффект отслеживания сенсорного ввода, описанные в этой статье реализует подход универсальной платформы Windows.

## <a name="the-touch-tracking-effect-api"></a>API отслеживания Touch эффект

[ **Touch отслеживания демонстрации эффекта** ](https://developer.xamarin.com/samples/xamarin-forms/effects/TouchTrackingEffectDemos/) образец содержит классы (и перечисления), которые реализуют низкого уровня отслеживания сенсорного ввода. Эти типы принадлежат к пространству имен `TouchTracking` и начинаются со слова `Touch`. **TouchTrackingEffectDemos** проекта библиотеки .NET Standard включает в себя `TouchActionType` перечисление для типа событий сенсорного экрана:

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

Все платформы также событие, которое указывает, что событие сенсорного ввода была отменена.

`TouchEffect` Класс в библиотеке .NET Standard является производным от `RoutingEffect` и определяет событие, именуемое `TouchAction` и метод с именем `OnTouchAction` , вызывающий `TouchAction` событий:

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

Также Обратите внимание, что `Capture` свойство. Чтобы фиксировать события касания, приложение необходимо установить это свойство в `true` до `Pressed` событий. В противном случае события касания ведут себя как в универсальной платформы Windows.

`TouchActionEventArgs` Класс в библиотеке .NET Standard содержит все сведения, сопровождающие каждое событие:

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

Приложение может использовать `Id` свойства для отслеживания отдельных пальцами. Обратите внимание, что `IsInContact` свойство. Это свойство всегда имеет `true` для `Pressed` события и `false` для `Released` события. Это также всегда `true` для `Moved` события в iOS и Android. `IsInContact` Свойство может быть `false` для `Moved` события на универсальной платформе Windows, когда программа выполняется на рабочем столе и указатель мыши перемещается без кнопки нажата.

Можно использовать `TouchEffect` класс в собственных приложениях путем включения файла в проект библиотеки .NET Standard решения и путем добавления экземпляра к `Effects` коллекции любого элемента Xamarin.Forms. Присоединить обработчик для `TouchAction` событий для получения событий касания.

Чтобы использовать `TouchEffect` в приложении, вам также потребуется реализации платформы, включенные в **TouchTrackingEffectDemos** решения.

## <a name="the-touch-tracking-effect-implementations"></a>В реализациях эффект отслеживания сенсорного ввода

IOS, Android и UWP реализации `TouchEffect` описаны ниже, начиная с простейшая реализация (UWP) и заканчивая с реализацией iOS, так как это структурно более сложные, чем другие.

### <a name="the-uwp-implementation"></a>Реализация универсальной платформы Windows

Реализация UWP `TouchEffect` является наиболее простым. Как правило, этот класс является производным от `PlatformEffect` и включает в себя два атрибута сборки:

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

`OnAttached` Переопределение сохраняет некоторые сведения в виде полей и присоединение обработчиков всех событий указателя:

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

`OnPointerPressed` Обработчик вызывает эффект событие путем вызова `onTouchAction` в `CommonHandler` метод:

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

`OnPointerPressed` также проверяет значение параметра `Capture` свойства в классе эффект в библиотеке .NET Standard и вызовы `CapturePointer` в случае `true`.

 Обработчики событий универсальной платформы Windows еще проще:

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

Реализации Android и iOS являются обязательно более сложными, так как они должны реализовывать `Exited` и `Entered` события при перемещении пальца от одного элемента к другому. Обе реализации структурируются так же.

Android `TouchEffect` класс устанавливает обработчик `Touch` событий:

```csharp
view = Control == null ? Container : Control;
...
view.Touch += OnTouch;
```

Этот класс также определяет два статических словаря:

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

Запись удаляется из словаря в `OnDetached`. Каждый экземпляр `TouchEffect` связан с конкретное представление, к которой подключен эффект. Статический словарь позволяет `TouchEffect` экземпляра для перечисления во всех остальных представлений и соответствующие им `TouchEffect` экземпляров. Это является необходимым условием для передачи событий из одного представления к другому.

Android назначает идентификатор кода на события касания, позволяющая приложению отслеживать движение. `idToEffectDictionary` Связывает этот код с `TouchEffect` экземпляра. Элемент добавляется в этот словарь при `Touch` обработчик вызывается для нажмите пальца:

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

Элемент удален из `idToEffectDictionary` при отпускании палец с экрана. `FireEvent` Метод просто собирает все сведения, необходимые для вызова `OnTouchAction` метод:

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

Все другие типы touch обрабатываются двумя разными способами: Если `Capture` свойство `true`, событие сенсорного ввода является довольно простым переводом на `TouchEffect` сведения. Он становится более сложным, когда `Capture` является `false` так, как события касания может потребоваться переместить от одного представления к другому. Это ответственность за `CheckForBoundaryHop` метод, который вызывается во время события move. Этот метод использует оба статическими словарями. Он выполняет перечисление `viewDictionary` чтобы определить представление, которое в данный момент касается палец и использует `idToEffectDictionary` для хранения текущего `TouchEffect` экземпляра (и следовательно, текущее представление) связанные с определенным кодом:

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

При внесении изменений в `idToEffectDictionary`, метод вызывает потенциально `FireEvent` для `Exited` и `Entered` для передачи от одного представления к другому. Тем не менее, палец может были перемещены в область, занимаемая представления без прикрепленное `TouchEffect`, или из этой области в представление с эффектом подключен.

Обратите внимание, что `try` и `catch` блокировки при доступе к представлению. На странице, осуществляется переход, а затем переходит обратно к домашней странице `OnDetached` метод не вызывается и элементы остаются в `viewDictionary` , но Android рассматривает их удаления.

### <a name="the-ios-implementation"></a>Реализация iOS

Реализация iOS схожа с Android реализации, за одним исключением iOS `TouchEffect` класса необходимо создать экземпляр, производный от `UIGestureRecognizer`. Это класс в проект iOS, с именем `TouchRecognizer`. Данный класс содержит два статических словаря, в которых хранятся `TouchRecognizer` экземпляров:

```csharp
static Dictionary<UIView, TouchRecognizer> viewDictionary =
    new Dictionary<UIView, TouchRecognizer>();

static Dictionary<long, TouchRecognizer> idToTouchDictionary =
    new Dictionary<long, TouchRecognizer>();
```

Значительная часть структуры это `TouchRecognizer` класс похож на Android `TouchEffect` класса.

## <a name="putting-the-touch-effect-to-work"></a>Применение эффекта сенсорного ввода на практике

[ **TouchTrackingEffectDemos** ](https://developer.xamarin.com/samples/xamarin-forms/effects/TouchTrackingEffectDemos/) программа содержит пять страниц, проверка результатов отслеживания сенсорного ввода для выполнения стандартных задач.

**Перетаскивания BoxView** страницы можно добавить `BoxView` элементы `AbsoluteLayout` и затем перетащите их по экрану. [Файл XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/BoxViewDraggingPage.xaml) создаются два `Button` представления для добавления `BoxView` элементы `AbsoluteLayout` и очистке `AbsoluteLayout`.

Метод в [файл с выделенным кодом](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/BoxViewDraggingPage.xaml.cs) , добавляет новую `BoxView` для `AbsoluteLayout` также добавляет `TouchEffect` объект `BoxView` и присоединяет обработчик событий к эффект:

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

`TouchAction` Обработчик событий обрабатывает все события касания для всех `BoxView` элементов, но требуется соблюдать осторожность некоторые: он не позволяет двух пальцев на отдельном `BoxView` так, как программа реализует только перетаскивание, и будет двумя пальцами конфликтовать друг с другом. По этой причине страница определяет встроенном классе для каждого пальца, в настоящее время отслеживаются:

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

`dragDictionary` Содержит запись для каждого `BoxView` перетаскиваемых в настоящий момент.

`Pressed` Касания добавляет элемент в этот словарь и `Released` действие удаляет его. `Pressed` Логики необходимо проверить, существует ли уже элемент в словаре для этого `BoxView`. Если Да, `BoxView` уже перетаскивание, а также новый событие является пальца секунды на этом же `BoxView`. Для `Moved` и `Released` действия, обработчик событий должен проверить Если словарь содержит запись для этого `BoxView` и что касание `Id` , перетащить свойство `BoxView` совпадает со значением в записи словаря:

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

`Pressed` Наборы логики `Capture` свойство `TouchEffect` объект `true`. Это приводит к доставки все последующие события для этого палец тот же обработчик событий.

`Moved` Перемещение логики `BoxView` путем изменения `LayoutBounds` вложенного свойства зависимостей. `Location` Свойство аргументов события всегда имеет относительно `BoxView` перетаскиваемых и если `BoxView` перетаскивается с постоянной скоростью, `Location` свойств последовательных событий будет примерно таким же. Например, если палец нажимает `BoxView` в его центре `Pressed` действии хранилищ `PressPoint` свойство (50, 50), которая остается одинаковым для последующих событий. Если `BoxView` перетаскивается по диагонали с постоянной скоростью, последующие `Location` свойства во время `Moved` операция может быть значения (55, 55), в этом случае `Moved` логики добавляется к горизонтальным и вертикальным положение 5`BoxView`. Перемещает `BoxView` таким образом, его центра снова непосредственно под пальцем.

Вы можете переместить несколько `BoxView` элементов одновременно с помощью разных пальцев.

[![](touch-tracking-images/boxviewdragging-small.png "Тройной снимок экрана страницы перетаскивания BoxView")](touch-tracking-images/boxviewdragging-large.png#lightbox "тройной снимок экрана страницы BoxView перетаскивания")

### <a name="subclassing-the-view"></a>Создание подкласса представления

Часто проще обрабатывать свои собственные события сенсорного ввода с помощью Xamarin.Forms элемента. **Перетаскиваемой перетаскивания BoxView** страница функционирует так же, как **перетаскивания BoxView** страницы, а также элементы, которые пользователь перетаскивания являются экземплярами [ `DraggableBoxView` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/DraggableBoxView.cs) класс, производный от `BoxView`:

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

Конструктор создает и присоединяет `TouchEffect`и задает `Capture` свойства, если сначала создать экземпляр этого объекта. Словарь не является обязательным, поскольку сам класс хранит `isBeingDragged`, `pressPoint`, и `touchId` значения, связанные с каждого пальца. `Moved` Изменяет обработки `TranslationX` и `TranslationY` таким образом, чтобы логика будет работать даже в том случае, если родительский `DraggableBoxView` не `AbsoluteLayout`.

### <a name="integrating-with-skiasharp"></a>Интеграция с SkiaSharp

Следующие два демонстраций требуют графики, и занимают SkiaSharp для этой цели. Вы можете получить сведения о [использование SkiaSharp в Xamarin.Forms](~/xamarin-forms/user-interface/graphics/skiasharp/index.md) прежде, чем при изучении этих примеров. Первые две статьи («Основы рисования в SkiaSharp» и «SkiaSharp строки пути и») охватывают все, что вам потребуется здесь.

**Рисование эллипса** страница позволяет нарисовать эллипс, проводя пальцем пальца на экране. В зависимости от того, как переместить палец с экрана, можно нарисовать эллипс в левом верхнем углу в правом нижнем углу или из любого другого угла противоположного угла. Эллипс с случайные значения цвета и прозрачности.

[![](touch-tracking-images/ellipsedrawing-small.png "Тройной снимок экрана страницы Рисование эллипса")](touch-tracking-images/ellipsedrawing-large.png#lightbox "тройной снимок экрана страницы Рисование эллипса")

Если вы затем коснуться одного из эллипсов, его можно перетащить в другое расположение. Для этого подход, называемый «попадания, «который включает в себя поиск графического объекта в определенной точке. Многоточие SkiaSharp не являются элементы Xamarin.Forms, поэтому они не могут выполнять собственные `TouchEffect` обработки. `TouchEffect` Необходимо применить ко всей `SKCanvasView` объекта.

[EllipseDrawPage.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/EllipseDrawingPage.xaml) файл экземпляр `SKCanvasView` в одной ячейке `Grid`. `TouchEffect` Объект присоединяется к, `Grid`:

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

В Android и универсальной платформы Windows `TouchEffect` можно подключить напрямую к `SKCanvasView`, но на iOS, которое не работает. Обратите внимание, что `Capture` свойству `true`.

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

`StartPoint` И `EndPoint` свойства используются в том случае, когда программа обрабатывает сенсорный ввод; `Rectangle` свойство используется для рисования эллипса. `LastFingerLocation` Свойство вступает в действие, когда перетаскивается эллипса и `IsInEllipse` метод помогает в попадания. Этот метод возвращает `true` Если точка находится внутри эллипса.

[Файл с выделенным кодом](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/EllipseDrawingPage.xaml.cs) поддерживает три коллекции:

```csharp
Dictionary<long, EllipseDrawingFigure> inProgressFigures = new Dictionary<long, EllipseDrawingFigure>();
List<EllipseDrawingFigure> completedFigures = new List<EllipseDrawingFigure>();
Dictionary<long, EllipseDrawingFigure> draggingFigures = new Dictionary<long, EllipseDrawingFigure>();
```

`draggingFigure` Словарь содержит подмножество `completedFigures` коллекции. SkiaSharp `PaintSurface` обработчик просто отображает объекты в этих `completedFigures` и `inProgressFigures` коллекций:

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

Самая сложная часть обработку сенсорного ввода является `Pressed` обработки. Именно для этого выполняется проверка нажатия, но если код обнаруживает эллипс под пальцем пользователя, что эллипс можно перетащить только если он в настоящее время не перетаскивается с другой палец. Если нет эллипс под пальцем пользователя, код начинает процесс рисования новый эллипс.

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

Во втором примере SkiaSharp является **Рисование пальцами** страницы. Цвет обводки и ширины штриха можно выбрать из двух `Picker` представлений и нарисуйте с одним или несколькими пальцами:

[![](touch-tracking-images/fingerpaint-small.png "Тройной снимок экрана страницы Рисование пальцами")](touch-tracking-images/fingerpaint-large.png#lightbox "тройной снимок экрана страницы Рисование пальцами")

В этом примере также требуется отдельный класс для представления каждой строки, рисования на экране:

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

`SKPath` Объект используется для отрисовки каждой строки. [FingerPaint.xaml.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/FingerPaintPage.xaml.cs) файл поддерживает две коллекции этих объектов, для этих ломаных линий в настоящее время отрисовывается и другой для завершенного ломаные линии:

```csharp
Dictionary<long, FingerPaintPolyline> inProgressPolylines = new Dictionary<long, FingerPaintPolyline>();
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

`Pressed` Обработки создает новый `FingerPaintPolyline`, вызовы `MoveTo` в объекте пути для хранения начальной точки и добавляет этот объект `inProgressPolylines` словаря. `Moved` Обработки вызовов `LineTo` на объекте путь в новое положение пальца и `Released` обработки передает завершенного ломаной линии из `inProgressPolylines` для `completedPolylines`. Опять же фактический SkiaSharp, код приложения относительно прост:

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

Установлена во всех предыдущих примерах `Capture` свойство `TouchEffect` для `true`, либо если `TouchEffect` был создан или когда `Pressed` произошло событие. Это гарантирует, что тот же элемент получает все события, связанные с пальцу, который первого нажатия представления. Последний пример *не* задать `Capture` для `true`. Это приводит к другому поведению, когда палец с экрана перемещается от одного элемента к другому. Элемент, который перемещает палец из получает событие с `Type` свойство значение `TouchActionType.Exited` и второй элемент получает событие с `Type` параметр `TouchActionType.Entered`.

Этот тип обработки touch очень полезна для клавиатуры музыки. Ключ должен иметь возможность обнаружить, когда она нажата, но и когда палец слайды с одного ключа к другому.

**Автоматической клавиатуры** страница определяет малых [ `WhiteKey` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/WhiteKey.cs) и [ `BlackKey` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/BlackKey.cs) классы, производные от [ `Key` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/Key.cs), который является производным от `BoxView`.

`Key` Класс готов для использования в программе музыкальные. Он определяет свойства с именами `IsPressed` и `KeyNumber`, которые предназначены быть установлен в основной код, установленные стандарт MIDI. `Key` Класс также определяет событие, именуемое `StatusChanged`, которое вызывается, когда `IsPressed` изменения свойств.

Пальцы, разрешена для каждого ключа. По этой причине `Key` класс поддерживает `List` идентификаторов сенсорной все пальцев, в настоящее время Если выбрать этот ключ:

```csharp
List<long> ids = new List<long>();
```

`TouchAction` Обработчик событий добавляет идентификатор к `ids` списка для обоих `Pressed` тип события и `Entered` типа, но только если `IsInContact` свойство `true` для `Entered` событий. Идентификатор удаляется из `List` для `Released` или `Exited` событий:

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

`AddToList` И `RemoveFromList` оба метода проверьте `List` изменился между пустым и не пустое и если да, вызывает `StatusChanged` событий.

Различные `WhiteKey` и `BlackKey` элементы расположены на странице [файл XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/SilentKeyboardPage.xaml), который лучше всего выглядит, когда телефон удерживается в альбомном режиме:

[![](touch-tracking-images/silentkeyboard-small.png "Тройной снимок экрана страницы автоматической клавиатуры")](touch-tracking-images/silentkeyboard-large.png#lightbox "тройной снимок экрана страницы автоматической клавиатуры")

Если очистка пальца по клавишам, вы увидите в с незначительными изменениями в цвет, что события касания, передаются из одного ключа к другому.

## <a name="summary"></a>Сводка

В этой статье показано, как вызывать события в эффект и записи и использовать эффект, реализует низкоуровневые мультисенсорные обработки.


## <a name="related-links"></a>Связанные ссылки

- [Мультисенсорные палец отслеживания в iOS](~/ios/app-fundamentals/touch/touch-tracking.md)
- [Палец Мультисенсорное отслеживание на Android](~/android/app-fundamentals/touch/touch-tracking.md)
- [Эффект отслеживания сенсорного ввода (пример)](https://developer.xamarin.com/samples/xamarin-forms/effects/TouchTrackingEffectDemos/)
