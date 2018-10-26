---
title: Пользовательская кнопка
ms.prod: xamarin
ms.assetid: C523D41E-5855-248D-079D-6B12B74B7617
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: b5ccefa1eb7e659584c1c82481bbd4473a3a8abc
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122859"
---
# <a name="custom-button"></a>Пользовательская кнопка

В этом разделе вы создадите кнопка с пользовательским образом вместо текста, с помощью [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) мини-приложение и XML-файл, определяющий три различных изображений, используемых для различные состояния кнопок. При нажатии кнопки, короткое сообщение будет отображаться.

Щелкните правой кнопкой мыши и скачайте приведенные ниже три изображения, а затем скопируйте их в **ресурсы/drawable** каталоге вашего проекта. Они будут использоваться для различные состояния кнопок.

 [![Android зеленый значок для обычного состояния](custom-button-images/android-normal.png)](custom-button-images/android-normal.png#lightbox) [ ![Android оранжевый значок конкретное состояние](custom-button-images/android-focused.png)](custom-button-images/android-focused.png#lightbox) [ ![Android желтый значок нажатом состоянии](custom-button-images/android-pressed.png)](custom-button-images/android-pressed.png#lightbox)

Создайте новый файл в **ресурсы/drawable** каталог с именем **android_button.xml**. Вставьте следующий код XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:drawable="@drawable/android_pressed"
          android:state_pressed="true" />
    <item android:drawable="@drawable/android_focused"
          android:state_focused="true" />
    <item android:drawable="@drawable/android_normal" />
</selector>
```

Определяет один ресурс drawable, которая изменит своего изображения на основании текущего состояния кнопки. Первый `<item>` определяет **android_pressed.png** как изображение при нажатии кнопки (активации); второй `<item>` определяет **android_focused.png** изображения при Кнопка с фокусом ввода (когда кнопки выделяется с помощью трекбол или Крестовина); а третий `<item>` определяет **android_normal.png** как изображение для его обычном состоянии (при нажатии, ни с фокусом ввода). Этот XML-файл теперь представляет один ресурс drawable и когда ссылка на [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) для своего фона изображения, отображаемого будет меняться в зависимости от этих трех состояний.


> [!NOTE]
> Порядок `<item>` важные элементы. При этом drawable есть ссылка, `<item>`являются обхода для определения, какой из них подходит для текущего состояния кнопки.
> Так как «обычная работа» образа последней, они не применяется, если условия `android:state_pressed` и `android:state_focused` равны false.

Откройте **Resources/layout/Main.axml** файл и добавьте [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) элемент:

```xml
<Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="10dp"
        android:background="@drawable/android_button" />
```

`android:background` Атрибут задает drawable ресурса для фона кнопки (который, при сохранении в **Resources/drawable/android.xml**, указанная как `@drawable/android`). Этот параметр заменяет обычный фоновое изображение, используемое для кнопок во всей системе. Чтобы drawable изменить ее образ на основе состояния кнопки образ должен применяться в фоновом режиме.

Чтобы сделать что-то при нажатии кнопки, добавьте следующий код в конце [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle/)
метод:

```csharp
Button button = FindViewById<Button>(Resource.Id.button);

button.Click += (o, e) => {
    Toast.MakeText (this, "Beep Boop", ToastLength.Short).Show ();
};
```

Здесь фиксируется [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) из макета, затем добавляет [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) сообщение, отображаемое при [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) нажатии.

Теперь запустите приложение.


*Некоторые части этой страницы, изменения с учетом работы создана и совместно используется Android откройте исходный проект и используются в соответствии с условиями, описанными в*
[*лицензии Creative Commons 2.5 Attribution* ](http://creativecommons.org/licenses/by/2.5/).
