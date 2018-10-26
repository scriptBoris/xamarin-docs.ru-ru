---
title: ToggleButton
ms.prod: xamarin
ms.assetid: 9ADA8FCF-63ED-897A-DD56-D7D86535A92C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 22eb8f999450ed8fb46b1f7809c92540be13aa65
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105926"
---
# <a name="togglebutton"></a>ToggleButton

В этом разделе вы создадите специфичный для переключения между двумя состояниями, с помощью кнопки [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) мини-приложения. Это мини-приложение – отличная альтернатива для переключателей, у вас есть два простых состояния, которые являются взаимоисключающими («on» и «отключено», например). Android 4.0 (уровень API 14) появилась альтернатива кнопки-переключателя, известный как [ `Switch` ](https://developer.xamarin.com/api/type/Android.Widget.Switch/).

Пример **ToggleButton** можно увидеть в паре «слева» образов, хотя пары правом образов приводится пример **коммутатора**:

![Примеры коммутаторов и ToggleButtons как включение и отключение состояния](toggle-button-images/togglebutton-switch.png)  

Какой элемент управления, приложение использует зависит от стиля. Оба мини-приложения функционально эквивалентны.

Откройте **Resources/layout/Main.axml** файл и добавьте [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) элемент (внутри [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

Чтобы сделать что-то, при изменении состояния, добавьте следующий код в конец [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle)
метод:

```csharp
ToggleButton togglebutton = FindViewById<ToggleButton>(Resource.Id.togglebutton);

togglebutton.Click += (o, e) => {
    // Perform action on clicks
    if (togglebutton.Checked)
        Toast.MakeText(this, "Checked", ToastLength.Short).Show ();
    else
        Toast.MakeText(this, "Not checked", ToastLength.Short).Show ();
};
```

Здесь фиксируется [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) элемента из макета и обрабатывает событие щелчка, который определяет действие, выполняемое при нажатии кнопки. В этом примере метод проверяет новое состояние кнопки, а затем отображает [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) сообщение, которое указывает текущее состояние.

Обратите внимание, что [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) собственное состояние переключения checked и unchecked, так что это просто попросите дескрипторов.

Запустите приложение.


**Совет:** Если необходимо изменить состояние самостоятельно (например, при загрузке сохраненного [ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference/)), используйте [`Checked`](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/)
метод задания свойства или [`Toggle()`](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle/)
метод.


## <a name="related-links"></a>Связанные ссылки

- [Выключатель](http://developer.android.com/reference/android/widget/ToggleButton.html)
- [Коммутатор](http://developer.android.com/reference/android/widget/Switch.html)
