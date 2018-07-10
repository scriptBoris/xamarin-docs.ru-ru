---
title: Сводка Глава 26. Пользовательские макеты
description: 'Создание мобильных приложений с помощью Xamarin.Forms: Сводка Глава 26. Пользовательские макеты'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2B7F4346-414E-49FF-97FB-B85E92D98A21
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: b6ef23364cac0dd1459681aa92c7a7db58bc81f0
ms.sourcegitcommit: 3e980fbf92c69c3dd737554e8c6d5b94cf69ee3a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935645"
---
# <a name="summary-of-chapter-26-custom-layouts"></a>Сводка Глава 26. Пользовательские макеты

Платформа Xamarin.Forms включает несколько классов, производных от [ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/):

* `StackLayout`,
* `Grid`,
* `AbsoluteLayout` и
* `RelativeLayout`.

В этой главе описывается создание собственных классов, производных от `Layout<View>`.

## <a name="an-overview-of-layout"></a>Общие сведения о макете

Нет не централизованной системе обработки макета Xamarin.Forms. Каждый элемент является ответственность за определение того, какой должна быть свой собственный размер и визуализироваться в определенном регионе.

### <a name="parents-and-children"></a>Родительские и дочерние элементы

Каждый элемент, имеющий дочерние элементы отвечает за размещение этих дочерних действий самих себя. Это родителя, который определяет, что размер его дочерние элементы должны быть основаны на размер имеет доступные и размер дочернего хочет иметь.

### <a name="sizing-and-positioning"></a>Изменения размеров и положения

Макет начинается в верхней части визуального дерева со страницей, а затем выполняется через все ветви. Является наиболее важным открытый метод в макете [ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/) определяется `VisualElement`. Каждый элемент, который является родительским для других вызовов элементов `Layout` для каждого из его дочерних элементов, чтобы предоставить дочерним, размер и положение относительно самого себя в виде [ `Rectangle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Rectangle/) значение. Эти `Layout` вызовы распространить по визуальному дереву.

Вызов `Layout` является обязательным для элемента, на экране и вызывает следующие свойства только для чтения, устанавливаемое значение. Согласованность с `Rectangle` передается в метод:

- [`Bounds`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Bounds/) типа `Rectangle`
- [`X`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.X/) типа `double`
- [`Y`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Y/) типа `double`
- [`Width`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Width/) типа `double`
- [`Height`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Height/) типа `double`

До версии `Layout` вызвать, `Height` и `Width` имеют макетов значения &ndash;1.

Вызов `Layout` также активирует следующие защищенных методов:

- [`SizeAllocated`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.SizeAllocated/p/System.Double/System.Double/), который вызывает
- [`OnSizeAllocated`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnSizeAllocated/p/System.Double/System.Double/), который можно переопределять.

Наконец запускается следующее событие:

- [`SizeChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.SizeChanged/)

`OnSizeAllocated` Метод переопределяется `Page` и `Layout`, которые являются только двух классов в Xamarin.Forms, которое может иметь дочерние элементы. Вызывает переопределенный метод

- [`UpdateChildrenLayout`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.UpdateChildrenLayout()/) для `Page` производные и [ `UpdateChildrenLayout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.UpdateChildrenLayout()/) для `Layout` производных материалов, которые вызывает
- [`LayoutChildren`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/) для `Page` производные и [ `LayoutChildren` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/) для `Layout` производных.

`LayoutChildren` затем вызывает `Layout` для каждого из его дочерних элементов. Если хотя бы один дочерний элемент добавлен новый `Bounds` параметр, то возникает следующее событие:

- [`LayoutChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.LayoutChanged/) для `Page` производные и [ `LayoutChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Layout.LayoutChanged/) для `Layout` производные

### <a name="constraints-and-size-requests"></a>Ограничения и размер запросов

Для `LayoutChildren` интеллектуально вызывать `Layout` на все его дочерние элементы, необходимо знать *предпочтительный* или *требуемой* размер для дочерних элементов. Поэтому вызовы `Layout` для каждого из дочерних элементов как правило, предшествует вызовы

- [`GetSizeRequest`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.GetSizeRequest/p/System.Double/System.Double/)

После публикации книги, `GetSizeRequest` метод был устарели и заменены

- [`Measure`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/)

`Measure` Метод [ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/) свойство и включает аргумент типа [ `MeasureFlag` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MeasureFlags/), который имеет два члена:

- [`IncludeMargins`](xref:Xamarin.Forms.MeasureFlags.IncludeMargins)
- [`None`](xref:Xamarin.Forms.MeasureFlags.None) Чтобы не включать поля

Для многих элементов `GetSizeRequest` или `Measure` получает собственный размер элемента из его модуль подготовки отчетов. Оба метода имеют параметры ширины и высоты *ограничения*. Например `Label` будет использовать ограничение ширины, чтобы определить создание программы-оболочки несколько строк текста.

Оба `GetSizeRequest`и `Measure` возвращают значение типа [ `SizeRequest` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SizeRequest/), который имеет два свойства:

- [`Request`](https://developer.xamarin.com/api/property/Xamarin.Forms.SizeRequest.Request/) типа `Size`
- [`Minimum`](https://developer.xamarin.com/api/property/Xamarin.Forms.SizeRequest.Minimum/) типа `Size`

Очень часто эти два значения совпадают и `Minimum` значение обычно можно игнорировать.

`VisualElement` также определяет защищенный метод, аналогичную `GetSizeRequest` , вызываемый из `GetSizeRequest`:

- [`OnSizeRequest`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnSizeRequest/p/System.Double/System.Double/) Возвращает `SizeRequest` значение

Этот метод является теперь устарели и заменены:

- [`OnMeasure`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/)

Каждый класс, производный от `Layout` или `Layout<T>` необходимо переопределить `OnSizeRequest` или `OnMeasure`. Это где класс макета определяет свой собственный размер, который зависит от размера его дочерних элементов, которые он получает путем вызова `GetSizeRequest` или `Measure` в дочерних элементах. До и после вызова метода `OnSizeRequest` или `OnMeasure`, `GetSizeRequest` или `Measure` при одновременном нажатии, исходя из следующих свойств:

- [`WidthRequest`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/)типа `double`, влияет на `Request` свойство `SizeRequest`
- [`HeightRequest`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/) типа `double`, влияет на `Request` свойство `SizeRequest`
- [`MinimumWidthRequest`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.MinimumWidthRequest/) типа `double`, влияет на `Minimum` свойство `SizeRequest`
- [`MinimumHeightRequest`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.MinimumHeightRequest/) типа `double`, влияет на `Minimum` свойство `SizeRequest`

### <a name="infinite-constraints"></a>Бесконечный ограничения

Ограничение аргументы, передаваемые `GetSizeRequest` (или `Measure`) и `OnSizeRequest` (или `OnMeasure`) может быть бесконечной (т. е. значения `Double.PositiveInfinity`). Тем не менее `SizeRequest` возвращаемые из этих методов не может содержать бесконечное измерений.

Бесконечный ограничения указывают, что запрошенный размер должно отражать естественному размеру элемента. Вертикальный `StackLayout` вызовы `GetSizeRequest` (или `Measure`) на его дочерние элементы с ограничением бесконечный высоты. Макет горизонтальный стек вызовов `GetSizeRequest` (или `Measure`) на его дочерние элементы с ограничением неограниченной ширины. `AbsoluteLayout` Вызовы `GetSizeRequest` (или `Measure`) на своей дочерней страницы с бесконечной ограничения ширины и высоты.

### <a name="peeking-inside-the-process"></a>Просмотр внутри процесса

[ **ExploreChildSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/ExploreChildSizes) отображает ограничение и размер запроса информации для простой макет.

## <a name="deriving-from-layoutview"></a>Наследование от макета<View>

Класс пользовательского макета является производным от `Layout<View>`. Он отвечает за две задачи:

- Переопределить `OnMeasure` для вызова `Measure` для дочерних элементов макета. Вернуть запрошенный размер для макета, сам
- Переопределить `LayoutChildren` для вызова `Layout` на дочерние элементы макета

`for` Или `foreach` цикл в этих переопределениях следует пропустить любой дочерний которого `IsVisible` свойству `false`.

Вызов `OnMeasure` не гарантируется. `OnMeasure` не будет вызываться, если родительского макета, управляющие размер макета (например, макет, который заполняет страницу). По этой причине `LayoutChildren` нельзя полагаться на размеры дочерних, полученные во время `OnMeasure` вызова. Очень часто `LayoutChildren` должен сам вызвать `Measure` для расположения дочерних элементов, или же можно применить определенный размер, кэширование логики (об этом будет рассказано позже).

### <a name="an-easy-example"></a>Простой пример

[ **VerticalStackDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/VerticalStackDemo) образец содержит упрощенную [ `VerticalStack` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter26/VerticalStackDemo/VerticalStackDemo/VerticalStackDemo/VerticalStack.cs) класс и демонстрации его использования.

### <a name="vertical-and-horizontal-positioning-simplified"></a>Вертикальное и горизонтальное расположение упрощенное

Одно из заданий, `VerticalStack` необходимо выполнить в ходе `LayoutChildren` переопределить. Данный метод использует ребенка `HorizontalOptions` свойство для определения способа изменить расположение дочерних в его ячейку в `VerticalStack`. Вместо этого можно вызвать статический метод [ `Layout.LayoutChildIntoBoundingRect` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildIntoBoundingRegion/p/Xamarin.Forms.VisualElement/Xamarin.Forms.Rectangle/). Этот метод вызывает метод `Measure` на дочерней и использует его `HorizontalOptions` и `VerticalOptions` свойства для размещения дочерние в пределах заданного прямоугольника.

### <a name="invalidation"></a>Недействительность

Изменение свойства элемента влияет на отображение этого элемента в макете. Представление должно быть не прошедшего проверку, чтобы активировать новый макет.

`VisualElement` Определяет защищенный метод [ `InvalidateMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.InvalidateMeasure()/), который обычно вызывается обработчиком property-changed любого привязываемые свойства которого был изменен, влияет на размер элемента. `InvalidateMeasure` Вызывается метод [ `MeasureInvalidated` ](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.MeasureInvalidated/) событий.

`Layout` Класс определяет аналогичный защищенный метод с именем [ `InvalidateLayout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.InvalidateLayout()/), который `Layout` производных следует вызвать для любого изменения, влияет на способ помещает и изменяет размер его дочерних элементов.

### <a name="some-rules-for-coding-layouts"></a>Некоторые правила кодирования макеты

1. Свойства, определенные `Layout<T>` производные должен поддерживаться привязываемые свойства, а должен вызывать обработчики property-changed `InvalidateLayout`.

2. Объект `Layout<T>` производных, который определяет присоединенное привязываемые свойства должны переопределять [ `OnAdded` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout%3CT%3E.OnAdded/p/T/) Добавление обработчика изменения свойств с его дочерними элементами и [ `OnRemoved` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout%3CT%3E.OnRemoved/p/T/) для удаления, обработчик. Проверьте изменения в этих присоединенных привязываемые свойства и реагировать на угрозы путем вызова обработчика `InvalidateLayout`.

3. Объект `Layout<T>` производных, реализующий кэш размеров дочерних должны переопределять `InvalidateLayout` и [ `OnChildMeasureInvalidated` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.OnChildMeasureInvalidated()/) и очистить кэш, при вызове этих методов.

### <a name="a-layout-with-properties"></a>Макет со свойствами

[ `WrapLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/WrapLayout.cs) В класс [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) предполагается, что все его дочерние узлы размеры и была перенесена дочерние элементы из одной строки (или столбца) Далее. Он определяет `Orientation` свойством, таким как `StackLayout`, и `ColumnSpacing` и `RowSpacing` свойства, такие как `Grid`, и он кэширует размеры дочерних.

[ **PhotoWrap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoWrap) пример помещает `WrapLayout` в `ScrollView` для отображения фотографий.

### <a name="no-unconstrained-dimensions-allowed"></a>Не допускается неограниченное аналитики!

[ `UniformGridLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/UniformGridLayout.cs) В [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) библиотека предназначена для отображения всех его дочерних узлов самих себя. Таким образом он не может работать с неограниченного измерений и вызывает исключение, если обнаруживается одно.

[ **PhotoGrid** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoGrid) в нем демонстрируется `UniformGridLayout`:

[![Тройной снимок фотографии в табличном](images/ch26fg08-small.png "единого макета сетки")](images/ch26fg08-large.png#lightbox "единого макета сетки")

### <a name="overlapping-children"></a>Перекрывающиеся дочерние элементы

Объект `Layout<T>` производных могут перекрывать друг друга его дочерних элементов. Тем не менее, дочерние элементы отображаются в порядке их в `Children` коллекции, а не порядок, в котором их `Layout` методы вызываются.

`Layout` Класс определяет два метода, которые дают возможность переместить дочерний в коллекции:

- [`LowerChild`](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LowerChild/p/Xamarin.Forms.View/) Чтобы переместить дочерний элемент в начало коллекции
- [`RaiseChild`](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.RaiseChild/p/Xamarin.Forms.View/) Чтобы переместить дочерний элемент в конец коллекции

Для перекрывающихся дочерних элементов дочерние элементы в конец коллекции визуально выводились поверх дочерних элементов в начале коллекции.

[ `OverlapLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/OverlapLayout.cs) В класс [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) библиотека определяет вложенное свойство для указания порядка отрисовки, что один из его дочерние элементы, отображаемый поверх остальных. [ **StudentCardFile** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/StudentCardFile) демонстрирует это:

[![Тройной снимок файловой сетке карту учащегося](images/ch26fg10-small.png "перекрывающиеся дочерние элементы макета")](images/ch26fg10-large.png#lightbox "перекрывающиеся дочерние элементы макета")

### <a name="more-attached-bindable-properties"></a>Подключенная привязываемые свойства

[ `CartesianLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CartesianLayout.cs) В класс [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) библиотека определяет присоединенное привязываемые свойства для указания двух `Point` значений и значение толщины и управляет `BoxView` элементы, чтобы он напоминал строки.

[ **UnitCube** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/UnitCube) образец использует эти данные для рисования трехмерный куб.

### <a name="layout-and-layoutto"></a>Макет и LayoutTo

Объект `Layout<T>` можно вызвать в производная `LayoutTo` вместо `Layout` для анимации макета. [ `AnimatedCartesianLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnimatedCartesianLayout.cs) Делает это и [ **AnimatedUnitCube** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/AnimatedUnitCube) в нем демонстрируется его.



## <a name="related-links"></a>Связанные ссылки

- [Глава 26 полнотекстового поиска (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch26-Apr2016.pdf)
- [Глава 26-примеры](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)
- [Создание пользовательских макетов](~/xamarin-forms/user-interface/layouts/custom.md)
