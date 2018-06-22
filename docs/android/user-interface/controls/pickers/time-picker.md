---
title: Выбор времени
description: При выборе другой с помощью TimePickerDialog и DialogFragment
ms.prod: xamarin
ms.assetid: EB4E8206-E8AD-9F04-AC1C-82AC9364A9DD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: c4261e3dccaccc4c88afe9c1033fb16b730fea6e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30769901"
---
# <a name="time-picker"></a>Выбор времени

Предоставляет способ для пользователя, выберите время, можно использовать [TimePicker](https://developer.xamarin.com/api/type/Android.Widget.TimePicker/). Обычно используют приложения Android `TimePicker` с [TimePickerDialog](https://developer.xamarin.com/api/type/Android.App.TimePickerDialog/) для выбора значения времени &ndash; это помогает гарантировать согласованный интерфейс между устройствами и приложениями. `TimePicker` позволяет пользователям выбирать время суток в 24-часового или 12-часовом режиме AM/PM.
`TimePickerDialog` — Это вспомогательный класс, который инкапсулирует `TimePicker` в диалоговом окне.

[![Снимок экрана: пример диалогового окна выбора времени в действии](time-picker-images/01-example-screen-sml.png)](time-picker-images/01-example-screen.png#lightbox)

## <a name="overview"></a>Обзор

Отображение современных приложений для Android `TimePickerDialog` в [DialogFragment](https://developer.xamarin.com/api/type/Android.App.DialogFragment/). Это позволяет приложению отображать `TimePicker` как всплывающее диалоговое окно или внедрить в действие. Кроме того `DialogFragment` управляет жизненным циклом и отображение диалогового окна, уменьшая объем кода, который должен быть реализован.

В этом руководстве демонстрируется использование `TimePickerDialog`, упакованное в `DialogFragment`. Приложение отображает образец `TimePickerDialog` как модальное диалоговое окно при нажатии кнопки в действии. Если время задано пользователем, выхода из диалогового окна и обновляет обработчик `TextView` на экран действия с временем, которая была выбрана.

## <a name="requirements"></a>Требования

Образец приложения в этом руководстве обращается Android 4.1 (уровень API
16) или более поздней версии, но может использоваться с Android 3.0 (уровень API 11 или выше). Существует возможность поддерживают более старые версии Android с добавлением v4 библиотеку поддержки Android для проекта и некоторые изменения в коде.

## <a name="using-the-timepicker"></a>С помощью TimePicker

В этом примере расширяет `DialogFragment`; реализация подкласс `DialogFragment` (называется `TimePickerFragment` ниже) для хранения и отображения `TimePickerDialog`. При запуске примера приложения отображает **ВЫБОРА времени** кнопку выше `TextView` будет использоваться для отображения выбранного времени:

[![Экран исходного образца приложения](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

При нажатии кнопки **ВЫБОРА времени** кнопка запускает приложение пример `TimePickerDialog` как показано на этом снимке экрана:

[![Снимок экрана: диалоговое окно Выбор времени по умолчанию отображается в приложении](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)

В `TimePickerDialog`, выбрав время и щелкнув **ОК** кнопку причины `TimePickerDialog` для вызова метода [IOnTimeSetListener.OnTimeSet](https://developer.xamarin.com/api/member/Android.App.TimePickerDialog+IOnTimeSetListener.OnTimeSet/p/Android.Widget.TimePicker/System.Int32/System.Int32/System.Int32/).
Этот интерфейс реализуется путем размещения `DialogFragment` (`TimePickerFragment`, описаны ниже). Щелкнув **отменить** кнопка вызывает фрагмент и диалоговое окно, чтобы быть закрыт.

`DialogFragment` Возвращает выбранное время размещения Actvity одним из трех способов:

1. **Вызов метода или задании свойства** &ndash; действия можно указать свойство или метод специально для данного параметра.

2. **При возникновении события** &ndash; `DialogFragment` можно определить событие, которое будет возникает, когда `OnTimeSet` вызывается.

3. **С помощью `Action`**  &ndash; `DialogFragment` можно вызвать `Action<DateTime>` для отображения времени в действии. Действие будет предоставлять `Action<DateTime` при создании экземпляра `DialogFragment`. 

Этот образец будет использовать третий способ, который требует действия питания `Action<DateTime>` обработчик `DialogFragment`.



## <a name="start-an-app-project"></a>Запуск проекта приложения

Начать новый проект Android с именем **TimePickerDemo** (Если вы не знакомы с созданием проектам Xamarin.Android, см. раздел [Привет, Android](~/android/get-started/hello-android/hello-android-quickstart.md) описывается создание нового проекта).

Изменить **Resources/layout/Main.axml** и замените его содержимое следующим кодом XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:layout_gravity="center_horizontal"
    android:padding="16dp">
    <Button
        android:id="@+id/select_button"
        android:paddingLeft="24dp"
        android:paddingRight="24dp"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="PICK TIME"
        android:textSize="20dp" />
    <TextView
        android:id="@+id/time_display"
        android:layout_height="wrap_content"
        android:layout_width="match_parent"
        android:paddingTop="22dp"
        android:text="Picked time will be displayed here"
        android:textSize="24dp" />
</LinearLayout>
```

Это базовый [LinearLayout](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) с [TextView](https://developer.xamarin.com/api/type/Android.Widget.TextView/) , время и [кнопку](https://developer.xamarin.com/api/type/Android.Widget.Button/) , которая будет открывать `TimePickerDialog`. Обратите внимание, что этот макет использует жестко запрограммированные строки и измерения чтобы это приложение стало проще и легче понять &ndash; рабочее приложение обычно использует ресурсы для этих значений (как можно увидеть в [DatePicker](https://github.com/xamarin/recipes/blob/master/android/controls/datepicker/select_a_date/Resources/layout/Main.axml) пример кода).

Изменить **MainActivity.cs** и замените его содержимое следующим кодом:

```csharp
using Android.App;
using Android.Widget;
using Android.OS;
using System;
using Android.Util;
using Android.Text.Format;

namespace TimePickerDemo
{
    [Activity(Label = "TimePickerDemo", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        TextView timeDisplay;
        Button timeSelectButton;

        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            SetContentView(Resource.Layout.Main);
            timeDisplay = FindViewById<TextView>(Resource.Id.time_display);
            timeSelectButton = FindViewById<Button>(Resource.Id.select_button);
        }
    }
}
```

При построении и запуска этого примера, вы увидите начальный экран, аналогично на следующем снимке экрана:

[![Начальный экран приложения](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

Щелкнув **ВЫБОРА времени** кнопка не выполняет никаких действий из-за `DialogFragment` еще не был реализован для отображения `TimePicker`.
Следующим шагом является создание этого `DialogFragment`.



## <a name="extending-dialogfragment"></a>Расширение DialogFragment

Чтобы расширить `DialogFragment` для использования с `TimePicker`, необходимо создать подкласс, который является производным от `DialogFragment` и реализует `TimePickerDialog.IOnTimeSetListener`. Добавьте следующий класс в **MainActivity.cs**:

```csharp
public class TimePickerFragment : DialogFragment, TimePickerDialog.IOnTimeSetListener
{
    public static readonly string TAG = "MyTimePickerFragment";
    Action<DateTime> timeSelectedHandler = delegate { };

    public static TimePickerFragment NewInstance(Action<DateTime> onTimeSelected)
    {
        TimePickerFragment frag = new TimePickerFragment();
        frag.timeSelectedHandler = onTimeSelected;
        return frag;
    }

    public override Dialog OnCreateDialog (Bundle savedInstanceState)
    {
        DateTime currentTime = DateTime.Now;
        bool is24HourFormat = DateFormat.Is24HourFormat(Activity);
        TimePickerDialog dialog = new TimePickerDialog
            (Activity, this, currentTime.Hour, currentTime.Minute, is24HourFormat);
        return dialog;
    }

    public void OnTimeSet(TimePicker view, int hourOfDay, int minute)
    {
        DateTime currentTime = DateTime.Now;
        DateTime selectedTime = new DateTime(currentTime.Year, currentTime.Month, currentTime.Day, hourOfDay, minute, 0);
        Log.Debug(TAG, selectedTime.ToLongTimeString());
        timeSelectedHandler (selectedTime);
    }
}
```

Это `TimePickerFragment` класс разбить на более мелкие части и описано в следующем разделе.


### <a name="dialogfragment-implementation"></a>Реализация DialogFragment

`TimePickerFragment` реализует несколько методов: метод фабрики, диалоговое окно при создании экземпляра метода и `OnTimeSet` метод обработчика, предусмотренного `TimePickerDialog.IOnTimeSetListener`.

-   `TimePickerFragment` является подклассом `DialogFragment`. Он также реализует `TimePickerDialog.IOnTimeSetListener` интерфейса (то есть, он передает необходимая `OnTimeSet` метод):

    ```csharp
    public class TimePickerFragment : DialogFragment, TimePickerDialog.IOnTimeSetListener
    ```

-   `TAG` инициализируется для ведения журнала (*MyTimePickerFragment* можно изменить для любых строк, вы хотите использовать). `timeSelectedHandler` Запустить действие для пустой делегата для предотвращения исключений пустая ссылка:

    ```csharp
    public static readonly string TAG = "MyTimePickerFragment";
    Action<DateTime> timeSelectedHandler = delegate { };
    ```

-   `NewInstance` Фабричный метод вызывается для создания нового экземпляра `TimePickerFragment`. Этот метод принимает `Action<DateTime>` обработчик, который вызывается, когда пользователь нажимает кнопку **ОК** кнопку в `TimePickerDialog`:

    ```csharp
    public static TimePickerFragment NewInstance(Action<DateTime> onTimeSelected)
    {
        TimePickerFragment frag = new TimePickerFragment();
        frag.timeSelectedHandler = onTimeSelected;
        return frag;
    }
    ```

-   После отображения фрагмента вызывает Android `DialogFragment` метод [OnCreateDialog](https://developer.xamarin.com/api/member/Android.App.DialogFragment.OnCreateDialog/p/Android.OS.Bundle/). 
    Этот метод создает новый `TimePickerDialog` объекта и инициализирует его с действием, объект обратного вызова (который является текущим экземпляром `TimePickerFragment`) и текущее время:

    ```csharp
    public override Dialog OnCreateDialog (Bundle savedInstanceState)
    {
        DateTime currentTime = DateTime.Now;
        bool is24HourFormat = DateFormat.Is24HourFormat(Activity);
        TimePickerDialog dialog = new TimePickerDialog
            (Activity, this, currentTime.Hour, currentTime.Minute, is24HourFormat);
        return dialog;
    }
    ```

-   Когда пользователь изменяет параметр времени в `TimePicker` диалоговом `OnTimeSet` вызывается метод. `OnTimeSet` Создает `DateTime` с использованием текущей даты и слияний времени (часы и минуты), выбранных пользователем:

    ```csharp
    public void OnTimeSet(TimePicker view, int hourOfDay, int minute)
    {
        DateTime currentTime = DateTime.Now;
        DateTime selectedTime = new DateTime(currentTime.Year, currentTime.Month, currentTime.Day, hourOfDay, minute, 0);
    ```


-   Это `DateTime` объект передается `timeSelectedHandler` , зарегистрированный на `TimePickerFragment` объекта во время создания. `OnTimeSet` вызывается этот обработчик для обновления отображения времени действия в выбранный момент времени (этот обработчик реализована в следующем разделе):

    ```csharp
    timeSelectedHandler (selectedTime);
    ```



## <a name="displaying-the-timepickerfragment"></a>Отображение TimePickerFragment

Теперь, когда `DialogFragment` был реализован, пришло время для создания экземпляра `DialogFragment` с помощью `NewInstance` фабричный метод и отобразить ее, вызвав [DialogFragment.Show](https://developer.xamarin.com/api/member/Android.App.DialogFragment.Show/p/Android.App.FragmentManager/System.String/):

Добавьте следующий метод `MainActivity`:

```csharp
void TimeSelectOnClick (object sender, EventArgs eventArgs)
{
    TimePickerFragment frag = TimePickerFragment.NewInstance (
        delegate (DateTime time)
        {
            timeDisplay.Text = time.ToShortTimeString();
        });

    frag.Show(FragmentManager, TimePickerFragment.TAG);
}
```

После `TimeSelectOnClick` создает `TimePickerFragment`, создается и передается в делегат для анонимного метода, обновляет отображения времени действия время переданное значение. Наконец, он запускает `TimePicker` диалоговое окно фрагмента (через `DialogFragment.Show`) для отображения `TimePicker` для пользователя.

В конце `OnCreate` метод, добавьте следующую строку, чтобы присоединить обработчик событий к **ВЫБОРА времени** кнопки, которая открывает диалоговое окно:

```csharp
timeSelectButton.Click += TimeSelectOnClick;
```

Когда **ВЫБОРА времени** по нажатию кнопки `TimeSelectOnClick` будет вызываться для отображения `TimePicker` диалоговое окно фрагмента для пользователя.



## <a name="try-it"></a>Попробуйте!

Выполните сборку и запуск приложения. При нажатии кнопки **ВЫБОРА времени** кнопки `TimePickerDialog` отображается в формате времени по умолчанию для действия (в этом случае — 12-часовом AM/PM режиме):

[![Отображается диалоговое окно времени, в режиме AM/PM](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)
   
При нажатии кнопки **ОК** в `TimePicker` диалоговое окно, обработчик обновляет действия `TextView` с выбранный момент времени, а затем завершает работу:

[![A/M время отображается в TextView действия](time-picker-images/04-after-time-dialog-sml.png)](time-picker-images/04-after-time-dialog.png#lightbox)

Добавьте следующую строку кода, чтобы `OnCreateDialog` сразу же после `is24HourFormat` объявлена и инициализирована:

```csharp
is24HourFormat = true;
```

Это изменение принудительно флаг, переданный `TimePickerDialog` конструктор, чтобы быть `true` , 24-часовом режим используется вместо формата времени размещения действия. При построении и запуске приложения, нажмите кнопку **ВЫБОРА времени** кнопки `TimePicker` диалоговое окно теперь отображается в 24-часовом формате:

[![Диалоговое окно TimePicker в 24-часовом формате](time-picker-images/05-24hr-time-dialog-sml.png)](time-picker-images/05-24hr-time-dialog.png#lightbox)

Поскольку обработчик вызывает [DateTime.ToShortTimeString](https://msdn.microsoft.com/en-us/library/system.datetime.toshortdatestring%28v=vs.110%29.aspx) печать времени к действию `TextView`, время по-прежнему выводится в формате по умолчанию 12-часовой AM/PM.



## <a name="summary"></a>Сводка

В этой статье описано, как отобразить `TimePicker` мини-приложение как модальное диалоговое окно всплывающее окно с Android действия. Он предоставляется образец `DialogFragment` реализацию и обсуждаются `IOnTimeSetListener` интерфейса. В этом примере также демонстрируется использование `DialogFragment` могут взаимодействовать с узлом, чтобы отобразить выбранный период времени.


## <a name="related-links"></a>Связанные ссылки

- [DialogFragment](https://developer.xamarin.com/api/type/Android.App.DialogFragment/)
- [TimePicker](https://developer.xamarin.com/api/type/Android.Widget.TimePicker/)
- [TimePickerDialog](https://developer.xamarin.com/api/type/Android.App.TimePickerDialog/)
- [TimePickerDialog.IOnTimeSetListener](https://developer.xamarin.com/api/type/Android.App.TimePickerDialog+IOnTimeSetListener/)
- [TimePickerDemo (пример)](https://developer.xamarin.com/samples/monodroid/UserInterface/TimePickerDemo)
