---
title: Объект GridLayout
ms.prod: xamarin
ms.assetid: B69A4BF5-9CFB-443A-9F7B-062D1E498F61
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: bbc764adc204a1f5b9ef4674a183473995be55c1
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115163"
---
# <a name="gridlayout"></a>Объект GridLayout

`GridLayout` — Это новая `ViewGroup` подкласс, который поддерживает размещение представлений в двухмерной сетке аналогично HTML-таблицы, как показано ниже:

 [![Обрезанные GridLayout отображение четыре ячейки](grid-layout-images/21-gridlayoutcropped.png)](grid-layout-images/21-gridlayoutcropped.png#lightbox)

 `GridLayout` работает с иерархией плоского представления, где дочерние представления установите их расположение в сетке, указав строки и столбцы, они должны находиться в. Таким образом, *GridLayout* является возможность размещать представлений в сетке без необходимости, что все промежуточные представления предоставить структуру таблицы, таким образом, как показано в таблице строк, используемых в TableLayout. За счет Плоская иерархия, *GridLayout* сможет более быстро макета его дочерних представлений. Давайте рассмотрим пример иллюстрирует, что эта концепция фактически означает, что в коде.


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

Макет будет настраивать размер строк и столбцов так, чтобы ячейки можно загрузить их содержимого, как показано на следующей схеме:

 [![Схема макета, которые показывают две ячейки слева меньше, чем справа](grid-layout-images/gridlayout-cells.png)](grid-layout-images/gridlayout-cells.png#lightbox)

Это приводит к следующий интерфейс пользователя при запуске приложения:

 [![Снимок экрана из GridLayoutDemo приложений, отображение четыре ячейки](grid-layout-images/01-gridlayout.png)](grid-layout-images/01-gridlayout.png#lightbox)



## <a name="specifying-orientation"></a>Указание ориентации

Обратите внимание в XML выше, каждый `TextView` не содержит строки или столбца. Если они не заданы, `GridLayout` назначает каждой дочернее представление, в порядке, в зависимости от ориентации. Например изменим объект GridLayout ориентацию по умолчанию, горизонтальная "," для вертикальной следующим образом:

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

 [![Схема, иллюстрирующая расположение ячеек в вертикальной ориентацией](grid-layout-images/gridlayoutorientation.png)](grid-layout-images/gridlayoutorientation.png#lightbox)

Это приводит к следующий интерфейс пользователя во время выполнения:

 [![Снимок экрана GridLayoutDemo с ячейками, размещается в вертикальной ориентацией](grid-layout-images/02-gridlayout.png)](grid-layout-images/02-gridlayout.png#lightbox)



### <a name="specifying-explicit-position"></a>Указание явных позиции

Если нам нужно явным образом задавать положение дочерних представлений в `GridLayout`, можно установить их `layout_row` и `layout_column` атрибуты. Например следующий код XML приведет к компоновку, показанную на первый снимок экрана (как показано выше), вне зависимости от ориентации.

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

У нас есть несколько вариантов, которые предоставят расстояние между дочерние представления `GridLayout`. Мы можем использовать `layout_margin` атрибут, чтобы задать поля для каждого дочернего представления напрямую, как показано ниже

```xml
<TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0"
            android:layout_margin="10dp" />
```

Кроме того, вызывается новое представление общего назначения интервалы в Android 4 `Space` теперь доступна. Чтобы использовать его, просто добавьте его как дочернее представление.
Например, следующий XML добавляется дополнительная строка для `GridLayout` , задав его `rowcount` до 3 и добавляет `Space` представление, которое содержит интервал между `TextViews`.

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

 [![Снимок экрана из GridLayoutDemo иллюстрирующая большего размера ячейки с интервалом](grid-layout-images/03-gridlayout.png)](grid-layout-images/03-gridlayout.png#lightbox)

Преимущества использования нового `Space` представление является позволяет для интервала и не нужно установить атрибуты для каждого дочернего представления.



### <a name="spanning-columns-and-rows"></a>Объединение строк и столбцов

`GridLayout` Также поддерживает ячейки, охватывающие несколько столбцов и строк. Предположим, мы добавим другая строка, содержащая кнопку, чтобы `GridLayout` как показано ниже:

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

Это приведет к в первом столбце `GridLayout` растягивается в соответствии с размер кнопки, как показано здесь:

[![Снимок экрана GridLayoutDemo с кнопкой, охватывающие только первый столбец](grid-layout-images/04-gridlayout.png)](grid-layout-images/04-gridlayout.png#lightbox)

Чтобы предотвратить растяжение в первом столбце, можно установить кнопку, чтобы охватывать два столбца, задав его columnspan следующим образом:

```xml
<Button
    android:id="@+id/myButton"
    android:text="@string/hello"       
    android:layout_row="3"
    android:layout_column="0"
    android:layout_columnSpan="2" />
```

Это приводит к макет для `TextViews` аналогичен макета, у нас было ранее, с кнопкой, добавленными в нижнюю часть `GridLayout` как показано ниже:

 [![Снимок экрана GridLayoutDemo с кнопкой, охватывающая оба столбца](grid-layout-images/05-gridlayout.png)](grid-layout-images/05-gridlayout.png#lightbox)


## <a name="related-links"></a>Связанные ссылки

- [GridLayoutDemo (пример)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ICS_Samples/GridLayoutDemo/)
- [Знакомство с Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Платформа Android 4.0](http://developer.android.com/sdk/android-4.0.html)
