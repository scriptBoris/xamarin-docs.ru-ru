---
title: В примере RecyclerView расширение
ms.prod: xamarin
ms.assetid: 707EE1CE-C164-485B-944C-82C6795E8A24
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 83147261a2d5458272f7e2bc105154da4308f4b0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30769267"
---
# <a name="extending-the-recyclerview-example"></a>В примере RecyclerView расширение


Простое приложение описано в [простой пример RecyclerView](~/android/user-interface/layouts/recycler-view/recyclerview-example.md) фактически ничего не делает &ndash; просто прокручивается и отображает список основных элементов фотографии для упрощения обзора. В реальных приложениях пользователи ожидают, что возможность взаимодействовать с приложением, коснувшись элементов на экране. Кроме того базового источника данных можно изменить (или изменить приложением) и отображение содержимого необходимо поддерживать целостность эти изменения. В следующих разделах вы узнаете, как обрабатывать событие щелчка элемента и обновить `RecyclerView` при изменении источника базовых данных.


### <a name="handling-item-click-events"></a>Обработка события щелчка элемента

Когда пользователь касается элемента в `RecyclerView`, генерируется событие щелчком элемента для уведомления приложения о том, какие был затронутых элементов. Это событие не формируется `RecyclerView` &ndash; вместо этого представления элементов (который помещается в владелец представления) обнаруживает изменения и сообщает эти изменения как события щелчка.

Чтобы показать, как обрабатывать событие щелчка элемента, следующие шаги поясняют, как изменить базовое приложение для просмотра фотографий какие фотографии обращались пользователем отчета. При возникновении события click элемента в пример приложения, происходит следующая последовательность:

1.  Фотография `CardView` обнаруживает события щелчка элемента и уведомляет о том, что адаптер.

2.  Адаптер обработчик щелчка элемента действия перенаправляет события (с сведения о позиции элемента).

3.  Обработчик щелчка элемента действия реагирует на события щелчка элемента.

Во-первых, вызывается член обработчик события `ItemClick` добавляется `PhotoAlbumAdapter` определения класса:

```csharp
public event EventHandler<int> ItemClick;
```

Затем добавляется метод обработчика события click элемента `MainActivity`.
Этот обработчик выводит краткое всплывающее уведомление, указывающее, какой элемент фотография был затронутых:

```csharp
void OnItemClick (object sender, int position)
{
    int photoNum = position + 1;
    Toast.MakeText(this, "This is photo number " + photoNum, ToastLength.Short).Show();
}

```

Далее строки кода должен зарегистрировать `OnItemClick` обработчик `PhotoAlbumAdapter`. Лучше всего сделать это — сразу после `PhotoAlbumAdapter` создается (в основной деятельности `OnCreate` метод):

```csharp
mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);
mAdapter.ItemClick += OnItemClick;

```

`PhotoAlbumAdapter` Теперь будет вызывать `OnItemClick` при получении события click элемента. Следующим шагом является создание обработчика в адаптер, который вызывает это `ItemClick` события. Следующий метод `OnClick`, добавляется непосредственно после адаптера `ItemCount` метод:

```csharp
void OnClick (int position)
{
    if (ItemClick != null)
        ItemClick (this, position);
}
```

Это `OnClick` метод — это адаптер *прослушивателя* для события click элемента из представления элемента. Прежде чем этот прослушиватель может быть зарегистрирован для представления элемента (через представление элемента владельца представления), `PhotoViewHolder` конструктора необходимо изменить, чтобы принять этот метод в качестве дополнительного аргумента и зарегистрировать `OnClick` с представлением элемента `Click` событий.
Ниже приводится измененный `PhotoViewHolder` конструктор:

```csharp
public PhotoViewHolder (View itemView, Action<int> listener)
    : base (itemView)
{
    Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
    Caption = itemView.FindViewById<TextView> (Resource.Id.textView);

    itemView.Click += (sender, e) => listener (base.LayoutPosition);
}

```

`itemView` Содержит ссылку на параметр `CardView` был затронутых пользователя. Обратите внимание, что базовый класс владельца представления знает, позиции макета элемента (`CardView`), он представляет (через `LayoutPosition` свойства), и эта позиция передается адаптера `OnClick` метод при наступлении события click элемента. Адаптер `OnCreateViewHolder` метод изменяются так, чтобы передать адаптера `OnClick` метод конструктору представления владелец:

```csharp
PhotoViewHolder vh = new PhotoViewHolder (itemView, OnClick);
```

Теперь при создании и запуске примера приложения просмотра фотографий, коснувшись фотографию в отображении вызовет тост отображаются в них, сообщает, какие фотографии был затронутых:

[![Касание примере извещения, отображаемый при фотографию карты](extending-the-example-images/01-photo-selected-sml.png)](extending-the-example-images/01-photo-selected.png#lightbox)

В этом примере показано только один из способов реализации обработчиков событий с `RecyclerView`. Другой подход, который может быть использован здесь — для помещения владелец представления событий и установлен адаптер подписываться на эти события. Если пример приложения фото фотографию возможности редактирования, отдельные события будет обязательным для `ImageView` и `TextView` внутри каждого `CardView`: затрагивает `TextView` запускает `EditView` диалоговое окно, которое позволяет пользователю изменить Заголовок и штрихи на `ImageView` запускает инструмента редактирования фотографий, позволяющий пользователю обрезать или поворота рисунка. В зависимости от потребностей вашего приложения необходимо разработать рекомендуемый подход к обработке и реагирование на события касания.

Чтобы продемонстрировать, как `RecyclerView` может обновляться, когда изменения набора данных, просмотр фотографий в пример приложения, которые могут быть изменены для случайным образом выбрать фотографию в источнике данных и его действие переключения с первой фото. Во-первых, **случайного выбора** фотоприложение примере добавляется кнопка **Main.axml** макета:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <Button
        android:id="@+id/randPickButton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:gravity="center_horizontal"
        android:textAppearance="?android:attr/textAppearanceLarge"
        android:text="Random Pick" />
    <android.support.v7.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:scrollbars="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" />
</LinearLayout>
```

Затем добавляется код в конце основной деятельности `OnCreate` метод нахождения `Random Pick` кнопку в макете и к ней подключить обработчик:

```csharp
Button randomPickBtn = FindViewById<Button>(Resource.Id.randPickButton);

randomPickBtn.Click += delegate
{
    if (mPhotoAlbum != null)
    {
        // Randomly swap a photo with the first photo:
        int idx = mPhotoAlbum.RandomSwap();
    }
};

```

Этот обработчик вызывает фотоальбом `RandomSwap` метод при **случайного выбора** касании кнопки. `RandomSwap` Метод случайным образом меняет фотографий с первой фотографии в источнике данных, а затем возвращает индекс фотографии местами случайным образом. При компиляции и запуска примера приложения с этим кодом, коснувшись **случайного выбора** кнопки не приводит к появлению изменения отображения поскольку `RecyclerView` не знает об изменении в источнике данных.

Чтобы сохранить `RecyclerView` обновлена после изменения источника данных **случайного выбора** нажмите кнопку обработчика необходимо изменить, чтобы вызвать адаптера `NotifyItemChanged` для каждого элемента в коллекции, которая была изменена (в этом случае два элемента имеют изменен: первый фотографии и местами фотографии). В результате `RecyclerView` обновления экрана, чтобы он был совместим с новым состоянием источника данных:

```csharp
Button randomPickBtn = FindViewById<Button>(Resource.Id.randPickButton);

randomPickBtn.Click += delegate
{
    if (mPhotoAlbum != null)
    {
        int idx = mPhotoAlbum.RandomSwap();

        // First photo has changed:
        mAdapter.NotifyItemChanged(0);

        // Swapped photo has changed:
        mAdapter.NotifyItemChanged(idx);
    }
};

```

Теперь, когда **случайного выбора** касании кнопки, `RecyclerView` обновляет отображение, чтобы показать, что фотографию дальнейшей работы в коллекции поменяли местами с первой фотографии в коллекции:

[![Первый снимок экрана перед заменой второй снимок экрана после переключения](extending-the-example-images/02-random-pick-sml.png)](extending-the-example-images/02-random-pick.png#lightbox)

Конечно `NotifyDataSetChanged` мог быть вызван вместо двух вызовах методов `NotifyItemChanged`, но делать так будет принудительно `RecyclerView` для обновления всей коллекции, несмотря на то, что были изменены только двух элементов в коллекции. Вызов `NotifyItemChanged` значительно более эффективно, чем вызов `NotifyDataSetChanged`.


## <a name="related-links"></a>Связанные ссылки

- [RecyclerViewer (пример)](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Части RecyclerView и функциональные возможности](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [Простой пример RecyclerView](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
