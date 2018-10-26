---
title: Пошаговое руководство. Сохранение состояния действия
description: Мы рассмотрели теория сохранения состояния в структуре жизненный цикл действия; Теперь давайте подробно рассмотрим пример.
ms.prod: xamarin
ms.assetid: A6090101-67C6-4BDD-9416-F2FB74805A87
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: c8f92e55648dff469227cc3bad981ad5f6e6d0ac
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122131"
---
# <a name="walkthrough---saving-the-activity-state"></a>Пошаговое руководство. Сохранение состояния действия

_Мы рассмотрели теория сохранения состояния в структуре жизненный цикл действия; Теперь давайте подробно рассмотрим пример._

## <a name="activity-state-walkthrough"></a>Пошаговое руководство состояния действия

Откроем **ActivityLifecycle_Start** проекта (в [ActivityLifecycle](https://developer.xamarin.com/samples/monodroid/ActivityLifecycle) пример), выполнить его сборку и запустите его. Это очень простой проект, который имеет два действия, чтобы продемонстрировать жизненный цикл действия и способ вызова методов жизненного цикла. При запуске приложения, на экране `MainActivity` отображается: 

[![Действие А экрана](saving-state-images/01-activity-a-sml.png)](saving-state-images/01-activity-a.png#lightbox)

### <a name="viewing-state-transitions"></a>Переходы состояния просмотра

Каждый метод в этом образце записывает в окно вывода интегрированной среды разработки приложения для указания состояния действия. (Чтобы открыть окно вывода в Visual Studio, введите **CTRL-ALT-O**; чтобы открыть окно вывода в Visual Studio для Mac, щелкните **Вид > панели > выходные данные приложения**.)

При первом запуске приложения, в окне вывода отображаются изменения состояния *действие А*: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

При нажатии **запустить действие Б** кнопку, мы видим *действие А* Приостановка и остановка при *действие Б* проходит через изменения своего состояния: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.SecondActivity] Activity B - OnCreate
[ActivityLifecycle.SecondActivity] Activity B - OnStart
[ActivityLifecycle.SecondActivity] Activity B - OnResume
[ActivityLifecycle.MainActivity] Activity A - OnStop
```

В результате *действие Б* запущена с отображаемый вместо объекта *действие А*: 

[![Экран действия Б](saving-state-images/02-activity-b-sml.png)](saving-state-images/02-activity-b.png#lightbox)

При нажатии **обратно** кнопки *действие Б* уничтожается и *действие А* возобновляется: 

```shell
[ActivityLifecycle.SecondActivity] Activity B - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnRestart
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
[ActivityLifecycle.SecondActivity] Activity B - OnStop
[ActivityLifecycle.SecondActivity] Activity B - OnDestroy
```
### <a name="adding-a-click-counter"></a>Добавление счетчика щелкните

Далее мы собираемся изменить приложение таким образом, чтобы у нас есть кнопка, которая подсчитывает количество раз, когда она нажата и. Во-первых давайте добавим `_counter` переменная экземпляра для `MainActivity`:

```csharp
int _counter = 0;
```

Затем изменим **Resource/layout/Main.axml** макета и добавьте новый `clickButton` , отображает, сколько раз пользователь нажал кнопку. Полученный в результате **Main.axml** должен выглядеть следующим образом: 

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

Давайте добавим следующий код в конец [OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) метод в `MainActivity` &ndash; этот код обрабатывает события из щелчка мыши `clickButton`:

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

Когда мы создаем и снова запустите приложение, новая кнопка отобразится, увеличивается и отображает значение `_counter` на каждом щелчке мыши:

[![Добавить счетчик сенсорного ввода](saving-state-images/03-touched-sml.png)](saving-state-images/03-touched.png#lightbox)

Но если мы повороте устройства в режим альбомной ориентации, это число будет потерян:

[![Поворот на альбомную задает число к нулю](saving-state-images/05-rotate-nosave-sml.png)](saving-state-images/05-rotate-nosave.png#lightbox)

Изучение выходных данных приложения, мы видим, что *действие А* была приостановлена, остановлена, уничтожается, заново, перезапустить, то выполнение возобновляется во время вращения от книжной к альбомной ориентации: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnStop
[ActivityLifecycle.MainActivity] Activity A - On Destroy

[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

Так как *действие А* удаляется и повторно снова при повороте устройства, состояние своего экземпляра теряется. Далее мы добавим код, чтобы сохранять и восстанавливать состояние экземпляра.

### <a name="adding-code-to-preserve-instance-state"></a>Добавление кода для сохранения состояния экземпляра

Давайте добавим метод `MainActivity` для сохранения состояния экземпляра. Прежде чем *действие А* является уничтожения Android автоматически вызывает [OnSaveInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnSaveInstanceState/p/Android.OS.Bundle/) и передает в [пакета](https://developer.xamarin.com/api/type/Android.OS.Bundle/) , можно использовать для хранения наших состояние экземпляра. Давайте используется для сохранения в виде целочисленного значения наших число нажатий:

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
    outState.PutInt ("click_count", _counter);
    Log.Debug(GetType().FullName, "Activity A - Saving instance state");

    // always call the base implementation!
    base.OnSaveInstanceState (outState);    
}
```

Когда *действие А* заново и возобновлении Android передает это `Bundle` обратно в наших `OnCreate` метод. Давайте добавим код, чтобы `OnCreate` восстановить `_counter` значение из переданного `Bundle`. Добавьте следующий код непосредственно перед строкой, где `clickbutton` определяется: 

```csharp
if (bundle != null)
{
    _counter = bundle.GetInt ("click_count", 0);
    Log.Debug(GetType().FullName, "Activity A - Recovered instance state");
}
```

Построение и снова запустите приложение, а затем нажмите вторую кнопку несколько раз. Когда мы повороте устройства в режим альбомной ориентации, сохраняется счетчик!

[![Поворот экрана показывает количество из четырех сохранены](saving-state-images/06-rotate-save-sml.png)](saving-state-images/06-rotate-save.png#lightbox)


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

Прежде чем [OnStop](https://developer.xamarin.com/api/member/Android.App.Activity.OnStop/) был вызван метод, нашего нового `OnSaveInstanceState` был вызван метод, чтобы сохранить `_counter` значение в `Bundle`. Android передать это `Bundle` к нам при его вызове наших `OnCreate` метод и мы смогли использовать ее для восстановления `_counter` значение там, где мы остановились.


## <a name="summary"></a>Сводка

В этом пошаговом руководстве мы использовали знание жизненный цикл действия для сохранения данных состояния. 



## <a name="related-links"></a>Связанные ссылки

- [ActivityLifecycle (пример)](https://developer.xamarin.com/samples/monodroid/ActivityLifecycle)
- [Жизненный цикл действия](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Действие Android](https://developer.xamarin.com/api/type/Android.App.Activity/)
