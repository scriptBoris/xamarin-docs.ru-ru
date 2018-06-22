---
title: ToggleButton
ms.prod: xamarin
ms.assetid: 9ADA8FCF-63ED-897A-DD56-D7D86535A92C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 8323c456a97a033e19374a4bd3ea7468ecafa608
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30762302"
---
# <a name="togglebutton"></a>ToggleButton

В этом разделе вы создадите специфичный для переключения между двумя состояниями, с помощью кнопки [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) мини-приложения. Это мини-приложение является альтернативой отлично переключателей, если у вас есть два простых состояния, которые являются взаимоисключающими («on» и «off», например). Android 4.0 (API уровня 14) представлены альтернативой выключатель, известный как [ `Switch` ](https://developer.xamarin.com/api/type/Android.Widget.Switch/).

Пример **ToggleButton** можно увидеть в пары слева образов, пока пары правую образов представляет собой пример **коммутатор**:

![Примеры параметров и ToggleButtons как включение и отключение состояния](toggle-button-images/togglebutton-switch.png)  

Какой элемент управления, приложение использует зависит от стиля. Оба мини-приложения функционально эквивалентны.

Откройте **Resources/layout/Main.axml** и добавьте [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) элемент (внутри [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

Чтобы сделать что-либо, при изменении состояния, добавьте следующий код в конец [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle) метод:

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

Здесь фиксируется [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) элемент из макета и обрабатывает событие Click, который определяет действие, выполняемое при нажатии кнопки. В этом примере метод проверяет новое состояние кнопки, а затем показано [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) сообщение, которое указывает текущее состояние.

Обратите внимание, что [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) собственное состояние переключения checked и unchecked, так что он является просто запросить дескрипторов.

Запустите приложение.


**Совет:** Если необходимо изменить состояние самостоятельно (например, при загрузке сохраненного [ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference/)), используйте [ `Checked` ](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/) метод задания свойства или [ `Toggle()` ](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle/) метод.


## <a name="related-links"></a>Связанные ссылки

- [ToggleButton](http://developer.android.com/reference/android/widget/ToggleButton.html)
- [Коммутатор](http://developer.android.com/reference/android/widget/Switch.html)
