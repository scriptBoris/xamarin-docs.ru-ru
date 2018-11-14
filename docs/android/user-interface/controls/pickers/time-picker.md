---
title: Выбор времени
description: Выбрав другой с помощью TimePickerDialog и DialogFragment
ms.prod: xamarin
ms.assetid: EB4E8206-E8AD-9F04-AC1C-82AC9364A9DD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 08fa9000a1fd9c97f7881a4a13c15fabfa6dda47
ms.sourcegitcommit: 6be6374664cd96a7d924c2e0c37aeec4adf8be13
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617674"
---
# <a name="time-picker"></a>Выбор времени

Предоставляет способ для пользователя для выбора времени, можно использовать [TimePicker](https://developer.xamarin.com/api/type/Android.Widget.TimePicker/). Android приложения обычно используют `TimePicker` с [TimePickerDialog](https://developer.xamarin.com/api/type/Android.App.TimePickerDialog/) для выбора значения времени &ndash; это позволяет обеспечить согласованный интерфейс для устройств и приложений. `TimePicker` позволяет пользователям выбирать время суток в 24-часового или 12-часовом режиме AM/PM.
`TimePickerDialog` — Это вспомогательный класс, который инкапсулирует `TimePicker` в диалоговом окне.

[![Пример снимка экрана диалоговое окно выбора времени, в действии](time-picker-images/01-example-screen-sml.png)](time-picker-images/01-example-screen.png#lightbox)

## <a name="overview"></a>Обзор

Отображение современных приложений Android `TimePickerDialog` в [DialogFragment](https://developer.xamarin.com/api/type/Android.App.DialogFragment/). Это позволяет приложению отображать `TimePicker` как всплывающее диалоговое окно или внедрить в действие. Кроме того `DialogFragment` управляет жизненным циклом и отображение диалогового окна, уменьшая объем кода, который должен быть реализован.

В этом руководстве демонстрируется использование `TimePickerDialog`заключен в `DialogFragment`. Приложение отображает образец `TimePickerDialog` как модальное диалоговое окно, когда пользователь нажимает кнопку в действии. Если пользователь задал на время, выхода из диалогового окна, и обработчик обновляет `TextView` на экране действия с временем, которая была выбрана.

## <a name="requirements"></a>Требования

В этом руководстве пример приложения предназначен для Android 4.1 (уровень API
16) или более поздней версии, но может использоваться с Android 3.0 (уровень API 11 или более поздней версии). Это можно реализовать поддержку более старых версиях Android с добавлением v4 библиотеки Android поддерживает для проекта и некоторые изменения в коде.

## <a name="using-the-timepicker"></a>С помощью элемента управления TimePicker

Этот пример расширяет `DialogFragment`; реализация подкласс `DialogFragment` (вызывается `TimePickerFragment` ниже) для хранения и отображения `TimePickerDialog`. При первом запуске примера приложения, он отображает **ВЫБОРА времени** кнопку выше `TextView` , будет использоваться для отображения установленный период времени:

[![Экран исходного образца приложения](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

При нажатии кнопки **ВЫБОРА времени** кнопку, будет запущено приложение пример `TimePickerDialog` как показано на следующем снимке экрана:

[![Снимок экрана диалогового окна по умолчанию средство выбора времени, отображаемого в приложении](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)

В `TimePickerDialog`, выбрав раз и щелкнув **ОК** кнопку причины `TimePickerDialog` для вызова метода [IOnTimeSetListener.OnTimeSet](https://developer.xamarin.com/api/member/Android.App.TimePickerDialog+IOnTimeSetListener.OnTimeSet/p/Android.Widget.TimePicker/System.Int32/System.Int32/System.Int32/).
Этот интерфейс реализуется путем размещения `DialogFragment` (`TimePickerFragment`, описанных ниже). Щелкнув **отменить** кнопка вызывает фрагмент и диалоговое окно можно закрыть.

`DialogFragment` Возвращает установленный период времени для размещения Actvity в одном из трех способов:

1. **Вызов метода или задании свойства** &ndash; действия можно указать свойства или метода специально для данного параметра.

2. **При возникновении события** &ndash; `DialogFragment` можно определить событие, которое будет возникает, когда `OnTimeSet` вызывается.

3. **С помощью `Action`**  &ndash; `DialogFragment` можно вызвать `Action<DateTime>` для отображения времени в действии. Действие будет предоставлять `Action<DateTime` при создании экземпляра `DialogFragment`. 

В этом примере используются третий способ, который требует действия питания `Action<DateTime>` обработчик `DialogFragment`.



## <a name="start-an-app-project"></a>Запуск проекта приложения

Начать новый проект Android с именем **TimePickerDemo** (Если вы не знакомы с созданием проектов Xamarin.Android, см. в разделе [Привет, Android](~/android/get-started/hello-android/hello-android-quickstart.md) чтобы узнать, как создать новый проект).

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

Это представляет собой простую [LinearLayout](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) с [TextView](https://developer.xamarin.com/api/type/Android.Widget.TextView/) , время и [кнопку](https://developer.xamarin.com/api/type/Android.Widget.Button/) откроется окно `TimePickerDialog`. Обратите внимание, что данной компоновки используется жестко запрограммированные строки и измерений, чтобы это приложение стало проще и легче понять &ndash; рабочее приложение обычно использует ресурсы для этих значений (как можно увидеть в [DatePicker](https://github.com/xamarin/recipes/blob/master/Recipes/android/controls/datepicker/select_a_date/Resources/layout/Main.axml) пример кода).

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

При построении и запустить этот пример, вы увидите начальный экран, показанный на следующем снимке экрана:

[![Начальный экран приложения](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

Щелкнув **ВЫБОРА времени** кнопки ничего не делает так как `DialogFragment` еще не реализовано для отображения `TimePicker`.
Следующим шагом является создание это `DialogFragment`.



## <a name="extending-dialogfragment"></a>Расширение DialogFragment

Чтобы расширить `DialogFragment` для использования с `TimePicker`, необходимо создать подкласс, производный от `DialogFragment` и реализует `TimePickerDialog.IOnTimeSetListener`. Добавьте следующий класс **MainActivity.cs**:

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

`TimePickerFragment` реализует несколько методов: фабричный метод, метод создания экземпляра диалогового окна и `OnTimeSet` обработчик метода, требуемого сервером `TimePickerDialog.IOnTimeSetListener`.

-   `TimePickerFragment` является подклассом `DialogFragment`. Он также реализует `TimePickerDialog.IOnTimeSetListener` интерфейса (то есть он передает необходимые `OnTimeSet` метод):

    ```csharp
    public class TimePickerFragment : DialogFragment, TimePickerDialog.IOnTimeSetListener
    ```

-   `TAG` инициализируется для ведения (*MyTimePickerFragment* можно изменить для любой строки, вы хотите использовать). `timeSelectedHandler` Действие инициализируется пустым делегат, для предотвращения возникновения исключений пустая ссылка:

    ```csharp
    public static readonly string TAG = "MyTimePickerFragment";
    Action<DateTime> timeSelectedHandler = delegate { };
    ```

-   `NewInstance` Вызывается метод фабрики для создания нового экземпляра `TimePickerFragment`. Этот метод принимает `Action<DateTime>` обработчик, вызываемый, когда пользователь щелкает **ОК** кнопку `TimePickerDialog`:

    ```csharp
    public static TimePickerFragment NewInstance(Action<DateTime> onTimeSelected)
    {
        TimePickerFragment frag = new TimePickerFragment();
        frag.timeSelectedHandler = onTimeSelected;
        return frag;
    }
    ```

-   Когда фрагмент для отображения, вызывает Android `DialogFragment` метод [OnCreateDialog](https://developer.xamarin.com/api/member/Android.App.DialogFragment.OnCreateDialog/p/Android.OS.Bundle/). 
    Этот метод создает новую `TimePickerDialog` объекта и инициализирует его с действием, объект обратного вызова (который является экземпляром класса `TimePickerFragment`) и текущее время:

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

-   Когда пользователь изменяет параметр времени в `TimePicker` диалоговом окне `OnTimeSet` вызывается метод. `OnTimeSet` Создает `DateTime` объекта, используя текущую дату и слияний в время (часы и минуты), выбранный пользователем:

    ```csharp
    public void OnTimeSet(TimePicker view, int hourOfDay, int minute)
    {
        DateTime currentTime = DateTime.Now;
        DateTime selectedTime = new DateTime(currentTime.Year, currentTime.Month, currentTime.Day, hourOfDay, minute, 0);
    ```


-   Это `DateTime` объект передается `timeSelectedHandler` , зарегистрированного с `TimePickerFragment` объекта во время создания. `OnTimeSet` вызывает этот обработчик для обновления отображения времени действия в установленный период времени (этот обработчик реализуется в следующем разделе):

    ```csharp
    timeSelectedHandler (selectedTime);
    ```



## <a name="displaying-the-timepickerfragment"></a>Отображение TimePickerFragment

Теперь, когда `DialogFragment` был реализован, пришло время для создания экземпляра `DialogFragment` с помощью `NewInstance` фабричный метод и отобразить ее, вызвав [DialogFragment.Show](https://developer.xamarin.com/api/member/Android.App.DialogFragment.Show/p/Android.App.FragmentManager/System.String/):

Добавьте следующий метод в `MainActivity`:

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

После `TimeSelectOnClick` создает `TimePickerFragment`, он создает и передает делегат для анонимного метода, который обновляет Отображение времени действия со значением времени в переданном. Наконец, она запускает `TimePicker` фрагмент диалоговое окно (с помощью `DialogFragment.Show`) для отображения `TimePicker` для пользователя.

В конце `OnCreate` метод, добавьте следующую строку для присоединения обработчика событий **ВЫБОРА времени** кнопка, которая открывает диалоговое окно:

```csharp
timeSelectButton.Click += TimeSelectOnClick;
```

Когда **ВЫБОРА времени** кнопки, `TimeSelectOnClick` будет вызываться для отображения `TimePicker` фрагмент диалоговое окно для пользователя.



## <a name="try-it"></a>Попробуйте!

Выполните сборку и запуск приложения. При нажатии кнопки **ВЫБОРА времени** кнопки `TimePickerDialog` отображается в формате времени, по умолчанию для действия (в этом случае — 12-часовом AM/PM режиме):

[![Диалоговое окно отображается в режиме AM/PM](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)
   
При нажатии кнопки **ОК** в `TimePicker` диалоговое окно, обработчик обновляет действия `TextView` с выбранный момент времени, а затем завершает работу:

[![Объект в минуту время отображается в TextView действия](time-picker-images/04-after-time-dialog-sml.png)](time-picker-images/04-after-time-dialog.png#lightbox)

Добавьте следующую строку кода, чтобы `OnCreateDialog` сразу же после `is24HourFormat` объявляется и инициализируется:

```csharp
is24HourFormat = true;
```

Это изменение заставляет флаг, переданный `TimePickerDialog` конструктор, чтобы быть `true` так режим, 24-часовом используется вместо формата времени размещения действия. При построении и снова запустите приложение, нажмите кнопку **ВЫБОРА времени** кнопки `TimePicker` диалоговое окно теперь отображается в 24-часовом формате:

[![Диалоговое окно TimePicker в 24-часовом формате](time-picker-images/05-24hr-time-dialog-sml.png)](time-picker-images/05-24hr-time-dialog.png#lightbox)

Так как обработчик вызывает [DateTime.ToShortTimeString](xref:System.DateTime.ToShortDateString*) печать время действия `TextView`, время по-прежнему выводится в стандартном 12-часовом формате AM/PM.



## <a name="summary"></a>Сводка

В этой статье описано, как отобразить `TimePicker` мини-приложения как модальное всплывающее диалоговое окно из действие Android. Она предоставила пример `DialogFragment` реализации и рассматривается `IOnTimeSetListener` интерфейс. В этом примере также демонстрируется использование `DialogFragment` можно взаимодействовать с хостом действие, чтобы отобразить установленный период времени.


## <a name="related-links"></a>Связанные ссылки

- [DialogFragment](https://developer.xamarin.com/api/type/Android.App.DialogFragment/)
- [TimePicker](https://developer.xamarin.com/api/type/Android.Widget.TimePicker/)
- [TimePickerDialog](https://developer.xamarin.com/api/type/Android.App.TimePickerDialog/)
- [TimePickerDialog.IOnTimeSetListener](https://developer.xamarin.com/api/type/Android.App.TimePickerDialog+IOnTimeSetListener/)
- [TimePickerDemo (пример)](https://developer.xamarin.com/samples/monodroid/UserInterface/TimePickerDemo)
