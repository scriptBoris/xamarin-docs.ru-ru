---
title: Коллекции
ms.prod: xamarin
ms.assetid: 3112E68A-7853-B147-90A6-6295CA2C4CB5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: 54c9e4fad71d74fe40fc119592a45f6c94b47056
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122261"
---
# <a name="gallery"></a>Коллекции

[`Gallery`](https://developer.xamarin.com/api/type/Android.Widget.Gallery/) — Это виджет макет, используемый для отображения элементов в виде горизонтальной прокруткой списка и устанавливает текущее выделение в центре представления.

> [!IMPORTANT]
> Это мини-приложение был объявлен устаревшим в Android 4.1 (уровень API 16). 

В этом руководстве можно создать коллекцию фотографий и затем отображения всплывающее сообщение каждый раз при выборе элемента галереи.

После `Main.axml` макета имеет значение для представления содержимого, `Gallery` фиксируется в макете с [ `FindViewById` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/p/System.Int32/).
В [`Adapter`](https://developer.xamarin.com/api/property/Android.Widget.AdapterView.RawAdapter/)
свойство затем используется для задания пользовательского адаптера ( `ImageAdapter`) как источник для всех элементов, отображаемых в dallery. `ImageAdapter` Создается в следующем шаге.

Чтобы сделать что-то, при щелчке элемента в коллекции, анонимный делегат является подписанным на [`ItemClick`](https://developer.xamarin.com/api/event/Android.Widget.AdapterView.ItemClick/)
. Он показывает [`Toast`](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
отображающий индекс (отсчитываемый от нуля) элемента theselected (в реальной ситуации, позиция может использоваться для получения полного размера образа для некоторой другой задачи).

Во-первых, есть несколько переменных-членов, включая массив идентификаторов, которые ссылаются на изображения, сохраненные в каталоге прорисовываемые ресурсы (**ресурсы/drawable**).

Далее идет конструктора класса, где [`Context`](https://developer.xamarin.com/api/type/Android.Content.Context/)
для `ImageAdapter` определяется и сохранен в локальном поле экземпляра.
Затем этот код реализует некоторые необходимые методы, унаследованные от [ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/).
Конструктор и [`Count`](https://developer.xamarin.com/api/property/Android.Widget.BaseAdapter.Count/)
Свойство говорят сами за себя. Как правило, [`GetItem(int)`](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItem/p/System.Int32/)
Вы получите реальный объект в указанной позиции в адаптере, но он учитывается в этом примере. Аналогичным образом, [`GetItemId(int)`](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItemId/p/System.Int32/)
должна возвращать идентификатор строки элемента, но он здесь не требуется.

Метод работает для применения к изображению [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
который будет внедрен в [`Gallery`](https://developer.xamarin.com/api/type/Android.Widget.Gallery/)
В этом методе, элемент [`Context`](https://developer.xamarin.com/api/type/Android.Content.Context/)
используется для создания нового [ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/).
В [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
подготавливается, применяя изображения из локального массива прорисовываемые ресурсы, параметр [`Gallery.LayoutParams`](https://developer.xamarin.com/api/type/Android.Widget.Gallery+LayoutParams/)
высоту и ширину изображения и настроив масштаб по размеру [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
измерения, а затем установить наконец знаниями по использованию чистых атрибут, полученный в конструкторе.

См. в разделе [ `ImageView.ScaleType` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView+ScaleType/) для другого изображения, параметры масштабирования.

## <a name="walkthrough"></a>Пошаговое руководство

Создайте новый проект с именем *HelloGallery*.

[![Снимок экрана нового проекта Android, в диалоговом окне нового решения](gallery-images/hellogallery1-sml.png)](gallery-images/hellogallery1.png#lightbox)

Найти некоторые фотографии, вы бы хотели использовать, или [скачайте эти примеры изображений](http://developer.android.com/shareables/sample_images.zip).
Добавление файлов изображений в проект **ресурсы/Drawable** каталога. В **свойства** окна, задайте действие при построении каждого из них на **AndroidResource**.

Откройте **Resources/Layout/Main.axml** и вставьте следующий текст:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Gallery xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/gallery"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
/>
```

Откройте `MainActivity.cs` и вставьте следующий код для [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)
метод:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "main" layout resource
    SetContentView (Resource.Layout.Main);

    Gallery gallery = (Gallery) FindViewById<Gallery>(Resource.Id.gallery);

    gallery.Adapter = new ImageAdapter (this);

    gallery.ItemClick += delegate (object sender, Android.Widget.AdapterView.ItemClickEventArgs args) {
        Toast.MakeText (this, args.Position.ToString (), ToastLength.Short).Show ();
    };
}
```

Создайте новый класс с именем `ImageAdapter` подклассами [ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/):

```csharp
public class ImageAdapter : BaseAdapter
{
    Context context;

    public ImageAdapter (Context c)
    {
          context = c;
    }

    public override int Count { get { return thumbIds.Length; } }

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
          ImageView i = new ImageView (context);

          i.SetImageResource (thumbIds[position]);
          i.LayoutParameters = new Gallery.LayoutParams (150, 100);
          i.SetScaleType (ImageView.ScaleType.FitXy);

          return i;
    }

    // references to our images
    int[] thumbIds = {
            Resource.Drawable.sample_1,
            Resource.Drawable.sample_2,
            Resource.Drawable.sample_3,
            Resource.Drawable.sample_4,
            Resource.Drawable.sample_5,
            Resource.Drawable.sample_6,
            Resource.Drawable.sample_7
     };
}

```

Запустите приложение. Он должен выглядеть как на снимке экрана ниже:

![Снимок экрана из HelloGallery отображение примеры изображений](gallery-images/hellogallery3.png)



## <a name="references"></a>Ссылки

-   [`BaseAdapter`](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/)
-   [`Gallery`](https://developer.xamarin.com/api/type/Android.Widget.Gallery/)
-   [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)

*Некоторые части этой страницы, изменения с учетом работы создана и совместно используется Android откройте исходный проект и используются в соответствии с условиями, описанными в*
[*лицензии Creative Commons 2.5 Attribution* ](http://creativecommons.org/licenses/by/2.5/).


