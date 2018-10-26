---
title: Добавление распознавателя жестов проведите по экрану
description: В этой статье объясняется, как распознать жеста, происходящих в представление.
ms.prod: xamarin
ms.assetid: 164976C2-1429-49FB-9EB6-621E2681C19B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/14/2018
ms.openlocfilehash: 95e95d8849824cd2dc31c2019627cc5adbbefeec
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50131924"
---
# <a name="adding-a-swipe-gesture-recognizer"></a>Добавление распознавателя жестов проведите по экрану

_Жест прокрутки происходит, когда палец перемещается по экрану в горизонтальном или вертикальном направлении, а также часто используется для инициации перехода по содержимому. В примерах кода в этой статье взяты из [жеста](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/SwipeGesture/) образца._

Чтобы сделать [ `View` ](xref:Xamarin.Forms.View) распознает жест прокрутки, создайте [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) экземпляра, установите [ `Direction` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Direction) свойства [ `SwipeDirection` ](xref:Xamarin.Forms.SwipeDirection) значение перечисления (`Left`, `Right`, `Up`, или `Down`), при необходимости задайте [ `Threshold` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold) свойство, дескриптор [ `Swiped` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) событий, и добавьте новый распознаватель жестов для [ `GestureRecognizers` ](xref:Xamarin.Forms.View.GestureRecognizers) коллекции для представления. В следующем коде показано в примере `SwipeGestureRecognizer` подключен к [ `BoxView` ](xref:Xamarin.Forms.BoxView):

```xaml
<BoxView Color="Teal" ...>
    <BoxView.GestureRecognizers>
        <SwipeGestureRecognizer Direction="Left" Swiped="OnSwiped"/>
    </BoxView.GestureRecognizers>
</BoxView>
```

Ниже приведен эквивалент C# кода:

```csharp
var boxView = new BoxView { Color = Color.Teal, ... };
var leftSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Left };
leftSwipeGesture.Swiped += OnSwiped;

boxView.GestureRecognizers.Add(leftSwipeGesture);
```

[ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) Класс также включает [ `Threshold` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold) свойство, которое можно при необходимости присвоить `uint` значение, представляющее считывание минимальное расстояние, которое должно быть достигнуто для проведите по экрану, чтобы распознаваться в аппаратно независимых единицах. Значение по умолчанию этого свойства равно 100, это означает, что любой вставляет, не более 100 аппаратно независимые единицы будет игнорироваться.

## <a name="recognizing-the-swipe-direction"></a>Распознавание направление жеста перетаскивания

В приведенных выше примеров [ `Direction` ](xref:Xamarin.Forms.SwipedEventArgs.Direction) свойству присваивается одно значение из [ `SwipeDirection` ](xref:Xamarin.Forms.SwipeDirection) перечисления. Тем не менее, можно также задать это свойство для нескольких значений из `SwipeDirection` перечисления, таким образом, чтобы [ `Swiped` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) события в ответ на считывание в более чем в одном направлении. Тем не менее, ограничением является то, что один [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) распознает только вставляет, возникающие на ту же ось. Таким образом, можно распознать вставляет, возникающие на горизонтальной оси, установив `Direction` свойства `Left` и `Right`:

```xaml
<SwipeGestureRecognizer Direction="Left,Right" Swiped="OnSwiped"/>
```

Аналогичным образом можно распознать вставляет, возникающие на вертикальной оси, установив [ `Direction` ](xref:Xamarin.Forms.SwipedEventArgs.Direction) свойства `Up` и `Down`:

```csharp
var swipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Up | SwipeDirection.Down };
```

Кроме того [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) направление для каждого жеста перетаскивания могут создаваться для распознавания вставляет в каждом направлении:

```xaml
<BoxView Color="Teal" ...>
    <BoxView.GestureRecognizers>
        <SwipeGestureRecognizer Direction="Left" Swiped="OnSwiped"/>
        <SwipeGestureRecognizer Direction="Right" Swiped="OnSwiped"/>
        <SwipeGestureRecognizer Direction="Up" Swiped="OnSwiped"/>
        <SwipeGestureRecognizer Direction="Down" Swiped="OnSwiped"/>
    </BoxView.GestureRecognizers>
</BoxView>
```

Ниже приведен эквивалент C# кода:

```csharp
var boxView = new BoxView { Color = Color.Teal, ... };
var leftSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Left };
leftSwipeGesture.Swiped += OnSwiped;
var rightSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Right };
rightSwipeGesture.Swiped += OnSwiped;
var upSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Up };
upSwipeGesture.Swiped += OnSwiped;
var downSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Down };
downSwipeGesture.Swiped += OnSwiped;

boxView.GestureRecognizers.Add(leftSwipeGesture);
boxView.GestureRecognizers.Add(rightSwipeGesture);
boxView.GestureRecognizers.Add(upSwipeGesture);
boxView.GestureRecognizers.Add(downSwipeGesture);
```

> [!NOTE]
> В приведенных выше примерах, тот же обработчик событий отвечает на [ `Swiped` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) события Click. Тем не менее каждый [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) экземпляра, при необходимости можно использовать другой обработчик событий.

## <a name="responding-to-the-swipe"></a>Реагирование на считывание

Обработчик событий для [ `Swiped` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) событие отображается в следующем примере:

```csharp
void OnSwiped(object sender, SwipedEventArgs e)
{
    switch (e.Direction)
    {
        case SwipeDirection.Left:
            // Handle the swipe
            break;
        case SwipeDirection.Right:
            // Handle the swipe
            break;
        case SwipeDirection.Up:
            // Handle the swipe
            break;
        case SwipeDirection.Down:
            // Handle the swipe
            break;
    }
}
```

[ `SwipedEventArgs` ](xref:Xamarin.Forms.SwipedEventArgs) Можно проверить, чтобы определить направление прокрутки, с помощью пользовательской логики, реагирование на считывание при необходимости. Направление жеста перетаскивания может быть получен из [ `Direction` ](xref:Xamarin.Forms.SwipedEventArgs.Direction) свойство аргументов событий, которые будет присвоено одно из значений из [ `SwipeDirection` ](xref:Xamarin.Forms.SwipeDirection) перечисления. Кроме того, аргументы события также имеют [ `Parameter` ](xref:Xamarin.Forms.SwipedEventArgs.Parameter) свойство, которое будет присвоено значение [ `CommandParameter` ](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) свойства, если он определен.

## <a name="using-commands"></a>С помощью команд

[ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) Класс также включает [ `Command` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Command) и [ `CommandParameter` ](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) свойства. Эти свойства обычно используются в приложениях, использующих шаблон Model-View-ViewModel (MVVM). `Command` Определяет свойство `ICommand` вызываемого, когда жест прокрутки распознается, с помощью `CommandParameter` свойства, определяющего объект передается `ICommand.` в следующем примере кода показано, как привязать `Command` свойства `ICommand` определены в модели представления, экземпляр которого задан в качестве страницы [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext):

```csharp
var boxView = new BoxView { Color = Color.Teal, ... };
var leftSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Left, CommandParameter = "Left" };
leftSwipeGesture.SetBinding(SwipeGestureRecognizer.CommandProperty, "SwipeCommand");
boxView.GestureRecognizers.Add(leftSwipeGesture);
```

Ниже приведен аналогичный код XAML:

```xaml
<BoxView Color="Teal" ...>
    <BoxView.GestureRecognizers>
        <SwipeGestureRecognizer Direction="Left" Command="{Binding SwipeCommand}" CommandParameter="Left" />
    </BoxView.GestureRecognizers>
</BoxView>
```

`SwipeCommand` является свойством типа `ICommand` определено в экземпляре модели представления, заданную в качестве страницы [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext). Когда жест прокрутки распознается, `Execute` метод `SwipeCommand` выполняется объект. Аргумент `Execute` метод — это значение [ `CommandParameter` ](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) свойство. Дополнительные сведения о командах см. в разделе [интерфейс команда](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

## <a name="creating-a-swipe-container"></a>Создание контейнера проведите по экрану

`SwipeContainer` Класс, который показан в следующем примере кода, является классом распознавания универсального проведите по экрану, быть оберткой [ `View` ](xref:Xamarin.Forms.View) для выполнения распознавания жестов жеста перетаскивания:

```csharp
public class SwipeContainer : ContentView
{
    public event EventHandler<SwipedEventArgs> Swipe;

    public SwipeContainer()
    {
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Left));
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Right));
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Up));
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Down));
    }

    SwipeGestureRecognizer GetSwipeGestureRecognizer(SwipeDirection direction)
    {
        var swipe = new SwipeGestureRecognizer { Direction = direction };
        swipe.Swiped += (sender, e) => Swipe?.Invoke(this, e);
        return swipe;
    }
}
```

`SwipeContainer` Класс создает [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) объектов для всех четырех считывание направлениях и присоединяет `Swipe` обработчики событий. Эти обработчики событий вызова `Swipe` событие, определенное `SwipeContainer`.

В следующем примере показан код XAML `SwipeContainer` класса упаковки [ `BoxView` ](xref:Xamarin.Forms.BoxView):

```xaml
<ContentPage ...>
    <StackLayout>
        <local:SwipeContainer Swipe="OnSwiped" ...>
            <BoxView Color="Teal" ... />
        </local:SwipeContainer>
    </StackLayout>
</ContentPage>
```

В следующем примере кода показано как `SwipeContainer` заключает в оболочку [ `BoxView` ](xref:Xamarin.Forms.BoxView) в C# страницы:

```csharp
public class SwipeContainerPageCS : ContentPage
{
    public SwipeContainerPageCS()
    {
        var boxView = new BoxView { Color = Color.Teal, ... };
        var swipeContainer = new SwipeContainer { Content = boxView, ... };
        swipeContainer.Swipe += (sender, e) =>
        {
          // Handle the swipe
        };

        Content = new StackLayout
        {
            Children = { swipeContainer }
        };
    }
}
```

Когда [ `BoxView` ](xref:Xamarin.Forms.BoxView) получает жеста, [ `Swiped` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) событие в [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) запускается. Это обрабатывается `SwipeContainer` класс, который запускает собственную `Swipe` событий. Это `Swipe` событие обрабатывается на странице. [ `SwipedEventArgs` ](xref:Xamarin.Forms.SwipedEventArgs) Можно затем изучить и определить направление прокрутки, с помощью пользовательской логики, реагирование на считывание при необходимости.

## <a name="related-links"></a>Связанные ссылки

- [Жест прокрутки (пример)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/SwipeGesture/)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [SwipeGestureRecognizer](xref:Xamarin.Forms.SwipeGestureRecognizer)
