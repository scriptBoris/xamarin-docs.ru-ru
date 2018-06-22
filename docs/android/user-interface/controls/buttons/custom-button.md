---
title: Пользовательские кнопки
ms.prod: xamarin
ms.assetid: C523D41E-5855-248D-079D-6B12B74B7617
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: e6fc3fe4c3cb89d74188557615f58cc8e34f5991
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30766573"
---
# <a name="custom-button"></a>Пользовательские кнопки

В этом разделе вы создадите кнопки с помощью пользовательского изображения вместо текста, с помощью [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) мини-приложения и XML-файл, определяющий три различные изображения для различные состояния кнопок. При нажатии этой кнопки отображается SMS-сообщений.

Щелкните правой кнопкой мыши и загрузить три ниже изображения, а затем скопируйте их в **ресурсы и drawable** каталога проекта. Они будут использоваться для различные состояния кнопок.

 [![Android зеленый значок нормальное состояние](custom-button-images/android-normal.png)](custom-button-images/android-normal.png#lightbox) [ ![Android оранжевый значок конкретное состояние](custom-button-images/android-focused.png)](custom-button-images/android-focused.png#lightbox) [ ![Android желтый значок нажатом состоянии](custom-button-images/android-pressed.png)](custom-button-images/android-pressed.png#lightbox)

Создайте новый файл в **ресурсы или drawable** каталог с именем **android_button.xml**. Вставьте следующий XML-код:

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

Это определяет одного drawable ресурса, который приведет к изменению его изображение на основании текущего состояния кнопки. Первый `<item>` определяет **android_pressed.png** как изображение, при нажатии кнопки (она была активирована); второй `<item>` определяет **android_focused.png** в изображении при Кнопка с фокусом ввода (при выделении кнопки с помощью трекболом или навигационную клавишу); а в третьем `<item>` определяет **android_normal.png** как изображение для обычном состоянии (при нажатии ни с фокусом ввода). Этот XML-файл теперь представляет drawable одного ресурса и при ссылается [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) для своего фона, изображение, отображаемое будет изменяться в зависимости на трех состояний.


> [!NOTE]
> Порядок `<item>` важные элементы. При этом drawable есть ссылка, `<item>`являются обхода в порядке, чтобы определить, какой из них лучше всего подходит для текущего состояния кнопки.
> Так как «normal» образа последней, является ли он применяется, если условия `android:state_pressed` и `android:state_focused` равны false.

Откройте **Resources/layout/Main.axml** и добавьте [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) элемента:

```xml
<Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="10dp"
        android:background="@drawable/android_button" />
```

`android:background` Атрибут задает drawable ресурса для использования в качестве фона кнопки (который, при сохранении в **Resources/drawable/android.xml**, упоминается как `@drawable/android`). Эта процедура заменяет обычный фонового изображения, используемые для отображения кнопок во всей системе. Чтобы drawable изменить его изображение на основании состояния кнопки образ должен применяться в фоновом режиме.

Чтобы сделать что-нибудь при нажатии кнопки, добавьте следующий код в конце [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle/) метод:

```csharp
Button button = FindViewById<Button>(Resource.Id.button);

button.Click += (o, e) => {
    Toast.MakeText (this, "Beep Boop", ToastLength.Short).Show ();
};
```

Здесь фиксируется [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) из макета, добавляет [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) сообщение, отображаемое при [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) нажата.

Теперь запустите приложение.


*Некоторые части этой страницы, изменения на основе работы создан и совместно используются Android открыть исходный проект и используются в соответствии с условиями, описанной в*
[*Creative Commons 2.5 однозначного соответствия лицензий* ](http://creativecommons.org/licenses/by/2.5/).
