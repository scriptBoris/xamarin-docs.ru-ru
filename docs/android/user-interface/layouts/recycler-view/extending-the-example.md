---
title: Расширим пример RecyclerView
description: Добавление обработчиков событий щелчком элемента в приложении-примере RecyclerView.
ms.prod: xamarin
ms.assetid: 707EE1CE-C164-485B-944C-82C6795E8A24
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/13/2018
ms.openlocfilehash: eca0f58a470228ce8e6331defe88c1ef727cef57
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105185"
---
# <a name="extending-the-recyclerview-example"></a>Расширим пример RecyclerView


Базовое приложение, описано в разделе [базовый пример RecyclerView](~/android/user-interface/layouts/recycler-view/recyclerview-example.md) фактически ничего не делает &ndash; просто прокручивается и отображает фиксированный список элементов фотографии для упрощения обзора. В реальных приложениях пользователи хотят иметь возможность взаимодействовать с приложением, коснувшись элементов на экране. Кроме того базового источника данных можно изменить (или изменить в приложении), и содержимое области отображения должны оставаться неизменными с этими изменениями. В следующих разделах вы узнаете, как обрабатывать события щелчка элемента и обновить `RecyclerView` при изменении базовых данных источника.


### <a name="handling-item-click-events"></a>Обработка события Click элемента

Когда пользователь касается элемента в `RecyclerView`, чтобы уведомлять приложение, в том, какой элемент был затронут, создается событие щелчком элемента. Это событие не формируются `RecyclerView` &ndash; вместо этого представление элементов, (который обернут в владелец представления) обнаруживает штрихи и сообщает эти штрихи, как события щелчка мыши.

Чтобы проиллюстрировать, как обрабатывать события click элемента, ниже показано, как базовое приложение просмотра фотографий, изменяется для отчетов, какие фотографии был затронут пользователем. При возникновении события щелчком элемента в примере приложения, выполняется следующая последовательность:

1.  Фотография `CardView` обнаруживает события щелчка элемента и уведомляет адаптер.

2.  Адаптер перенаправляет событие (со сведениями, положение элемента) обработчик щелчка элемента действия.

3.  Обработчик щелчка элемента действия реагирует на события щелчка элемента.

Во-первых, вызывается член обработчик события `ItemClick` добавляется `PhotoAlbumAdapter` определение класса:

```csharp
public event EventHandler<int> ItemClick;
```

После этого добавляется метод обработчика событий щелчка элемента `MainActivity`.
Этот обработчик выводит краткое всплывающее уведомление, указывающее, какой элемент фотографии был затронут:

```csharp
void OnItemClick (object sender, int position)
{
    int photoNum = position + 1;
    Toast.MakeText(this, "This is photo number " + photoNum, ToastLength.Short).Show();
}

```

После этого строка кода требуется зарегистрировать `OnItemClick` обработчик с `PhotoAlbumAdapter`. Лучше всего сделать это — сразу после `PhotoAlbumAdapter` создается: 

```csharp
mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);
mAdapter.ItemClick += OnItemClick;

```

В этом примере регистрация обработчика выполняется в основное действие `OnCreate` метод, но рабочее приложение может зарегистрировать обработчик в `OnResume` и отменить регистрацию в `OnPause` &ndash; см. в разделе [жизненный цикл действия ](~/android/app-fundamentals/activity-lifecycle/index.md) Дополнительные сведения.

`PhotoAlbumAdapter` Теперь будут вызывать `OnItemClick` при получении события щелчка элемента. Следующим шагом является создание обработчика в адаптер, который вызывает это `ItemClick` событий. Следующий метод, `OnClick`, добавляется непосредственно после адаптера `ItemCount` метод:

```csharp
void OnClick (int position)
{
    if (ItemClick != null)
        ItemClick (this, position);
}
```

Это `OnClick` метод является адаптера *прослушивателя* для события щелчка элемента из представления элемента. Перед регистрацией этого прослушивателя с представлением элемента (через представление элементов владелец представления), `PhotoViewHolder` конструктор необходимо изменить, чтобы принять этот метод в качестве дополнительного аргумента и зарегистрировать `OnClick` с представлением элемента `Click` событий.
Ниже приведен измененный `PhotoViewHolder` конструктор:

```csharp
public PhotoViewHolder (View itemView, Action<int> listener)
    : base (itemView)
{
    Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
    Caption = itemView.FindViewById<TextView> (Resource.Id.textView);

    itemView.Click += (sender, e) => listener (base.LayoutPosition);
}

```

`itemView` Параметра содержит ссылку на `CardView` , был затронут пользователем. Обратите внимание, что базовый класс владелец представления знает позиции макета элемента (`CardView`), представляемой этим (через `LayoutPosition` свойства), и эта позиция передается в адаптер `OnClick` метод при наступлении события щелчка элемента. Адаптера `OnCreateViewHolder` метод изменяется для передачи адаптера `OnClick` метод конструктору представления владельца:

```csharp
PhotoViewHolder vh = new PhotoViewHolder (itemView, OnClick);
```

Теперь при построении и запуск примера приложения для просмотра фотографий, коснувшись фотографию в отображении вызывает всплывающее уведомление для отображения, который сообщает, какие фотографии был затронут:

[![Пример всплывающих, открываемом фотографию карты шифрованию](extending-the-example-images/01-photo-selected-sml.png)](extending-the-example-images/01-photo-selected.png#lightbox)

В этом примере показано лишь одним из подходов для реализации обработчиков событий с `RecyclerView`. Другой подход, который может использоваться здесь является снабдить владелец представления событий и адаптер подписываться на эти события. Если приложение по обмену фотографиями образец предоставляется возможность редактирования фотографий, отдельные события потребовалось бы для `ImageView` и `TextView` внутри каждой `CardView`: затрагивает `TextView` запускает `EditView` диалоговое окно, которое позволяет пользователю изменить Заголовок и затрагивает `ImageView` запускает инструмента редактирования фотографий, позволяющая пользователю обрезать или поворота рисунка. В зависимости от потребностей вашего приложения необходимо разработать оптимального подхода для обработки и реагирование на события касания.

Чтобы продемонстрировать, как `RecyclerView` могут обновляться, когда изменения набора данных, просмотра фотографий в примере приложения, которые могут быть изменены случайным образом выбрать фотографию в источнике данных и поменять его местами с первой фотографии. Во-первых, **случайного выбора** photo примера приложения добавляется кнопка **Main.axml** макета:

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

Затем код добавляется в конце основное действие `OnCreate` метод необходимо найти `Random Pick` кнопку в макете и присоединить обработчик к нему:

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

Этот обработчик вызывает дополнение photo album `RandomSwap` метод при **случайного выбора** нажатии кнопки. `RandomSwap` Метод случайным образом меняет местами фотографии с первой фотографии в источнике данных, а затем возвращает индекс фотографии случайным образом с перестановкой. Компиляция и запуск примера приложения с этим кодом, коснувшись **случайного выбора** кнопки не приводит к появлению изменения отображения поскольку `RecyclerView` не учитывает изменения в источник данных.

Чтобы сохранить `RecyclerView` обновлены после изменения источника данных **случайного выбора** щелкните обработчик необходимо изменить, чтобы вызвать `NotifyItemChanged` для каждого элемента в коллекции, которая была изменена (в этом случае два элемента имеют изменено: первый фотографию и местами фото). В результате `RecyclerView` обновления экрана, чтобы порядок был согласован с новым состоянием источника данных:

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

Теперь, когда **случайного выбора** нажатии кнопки, `RecyclerView` обновится, чтобы показать, что фотографию дальнейшей вниз в коллекции было переведено с первой фотографии в коллекции:

[![Первый снимок экрана перед заменой, второй снимок экрана после переключения](extending-the-example-images/02-random-pick-sml.png)](extending-the-example-images/02-random-pick.png#lightbox)

Само собой `NotifyDataSetChanged` мог быть вызван вместо того, чтобы два вызова `NotifyItemChanged`, но это таким образом вынудить `RecyclerView` для обновления всей коллекции, несмотря на то, что были изменены только два элемента в коллекции. Вызов `NotifyItemChanged` значительно эффективнее, чем вызов `NotifyDataSetChanged`.


## <a name="related-links"></a>Связанные ссылки

- [RecyclerViewer (пример)](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [RecyclerView компоненты и функции](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [Простой пример RecyclerView](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
