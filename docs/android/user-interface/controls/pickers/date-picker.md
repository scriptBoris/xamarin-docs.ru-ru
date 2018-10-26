---
title: Управляющий элемент выбора даты
description: Выбор календарных дат, используя DatePickerDialog и DialogFragment
ms.prod: xamarin
ms.assetid: F2BCD8D4-8957-EA53-C5A8-6BB603ADB47B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 01/22/2018
ms.openlocfilehash: 9f82317f6041de3952d11b391afffafe6fbd8761
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122976"
---
# <a name="date-picker"></a>Управляющий элемент выбора даты

## <a name="overview"></a>Обзор

Существуют случаи, когда пользователь должен входные данные в приложение Android. Чтобы облегчить с этим, предлагает платформы Android [ `DatePicker` ](https://developer.xamarin.com/api/type/Android.Widget.DatePicker/) мини-приложение и [ `DatePickerDialog` ](https://developer.xamarin.com/api/type/Android.App.DatePickerDialog/) . `DatePicker` Позволяет пользователям выбирать в год, месяц и день в согласованный интерфейс для устройств и приложений. `DatePickerDialog` — Это вспомогательный класс, который инкапсулирует `DatePicker` в диалоговом окне.

Современные приложения Android должны отображать `DatePickerDialog` в [ `DialogFragment` ](https://developer.xamarin.com/api/type/Android.App.DialogFragment/). Это позволит приложению отображать DatePicker, как всплывающее диалоговое окно или внедренный в действие. Кроме того `DialogFragment` будет управлять жизненным циклом и отображение диалогового окна, уменьшая объем кода, который должен быть реализован.

В этом руководстве будет показано, как использовать `DatePickerDialog`заключен в `DialogFragment`. Пример приложения будет отображаться `DatePickerDialog` как модальное диалоговое окно, когда пользователь нажимает кнопку в действии. Если дата устанавливается пользователем, `TextView` обновит с датой, которая была выбрана.

[![Снимок экрана выбора даты кнопки, следуют диалоговое окно выбора даты](date-picker-images/image-01-sml.png)](date-picker-images/image-01.png#lightbox)

## <a name="requirements"></a>Требования

В этом руководстве пример приложения предназначен для Android 4.1 (уровень API
16) или более поздней версии, но применяется к Android 3.0 (уровень API 11 или более поздней версии). Это можно реализовать поддержку более старых версиях Android с добавлением v4 библиотеки Android поддерживает для проекта и некоторые изменения в коде.

## <a name="using-the-datepicker"></a>С помощью DatePicker

В этом примере будут расширены `DialogFragment`. Подкласс будут размещены и отображения `DatePickerDialog`:

![Диалоговое окно выбора даты увеличенное изображение](date-picker-images/image-02.png)

Когда пользователь выбирает дату и нажимает кнопку **ОК** кнопки `DatePickerDialog` вызовет метод [ `IOnDateSetListener.OnDateSet` ](https://developer.xamarin.com/api/member/Android.App.DatePickerDialog+IOnDateSetListener.OnDateSet/p/Android.Widget.DatePicker/System.Int32/System.Int32/System.Int32/).
Этот интерфейс реализуется путем размещения `DialogFragment`. Если пользователь нажимает кнопку **отменить** кнопку, то фрагмент и диалоговое окно приводит к отмене сами.

Существует несколько способов `DialogFragment` может возвращать выбранной даты для размещения действия:

1. **Вызов метода или задать свойство** &ndash; действия можно указать свойства или метода специально для данного параметра.

2. **Создайте событие** &ndash; `DialogFragment` можно определить событие, которое будет возникает, когда `OnDateSet` вызывается.

3. **Используйте `Action`**  &ndash; `DialogFragment` можно вызвать `Action<DateTime>` для отображения даты в действии. Действие будет предоставлять `Action<DateTime` при создании экземпляра `DialogFragment`. В этом примере будет использоваться третий способ и указывать действие нужно `Action<DateTime>` для `DialogFragment`.



### <a name="extending-dialogfragment"></a>Расширение DialogFragment

Первым шагом в отображении `DatePickerDialog` — в подкласс `DialogFragment` , который будет реализовывать `IOnDateSetListener` интерфейса:

```csharp
public class DatePickerFragment : DialogFragment, 
                                  DatePickerDialog.IOnDateSetListener
{
    // TAG can be any string of your choice.
    public static readonly string TAG = "X:" + typeof (DatePickerFragment).Name.ToUpper();
    
    // Initialize this value to prevent NullReferenceExceptions.
    Action<DateTime> _dateSelectedHandler = delegate { };
    
    public static DatePickerFragment NewInstance(Action<DateTime> onDateSelected)
    {
        DatePickerFragment frag = new DatePickerFragment();
        frag._dateSelectedHandler = onDateSelected;
        return frag;
    }
    
    public override Dialog OnCreateDialog(Bundle savedInstanceState)
    {
        DateTime currently = DateTime.Now;
        DatePickerDialog dialog = new DatePickerDialog(Activity, 
                                                       this, 
                                                       currently.Year, 
                                                       currently.Month - 1,
                                                       currently.Day);
        return dialog;
    }
    
    public void OnDateSet(DatePicker view, int year, int monthOfYear, int dayOfMonth)
    {
        // Note: monthOfYear is a value between 0 and 11, not 1 and 12!
        DateTime selectedDate = new DateTime(year, monthOfYear + 1, dayOfMonth);
        Log.Debug(TAG, selectedDate.ToLongDateString());
        _dateSelectedHandler(selectedDate);
    }
}
```

`NewInstance` Метод вызывается для создания нового `DatePickerFragment`. Этот метод принимает `Action<DateTime>` , будет вызываться, когда пользователь щелкает **ОК** кнопку `DatePickerDialog`.

Когда фрагмент будет отображаться, Android вызывает метод `OnCreateDialog`. Этот метод создаст новый `DatePickerDialog` и инициализируйте его с текущей даты и объект обратного вызова (который является экземпляром класса `DatePickerFragment`).


> [!NOTE]
> Имейте в виду, что значение месяца при `IOnDateSetListener.OnDateSet` вызывается находится в диапазоне от 0 до 11, а не от 1 до 12. День месяца будет находиться в диапазоне от 1 до 31 (в зависимости от выбранного месяца).



### <a name="showing-the-datepickerfragment"></a>Отображение DatePickerFragment

Теперь, когда `DialogFragment` был реализован, в этом разделе будет рассмотрено использование фрагмента в действии. В примере приложения, сопровождающий это руководство, действие будет создавать экземпляр `DialogFragment` с помощью `NewInstance` фабричный метод, а затем отобразите, он вызывать `DialogFragment.Show`. В ходе создания `DialogFragment`, действие проходит `Action<DateTime>`, который будет отображать даты в `TextView` , размещенного с помощью действия:

```csharp
[Activity(Label = "@string/app_name", MainLauncher = true, Icon = "@drawable/icon")]
public class MainActivity : Activity
{
    TextView _dateDisplay;
    Button _dateSelectButton;

    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SetContentView(Resource.Layout.Main);

        _dateDisplay = FindViewById<TextView>(Resource.Id.date_display);
        _dateSelectButton = FindViewById<Button>(Resource.Id.date_select_button);
        _dateSelectButton.Click += DateSelect_OnClick;
    }

    void DateSelect_OnClick(object sender, EventArgs eventArgs)
    {
        DatePickerFragment frag = DatePickerFragment.NewInstance(delegate(DateTime time)
                                                                 {
                                                                     _dateDisplay.Text = time.ToLongDateString();
                                                                 });
        frag.Show(FragmentManager, DatePickerFragment.TAG);
    }
}
```


## <a name="summary"></a>Сводка

В этом примере описано, как отобразить `DatePicker` мини-приложения как модальное всплывающее диалоговое окно в составе действие Android. Пример реализации DialogFragment управляемый код и обсуждается `IOnDateSetListener` интерфейс. В этом примере также демонстрируется, как DialogFragment может взаимодействовать с хостом для отображения выбранной даты действия.


## <a name="related-links"></a>Связанные ссылки

- [DialogFragment](https://developer.xamarin.com/api/type/Android.App.DialogFragment/)
- [DatePicker](https://developer.xamarin.com/api/type/Android.Widget.DatePicker/)
- [DatePickerDialog](https://developer.xamarin.com/api/type/Android.App.DatePickerDialog/)
- [DatePickerDialog.IOnDateSetListener](https://developer.xamarin.com/api/type/Android.App.DatePickerDialog+IOnDateSetListener/)
- [Выберите дату](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/datepicker/select_a_date)
