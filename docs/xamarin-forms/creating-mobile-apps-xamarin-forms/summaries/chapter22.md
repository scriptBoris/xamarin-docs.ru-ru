---
title: Сводка информации в главе 22. Анимация
description: 'Создание мобильных приложений с помощью Xamarin.Forms: сводка информации в главе 22. Анимация'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 47C2B9AB-E688-4412-8AF5-9F633B3DA695
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 82ffecd41115bc91df9732793f5e22e721dbdee4
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563905"
---
# <a name="summary-of-chapter-22-animation"></a>Сводка информации в главе 22. Анимация

Вы уже видели, что можно создать анимацию с помощью таймера Xamarin.Forms или `Task.Delay`, но обычно проще с помощью анимации, предоставляемых Xamarin.Forms. Три класса реализуют эти анимации:

- [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions), общий подход
- [`Animation`](xref:Xamarin.Forms.Animation), более гибкими, но более сложные
- [`AnimationExtension`](xref:Xamarin.Forms.AnimationExtensions), наиболее гибким и самого низкого уровня подход

Как правило анимации целевого свойства, которые обеспечиваются связываемые свойства. Это не обязательное требование, но это только свойства, которые динамически реагировать на изменения.

Нет интерфейса XAML для анимации, но вы можете интегрировать анимаций в XAML, методами, описанными в [ **Глава 23. Триггеры и поведения**](chapter23.md).

## <a name="exploring-basic-animations"></a>Изучение базовых анимаций

Простая анимация функции являются методы расширения, найденные в [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) класса. Эти методы применяются к любой объект, который является производным от `VisualElement`. Самый простой анимации целевых преобразований, подробно свойства [ `Chapter 21. Transforms` ](chapter21.md).

[ **AnimationTryout** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/AnimationTryout) показано, как `Clicked` обработчик событий для `Button` можно вызвать [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) метод расширения, чтобы запустить Кнопка в кружке.

`RotateTo` Изменение метода `Rotation` свойство `Button` от 0 до 360 в ходе четверти секунды (по умолчанию). Если `Button` шифрованию опять же, однако он не выполняет никаких действий из-за `Rotation` свойство уже 360 градусов.

### <a name="setting-the-animation-duration"></a>Назначение длительности анимации

Второй аргумент `RotateTo` — это период времени в миллисекундах. Если задать большое значение, коснувшись `Button` во время анимации начинается в новой анимации, начиная с текущего угла.

### <a name="relative-animations"></a>Относительный анимации

`RelRotateTo` Метод выполняет относительный поворот, добавив указанное значение к существующему значению. Этот метод позволяет `Button` для отвода несколько раз, а для каждого время увеличивается `Rotation` свойство на 360 градусов.

### <a name="awaiting-animations"></a>Ожидающие анимации

Все методы анимации в `ViewExtensions` возвращают `Task<bool>` объектов. Это означает, что можно определить ряд последовательных анимаций с помощью `ContinueWith` или `await`. `bool` Завершения возвращаемое значение — `false` окончания анимации без прерывания или `true` если оно было отменено по запросу [ `CancelAnimation` ](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)) метод, который отменяет все анимации, инициированные другой метод в `ViewExtensions` , которые задаются в том же элементе.

### <a name="composite-animations"></a>Составной анимации

Вы можете объединять ожидаемой и не ожидать анимации для создания составного анимаций. Это анимации в `ViewExtensions` , предназначенных `TranslationX`, `TranslationY`, и `Scale` преобразования свойств:

- [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing))
- [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))
- [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))

Обратите внимание, что `TranslateTo` потенциально влияет на них `TranslationX` и `TranslationY` свойства.

### <a name="taskwhenall-and-taskwhenany"></a>Task.WhenAll и Task.WhenAny

Это также позволяет использовать несколько одновременных анимаций с помощью [ `Task.WhenAll` ](xref:System.Threading.Tasks.Task.WhenAll*), который сообщает, когда несколько задач все завершения, и [ `Task.WhenAny` ](xref:System.Threading.Tasks.Task.WhenAny*), который сигнализирует, когда первый из нескольких завершила задачи.

### <a name="rotation-and-anchors"></a>Поворот и привязки

При вызове `ScaleTo`, `RelScaleTo`, `RotateTo`, и `RelRotateTo` методов, можно задать [ `AnchorX` ](xref:Xamarin.Forms.VisualElement.AnchorX) и [ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY) свойств, чтобы указать Центр масштабировании и повороте.

[ **CircleButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CircleButton) этот метод продемонстрирован вращением `Button` относительно центра страницы.

### <a name="easing-functions"></a>Функции плавности

Обычно анимации линейной из начальное значение для конечного значений. Функции плавности может привести к анимации ускорить или замедлить их ходе. Последний необязательный аргумент методы анимации имеет тип [ `Easing` ](xref:Xamarin.Forms.Easing), класс, определяющий 11 статические поля только для чтения типа `Easing`:

- [`Linear`](xref:Xamarin.Forms.Easing.Linear), значение по умолчанию
- [`SinIn`](xref:Xamarin.Forms.Easing.SinIn), [ `SinOut` ](xref:Xamarin.Forms.Easing.SinOut), и [`SinInOut`](xref:Xamarin.Forms.Easing.SinInOut)
- [`CubicIn`](xref:Xamarin.Forms.Easing.CubicIn), [ `CubicOut` ](xref:Xamarin.Forms.Easing.CubicOut), и [`CubicInOut`](xref:Xamarin.Forms.Easing.CubicInOut)
- [`BounceIn`](xref:Xamarin.Forms.Easing.BounceIn) и [`BounceOut`](xref:Xamarin.Forms.Easing.BounceOut)
- [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) и [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut)

`In` Суффикс указывает, что эффект в начале анимации, `Out` означает, что в конце и `InOut` означает, что в начале и в конце анимации.

[ **BounceButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BounceButton) в нем демонстрируется использование функции плавности.

### <a name="your-own-easing-functions"></a>Свои собственные функции плавности

Можно также определить вы собственные функции плавности путем передачи [ `Func<double, double>` ](xref:System.Func`2) для [ `Easing` конструктор](xref:Xamarin.Forms.Easing.%23ctor(System.Func{System.Double,System.Double})). `Easing` также определяет неявное преобразование из `Func<double, double>` для `Easing`. Аргумент в функцию плавности всегда находится в диапазоне от 0 до 1, процессе анимации линейно от начала до конца. Функция *обычно* возвращает значение в диапазоне от 0 до 1, но может быть кратко отрицательное или больше, чем 1 (как в случае с `SpringIn` и `SpringOut` функции) или может нарушить правила, если вы знаете, что он делает.

[ **UneasyScale** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/UneasyScale) в нем демонстрируется пользовательской функции плавности, и [ **CustomCubicEase** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CustomCubicEase) демонстрирует другой.

[ **SwingButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingButton) образец также демонстрирует пользовательской функции плавности, а также способ изменения `AnchorX` и `AnchorY` свойства внутри последовательности анимации поворота.

[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) библиотека имеет [ `JiggleButton` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/JiggleButton.cs) класс, который использует пользовательский плавности функцию jiggle кнопки при нажатии. [ **JiggleButtonDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/JiggleButtonDemo) в нем демонстрируется его.

### <a name="entrance-animations"></a>Вступительной анимации

Несколько популярных видов анимация происходит при первом открытии страницы. Такой анимации могут запускаться [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) переопределить части страницы. Для этих анимаций, мы рекомендуем настроить XAML как требуется страницы для отображения *после* анимации, а затем инициализировать и анимировать макет из кода.

[ **FadingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingEntrance) примере используется [ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) метод расширения в содержимое страницы.

[ **SlidingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SlidingEntrance) примере используется [ `TranslateTo` ](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) метод расширения к слайду в содержимое страницы из сторон.

[ **SwingingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingingEntrance) примере используется [ `RotateYTo` ](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) метод расширения для анимации `RotationY` свойство. Объект [ `RotateXTo` ](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) метод также доступен.

### <a name="forever-animations"></a>Навсегда анимации

С другой стороны «всегда» анимации запущены программа будет завершена. Как правило, они предназначены для демонстрационных целей.

[ **FadingTextAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingTextAnimation) примере используется [ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) анимация двух фрагментов текста и исчезновения.

[**PalindromeAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/PalindromeAnimation) отображает palindrome, а затем последовательно поворачивает отдельных букв на 180 градусов, они будут все ногами. Затем всю строку изменилось 180 градусов для чтения, так же, как исходная строка.

[ **CopterAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CopterAnimation) пример поворачивает простой `BoxView` вертолетом при его вращение вокруг центральной части экрана.

[**RotatingSpokes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotatingSpokes) вращается `BoxView` сектора вокруг центральной части экрана, а затем выполняется поворот каждой периферийной зоне для создания интересных шаблонов:

[![Тройной снимок экрана: вращение сектора](images/ch22fg21-small.png "поворот сектора")](images/ch22fg21-large.png#lightbox "поворот сектора")

Тем не менее, постепенно увеличение `Rotation` свойства элемента может не работать в долгосрочной перспективе, как [ **RotationBreakdown** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotationBreakdown) в нем демонстрируется.

[ **SpinningImage** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpinningImage) примере используется [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), [ `RotateXTo` ](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), и [ `RotateYTo` ](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) Чтобы создать ощущение, как если Битовая карта поворот в трехмерном пространстве.

### <a name="animating-the-bounds-property"></a>Анимация свойства bounds

Единственный метод расширения в `ViewExtensions` еще не показано является [ `LayoutTo` ](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing)), который фактически выполняет анимацию только для чтения [ `Bounds` ](xref:Xamarin.Forms.VisualElement.Bounds) свойства путем вызова [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) метод. Этот метод обычно вызывается `Layout` производные, как будут рассмотрены в [ **Глава 26. CustomLayouts**](chapter26.md).

`LayoutTo` Метод должен быть ограничен особых целей. [ **BouncingBox** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BouncingBox) программа использует его, разверните `BoxView` как его отскакивает стороны страницы.

[ **XamagonXuzzle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle) примере используется `LayoutTo` перемещение плитки в реализации классического головоломки 15-16, отображающий скремблированных образа, а не нумерованных плитки:

[![Тройной снимок Xamarin Xuzzle](images/ch22fg26-small.png "игры головоломки Xuzzle")](images/ch22fg26-large.png#lightbox "Xuzzle головоломки игры")

### <a name="your-own-awaitable-animations"></a>Awaitable анимациями

[ **TryAwaitableAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/TryAwaitableAnimation) образец создает поддерживающий ожидание объект анимацию. Решающее значение класса, который может возвращать `Task` объект из метода и сигнал при завершении анимации является [ `TaskCompletionSource` ](xref:System.Threading.Tasks.TaskCompletionSource`1).

## <a name="deeper-into-animations"></a>Более подробная анимации

Xamarin.Forms системы анимации может быть немного сбить с толку. В дополнение к `Easing` класс, состоит из системы анимации `ViewExtensions`, `Animation`, и `AnimationExtension` классы.

### <a name="viewextensions-class"></a>Класс ViewExtensions

Вы уже видели [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions). Он определяет девять методов, возвращающих `Task<bool>` и [ `CancelAnimations` ](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)). Семь целевого девять методов преобразования свойств. Два других [ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), целевые объекты, которые [ `Opacity` ](xref:Xamarin.Forms.VisualElement.Opacity) свойство, и [ `LayoutTo` ](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing)), который вызывает [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) метод.

### <a name="animation-class"></a>Класс анимации

[ `Animation` ](xref:Xamarin.Forms.AnimationExtensions) Класс имеет [конструктор](xref:Xamarin.Forms.Animation.%23ctor(System.Action{System.Double},System.Double,System.Double,Xamarin.Forms.Easing,System.Action)) пять аргументов, чтобы определить обратный вызов по завершении методов и параметров анимации.

Дочерние анимации могут быть добавлены инструкцией [ `Add` ](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation)), [ `Insert` ](xref:Xamarin.Forms.Animation.Insert(System.Double,System.Double,Xamarin.Forms.Animation)), [ `WithConcurrent` ](xref:Xamarin.Forms.Animation.WithConcurrent(Xamarin.Forms.Animation,System.Double,System.Double))и перегрузки [ `WithConcurrent` ](xref:Xamarin.Forms.Animation.WithConcurrent(System.Action{System.Double},System.Double,System.Double,Xamarin.Forms.Easing,System.Double,System.Double)).

Затем объекта анимации запускается с помощью вызова [ `Commit` ](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) метод.

### <a name="animationextensions-class"></a>Класс AnimationExtensions

[ `AnimationExtensions` ](xref:Xamarin.Forms.AnimationExtensions) Класс содержит главным образом методы расширения. Существует несколько версий `Animate` метод и универсального [ `Animate` ](xref:Xamarin.Forms.AnimationExtensions.Animate*) метод является настолько универсальным, что это действительно вам нужна функция анимацию.

## <a name="working-with-the-animation-class"></a>Работа с класса анимации

[ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) в нем демонстрируется [ `Animation` ](xref:Xamarin.Forms.Animation) класса с помощью нескольких различных анимаций.

### <a name="child-animations"></a>Дочерних анимациях

[ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) образец также демонстрирует дочерних анимации, которые делают использование (очень похожее) [ `Add` ](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation)) и [ `Insert` ](xref:Xamarin.Forms.Animation.Insert(System.Double,System.Double,Xamarin.Forms.Animation)) методы.

### <a name="beyond-the-high-level-animation-methods"></a>За пределами методов высокоуровневые анимации

[ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) образце также показано, как для выполнения анимации, которые выходят за рамки свойства, `ViewExtensions` методы. В примере один ряд периоды становиться длиннее; в другом примере `BackgroundColor` анимированного свойства.

### <a name="more-of-your-own-awaitable-methods"></a>Несколько собственных awaitable методов

[ `TranslateTo` ](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) Метод `ViewExtensions` не работает с [ `Easing.SpringOut` ](xref:Xamarin.Forms.Easing.SpringOut) функции. Оно останавливается при получении плавности выходных данных выше 1.

[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) библиотека содержит [ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) класса [ `TranslateXTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L12) и [ `TranslateYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L49) методы расширения, которые не имеют эту проблему, а также [ `CancelTranslateXTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L44) и [ `CancelTranslateYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L71) методы для их отмены анимации.

[ **SpringSlidingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpringSlidingEntrance) демонстрирует `TranslateXTo` метод.

`MoreExtensions` Класс также содержит [ `TranslateXYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L76) метод расширения, который объединяет перевода X и Y, и [ `CancelTranslateXYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L113) метод.

### <a name="implementing-a-bezier-animation"></a>Реализация анимации Безье

Можно также разработать анимацию, которая перемещает элемент пути сплайна Безье. [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) библиотека содержит [ `BezierSpline` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierSpline.cs) структура, которая инкапсулирует сплайн Безье и [ `BezierTangent` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierTangent.cs) перечисления ориентацию элемента управления.

[ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) Класс содержит [ `BezierPathTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L118) метод расширения и [ `CancelBezierPathTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L161) метод.

[ **BezierLoop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BezierLoop) в нем демонстрируется анимация по контуру Beizer элемент.

## <a name="working-with-animationextensions"></a>Работа с AnimationExtensions

Один тип анимации, в стандартной коллекции отсутствует такое анимация цвета. Проблема — это правильный способ интерполяции между двумя `Color` значения. Можно выполнять интерполяцию отдельных значений RGB, но только как допустимый интерполяцией значения HSL.

По этой причине [ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) в класс [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) библиотека содержит два `Color` методы анимации: [ `RgbColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L166)и [ `HslColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L188). (Также существует два способа отмены: [ `CancelRgbColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L183) и [ `CancelHslColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L206)).

Оба метода делают использование [ `ColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L211), который выполняет анимацию путем вызова обширной универсального [ `Animate` ](xref:Xamarin.Forms.AnimationExtensions.Animate*) метод в [ `AnimationExtensions` ](xref:Xamarin.Forms.AnimationExtensions).

[ **ColorAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ColorAnimations) образце показано использование этих двух типов анимации цвета.

## <a name="structuring-your-animations"></a>Структурирование анимациями

Иногда полезно выразить анимации в XAML и использовать их в сочетании с MVVM. Это рассматривается в следующей главе, [ **Глава 23. Триггеры и поведения**](chapter23.md).



## <a name="related-links"></a>Связанные ссылки

- [Полный текст в главе 22 книги (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)
- [Примеры в главе 22](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)
- [Анимация](~/xamarin-forms/user-interface/animation/index.md)
