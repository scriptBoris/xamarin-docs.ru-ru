---
title: GridView
ms.prod: xamarin
ms.assetid: 6992C4FF-ECBB-3493-AEE6-3E063C1A8C54
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 82c82de912fd253d45e6343e2dd1c50e389c6371
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30766303"
---
# <a name="gridview"></a>GridView

[`GridView`](https://developer.xamarin.com/api/type/Android.Widget.GridView/) — [ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/) , отображение элементов в двухмерной, прокручиваемой сетке. Элементов сетки автоматически добавляются в макет с помощью [ `ListAdapter` ](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/).

В этом учебнике вы создадите сетку эскизы изображений. При выборе элемента появляется всплывающее сообщение положение изображения.

Создание нового проекта с именем **HelloGridView**.

Найти некоторые фотографии, вы хотите использовать, или [загрузки этих образцов изображений](http://developer.android.com/shareables/sample_images.zip). Добавление файлов изображений в проект **ресурсы и Drawable** каталога. В **свойства** задайте действие при построении каждого из **AndroidResource**.

Откройте **Resources/Layout/Main.axml** файл и вставьте следующий текст:

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridView xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/gridview"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:columnWidth="90dp"
    android:numColumns="auto_fit"
    android:verticalSpacing="10dp"
    android:horizontalSpacing="10dp"
    android:stretchMode="columnWidth"
    android:gravity="center"
/>
```

Это [ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/) заполнит весь экран. Атрибуты имеют пояснительный вместо самостоятельно. Дополнительные сведения о допустимых атрибутов см. в разделе [ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/) ссылки.

Откройте `HelloGridView.cs` и вставьте следующий код для [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) метод:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);

    var gridview = FindViewById<GridView> (Resource.Id.gridview);
    gridview.Adapter = new ImageAdapter (this);

    gridview.ItemClick += delegate (object sender, AdapterView.ItemClickEventArgs args) {
        Toast.MakeText (this, args.Position.ToString (), ToastLength.Short).Show ();
    };
}
```

После **Main.axml** макета имеет значение для представления содержимого, [ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/) захвачен из макета с [ `FindViewById` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/). [ `Adapter` ](https://developer.xamarin.com/api/property/Android.Widget.AdapterView.RawAdapter/) Свойство затем используется для задания настраиваемого адаптера (`ImageAdapter`) в качестве источника для всех элементов, отображаемых в сетке. `ImageAdapter` Создается в следующем шаге.

Чтобы сделать что-нибудь при нажатии элемента в сетке, подписана анонимного делегата [ `ItemClick` ](https://developer.xamarin.com/api/event/Android.Widget.AdapterView.ItemClick/) событий.
Он показывает [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) , отображающий индекс (отсчитываемый от нуля) выбранного элемента (в реальной ситуации, позиция может использоваться для получения полного размера образа другая задача). Обратите внимание, что стиль Java прослушивателя классы можно использовать вместо событий .NET.

Создайте новый класс с именем `ImageAdapter` который наследуется от класса [ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/):

```csharp
public class ImageAdapter : BaseAdapter
{
    Context context;

    public ImageAdapter (Context c)
    {
        context = c;
    }

    public override int Count {
        get { return thumbIds.Length; }
    }

    public override Java.Lang.Object GetItem (int position)
    {
        return null;
    }

    public override long GetItemId (int position)
    {
        return 0;
    }

    // create a new ImageView for each item referenced by the Adapter
    public override View GetView (int position, View convertView, ViewGroup parent)
    {
        ImageView imageView;

        if (convertView == null) {  // if it's not recycled, initialize some attributes
            imageView = new ImageView (context);
            imageView.LayoutParameters = new GridView.LayoutParams (85, 85);
            imageView.SetScaleType (ImageView.ScaleType.CenterCrop);
            imageView.SetPadding (8, 8, 8, 8);
        } else {
            imageView = (ImageView)convertView;
        }

        imageView.SetImageResource (thumbIds[position]);
        return imageView;
    }

    // references to our images
    int[] thumbIds = {
        Resource.Drawable.sample_2, Resource.Drawable.sample_3,
        Resource.Drawable.sample_4, Resource.Drawable.sample_5,
        Resource.Drawable.sample_6, Resource.Drawable.sample_7,
        Resource.Drawable.sample_0, Resource.Drawable.sample_1,
        Resource.Drawable.sample_2, Resource.Drawable.sample_3,
        Resource.Drawable.sample_4, Resource.Drawable.sample_5,
        Resource.Drawable.sample_6, Resource.Drawable.sample_7,
        Resource.Drawable.sample_0, Resource.Drawable.sample_1,
        Resource.Drawable.sample_2, Resource.Drawable.sample_3,
        Resource.Drawable.sample_4, Resource.Drawable.sample_5,
        Resource.Drawable.sample_6, Resource.Drawable.sample_7
    };
}
```

Во-первых, это реализует некоторые необходимые методы, унаследованные от [ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/). Конструктор и [ `Count` ](https://developer.xamarin.com/api/property/Android.Widget.BaseAdapter.Count/) свойство говорят сами за себя. Как правило [ `GetItem(int)` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItem/) должен возвращать фактического объекта в указанной позиции в адаптере, но он учитывается в данном примере. Аналогичным образом [ `GetItemId(int)` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItemId/) должен возвращать идентификатор строки элемента, но здесь не требуется.

Первый метод необходимые является [ `GetView()` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetView/).
Этот метод создает новый [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) для каждого добавляемого образа `ImageAdapter`. При вызове [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) передается в, который обычно является объектом перезапущен (по крайней мере после этого вызова один раз), поэтому производится проверка, является ли объект значение null. Если он *—* имеет значение null, [ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/) создается и настроены необходимые свойства изображения представления:

- [`LayoutParams`](https://developer.xamarin.com/api/property/Android.Views.View.LayoutParameters/) Задает высоту и ширину для представления&mdash;это гарантирует, что независимо от размера drawable, каждого изображения является размер и обрезано в этих измерений, соответствующим образом.

- [`SetScaleType()`](https://developer.xamarin.com/api/member/Android.Widget.ImageView.SetScaleType/) Объявляет, что изображения следует обрезаны к центру (при необходимости).

- [`SetPadding(int, int, int, int)`](https://developer.xamarin.com/api/member/Android.Views.View.SetPadding/) Определяет заполнение для всех сторон. (Обратите внимание, что если изображения имеют разные пропорции, затем меньше заполнения приводит дополнительные кадрирования изображения, если она не совпадает с размерами, заданными для ImageView).

Если [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) передаваемый [ `GetView()` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetView/) — *не* значение null, то локальная [ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/) инициализируется с повторно [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) объекта.

В конце [ `GetView()` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetView/) метода `position` целое число, передаваемое в метод используется для выбора образа с `thumbIds` массив, который задан в качестве ресурса изображения для [ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/).

Осталось состоит в определении `thumbIds` массива drawable ресурсов.

Запустите приложение. Макет сетки должен выглядеть примерно следующим образом:

[![Снимок экрана: пример GridView, отображение рисунков 15](grid-view-images/helloviews4.png)](grid-view-images/helloviews4.png#lightbox)

Поэкспериментировать с поведение [ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/) и [ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/) элементы, перемещая их свойства. Например, вместо использования [ `LayoutParams` ](https://developer.xamarin.com/api/property/Android.Views.View.LayoutParameters/) попробуйте использовать [ `SetAdjustViewBounds()` ](https://developer.xamarin.com/api/member/Android.Widget.ImageView.SetAdjustViewBounds/).


## <a name="references"></a>Ссылки

-   [`GridView`](https://developer.xamarin.com/api/type/Android.Widget.GridView/) 
-   [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
-   [`BaseAdapter`](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/).

*Некоторые части этой страницы, изменения на основе работы создан и совместно используются Android открыть исходный проект и используются в соответствии с условиями, описанной в*
[*Creative Commons 2.5 однозначного соответствия лицензий* ](http://creativecommons.org/licenses/by/2.5/).
