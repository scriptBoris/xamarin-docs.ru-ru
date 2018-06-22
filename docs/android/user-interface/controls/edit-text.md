---
title: Изменить текст
ms.prod: xamarin
ms.assetid: E513BCBC-438E-15E8-B83A-4B768A8E8B32
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: d6be8ae1587742a8c2a37b22b2da3701187dde2a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30774607"
---
# <a name="edit-text"></a>Изменить текст

В этом разделе вы создадите текстовое поле для ввода данных пользователем, с помощью [EditText](https://developer.xamarin.com/api/type/Android.Widget.EditText/) мини-приложения. После ввода текста в поле клавишу «ВВОД» будет отображать текст в сообщении тост.

Откройте <code>Resources\layout\main.xml</code> и добавьте [EditText](https://developer.xamarin.com/api/type/Android.Widget.EditText/) элемент (внутри [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

```xml
<pre><code class=" syntax brush-C#">&lt;EditText
    android:id="@+id/edittext"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"/&gt;</code></pre>
```

Чтобы сделать что-либо текстом, который пользователем, добавьте следующий код в конец [OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/) метод:

```csharp
EditText edittext = FindViewById<EditText>(Resource.Id.edittext);
edittext.KeyPress += (object sender, View.KeyEventArgs e) => {
 e.Handled = false;
 if (e.Event.Action == KeyEventActions.Down && e.KeyCode == Keycode.Enter) {
  Toast.MakeText (this, edittext.Text, ToastLength.Short).Show ();
  e.Handled = true;
         }
};
```

Здесь фиксируется [ `EditText` ](https://developer.xamarin.com/api/type/Android.Widget.EditText/) элемента макета и наборы [ `KeyPress` ](https://developer.xamarin.com/api/event/Android.Views.View.KeyPress/) обработчик, который определяет действие, необходимо учитывать при нажатии клавиши, когда мини-приложение имеет фокус. В этом случае для прослушивания клавишу ВВОД (при нажатии клавиши), а затем всплывающее окно определен метод [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) сообщение с текстом, который был введен. [ `Handled` ](https://developer.xamarin.com/api/property/Android.Views.View+KeyEventArgs.Handled/) Свойство всегда должно быть `true` Если событие было обработано, так что событие не всплывающее (что привело бы к символ возврата каретки в текстовом поле).

Запустите приложение.

*Части этой страницы, изменения на основе работы создан и* [ *совместно с Android открыть исходный проект* ](http://code.google.com/policies.html) *и используются в соответствии с условиями, описанной в* [ *Лицензии creative Commons 2.5 однозначного соответствия* ](http://creativecommons.org/licenses/by/2.5/) *. Этот учебник основывается на* [ *Android Stuff формы учебника* ](http://developer.android.com/resources/tutorials/views/hello-formstuff.html) *.*



## <a name="related-links"></a>Связанные ссылки

- [EditTextSample](https://developer.xamarin.com/samples/monodroid/UserInterface/EditTextSample/)
