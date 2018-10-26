---
title: Переходы контроллера представления в Xamarin.iOS
description: В этом документе описывается настройка анимированные переходы между контроллерами представления в приложениях Xamarin.iOS.
ms.prod: xamarin
ms.assetid: CB3AC8E2-8A47-4839-AFA5-AE33047BB26C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: c143d01a5e68bf8ce9b9b69fdaf79d445f372357
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118075"
---
# <a name="view-controller-transitions-in-xamarinios"></a>Переходы контроллера представления в Xamarin.iOS

UIKit добавляет поддержку для настройки анимированный переход, возникающее при представлении контроллеров представлений. Эта поддержка входит в состав встроенных контроллеров, а также какие-либо пользовательские контроллеры, которые наследуют непосредственно от `UIViewController`. Кроме того `UICollectionViewController` использует преимущества настройки перехода контроллер использовать анимированные переходы в макетах представления коллекции.

## <a name="custom-transitions"></a>Пользовательские переходы

Анимированный переход между контроллерами представления в iOS 7 является полностью настраиваемым. `UIViewController` Теперь включает `TransitioningDelegate` свойство, которое предоставляет класс пользовательского animator в систему, когда происходит переход.

Чтобы использовать пользовательский переход с `PresentViewController`:

1.  Задайте `ModalPresentationStyle` для `UIModalPresentationStyle.Custom` на контроллере представления.
2.  Реализуйте `UIViewControllerTransitioningDelegate` создание класса animator, который является экземпляром `UIViewControllerAnimatedTransitioning` .
3.  Задайте `TransitioningDelegate` свойства к экземпляру `UIViewControllerTransitioningDelegate` , также на контроллере представления.
4.  Представляет контроллер представления.


Например, следующий код демонстрирует контроллер представления типа `ControllerTwo` - `UIViewController` подкласс:

```csharp
showTwo.TouchUpInside += (object sender, EventArgs e) => {

    controllerTwo = new ControllerTwo ();

    this.PresentViewController (controllerTwo, true, null);
};
```

Запуск приложения и нажатия кнопки приводит к анимации по умолчанию второго контроллера представления для анимации в нижней части, как показано ниже:

 ![](transitions-images/no-custom-transition.png "Запуск приложения и нажатия кнопки приводит к анимации по умолчанию второй контроллеры представления для анимации в снизу")

Однако можно присвоить `ModalPresentationStyle` и `TransitioningDelegate` приводит к пользовательской анимации для перехода:

```csharp
showTwo.TouchUpInside += (object sender, EventArgs e) => {

    controllerTwo = new ControllerTwo () {
        ModalPresentationStyle = UIModalPresentationStyle.Custom;
        };

    transitioningDelegate = new TransitioningDelegate ();
    controllerTwo.TransitioningDelegate = transitioningDelegate;

    this.PresentViewController (controllerTwo, true, null);
};
```

`TransitioningDelegate` Отвечает за создание экземпляра `UIViewControllerAnimatedTransitioning` подкласс - вызывается `CustomAnimator` в следующем примере:

```csharp
public class TransitioningDelegate : UIViewControllerTransitioningDelegate
{
    CustomTransitionAnimator animator;

    public override IUIViewControllerAnimatedTransitioning GetAnimationControllerForPresentedController (UIViewController presented, UIViewController presenting, UIViewController source)
    {
        animator = new CustomTransitionAnimator ();
        return animator;
    }
}
```

Если происходит переход, система создает экземпляр класса `IUIViewControllerContextTransitioning`, который он передается animator методы. `IUIViewControllerContextTransitioning` содержит `ContainerView` место анимации, а также инициирует переход контроллера представления и контроллера представления, которое осуществляется переход.

`UIViewControllerAnimatedTransitioning` Класс обрабатывает фактические анимации. Необходимо реализовать два метода:

1.  `TransitionDuration` — Возвращает продолжительность анимации в секундах.
1.  `AnimateTransition` — выполняет фактическое анимации.


Например, следующий класс реализует `UIViewControllerAnimatedTransitioning` для анимации рамки контроллера представления:

```csharp
public class CustomTransitionAnimator : UIViewControllerAnimatedTransitioning
{
    public CustomTransitionAnimator ()
    {
    }

    public override double TransitionDuration (IUIViewControllerContextTransitioning transitionContext)
    {
        return 1.0;
    }

    public override void AnimateTransition (IUIViewControllerContextTransitioning transitionContext)
        {
            var inView = transitionContext.ContainerView;
            var toVC = transitionContext.GetViewControllerForKey (UITransitionContext.ToViewControllerKey);
            var toView = toVC.View;

            inView.AddSubview (toView);

            var frame = toView.Frame;
            toView.Frame = CGRect.Empty;

            UIView.Animate (TransitionDuration (transitionContext), () => {
                toView.Frame = new CGRect (20, 20, frame.Width - 40, frame.Height - 40);
            }, () => {
                transitionContext.CompleteTransition (true);
            });
        }
}
```

Теперь, при нажатии кнопки, анимация реализованы в `UIViewControllerAnimatedTransitioning` класс используется:

 ![](transitions-images/custom-transition.png "Пример масштабирования фактически под управлением")

## <a name="collection-view-transitions"></a>Переходы представления коллекции

Представления коллекций имеют встроенную поддержку создания анимации:

-  **Контроллеры навигации** — анимировать переход между двумя `UICollectionViewController` экземпляры могут дополнительно обрабатываться автоматически при `UINavigationController` управляет ими.
-  **Переход макета** — новый `UICollectionViewTransitionLayout` класс позволяет интерактивных переход между макетами.


### <a name="navigation-controller-transitions"></a>Переходы контроллера навигации

При использовании в контроллер навигации `UICollectionViewController` включает в себя поддержку анимированные переходы между контроллерами. Такая поддержка встроена и требуется всего несколько простых шагов для реализации:

1.  Задайте `UseLayoutToLayoutNavigationTransitions` для `false` на `UICollectionViewController` .
1.  Добавьте экземпляр `UICollectionViewController` в корень стек контроллеров навигации.
1.  Создайте вторую `UICollectionViewController` и задайте его `UseLayoutToLayoutNavigtionTransitions` свойства `true` .
1.  Push-второй `UICollectionViewController` в стек контроллеров навигации.


Следующий код добавляет `UICollectionViewController` подкласс с именем `ImagesCollectionViewController` в корень стека контроллер навигации с `UseLayoutToLayoutNavigationTransitions` свойство значение `false`:

```csharp
UIWindow window;
ImagesCollectionViewController viewController;
UICollectionViewFlowLayout layout;
UINavigationController navController;

public override bool FinishedLaunching (UIApplication app, NSDictionary options)
{
    window = new UIWindow (UIScreen.MainScreen.Bounds);

    // create and initialize a UICollectionViewFlowLayout
    layout = new UICollectionViewFlowLayout (){
        SectionInset = new UIEdgeInsets (10,5,10,5),
        MinimumInteritemSpacing = 5,
        MinimumLineSpacing = 5,
        ItemSize = new CGSize (100, 100)
    };

    viewController = new ImagesCollectionViewController (layout) {
            UseLayoutToLayoutNavigationTransitions = false;
        }

    navController = new UINavigationController (viewController);

    window.RootViewController = navController;
    window.MakeKeyAndVisible ();
    
    return true;
}
```

Если выбран элемент, второй экземпляр `ImagesController` создается только в этот раз с помощью класса другой макет. Для этого контроллера `UseLayoutToLayoutNavigtionTransitions` присваивается `true`, как показано ниже:

```csharp
CircleLayout circleLayout;
ImagesCollectionViewController controller2;

...

public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
{
    // UseLayoutToLayoutNavigationTransitions when item is selected
        circleLayout = new CircleLayout (Monkeys.Instance.Count){
                ItemSize = new CGSize (100, 100)
            };
            
    controller2 = new ImagesCollectionViewController (circleLayout) {
        UseLayoutToLayoutNavigationTransitions = true;
        }

    NavigationController.PushViewController (controller2, true);
}
```

`UseLayoutToLayoutNavigationTransitions` Свойство должно иметь значение перед добавлением контроллера в стек навигации. Это свойство задано обычный переход по горизонтали скользящего заменяется анимированный переход между макетами двух контроллеров, как показано ниже:

![](transitions-images/nav2.png "Анимированный переход между макетами двух контроллеров")

### <a name="transition-layout"></a>Макет перехода

Помимо поддержки перехода макета в контроллерах навигации, вызывается новый макет `UICollectionViewTransitionLayout` теперь доступна. Этот класс макета позволяет интерактивного управления в процессе перехода макета, позволяя `TransitionProgress` из кода. `UICollectionViewTransitionLayout` отличается от — и не является заменой — `SetCollectionViewLayout` метода из iOS 6, вызвавшее переход анимированных макета. Этот метод не предоставил встроенную поддержку для управления ходом анимированный переход.

 `UICollectionViewTransitionLayout` позволяет, например, распознаватель жестов настроить для управления перехода между макетами в ответ на взаимодействие с пользователем, управляя исходного макета, а также предполагаемую макета к переходу.

Шаги по реализации интерактивного перехода в пределах распознавателя с помощью `UICollectionViewTransitionLayout` таковы:

1.  Создайте распознавателя жестов.
1.  Вызовите `StartInteractiveTransition` метод `UICollectionView` , макет целевой объект и обработчик завершения.
1.  Задайте `TransitionProgress` свойство `UICollectionViewTransitionLayout` экземпляр, возвращаемый `StartInteractiveTransition` метод.
1.  К недействительности структуры.
1.  Вызовите `FinishInteractiveTransition` метод `UICollectionView` для завершения перехода или `CancelInteractiveTransition` метод, чтобы отменить его.  `FinishInteractiveTransition` приводит к анимации для завершения перехода к макету целевой, тогда как `CancelInteractiveTransition` приводит к анимации, Возврат исходного макета.
1.  Он завершал перехода в обработчике завершения `StartInteractiveTransition` метод.
1.  Добавление средства распознавания жестов представлению коллекции.


Следующий код реализует при переходе интерактивных макета в распознавателя жестов жестом сжатия:

```csharp
imagesController = new ImagesCollectionViewController (flowLayout);

nfloat sf = 0.4f;
UICollectionViewTransitionLayout trLayout = null;
UICollectionViewLayout nextLayout;

pinch = new UIPinchGestureRecognizer (g => {

    var progress = Math.Abs(1.0f -  g.Scale)/sf;

    if(trLayout == null){
        if(imagesController.CollectionView.CollectionViewLayout is CircleLayout)
            nextLayout = flowLayout;
        else
            nextLayout = circleLayout;

        trLayout = imagesController.CollectionView.StartInteractiveTransition (nextLayout, (completed, finished) => {   
            Console.WriteLine ("transition completed");
            trLayout = null;
        });
    }

    trLayout.TransitionProgress = (nfloat)progress;

    imagesController.CollectionView.CollectionViewLayout.InvalidateLayout ();

    if(g.State == UIGestureRecognizerState.Ended){
        if (trLayout.TransitionProgress > 0.5f)
            imagesController.CollectionView.FinishInteractiveTransition ();
        else
            imagesController.CollectionView.CancelInteractiveTransition ();
    }

});

imagesController.CollectionView.AddGestureRecognizer (pinch);

```

Как пользователь pinches представление коллекции `TransitionProgress` задается относительно масштаба жестом сжатия. В этой реализации Если пользователь завершает жестом сжатия до перехода 50% завершено, то переход отменяется. В противном случае завершения перехода.




## <a name="related-links"></a>Связанные ссылки

- [Введение в iOS 7 (пример)](https://developer.xamarin.com/samples/monotouch/IntroToiOS7)
- [Обзор пользовательского интерфейса iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Фоновая обработка](~/ios/app-fundamentals/backgrounding/index.md)
