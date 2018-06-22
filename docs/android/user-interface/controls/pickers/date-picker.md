---
title: Управляющий элемент выбора даты
description: При выборе календарных дат, используя DatePickerDialog и DialogFragment
ms.prod: xamarin
ms.assetid: F2BCD8D4-8957-EA53-C5A8-6BB603ADB47B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/22/2018
ms.openlocfilehash: 916a9c74fa28b99e799eef80db822e86cfda617d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30764987"
---
# <a name="date-picker"></a>Управляющий элемент выбора даты

## <a name="overview"></a>Обзор

Бывают ситуации, когда пользователь должен ввести данные в приложения. Для упрощения этого платформа Android предоставляет [ `DatePicker` ](https://developer.xamarin.com/api/type/Android.Widget.DatePicker/) мини-приложение и [ `DatePickerDialog` ](https://developer.xamarin.com/api/type/Android.App.DatePickerDialog/) . `DatePicker` Позволяет пользователю выбрать год, месяц и день в согласованный интерфейс между устройствами и приложениями. `DatePickerDialog` — Это вспомогательный класс, который инкапсулирует `DatePicker` в диалоговом окне.

Отображать современных приложений для Android `DatePickerDialog` в [ `DialogFragment` ](https://developer.xamarin.com/api/type/Android.App.DialogFragment/). Это позволяет приложению отображать DatePicker как всплывающее диалоговое окно или внедрены в действии. Кроме того `DialogFragment` будет осуществляться управление жизненным циклом и отображение диалогового окна, уменьшая объем кода, который должен быть реализован.

В этом руководстве будет показано, как использовать `DatePickerDialog`, упакованное в `DialogFragment`. Отображает образец приложения `DatePickerDialog` как модальное диалоговое окно при нажатии кнопки в действии. Если дата задается пользователем, `TextView` обновит с датой, которая была выбрана.

[![Снимок экрана выбора даты кнопка следуют диалоговое окно «Выбор даты»](date-picker-images/image-01-sml.png)](date-picker-images/image-01.png#lightbox)

## <a name="requirements"></a>Требования

Образец приложения в этом руководстве обращается Android 4.1 (уровень API
16) или более поздней версии, но применимо к Android 3.0 (уровень API 11 или выше). Существует возможность поддерживают более старые версии Android с добавлением v4 библиотеку поддержки Android для проекта и некоторые изменения в коде.

## <a name="using-the-datepicker"></a>С помощью DatePicker

В этом примере будет расширять `DialogFragment`. Подкласс размещения и отображения `DatePickerDialog`:

![Диалоговое окно выбора даты увеличенное изображение](date-picker-images/image-02.png)

Когда пользователь выбирает дату и нажимает кнопку **ОК** кнопки, `DatePickerDialog` вызывает метод [ `IOnDateSetListener.OnDateSet` ](https://developer.xamarin.com/api/member/Android.App.DatePickerDialog+IOnDateSetListener.OnDateSet/p/Android.Widget.DatePicker/System.Int32/System.Int32/System.Int32/).
Этот интерфейс реализуется путем размещения `DialogFragment`. Если пользователь нажимает кнопку **отменить** кнопку фрагмент и диалоговое окно будет закрыть сами.

Существует несколько способов `DialogFragment` может возвращать выбранную дату для размещения действия:

1. **Вызвать метод или задать свойство** &ndash; действия можно указать свойство или метод специально для данного параметра.

2. **Вызывает событие** &ndash; `DialogFragment` можно определить событие, которое будет возникает, когда `OnDateSet` вызывается.

3. **Используйте `Action`**  &ndash; `DialogFragment` можно вызвать `Action<DateTime>` для отображения даты в действии. Действие будет предоставлять `Action<DateTime` при создании экземпляра `DialogFragment`. В этом примере третий способ использования и указывать действия нужно `Action<DateTime>` для `DialogFragment`.



### <a name="extending-dialogfragment"></a>Расширение DialogFragment

Первым шагом в отображение `DatePickerDialog` является подклассом `DialogFragment` и его реализации `IOnDateSetListener` интерфейс:

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

`NewInstance` Для создания нового экземпляра будет вызван метод `DatePickerFragment`. Этот метод принимает `Action<DateTime>` , будет вызываться, когда пользователь щелкает **ОК** кнопку в `DatePickerDialog`.

После отображения фрагмента Android вызывает метод `OnCreateDialog`. Этот метод создаст новый `DatePickerDialog` и инициализируйте его с текущей даты и объект обратного вызова (который является текущим экземпляром `DatePickerFragment`).


> [!NOTE]
> Имейте в виду, что значение месяца при `IOnDateSetListener.OnDateSet` вызывается находится в диапазоне от 0 до 11, а не от 1 до 12. День месяца будет в диапазоне от 1 до 31 (в зависимости от того, который был выбран месяц).



### <a name="showing-the-datepickerfragment"></a>Отображение DatePickerFragment

Теперь, когда `DialogFragment` был реализован, в этом разделе рассмотрим, как использовать этот фрагмент в действии. В примере приложения, сопровождающий это руководство, будет создан экземпляр действия `DialogFragment` с помощью `NewInstance` фабричный метод и затем отображения, он вызывать `DialogFragment.Show`. В процессе создания экземпляра `DialogFragment`, действие передает `Action<DateTime>`, который будет отображать даты в `TextView` , размещенного на действия:

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

В этом примере описано, как отобразить `DatePicker` мини-приложение как модальное диалоговое окно всплывающего окна в составе Android действия. Он предоставлен образец реализации DialogFragment и обсуждаются `IOnDateSetListener` интерфейса. В этом примере также показано, как DialogFragment может взаимодействовать с узлом, чтобы отобразить выбранную дату.


## <a name="related-links"></a>Связанные ссылки

- [DialogFragment](https://developer.xamarin.com/api/type/Android.App.DialogFragment/)
- [DatePicker](https://developer.xamarin.com/api/type/Android.Widget.DatePicker/)
- [DatePickerDialog](https://developer.xamarin.com/api/type/Android.App.DatePickerDialog/)
- [DatePickerDialog.IOnDateSetListener](https://developer.xamarin.com/api/type/Android.App.DatePickerDialog+IOnDateSetListener/)
- [Выберите дату](https://github.com/xamarinhttps://developer.xamarin.com/recipes/tree/master/android/controls/datepicker/select_a_date)
