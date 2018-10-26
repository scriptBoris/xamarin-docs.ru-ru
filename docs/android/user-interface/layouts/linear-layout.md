---
title: LinearLayout
ms.prod: xamarin
ms.assetid: B49D129C-AF24-3C5A-C833-5A34AFBB2442
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/07/2018
ms.openlocfilehash: d8ee5f867c9a4d724c54c14b5afdd005f1b805d3
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115774"
---
# <a name="linearlayout"></a>LinearLayout

[`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) — [`ViewGroup`](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)
отображающий дочерний [`View`](https://developer.xamarin.com/api/type/Android.Views.View/)
элементы в линейной направлении, вертикально или горизонтально.

Следует соблюдать осторожность, об использовании чрезмерно [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/).
Если вы начинаете вложенности несколько [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)s, вы можете рассмотреть возможность использования [`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)
Вместо этого.

Создайте новый проект с именем **HelloLinearLayout**.

Откройте **Resources/Layout/Main.axml** и вставьте следующий текст:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation=    "vertical"
    android:layout_width=    "match_parent"
    android:layout_height=    "match_parent"    >

  <LinearLayout
      android:orientation=    "horizontal"
      android:layout_width=    "match_parent"
      android:layout_height=    "match_parent"
      android:layout_weight=    "1"    >
      <TextView
          android:text=    "red"
          android:gravity=    "center_horizontal"
          android:background=    "#aa0000"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "green"
          android:gravity=    "center_horizontal"
          android:background=    "#00aa00"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "blue"
          android:gravity=    "center_horizontal"
          android:background=    "#0000aa"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "yellow"
          android:gravity=    "center_horizontal"
          android:background=    "#aaaa00"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
  </LinearLayout>
        
  <LinearLayout
    android:orientation=    "vertical"
    android:layout_width=    "match_parent"
    android:layout_height=    "match_parent"
    android:layout_weight=    "1"    >
    <TextView
        android:text=    "row one"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row two"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row three"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row four"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
       android:layout_weight=    "1"    />
  </LinearLayout>

</LinearLayout>
```

Тщательно изучите этот XML-документ. Нет корня [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)
определяющий его ориентацию на вертикальную &ndash; все дочерние [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)s (от которого он имеет два) будут располагаться друг над другом вертикально. Первый дочерний элемент является другой [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)
использующий горизонтальной ориентации и второй дочерний элемент является [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)
использующий вертикальную ориентацию. Каждый из этих вложенных [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)s содержит несколько [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/)
элементы, которые ориентируются друг с другом так, как определяется родительских [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/).

Теперь откройте **HelloLinearLayout.cs** и убедитесь, что он загружает **Resources/Layout/Main.axml** макета в [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)
метод:

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

[ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/(System.Int32)) Метод загружает файл макета для [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/), указанного по Идентификатору ресурса &ndash; `Resources.Layout.Main` ссылается на **ресурсы/макета / Main.AXML** файл макета.

Запустите приложение. Вы должны увидеть следующее:

[![Снимок экрана приложения, первый LinearLayout располагаются горизонтально, второй по вертикали](linear-layout-images/helloviews1.png)](linear-layout-images/helloviews1.png#lightbox)

Обратите внимание на то, как определить поведение каждого представления в XML-атрибуты. Поэкспериментируйте с различными значениями для `android:layout_weight` чтобы увидеть распределение площади экрана на основе веса каждого элемента. См. в разделе [общих объектов макета](http://developer.android.com/guide/topics/ui/declaring-layout.html) документа для получения дополнительных сведений о том, как [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)
дескрипторы `android:layout_weight` атрибута.


## <a name="references"></a>Ссылки

-   [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) 
-   [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/) 

*Некоторые части этой страницы, изменения с учетом работы создана и совместно используется Android откройте исходный проект и используются в соответствии с условиями, описанными в*
[*лицензии Creative Commons 2.5 Attribution* ](http://creativecommons.org/licenses/by/2.5/).

