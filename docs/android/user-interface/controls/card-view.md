---
title: CardView
description: Мини-приложения Cardview является компонентом пользовательского интерфейса, который представляет содержимое text и image в представлениях, которые напоминают карты. В этом руководстве объясняется, как использовать и настраивать CardView в приложениях Xamarin.Android, поддерживая обратную совместимость с более ранних версиях Android.
ms.prod: xamarin
ms.assetid: CF12FE85-D03A-4E64-95D2-D7115061A500
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 019d89261687d1139ebced9400afbdf5eaf7a128
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109202"
---
# <a name="cardview"></a>CardView

_Мини-приложения Cardview является компонентом пользовательского интерфейса, который представляет содержимое text и image в представлениях, которые напоминают карты. В этом руководстве объясняется, как использовать и настраивать CardView в приложениях Xamarin.Android, поддерживая обратную совместимость с более ранних версиях Android._


## <a name="overview"></a>Обзор

`Cardview` Мини-приложения, представленные в Android 5.0 (Lollipop), является компонентом пользовательского интерфейса, который представляет содержимое text и image в представлениях, которые напоминают карты. `CardView` реализуется в виде `FrameLayout` мини-приложение со скругленными углами и тени. Как правило `CardView` используется для представления элемента одной строки в `ListView` или `GridView` группа «Просмотр». Например, на следующем снимке экрана приведен пример приложения travel резервирование, которое реализует `CardView`-на основе карт назначения путешествия в прокручиваемой `ListView`:

![Пример приложения с помощью CardView travel назначений](card-view-images/01-cardview-example.png)

В этом руководстве объясняется, как добавить `CardView` пакета для вашего Xamarin.Android проекта, как добавить `CardView` макет, и как настроить внешний вид `CardView` в вашем приложении. Кроме того, это руководство содержит подробный список `CardView` атрибуты, которые можно изменить, включая атрибуты, которые помогут вам использовать `CardView` в версиях Android более ранних, чем Android 5.0 Lollipop.

<a name="requirements" />

## <a name="requirements"></a>Требования

Следующие необходим для использования нового Android 5.0 и более поздних версий компонентов (включая `CardView`) в приложениях на базе Xamarin:

-  **Xamarin.Android** &ndash; Xamarin.Android 4.20 или более поздней версии необходимо установить и настроить Visual Studio или Visual Studio для Mac.

-  **Пакет SDK для Android** &ndash; Android 5.0 (API 21) или более поздней версии необходимо установить через диспетчер пакетов SDK для Android.

-  **Java JDK 1.8** &ndash; JDK 1.7 может использоваться, если вы являетесь специально уровень API — 23 и более ранних версий. JDK 1.8 доступен из [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

Приложение должно также включать `Xamarin.Android.Support.v7.CardView` пакета. Чтобы добавить `Xamarin.Android.Support.v7.CardView` пакета в Visual Studio для Mac:

1. Откройте проект, щелкните правой кнопкой мыши **пакетов** и выберите **добавить пакеты**.

2. В **добавить пакеты** диалоговое окно, и выполните поиск **CardView**.

3. Выберите **CardView v7 библиотека поддержки Xamarin**, затем нажмите кнопку **Add Package**.

Чтобы добавить `Xamarin.Android.Support.v7.CardView` пакета в Visual Studio:

1. Откройте проект, щелкните правой кнопкой мыши **ссылки** узла (в **обозревателе решений** области) и выберите **управление пакетами NuGet...** .

2. Когда **управление пакетами NuGet** отображается диалоговое окно, введите **CardView** в поле поиска.

3. Когда **CardView v7 библиотека поддержки Xamarin** отображается, нажмите кнопку **установить**.

Чтобы узнать, как настроить проект приложения Android 5.0, см. в разделе [параметр вверх Android 5.0 проекта](~/android/platform/lollipop.md).
Дополнительные сведения об установке пакетов NuGet см. в разделе [Пошаговое руководство: включая NuGet в проекте](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).


## <a name="introducing-cardview"></a>Знакомство с CardView

Значение по умолчанию `CardView` напоминает карточку белый с как минимум скругленных углов и небольшая тень. Следующий пример **Main.axml** макет отображается одна `CardView` мини-приложения, содержащий `TextView`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:gravity="center_horizontal"
    android:padding="5dp">
    <android.support.v7.widget.CardView
        android:layout_width="fill_parent"
        android:layout_height="245dp"
        android:layout_gravity="center_horizontal">
        <TextView
            android:text="Basic CardView"
            android:layout_marginTop="0dp"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:gravity="center"
            android:layout_centerVertical="true"
            android:layout_alignParentRight="true"
            android:layout_alignParentEnd="true" />
    </android.support.v7.widget.CardView>
</LinearLayout>
```

При использовании этот XML-код, чтобы заменить существующее содержимое **Main.axml**, убедитесь, что комментарии все фрагменты кода в **MainActivity.cs** , ссылающийся на ресурсы в предыдущем коде XML.

В этом примере макет создается по умолчанию `CardView` одной строкой текста, как показано на следующем снимке экрана:

[![Снимок экрана CardView с белым фоном и строка текста](card-view-images/02-basic-cardview-sml.png)](card-view-images/02-basic-cardview.png#lightbox)

В этом примере приложения стиля задано значение света тема материала (`Theme.Material.Light`), чтобы `CardView` теней и ребра легче см. в разделе. Дополнительные сведения о приложениях темы Android 5.0 см. в разделе [тема материала](~/android/user-interface/material-theme.md). В следующем разделе вы узнаете способы настройки `CardView` для приложения.


## <a name="customizing-cardview"></a>Настройка CardView

Вы можете изменить базовый `CardView` атрибуты для настройки внешнего вида `CardView` в вашем приложении. Например, что повышение `CardView` можно увеличить до уводят в тень большего размера (что делает кажется, что число с плавающей запятой, чем выше фона карты). Кроме того можно увеличить радиус углов вносить углов дополнительные карты округляется.

В следующем примере разметки, настраиваемый `CardView` используется для создания моделирование печати фотографии («моментальный снимок»). `ImageView` Добавляется `CardView` для отображения изображения и `TextView` находится ниже `ImageView` для отображения заголовка изображения.
В этом примере `CardView` имеет следующие настройки:

-  `cardElevation` Увеличивается до 4dp для приведения тенью большего размера.
-  `cardCornerRadius` Увеличивается до 5dp, чтобы сделать отображаются более скругленных углов.

Так как `CardView` предоставляется библиотекой поддержки Android версии 7, его атрибуты не доступны из `android:` пространства имен. Таким образом, необходимо определить собственное пространство имен XML и использовать это пространство имен, как `CardView` префикс атрибута. В следующем примере макета, мы используем эту строку для определения пространства имен `cardview`:

```xml
    xmlns:cardview="http://schemas.android.com/apk/res-auto"
```

Можно вызвать это пространство имен `card_view` или даже `myapp` при выборе (он доступен только в пределах этого файла). Все, что вы решили вызывать это пространство имен, его необходимо использовать в качестве префикса для `CardView` атрибут, который требуется изменить. В этом примере макета `cardview` пространство имен — это префикс для `cardElevation` и `cardCornerRadius`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:cardview="http://schemas.android.com/apk/res-auto"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:gravity="center_horizontal"
    android:padding="5dp">
    <android.support.v7.widget.CardView
        android:layout_width="fill_parent"
        android:layout_height="245dp"
        android:layout_gravity="center_horizontal"
        cardview:cardElevation="4dp"
        cardview:cardCornerRadius="5dp">
        <LinearLayout
            android:layout_width="fill_parent"
            android:layout_height="240dp"
            android:orientation="vertical"
            android:padding="8dp">
            <ImageView
                android:layout_width="fill_parent"
                android:layout_height="190dp"
                android:id="@+id/imageView"
                android:scaleType="centerCrop" />
            <TextView
                android:layout_width="fill_parent"
                android:layout_height="wrap_content"
                android:textAppearance="?android:attr/textAppearanceMedium"
                android:textColor="#333333"
                android:text="Photo Title"
                android:id="@+id/textView"
                android:layout_gravity="center_horizontal"
                android:layout_marginLeft="5dp" />
        </LinearLayout>
    </android.support.v7.widget.CardView>
</LinearLayout>
```

Если в этом примере макета используется для отображения изображения в приложении просмотра фотографий, `CardView` выглядящую photo моментальный снимок, как показано на следующем снимке экрана:

[![CardView с изображением и заголовка под изображением](card-view-images/03-photo-cardview-sml.png)](card-view-images/03-photo-cardview.png#lightbox)

На этом снимке экрана взят из [RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer) пример приложения, который использует `RecyclerView` мини-приложения для представления прокручиваемого списка `CardView` изображения для просмотра фотографий. Дополнительные сведения о `RecyclerView`, см. в разделе [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) руководства.

Обратите внимание, что `CardView` можно отобразить более одного дочернего представления в его области содержимого. Например, на просмотр примера приложения выше фотографии, области содержимого включает в себя `ListView` , содержащий `ImageView` и `TextView`. Несмотря на то что `CardView` экземпляры часто располагаются вертикально, можно упорядочить их по горизонтали (см. в разделе [создания пользовательского стиля представления](~/android/user-interface/material-theme.md#customview) для снимок экрана для примера).


### <a name="cardview-layout-options"></a>Параметры макета CardView

`CardView` макеты можно настроить, установив один или несколько атрибутов, которые влияют на его заполнения, повышение прав, радиус скругления угла и цвет фона:

[![Схема CardView атрибутов](card-view-images/04-attributes-sml.png)](card-view-images/04-attributes.png#lightbox)

Каждый атрибут также можно динамически изменить путем вызова с соответствующим `CardView` метод (Дополнительные сведения о `CardView` методы, см. в разделе [ссылки на класс CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html)).
Обратите внимание на то, что эти атрибуты (за исключением цвет фона) допускают значение измерения, — это десятичное число, следуют единицы. Например `11.5dp` указывает 11,5 density независимых пикселях.


#### <a name="padding"></a>Заполнение
`
CardView` предлагает пять атрибуты поля для размещения содержимого в карточке. Вы можете задать их в макете XML или аналогичные методы можно вызывать в коде:

[![Схема CardView атрибуты внутренних полей](card-view-images/05-padding-sml.png)](card-view-images/05-padding.png#lightbox)

Атрибуты поля описаны следующим образом:

-  `contentPadding` &ndash; Внутренние отступы между режимами дочерних `CardView` и всех краев элемента карты.

-  `contentPaddingBottom` &ndash; Внутренние отступы между режимами дочерних `CardView` и нижним краем элемента карты.

-  `contentPaddingLeft` &ndash; Внутренние отступы между режимами дочерних `CardView` и левым краем элемента карты.

-  `contentPaddingRight` &ndash; Внутренние отступы между режимами дочерних `CardView` и правым краем элемента карты.

-  `contentPaddingTop` &ndash; Внутренние отступы между режимами дочерних `CardView` и верхним краем элемента карты.

Атрибуты содержимого поля являются относительно границы области содержимого, а не для любого заданного мини-приложение, расположенное в области содержимого.
Например если `contentPadding` достаточно увеличении в приложении просмотра фотографий, `CardView` бы обрезать изображение и текст, отображаемый на карте.



#### <a name="elevation"></a>Повышение прав

`CardView` предлагает два атрибута повышение прав для управления его повышение прав и, соответственно, размер его тени:

[![Схема атрибуты CardView повышение прав](card-view-images/06-elevation-sml.png)](card-view-images/06-elevation.png#lightbox)

Повышение прав атрибуты описаны следующим образом:

-  `cardElevation` &ndash; Угол возвышения `CardView` (представляет оси Z).

-  `cardMaxElevation` &ndash; Максимальное значение `CardView`на повышение прав.

Большие значения из `cardElevation` увеличить размер тени, чтобы сделать `CardView` показаться число с плавающей запятой, чем выше фона. `cardElevation` Атрибут также определяет порядок отображения перекрывающихся представлений, то есть, `CardView` под другой перекрывающиеся представления с параметром повышенного уровня повышение прав и более поздних версий все перекрывающиеся представления с более низкий уровень повышения прав.
`cardMaxElevation` Параметр подходит, когда приложение изменяет повышения прав, динамически &ndash; тени предотвращает расширение за границу, который определяется с помощью этого параметра.


#### <a name="corner-radius-and-background-color"></a>Радиус скругления угла и цвет фона

`CardView` Предоставляет атрибуты, которые можно использовать для управления его радиуса скругления угла и цвет фона. Эти свойства позволяют изменить общий стиль `CardView`:

[![Схема radious углу CardView и атрибутов цвета фона](card-view-images/07-radius-bgcolor-sml.png)](card-view-images/07-radius-bgcolor.png#lightbox)

Эти атрибуты описаны следующим образом:

-  `cardCornerRadius` &ndash; Радиус скругления углов всех углов `CardView`.

-  `cardBackgroundColor` &ndash; Цвет фона `CardView`.

На этой схеме `cardCornerRadius` присваивается более скругленными 10dp и `cardBackgroundColor` присваивается `"#FFFFCC"` (Светло-желтый).


## <a name="compatibility"></a>Совместимость

Можно использовать `CardView` в версиях Android более ранних, чем Android 5.0 Lollipop. Так как `CardView` входит в состав библиотеки поддержки Android версии 7, можно использовать `CardView` с Android 2.1 (API уровня 7) и более поздних версий.
Тем не менее, необходимо установить `Xamarin.Android.Support.v7.CardView` пакета, как описано в разделе [требования](#requirements)выше.

`CardView` Существуют немного по-разному на устройствах, прежде чем Lollipop (уровень API 21).

-  `CardView` использует реализацию программный тени, добавляющий добавленных для заполнения.

-  `CardView` обрезает дочерние представления, пересекающихся с `CardView`скругленные углы.

Чтобы помочь в управлении эти различия совместимости `CardView` предоставляет некоторые дополнительные атрибуты, которые можно настроить в файле макета:

-   `cardPreventCornerOverlap` &ndash; Установите этому атрибуту значение `true` Добавление заполнения, когда ваше приложение работает в более ранних версиях Android (уровень API 20 и более ранних версий). Этот параметр позволяет избежать `CardView` содержимого из пересекающиеся с `CardView`скругленные углы.

-   `cardUseCompatPadding` &ndash; Установите этому атрибуту значение `true` Добавление заполнения, когда ваше приложение работает в версиях Android в или больше, чем уровень API 21. Если вы хотите использовать `CardView` на устройствах до без описания операций, который будет выглядеть так же без описания операций (или более поздней версии), установите этому атрибуту значение `true`. При включении этого атрибута `CardView` добавляет дополнительные внутренние поля для отрисовки тени, при выполнении на устройствах до без описания операций. Это позволяет преодолеть различия дополнения, которые вводятся при реализации предварительного Lollipop программный теневого вступают в силу.

Дополнительные сведения о совместимости с более ранних версиях Android, см. в разделе [поддержание совместимости](https://developer.android.com/training/material/compatibility.html).


## <a name="summary"></a>Сводка

Это руководство содержит общие новый `CardView` мини-приложения, включенные в Android 5.0 (Lollipop). Вы узнали, значение по умолчанию `CardView` внешний вид и было рассмотрено, как настроить `CardView` , изменив его повышение прав, скругление углов, контента заполнения, а цвет фона. Она появляется `CardView` атрибуты макета (с помощью ссылки "схемы") и как их использовать `CardView` на устройствах Android более ранних, чем Android 5.0 Lollipop. Дополнительные сведения о `CardView`, см. в разделе [ссылки на класс CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html).


## <a name="related-links"></a>Связанные ссылки

- [RecyclerView (пример)](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [Общие сведения о без описания операций](~/android/platform/lollipop.md)
- [Ссылки на класс CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html)
