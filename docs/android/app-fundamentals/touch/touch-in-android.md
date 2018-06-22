---
title: Touch в Android
ms.prod: xamarin
ms.assetid: 405A1FA0-4EFA-4AEB-B672-F36307B9CF16
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: f1ccc86a20cb441bfdda864b8c7e263a691f5ab7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30767496"
---
# <a name="touch-in-android"></a>Touch в Android

Гораздо как iOS, Android создает объект, содержащий данные о физических взаимодействие пользователя с экрана &ndash; `Android.View.MotionEvent` объекта. Этот объект содержит данные, например, какое действие выполняется, где заняло сенсорный установить, какое давление применения и т. д. Объект `MotionEvent` объект разбивает перемещения в следующие значения:

-  Действие код, который описывает тип перемещения, такие как сенсорный ввод начальной сенсорный ввод, перенос между на экране или заканчивается сенсорный ввод.

-  Набор значений оси, которые описывают позицию `MotionEvent` и другие свойства перемещения, где сенсорный происходит, когда выполнялась сенсорный и какое давление использовался.
   Значения оси может отличаться в зависимости от устройства, поэтому предыдущего списка не описываются все значения на оси.


`MotionEvent` Объекта будет передан соответствующий метод в приложении. Приложение Xamarin.Android реагировать на события касания тремя способами:

-  *Создайте обработчик события для `View.Touch`*  - `Android.Views.View` класс имеет `EventHandler<View.TouchEventArgs>` приложения, которые можно назначить обработчик. Это нормальное поведение .NET.

-  *Реализация `View.IOnTouchListener`*  -экземпляров этого интерфейса может назначаться для представления объекта, с помощью представления. `SetOnListener` метод. Это функционально эквивалентно назначении обработчика событий для `View.Touch` события. В случае некоторых общими логику, которая может понадобиться несколько представлений при затронутых будет более эффективным будет создание класса и Реализуйте этот метод, чтобы назначить каждое представление собственный обработчик событий.

-  *Переопределить `View.OnTouchEvent`*  -всех представлений в Android подкласс `Android.Views.View`. Когда представление затронутых, вызывает Android `OnTouchEvent` и передать его `MotionEvent` объект в качестве параметра.


> [!NOTE]
> Не все устройства Android поддерживают сенсорных экранов. 

Добавив следующий тег в файл манифеста вызывает Google Play, отображаются только приложения для тех устройств, которые являются поддержка сенсорного ввода.

```xml
<uses-configuration android:reqTouchScreen="finger" />
```

## <a name="gestures"></a>Жесты

Жест является рукописный фигуру на сенсорного экрана. Жест может иметь один или несколько штрихов, каждого штриха, состоящее из последовательности точек, созданных с использованием разных точки контакта на экране. Android может поддерживать множество различных типов жесты, от простых fling по экрану для сложных жесты, включающих мультисенсорные.

Android предоставляет `Android.Gestures` пространства имен, специально для управления и реагирование на жесты. AT лежит в основе все жесты имеет специальный класс с именем `Android.Gestures.GestureDetector`. Как следует из имен, этот класс будет прослушивать жестов и события на основе `MotionEvents` предоставляемые операционной системой.

Чтобы реализовать детектор жестов, необходимо создать экземпляр действия `GestureDetector` класса и предоставить экземпляр `IOnGestureListener`, как показано в следующем фрагменте кода:

```csharp
GestureOverlayView.IOnGestureListener myListener = new MyGestureListener();
_gestureDetector = new GestureDetector(this, myListener);
```

Действие также необходимо реализовать OnTouchEvent и передать MotionEvent детектор жестов. В следующем фрагменте кода показан пример этого.

```csharp
public override bool OnTouchEvent(MotionEvent e)
{
    // This method is in an Activity
    return _gestureDetector.OnTouchEvent(e);
}
```

При создании экземпляра `GestureDetector` идентифицирует жест интерес, она будет уведомлять действия или приложения при возникновении события или посредством обратного вызова, предоставляемые `GestureDetector.IOnGestureListener`.
Этот интерфейс предоставляет шесть методов для различных жестов:

-  *OnDown* — вызывается, когда происходит касание, но не освобождается.

-  *OnFling* — вызывается, когда происходит fling и предоставляет данные на сенсорный ввод начала и окончания, вызвавшей событие.

-  *OnLongPress* — вызывается, когда происходит нажатие long.

-  *OnScroll* -вызывается при возникновении события прокрутки.

-  *OnShowPress* - вызывается после возникновения OnDown и перемещения или не было выполнено событие.

-  *OnSingleTapUp* — вызывается, когда происходит одним касанием.


Во многих случаях приложения только внимание подмножество жестов. В этом случае приложения должны расширять класс GestureDetector.SimpleOnGestureListener и переопределять методы, соответствующие события, который их интересует.

## <a name="custom-gestures"></a>Пользовательских жестов

Жесты прекрасно подходят для пользователям взаимодействовать с приложением. API-интерфейсы пока обнаруженного было бы достаточно для простых жестов, но могут оказаться немного обременительной задачей для более сложных жестов. Чтобы упростить сложнее жесты, Android предоставляет еще один набор API-Интерфейсы в пространстве имен Android.Gestures, которая облегчит часть нагрузки, связанные с пользовательских жестов.

### <a name="creating-custom-gestures"></a>Создание пользовательских жестов

С момента Android 1.6 Android SDK поставляется вместе с приложением, предустановленные на эмуляторе вызывается жесты построитель. Это приложение позволяет разработчику создавать предопределенные жесты, которые могут быть внедрены в приложении. На следующем снимке экрана показан пример построителя жесты.

[![Снимок экрана жесты построитель с помощью жестов пример](touch-in-android-images/image11.png)](touch-in-android-images/image11.png#lightbox)

Улучшенная версия этого приложения, программа жестов можно найти Google Play. Жест оно очень похоже жесты построитель за исключением того, что позволяет выполнить тестирование жесты после их создания. Это Далее снимке экрана показан построитель жесты.

[![Снимок экрана жестов средство, с помощью жестов пример](touch-in-android-images/image12.png)](touch-in-android-images/image12.png#lightbox)

Средство жестов более удобна для создания пользовательских жестов разрешается жесты, проверяемое при их создании и легко доступны через Google Play.

Жест средство позволяет создавать жест с помощью рисования на экране и назначение имени. После создания жесты, они сохраняются в виде двоичного файла, на SD-карту устройства. Этот файл должен быть полученные от устройства, а затем упаковываются вместе с приложением в папку /Resources/raw. Этот файл можно получить из эмулятора, используя мост отладки Android. В следующем примере показано копирование файла из хранилища Galaxy в каталоге ресурсов приложения:

```shell
$ adb pull /storage/sdcard0/gestures <projectdirectory>/Resources/raw
```

После извлечения файла его необходимо упакованных вместе с приложением в каталоге /Resources/raw. Чтобы использовать этот файл жестов проще загрузить его в GestureLibrary, как показано в следующем фрагменте:

```csharp
GestureLibary myGestures = GestureLibraries.FromRawResources(this, Resource.Raw.gestures);
if (!myGestures.Load())
{
    // The library didn't load, so close the activity.
    Finish();
}
```

### <a name="using-custom-gestures"></a>С помощью пользовательских жестов

Для распознавания пользовательских жестов в действии, она должна иметь Android.Gesture.GestureOverlay объекта, добавляемого в макете. В следующем фрагменте кода показано, как программным способом добавить GestureOverlayView действия:

```csharp
GestureOverlayView gestureOverlayView = new GestureOverlayView(this);
gestureOverlayView.AddOnGesturePerformedListener(this);
SetContentView(gestureOverlayView);
```

В следующем фрагменте XML показано, как декларативно добавить GestureOverlayView:

```xml
<android.gesture.GestureOverlayView
    android:id="@+id/gestures"
    android:layout_width="match_parent "
    android:layout_height="match_parent" />
```

`GestureOverlayView` Имеет несколько событий, которые будет вызываться в процессе рисования жест. Наиболее интересные событие `GesturePeformed`. Это событие возникает после завершения рисования их жестов.

Это событие возникает, операции запрашивает `GestureLibrary` следует предпринять попытку установить жестов, созданные пользователями с помощью одного из жестов средством жестов. `GestureLibrary` Возвращает список объектов прогноза.

Каждый объект прогноза содержит показатель и имя одного из жестов в `GestureLibrary`. Чем выше оценка, тем выше вероятность жестов, с именем в прогноз соответствует жест нарисованного пользователем.
Вообще говоря оценки меньше 1.0 считаются низкой совпадений.

Ниже показан пример совпадающих жест.

```csharp
private void GestureOverlayViewOnGesturePerformed(object sender, GestureOverlayView.GesturePerformedEventArgs gesturePerformedEventArgs)
{
    // In this example _gestureLibrary was instantiated in OnCreate
    IEnumerable<Prediction> predictions = from p in _gestureLibrary.Recognize(gesturePerformedEventArgs.Gesture)
    orderby p.Score descending
    where p.Score > 1.0
    select p;
    Prediction prediction = predictions.FirstOrDefault();

    if (prediction == null)
    {
        Log.Debug(GetType().FullName, "Nothing matched the user's gesture.");
        return;
    }

    Toast.MakeText(this, prediction.Name, ToastLength.Short).Show();
}
```

В это сделать необходимо иметь представление о том, как использовать сенсорный ввод и жестов в приложении Xamarin.Android. Теперь давайте перейдите к Пошаговое руководство и просмотреть все основные понятия в рабочий пример приложения.



## <a name="related-links"></a>Связанные ссылки

- [Android Touch запуска (пример)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_start)
- [Android Touch окончательного (пример)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_final)
