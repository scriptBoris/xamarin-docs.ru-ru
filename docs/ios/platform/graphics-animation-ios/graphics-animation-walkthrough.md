---
title: С помощью Core графика и анимация Core в Xamarin.iOS
description: В этой статье демонстрируется Пошаговое создание приложения, использующего двухмерной графики и анимации Core. Он показывает способ рисования на экране в ответ на сенсорного ввода пользователя, а также демонстрируется порядок анимации свойства изображение, чтобы пройти вдоль контура.
ms.prod: xamarin
ms.assetid: 4B96D5CD-1BF5-4520-AAA6-2B857C83815C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: cecfd7f3a9678f298af3ed547aa7b50a18238729
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242008"
---
# <a name="using-core-graphics-and-core-animation-in-xamarinios"></a>С помощью Core графика и анимация Core в Xamarin.iOS

В этом пошаговом руководстве мы собираемся рисование контура с помощью двухмерной графики в ответ на сенсорный ввод. Затем мы добавим `CALayer` содержащий изображение, которое мы будет анимации вдоль пути.

На следующем снимке экрана показан завершенное приложение:

![](graphics-animation-walkthrough-images/00-final-app.png "Готовое приложение")

Перед началом загрузки *GraphicsDemo* , которая входит в этом руководстве. Его можно скачать [здесь](https://developer.xamarin.com/samples/monotouch/GraphicsAndAnimation/) и находится внутри **GraphicsWalkthrough** каталог запуска в проект с именем **GraphicsDemo_starter** , дважды щелкнув его, и Откройте `DemoView` класса.

## <a name="drawing-a-path"></a>Рисование контура


1. В `DemoView` добавить `CGPath` переменной в класс и создать его экземпляр в конструктор. Также объявить два `CGPoint` переменные, `initialPoint` и `latestPoint`, что мы будем использовать для захвата точки касания, откуда же следует конструировать путь:
    
    ```csharp
    public class DemoView : UIView
    {
        CGPath path;
        CGPoint initialPoint;
        CGPoint latestPoint;
    
        public DemoView ()
        {
            BackgroundColor = UIColor.White;
    
            path = new CGPath ();
        }
    }
    ```

2. Добавьте следующие директивы using:

    ```csharp
    using CoreGraphics;
    using CoreAnimation;
    using Foundation;
    ```

3. Затем Переопределите `TouchesBegan` и `TouchesMoved,` и добавьте следующие реализации для записи начального сенсорной точки и каждой последующей сенсорной точки, соответственно:

    ```csharp
    public override void TouchesBegan (NSSet touches, UIEvent evt){
    
        base.TouchesBegan (touches, evt);
    
        UITouch touch = touches.AnyObject as UITouch;
        
        if (touch != null) {
            initialPoint = touch.LocationInView (this);
        }
    }
    
    public override void TouchesMoved (NSSet touches, UIEvent evt){
    
        base.TouchesMoved (touches, evt);
    
        UITouch touch = touches.AnyObject as UITouch;
        
        if (touch != null) {
            latestPoint = touch.LocationInView (this);
            SetNeedsDisplay ();
        }
    }
    ```

    `SetNeedsDisplay` будет вызываться каждый раз, перемещать штрихи выполнился `Draw` должен быть вызван в следующей проверки выполнения цикла.

4. Мы добавим строки в путь в `Draw` метод и использование красной пунктирной линией для рисования с помощью. [Реализуйте `Draw` ](~/ios/platform/graphics-animation-ios/core-graphics.md) с кодом, показанным ниже:

    ```csharp
    public override void Draw (CGRect rect){
    
        base.Draw (rect);
    
        if (!initialPoint.IsEmpty) {
    
            //get graphics context
            using(CGContext g = UIGraphics.GetCurrentContext ()){
                    
                //set up drawing attributes
                g.SetLineWidth (2);
                UIColor.Red.SetStroke ();
    
                //add lines to the touch points
                if (path.IsEmpty) {
                    path.AddLines (new CGPoint[]{initialPoint, latestPoint});
                } else {
                    path.AddLineToPoint (latestPoint);
                }
            
                //use a dashed line
                g.SetLineDash (0, new nfloat[] { 5, 2 * (nfloat)Math.PI });
                                
                //add geometry to graphics context and draw it
                g.AddPath (path);       
                g.DrawPath (CGPathDrawingMode.Stroke);
            }
        }
    }
    ```

Если запустить приложение сейчас, мы можете коснуться для рисования на экране, как показано на следующем снимке экрана:

![](graphics-animation-walkthrough-images/01-path.png "Рисование на экране")

## <a name="animating-along-a-path"></a>Анимации вдоль пути

Теперь, когда мы реализовали код, чтобы разрешить пользователям Нарисуйте контур, давайте добавим код для анимации слой нарисованной траектории.

1. Во-первых, добавьте [ `CALayer` ](~/ios/platform/graphics-animation-ios/core-animation.md) переменных в класс и создать его в конструкторе:

    ```csharp
    public class DemoView : UIView
        {
            …
    
            CALayer layer;
    
            public DemoView (){
                …
    
                //create layer
                layer = new CALayer ();
                layer.Bounds = new CGRect (0, 0, 50, 50);
                layer.Position = new CGPoint (50, 50);
                layer.Contents = UIImage.FromFile ("monkey.png").CGImage;
                layer.ContentsGravity = CALayer.GravityResizeAspect;
                layer.BorderWidth = 1.5f;
                layer.CornerRadius = 5;
                layer.BorderColor = UIColor.Blue.CGColor;
                layer.BackgroundColor = UIColor.Purple.CGColor;
            }
    ```

2. Далее мы добавим слой как подуровень слоя представления когда пользователь поднимает вверх пальцев на экране. Затем мы создадим опорный кадр анимации, используя путь, анимация слоя `Position`.

    Для этого нам нужно переопределить `TouchesEnded` и добавьте следующий код:

    ```csharp
    public override void TouchesEnded (NSSet touches, UIEvent evt)
        {
            base.TouchesEnded (touches, evt);

            //add layer with image and animate along path

            if (layer.SuperLayer == null)
                Layer.AddSublayer (layer);

            // create a keyframe animation for the position using the path
            layer.Position = latestPoint;
            CAKeyFrameAnimation animPosition = (CAKeyFrameAnimation)CAKeyFrameAnimation.FromKeyPath ("position");
            animPosition.Path = path;
            animPosition.Duration = 3;
            layer.AddAnimation (animPosition, "position");
        }
    ```

3. Запустить приложение сейчас и после рисования, слой с изображением добавляется и перемещается вдоль вычерченного пути:

![](graphics-animation-walkthrough-images/00-final-app.png "Добавляется слой с изображением, а перемещается вдоль нарисованную траекторию")

## <a name="summary"></a>Сводка

В этой статье мы были рассмотрены пример, в котором объединенных концепции графики и анимации. Во-первых, мы показали, как использовать Core Graphics для рисования контура `UIView` в ответ на сенсорного ввода пользователя. Затем мы показали, как использовать базовой анимации, чтобы сделать образ перемещаться по этому пути.


## <a name="related-links"></a>Связанные ссылки

- [Core Animation](~/ios/platform/graphics-animation-ios/core-animation.md)
- [Core Graphics](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [Рецепты Core анимации](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
