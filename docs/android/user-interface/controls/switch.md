---
title: Параметр
ms.prod: xamarin
ms.assetid: 6E1F3324-EC41-454A-AEC0-0208813C7E50
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/05/2018
ms.openlocfilehash: 0f4bfc3646f1ccd956ee8151468b3de20f6e1e2b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30762663"
---
# <a name="switch"></a>Параметр

`Switch` Мини-приложение (показано ниже) позволяет пользователю переключаться между двумя состояниями, как в или OFF. `Switch` Значение по умолчанию — OFF. Ниже приводится мини-приложения в его ON и OFF состояний.

[![Снимки экрана для коммутатора мини-приложение в состояниях и ВЫКЛЮЧЕНИЕ](switch-images/16-switch-onoff.png)](switch-images/16-switch-onoff.png#lightbox)


## <a name="creating-a-switch"></a>Создание к коммутатору

Чтобы создать параметр, объявите `Switch` элемента в XML следующим образом:

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
```

Это создает базовый коммутатор, как показано ниже:

[![Снимок экрана: отображение переключателя в состояние OFF нашего примера приложения](switch-images/07-switch.png)](switch-images/07-switch.png#lightbox)


## <a name="changing-default-values"></a>Изменение значений по умолчанию

Текст, который отображается элемент управления для состояния ON и OFF и значение по умолчанию являются настраиваемыми. Например, чтобы сделать коммутатор по умолчанию ON и чтение вместо Вкл. / Выкл нет/Да, можно установить `checked`, `textOn`, и `textOff` атрибуты в следующем XML-КОДЕ.

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```



## <a name="providing-a-title"></a>Предоставление заголовок

`Switch` Мини-приложение также поддерживает, включая текстовая метка, задав `text` атрибута следующим образом:

```xml
<Switch android:text="Is Xamarin.Android great?"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```

Эта разметка создает следующий снимок экрана во время выполнения:

[![Снимок экрана нашего примера приложения с текстом по горизонтали предшествующий коммутатора мини-приложения](switch-images/08-switch.png)](switch-images/08-switch.png#lightbox)

Когда `Switch`его значение изменяется, он выдает `CheckedChange` событий.
Например, в следующем коде мы записи этого события и представлять `Toast` мини-приложения с сообщением, основываясь на `isChecked` значение `Switch`, который передается в обработчик событий как часть `CompoundButton.CheckedChangeEventArg` аргумент.

```csharp
Switch s = FindViewById<Switch> (Resource.Id.monitored_switch);
           
s.CheckedChange += delegate(object sender, CompoundButton.CheckedChangeEventArgs e) {
    var toast = Toast.MakeText (this, "Your answer is " +
        (e.IsChecked ?  "correct" : "incorrect"), ToastLength.Short);
    toast.Show ();
};
```


## <a name="related-links"></a>Связанные ссылки

- [SwitchDemo (пример)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ICS_Samples/SwitchDemo/)
- [Учебник по макету вкладки](~/android/user-interface/layouts/tab-layout/index.md)
- [Знакомство с приложением Сандвичевы мороженого](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 платформы](http://developer.android.com/sdk/android-4.0.html)
