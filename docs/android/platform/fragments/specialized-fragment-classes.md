---
title: Фрагмент специализированные классы
ms.prod: xamarin
ms.assetid: 7A0AEB2C-EE77-63BF-652A-DA049B691C64
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/08/2018
ms.openlocfilehash: ab7045b16d441a3f2ad06c64d08b9b98135b18a7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30769251"
---
# <a name="specialized-fragment-classes"></a>Фрагмент специализированные классы

Фрагменты API предоставляет другие подклассов, которые инкапсулируют некоторые из наиболее распространенных функциональными возможностями, имеющимися в приложениях. Ниже приведены эти подклассы.

-   **ListFragment** &ndash; этот фрагмент, используется для отображения списка элементов, привязанный к источнику данных, например, массив или курсор.

-   **DialogFragment** &ndash; этот фрагмент используется как оболочка диалогового окна. Фрагмент отображается диалоговое окно на основе его действия.

-   **PreferenceFragment** &ndash; этот фрагмент используется для отображения объектов предпочтений в виде списков.



## <a name="the-listfragment"></a>ListFragment

`ListFragment` Очень похожа на концепции и функциональные возможности для `ListActivity`; это программа-оболочка, на котором размещена `ListView` в фрагменте. На рисунке ниже показана `ListFragment` на планшете и телефон:

[![Снимки экрана ListFragment на планшете и на телефон](specialized-fragment-classes-images/intro-screenshot-sml.png)](specialized-fragment-classes-images/intro-screenshot.png#lightbox)


### <a name="binding-data-with-the-listadapter"></a>Привязка данных с ListAdapter

`ListFragment` Класс уже предоставляет макет по умолчанию, поэтому не требуется переопределять `OnCreateView` для отображения содержимого `ListFragment`. `ListView` Привязан к данным с помощью `ListAdapter` реализации. В следующем примере показано, как это можно сделать с помощью простого массива строк:

```csharp
public override void OnActivityCreated(Bundle savedInstanceState)
{
    base.OnActivityCreated(savedInstanceState);
    string[] values = new[] { "Android", "iPhone", "WindowsMobile",
                "Blackberry", "WebOS", "Ubuntu", "Windows7", "Max OS X",
                "Linux", "OS/2" };
    this.ListAdapter = new ArrayAdapter<string>(Activity, Android.Resource.Layout.SimpleExpandableListItem1, values);
}
```

При задании `ListAdapter`, очень важно использовать `ListFragment.ListAdapter` свойства, а не `ListView.ListAdapter` свойство. С помощью `ListView.ListAdapter` вызовет важный код инициализации должен пропускать.



### <a name="responding-to-user-selection"></a>Отклик на выбор пользователя

Для ответа на выборе пользователя, приложение необходимо переопределить `OnListItemClick` метод. Ниже приведен пример такой возможности.

```csharp
public override void OnListItemClick(ListView l, View v, int index, long id)
{
    // We can display everything in place with fragments.
    // Have the list highlight this item and show the data.
    ListView.SetItemChecked(index, true);

    // Check what fragment is shown, replace if needed.
    var details = FragmentManager.FindFragmentById<DetailsFragment>(Resource.Id.details);
    if (details == null || details.ShownIndex != index)
    {
        // Make new fragment to show this selection.
        details = DetailsFragment.NewInstance(index);

        // Execute a transaction, replacing any existing
        // fragment with this one inside the frame.
        var ft = FragmentManager.BeginTransaction();
        ft.Replace(Resource.Id.details, details);
        ft.SetTransition(FragmentTransit.FragmentFade);
        ft.Commit();
    }
}
```

В коде выше, когда пользователь выбирает элемент в `ListFragment`, новый фрагмент отображается в действии размещения отображение подробной информации о выбранного элемента.



## <a name="dialogfragment"></a>DialogFragment

*DialogFragment* является фрагментом, который используется для отображения диалогового окна объекта внутри фрагмента, который перемещается над окном действия. Он предназначен для замены управляемого API-интерфейсы (начиная с Android 3.0) диалогового окна. На следующем рисунке показан пример `DialogFragment`:

[![Снимок экрана из DialogFragment отображение добавить новый элемент управления EditBox Vehicle](specialized-fragment-classes-images/dialog-fragment-example.png)](specialized-fragment-classes-images/dialog-fragment-example.png#lightbox)

Объект `DialogFragment` гарантирует, что состояние между фрагмента и диалогового окна остаются согласованными. Все взаимодействия и управления диалогового окна объекта должны быть выполнены через `DialogFragment` API и не выполнять с прямыми вызовами в объекте диалогового окна. `DialogFragment` API предоставляет каждый экземпляр с `Show()` метод, используемый для отображения фрагмента. Чтобы избавиться от фрагмента двумя способами.

-  Вызовите `DialogFragment.Dismiss()` на `DialogFragment` экземпляра. 

-  Отображение другого `DialogFragment`.

Для создания `DialogFragment`, класс наследует от `Android.App.DialogFragment,` и переопределяет метод одно из следующих двух способов:

- **OnCreateView** &ndash; это создает и возвращает представление.

- **OnCreateDialog** &ndash; при этом создается настраиваемое диалоговое окно. Обычно он используется для отображения *AlertDialog*. При переопределении этого метода, не требуется переопределять `OnCreateView` .



### <a name="a-simple-dialogfragment"></a>Простой DialogFragment

На следующем рисунке показан простой `DialogFragment` с `TextView` и два `Button`s:

[![Пример DialogFragment с текстового представления и две кнопки](specialized-fragment-classes-images/dialog-fragment-example-2.png)](specialized-fragment-classes-images/dialog-fragment-example-2.png#lightbox)

`TextView` Отображается, сколько раз пользователь нажал кнопку один в `DialogFragment`, в то время, нажав кнопку «Другие» закроется фрагмента. Код для `DialogFragment` является:

```csharp
public class MyDialogFragment : DialogFragment
{
    private int _clickCount;
    public override void OnCreate(Bundle savedInstanceState)
    {
        _clickCount = 0;
    }

    public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState)
        
        var view = inflater.Inflate(Resource.Layout.dialog_fragment_layout, container, false);
        var textView = view.FindViewById<TextView>(Resource.Id.dialog_text_view);
            
        view.FindViewById<Button>(Resource.Id.dialog_button).Click += delegate
            {
                textView.Text = "You clicked the button " + _clickCount++ + " times.";
            };

        // Set up a handler to dismiss this DialogFragment when this button is clicked.
        view.FindViewById<Button>(Resource.Id.dismiss_dialog_button).Click += (sender, args) => Dismiss();
        return view;
        }
    }
}
```


### <a name="displaying-a-fragment"></a>Отображение фрагмента

Как и все фрагменты `DialogFragment` отображается в контексте `FragmentTransaction`.

`Show()` Метод `DialogFragment` принимает `FragmentTransaction` и `string` в качестве ввода. Диалог будет добавлен в действие и `FragmentTransaction` зафиксирована.

Следующий код демонстрирует один из возможных путей, могут использовать действие `Show()` метод для отображения `DialogFragment`:

```csharp
public void ShowDialog()
{
    var transaction = FragmentManager.BeginTransaction();
    var dialogFragment = new MyDialogFragment();
    dialogFragment.Show(transaction, "dialog_fragment");
}
```


### <a name="dismissing-a-fragment"></a>Отклонения фрагмент

Вызов `Dismiss()` экземпляра `DialogFragment` вызывает фрагмент, удаляемый из действия и фиксации этой транзакции.
Стандартные методы жизненного цикла фрагмент, участвующих в уничтожения фрагмента будет вызываться.


### <a name="alert-dialog"></a>Диалоговое окно предупреждения

Вместо переопределения `OnCreateView`, `DialogFragment` вместо могут переопределять `OnCreateDialog`. Это позволяет приложению создавать [AlertDialog](https://developer.xamarin.com/api/type/Android.App.AlertDialog/) под управлением фрагмент. Ниже приведен пример, использующий `AlertDialog.Builder` для создания `Dialog`:

```csharp
public class AlertDialogFragment : DialogFragment
{
    public override Dialog OnCreateDialog(Bundle savedInstanceState)
    {
        EventHandler<DialogClickEventArgs> okhandler;
        var builder = new AlertDialog.Builder(Activity)
            .SetMessage("This is my dialog.")
            .SetPositiveButton("Ok", (sender, args) =>
                                         {
                                             // Do something when this button is clicked.
                                         })
            .SetTitle("Custom Dialog");
        return builder.Create();
    }
}
```



## <a name="preferencefragment"></a>PreferenceFragment

Чтобы установить и другие параметры, предоставляет API фрагментов `PreferenceFragment` подкласс. `PreferenceFragment` Аналогичен [PreferenceActivity](https://developer.xamarin.com/api/type/Android.Preferences.PreferenceActivity/) &ndash; иерархию персональные настройки для пользователя, она будет отображаться в фрагменте. Как пользователь взаимодействует с настройками, они будут автоматически сохранены для [SharedPreferences](http://developer.android.com/reference/android/content/SharedPreferences.html).
В Android 3.0 или более поздней версии приложения, используйте `PreferenceFragment` с предпочтения в приложении. На следующем рисунке показаны пример `PreferenceFragment`:

[![Пример PreferencesFragment с встроенного диалогового окна и параметры запуска](specialized-fragment-classes-images/preferences-dialog.png)](specialized-fragment-classes-images/preferences-dialog.png#lightbox)


### <a name="create-a-preference-fragment-from-a-resource"></a>Создать фрагмент предпочтений из ресурса

Предпочтения, может оказаться завышенными фрагмент из файла ресурсов XML с помощью [PreferenceFragment.AddPreferencesFromResource](https://developer.xamarin.com/api/member/Android.Preferences.PreferenceFragment.AddPreferencesFromResource/p/System.Int32/) метод. Для вызова этого метода в жизненном цикле фрагмента логично будут находиться в `OnCreate` метод.

`PreferenceFragment` Изображено на рисунке выше был создан по загрузке ресурса из XML. Файл ресурсов находится:

```xml
<?xml version="1.0" encoding="utf-8"?>

<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android">

  <PreferenceCategory android:title="Inline Preferences">
    <CheckBoxPreference android:key="checkbox_preference"
                        android:title="Checkbox Preference Title"
                        android:summary="Checkbox Preference Summary" />

  </PreferenceCategory>

  <PreferenceCategory android:title="Dialog Based Preferences">

    <EditTextPreference android:key="edittext_preference"
                        android:title="EditText Preference Title"
                        android:summary="EditText Preference Summary"
                        android:dialogTitle="Edit Text Preferrence Dialog Title" />

  </PreferenceCategory>

  <PreferenceCategory android:title="Launch Preferences">

    <!-- This PreferenceScreen tag serves as a screen break (similar to page break
             in word processing). Like for other preference types, we assign a key
             here so it is able to save and restore its instance state. -->
    <PreferenceScreen android:key="screen_preference"
                      android:title="Title Screen Preferences"
                      android:summary="Summary Screen Preferences">

      <!-- You can place more preferences here that will be shown on the next screen. -->

      <CheckBoxPreference android:key="next_screen_checkbox_preference"
                          android:title="Next Screen Toggle Preference Title"
                          android:summary="Next Screen Toggle Preference Summary" />

    </PreferenceScreen>

    <PreferenceScreen android:title="Intent Preference Title"
                      android:summary="Intent Preference Summary">

      <intent android:action="android.intent.action.VIEW"
              android:data="http://www.android.com" />

    </PreferenceScreen>

  </PreferenceCategory>

</PreferenceScreen>
```

Код для настройки фрагмент выглядит следующим образом:

```csharp
public class PrefFragment : PreferenceFragment
{
    public override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        AddPreferencesFromResource(Resource.Xml.preferences);
    }
}
```



### <a name="querying-activities-to-create-a-preference-fragment"></a>Запрос действия для создания фрагмента предпочтения

Другим способом создания `PreferenceFragment` включает в себя запросы действий. Каждое действие можно использовать [МЕТАДАННЫЕ\_ключ\_ПРЕДПОЧТЕНИЯ](https://developer.xamarin.com/api/field/Android.Preferences.PreferenceManager.MetadataKeyPreferences/) атрибут, который будет указывать на файл ресурсов XML. В Xamarin.Android, это можно сделать, Декорирование действие с `MetaDataAttribute`и указав файл ресурсов. `PreferenceFragment` Класс предоставляет метод [AddPreferenceFromIntent](https://developer.xamarin.com/api/member/Android.Preferences.PreferenceFragment.AddPreferencesFromIntent/p/Android.Content.Intent/)) может использоваться для запроса для поиска этого ресурса XML и увеличению иерархии предпочтения для него действия.

Пример этого процесса приведен в следующем фрагменте кода, который использует `AddPreferencesFromIntent` для создания `PreferenceFragment`:

```csharp
public class MyPreferenceFragment : PreferenceFragment
{
    public override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        var intent = new Intent(this.Activity, typeof (MyActivityWithPreferences));
        AddPreferencesFromIntent(intent);
    }
}
```

Android будут рассмотрены класса `MyActivityWithPreference`. Снабженных класса `MetaDataAttribute,` как показано в следующем фрагменте кода:

```csharp
[Activity(Label = "My Activity with Preferences")]
[MetaData(PreferenceManager.MetadataKeyPreferences, Resource = "@xml/preference_from_intent")]
public class MyActivityWithPreferences : Activity
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        // This is deliberately blank
    }
}
```

`MetaDataAttribute` Объявляет файл ресурсов XML, `PreferenceFragment` будет использовать для увеличению иерархии предпочтения. Если `MetatDataAttribute` значение не указано, будет создано исключение во время выполнения. Этот код, запускаемый `PreferenceFragment` отображается как в следующем снимке экрана:

[![Снимок экрана: пример приложения с PreferenceFragment отображается](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png)](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png#lightbox)
