---
title: 'Пошаговое руководство: Использование сенсорных технологий в Xamarin.iOS'
description: Этот документ описывает обработку сенсорного ввода в приложениях Xamarin.iOS, обсуждать пример сенсорного взаимодействия, средства распознавания жестов и жест пользовательского распознавателя.
ms.prod: xamarin
ms.assetid: 13F8289B-7A80-4959-AF3F-57874D866DCA
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: bff4d46ac9d5fe893cbb0a2dfa032e1b9f6daa0e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121559"
---
# <a name="walkthrough-using-touch-in-xamarinios"></a>Пошаговое руководство: Использование сенсорных технологий в Xamarin.iOS

В этом пошаговом руководстве показано, как написать код, который реагирует на различные виды событий касания. Каждый пример содержится в отдельный экран:

- [Touch примеры](#Touch_Samples) — как реагировать на события касания.
- [Примеры распознаватель жестов](#Gesture_Recognizer_Samples) — как использовать встроенные жест распознаватели.
- [Пользовательский распознаватель жестов образец](#Custom_Gesture_Recognizer) — способ построения пользовательского распознавателя.

Каждый раздел содержит инструкции по написанию кода с нуля.
[Начиная пример кода](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start) уже включает в себя полный экран раскадровки и меню:

 [![](ios-touch-walkthrough-images/image3.png "Пример включает меню экрана")](ios-touch-walkthrough-images/image3.png#lightbox)

Следуйте инструкциям ниже, чтобы добавить код в раскадровку и узнайте о различных типах событий сенсорного ввода, доступных в iOS. Кроме того, откройте [готовом примере](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_final) чтобы увидеть, что все работает.

<a name="Touch_Samples"/>

## <a name="touch-samples"></a>Примеры сенсорного ввода

В этом примере продемонстрированы некоторые точки касания API-интерфейсы. Выполните следующие действия, чтобы добавить код, необходимый для реализации события касания.


1. Откройте проект **Touch_Start**. Первый запуск проекта, чтобы убедиться, что все работает нормально и жестов **Touch примеры** кнопки. (Несмотря на то, что ни одна из кнопок будет работать), вы увидите экран, аналогичный следующему:
    
    [![](ios-touch-walkthrough-images/image4.png "Пример приложения, выполните с помощью кнопок не работает")](ios-touch-walkthrough-images/image4.png#lightbox)


1. Измените файл **TouchViewController.cs** и добавьте следующие переменные два экземпляра в класс `TouchViewController`:

    ```csharp 
    #region Private Variables
    private bool imageHighlighted = false;
    private bool touchStartedInside;
    #endregion
    ```


1. Реализуйте `TouchesBegan` метод, как показано в следующем коде:

    ```csharp 
    public override void TouchesBegan(NSSet touches, UIEvent evt)
    {
        base.TouchesBegan(touches, evt);
    
        // If Multitouch is enabled, report the number of fingers down
        TouchStatus.Text = string.Format ("Number of fingers {0}", touches.Count);
    
        // Get the current touch
        UITouch touch = touches.AnyObject as UITouch;
        if (touch != null)
        {
            // Check to see if any of the images have been touched
            if (TouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                // Fist image touched
                TouchImage.Image = UIImage.FromBundle("TouchMe_Touched.png");
                TouchStatus.Text = "Touches Began";
            } else if (touch.TapCount == 2 && DoubleTouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                // Second image double-tapped, toggle bitmap
                if (imageHighlighted)
                {
                    DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe.png");
                    TouchStatus.Text = "Double-Tapped Off";
                }
                else
                {
                    DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe_Highlighted.png");
                    TouchStatus.Text = "Double-Tapped On";
                }
                imageHighlighted = !imageHighlighted;
            } else if (DragImage.Frame.Contains(touch.LocationInView(View)))
            {
                // Third image touched, prepare to drag
                touchStartedInside = true;
            }
        }
    }
    ```
    
    Этот метод работает, проверив наличие `UITouch` объекта и если он существует выполнить некоторую операцию над которой произошло сенсорное устройство:

    * _Внутри TouchImage_ — отображать текст `Touches Began` в метку и изменение образа.
    * _Внутри DoubleTouchImage_ — изменить изображение, отображаемое, если жест двойного касания.
    * _Внутри DragImage_ — установить флаг, указывающий, что была начата сенсорное устройство. Метод `TouchesMoved` будет использовать этот флаг для определения, если `DragImage` должны перемещаться по экрану или нет, как будет показано на следующем шаге.

    Приведенный выше код обрабатывает только отдельные штрихи, по-прежнему нет поведения, если пользователь перемещается пальцев на экране. Чтобы ответить на перемещение, реализовать `TouchesMoved` как показано в следующем коде:

    ```csharp 
    public override void TouchesMoved(NSSet touches, UIEvent evt)
    {
        base.TouchesMoved(touches, evt);
        // get the touch
        UITouch touch = touches.AnyObject as UITouch;
        if (touch != null)
        {
            //==== IMAGE TOUCH
            if (TouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                TouchStatus.Text = "Touches Moved";
            }

            //==== IMAGE DRAG
            // check to see if the touch started in the drag me image
            if (touchStartedInside)
            {
                // move the shape
                float offsetX = touch.PreviousLocationInView(View).X - touch.LocationInView(View).X;
                float offsetY = touch.PreviousLocationInView(View).Y - touch.LocationInView(View).Y;
                DragImage.Frame = new RectangleF(new PointF(DragImage.Frame.X - offsetX, DragImage.Frame.Y - offsetY), DragImage.Frame.Size);
            }
        }
    }
    ```

    Этот метод возвращает `UITouch` объекта, а затем проверяет, где произошла сенсорное устройство. Если сенсорный `TouchImage`, текст перемещать штрихи отображается на экране. 

    Если `touchStartedInside` имеет значение true, то мы знаем, что у пользователя есть пальцев `DragImage` и перемещения его. Код будет переместить `DragImage` пользователь перемещает их пальцем по экрану.

1. Мы должны обрабатывать случаи, когда пользователь поднимает палец его на экране или iOS отменяет событие сенсорного ввода. Для этого мы будем реализовывать `TouchesEnded` и `TouchesCancelled` как показано ниже:

    ```csharp
    public override void TouchesCancelled(NSSet touches, UIEvent evt)
    {
        base.TouchesCancelled(touches, evt);
    
        // reset our tracking flags
        touchStartedInside = false;
        TouchImage.Image = UIImage.FromBundle("TouchMe.png");
        TouchStatus.Text = "";
    }
    
    public override void TouchesEnded(NSSet touches, UIEvent evt)
    {
        base.TouchesEnded(touches, evt);
        // get the touch
        UITouch touch = touches.AnyObject as UITouch;
        if (touch != null)
        {
            //==== IMAGE TOUCH
            if (TouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                TouchImage.Image = UIImage.FromBundle("TouchMe.png");
                TouchStatus.Text = "Touches Ended";
            }
        }
        // reset our tracking flags
        touchStartedInside = false;
    }
    ```
    
    Оба эти метода приведет к сбросу `touchStartedInside` значение false для флага. `TouchesEnded` также будет отображения `TouchesEnded` на экране.

1. На этом этапе на экране Touch примеры завершена. Обратите внимание на то, как экрана изменяется при взаимодействии с каждым из образов, как показано на следующем снимке экрана:
        
    [![](ios-touch-walkthrough-images/image4.png "Начальный экран приложения")](ios-touch-walkthrough-images/image4.png#lightbox)
    
    [![](ios-touch-walkthrough-images/image5.png "Экран после перетаскивания пользователем кнопки")](ios-touch-walkthrough-images/image5.png#lightbox)
 

<a name="Gesture_Recognizer_Samples" />

##  <a name="gesture-recognizer-samples"></a>Примеры распознаватель жестов

[Выше](#Touch_Samples) было показано, как перетаскивание объекта по экрану с помощью события касания.
В этом разделе мы будет избавиться от событий касания и показано, как использовать следующие средства распознавания жестов:

-  `UIPanGestureRecognizer` Для перетаскивания изображения по экрану.
-  `UITapGestureRecognizer` Реагировать на двойные касания на экране.

При запуске [начиная пример кода](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start) и щелкнуть **примеры распознаватель жестов** кнопку, вы должны увидеть следующий экран:

 [![](ios-touch-walkthrough-images/image6.png "Щелкните кнопку \"образцы распознаватель жестов\", чтобы отобразить этот экран")](ios-touch-walkthrough-images/image6.png#lightbox)

Выполните следующие действия для реализации распознавателей жестов.


1. Измените файл **GestureViewController.cs** и добавьте следующую переменную экземпляра:

    ```csharp
    #region Private Variables
    private bool imageHighlighted = false;
    private RectangleF originalImageFrame = RectangleF.Empty;
    #endregion
    ```

    Нам нужен этот экземпляр переменной для отслеживания предыдущее расположение изображения.
Распознаватель жестов pan будет использовать `originalImageFrame` значение вычисляется смещение, необходимые для повторной отрисовки поверхности изображения на экране.

1. Добавьте следующий метод к контроллеру:

    ```csharp
    private void WireUpDragGestureRecognizer()
    {
        // Create a new tap gesture
        UIPanGestureRecognizer gesture = new UIPanGestureRecognizer();
    
        // Wire up the event handler (have to use a selector)
        gesture.AddTarget(() => HandleDrag(gesture));  // to be defined
    
        // Add the gesture recognizer to the view
        DragImage.AddGestureRecognizer(gesture);
    }
    ```

    Этот код создает экземпляр `UIPanGestureRecognizer` экземпляра и добавляет его в представление.
Обратите внимание на то, что целевой объект присвоенное жестов в виде метода `HandleDrag` — этот метод предоставляется на следующем шаге.

1. Чтобы реализовать HandleDrag, добавьте следующий код в контроллер:

    ```csharp
    private void HandleDrag(UIPanGestureRecognizer recognizer)
    {
        // If it's just began, cache the location of the image
        if (recognizer.State == UIGestureRecognizerState.Began)
        {
            originalImageFrame = DragImage.Frame;
        }
    
        // Move the image if the gesture is valid
        if (recognizer.State != (UIGestureRecognizerState.Cancelled | UIGestureRecognizerState.Failed
            | UIGestureRecognizerState.Possible))
        {
            // Move the image by adding the offset to the object's frame
            PointF offset = recognizer.TranslationInView(DragImage);
            RectangleF newFrame = originalImageFrame;
            newFrame.Offset(offset.X, offset.Y);
            DragImage.Frame = newFrame;
        }
    }
    ```

    Приведенный выше код будет сначала проверьте состояние средства распознавания жестов и переместите изображение по экрану. Этот код в месте контроллер теперь поддерживает перетаскивание один образ по экрану.


1. Добавление `UITapGestureRecognizer` это приведет к изменению изображения, отображаемого в DoubleTouchImage. Добавьте следующий метод в `GestureViewController` контроллера:

    ```csharp
    private void WireUpTapGestureRecognizer()
    {
        // Create a new tap gesture
        UITapGestureRecognizer tapGesture = null;
    
        // Report touch
        Action action = () => {
            TouchStatus.Text = string.Format("Image touched at: {0}",tapGesture.LocationOfTouch(0, DoubleTouchImage));
    
            // Toggle the image
            if (imageHighlighted)
            {
                DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe.png");
            }
            else
            {
                DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe_Highlighted.png");
            }
            imageHighlighted = !imageHighlighted;
        };
    
        tapGesture = new UITapGestureRecognizer(action);
    
        // Configure it
        tapGesture.NumberOfTapsRequired = 2;
    
        // Add the gesture recognizer to the view
        DoubleTouchImage.AddGestureRecognizer(tapGesture);
    }
    ```

    Этот код очень похож на код `UIPanGestureRecognizer` , но вместо использования делегата для целевого объекта, мы используем `Action`. 

1. Последнее, нам нужно сделать, — это изменить `ViewDidLoad` таким образом, чтобы она вызывает методы, которые мы только что добавили. Измените ViewDidLoad, поэтому она похожа на следующий код:

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();
    
        Title = "Gesture Recognizers";
    
        // Save initial state
        originalImageFrame = DragImage.Frame;
    
        WireUpTapGestureRecognizer();
        WireUpDragGestureRecognizer();
    }
    ```

    Также Заметьте, что мы инициализации значения `originalImageFrame`.


1. Запустите приложение и взаимодействовать с два изображения.
Ниже приведен один пример этих взаимодействий:
    
    [![](ios-touch-walkthrough-images/image7.png "На этом снимке экрана показано взаимодействие перетаскивания")](ios-touch-walkthrough-images/image7.png#lightbox)



<a name="Custom_Gesture_Recognizer"/>

## <a name="custom-gesture-recognizer"></a>Пользовательский распознаватель

В этом разделе мы применяем концепции из предыдущих разделов для построения пользовательского распознавателя. Пользовательский распознаватель будет подклассы `UIGestureRecognizer`и распознает когда пользователь рисует «V» на экране затем переключите растрового изображения. Ниже приведен пример этого экрана:

 [![](ios-touch-walkthrough-images/image8.png "Приложение распознает, когда пользователь рисует «V» на экране")](ios-touch-walkthrough-images/image8.png#lightbox)

Выполните следующие действия для создания пользовательского распознавателя.


1. Добавьте новый класс в проект с именем `CheckmarkGestureRecognizer`и это должно выглядеть аналогично следующему коду:

    ```csharp
    using System;
    using CoreGraphics;
    using Foundation;
    using UIKit;
    
    namespace Touch
    {
        public class CheckmarkGestureRecognizer : UIGestureRecognizer
        {
            #region Private Variables
            private CGPoint midpoint = CGPoint.Empty;
            private bool strokeUp = false;
            #endregion
    
            #region Override Methods
            /// <summary>
            ///   Called when the touches end or the recognizer state fails
            /// </summary>
            public override void Reset()
            {
                base.Reset();
    
                strokeUp = false;
                midpoint = CGPoint.Empty;
            }
    
            /// <summary>
            ///   Is called when the fingers touch the screen.
            /// </summary>
            public override void TouchesBegan(NSSet touches, UIEvent evt)
            {
                base.TouchesBegan(touches, evt);
    
                // we want one and only one finger
                if (touches.Count != 1)
                {
                    base.State = UIGestureRecognizerState.Failed;
                }
    
                Console.WriteLine(base.State.ToString());
            }
    
            /// <summary>
            ///   Called when the touches are cancelled due to a phone call, etc.
            /// </summary>
            public override void TouchesCancelled(NSSet touches, UIEvent evt)
            {
                base.TouchesCancelled(touches, evt);
                // we fail the recognizer so that there isn't unexpected behavior
                // if the application comes back into view
                base.State = UIGestureRecognizerState.Failed;
            }
    
            /// <summary>
            ///   Called when the fingers lift off the screen
            /// </summary>
            public override void TouchesEnded(NSSet touches, UIEvent evt)
            {
                base.TouchesEnded(touches, evt);
                //
                if (base.State == UIGestureRecognizerState.Possible && strokeUp)
                {
                    base.State = UIGestureRecognizerState.Recognized;
                }
    
                Console.WriteLine(base.State.ToString());
            }
    
            /// <summary>
            ///   Called when the fingers move
            /// </summary>
            public override void TouchesMoved(NSSet touches, UIEvent evt)
            {
                base.TouchesMoved(touches, evt);
    
                // if we haven't already failed
                if (base.State != UIGestureRecognizerState.Failed)
                {
                    // get the current and previous touch point
                    CGPoint newPoint = (touches.AnyObject as UITouch).LocationInView(View);
                    CGPoint previousPoint = (touches.AnyObject as UITouch).PreviousLocationInView(View);
    
                    // if we're not already on the upstroke
                    if (!strokeUp)
                    {
                        // if we're moving down, just continue to set the midpoint at
                        // whatever point we're at. when we start to stroke up, it'll stick
                        // as the last point before we upticked
                        if (newPoint.X >= previousPoint.X && newPoint.Y >= previousPoint.Y)
                        {
                            midpoint = newPoint;
                        }
                        // if we're stroking up (moving right x and up y [y axis is flipped])
                        else if (newPoint.X >= previousPoint.X && newPoint.Y <= previousPoint.Y)
                        {
                            strokeUp = true;
                        }
                        // otherwise, we fail the recognizer
                        else
                        {
                            base.State = UIGestureRecognizerState.Failed;
                        }
                    }
                }
    
                Console.WriteLine(base.State.ToString());
            }
            #endregion
        }
    }
    ```

    Когда вызывается метод Reset `State` свойство меняется на `Recognized` или `Ended`. Это время для сброса в пользовательских распознавания внутреннее состояние.
Теперь класс начните следующий раз, когда пользователь взаимодействует с приложением и готовы для выполнения повторной попытки распознавание жестов.



1. Теперь, когда мы определили пользовательского распознавателя (`CheckmarkGestureRecognizer`) изменить **CustomGestureViewController.cs** файл и добавьте следующие два экземпляра переменные:

    ```csharp
    #region Private Variables
    private bool isChecked = false;
    private CheckmarkGestureRecognizer checkmarkGesture;
    #endregion
    ```

1. Чтобы создать и настроить наших распознаватель жестов, добавьте следующий метод к контроллеру:

    ```csharp
    private void WireUpCheckmarkGestureRecognizer()
    {
        // Create the recognizer
        checkmarkGesture = new CheckmarkGestureRecognizer();
    
        // Wire up the event handler
        checkmarkGesture.AddTarget(() => {
            if (checkmarkGesture.State == (UIGestureRecognizerState.Recognized | UIGestureRecognizerState.Ended))
            {
                if (isChecked)
                {
                    CheckboxImage.Image = UIImage.FromBundle("CheckBox_Unchecked.png");
                }
                else
                {
                    CheckboxImage.Image = UIImage.FromBundle("CheckBox_Checked.png");
                }
                isChecked = !isChecked;
            }
        });
    
        // Add the gesture recognizer to the view
        View.AddGestureRecognizer(checkmarkGesture);
    }
    ```

1. Изменить `ViewDidLoad` таким образом, чтобы он вызывает `WireUpCheckmarkGestureRecognizer`, как показано в следующем фрагменте кода:

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();
    
        // Wire up the gesture recognizer
        WireUpCheckmarkGestureRecognizer();
    }
    ```

1. Запустите приложение и попробуйте изображения на экране «V». Вы увидите, что изображение Отображение изменений, как показано на следующем снимке экрана:
    
    [![](ios-touch-walkthrough-images/image9.png "Кнопка проверки")](ios-touch-walkthrough-images/image9.png#lightbox)
    
    [![](ios-touch-walkthrough-images/image10.png "Unchecked кнопки")](ios-touch-walkthrough-images/image10.png#lightbox)



Указанных выше трех разделах демонстрируется различные способы реагировать на события в iOS касания: с помощью события касания, распознавателей жестов встроенные или с помощью пользовательского распознавателя.



## <a name="related-links"></a>Связанные ссылки

- [iOS Touch запустить (пример)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start)
- [iOS Touch окончательный (пример)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_final)
