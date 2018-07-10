---
title: Пошаговое руководство. Использование сенсорных технологий в Android
ms.prod: xamarin
ms.assetid: E281F89B-4142-4BD8-8882-FB65508BF69E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/09/2018
ms.openlocfilehash: d379630e3b7fa2b42bd9530e1dccd75e9634dd2f
ms.sourcegitcommit: 3e980fbf92c69c3dd737554e8c6d5b94cf69ee3a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935531"
---
# <a name="walkthrough---using-touch-in-android"></a>Пошаговое руководство. Использование сенсорных технологий в Android

Сообщите нам см. в разделе способы использования концепции из предыдущего раздела в работающее приложение. Мы создадим приложение с помощью четырех действий. Первым действием будет меню или управления, который запускает другие действия, чтобы продемонстрировать различным интерфейсам API. На следующем снимке экрана показано основное действие:

[![Снимок экрана с примером с касания кнопки](android-touch-walkthrough-images/image14.png)](android-touch-walkthrough-images/image14.png#lightbox)

Первое действие, пример Touch, демонстрируют использование обработчиков событий для изменения представления. Распознаватель жестов действия продемонстрирую как подкласс `Android.View.Views` и обрабатывать события, а также показано, как обрабатывать жесты жестом сжатия. Третье и последнее действие, **пользовательский жест**, будет показано, как использовать пользовательских жестов. Чтобы упростить работу и absorb, мы будем разделить в этом пошаговом руководстве на разделы, содержащие каждый раздел, уделяя основное внимание одно из действий.

## <a name="touch-sample-activity"></a>Пример действия сенсорного ввода

-   Откройте проект **TouchWalkthrough\_запустить**. **MainActivity** установлена для перехода &ndash; мы для реализации поведения сенсорного ввода в действие. Если вы запустите приложение и нажмите кнопку **Touch пример**, должно быть запущено следующие действия:

    [![Снимок экрана действия Touch начинает отображается](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

-   Теперь, когда мы подтвердили, что действие запускается, откройте файл **TouchActivity.cs** и добавьте обработчик для `Touch` событие `ImageView`:

    ```csharp
    _touchMeImageView.Touch += TouchMeImageViewOnTouch;
    ```

-   Добавьте следующий метод в **TouchActivity.cs**:

    ```csharp
    private void TouchMeImageViewOnTouch(object sender, View.TouchEventArgs touchEventArgs)
    {
        string message;
        switch (touchEventArgs.Event.Action & MotionEventActions.Mask)
        {
            case MotionEventActions.Down:
            case MotionEventActions.Move:
            message = "Touch Begins";
            break;

            case MotionEventActions.Up:
            message = "Touch Ends";
            break;

            default:
            message = string.Empty;
            break;
        }

        _touchInfoTextView.Text = message;
    }
    ```

Обратите внимание, что в приведенном выше коде Мы относимся `Move` и `Down` действие считаются одинаковыми. Это обусловлено тем, даже если пользователь может не поднимайте пальцев `ImageView`, он может перемещаться или давление выдерживать пользователь может измениться. Эти типы изменений создаст `Move` действие.

Каждый раз, когда пользователь штрихи `ImageView`, `Touch` событие будет вызываться и наш обработчик отобразит сообщение **Touch начинает** на экране, как показано на следующем снимке экрана:

[![Снимок экрана действия Touch начинает](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

До тех пор, пока пользователь касается `ImageView`, **Touch начинает** будет отображаться в `TextView`. Если пользователь больше не касается `ImageView`, сообщение **Touch заканчивается** будет отображаться в `TextView`, как показано на следующем снимке экрана:

[![Снимок экрана действия с участием пользователя заканчивается](android-touch-walkthrough-images/image16.png)](android-touch-walkthrough-images/image16.png#lightbox)


## <a name="gesture-recognizer-activity"></a>Действие распознаватель жестов

Теперь давайте реализуйте действие распознаватель жестов. Это действие будет показано, как Перетащите представление по экрану, иллюстрирующие один из способов реализации масштаба жестом сжатия.

-   Добавьте новое действие к приложению с именем `GestureRecognizer`.
    Измените код для этого действия, как показано ниже:

    ```csharp
    public class GestureRecognizerActivity : Activity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            View v = new GestureRecognizerView(this);
            SetContentView(v);
        }
    }
    ```

-   Добавить новую Android просмотра в проект и назовите его `GestureRecognizerView`. Добавьте следующие переменные для этого класса:

    ```csharp
    private static readonly int InvalidPointerId = -1;

    private readonly Drawable _icon;
    private readonly ScaleGestureDetector _scaleDetector;

    private int _activePointerId = InvalidPointerId;
    private float _lastTouchX;
    private float _lastTouchY;
    private float _posX;
    private float _posY;
    private float _scaleFactor = 1.0f;
    ```

-   Добавьте следующий конструктор `GestureRecognizerView`. Этот конструктор будет добавлен `ImageView` наши действия. На этом этапе код по-прежнему не будет компилироваться &ndash; нам необходимо создать класс `MyScaleListener` помогут с изменением размеров `ImageView` когда пользователь pinches:

    ```csharp
    public GestureRecognizerView(Context context): base(context, null, 0)
    {
        _icon = context.Resources.GetDrawable(Resource.Drawable.Icon);
        _icon.SetBounds(0, 0, _icon.IntrinsicWidth, _icon.IntrinsicHeight);
        _scaleDetector = new ScaleGestureDetector(context, new MyScaleListener(this));
    }
    ```

-   Для рисования изображения на наших действий, нам нужно переопределить `OnDraw` метод класса представления, как показано в следующем фрагменте. Этот код будет переместить `ImageView` к позиции, заданной параметром `_posX` и `_posY` также как и в случае изменения размера изображения на основании коэффициента масштабирования:

    ```csharp
    protected override void OnDraw(Canvas canvas)
    {
        base.OnDraw(canvas);
        canvas.Save();
        canvas.Translate(_posX, _posY);
        canvas.Scale(_scaleFactor, _scaleFactor);
        _icon.Draw(canvas);
        canvas.Restore();
    }
    ```

-   Далее нам нужно обновить переменную экземпляра `_scaleFactor` как пользователь pinches `ImageView`. Мы добавим класс с именем `MyScaleListener`. Этот класс будет прослушивать события масштабирования, которые будет вызываться системой Android, когда пользователь pinches `ImageView`.
    Добавьте следующий класс внутреннего `GestureRecognizerView`. Этот класс является `ScaleGesture.SimpleOnScaleGestureListener`. Этот класс — это класс удобства, что прослушиватели как подкласс, если бы вы заинтересованы в подмножество жестов:

    ```csharp
    private class MyScaleListener : ScaleGestureDetector.SimpleOnScaleGestureListener
    {
        private readonly GestureRecognizerView _view;

        public MyScaleListener(GestureRecognizerView view)
        {
            _view = view;
        }

        public override bool OnScale(ScaleGestureDetector detector)
        {
            _view._scaleFactor *= detector.ScaleFactor;

            // put a limit on how small or big the image can get.
            if (_view._scaleFactor > 5.0f)
            {
                _view._scaleFactor = 5.0f;
            }
            if (_view._scaleFactor < 0.1f)
            {
                _view._scaleFactor = 0.1f;
            }

            _view.Invalidate();
            return true;
        }
    }
    ```

-   Метод next, нам нужно переопределить в `GestureRecognizerView` является `OnTouchEvent`. В следующем коде перечислены полная реализация этого метода. Есть много кода, поэтому ее нужно внимательно и проверьте, что здесь происходит. Первое, что этот метод не выполняет — масштабирование значок при необходимости &ndash; это делается путем вызова `_scaleDetector.OnTouchEvent`. Далее мы пытаемся выяснить, какое действие вызывается этот метод:

    - Если пользователь затронутых экрана, запишем координаты Х и Y, а также идентификатор первого указатель, который затронутых экрана.

    - Если пользователь перемещена их сенсорного экрана, затем мы выяснить, насколько он перемещен указатель.

    - Если пользователь сняты его палец на экране, мы будет остановить отслеживание жестов.

    ```csharp
    public override bool OnTouchEvent(MotionEvent ev)
    {
        _scaleDetector.OnTouchEvent(ev);

        MotionEventActions action = ev.Action & MotionEventActions.Mask;
        int pointerIndex;

        switch (action)
        {
            case MotionEventActions.Down:
            _lastTouchX = ev.GetX();
            _lastTouchY = ev.GetY();
            _activePointerId = ev.GetPointerId(0);
            break;

            case MotionEventActions.Move:
            pointerIndex = ev.FindPointerIndex(_activePointerId);
            float x = ev.GetX(pointerIndex);
            float y = ev.GetY(pointerIndex);
            if (!_scaleDetector.IsInProgress)
            {
                // Only move the ScaleGestureDetector isn't already processing a gesture.
                float deltaX = x - _lastTouchX;
                float deltaY = y - _lastTouchY;
                _posX += deltaX;
                _posY += deltaY;
                Invalidate();
            }

            _lastTouchX = x;
            _lastTouchY = y;
            break;

            case MotionEventActions.Up:
            case MotionEventActions.Cancel:
            // We no longer need to keep track of the active pointer.
            _activePointerId = InvalidPointerId;
            break;

            case MotionEventActions.PointerUp:
            // check to make sure that the pointer that went up is for the gesture we're tracking.
            pointerIndex = (int) (ev.Action & MotionEventActions.PointerIndexMask) >> (int) MotionEventActions.PointerIndexShift;
            int pointerId = ev.GetPointerId(pointerIndex);
            if (pointerId == _activePointerId)
            {
                // This was our active pointer going up. Choose a new
                // action pointer and adjust accordingly
                int newPointerIndex = pointerIndex == 0 ? 1 : 0;
                _lastTouchX = ev.GetX(newPointerIndex);
                _lastTouchY = ev.GetY(newPointerIndex);
                _activePointerId = ev.GetPointerId(newPointerIndex);
            }
            break;

        }
        return true;
    }
    ```

-   Теперь запустите приложение и запустить действие распознаватель жестов.
    При запуске экран должен выглядеть примерно как на снимке экрана ниже:

    [![Распознаватель жестов начальный экран со значком Android](android-touch-walkthrough-images/image17.png)](android-touch-walkthrough-images/image17.png#lightbox)

-   Теперь коснитесь значка и перетащите его на экране. Попробуйте жест жестом сжатия для масштабирования. В некоторый момент экран может выглядеть примерно как на снимке экрана ниже:

    [![Значок жесты перемещения по экрану](android-touch-walkthrough-images/image18.png)](android-touch-walkthrough-images/image18.png#lightbox)

На этом этапе следует оставить для себя личный маркер доступа на задней: вы только что реализовали масштаба жестом сжатия в приложения Android! Сделайте перерыв быстрого и позволяет перейти к третьей и последней действия в этом пошаговом руководстве &ndash; с помощью пользовательских жестов.

## <a name="custom-gesture-activity"></a>Жест пользовательского действия

Окончательный экрана в этом пошаговом руководстве будет использоваться пользовательских жестов.

Для целей данного пошагового руководства, библиотека жесты уже создана с помощью средства жестов и добавлен в проект в файле **ресурсы/raw/жесты**. Используя этот фрагмент пояснения в сторону позволяет мириться с последнего действия в этом пошаговом руководстве.

-   Добавьте файл макета, с именем **пользовательских\_жест\_layout.axml** в проект со следующим содержимым. Проект уже содержит все образы **ресурсы** папку:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:orientation="vertical"
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="1" />
        <ImageView
            android:src="@drawable/check_me"
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="3"
            android:id="@+id/imageView1"
            android:layout_gravity="center_vertical" />
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="1" />
    </LinearLayout>
    ```

-   Затем добавьте новое действие в проект и назовите его `CustomGestureRecognizerActivity.cs`. Добавьте две переменные экземпляра класса, как показано в следующих двух строках кода:

    ```csharp
    private GestureLibrary _gestureLibrary;
    private ImageView _imageView;
    ```

-   Изменить `OnCreate` метод данного действия, так что он похож на следующий код. Давайте рассмотрим немного объяснить, что происходит в этом коде. Первое, что мы делаем — создать экземпляр `GestureOverlayView` и укажите ее в качестве представления корневого действия.
    Мы также назначить обработчик события для `GesturePerformed` событие `GestureOverlayView`. Затем мы увеличению файл макета, который был создан ранее и добавьте, как дочерние представления `GestureOverlayView`. Последним шагом является для инициализации переменной `_gestureLibrary` и загрузить файл жесты из ресурсов приложения. Если для какой-либо причине не удается загрузить файл жесты, нет немногое может сделать это действие, поэтому она выключена:

    ```csharp
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);

        GestureOverlayView gestureOverlayView = new GestureOverlayView(this);
        SetContentView(gestureOverlayView);
        gestureOverlayView.GesturePerformed += GestureOverlayViewOnGesturePerformed;

        View view = LayoutInflater.Inflate(Resource.Layout.custom_gesture_layout, null);
        _imageView = view.FindViewById<ImageView>(Resource.Id.imageView1);
        gestureOverlayView.AddView(view);

        _gestureLibrary = GestureLibraries.FromRawResource(this, Resource.Raw.gestures);
        if (!_gestureLibrary.Load())
        {
            Log.Wtf(GetType().FullName, "There was a problem loading the gesture library.");
            Finish();
        }
    }
    ```

-   Последнее, нам нужно реализовать метод `GestureOverlayViewOnGesturePerformed` как показано в следующем фрагменте кода. Когда `GestureOverlayView` обнаруживает жест, он осуществляет обратный вызов этого метода. Первое, что мы пытаемся получить `IList<Prediction>` объектов, соответствующих жест путем вызова `_gestureLibrary.Recognize()`. Мы используем немного LINQ для получения `Prediction` , имеет наивысший рейтинг для зачеркивания.

    Если без соответствующей жестов с высоким достаточно оценки, а затем завершает работу обработчика событий, не выполняя никаких действий. В противном случае мы Проверьте имя прогноза и изменить на изображение на основе имени жеста:

    ```csharp
    private void GestureOverlayViewOnGesturePerformed(object sender, GestureOverlayView.GesturePerformedEventArgs gesturePerformedEventArgs)
    {
        IEnumerable<Prediction> predictions = from p in _gestureLibrary.Recognize(gesturePerformedEventArgs.Gesture)
        orderby p.Score descending
        where p.Score > 1.0
        select p;
        Prediction prediction = predictions.FirstOrDefault();

        if (prediction == null)
        {
            Log.Debug(GetType().FullName, "Nothing seemed to match the user's gesture, so don't do anything.");
            return;
        }

        Log.Debug(GetType().FullName, "Using the prediction named {0} with a score of {1}.", prediction.Name, prediction.Score);

        if (prediction.Name.StartsWith("checkmark"))
        {
            _imageView.SetImageResource(Resource.Drawable.checked_me);
        }
        else if (prediction.Name.StartsWith("erase", StringComparison.OrdinalIgnoreCase))
        {
            // Match one of our "erase" gestures
            _imageView.SetImageResource(Resource.Drawable.check_me);
        }
    }
    ```

-   Запустите приложение и загрузите действия настраиваемый распознаватель жестов. Он должен выглядеть примерно как на следующем снимке экрана:

    [![Снимок экрана с проверьте образ](android-touch-walkthrough-images/image19.png)](android-touch-walkthrough-images/image19.png#lightbox)

    Теперь рисовать флажок на экране, и отображение точечный рисунок должен выглядеть примерно так, как показано на следующем снимке экрана:

    [![Распознается формируемого флажок, флажок](android-touch-walkthrough-images/image20.png)](android-touch-walkthrough-images/image20.png#lightbox)

    Наконец рисуйте scribble на экране. Этот флажок следует изменить обратно до исходного образа, как показано на эти снимки экрана:

    [![Отображается Scribble на экране, исходное изображение](android-touch-walkthrough-images/image21.png)](android-touch-walkthrough-images/image21.png#lightbox)

Теперь вы узнаете, как интегрировать касания и жестов в приложение Android с помощью Xamarin.Android.


## <a name="related-links"></a>Связанные ссылки

- [Android Touch запустить (пример)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_start)
- [Android Touch окончательный (пример)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_final)
