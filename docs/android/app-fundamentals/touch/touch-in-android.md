---
title: Сенсорные технологии в Android
ms.prod: xamarin
ms.assetid: 405A1FA0-4EFA-4AEB-B672-F36307B9CF16
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: a25a1c3be8c952536c0ef40b7f7c4a64f5748516
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/11/2018
ms.locfileid: "51527239"
---
# <a name="touch-in-android"></a>Сенсорные технологии в Android

Во многом подобно iOS, Android создает объект, содержащий данные о физического взаимодействия пользователя с экрана &ndash; `Android.View.MotionEvent` объекта. Этот объект содержит данные, например, какое действие выполняется, где заняло сенсорное устройство поместите, какое давление была применена, и т.д. Объект `MotionEvent` объект разбивает перемещения в следующие значения:

-  Код действия, который описывает тип перемещения, такие как начальная touch, сенсорное устройство, перемещение между экрана или конечный сенсорного ввода.

-  Набор значений оси, которые описывают позицию `MotionEvent` и другие свойства перемещения, например где сенсорное устройство, предпринимаются, когда выполнялась касание и какое давление использовался.
   Значения оси может отличаться в зависимости от устройства, поэтому приведенного выше списка не описывает все значения оси.


`MotionEvent` Объект будет передаваться для соответствующего метода в приложении. Существует три способа для приложения Xamarin.Android реагировать на событие сенсорного ввода:

-  *Назначьте обработчик событий для `View.Touch`*  - `Android.Views.View` класс имеет `EventHandler<View.TouchEventArgs>` приложения, которые можно назначить обработчик. Такое поведение является типичным .NET.

-  *Реализация `View.IOnTouchListener`*  -экземпляры этого интерфейса может быть назначен объект представления, с помощью представления. `SetOnListener` метод. Это функционально эквивалентно назначении обработчика событий к `View.Touch` событий. Если имеется несколько точек зрения может потребоваться, если они затронут логикой общими, он будет более эффективной, для создания класса и реализует этот метод, чтобы назначить каждое представление собственный обработчик событий.

-  *Переопределить `View.OnTouchEvent`*  -всех представлений в Android подкласс `Android.Views.View`. Когда возникает при просмотре представления, вызывает Android `OnTouchEvent` и передать его `MotionEvent` объект в качестве параметра.


> [!NOTE]
> Не все устройства Android поддерживают сенсорных экранов. 

Добавив следующий тег в файл манифеста приводит к Google Play для отображения только приложения для тех устройств, которые являются поддержка сенсорного ввода:

```xml
<uses-configuration android:reqTouchScreen="finger" />
```

## <a name="gestures"></a>Жесты

Жест является фигурой нарисованные от руки на сенсорный экран. Жест может иметь один или несколько штрихов, каждого штриха, состоящее из последовательности точек, созданных другой точки контакта с экрана. Android может поддерживать множество различных типов жесты, от простой посвященном по экрану на сложные жесты, включающие мультисенсорные.

Android предоставляет `Android.Gestures` пространство имен специально для управления и реагирование на жесты. В сердце все жесты — это специальный класс с именем `Android.Gestures.GestureDetector`. Как и предполагает название, этот класс будет прослушивать жестов и события на основе `MotionEvents` предоставляемые операционной системой.

Чтобы реализовать обнаружения жестов, необходимо создать экземпляр действия `GestureDetector` класса и укажем экземпляр `IOnGestureListener`, как показано в следующем фрагменте кода:

```csharp
GestureOverlayView.IOnGestureListener myListener = new MyGestureListener();
_gestureDetector = new GestureDetector(this, myListener);
```

Действие также необходимо реализовать OnTouchEvent и передать MotionEvent обнаружения жест. В следующем фрагменте кода показан пример этого:

```csharp
public override bool OnTouchEvent(MotionEvent e)
{
    // This method is in an Activity
    return _gestureDetector.OnTouchEvent(e);
}
```

При создании экземпляра класса `GestureDetector` определяет жест интерес, оно уведомит действия или приложения при возникновении события либо посредством обратного вызова, предоставляемые `GestureDetector.IOnGestureListener`.
Этот интерфейс предоставляет шесть методов для различных жестов:

-  *OnDown* — вызывается, когда происходит отвод, но не освобождается.

-  *OnFling* — вызывается, когда посвященном происходит и предоставляет данные об начала и окончания сенсорное устройство, вызвавшей событие.

-  *OnLongPress* — вызывается, когда происходит последовательности при долгом нажатии.

-  *OnScroll* -вызывается при возникновении события прокрутки.

-  *OnShowPress* - вызывается после возникновения OnDown и перемещения или не было выполнено событие.

-  *OnSingleTapUp* — вызывается, когда происходит одним касанием.


Во многих случаях приложения может быть заинтересован только в подмножестве жестов. В этом случае приложения должны расширять класс GestureDetector.SimpleOnGestureListener и переопределить методы, которые соответствуют событиям, которые их интересует.

## <a name="custom-gestures"></a>Пользовательских жестов

Жесты — это отличный способ пользователям взаимодействовать с приложением. API-интерфейсы до сих обнаруженного было бы достаточно для простых жестов, но может потребовать немного обременительным для более сложных жестов. Чтобы помочь в более сложных жесты, Android предоставляет другой набор интерфейсов API в пространстве имен Android.Gestures, которая облегчит некоторые нагрузку, связанную с пользовательских жестов.

### <a name="creating-custom-gestures"></a>Создание пользовательских жестов

С момента Android 1.6 пакет SDK для Android поставляется с предварительно установленным в эмуляторе, вызывается построитель жесты приложения. Это приложение позволяет разработчикам создавать предварительно определенных жестов, которые могут быть внедрены в приложении. На следующем снимке экрана показан пример построителя жестов:

[![Снимок экрана жесты построитель с жестами пример](touch-in-android-images/image11.png)](touch-in-android-images/image11.png#lightbox)

Усовершенствованная версия этого приложения, программа жестов можно найти Google Play. Жест оно очень похож жесты построитель за исключением того, что он позволяет проверить жесты, после их создания. Этом далее снимке экрана показан построитель жестов:

[![Снимок экрана: жест средство, с помощью жестов пример](touch-in-android-images/image12.png)](touch-in-android-images/image12.png#lightbox)

Средство жест более удобна для создания пользовательских жестов, так как оно обеспечивает жесты, проверяемое при их создании и легко доступен через Google Play.

Жест средство позволяет создавать жест путем рисования на экране и назначение имени. После создания жесты они сохраняются в двоичном файле на SD-карту устройства. Этот файл должен быть полученные от устройства, а затем упаковываются вместе с приложением, в папку /Resources/raw. Этот файл можно получить из эмулятора с помощью моста отладки Android. В следующем примере показано копирование файла из Galaxy Nexus каталог ресурсов приложения:

```shell
$ adb pull /storage/sdcard0/gestures <projectdirectory>/Resources/raw
```

После получения файла он должен быть упакованной с приложением внутри directory /Resources/raw. Чтобы использовать этот файл жест проще для загрузки файла в GestureLibrary, как показано в следующем фрагменте:

```csharp
GestureLibary myGestures = GestureLibraries.FromRawResources(this, Resource.Raw.gestures);
if (!myGestures.Load())
{
    // The library didn't load, so close the activity.
    Finish();
}
```

### <a name="using-custom-gestures"></a>С помощью пользовательских жестов

Для распознавания пользовательских жестов в действии, он должен иметь Android.Gesture.GestureOverlay объект, добавленный к его макет. В следующем фрагменте кода показано, как программным способом добавить GestureOverlayView действия:

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

`GestureOverlayView` Имеет несколько событий, которые будут вызываться в процессе рисования жест. Наиболее интересные событие `GesturePerformed`. Это событие возникает после завершения рисования их жест пользователя.

При возникновении этого события, действия запрашивает `GestureLibrary` попробовать и соответствует жест, созданной пользователем с помощью одного из жестов средством жест. `GestureLibrary` Возвращает список объектов прогноза.

Каждый объект прогноза содержит показатель и имя одного из жестов в `GestureLibrary`. Чем выше оценка, тем выше вероятность данный жест, с именем в прогноз соответствует жестов, нарисованного пользователем.
Вообще говоря оценок, ниже, чем 1.0, считаются низкой совпадений.

Следующий код является примером сопоставления жест:

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

Это сделано необходимо понимать, как использовать касания и жестов в приложениях Xamarin.Android. Давайте теперь перейдем к Пошаговое руководство и концепциях в рабочий пример приложения.



## <a name="related-links"></a>Связанные ссылки

- [Android Touch запустить (пример)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_start)
- [Android Touch окончательный (пример)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_final)
