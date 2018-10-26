---
title: С помощью RelativeLayout в Xamarin.Android
description: Как использовать RelativeLayout в приложениях Xamarin.Android
ms.prod: xamarin
ms.assetid: AFD9C849-02C3-E728-BC78-77A563612BC5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/29/2018
ms.openlocfilehash: af2972ecc92435836a75013e6203ba47c2c04627
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113156"
---
# <a name="relativelayout"></a>RelativeLayout

[`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/) — [ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/) , отображающий дочерний [`View`](https://developer.xamarin.com/api/type/Android.Views.View/)
элементы в относительные позиции. Положение [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) может быть задан относительно одноуровневых элементов (например, относительно левой части или ниже данного элемента) или в помещает относительно [`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)
область (например, выровненный по нижнему краю слева центра).

Объект [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/) — очень мощная служебная программа, разработке пользовательского интерфейса, так как позволяет избавиться от вложенных [ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)s. Если вы с помощью нескольких вложенных [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)
группы, можно заменить их одним [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/).

Создайте новый проект с именем **HelloRelativeLayout**.

Откройте **Resources/Layout/Main.axml** файл и вставьте следующий текст:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <TextView
        android:id="@+id/label"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Type here:"/>
    <EditText
        android:id="@+id/entry"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="@android:drawable/editbox_background"
        android:layout_below="@id/label"/>
    <Button
        android:id="@+id/ok"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@id/entry"
        android:layout_alignParentRight="true"
        android:layout_marginLeft="10dip"
        android:text="OK" />
    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toLeftOf="@id/ok"
        android:layout_alignTop="@id/ok"
        android:text="Cancel" />
</RelativeLayout>
```

Обратите внимание, что каждый из `android:layout_*` атрибуты, такие как `layout_below`, `layout_alignParentRight`, и `layout_toLeftOf`.
При использовании [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/), эти атрибуты можно использовать для описания того, как вы хотите разместить каждый [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/). Каждый из этих атрибутов определяют различные виды относительное положение. Некоторые атрибуты использовать идентификатор ресурса того же уровня [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) определить свой собственный относительное положение. Например, последний [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) определен на более низких взятия слева и выравниваются с-top-of [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) с Идентификатором `ok` (который является предыдущей [`Button`](https://developer.xamarin.com/api/type/Android.Widget.Button/)).

Все доступные макета атрибуты определены в [ `RelativeLayout.LayoutParams` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout+LayoutParams/).

Убедитесь, что вы загружаете этот макет в [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)
метод:

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

[ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/p/System.Int32/) Метод загружает файл макета для [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/), указанного по Идентификатору ресурса &mdash; `Resource.Layout.Main` ссылается на **ресурсы/макета / Main.AXML** файл макета.

Запустите приложение. Вы должны увидеть следующий результат:

[![Снимок экрана относительный макета с использованием текстового представления, EditText и две кнопки](relative-layout-images/helloviews2.png)](relative-layout-images/helloviews2.png#lightbox)


## <a name="resources"></a>Ресурсы

-   [`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)
-   [`RelativeLayout.LayoutParams`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout+LayoutParams/)
-   [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/)
-   [`EditText`](https://developer.xamarin.com/api/type/Android.Widget.EditText/)
-   [`Button`](https://developer.xamarin.com/api/type/Android.Widget.Button/)


*Некоторые части этой страницы, изменения с учетом работы создана и совместно используется Android откройте исходный проект и используются в соответствии с условиями, описанными в*
[*лицензии Creative Commons 2.5 Attribution* ](http://creativecommons.org/licenses/by/2.5/).
