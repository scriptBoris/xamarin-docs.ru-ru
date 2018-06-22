---
title: Коллекции
ms.prod: xamarin
ms.assetid: 3112E68A-7853-B147-90A6-6295CA2C4CB5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/15/2018
ms.openlocfilehash: cb05dfb79c00e9c6f9fa1a8d80627abdb911c36e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30765653"
---
# <a name="gallery"></a>Коллекции

[`Gallery`](https://developer.xamarin.com/api/type/Android.Widget.Gallery/) является макет мини-приложение используется для отображения элементов в списке горизонтальной прокруткой и помещает текущее выделение в центре представления.

> [!IMPORTANT]
> Это мини-приложение был объявлен устаревшим в Android 4.1 (уровень API 16). 

В этом учебнике предстоит создать фотогалереи и отобразите всплывающее сообщение при каждом выборе элемента галереи.

После `Main.axml` макета имеет значение для представления содержимого, `Gallery` захвачен из макета с [ `FindViewById` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/p/System.Int32/).
[ `Adapter` ](https://developer.xamarin.com/api/property/Android.Widget.AdapterView.RawAdapter/) Свойство затем используется для задания настраиваемого адаптера ( `ImageAdapter`) в качестве источника для всех элементов, отображаемых в dallery. `ImageAdapter` Создается в следующем шаге.

Чтобы сделать что-нибудь при нажатии элемента в коллекции, подписана анонимного делегата [ `ItemClick` ](https://developer.xamarin.com/api/event/Android.Widget.AdapterView.ItemClick/) событий. Он показывает [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) , отображающий индекс (отсчитываемый от нуля) элемента theselected (в реальной ситуации, позиция может использоваться для получения полного размера образа другая задача).

Во-первых, есть несколько переменных-членов, включая массив идентификаторов, которые ссылаются на изображения, сохраненные в каталоге ресурсов drawable (**ресурсы или drawable**).

Далее следует конструктора класса, где [ `Context` ](https://developer.xamarin.com/api/type/Android.Content.Context/) для `ImageAdapter` определяется и сохраняется в локальном поля экземпляра.
Затем это реализует некоторые необходимые методы, унаследованные от [ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/).
Конструктор и [ `Count` ](https://developer.xamarin.com/api/property/Android.Widget.BaseAdapter.Count/) свойство говорят сами за себя. Как правило [ `GetItem(int)` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItem/p/System.Int32/) должен возвращать фактического объекта в указанной позиции в адаптере, но он учитывается в данном примере. Аналогичным образом [ `GetItemId(int)` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItemId/p/System.Int32/) должен возвращать идентификатор строки элемента, но здесь не требуется.

Метод выполняет работу, чтобы применить к изображению [ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/) , будут внедрены в [ `Gallery` ](https://developer.xamarin.com/api/type/Android.Widget.Gallery/) в этом методе члена [ `Context` ](https://developer.xamarin.com/api/type/Android.Content.Context/) — используется для создания нового [ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/).
[ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/) Подготовил применение образа из локальный массив drawable ресурсы, задание [ `Gallery.LayoutParams` ](https://developer.xamarin.com/api/type/Android.Widget.Gallery+LayoutParams/) высоту и ширину изображения и настроив масштабировать по [ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/) измерений и наконец Задание фона использовать атрибут styleable, полученному в конструктор.

В разделе [ `ImageView.ScaleType` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView+ScaleType/) для другого изображения, параметры масштабирования.

## <a name="walkthrough"></a>Пошаговое руководство

Создание нового проекта с именем *HelloGallery*.

[![Снимок экрана нового проекта Android, в диалоговом окне нового решения](gallery-images/hellogallery1-sml.png)](gallery-images/hellogallery1.png#lightbox)

Найти некоторые фотографии, вы хотите использовать, или [загрузки этих образцов изображений](http://developer.android.com/shareables/sample_images.zip).
Добавление файлов изображений в проект **ресурсы и Drawable** каталога. В **свойства** задайте действие при построении каждого из **AndroidResource**.

Откройте **Resources/Layout/Main.axml** и вставьте следующий текст:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Gallery xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/gallery"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
/>
```

Откройте `MainActivity.cs` и вставьте следующий код для [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) метод:

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

Создайте новый класс с именем `ImageAdapter` который наследуется от класса [ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/):

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

Запустите приложение. Оно должно иметь вид на снимке экрана ниже:

![Снимок экрана из HelloGallery отображение образцов изображений](gallery-images/hellogallery3.png)



## <a name="references"></a>Ссылки

-   [`BaseAdapter`](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/)
-   [`Gallery`](https://developer.xamarin.com/api/type/Android.Widget.Gallery/)
-   [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)

*Некоторые части этой страницы, изменения на основе работы создан и совместно используются Android открыть исходный проект и используются в соответствии с условиями, описанной в*
[*Creative Commons 2.5 однозначного соответствия лицензий* ](http://creativecommons.org/licenses/by/2.5/).


