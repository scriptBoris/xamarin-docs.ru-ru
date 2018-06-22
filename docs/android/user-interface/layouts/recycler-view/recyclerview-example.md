---
title: Простой пример RecyclerView
ms.prod: xamarin
ms.assetid: A50520D2-1214-40E1-9B27-B0891FE11584
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: d5be838dcb5530ece76c3701d8fce10403622e8d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30770616"
---
# <a name="a-basic-recyclerview-example"></a>Простой пример RecyclerView


Чтобы понять, как `RecyclerView` работает в обычном приложении, в данном разделе рассматриваются [RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer/) пример приложения, простой пример кода, использующего `RecyclerView` для отображения больших Коллекция фотографий: 

[![Два снимка экрана RecyclerView приложение, которое использует CardViews для отображения фотографий](recyclerview-example-images/01-recyclerviewer-sml.png)](recyclerview-example-images/01-recyclerviewer.png#lightbox)

**RecyclerViewer** использует [CardView](~/android/user-interface/controls/card-view.md) для реализации каждого элемента фотографии в `RecyclerView` макета. Из-за `RecyclerView`его преимущество в производительности, этот образец приложения быстро прокручивать большая коллекция фотографий и без заметной задержки.


### <a name="an-example-data-source"></a>Пример источника данных

В этом примере приложении источник данных «фотоальбом» (представленного `PhotoAlbum` класс) предоставляет `RecyclerView` с содержимым элемента.
`PhotoAlbum` — Это коллекция фотографий с подписями; При инициализации, вы получаете готовых Коллекция фотографий 32:

```csharp
PhotoAlbum mPhotoAlbum = new PhotoAlbum ();
```

Каждый экземпляр фотографий в `PhotoAlbum` предоставляет свойства, которые позволяют читать его идентификатор ресурса изображения, `PhotoID`и его строка заголовка `Caption`. Коллекция фотографий упорядочен таким образом, что каждой фотографии может осуществляться индексатора. Например следующие строки кода доступа к идентификатор ресурса изображения и заголовок десятый фотографии в коллекции:

```csharp
int imageId = mPhotoAlbum[9].ImageId;
string caption = mPhotoAlbum[9].Caption;
```

`PhotoAlbum` также предоставляет `RandomSwap` метод, который можно использовать для замены первого фотографии в коллекции с фотографию случайно выбранных в другом месте в коллекции:

```csharp
mPhotoAlbum.RandomSwap ();
```

Так как сведения о реализации `PhotoAlbum` не являются значимыми для понимания `RecyclerView`, `PhotoAlbum` исходный код не представленных здесь. Исходный код для `PhotoAlbum` доступен на [PhotoAlbum.cs](https://github.com/xamarin/monodroid-samples/blob/master/android5.0/RecyclerViewer/RecyclerViewer/PhotoAlbum.cs) в [RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer/) пример приложения.


### <a name="layout-and-initialization"></a>Макет и инициализации

Файл макета **Main.axml**, состоит из одного `RecyclerView` в `LinearLayout`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <android.support.v7.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:scrollbars="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" />
</LinearLayout>
```

Обратите внимание, что необходимо использовать полное имя **android.support.v7.widget.RecyclerView** из-за `RecyclerView` упаковывается в библиотеке технической поддержки. `OnCreate` Метод `MainActivity` инициализирует этот макет, создает экземпляр адаптера и подготавливает базового источника данных:

```csharp
public class MainActivity : Activity
{
    RecyclerView mRecyclerView;
    RecyclerView.LayoutManager mLayoutManager;
    PhotoAlbumAdapter mAdapter;
    PhotoAlbum mPhotoAlbum;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Prepare the data source:
        mPhotoAlbum = new PhotoAlbum ();

        // Instantiate the adapter and pass in its data source:
        mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);

        // Set our view from the "main" layout resource:
        SetContentView (Resource.Layout.Main);

        // Get our RecyclerView layout:
        mRecyclerView = FindViewById<RecyclerView> (Resource.Id.recyclerView);

        // Plug the adapter into the RecyclerView:
        mRecyclerView.SetAdapter (mAdapter);
```

Этот код выполняет следующие задачи.

1. Создает экземпляр `PhotoAlbum` источника данных.

2. Передает источника данных альбома фотографий в конструктор адаптера `PhotoAlbumAdapter` (который определяется далее в этом руководстве). 
   Обратите внимание, что он считается наилучшим решением будет передать источника данных в качестве параметра в конструктор адаптера. 

3. Возвращает `RecyclerView` из макета.

4. Подключает адаптер в `RecyclerView` экземпляр путем вызова `RecyclerView` `SetAdapter` метода, как показано выше.

### <a name="layout-manager"></a>Диспетчер макета

Каждый элемент в `RecyclerView` состоит из `CardView` , содержащий изображение и заголовка фотографии (подробности описаны в [владелец представления](#view-holder) разделе ниже). О стандартных `LinearLayoutManager` используется для размещения каждого `CardView` в вертикальное расположение прокрутки:

```csharp
mLayoutManager = new LinearLayoutManager (this);
mRecyclerView.SetLayoutManager (mLayoutManager);
```

Этот код находится в основной деятельности `OnCreate` метод. Конструктор для диспетчера структуры требует *контекста*, поэтому `MainActivity` передается с помощью `this` см. выше.

Вместо использования predefind `LinearLayoutManager`, можно подключить диспетчер пользовательский макет, который отображает два `CardView` элементы side-by-side, реализация эффект анимации переворачивания для прохода по коллекции фотографий. Далее в этом руководстве вы увидите пример того, как изменить макет путем переключения в диспетчере другой макет.

<a name="view-holder" />

### <a name="view-holder"></a>Владелец представления

Класс владельца представления, называется `PhotoViewHolder`. Каждый `PhotoViewHolder` ссылается экземпляр `ImageView` и `TextView` элемента соответствующих строк, который располагается в `CardView` как здесь разработки:

[![Схема CardView, содержащий ImageView и TextView](recyclerview-example-images/02-cardview-layout-sml.png)](recyclerview-example-images/02-cardview-layout.png#lightbox)

`PhotoViewHolder` является производным от `RecyclerView.ViewHolder` и содержит свойства для хранения ссылки на `ImageView` и `TextView` показано в приведенном выше макета.
`PhotoViewHolder` состоит из двух свойств и один конструктор.

```csharp
public class PhotoViewHolder : RecyclerView.ViewHolder
{
    public ImageView Image { get; private set; }
    public TextView Caption { get; private set; }

    public PhotoViewHolder (View itemView) : base (itemView)
    {
        // Locate and cache view references:
        Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
        Caption = itemView.FindViewById<TextView> (Resource.Id.textView);
    }
}
```
В этом примере кода `PhotoViewHolder` конструктору передается ссылка на представление родительского элемента ( `CardView`), `PhotoViewHolder` заключает в оболочку. Обратите внимание, что вы всегда отправляет родительского элемента представления конструктора базового класса. `PhotoViewHolder` Вызовы конструктора `FindViewById` в представлении родительского элемента для найдите все ссылки на дочерние представления, `ImageView` и `TextView`, сохраняя результаты в `Image` и `Caption` свойства, соответственно. Адаптер позже извлекает Просмотр ссылок из этих свойств при обновлении это `CardView`его дочерние представления с новыми данными.

Дополнительные сведения о `RecyclerView.ViewHolder`, в разделе [ссылку на класс RecyclerView.ViewHolder](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html).


### <a name="adapter"></a>Адаптер

Адаптер загружает каждую `RecyclerView` строку с данными для определенного фотографий. Для данной фотографии в позиции строки *P*, например, адаптер находит связанные данные в позиции *P* в пределах источника данных и копирует эти данные в строку элемента в позиции *P* в `RecyclerView` коллекции. Адаптер использует владелец представления для поиска ссылок для `ImageView` и `TextView` в этой позиции, поэтому не требуется многократно вызывать `FindViewById` для тех представления пользователь выполняет прокрутку коллекции фотографии и повторно использует представления.

В **RecyclerViewer**, класс адаптера является производным от `RecyclerView.Adapter` для создания `PhotoAlbumAdapter`:

```csharp
public class PhotoAlbumAdapter : RecyclerView.Adapter
{
    public PhotoAlbum mPhotoAlbum;

    public PhotoAlbumAdapter (PhotoAlbum photoAlbum)
    {
        mPhotoAlbum = photoAlbum;
    }
    ...
}
```

`mPhotoAlbum` Член содержит источник данных, который передается в конструктор (фотоальбом); конструктор копирует фотоальбома в переменную-член. Следующие необходимые `RecyclerView.Adapter` реализованы методы:

-   **`OnCreateViewHolder`** &ndash; Создает экземпляр владелец файла и представление макета элемента.

-   **`OnBindViewHolder`** &ndash; Загружает данные в указанной позиции в представления, ссылки для которых хранятся в владелец данного представления.

-   **`ItemCount`** &ndash; Возвращает количество элементов в источнике данных.

Диспетчер макета вызывает эти методы, пока он размещение элементов в пределах `RecyclerView`. Реализация этих методов рассматриваются в следующих разделах.


#### <a name="oncreateviewholder"></a>OnCreateViewHolder

Диспетчер вызовов структуры `OnCreateViewHolder` при `RecyclerView` должен передана представление для представления элемента. `OnCreateViewHolder` увеличивает представление элементов из файла разметки представления и создает оболочку для представления в новом `PhotoViewHolder` экземпляра. `PhotoViewHolder` Конструктор находит и сохраняет ссылки на дочерние представления в макете, как описано ранее в [владелец представления](#view-holder).

Каждый элемент строки представляется `CardView` , содержащий `ImageView` (для фотографии) и `TextView` (для заголовка). Этот макет находится в файле **PhotoCardView.axml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:card_view="http://schemas.android.com/apk/res-auto"
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content">
    <android.support.v7.widget.CardView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        card_view:cardElevation="4dp"
        card_view:cardUseCompatPadding="true"
        card_view:cardCornerRadius="5dp">
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:padding="8dp">
            <ImageView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:id="@+id/imageView"
                android:scaleType="centerCrop" />
            <TextView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:textAppearance="?android:attr/textAppearanceMedium"
                android:textColor="#333333"
                android:text="Caption"
                android:id="@+id/textView"
                android:layout_gravity="center_horizontal"
                android:layout_marginLeft="4dp" />
        </LinearLayout>
    </android.support.v7.widget.CardView>
</FrameLayout>
```

Для этой структуры представляет элемент одной строки в `RecyclerView`. `OnBindViewHolder` (Как описано ниже) копирует данные из источника данных в `ImageView` и `TextView` этой структуры.
`OnCreateViewHolder` увеличивает этот макет для расположения фотографий в `RecyclerView` и создает новый `PhotoViewHolder` экземпляра (который находит и кэширует ссылки на `ImageView` и `TextView` дочерние представления в связанном `CardView` макета):

```csharp
public override RecyclerView.ViewHolder
    OnCreateViewHolder (ViewGroup parent, int viewType)
{
    // Inflate the CardView for the photo:
    View itemView = LayoutInflater.From (parent.Context).
                Inflate (Resource.Layout.PhotoCardView, parent, false);

    // Create a ViewHolder to hold view references inside the CardView:
    PhotoViewHolder vh = new PhotoViewHolder (itemView);
    return vh;
}

```

Полученный экземпляр держателя представление `vh`, возвращается обратно в вызывающий объект (диспетчера структуры).


#### <a name="onbindviewholder"></a>OnBindViewHolder

Когда диспетчер макета будет готов для отображения отдельного представления в `RecyclerView`в видимой области экрана, она вызывает адаптера `OnBindViewHolder` метод для заполнения элемента в позиции указанной строки с содержимым источника данных. `OnBindViewHolder` Возвращает сведения о фото для указанной строки позиции (ресурс изображения фотографии и в строке заголовка фотографии) и копирует эти данные в связанные с ними представления. Представления расположены посредством ссылки, хранимых в объекте владельца представления (который передается через `holder` параметра):

```csharp
public override void
    OnBindViewHolder (RecyclerView.ViewHolder holder, int position)
{
    PhotoViewHolder vh = holder as PhotoViewHolder;

    // Load the photo image resource from the photo album:
    vh.Image.SetImageResource (mPhotoAlbum[position].PhotoID);

    // Load the photo caption from the photo album:
    vh.Caption.Text = mPhotoAlbum[position].Caption;
}
```

Объект владельца представления переданное необходимо сначала привести в тип владельца производным представлением (в данном случае `PhotoViewHolder`) до его использования.
Адаптер загружает ресурс изображения в представлении ссылается владелец представления `Image` свойство и его копирует текст заголовка в представлении ссылается владелец представления `Caption` свойство. Это *привязывает* связанного представления с его данными.

Обратите внимание, что `OnBindViewHolder` приводится код, который работает непосредственно со структурой данных. В этом случае `OnBindViewHolder` понимает сопоставление `RecyclerView` элемент позиции его элементу связанные данные в источнике данных. Сопоставление проста в этом случае так, как позицию можно использовать в качестве индекса массива в фотоальбом; Однако более сложных источников данных может потребоваться дополнительный код, чтобы установить такое сопоставление.


#### <a name="itemcount"></a>ItemCount

`ItemCount` Метод возвращает количество элементов в коллекции данных. В пример приложения просмотра фотографий количество элементов — количество фотографии в фотоальбом:

```csharp
public override int ItemCount
{
    get { return mPhotoAlbum.NumPhotos; }
}
```

Дополнительные сведения о `RecyclerView.Adapter`, в разделе [ссылку на класс RecyclerView.Adapter](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html).


### <a name="putting-it-all-together"></a>Объединение

Итоговый `RecyclerView` реализации для примера приложения фото состоит из `MainActivity` код, который создает источник данных, диспетчера макета и адаптер. `MainActivity` Создает `mRecyclerView` экземпляра, создает экземпляр источника данных и адаптера и подключает диспетчер макета и адаптера:

```csharp
public class MainActivity : Activity
{
    RecyclerView mRecyclerView;
    RecyclerView.LayoutManager mLayoutManager;
    PhotoAlbumAdapter mAdapter;
    PhotoAlbum mPhotoAlbum;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        mPhotoAlbum = new PhotoAlbum();
        SetContentView (Resource.Layout.Main);
        mRecyclerView = FindViewById<RecyclerView> (Resource.Id.recyclerView);

        // Plug in the linear layout manager:
        mLayoutManager = new LinearLayoutManager (this);
        mRecyclerView.SetLayoutManager (mLayoutManager);

        // Plug in my adapter:
        mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);
        mRecyclerView.SetAdapter (mAdapter);
    }
}

```

`PhotoViewHolder` Находит и кэширует Просмотр ссылок:

```csharp
public class PhotoViewHolder : RecyclerView.ViewHolder
{
    public ImageView Image { get; private set; }
    public TextView Caption { get; private set; }

    public PhotoViewHolder (View itemView) : base (itemView)
    {
        // Locate and cache view references:
        Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
        Caption = itemView.FindViewById<TextView> (Resource.Id.textView);
    }
}
```

`PhotoAlbumAdapter` реализует три необходимый метод переопределения:

```csharp
public class PhotoAlbumAdapter : RecyclerView.Adapter
{
    public PhotoAlbum mPhotoAlbum;
    public PhotoAlbumAdapter (PhotoAlbum photoAlbum)
    {
        mPhotoAlbum = photoAlbum;
    }

    public override RecyclerView.ViewHolder
        OnCreateViewHolder (ViewGroup parent, int viewType)
    {
        View itemView = LayoutInflater.From (parent.Context).
                    Inflate (Resource.Layout.PhotoCardView, parent, false);
        PhotoViewHolder vh = new PhotoViewHolder (itemView);
        return vh;
    }

    public override void
        OnBindViewHolder (RecyclerView.ViewHolder holder, int position)
    {
        PhotoViewHolder vh = holder as PhotoViewHolder;
        vh.Image.SetImageResource (mPhotoAlbum[position].PhotoID);
        vh.Caption.Text = mPhotoAlbum[position].Caption;
    }

    public override int ItemCount
    {
        get { return mPhotoAlbum.NumPhotos; }
    }
}
```

При компиляции и запуска этого кода, он создает базовый фотографий, просмотре приложения, как показано на следующем снимке экрана:

[![Два снимка экрана приложения с вертикальной прокрутки фото карты просмотра фотографий](recyclerview-example-images/03-recyclerviewer-basic-sml.png)](recyclerview-example-images/03-recyclerviewer-basic.png#lightbox)

Это простое приложение поддерживает только просмотр фотоальбома. Он не отвечает на элемент нажатием события, а также обрабатывать изменения в базовых данных. Эта функция добавляется в [расширение в примере RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md).


### <a name="changing-the-layoutmanager"></a>Изменение LayoutManager

Из-за `RecyclerView`его гибкость, можно легко изменить приложения для использования диспетчера другой макет. В следующем примере он изменяется для отображения фотоальбом с макета сетки, который выполняет прокрутку по горизонтали, а не с Вертикальный линейный макет. Для этого экземпляра диспетчера макета перенастраивается на использование `GridLayoutManager` следующим образом:

```csharp
mLayoutManager = new GridLayoutManager(this, 2, GridLayoutManager.Horizontal, false);
```

Это изменение кода заменяет вертикальную `LinearLayoutManager` с `GridLayoutManager` представляет сетку, состоящую из двух строк, которые прокрутки по горизонтали. При компиляции и запуске приложения, вы увидите, что фотографии отображаются в сетке и что прокрутка является горизонтальной, а не вертикальной:

[![Снимок экрана примера приложения с горизонтальной прокрутки фотографии в сетке](recyclerview-example-images/04-gridlayoutmanager-sml.png)](recyclerview-example-images/04-gridlayoutmanager.png#lightbox)

Изменяя только одной строки кода, можно изменить просмотра фотографий приложению использовать структуру с другим поведением.
Обратите внимание, что код адаптера ни разметки XML бы модифицировать, чтобы изменить стиль макета. 

В следующем разделе [расширение в примере RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md), этот пример базового приложения расширяется, чтобы обрабатывать событие щелчка элемента и обновить `RecyclerView` при изменении источника базовых данных.



## <a name="related-links"></a>Связанные ссылки

- [RecyclerViewer (пример)](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Части RecyclerView и функциональные возможности](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [В примере RecyclerView расширение](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
