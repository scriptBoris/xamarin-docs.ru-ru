---
title: TableLayout
ms.prod: xamarin
ms.assetid: 0C7B9C95-5E5F-A069-BA37-984E49F7DCAD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: f9a77655091e4b552bd4a9d440f50b6a3cbeabcc
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763651"
---
# <a name="tablelayout"></a>TableLayout

[`TableLayout`](https://developer.xamarin.com/api/type/Android.Widget.TableLayout/) — [ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/) , отображающий дочерние [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) элементы в строках и столбцах.

Создание нового проекта с именем **HelloTableLayout**.

Откройте **Resources/Layout/Main.axml** файл и вставьте следующий текст:

```xml
<?xml version="1.0" encoding="utf-8"?>
<TableLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:stretchColumns="1">

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Open..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-O"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Save..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-S"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Save As..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-Shift-S"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <View
        android:layout_height="2dip"
        android:background="#FF909090"/>

    <TableRow>
        <TextView
            android:text="X"
            android:padding="3dip"/>
        <TextView
            android:text="Import..."
            android:padding="3dip"/>
    </TableRow>

    <TableRow>
        <TextView
            android:text="X"
            android:padding="3dip"/>
        <TextView
            android:text="Export..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-E"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <View
        android:layout_height="2dip"
        android:background="#FF909090"/>

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Quit"
            android:padding="3dip"/>
    </TableRow>
</TableLayout>
```

Обратите внимание на то, как это похож на структуру таблицы HTML. [ `TableLayout` ](https://developer.xamarin.com/api/type/Android.Widget.TableLayout/) Элемент аналогичен HTML `<table>` элемента; [ `TableRow` ](https://developer.xamarin.com/api/type/Android.Widget.TableRow/) подобно `<tr>` элементом; для ячеек, можно использовать любой тип, но [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) элемента. В этом примере [ `TextView` ](https://developer.xamarin.com/api/type/Android.Widget.TextView/) используется для каждой ячейки. Между некоторые из строк, также имеется базовый [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/), который используется для отрисовки горизонтальной линии.

Убедитесь, что ваш **HelloTableLayout** действие загружает этот макет в [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) метод:

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

[ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/(System.Int32)) Метод загружает файл макета для [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/), указанной по Идентификатору ресурса &mdash; `Resource.Layout.Main` ссылается на **ресурсы и макета или Main.AXML** файл макета.

Запустите приложение. Вы увидите следующее:

[![Снимок экрана примера приложения TableLayout отображение нескольких строк таблицы](table-layout-images/helloviews3.png)](table-layout-images/helloviews3.png#lightbox)



## <a name="references"></a>Ссылки

-   [`TableLayout`](https://developer.xamarin.com/api/type/Android.Widget.TableLayout/) 

-   [`TableRow`](https://developer.xamarin.com/api/type/Android.Widget.TableRow/) 

-   [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/) 

*Некоторые части этой страницы, изменения на основе работы создан и совместно используются Android открыть исходный проект и используются в соответствии с условиями, описанной в*
[*Creative Commons 2.5 однозначного соответствия лицензий* ](http://creativecommons.org/licenses/by/2.5/).
