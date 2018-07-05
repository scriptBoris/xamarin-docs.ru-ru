---
title: Параметр
description: Как использовать мини-приложение Switch в приложениях Xamarin.Android
ms.prod: xamarin
ms.assetid: 6E1F3324-EC41-454A-AEC0-0208813C7E50
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/29/2018
ms.openlocfilehash: e3bcce48a675a9ba3d1d41f93babc7fcb26448c8
ms.sourcegitcommit: 081a2d094774c6f75437d28b71d22607e33aae71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37403280"
---
# <a name="switch"></a>Параметр

`Switch` Мини-приложения (см. ниже) позволяет пользователю переключаться между двумя состояниями, как в или ВЫКЛЮЧАЕТСЯ. `Switch` Значение по умолчанию — OFF. Мини-приложения приведен ниже в ON и OFF состояния.

[![Снимки экрана коммутатора мини-приложение в состояниях и ВЫКЛЮЧЕНИЕ](switch-images/16-switch-onoff.png)](switch-images/16-switch-onoff.png#lightbox)


## <a name="creating-a-switch"></a>Создания коммутатора

Чтобы создать параметр, просто объявите `Switch` элемента в XML следующим образом:

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
```

Это создает базовый переключатель, как показано ниже:

[![Снимок экрана демонстрационное приложение, отображение переключателя в состоянии OFF](switch-images/07-switch.png)](switch-images/07-switch.png#lightbox)


## <a name="changing-default-values"></a>Изменение значений по умолчанию

Текст, который отображает элемент управления для состояния ON и OFF и значение по умолчанию можно настроить. Например, чтобы сделать параметр по умолчанию в значение ON и чтение нет/Да вместо OFF или ON, можно установить `checked`, `textOn`, и `textOff` атрибуты в следующий код XML.

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```



## <a name="providing-a-title"></a>Указав заголовок

`Switch` Мини-приложение также поддерживает в том числе текстовую метку, задав `text` следующим образом:

```xml
<Switch android:text="Is Xamarin.Android great?"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```

Эта разметка создает следующий снимок экрана во время выполнения:

[![Снимок экрана демонстрационное приложение с текстом, горизонтально перед коммутатора мини-приложения](switch-images/08-switch.png)](switch-images/08-switch.png#lightbox)

Когда `Switch`его значение изменяется, он выдает `CheckedChange` событий.
Например, в следующем коде мы записи этого события и представлять `Toast` мини-приложения с сообщением, основываясь на `isChecked` значение `Switch`, передаваемый в обработчик событий, как часть `CompoundButton.CheckedChangeEventArg` аргумент.

```csharp
Switch s = FindViewById<Switch> (Resource.Id.monitored_switch);
           
s.CheckedChange += delegate(object sender, CompoundButton.CheckedChangeEventArgs e) {
    var toast = Toast.MakeText (this, "Your answer is " +
        (e.IsChecked ?  "correct" : "incorrect"), ToastLength.Short);
    toast.Show ();
};
```


## <a name="related-links"></a>Связанные ссылки

- [SwitchDemo (пример)](https://developer.xamarin.com/samples/monodroid/SwitchDemo/)
- [Учебник по макету вкладку](~/android/user-interface/layouts/tab-layout/index.md)
