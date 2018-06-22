---
title: С помощью RelativeLayout в Xamarin.Android
ms.prod: xamarin
ms.assetid: AFD9C849-02C3-E728-BC78-77A563612BC5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/25/2018
ms.openlocfilehash: cd2d7537036978e30c97b5776155e429178b6dac
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32436002"
---
# <a name="relativelayout"></a>RelativeLayout

[`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/) — [ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/) , отображающий дочерние [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) элементов в относительные позиции. Положение [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) может быть задан относительно элементов того же уровня (например, относительно левой части или ниже данного элемента) или в помещает относительно [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/) области (например, Выравнивание по нижнему краю левый в центре).

Объект [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/) — это очень мощный программа, разработке пользовательского интерфейса, так как он может устранить вложенных [ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)s. Если вы с помощью нескольких вложенных [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) групп, можно заменить один [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/).

Создание нового проекта с именем **HelloRelativeLayout**.

Откройте **Resources/Layout/Main.axml** файл и вставьте следующий текст:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <TextView
        android:id="@+id/label"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="Type here:"/>
    <EditText
        android:id="@+id/entry"
        android:layout_width="fill_parent"
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

Обратите внимание на то, каждый из `android:layout_*` атрибуты, такие как `layout_below`, `layout_alignParentRight`, и `layout_toLeftOf`.
При использовании [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/), эти атрибуты можно использовать для описания того, как можно разместить каждый [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/). Каждый одного из этих атрибутов определяют различные виды относительное положение. Некоторые атрибуты использовать идентификатор ресурса одноуровневой [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) для определения собственного относительное положение. Например, последний [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) определено для размещения в левой части и выравниваются с сверху of [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) с Идентификатором `ok` (являющийся предыдущих [`Button`](https://developer.xamarin.com/api/type/Android.Widget.Button/)).

Все доступные макета атрибуты определены в [ `RelativeLayout.LayoutParams` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout+LayoutParams/).

Убедитесь, что загрузить этот макет в [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) метод:

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

[ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/p/System.Int32/) Метод загружает файл макета для [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/), указанной по Идентификатору ресурса &mdash; `Resource.Layout.Main` ссылается на **ресурсы и макета или Main.AXML** файл макета.

Запустите приложение. Должен появиться следующий результат:

[![Снимок экрана относительный макета с использованием текстового представления, EditText и две кнопки](relative-layout-images/helloviews2.png)](relative-layout-images/helloviews2.png#lightbox)


## <a name="resources"></a>Ресурсы

-   [`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)
-   [`RelativeLayout.LayoutParams`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout+LayoutParams/)
-   [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/)
-   [`EditText`](https://developer.xamarin.com/api/type/Android.Widget.EditText/)
-   [`Button`](https://developer.xamarin.com/api/type/Android.Widget.Button/)


*Некоторые части этой страницы, изменения на основе работы создан и совместно используются Android открыть исходный проект и используются в соответствии с условиями, описанной в*
[*Creative Commons 2.5 однозначного соответствия лицензий* ](http://creativecommons.org/licenses/by/2.5/).
