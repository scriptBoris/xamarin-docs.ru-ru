---
title: Пошаговое руководство. Сохранение состояния действия
description: Мы рассмотрели теория сохранение состояния в руководстве жизненный цикл действия; Теперь давайте рассмотрим пример.
ms.prod: xamarin
ms.assetid: A6090101-67C6-4BDD-9416-F2FB74805A87
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: e282eeb8732bd5294da4ec4e3fe337e81107c8f3
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30767431"
---
# <a name="walkthrough---saving-the-activity-state"></a>Пошаговое руководство. Сохранение состояния действия

_Мы рассмотрели теория сохранение состояния в руководстве жизненный цикл действия; Теперь давайте рассмотрим пример._

## <a name="activity-state-walkthrough"></a>Пошаговое руководство состояния действия

Откроем **ActivityLifecycle_Start** проекта (в [ActivityLifecycle](https://developer.xamarin.com/samples/monodroid/ActivityLifecycle) образца), построить и запустить его. Это очень простой проект, который имеет два действия, чтобы продемонстрировать жизненный цикл действия и вызовом методов жизненного цикла. При запуске приложения на экране `MainActivity` отображается: 

[![Действие А экрана](saving-state-images/01-activity-a-sml.png)](saving-state-images/01-activity-a.png#lightbox)

### <a name="viewing-state-transitions"></a>Переходы состояния просмотра

Каждый метод в этом образце записывает окна вывода интегрированной среды разработки приложения для обозначения состояния действия. (Чтобы открыть окно вывода в Visual Studio, введите **CTRL-ALT-O**; чтобы открыть окно вывода в Visual Studio для Mac, щелкните **представление > дополняет > выходных данных приложения**.)

При первом запуске приложения в окне вывода отображаются изменения состояния *действие А*: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

Если щелкнуть **запустить действие Б** кнопку, мы увидим *действие А* Пауза и остановка при *действие Б* проходит через изменения в своем состоянии: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.SecondActivity] Activity B - OnCreate
[ActivityLifecycle.SecondActivity] Activity B - OnStart
[ActivityLifecycle.SecondActivity] Activity B - OnResume
[ActivityLifecycle.MainActivity] Activity A - OnStop
```

В результате *действие Б* будет запущен и отображен вместо *действие А*: 

[![Экран действия B](saving-state-images/02-activity-b-sml.png)](saving-state-images/02-activity-b.png#lightbox)

Если щелкнуть **обратно** кнопки, *действие Б* разрушается и *действие А* возобновляется: 

```shell
[ActivityLifecycle.SecondActivity] Activity B - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnRestart
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
[ActivityLifecycle.SecondActivity] Activity B - OnStop
[ActivityLifecycle.SecondActivity] Activity B - OnDestroy
```
### <a name="adding-a-click-counter"></a>Добавление счетчика щелкните

Далее мы собираемся изменить приложение, чтобы у нас есть кнопки, которая подсчитывает количество щелчков по его и. Во-первых давайте добавим `_counter` переменная экземпляра для `MainActivity`:

```csharp
int _counter = 0;
```

Теперь изменим **Resource/layout/Main.axml** макета и добавьте новый `clickButton` , отображает, сколько раз пользователь нажал на кнопку. Итоговый **Main.axml** должен выглядеть следующим образом: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <Button
        android:id="@+id/myButton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/mybutton_text" />
    <Button
        android:id="@+id/clickButton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/counterbutton_text" />
</LinearLayout>
```

Давайте добавьте следующий код в конец [OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) метод в `MainActivity` &ndash; этот код обрабатывает события из щелчка мыши `clickButton`:

```csharp
var clickbutton = FindViewById<Button> (Resource.Id.clickButton);
clickbutton.Text = Resources.GetString (
    Resource.String.counterbutton_text, _counter);
clickbutton.Click += (object sender, System.EventArgs e) =>
{
    _counter++;
    clickbutton.Text = Resources.GetString (
        Resource.String.counterbutton_text, _counter);
} ;
```

Когда мы создаем и снова запустить приложение, новая кнопка появляется, увеличивает и отображает значение `_counter` при каждом щелчке:

[![Добавить счетчик сенсорного ввода](saving-state-images/03-touched-sml.png)](saving-state-images/03-touched.png#lightbox)

Но если мы повернуть устройства в альбомном режиме, этот счетчик, будут потеряны.

[![Поворот Альбомная задает счетчик до нуля](saving-state-images/05-rotate-nosave-sml.png)](saving-state-images/05-rotate-nosave.png#lightbox)

Анализируя выходные данные приложения, мы увидим, что *действие А* был приостановлен, остановлен, уничтожается, заново, перезапустить, а затем выполнение возобновляется во время поворот с книжной и альбомной ориентации: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnStop
[ActivityLifecycle.MainActivity] Activity A - On Destroy

[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

Поскольку *действие А* удаляется и повторно снова при повороте устройства, состояние своего экземпляра будут потеряны. Далее мы добавим код для сохранения и восстановления данных о состоянии экземпляра.

### <a name="adding-code-to-preserve-instance-state"></a>Добавление кода для сохранения состояния экземпляра

Давайте добавим метод `MainActivity` для сохранения состояния экземпляра. Прежде чем *действие А* является уничтожения Android автоматически вызывает [OnSaveInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnSaveInstanceState/p/Android.OS.Bundle/) и передает в [пакета](https://developer.xamarin.com/api/type/Android.OS.Bundle/) , можно использовать для хранения состояния наш экземпляр. Давайте используется для сохранения в виде целочисленного значения нашей число нажатий:

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
    outState.PutInt ("click_count", _counter);
    Log.Debug(GetType().FullName, "Activity A - Saving instance state");

    // always call the base implementation!
    base.OnSaveInstanceState (outState);    
}
```

При *действие А* будет повторно создан и возобновлении Android передает это `Bundle` обратно в нашем `OnCreate` метод. Давайте добавим код, чтобы `OnCreate` восстановление `_counter` значение из переданного `Bundle`. Добавьте следующий код перед строкой где `clickbutton` определяется: 

```csharp
if (bundle != null)
{
    _counter = bundle.GetInt ("click_count", 0);
    Log.Debug(GetType().FullName, "Activity A - Recovered instance state");
}
```

Построить и запустить приложение еще раз, а затем нажмите вторую кнопку несколько раз. При мы повернуть устройства в альбомной ориентации, счетчик сохраняется!

[![Поворот экрана показывает количество из четырех сохраняются](saving-state-images/06-rotate-save-sml.png)](saving-state-images/06-rotate-save.png#lightbox)


Давайте рассмотрим окно вывода, чтобы увидеть, что произошло:
    
```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.MainActivity] Activity A - Saving instance state
[ActivityLifecycle.MainActivity] Activity A - OnStop
[ActivityLifecycle.MainActivity] Activity A - On Destroy

[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - Recovered instance state
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
``` 

Прежде чем [OnStop](https://developer.xamarin.com/api/member/Android.App.Activity.OnStop/) метод был вызван, наш новый `OnSaveInstanceState` метод был вызван для сохранения `_counter` значение в `Bundle`. Android передан это `Bundle` к нам при его вызове нашей `OnCreate` метода и мы смогли позволяет восстановить `_counter` значение, где было остановлено.


## <a name="summary"></a>Сводка

В этом walkthough мы использовали имеющиеся знания о жизненном цикле действие для сохранения данных о состоянии. 



## <a name="related-links"></a>Связанные ссылки

- [ActivityLifecycle (пример)](https://developer.xamarin.com/samples/monodroid/ActivityLifecycle)
- [Жизненный цикл действия](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Действие Android](https://developer.xamarin.com/api/type/Android.App.Activity/)
