---
title: CheckBox
ms.prod: xamarin
ms.assetid: A884AF10-D5EA-72CA-2301-B80CEC7FFBE7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 85c505d03e7a763b24fb176b6a94c0fe43009b79
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30765757"
---
# <a name="checkbox"></a>CheckBox

В этом разделе вы создадите флажок для выбора элементов, с помощью [ `CheckBox` ](https://developer.xamarin.com/api/type/Android.Widget.CheckBox) мини-приложения. При нажатии флажок всплывающее сообщение текущее состояние флажка.

Откройте **Resources/layout/Main.axml** и добавьте [ `CheckBox` ](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/) элемент (внутри [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout)):

```xml
<CheckBox android:id="@+id/checkbox"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="check it out" />
```

Чтобы сделать что-либо, при изменении состояния, добавьте следующий код в конец [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle) метод:

```csharp
CheckBox checkbox = FindViewById<CheckBox>(Resource.Id.checkbox);

checkbox.Click += (o, e) => {
    if (checkbox.Checked)
        Toast.MakeText (this, "Selected", ToastLength.Short).Show ();
    else
        Toast.MakeText (this, "Not selected", ToastLength.Short).Show ();
};
```

Здесь фиксируется [ `CheckBox` ](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/) элемент из макета, затем обрабатывает событие Click, который определяет действие, которое будет предпринята, когда выбирается флажок. При щелчке [ `Checked` ](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/) свойство вызывается, чтобы проверить новое состояние флажка. Если он установлен, то [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) отображается сообщение «Выбранные», в противном случае отображается «Не установлен». [ `CheckBox` ](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/) Обрабатывает собственные изменения состояния, поэтому необходимо запрашивать текущее состояние.

Запустите его.

**Совет:** Если необходимо изменить состояние самостоятельно (например, при загрузке сохраненного [ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference), используйте [ `Checked` ](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked) метод задания свойства или [ `Toggle()` ](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle) метод.

*Некоторые части этой страницы, изменения на основе работы создан и совместно используются Android открыть исходный проект и используются в соответствии с условиями, описанной в*
[*Creative Commons 2.5 однозначного соответствия лицензий* ](http://creativecommons.org/licenses/by/2.5/).
