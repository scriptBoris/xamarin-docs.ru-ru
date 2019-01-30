---
title: Core анимации в Xamarin.iOS
description: В данной статье рассматриваются framework Core Animation, показывающий, как она позволяет использовать высокий уровень производительности, плавности анимации в UIKit, а также как использовать его напрямую для элемента управления анимации более низкого уровня.
ms.prod: xamarin
ms.assetid: D4744147-FACB-415B-8155-3A6B3C35E527
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: a40d0911b7dabc900a4c6e50c692e4f091f22be9
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233631"
---
# <a name="core-animation-in-xamarinios"></a>Core анимации в Xamarin.iOS

_В данной статье рассматриваются framework Core Animation, показывающий, как она позволяет использовать высокий уровень производительности, плавности анимации в UIKit, а также как использовать его напрямую для элемента управления анимации более низкого уровня._

включает в себя iOS [ *Core Animation* ](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreAnimation_guide/Introduction/Introduction.html) для обеспечения поддержки анимации для представлений в приложении.
Все Сверхвысокий smooth анимации в iOS, таких как прокрутка таблиц и проведение пальцем по экрану, между различными представлениями выполнить также, как они основаны на базовой анимации, внутренним образом.

На платформах, Core Animation и двухмерной графики могут работать совместно для создания привлекательных, анимировано двумерной графики. На самом деле базовой анимации можно даже преобразовать двумерная графика в трехмерном пространстве, создание потрясающие, возможности взаимодействия. Тем не менее чтобы создать true трехмерной графики, потребовалось бы использовать что-то, как OpenGL ES, или очереди игры на такой API, как MonoGame, несмотря на то, что 3D выходит за рамки этой статьи.

<a name="Using_Core_Animation" />

## <a name="core-animation"></a>Базовой анимации

iOS использует платформу, Core Animation для создания эффектов анимации, такие как переход между представлениями, скользящий меню и прокрутка эффекты лишь некоторые из них. Для работы с анимацией двумя способами:

- [Через UIKit](#Using_UIKit_Animation), который включает анимации на основе представлений, а также анимированные переходы между контроллерами.
- [С помощью базовой анимации](#Using_Core_Animation), какие слои напрямую, что для более детального элемента управления.

<a name="Using_UIKit_Animation" />

## <a name="using-uikit-animation"></a>С помощью UIKit анимации

UIKit предоставляет несколько функций, которые позволяют легко добавить анимацию к приложению. Несмотря на то, что внутри она использует базовой анимации, он упрощает его чтобы работать только с помощью представлений и контроллеров.

В этом разделе рассматриваются функции UIKit анимации, в том числе:

-  Переходы между контроллерами
-  Переходы между представлениями
-  Просмотр свойств анимации


### <a name="view-controller-transitions"></a>Переходы контроллера представления

 `UIViewController` предоставляет встроенную поддержку для перехода между контроллерами представления через `PresentViewController` метод. При использовании `PresentViewController`, переход на втором контроллере при необходимости могут быть анимированы.

Например, рассмотрим приложение с двумя контроллерами, которых касается кнопки в первый контроллер вызывает `PresentViewController` для отображения второй контроллер. Чтобы контролировать, какие анимации перехода, используется для отображения второго контроллера, просто присвойте его [ `ModalTransitionStyle` ](xref:UIKit.UIModalTransitionStyle) свойства, как показано ниже:

```csharp
SecondViewController vc2 = new SecondViewController {
    ModalTransitionStyle = UIModalTransitionStyle.PartialCurl
};
```

В этом случае `PartialCurl` анимации используется, несмотря на то, что несколько других, в том числе:

-  `CoverVertical` — Слайдов вверх в нижней части экрана
-  `CrossDissolve` Старое представление исчезает и появляется новое представление
-  `FlipHorizontal` -Перевернуть горизонтального справа налево. На увольнения перехода зеркальное отражение слева направо.


Для анимации перехода, передайте `true` как второй аргумент `PresentViewController`:

```csharp
PresentViewController (vc2, true, null);
```

На следующем рисунке показан как выглядит перехода для `PartialCurl` случай:

 ![](core-animation-images/06-view-transitions.png "На этом снимке экрана показано PartialCurl перехода")

### <a name="view-transitions"></a>Представление переходов

Помимо переходы между контроллерами UIKit поддерживает анимации переходы между представлениями для замены одно представление для другого.

Например, допустим, имеется контроллер с `UIImageView`, которой коснувшись изображения отображались секунды `UIImageView`. Для анимации изображение суперпредставления представления для перехода на второй вид изображения осуществляется простым вызовом `UIView.Transition`, передавая ему `toView` и `fromView` как показано ниже:

```csharp
UIView.Transition (
    fromView: view1,
    toView: view2,
    duration: 2,
    options: UIViewAnimationOptions.TransitionFlipFromTop |
        UIViewAnimationOptions.CurveEaseInOut,
    completion: () => { Console.WriteLine ("transition complete"); });
```

`UIView.Transition` также принимает `duration` параметр, который определяет, сколько времени выполняется анимация, а также [ `options` ](xref:UIKit.UIViewAnimationOptions) Чтобы задать такие параметры, такие как анимация и функцией плавности. Кроме того можно указать обработчик завершения, который будет вызываться при завершении анимации.

Снимок экрана ниже показано анимированные переходы между изображение представления, если `TransitionFlipFromTop` используется:

 ![](core-animation-images/07-animated-transition.png "На этом снимке экрана показано анимированный переход между представлениями изображения при использовании TransitionFlipFromTop")

### <a name="view-property-animations"></a>Анимация свойств представления

Поддерживает UIKit анимации на различные свойства `UIView` класса бесплатно, включая:

-  Frame
-  Границы
-  Центр
-  Коэффициент альфа
-  Transform
-  Цвет


Эти анимации происходит неявно, указав изменения свойств в `NSAction` делегат, переданный статический `UIView.Animate` метод. Например, следующий код выполняет анимацию центральной точки `UIImageView`:

```csharp
pt = imgView.Center;

UIView.Animate (
    duration: 2, 
    delay: 0, 
    options: UIViewAnimationOptions.CurveEaseInOut | 
        UIViewAnimationOptions.Autoreverse,
    animation: () => {
        imgView.Center = new CGPoint (View.Bounds.GetMaxX () 
            - imgView.Frame.Width / 2, pt.Y);},
    completion: () => {
        imgView.Center = pt; }
);
```

В результате образ анимации вперед и назад в верхней части экрана, как показано ниже:

 ![](core-animation-images/08-animate-center.png "Изображение анимации вперед и назад в верхней части экрана как выходные данные")

Как и в `Transition` метода `Animate` позволяет длительность задать, вместе с функцией плавности. В этом примере также используется `UIViewAnimationOptions.Autoreverse` параметр, который приводит к анимации для анимации из значения, в котором начальной. Тем не менее, код также задает `Center` начальное значение в завершающий обработчик. Во время анимации интерполяцией значения свойств со временем, реальной модели значение свойства всегда равно конечное значение, которое было задано. В этом примере возвращается в точку рядом с правой стороны суперпредставления. Без параметра `Center` до начальной точки, который является, где из-за завершения анимации `Autoreverse` задаваемое, изображение было подключить обратно в правую часть после завершения анимации, как показано ниже:

 ![](core-animation-images/09-animation-complete.png "Без установки центра начальной точки, изображение было подключить обратно в правую часть после завершения анимации")

## <a name="using-core-animation"></a>С помощью базовой анимации

 `UIView` анимации, обеспечивающие большую возможность и должны использоваться, если это возможно из-за Простота реализации. Как упоминалось ранее, анимации UIView использовать платформу Core Animation. Тем не менее, некоторые вещи не может выполняться с помощью `UIView` анимации, такие как анимация дополнительные свойства, которые не могут анимироваться представление или интерполяцией нелинейных пути. В таких случаях, когда необходимо более детального контроля Core Animation используется также и напрямую.

### <a name="layers"></a>Слои

При работе с базовой анимации, анимации осуществляется через *слои*, которые имеют тип `CALayer`. Слой аналогичен представления, где уровень иерархии, во многом как просмотр иерархии. На самом деле слои обратно представления с представлением, добавляя поддержку взаимодействия с пользователем. Вы можете доступа к уровню любого представления в представлении `Layer` свойство. На самом деле, используется контекст в `Draw` метод `UIView` фактически создается из слоя. На внутреннем уровне слоя резервного `UIView` имеет его делегат, задайте режим, который является то, что вызывает `Draw`. Да, при рисовании для `UIView`, фактически рисуемого ее слой.

Анимация уровень может быть явными и неявными. Неявные анимации являются декларативными. Объявить свойства слоя, которое следует изменить, и анимация просто работает. С другой стороны явные анимации создаются через класс анимации, который добавляется к слою. Явные анимации разрешить добавление контролировать способ создания анимации. Следующие разделы углубимся в явных и неявных анимации более подробно.

### <a name="implicit-animations"></a>Неявные анимации

Для анимации свойств слоя рекомендуется с помощью неявного анимации. `UIView` анимации Создание неявного анимации. Тем не менее можно создать неявный анимации непосредственно в слой также.

Например, следующий код задает слоя `Contents` из образа, задает ширину границы и цвет, и добавляет слой как подуровень слоя представления:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    layer = new CALayer ();
    layer.Bounds = new CGRect (0, 0, 50, 50);
    layer.Position = new CGPoint (50, 50);
    layer.Contents = UIImage.FromFile ("monkey2.png").CGImage;
    layer.ContentsGravity = CALayer.GravityResize;
    layer.BorderWidth = 1.5f;
    layer.BorderColor = UIColor.Green.CGColor;

    View.Layer.AddSublayer (layer);
}
```

Чтобы добавить неявное анимации для слоя, просто перенести изменения свойств в `CATransaction`. Это позволяет анимации свойств, которые не будут анимируемым с анимированный значок представления, такие как `BorderWidth` и `BorderColor` как показано ниже:

```csharp
public override void ViewDidAppear (bool animated)
{
    base.ViewDidAppear (animated);

    CATransaction.Begin ();
    CATransaction.AnimationDuration = 10;
    layer.Position = new CGPoint (50, 400);
    layer.BorderWidth = 5.0f;
    layer.BorderColor = UIColor.Red.CGColor;
    CATransaction.Commit ();
}
```

Этот код также анимирует слоя `Position`, — расположение точки привязки слоя измеряется от левого верхнего угла superlayer координат. Точка привязки слоя — нормализованный точки в системе координат слоя.

На следующем рисунке показана точка положение и привязки:

 ![](core-animation-images/10-postion-anchorpt.png "На этом рисунке показано точку положения и привязки")

При выполнении примера `Position`, `BorderWidth` и `BorderColor` анимации, как показано на следующем снимке экрана:

 ![](core-animation-images/11-implicit-animation.png "При выполнении примера положение, BorderWidth и BorderColor анимировать как показано")

### <a name="explicit-animations"></a>Явные анимации

В дополнение к неявное анимации, Core Animation включает широкий набор классов, унаследованных от `CAAnimation` , которые позволяют инкапсулировать анимации, которые затем добавлены явно со слоем. Это позволяет более детального контроля над анимацией, например изменить начальное значение анимации, группирование анимации и указания опорных кадров разрешены нелинейных пути.

Следующий код является примером явные анимацией с помощью `CAKeyframeAnimation` для слоя, показанный ранее (в разделе «неявные анимации):

```csharp
public override void ViewDidAppear (bool animated)
{
    base.ViewDidAppear (animated);
    
    // get the initial value to start the animation from
    CGPoint fromPt = layer.Position;
    
    /* set the position to coincide with the final animation value
    to prevent it from snapping back to the starting position
    after the animation completes*/
    layer.Position = new CGPoint (200, 300);
    
    // create a path for the animation to follow
    CGPath path = new CGPath ();
    path.AddLines (new CGPoint[] { fromPt, new CGPoint (50, 300), new CGPoint (200, 50), new CGPoint (200, 300) });
    
    // create a keyframe animation for the position using the path
    CAKeyFrameAnimation animPosition = (CAKeyFrameAnimation)CAKeyFrameAnimation.FromKeyPath ("position");
    animPosition.Path = path;
    animPosition.Duration = 2;
    
    // add the animation to the layer.
    /* the "position" key is used to overwrite the implicit animation created
    when the layer positino is set above*/
    layer.AddAnimation (animPosition, "position");
}
```

Этот код изменяет `Position` слоя, создав путь, который затем используется для определения анимации ключевого кадра. Обратите внимание, что слоя `Position` присваивается конечное значение `Position` из анимации. Без этого слоя необходимо сразу же станет его `Position` перед анимации так, как анимация изменяет только значения презентации и не фактического значения модели. Установив значение модели конечное значение анимации, слой сохранятся в конце анимации.

На следующих снимках экрана показано слой, содержащий изображение анимации по указанному пути:

 ![](core-animation-images/12-explicit-animation.png "На этом снимке экрана показано слой, содержащий изображение анимации по указанному пути")
 
## <a name="summary"></a>Сводка

В этой статье мы рассмотрели возможности анимации, предоставляемого *Core Animation* платформ. Мы рассмотрели Core Animation, узнайте, как он поддерживает анимацию в UIKit, и как он может использоваться непосредственно для элемента управления анимации более низкого уровня.

## <a name="related-links"></a>Связанные ссылки

- [Пример анимации Core](https://developer.xamarin.com/samples/monotouch/GraphicsAndAnimation/)
- [Core Graphics](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [Графика и анимация Пошаговое руководство](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [Core Animation](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
