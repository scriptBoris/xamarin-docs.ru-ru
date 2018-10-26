---
title: RadioButton
ms.prod: xamarin
ms.assetid: 3C32EA3F-D917-C988-72C5-A17354DA791E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: be473580b24dba6b4f08384771e2097d368f8dc8
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123600"
---
# <a name="radiobutton"></a>RadioButton

В этом разделе вы создадите два взаимоисключающими переключателей (Включение один запрет на другой), с помощью [`RadioGroup`](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/)
и [`RadioButton`](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)
мини-приложения. При нажатии одной из кнопок переключателей, отобразится всплывающее сообщение.


Откройте **Resources/layout/Main.axml** файл и добавьте два [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)s, вложенные в [ `RadioGroup` ](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/) (внутри [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

```xml
<RadioGroup
  android:layout_width="fill_parent"
  android:layout_height="wrap_content"
  android:orientation="vertical">
  <RadioButton android:id="@+id/radio_red"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:text="Red" />
  <RadioButton android:id="@+id/radio_blue"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:text="Blue" />
</RadioGroup>
```

Очень важно, [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)s группируются по [ `RadioGroup` ](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/) элемент таким образом, можно выбрать не более одного за раз. Эта логика автоматически обрабатываются системой Android. Если в одном [`RadioButton`](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)
в выбранной группе, все другие автоматически невыделенные.

Сделать что-то при каждой [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/) — флажок установлен, необходимо написать обработчик событий:

```csharp
private void RadioButtonClick (object sender, EventArgs e)
{
    RadioButton rb = (RadioButton)sender;
    Toast.MakeText (this, rb.Text, ToastLength.Short).Show ();
}
```

Во-первых отправитель, который передается в будет преобразована в RadioButton.
Затем [`Toast`](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
сообщение отображается текст выбранного переключателя.

Теперь, в нижней части [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle)
метод, добавьте следующий код:

```csharp
RadioButton radio_red = FindViewById<RadioButton>(Resource.Id.radio_red);
RadioButton radio_blue = FindViewById<RadioButton>(Resource.Id.radio_blue);

radio_red.Click += RadioButtonClick;
radio_blue.Click += RadioButtonClick;
```

Это соответствует каждая из [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)s из макета и добавляет handlerto созданное событие каждый.

Запустите приложение.

**Совет:** Если необходимо изменить состояние самостоятельно (например, при загрузке сохраненного [ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference/)), используйте [`Checked`](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/)
метод задания свойства или [`Toggle()`](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle/)
метод.

*Некоторые части этой страницы, изменения с учетом работы создана и совместно используется Android откройте исходный проект и используются в соответствии с условиями, описанными в*
[*лицензии Creative Commons 2.5 Attribution* ](http://creativecommons.org/licenses/by/2.5/). 
