---
title: GridView
ms.prod: xamarin
ms.assetid: 6992C4FF-ECBB-3493-AEE6-3E063C1A8C54
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 9ec27402aefd28ded4cf53e7e9fa52eedd2b86b6
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103015"
---
# <a name="gridview"></a>GridView

[`GridView`](https://developer.xamarin.com/api/type/Android.Widget.GridView/) — [`ViewGroup`](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)
отображающий элементы в двухмерной, прокручиваемой сетке. Элементов сетки автоматически вставляются в макет с помощью [ `ListAdapter` ](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/).

В этом руководстве вы создадите сетку эскизы изображений. При выборе элемента появляется всплывающее сообщение положение изображения.

Создайте новый проект с именем **HelloGridView**.

Найти некоторые фотографии, вы бы хотели использовать, или [скачайте эти примеры изображений](http://developer.android.com/shareables/sample_images.zip). Добавление файлов изображений в проект **ресурсы/Drawable** каталога. В **свойства** окна, задайте действие при построении каждого из них на **AndroidResource**.

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

Это [ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/) заполнит весь экран. Атрибуты, а собственный пояснительный. Дополнительные сведения о допустимых атрибутов, см. в разделе [ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/) ссылки.

Откройте `HelloGridView.cs` и вставьте следующий код для [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)
метод:

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

После **Main.axml** макета имеет значение для представления содержимого, [ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/) фиксируется в макете с [ `FindViewById` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/). В [`Adapter`](https://developer.xamarin.com/api/property/Android.Widget.AdapterView.RawAdapter/)
свойство затем используется для задания пользовательского адаптера (`ImageAdapter`) как источник для всех элементов, отображаемых в сетке. `ImageAdapter` Создается в следующем шаге.

Чтобы сделать что-то, при щелчке элемента в сетке, анонимный делегат является подписанным на [ `ItemClick` ](https://developer.xamarin.com/api/event/Android.Widget.AdapterView.ItemClick/) событий.
Он показывает [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) , отображающий индекс (отсчитываемый от нуля) выбранного элемента (в реальной ситуации, позиция может использоваться для получения полного размера образа для некоторой другой задачи). Обратите внимание на то, что классы прослушивателя в стиле Java можно использовать вместо событий .NET.

Создайте новый класс с именем `ImageAdapter` подклассами [ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/):

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

Во-первых, этот код реализует некоторые необходимые методы, унаследованные от [ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/). Конструктор и [ `Count` ](https://developer.xamarin.com/api/property/Android.Widget.BaseAdapter.Count/) свойство говорят сами за себя. Как правило, [`GetItem(int)`](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItem/)
Вы получите реальный объект в указанной позиции в адаптере, но он учитывается в этом примере. Аналогичным образом, [`GetItemId(int)`](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItemId/)
должна возвращать идентификатор строки элемента, но он здесь не требуется.

Первый метод необходимые [ `GetView()` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetView/).
Этот метод создает новую [`View`](https://developer.xamarin.com/api/type/Android.Views.View/)
для каждого изображения, добавляемого `ImageAdapter`. При вызове [`View`](https://developer.xamarin.com/api/type/Android.Views.View/)
передается в, которая представляет собой обычно перезапущен объекта (по крайней мере после этого вызова один раз), поэтому нет проверяет, является ли объект значение null. Если он *является* null, [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
определяется и настраивается с требуемыми свойствами для представления изображения:

- [`LayoutParams`](https://developer.xamarin.com/api/property/Android.Views.View.LayoutParameters/) Задает высоту и ширину для представления&mdash;это гарантирует, что, независимо от размера drawable, каждый образ и изменении размера обрезано этих измерений, соответствующим образом.

- [`SetScaleType()`](https://developer.xamarin.com/api/member/Android.Widget.ImageView.SetScaleType/) Объявляет, что образы должны обрезаны направлении (при необходимости).

- [`SetPadding(int, int, int, int)`](https://developer.xamarin.com/api/member/Android.Views.View.SetPadding/) Определяет заполнение для всех сторон. (Обратите внимание, что, если изображения имеют разные пропорции, затем меньше заполнения вызовет дополнительные Обрезка изображения, если он не совпадает с размерами, заданными для ImageView).

Если [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) передается [ `GetView()` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetView/) — *не* значение null, то локальная [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
инициализируется с перезапущен [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) объекта.

В конце [`GetView()`](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetView/)
метод, `position` целое число, переданное в метод используется для выбора образа из `thumbIds` массив, который задан в качестве ресурса изображения для [ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/).

Остается лишь для определения `thumbIds` массив прорисовываемые ресурсы.

Запустите приложение. Макет сетки должен выглядеть следующим образом:

[![Пример снимка экрана с GridView, отображение рисунков 15](grid-view-images/helloviews4.png)](grid-view-images/helloviews4.png#lightbox)

Поэкспериментируйте с поведение [ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/) и [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
элементы, перемещая их свойства. Например, вместо использования [ `LayoutParams` ](https://developer.xamarin.com/api/property/Android.Views.View.LayoutParameters/) попробуйте использовать [ `SetAdjustViewBounds()` ](https://developer.xamarin.com/api/member/Android.Widget.ImageView.SetAdjustViewBounds/).


## <a name="references"></a>Ссылки

-   [`GridView`](https://developer.xamarin.com/api/type/Android.Widget.GridView/) 
-   [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
-   [`BaseAdapter`](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/).

*Некоторые части этой страницы, изменения с учетом работы создана и совместно используется Android откройте исходный проект и используются в соответствии с условиями, описанными в*
[*лицензии Creative Commons 2.5 Attribution* ](http://creativecommons.org/licenses/by/2.5/).
