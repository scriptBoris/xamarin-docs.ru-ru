---
title: CardView
description: Мини-приложение Cardview является компонентом пользовательского интерфейса, который представляет содержимое text и image в представления, которые похожи на картах. Это руководство содержит описание использования и настройки CardView в приложениях Xamarin.Android, поддерживая обратную совместимость с более ранних версиях Android.
ms.prod: xamarin
ms.assetid: CF12FE85-D03A-4E64-95D2-D7115061A500
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 21e2a2e8ef04936664344cb4fb758bc2af3b4d05
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30771838"
---
# <a name="cardview"></a>CardView

_Мини-приложение Cardview является компонентом пользовательского интерфейса, который представляет содержимое text и image в представления, которые похожи на картах. Это руководство содержит описание использования и настройки CardView в приложениях Xamarin.Android, поддерживая обратную совместимость с более ранних версиях Android._


## <a name="overview"></a>Обзор

`Cardview` Мини-приложение, появившаяся в Android 5.0 (без описания операций), — это компонент пользовательского интерфейса, который представляет содержимое text и image в представления, которые похожи на картах. `CardView` реализуется в виде `FrameLayout` мини-приложение со скругленными углами и тени. Как правило `CardView` используется для представления элемента одной строки в `ListView` или `GridView` группа «Просмотр». Например, на следующем снимке экрана является примером приложения резервирования движения, который реализует `CardView`-на основе карт движения назначения в прокручиваемой `ListView`:

![Пример приложения с помощью CardView назначений движения](card-view-images/01-cardview-example.png)

В этом руководстве объясняется, как добавить `CardView` проекта пакет для вашей Xamarin.Android, как добавить `CardView` для настройки макета и как настроить внешний вид `CardView` в вашем приложении. Кроме того, это руководство предоставляет подробный список `CardView` атрибуты, которые можно изменить, включая атрибуты, которые помогут вам использовать `CardView` для версий Android, предшествующих Android 5.0 без описания операций.

<a name="requirements" />

## <a name="requirements"></a>Требования

Следующие необходим для использования нового Android 5.0 и более поздних версий компонентов (включая `CardView`) в приложениях на основе Xamarin:

-  **Xamarin.Android** &ndash; Xamarin.Android 4.20 или более поздней версии необходимо установить и настроить с помощью Visual Studio или Visual Studio для Mac.

-  **Пакет SDK для Android** &ndash; Android 5.0 (API 21) должен быть установлен через диспетчер Android SDK.

-  **Java JDK 1.8** &ndash; JDK 1.7 можно использовать, если вы являетесь специально для различных версий API уровня 23 и более ранних версий. JDK 1.8 доступна из [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

Приложение также должен включать `Xamarin.Android.Support.v7.CardView` пакета. Чтобы добавить `Xamarin.Android.Support.v7.CardView` пакета в Visual Studio для Mac:

1. Откройте проект, щелкните правой кнопкой мыши **пакетов** и выберите **добавить пакеты**.

2. В **добавить пакеты** диалогового окна, поиск **CardView**.

3. Выберите **CardView v7 библиотека поддержки Xamarin**, нажмите кнопку **добавить пакет**.

Чтобы добавить `Xamarin.Android.Support.v7.CardView` пакета в Visual Studio:

1. Откройте проект, щелкните правой кнопкой мыши **ссылки** узла (в **обозревателе решений** области) и выберите **управление пакетами NuGet...** .

2. Когда **управление пакетами NuGet** отображается диалоговое окно, введите **CardView** в поле поиска.

3. Когда **CardView v7 библиотека поддержки Xamarin** отображается, нажмите кнопку **установить**.

Дополнительные сведения о настройке проекта приложения Android 5.0 см. в разделе [параметр вверх Android 5.0 проекта](~/android/platform/lollipop.md).
Дополнительные сведения об установке пакетов NuGet см. в разделе [Пошаговое руководство: включая NuGet в проекте](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).


## <a name="introducing-cardview"></a>Знакомство с приложением CardView

Значение по умолчанию `CardView` напоминает карточку белый с минимальным скругленными углами и небольшие тени. Следующий пример **Main.axml** макета отображается одна `CardView` мини-приложения, содержащий `TextView`:

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

При использовании этот XML-код, чтобы заменить существующее содержимое **Main.axml**, не забудьте закомментировать любого кода в **MainActivity.cs** , ссылается на ресурсы в предыдущем XML.

В этом примере макет создается значение по умолчанию `CardView` одной строкой текста, как показано на следующем снимке экрана:

[![Снимок экрана CardView с белым фоном и текстовой строки](card-view-images/02-basic-cardview-sml.png)](card-view-images/02-basic-cardview.png#lightbox)

В этом примере стиль приложения имеет значение Светлая тема материал (`Theme.Material.Light`), чтобы `CardView` тени и ребра, легче увидеть. Дополнительные сведения о приложениях темы Android 5.0 см. в разделе [темы материал](~/android/user-interface/material-theme.md). В следующем разделе будет рассказано, как настроить `CardView` для приложения.


## <a name="customizing-cardview"></a>Настройка CardView

Вы можете изменить базовый `CardView` атрибуты, чтобы настроить внешний вид `CardView` в вашем приложении. Например, повышение `CardView` можно увеличить до тени большего размера (в результате отображаться как число с плавающей запятой, чем выше фон карты). Кроме того можно увеличить радиус скругления углов вносить углов дополнительные карты округляется.

В следующем примере макета, настраиваемый `CardView` используется для создания моделирование печати фотографии («моментального снимка»). `ImageView` Добавляется `CardView` для отображения изображения и `TextView` находится ниже `ImageView` для отображения заголовка изображения.
В этом примере `CardView` имеет следующие настройки:

-  `cardElevation` Увеличивается до 4dp для тени большего размера.
-  `cardCornerRadius` Увеличивается до 5dp вносить отображаются более округленные углы.

Поскольку `CardView` предоставляется по библиотеке поддержки Android версии 7 его атрибуты недоступны из `android:` пространства имен. Таким образом, необходимо определить свои собственные пространства имен XML и использовать это пространство имен, как `CardView` префикс атрибута. В следующем примере макета, мы используем следующую строку для определения имен с именем `cardview`:

```xml
    xmlns:cardview="http://schemas.android.com/apk/res-auto"
```

Это пространство имен можно вызвать `card_view` или даже `myapp` при выборе (он доступен только в пределах этого файла). Все, что вы решили вызвать это пространство имен, его необходимо использовать в качестве префикса `CardView` атрибут, который требуется изменить. В этом примере макета `cardview` пространство имен — это префикс для `cardElevation` и `cardCornerRadius`:

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

При использовании в этом примере макет для отображения изображения в приложении просмотра фотографий `CardView` имеет вид фото моментальных снимков, как показано на следующем снимке экрана:

[![CardView заголовка под изображением и изображений](card-view-images/03-photo-cardview-sml.png)](card-view-images/03-photo-cardview.png#lightbox)

На этом снимке экрана берется из [RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer) пример приложения, использующего `RecyclerView` мини-приложение для представления прокрутки списка `CardView` изображения для просмотра фотографий. Дополнительные сведения о `RecyclerView`, в разделе [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) руководства.

Обратите внимание, что `CardView` можно отобразить несколько дочерних представлений в его области содержимого. Например, в просмотра пример приложения выше фотографий, область содержимого состоит из `ListView` , содержащий `ImageView` и `TextView`. Несмотря на то что `CardView` экземпляры часто располагаются вертикально, можно упорядочить их по горизонтали (в разделе [создания пользовательского стиля представления](~/android/user-interface/material-theme.md#customview) для снимок экрана примера).


### <a name="cardview-layout-options"></a>Параметры макета CardView

`CardView` макеты можно настроить, задав один или несколько атрибутов, которые влияют на его заполнения, повышение прав, скругленных углов и цвет фона:

[![Схема CardView атрибутов](card-view-images/04-attributes-sml.png)](card-view-images/04-attributes.png#lightbox)

Каждый атрибут также может динамически изменить вызовом аналога `CardView` метод (Дополнительные сведения о `CardView` методов, см. [ссылку на класс CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html)).
Обратите внимание, что эти атрибуты (за исключением цвет фона) принимать значение измерения, — это десятичное число, следуют единицы. Например `11.5dp` указывает 11.5 плотность независимых пикселях.


#### <a name="padding"></a>Заполнение
`
CardView` Существует пять атрибутов заполнения для размещения содержимого в карточке. Можно задать их в макете XML или аналогичные методы можно вызывать в коде:

[![Схема CardView атрибуты внутренних полей](card-view-images/05-padding-sml.png)](card-view-images/05-padding.png#lightbox)

Атрибуты заполнения описать следующим образом:

-  `contentPadding` &ndash; Величина заполнения между дочерние представления из внутреннего `CardView` и всех границ карты.

-  `contentPaddingBottom` &ndash; Величина заполнения между дочерние представления из внутреннего `CardView` и нижней границей карты.

-  `contentPaddingLeft` &ndash; Величина заполнения между дочерние представления из внутреннего `CardView` и левой границей карты.

-  `contentPaddingRight` &ndash; Величина заполнения между дочерние представления из внутреннего `CardView` и правым краем карты.

-  `contentPaddingTop` &ndash; Величина заполнения между дочерние представления из внутреннего `CardView` и верхней границей карты.

Атрибуты содержимого заполнения, относительно границ области содержимого, а не в любой заданной мини-приложение находится в области содержимого.
Например если `contentPadding` достаточно увеличении в приложении просмотра фотографий, `CardView` бы обрезать изображение и текст, отображаемый на карте.



#### <a name="elevation"></a>Повышение прав

`CardView` предлагает два атрибута повышение прав для управления его повышение прав и, таким образом, размер тени его:

[![Схема CardView повышение атрибутов](card-view-images/06-elevation-sml.png)](card-view-images/06-elevation.png#lightbox)

Повышение атрибутов описать следующим образом:

-  `cardElevation` &ndash; Повышение прав `CardView` (представляет оси Z).

-  `cardMaxElevation` &ndash; Максимальное значение `CardView`на повышение прав.

Большие значения из `cardElevation` увеличить размер тени `CardView` отображаться как число с плавающей запятой, чем выше фона. `cardElevation` Атрибут также определяет порядок отображения перекрывающихся представления, то есть, `CardView` будет отображаться в другое представление перекрывающихся с максимальным повышение прав и более поздних версий все перекрывающиеся представления с более низкий уровень повышения прав.
`cardMaxElevation` Параметр полезен для приложения изменения повышение динамически &ndash; тени предотвращает расширение за границу, определяющие этот параметр.


#### <a name="corner-radius-and-background-color"></a>Радиус скругления углов и цвет фона

`CardView` Предоставляет атрибуты, которые можно использовать для управления его скругленных углов и цвет фона. Эти свойства позволяют изменить общий стиль `CardView`:

[![Схема углу CardView radious и атрибуты цвета фона](card-view-images/07-radius-bgcolor-sml.png)](card-view-images/07-radius-bgcolor.png#lightbox)

Эти атрибуты описаны следующим образом:

-  `cardCornerRadius` &ndash; Скругления углов все углы `CardView`.

-  `cardBackgroundColor` &ndash; Цвет фона `CardView`.

На этой диаграмме `cardCornerRadius` задано значение более скругленными 10dp и `cardBackgroundColor` задано значение `"#FFFFCC"` (Светло-желтый).


## <a name="compatibility"></a>Совместимость

Можно использовать `CardView` для версий Android, предшествующих Android 5.0 без описания операций. Поскольку `CardView` входит в состав библиотеки поддержки Android версии 7, можно использовать `CardView` с Android 2.1 (API уровня 7) и более поздней версии.
Тем не менее, необходимо установить `Xamarin.Android.Support.v7.CardView` пакета, как описано в [требования](#requirements)выше.

`CardView` Существуют немного по-разному на устройствах до без описания операций (уровень API 21).

-  `CardView` использует реализацию программный тени, добавляющий добавленных для заполнения.

-  `CardView` обрезает дочерние представления, пересекающийся с `CardView`скругленные углы.

Чтобы помочь в управлении отличия совместимости `CardView` предоставляет некоторые дополнительные атрибуты, которые можно настроить в макете:

-   `cardPreventCornerOverlap` &ndash; Установите этому атрибуту значение `true` Добавление заполнения, если приложение выполняется на более ранней версии Android (API уровня 20 и более ранних версий). Этот параметр запрещает `CardView` содержимого из пересекающиеся с `CardView`скругленные углы.

-   `cardUseCompatPadding` &ndash; Установите этому атрибуту значение `true` Добавление заполнения, когда приложение запущено в версиях Android на или больше, чем уровень API 21. Если вы хотите использовать `CardView` на устройствах до без описания операций будут выглядеть так же, без описания операций (или более поздней версии), присвойте этому атрибуту значение `true`. Если этот атрибут включен, `CardView` добавляет добавленных для заполнения для отрисовки тени, когда оно работает на устройствах до без описания операций. Это позволяет устранить различия в разреженности, введенные при предварительной без описания операций программный затененные реализации вступают в силу.

Дополнительные сведения о поддержке совместимости с предыдущими версиями Android см. в разделе [обслуживание совместимости](https://developer.android.com/training/material/compatibility.html).


## <a name="summary"></a>Сводка

В этом руководстве появился новый `CardView` мини-приложение включен в Android 5.0 (без описания операций). Оно будет показано значение по умолчанию `CardView` внешний вид и объясняется, как настроить `CardView` , изменив его повышения прав, скругления, контента заполнения, а цвет фона. Он будет показан `CardView` атрибуты макета (с диаграммами ссылка) и объясняется, как использовать `CardView` на устройствах Android в более ранних, чем Android 5.0 без описания операций. Дополнительные сведения о `CardView`, в разделе [ссылку на класс CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html).


## <a name="related-links"></a>Связанные ссылки

- [RecyclerView (пример)](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [Общие сведения о без описания операций](~/android/platform/lollipop.md)
- [Ссылку на класс CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html)
