---
title: Изменить текст
description: Как использовать мини-приложение EditText принимать ввод пользователя.
ms.prod: xamarin
ms.assetid: E513BCBC-438E-15E8-B83A-4B768A8E8B32
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 08/09/2018
ms.openlocfilehash: bb2cb13472e7e17eb1b0438ed67033f2b04defe2
ms.sourcegitcommit: b6f3e55d4f3dcdc505abc8dc9241cff0bb5bd154
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2018
ms.locfileid: "40251208"
---
# <a name="edit-text"></a>Изменить текст

В этом разделе вы воспользуетесь [EditText](https://developer.xamarin.com/api/type/Android.Widget.EditText/) мини-приложение, чтобы создать текстовое поле для ввода данных пользователем. После ввода текста в поле **ввод** ключ будет отображать текст в всплывающее сообщение.

Откройте **Resources/layout/activity_main.axml** и добавьте [EditText](https://developer.xamarin.com/api/type/Android.Widget.EditText/) элемент, содержащий макет. Следующий пример **activity_main.axml** имеет `EditText` , добавленного к `LinearLayout`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <EditText
        android:id="@+id/edittext"
        android:layout_width="match_parent"
        android:imeOptions="actionGo"
        android:inputType="text"
        android:layout_height="wrap_content" />
</LinearLayout>
```

В этом примере кода `EditText` атрибут `android:imeOptions` присваивается `actionGo`. Этот параметр изменяет значение по умолчанию [сделать](https://developer.android.com/reference/android/view/inputmethod/EditorInfo#IME_ACTION_DONE) действие [Go](https://developer.android.com/reference/android/view/inputmethod/EditorInfo#IME_ACTION_GO) действие таким образом, при касании **ввод** ключа триггеры `KeyPress` входной обработчик.
(Как правило, `actionGo` используется, чтобы **ввод** ключ направляет пользователя на целевой URL-адреса, введенного в поле.)

Чтобы обрабатывать ввод текста пользователем, добавьте следующий код в конец [OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/) метод в **MainActivity.cs**:

```csharp
EditText edittext = FindViewById<EditText>(Resource.Id.edittext);
edittext.KeyPress += (object sender, View.KeyEventArgs e) => {
    e.Handled = false;
    if (e.Event.Action == KeyEventActions.Down && e.KeyCode == Keycode.Enter) 
    {
        Toast.MakeText(this, edittext.Text, ToastLength.Short).Show();
        e.Handled = true;
    }
};
```

Кроме того, добавьте следующий `using` инструкцию в верхнюю часть **MainActivity.cs** если его еще нет:

```csharp
using Android.Views;
```

Данный пример кода увеличивает [EditText](https://developer.xamarin.com/api/type/Android.Widget.EditText/) элемента из макета и добавляет [KeyPress](https://developer.xamarin.com/api/event/Android.Views.View.KeyPress/) обработчик, который определяет действие, которое следует учитывать при нажатии клавиши, когда фокус находится в мини-приложения. В этом случае метод определяется для прослушивания **ввод** ключ (когда касание), а затем всплывающее окно [всплывающих](https://developer.xamarin.com/api/type/Android.Widget.Toast/) сообщение с текстом, который был введен. Обратите внимание, что [обработанных](https://developer.xamarin.com/api/property/Android.Views.View+KeyEventArgs.Handled/) свойства всегда должно быть `true` Если событие было обработано. Это необходимо предотвратить это событие восходящей маршрутизации вверх (что привело бы символ возврата каретки в текстовом поле).

Запустите приложение и введите любой текст в текстовое поле. При нажатии клавиши **ввод** ключа, всплывающее уведомление будет отображаться как показано в правой части:

[![Примеры ввода текста в EditText](edit-text-images/edit-text-sml.png)](edit-text-images/edit-text.png#lightbox)

*Некоторые части этой страницы, изменения с учетом работы и* [ *совместно используется Android откройте исходный проект* ](http://code.google.com/policies.html) *и используются в соответствии с условиями, описанными в* [ *Лицензия creative Commons 2.5 однозначного соответствия примитивов* ](http://creativecommons.org/licenses/by/2.5/) *. Этот учебник создан на основе* [ *Android Stuff формы руководстве* ](http://developer.android.com/resources/tutorials/views/hello-formstuff.html) *.*


## <a name="related-links"></a>Связанные ссылки

- [EditTextSample](https://developer.xamarin.com/samples/monodroid/UserInterface/EditTextSample/)
