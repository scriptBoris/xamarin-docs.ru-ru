---
title: Пошаговое руководство. использование сенсорного ввода в Android
ms.prod: xamarin
ms.assetid: E281F89B-4142-4BD8-8882-FB65508BF69E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/09/2018
ms.openlocfilehash: 625ba800ce498f80c0344c67e26bd79360de4002
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "34050563"
---
# <a name="walkthrough---using-touch-in-android"></a>Пошаговое руководство. использование сенсорного ввода в Android

Сообщите нам статье использовать концепции из предыдущего раздела в реально работающем приложении. Мы создадим приложение с четыре действия. Первым действием будет меню или коммутатор, который запускает другие действия для демонстрации различных API-интерфейсов. На следующем снимке экрана показано основных действий:

[![Снимок экрана примера с касания кнопки](android-touch-walkthrough-images/image14.png)](android-touch-walkthrough-images/image14.png#lightbox)

Первое действие касания образца, будет показано, как использовать обработчики событий для касаясь представления. Распознаватель жестов действия продемонстрируют как подкласс `Android.View.Views` и обрабатывать события, а также показано, как обрабатывать жесты жестом сжатия. Действие третьей и последней **пользовательских жестов**, будет показано, как использовать пользовательских жестов. Чтобы упростить запоминаются и инструкциями, мы будем разбейте в этом пошаговом руководстве на разделы, содержащие каждого раздела, посвященных одно из действий.

## <a name="touch-sample-activity"></a>Пример действия касания

-   Откройте проект **TouchWalkthrough\_запустить**. **MainActivity** установлена переход &ndash; мы для реализации поведения сенсорного ввода в действие. Если запустить приложение и нажмите кнопку **Touch образца**, необходимо запустить следующие действия:

    [![Снимок экрана: действия с Touch начинает отображается](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

-   Теперь, когда мы подтвердили, что действие запускается, откройте файл **TouchActivity.cs** и добавьте обработчик для `Touch` событие `ImageView`:

    ```csharp
    _touchMeImageView.Touch += TouchMeImageViewOnTouch;
    ```

-   Добавьте следующий метод **TouchActivity.cs**:

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

Обратите внимание, в приведенном выше коде мы обращаемся `Move` и `Down` действие считаются одинаковыми. Причина в том, даже если пользователь может не поднимайте пальцев `ImageView`, он может переместиться в другое место или давление выдерживать пользователь может изменить. Эти типы изменений создаст `Move` действие.

При каждом запуске штрихи пользователя `ImageView`, `Touch` возникает событие и наши обработчик будет отображено сообщение **Touch начинает** на экране, как показано на следующем снимке экрана:

[![Снимок экрана: действия с участием начинается](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

До тех пор, пока пользователь касается `ImageView`, **Touch начинает** будет отображаться в `TextView`. Если пользователь больше не касается `ImageView`, сообщение **Touch заканчивается** будет отображаться в `TextView`, как показано на следующем снимке экрана:

[![Снимок экрана: действия с участием заканчивается](android-touch-walkthrough-images/image16.png)](android-touch-walkthrough-images/image16.png#lightbox)


## <a name="gesture-recognizer-activity"></a>Действие распознаватель жестов

Теперь позволяет реализовать действие распознаватель жестов. Это действие будет показано перетащите представление на экране и иллюстрируется, как реализовать масштабирование сжатием.

-   Добавление нового действия в приложении с именем `GestureRecognizer`.
    Измените код для этого действия, как показано в следующем коде:

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

-   Добавить новый Android просмотра в проект и назовите его `GestureRecognizerView`. Добавьте следующие переменные для этого класса:

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

-   Для рисования изображения на наши действия, необходимо переопределить `OnDraw` метод класса представления, как показано в следующем фрагменте. Этот код будет перемещаться `ImageView` в позиции, указанной параметром `_posX` и `_posY` также как и в случае изменения размера изображения в соответствии с коэффициент масштабирования:

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

-   Далее нам нужно обновить переменную экземпляра `_scaleFactor` имени пользователя pinches `ImageView`. Мы добавим класс с именем `MyScaleListener`. Этот класс будет прослушивать события шкалы, которые будет вызываться системой Android, когда пользователь pinches `ImageView`.
    Добавьте следующий класс внутреннего `GestureRecognizerView`. Этот класс является `ScaleGesture.SimpleOnScaleGestureListener`. Этот класс является удобство, прослушиватели можно подкласс, если вы заинтересованы в подмножестве жестов:

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

            _iconview.Invalidate();
            return true;
        }
    }
    ```

-   Далее метод необходимо переопределить в `GestureRecognizerView` — `OnTouchEvent`. В следующем коде перечислены полная реализация этого метода. Имеется большой объем кода здесь, поэтому следует внимательно и проверьте, что происходит в данной статье. Первое, что этот метод не является масштабировать значок при необходимости &ndash; эта операция обрабатывается путем вызова `_scaleDetector.OnTouchEvent`. Далее мы пытаемся выяснить, какое действие вызывает этот метод:

    - Если пользователь затронутых экрана, мы записывать положения X и Y и идентификатор первого из них, затронутых экрана.

    - Если пользователь перемещена их сенсорного экрана, затем мы понять, насколько далеко пользователь перемещен указатель.

    - Если пользователь поднят его палец с экрана, затем будет остановлен отслеживания жесты.

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

-   Теперь запустите приложение и запустить средство распознавания жестов действия.
    При открытии экрана должен выглядеть примерно как на снимке экрана ниже:

    [![Экран запуска распознаватель жестов значком Android](android-touch-walkthrough-images/image17.png)](android-touch-walkthrough-images/image17.png#lightbox)

-   Теперь коснитесь значка и перетащите его на экране. Попробуйте жестов масштабирование сжатием. В некоторый момент экрана может выглядеть примерно как на следующем снимке экрана:

    [![Значок перемещения жесты на экране](android-touch-walkthrough-images/image18.png)](android-touch-walkthrough-images/image18.png#lightbox)

На этом этапе следует предоставить самостоятельно pat на задней: масштабирование сжатием просто реализованной в приложении Android! Сделайте перерыв быстрого и позволяет перейти к третьей и последней действия в этом пошаговом руководстве &ndash; с помощью пользовательских жестов.

## <a name="custom-gesture-activity"></a>Действие пользовательских жестов

Последнем экране в этом пошаговом руководстве будет использоваться пользовательских жестов.

Для целей данного пошагового руководства, библиотека жесты уже создана с помощью средства жестов и добавлен в проект в файле **ресурсы или raw/жесты**. Это битом housekeeping в сторону позволяет получить на с последним действием в этом пошаговом руководстве.

-   Добавьте файл макет с именем **пользовательские\_жестов\_layout.axml** в проект со следующим содержимым. Проект уже содержит все образы **ресурсов** папки:

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

-   Затем добавьте новое действие в проект и назовите его `CustomGestureRecognizerActivity.cs`. Добавьте две переменные экземпляра класса, как отображение в следующие две строки кода:

    ```csharp
    private GestureLibrary _gestureLibrary;
    private ImageView _imageView;
    ```

-   Изменить `OnCreate` этот метод так, чтобы она похожа на следующий код. Давайте минуту объясняется, что происходит в этом коде. Первое, что мы делаем — создать экземпляр `GestureOverlayView` и укажите его в качестве представления корневого действия.
    Мы также назначить обработчик события для `GesturePerformed` событие `GestureOverlayView`. Далее мы увеличению файл макета, который был создан ранее и добавить его как дочерние представления `GestureOverlayView`. Последним шагом является для инициализации переменной `_gestureLibrary` и загрузить файл жесты из ресурсов приложения. Если для какой-либо причине не удается загрузить файл жесты, нет практически ничего сделать это действие, поэтому он выключен:

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

-   Заключительную, нам необходимо реализовать метод `GestureOverlayViewOnGesturePerformed` как показано в следующем фрагменте кода. Когда `GestureOverlayView` обнаруживает жест, он выполняет обратный вызов этого метода. Прежде всего, мы пытаемся получить `IList<Prediction>` объектов, соответствующих жест путем вызова `_gestureLibrary.Recognize()`. Мы используем немного LINQ для получения `Prediction` , имеет наибольший показатель для жест.

    Если без соответствующей жестов с высоким достаточно оценки, а затем завершает работу обработчика событий, не выполняя никаких действий. В противном случае мы Проверьте имя прогноза и изменить на изображение на основе имени жест:

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

-   Запустите приложение и запустить средство распознавания жестов пользовательского действия. Он должен выглядеть примерно следующий снимок экрана:

    [![Снимок экрана с проверьте изображения](android-touch-walkthrough-images/image19.png)](android-touch-walkthrough-images/image19.png#lightbox)

    Теперь нарисуйте флажком на экране и отображение точечного рисунка должна выглядеть примерно так, что показано на следующем снимке экрана:

    [![Распознается формируемого флажком, флажок](android-touch-walkthrough-images/image20.png)](android-touch-walkthrough-images/image20.png#lightbox)

    Наконец нарисуйте scribble на экране. Флажок следует изменить обратно до исходного образа, как показано в эти снимки экрана:

    [![Отображается Scribble на экране, исходное изображение](android-touch-walkthrough-images/image21.png)](android-touch-walkthrough-images/image21.png#lightbox)

Теперь у вас есть узнаете, как интегрировать сенсорный ввод и жестов в приложения с помощью Xamarin.Android.


## <a name="related-links"></a>Связанные ссылки

- [Android Touch запуска (пример)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_start)
- [Android Touch окончательного (пример)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_final)
