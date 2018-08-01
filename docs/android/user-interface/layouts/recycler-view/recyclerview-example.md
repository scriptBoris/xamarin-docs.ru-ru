---
title: Простой пример RecyclerView
description: Пример приложения, демонстрирующий способ использования RecyclerView.
ms.prod: xamarin
ms.assetid: A50520D2-1214-40E1-9B27-B0891FE11584
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 07/30/2018
ms.openlocfilehash: d48796b3c62fc342bd86f2d58e74c5f1710174bb
ms.sourcegitcommit: 0a1c392829454468dbe92f81d975e124a22b7014
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39360842"
---
# <a name="a-basic-recyclerview-example"></a>Простой пример RecyclerView

Чтобы понять, как `RecyclerView` работает в типичном приложении, в этом разделе рассматриваются [RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer/) пример приложения, простой пример кода, использующего `RecyclerView` для отображения большая коллекция фотографий: 

[![Два снимка экрана RecyclerView приложения, которое использует CardViews для отображения фотографий](recyclerview-example-images/01-recyclerviewer-sml.png)](recyclerview-example-images/01-recyclerviewer.png#lightbox)

**RecyclerViewer** использует [CardView](~/android/user-interface/controls/card-view.md) для реализации каждого элемента фотографии в `RecyclerView` макета. Из-за `RecyclerView`преимущества производительности, этот пример приложения быстро прокручивать большая коллекция фотографий и без заметной задержки.


### <a name="an-example-data-source"></a>Источник данных пример

В этом приложении-примере источник данных «фотоальбом» (представленный `PhotoAlbum` класс) предоставляет `RecyclerView` с содержимым элемента.
`PhotoAlbum` — Это коллекция фотографий с субтитрами; При создании экземпляра его, вы получаете готовые Коллекция фотографий 32:

```csharp
PhotoAlbum mPhotoAlbum = new PhotoAlbum ();
```

Каждый экземпляр фотографии в `PhotoAlbum` обеспечивает доступ к свойствам, которые дают возможность увидеть ее идентификатор ресурса образа, `PhotoID`и его строка заголовка `Caption`. Коллекция фотографий организована таким образом, каждой фотографии может осуществляться в индексаторе. Например приведенный ниже код доступа к идентификатор ресурса изображения и заголовок десятый фотографии в коллекции:

```csharp
int imageId = mPhotoAlbum[9].ImageId;
string caption = mPhotoAlbum[9].Caption;
```

`PhotoAlbum` также предоставляет `RandomSwap` метод, который можно использовать для замены первого фотографию в коллекции с фотографию случайно выбранных в другом месте в коллекции:

```csharp
mPhotoAlbum.RandomSwap ();
```

Так как сведения о реализации `PhotoAlbum` не относящиеся к анализу `RecyclerView`, `PhotoAlbum` исходный код не показан здесь. В исходном коде `PhotoAlbum` доступен по [PhotoAlbum.cs](https://github.com/xamarin/monodroid-samples/blob/master/android5.0/RecyclerViewer/RecyclerViewer/PhotoAlbum.cs) в [RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer/) примера приложения.


### <a name="layout-and-initialization"></a>Макет и инициализации

Файл макета, **Main.axml**, состоит из одного `RecyclerView` в `LinearLayout`:

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

Обратите внимание, что необходимо использовать полное доменное имя **android.support.v7.widget.RecyclerView** поскольку `RecyclerView` упакован в библиотеку поддержки. `OnCreate` Метод `MainActivity` инициализирует этот макет, создает экземпляр адаптера и подготавливает базового источника данных:

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

Этот код выполняет следующие функции:

1. Создает экземпляр `PhotoAlbum` источника данных.

2. Передает в конструктор адаптера источника данных photo album `PhotoAlbumAdapter` (который определяется далее в этом руководстве). 
   Обратите внимание на то, что он считается наилучшим решением будет передать конструктору адаптера источника данных как параметр. 

3. Получает `RecyclerView` из макета.

4. Подключается адаптера в `RecyclerView` экземпляра путем вызова `RecyclerView` `SetAdapter` метод, как показано выше.

### <a name="layout-manager"></a>Диспетчер макета

Каждый элемент в `RecyclerView` состоит из `CardView` , содержащий изображение и заголовок photo (подробности описаны в [владелец представления](#view-holder) раздел ниже). Предопределенный `LinearLayoutManager` используется для размещения каждой `CardView` в вертикальной прокрутки порядке:

```csharp
mLayoutManager = new LinearLayoutManager (this);
mRecyclerView.SetLayoutManager (mLayoutManager);
```

Этот код находится в основное действие `OnCreate` метод. Конструктор, чтобы диспетчер структуры требует *контекст*, поэтому `MainActivity` передается с помощью `this` см. выше.

Вместо использования predefind `LinearLayoutManager`, вы можете подключить диспетчер настраиваемый макет, который отображает два `CardView` элементы side-by-side, реализация эффект переворачивания страниц для прохода по коллекции фотографий. Далее в этом руководстве вы увидите пример того, как изменить макет путем переключения в диспетчере другой макет.

<a name="view-holder" />

### <a name="view-holder"></a>Владелец представления

Класс владельца представления, называется `PhotoViewHolder`. Каждый `PhotoViewHolder` ссылается экземпляр `ImageView` и `TextView` связанная строка элемента, который располагается в `CardView` как показано на диаграмме ниже:

[![Схема CardView, содержащий ImageView и TextView](recyclerview-example-images/02-cardview-layout-sml.png)](recyclerview-example-images/02-cardview-layout.png#lightbox)

`PhotoViewHolder` является производным от `RecyclerView.ViewHolder` и содержит свойства для хранения ссылки на `ImageView` и `TextView` показанный выше макета.
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
В этом примере кода `PhotoViewHolder` конструктору передается ссылка на представление родительского элемента ( `CardView`), `PhotoViewHolder` заключает в оболочку. Обратите внимание на то, что вы всегда отправляет родительского элемента представления конструктора базового класса. `PhotoViewHolder` Конструктор вызывает `FindViewById` в представлении родительского элемента, чтобы найдите все ссылки на нее дочерние, `ImageView` и `TextView`, сохраняя результаты в `Image` и `Caption` свойства, соответственно. Адаптер будет извлекать Просмотр ссылок из этих свойств при обновлении это `CardView`на дочерние представления с новыми данными.

Дополнительные сведения о `RecyclerView.ViewHolder`, см. в разделе [ссылки на класс RecyclerView.ViewHolder](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html).


### <a name="adapter"></a>Адаптер

Адаптер загружает каждую `RecyclerView` строку с данными для определенного фотографии. Для заданного фотографии в позиции строки *P*, например, адаптер находит связанные данные в позиции *P* в пределах источника данных и копирует эти данные в строку элемент в позиции *P* в `RecyclerView` коллекции. Адаптер использует владелец представления для уточняющего запроса ссылки для `ImageView` и `TextView` в этой позиции, поэтому не требуется многократно вызывать `FindViewById` для тех, представления, как пользователь прокручивает коллекции фотографии и повторно использует представления.

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

`mPhotoAlbum` Член содержит источник данных (фотоальбом), который передается в конструктор; конструктор копирует фотоальбома в переменную-член. Следующие необходимые `RecyclerView.Adapter` реализуются методы:

-   **`OnCreateViewHolder`** &ndash; Создает владелец файла и представление макета элемента.

-   **`OnBindViewHolder`** &ndash; Загружает данные в указанной позиции в представления, ссылки для которых хранятся в владелец данного представления.

-   **`ItemCount`** &ndash; Возвращает число элементов в источнике данных.

Диспетчер структуры вызывает эти методы, хотя он размещение элементов в пределах `RecyclerView`. В следующих разделах рассматриваются реализация этих методов.


#### <a name="oncreateviewholder"></a>OnCreateViewHolder

Вызовы manager макета `OnCreateViewHolder` при `RecyclerView` должен передана представление для представления элемента. `OnCreateViewHolder` увеличивает представление элементов из файла макета представления и создает оболочку для представления в новом `PhotoViewHolder` экземпляра. `PhotoViewHolder` Конструктор находит и сохраняет ссылки на дочерние представления в макете, как описано ранее в [владелец представления](#view-holder).

Каждый элемент строку представленного `CardView` , содержащий `ImageView` (для фотографии) и `TextView` (для заголовка). Этот макет находится в файле **PhotoCardView.axml**:

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

Эта структура представляет элемент одной строки в `RecyclerView`. `OnBindViewHolder` (Как описано ниже) метод копирует данные из источника данных в `ImageView` и `TextView` из этого макета.
`OnCreateViewHolder` увеличивает этот макет с места, фотографий `RecyclerView` и создает новый экземпляр `PhotoViewHolder` экземпляра (который находит и кэширует ссылки на `ImageView` и `TextView` дочерних представлений в связанном `CardView` макета):

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

Полученный экземпляр владельца представления, `vh`, возвращается обратно в вызывающий объект (диспетчер структуры).


#### <a name="onbindviewholder"></a>OnBindViewHolder

Когда диспетчер структуры будет готов для отображения конкретного представления в `RecyclerView`в видимой области экрана, она вызывает адаптера `OnBindViewHolder` метод для заполнения элемента в позицию указанной строки с содержимым источника данных. `OnBindViewHolder` Возвращает сведения о фотографии для заданной строки позиции (ресурс с изображением фотографии и строку для заголовка фотографии) и копирует эти данные в связанные с ними представления. Представления находятся с помощью ссылки, хранимых в объекте владельца представления (который передаются через `holder` параметра):

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

Объект владельца представления переданное необходимо сначала привести в тип владельца производным представлением (в этом случае `PhotoViewHolder`) перед их использованием.
Адаптер загружает ресурс изображения в представление, указанное владельцем представления `Image` . оно копирует текст заголовка в представлении ссылается владелец представления `Caption` свойство. Это *привязывает* связанного представления с данными.

Обратите внимание, что `OnBindViewHolder` приведен код, который работает непосредственно со структурой данных. В этом случае `OnBindViewHolder` понимает, как сопоставить `RecyclerView` элемент положение его элементу связанные данные в источнике данных. Сопоставление прост в этом случае поскольку положение можно использовать как индекс массива в фотоальбом; Тем не менее более сложных источников данных может потребоваться дополнительный код, чтобы установить такое сопоставление.


#### <a name="itemcount"></a>ItemCount

`ItemCount` Метод возвращает число элементов в коллекции данных. В примере фото приложением для просмотра количество элементов — это номер фотографии в фотоальбом:

```csharp
public override int ItemCount
{
    get { return mPhotoAlbum.NumPhotos; }
}
```

Дополнительные сведения о `RecyclerView.Adapter`, см. в разделе [ссылки на класс RecyclerView.Adapter](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html).


### <a name="putting-it-all-together"></a>Объединение

Полученный в результате `RecyclerView` состоит из реализации для примера приложения photo `MainActivity` код, который создает источник данных, диспетчер макета и адаптер. `MainActivity` Создает `mRecyclerView` экземпляра, создает экземпляр источника данных и адаптер и подключает диспетчер макета и адаптера:

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

Когда этот код скомпилирован и запущен, он создает базовый фотографий, просмотре приложения, как показано на следующем снимке экрана:

[![Два снимка экрана просмотра приложения с вертикальной прокрутки карты photo фотографий](recyclerview-example-images/03-recyclerviewer-basic-sml.png)](recyclerview-example-images/03-recyclerviewer-basic.png#lightbox)

Если не выполняется рисовании теней (как показано в приведенном выше снимке экрана), изменить **Properties/AndroidManifest.xml** и добавьте следующий параметр атрибута в `<application>` элемент:

```xml
android:hardwareAccelerated="true"
```

Это базовое приложение поддерживает только просмотр фотоальбома. Он не отвечает на события элемента touch, а также обрабатывать изменения в базовых данных. Эта возможность будет добавлена в [расширим пример RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md).




### <a name="changing-the-layoutmanager"></a>Изменение LayoutManager

Из-за `RecyclerView`его гибкость, очень просто измените приложение для использования диспетчера другой макет. В следующем примере изменяется для отображения фотоальбома макет сетки которой прокручивается по горизонтали, а не с вертикальной линейной структурой. Чтобы сделать это, при создании экземпляра диспетчера макета перенастраивается на использование `GridLayoutManager` следующим образом:

```csharp
mLayoutManager = new GridLayoutManager(this, 2, GridLayoutManager.Horizontal, false);
```

Такое изменение кода заменяет по вертикали `LinearLayoutManager` с `GridLayoutManager` представляет сетки, состоящий из двух строк, которые можно прокручивать в горизонтальном направлении. После компиляции и снова запустите приложение, вы увидите, что фотографий отображаются в сетке и что прокрутку по горизонтали, а не по вертикали:

[![Снимок экрана с примером приложения с фотографиями горизонтальной прокрутки в сетке](recyclerview-example-images/04-gridlayoutmanager-sml.png)](recyclerview-example-images/04-gridlayoutmanager.png#lightbox)

Можно изменить только одну строку кода, — можно изменить приложение просмотра фотографий, чтобы использовать другой макет с другим поведением.
Обратите внимание на то, что XML-ФАЙЛ макета, ни код адаптера необходимо было изменить, чтобы изменить стиль макета. 

В следующем разделе [расширим пример RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md), этот базовый пример приложения расширяется для обработки события click элемента и обновить `RecyclerView` при изменении базовых данных источника.



## <a name="related-links"></a>Связанные ссылки

- [RecyclerViewer (пример)](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [RecyclerView компоненты и функции](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [Расширим пример RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
