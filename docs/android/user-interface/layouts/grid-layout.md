---
title: GridLayout
ms.prod: xamarin
ms.assetid: B69A4BF5-9CFB-443A-9F7B-062D1E498F61
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 53f2ba8ff14a4338310e02244acdbfd7fa9bc13c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30768510"
---
# <a name="gridlayout"></a>GridLayout

`GridLayout` — Это новая `ViewGroup` подкласс, в котором поддерживает определение представления в 2D сетке аналогично HTML-таблицы, как показано ниже:

 [![Обрезку GridLayout отображение четыре ячейки](grid-layout-images/21-gridlayoutcropped.png)](grid-layout-images/21-gridlayoutcropped.png#lightbox)

 `GridLayout` работает с иерархией плоский вид, где дочерние представления установите их расположение в сетке, указав строки и столбцы, они должны находиться в. В этом случае *GridLayout* может разместить представлений в сетке без необходимости, любые промежуточные представления предоставляют структуру таблицы, такие, как показано в таблице строк, используемых в TableLayout. Поддерживая Плоская иерархия *GridLayout* может более быстро макета его дочерние представления. Давайте рассмотрим пример для иллюстрации этой концепции фактически это значит, в коде.


## <a name="creating-a-grid-layout"></a>Создание сетки макета

Следующий XML-код добавляет некоторые `TextView` элементы управления *GridLayout*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip" />
     <TextView
            android:text="Cell 2"
            android:textSize="14dip" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip" />
</GridLayout>
```

Макет скорректирует размеры строк и столбцов, чтобы ячейки соответствии с содержимым, как показано на следующей схеме.

 [![Схема макета, показывающая две ячейки слева меньше, чем в правой части](grid-layout-images/gridlayout-cells.png)](grid-layout-images/gridlayout-cells.png#lightbox)

Это приведет к следующей пользовательский интерфейс, если выполняются в приложении:

 [![Снимок экрана GridLayoutDemo приложения отображение четыре ячейки](grid-layout-images/01-gridlayout.png)](grid-layout-images/01-gridlayout.png#lightbox)



## <a name="specifying-orientation"></a>Указание ориентации

Обратите внимание в XML-документе выше, каждый `TextView` не соответствует строке или столбце. Если они не указаны, `GridLayout` назначает каждого дочернего представления в порядке, в зависимости от ориентации. Например давайте ориентация GridLayout с по умолчанию, равное горизонтально, с вертикальной следующим образом:

```xml
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2"
        android:orientation="vertical">
</GridLayout>
```

Теперь `GridLayout` расположит ячейки сверху вниз в каждом столбце, а не слева направо, как показано ниже:

 [![Диаграмма, иллюстрирующая расположение ячейки с вертикальной ориентацией.](grid-layout-images/gridlayoutorientation.png)](grid-layout-images/gridlayoutorientation.png#lightbox)

Это приведет к следующей пользовательский интерфейс во время выполнения:

 [![Снимок экрана GridLayoutDemo с ячейками, размещается в вертикальной ориентацией](grid-layout-images/02-gridlayout.png)](grid-layout-images/02-gridlayout.png#lightbox)



### <a name="specifying-explicit-position"></a>Указать явные позиции

Если мы хотим явным образом задавать положение дочерних представлений в `GridLayout`, можно установить их `layout_row` и `layout_column` атрибуты. Например следующий код XML приведет к компоновку, показанную на первом экрана (показано выше), вне зависимости от ориентации.

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="1" />
     <TextView
            android:text="Cell 2"
            android:textSize="14dip"
            android:layout_row="1"
            android:layout_column="0" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip"
            android:layout_row="1"
            android:layout_column="1"  />
</GridLayout>
```



### <a name="specifying-spacing"></a>Указание интервала

У нас есть две возможности, которые предоставят интервал между дочерние представления `GridLayout`. Мы используем `layout_margin` атрибут, чтобы задать поля для каждого дочернего представления напрямую, как показано ниже

```xml
<TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0"
            android:layout_margin="10dp" />
```

Кроме того, вызывается новое представление общего назначения интервалы в Android 4 `Space` теперь доступен. Чтобы использовать его, просто добавьте его в качестве дочернего представления.
Например, ниже XML добавляется дополнительная строка для `GridLayout` , установив его `rowcount` 3 и добавляет `Space` представление, которое предоставляет интервал между `TextViews`.

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="3"
        android:columnCount="2"
        android:orientation="vertical">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip"
            android:layout_row="0"        
            android:layout_column="1" />
     <Space
            android:layout_row="1"
            android:layout_column="0"
            android:layout_width="50dp"         
            android:layout_height="50dp" />    
     <TextView
            android:text="Cell 2"
            android:textSize="14dip"
            android:layout_row="2"        
            android:layout_column="0" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip"
            android:layout_row="2"        
            android:layout_column="1" />
</GridLayout>
```

Этот XML-код создает интервал в `GridLayout` как показано ниже:

 [![Снимок экрана из GridLayoutDemo наглядного более крупные ячейки с интервалом](grid-layout-images/03-gridlayout.png)](grid-layout-images/03-gridlayout.png#lightbox)

Преимущество использования нового `Space` представление, он позволяет интервала и не требует нам задать атрибуты для каждого дочернего представления.



### <a name="spanning-columns-and-rows"></a>Охват столбцов и строк

`GridLayout` Также поддерживает ячеек, которые охватывают несколько столбцов и строк. Допустим, мы добавьте еще одну строку, содержащую кнопку, чтобы `GridLayout` как показано ниже:

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="4"
        android:columnCount="2"
        android:orientation="vertical">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip"
            android:layout_row="0"        
            android:layout_column="1" />
     <Space
            android:layout_row="1"
            android:layout_column="0"
            android:layout_width="50dp"        
            android:layout_height="50dp" />   
     <TextView
            android:text="Cell 2"
            android:textSize="14dip"
            android:layout_row="2"        
            android:layout_column="0" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip"        
            android:layout_row="2"        
            android:layout_column="1" />
     <Button
            android:id="@+id/myButton"
            android:text="@string/hello"        
            android:layout_row="3"
            android:layout_column="0" />
</GridLayout>
```

Это приведет к первый столбец `GridLayout` растягивается до размеров кнопки, как показано ниже:

[![Снимок экрана GridLayoutDemo с кнопкой охват только первый столбец](grid-layout-images/04-gridlayout.png)](grid-layout-images/04-gridlayout.png#lightbox)

Чтобы предотвратить растяжение в первом столбце, мы устанавливаем кнопка занимал два столбца, установив его columnspan следующим образом:

```xml
<Button
    android:id="@+id/myButton"
    android:text="@string/hello"       
    android:layout_row="3"
    android:layout_column="0"
    android:layout_columnSpan="2" />
```

Это приводит к макет для `TextViews` аналогичен макета, в более ранних версиях необходимо было с кнопкой, добавляемый в конец `GridLayout` как показано ниже:

 [![Снимок экрана GridLayoutDemo с кнопкой, объединение обоих столбцов](grid-layout-images/05-gridlayout.png)](grid-layout-images/05-gridlayout.png#lightbox)


## <a name="related-links"></a>Связанные ссылки

- [GridLayoutDemo (пример)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ICS_Samples/GridLayoutDemo/)
- [Знакомство с приложением Сандвичевы мороженого](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 платформы](http://developer.android.com/sdk/android-4.0.html)
