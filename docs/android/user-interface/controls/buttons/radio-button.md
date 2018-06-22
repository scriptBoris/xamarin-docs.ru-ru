---
title: RadioButton
ms.prod: xamarin
ms.assetid: 3C32EA3F-D917-C988-72C5-A17354DA791E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 1267491f2d9b7519f76651df059722420fa8e1eb
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763118"
---
# <a name="radiobutton"></a>RadioButton

В этом разделе вы создадите два взаимоисключающими переключателей (Включение один отключает другой), с помощью [ `RadioGroup` ](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/) и [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/) мини-приложения. При нажатии любой переключатель отображается всплывающее сообщение.


Откройте **Resources/layout/Main.axml** и добавьте два файла [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)s, вложенные в [ `RadioGroup` ](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/) (внутри [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

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

Очень важно, [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)s группируются с [ `RadioGroup` ](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/) элемент, чтобы одновременно не более одного можно выбрать. Эта логика автоматически обрабатываются системой Android. Если в одном [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/) в выбранной группе, все остальные не автоматически выбраны.

К каким-либо при каждой [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/) — флажок установлен, необходимо написать обработчик событий:

```csharp
private void RadioButtonClick (object sender, EventArgs e)
{
    RadioButton rb = (RadioButton)sender;
    Toast.MakeText (this, rb.Text, ToastLength.Short).Show ();
}
```

Во-первых отправитель переданный приведен в RadioButton.
Затем [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) сообщение отображается текст выбранного переключателя.

Теперь в нижней части [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle) метод, добавьте следующий код:

```csharp
RadioButton radio_red = FindViewById<RadioButton>(Resource.Id.radio_red);
RadioButton radio_blue = FindViewById<RadioButton>(Resource.Id.radio_blue);

radio_red.Click += RadioButtonClick;
radio_blue.Click += RadioButtonClick;
```

Это соответствует каждая из [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)s из макета и добавляет handlerto созданное событие каждый.

Запустите приложение.

**Совет:** Если необходимо изменить состояние самостоятельно (например, при загрузке сохраненного [ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference/)), используйте [ `Checked` ](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/) метод задания свойства или [ `Toggle()` ](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle/) метод.

*Некоторые части этой страницы, изменения на основе работы создан и совместно используются Android открыть исходный проект и используются в соответствии с условиями, описанной в*
[*Creative Commons 2.5 однозначного соответствия лицензий* ](http://creativecommons.org/licenses/by/2.5/). 
