---
title: Сводка Глава 4. Раскрутка стека
description: 'Создание мобильных приложений с помощью Xamarin.Forms: Сводка Глава 4. Раскрутка стека'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 7A39FD4F-15AD-4F94-960E-9FEEB63FFD44
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 3571774ddec4182f35cac6f13d4582235e2ff31a
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997430"
---
# <a name="summary-of-chapter-4-scrolling-the-stack"></a>Сводка Глава 4. Раскрутка стека

В этой главе в основном посвящен понятие *макета*, это общий термин, классы и методы, которые использует Xamarin.Forms для организации визуальное отображение элемента несколько представлений на странице.

Макет включает в себя несколько классов, производных от [ `Layout` ](xref:Xamarin.Forms.Layout) и [ `Layout<T>` ](xref:Xamarin.Forms.Layout`1). Эта глава посвящена [ `StackLayout` ](xref:Xamarin.Forms.StackLayout).

Также введена в этой главе, [ `ScrollView` ](xref:Xamarin.Forms.ScrollView), [ `Frame` ](xref:Xamarin.Forms.Frame), и [ `BoxView` ](xref:Xamarin.Forms.BoxView) классы.

## <a name="stacks-of-views"></a>Стеки представлений

[`StackLayout`](xref:Xamarin.Forms.StackLayout) является производным от `Layout<View>` и наследует [ `Children` ](xref:Xamarin.Forms.Layout`1) свойство типа `IList<View>`. Добавьте несколько элементов представления в эту коллекцию и `StackLayout` отображает их в виде стека горизонтальную или вертикальную.

Задайте [ `Orientation` ](xref:Xamarin.Forms.StackLayout.Orientation) свойство `StackLayout` члену [ `StackOrientation` ](xref:Xamarin.Forms.StackOrientation) перечисления, либо [ `Vertical` ](xref:Xamarin.Forms.StackOrientation.Vertical) или [ `Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal). Значение по умолчанию — `Vertical`.

Задайте [ `Spacing` ](xref:Xamarin.Forms.StackLayout.Spacing) свойство `StackLayout` для `double` значение, указывающее расстояние между дочерние элементы. Значение по умолчанию — 6.

В коде, можно добавить элементы к `Children` коллекцию `StackLayout` в `for` или `foreach` цикл, как показано в [ **ColorLoop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorLoop) образец кода, или вы можете инициализировать `Children` коллекцией со списком отдельных представлений, как показано в [ **ColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorList). Дочерние элементы должны быть производными от `View` , но может включать другие `StackLayout` объектов.

## <a name="scrolling-content"></a>Прокрутка содержимого

Если `StackLayout` содержит слишком много дочерних элементов, отображаемых на странице, можно поместить `StackLayout` в [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) позволяет прокрутку.

Задайте [ `Content` ](xref:Xamarin.Forms.ScrollView.Content) свойство `ScrollView` к представлению, для прокрутки. Это часто `StackLayout`, но это может быть любое представление.

Задайте [ `Orientation` ](xref:Xamarin.Forms.ScrollView.Orientation) свойство `ScrollView` члену [ `ScrollOrientation` ](xref:Xamarin.Forms.ScrollOrientation) свойство, [ `Vertical` ](xref:Xamarin.Forms.ScrollOrientation.Vertical), [ `Horizontal` ](xref:Xamarin.Forms.ScrollOrientation.Horizontal), или [ `Both` ](xref:Xamarin.Forms.ScrollOrientation.Both). Значение по умолчанию — `Vertical`. Если содержимое `ScrollView` является `StackLayout`, две ориентации должны быть согласованы.

[ **ReflectedColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ReflectedColors) в нем демонстрируется использование `ScrollView` и `StackLayout` для отображения доступных цветов. Образец также демонстрирует использование отражения .NET для получения все открытые статические свойства и поля `Color` структуры не требуется явным образом перечислить их.

## <a name="the-expands-option"></a>Параметр разворачивается

Когда `StackLayout` стеки его дочерних элементов, каждый дочерний элемент занимает определенного ячейку в общую высоту `StackLayout` , зависит от размера дочернего элемента и параметры его `HorizontalOptions` и `VerticalOptions` свойства. Эти свойства присваиваются значения типа [ `LayoutOptions` ](http://developer.xamstage.com/api/type/Xamarin.Forms.LayoutOptions/).

`LayoutOptions` Структура определяет два свойства:

- [`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment) типа перечисления [ `LayoutAlignment` ](xref:Xamarin.Forms.LayoutAlignment) с четырьмя элементами [ `Start` ](xref:Xamarin.Forms.LayoutAlignment.Start), [ `Center` ](xref:Xamarin.Forms.LayoutAlignment.Center), [ `End` ](xref:Xamarin.Forms.LayoutAlignment.End), и [`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill)
- [`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) типа `bool`

Для удобства `LayoutOptions` структура также определяет восемь статические поля только для чтения типа `LayoutOptions` , охватывающих все комбинации два экземпляра свойств:

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

Включает в себя следующее обсуждение `StackLayout` с вертикальной ориентацией по умолчанию. Горизонтальное `StackLayout` является аналогом.

Для вертикального `StackLayout`, `HorizontalOptions` параметр определяет, как дочерний элемент по горизонтали размещен ширине `StackLayout`. `Alignment` Параметр `Start`, `Center`, или `End` , дочерние быть горизонтально без ограничений. Дочерние определяет свой собственный ширины и находится на слева, по центру или по правому краю `StackLayout`. `Fill` Параметр, дочерние по горизонтали ограничено и заполняет ширину `StackLayout`.

Для вертикального `StackLayout`, каждый дочерний элемент вертикально неограниченного и получает вертикальная область памяти в зависимости от высоты дочернего элемента, в этом случае `VerticalOptions` параметр не имеет значения.

Если по вертикали `StackLayout` сам неограниченного&mdash;то есть если его `VerticalOptions` параметр `Start`, `Center`, или `End`, затем высоту `StackLayout` является общая высота из его дочерних элементов.

Тем не менее если по вертикали `StackLayout` ограничен по вертикали&mdash;если его `VerticalOptions` параметр `Fill` &mdash;затем высоту `StackLayout` будет высота контейнера, который может быть больше общего количества Высота элемента его дочерние элементы. Если это так, и имеет по крайней мере один дочерний элемент `VerticalOptions` параметра `Expands` флаг `true`, затем дополнительное пространство в `StackLayout` равномерно распределяется среди этих дочерних действий с `Expands` флаг `true`. Общая высота дочерние элементы будет равен высоту `StackLayout`и `Alignment` частью `VerticalOptions` параметр определяет, как дочерний по вертикали размещен в его слоте.

Это показано в [ **VerticalOptionsDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/VerticalOptionsDemo) образца.

## <a name="frame-and-boxview"></a>Кадр и BoxView

Эти два представления прямоугольный часто используются для целей представления.

[ `Frame` ](xref:Xamarin.Forms.Frame) Представление отображает прямоугольную рамку вокруг другое представление, которое может быть макета, такие как `StackLayout`. `Frame` наследует [ `Content` ](xref:Xamarin.Forms.ContentView.Content) свойства из [ `ContentView` ](xref:Xamarin.Forms.ContentView) , задайте режим, который необходимо отобразить в `Frame`. `Frame` Прозрачен по умолчанию. Задайте следующие три свойства для настройки внешнего вида рамки:

- [ `OutlineColor` ](xref:Xamarin.Forms.Frame.OutlineColor) Свойство, чтобы сделать его видимым. Обычно для задания `OutlineColor` для `Color.Accent` вы не знаете, когда основной цвет.
- [ `HasShadow` ](xref:Xamarin.Forms.Frame.HasShadow) Можно присвоить свойство `true` для отображения черный тень на устройствах iOS.
- Задайте [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) свойства `Thickness` значение вставлять пробелы между и кадром содержимого. Значение по умолчанию — 20 единиц со всех сторон.

`Frame` Имеет значение по умолчанию `HorizontalOptions` и `VerticalOptions` значения `LayoutOptions.Fill`, означающее, что `Frame` заполнит его контейнера. С другими параметрами, размер `Frame` зависимости от размера его содержимого.

`Frame` Демонстрируется в [ **FramedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FramedText) образца.

[ `BoxView` ](xref:Xamarin.Forms.BoxView) Отображается прямоугольная область цветов, заданных его [ `Color` ](xref:Xamarin.Forms.BoxView.Color) свойство.

Если `BoxView` ограничен (его `HorizontalOptions` и `VerticalOptions` свойства имеют свои параметры по умолчанию `LayoutOptions.Fill`), `BoxView` заполняет доступное место для него. Если `BoxView` — без ограничений (с `HorizontalOptions` и `LayoutOptions` параметры `Start`, `Center`, или `End`), он имеет квадрата 40 единиц измерения по умолчанию. Объект `BoxView` может быть ограничен в одно измерение и без ограничений в другой.

Часто, необходимо установить [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest) и [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) свойства `BoxView` ему определенный размер. Это продемонстрировано [ **SizedBoxView** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/SizedBoxView) образца.

Можно использовать несколько экземпляров `StackLayout` для объединения `BoxView` и несколько `Label` экземпляров в `Frame` для отображения определенного цвета, а затем размещать каждый из этих представлений в `StackLayout` в `ScrollView` для создания привлекательных Список цветов, отображаемых в [ **ColorBlocks** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorBlocks) пример:

[![Тройной снимок экрана: блоки цвет](images/ch04fg11-small.png "списка цветов")](images/ch04fg11-large.png#lightbox "списка цветов")

## <a name="a-scrollview-in-a-stacklayout"></a>ScrollView в StackLayout?

Поместив `StackLayout` в `ScrollView` , но размещение `ScrollView` в `StackLayout` также иногда бывает удобно. Теоретически это не всегда возможно так как дочерние элементы вертикальной `StackLayout` являются вертикально без ограничений. Но `ScrollView` должен ограничиваться по вертикали. Ему нужно назначить высоту, чтобы затем можно определить размер его дочерних для прокрутки.

Для этого достаточно для предоставления `ScrollView` потомком `StackLayout` `VerticalOptions` параметр `FillAndExpand`. Это показано в [ **BlackCat** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCat) образца.

**BlackCat** образце также показано, как определить и получить доступ к ресурсам программы, которые внедряются в переносимой библиотеки классов (PCL). Это можно сделать также с проектами общих ресурсов (SAPs), но процесс несколько сложнее, как [ **BlackCatSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCatSap) в нем демонстрируется.



## <a name="related-links"></a>Связанные ссылки

- [Глава 4 полнотекстового поиска (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch04-Apr2016.pdf)
- [Примеры в главе 4](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)
- [Глава 4 F #-Примеры](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FS)
- [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md)
- [ScrollView](~/xamarin-forms/user-interface/layouts/scroll-view.md)
