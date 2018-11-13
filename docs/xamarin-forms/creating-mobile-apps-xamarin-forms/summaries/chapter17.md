---
title: Сводка Глава 17. Совершенствование навыков работы в сетке
description: 'Создание мобильных приложений с помощью Xamarin.Forms: Сводка Глава 17. Совершенствование навыков работы в сетке'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 71EDEF9C-4220-4D2E-A235-43F1EC8746C1
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: db16ee34ed353379ea23899da0d3d16fc8428c62
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563360"
---
# <a name="summary-of-chapter-17-mastering-the-grid"></a>Сводка Глава 17. Совершенствование навыков работы в сетке

[ `Grid` ](xref:Xamarin.Forms.Grid) — Это набор мощных механизм, который упорядочивает его дочерние элементы в строки и столбцы ячеек. В отличие от HTML-код, аналогичный `table` элемент, `Grid` , предназначенное исключительно для целей макета, а не презентации.

## <a name="the-basic-grid"></a>Основные сетки

`Grid` является производным от [ `Layout<View>` ](xref:Xamarin.Forms.Layout`1), который определяет [ `Children` ](xref:Xamarin.Forms.Layout`1.Children) свойство, `Grid` наследует. Вы можете заполнить эту коллекцию на XAML или кода.

### <a name="the-grid-in-xaml"></a>Сетка в XAML

Определение `Grid` в XAML обычно начинается с заполнения [ `RowDefinitions` ](xref:Xamarin.Forms.Grid.RowDefinitions) и [ `ColumnDefinitions` ](xref:Xamarin.Forms.Grid.ColumnDefinitions) коллекции `Grid` с [ `RowDefinition` ](xref:Xamarin.Forms.RowDefinition) и [ `ColumnDefinition` ](xref:Xamarin.Forms.ColumnDefinition) объектов. Это, как установить число строк и столбцов `Grid`и их свойства.

`RowDefinition` имеет [ `Height` ](xref:Xamarin.Forms.RowDefinition.Height) свойство и `ColumnDefinition` имеет [ `Width` ](xref:Xamarin.Forms.ColumnDefinition.Width) свойство, оба типа [ `GridLength` ](xref:Xamarin.Forms.GridLength), структуру.

В XAML [ `GridLengthTypeConverter` ](xref:Xamarin.Forms.GridLengthTypeConverter) преобразует простые текстовые строки в `GridLength` значения. На самом деле [ `GridLength` конструктор](xref:Xamarin.Forms.GridLength.%23ctor(System.Double,Xamarin.Forms.GridUnitType)) создает `GridLength` значение, основанное на число и значение типа [ `GridUnitType` ](xref:Xamarin.Forms.GridUnitType), перечисление с тремя элементами:

- [`Absolute`](xref:Xamarin.Forms.GridUnitType.Absolute) &mdash; Ширина или высота задается в аппаратно независимых единицах (число в XAML)
- [`Auto`](xref:Xamarin.Forms.GridUnitType.Auto) &mdash; Высота или ширина будет определяться автоматически, основываясь на содержимом ячейки («Auto» в XAML)
- [`Star`](xref:Xamarin.Forms.GridUnitType.Star) &mdash; Оставшиеся высоты или ширины распределяется пропорционально (число с "\*«, который называется *типа" звезда "*, в XAML)

Каждый дочерний элемент элемента `Grid` также должен быть назначен строк и столбцов (явно или неявно). Охватывает строки и столбца диапазонов являются необязательными. Они все указываются с помощью присоединенного свойства связывания &mdash; свойства, которые определяются `Grid` но устанавливаться для дочерних элементов `Grid`. `Grid` Определяет четыре статических присоединенного привязываемые свойства:

- [`RowProperty`](xref:Xamarin.Forms.Grid.RowProperty) &mdash; Отсчитываемый от нуля строку; значение по умолчанию — 0
- [`ColumnProperty`](xref:Xamarin.Forms.Grid.ColumnProperty) &mdash; Отсчитываемый от нуля столбец; значение по умолчанию — 0
- [`RowSpanProperty`](xref:Xamarin.Forms.Grid.RowSpanProperty) &mdash; число строк распространяется на дочерние; по умолчанию — 1
- [`ColumnSpanProperty`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) &mdash; число столбцов распространяется на дочерние; по умолчанию — 1

В коде программа может использовать восемь статические методы для задания и получения этих значений:

- [`Grid.SetRow`](xref:Xamarin.Forms.Grid.SetRow(Xamarin.Forms.BindableObject,System.Int32)) и [`Grid.GetRow`](xref:Xamarin.Forms.Grid.GetRow(Xamarin.Forms.BindableObject))
- [`Grid.SetColumn`](xref:Xamarin.Forms.Grid.SetColumn(Xamarin.Forms.BindableObject,System.Int32)) и [`Grid.GetColumn`](xref:Xamarin.Forms.Grid.GetColumn(Xamarin.Forms.BindableObject))
- [`Grid.SetRowSpan`](xref:Xamarin.Forms.Grid.SetRowSpan(Xamarin.Forms.BindableObject,System.Int32)) и [`Grid.GetRowSpan`](xref:Xamarin.Forms.Grid.GetRowSpan(Xamarin.Forms.BindableObject))
- [`Grid.SetColumnSpan`](xref:Xamarin.Forms.Grid.SetColumnSpan(Xamarin.Forms.BindableObject,System.Int32)) и [`Grid.GetColumnSpan`](xref:Xamarin.Forms.Grid.GetColumnSpan(Xamarin.Forms.BindableObject))

В XAML о настройке этих значений можно использовать следующие атрибуты:

- `Grid.Row`
- `Grid.Column`
- `Grid.RowSpan`
- `Grid.ColumnSpan`

[ **SimpleGridDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SimpleGridDemo) в нем демонстрируется создание и инициализация `Grid` в XAML.

`Grid` Наследует [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) свойства из `Layout` и определяет две дополнительные свойства, которые предоставляют расстояние между строками и столбцами:

- [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing) имеет значение по умолчанию из 6
- [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) имеет значение по умолчанию из 6

`RowDefinitions` И `ColumnDefinitions` коллекции не является обязательным. Если значение отсутствует, `Grid` создает строки и столбцы для `Grid` дочерних элементов и присваивает им все значения по умолчанию `GridLength` из "\*" (звездочка).

### <a name="the-grid-in-code"></a>Сетка в коде

[ **GridCodeDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCodeDemo) образце показано, как создать и заполнить `Grid` в коде. Вложенные свойства можно задать для каждого дочернего напрямую или косвенно путем вызова дополнительных `Add` методы, такие как [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid+IGridList%3CT%3E.Add/p/Xamarin.Forms.View/System.Int32/System.Int32/System.Int32/System.Int32/) определяется [Grid.IGridList<T> ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid+IGridList%3CT%3E/) интерфейс.

### <a name="the-grid-bar-chart"></a>На линейчатой диаграмме сетки

[ **GridBarChart** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridBarChart) образце показано, как добавить несколько `BoxView` элементы `Grid` с помощью массового [ `AddHorizontal` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid+IGridList%3CT%3E.AddHorizontal/p/System.Collections.Generic.IEnumerable%7BXamarin.Forms.View%7D/) метод. По умолчанию эти `BoxView` элементы имеют равные ширину. Высота каждой `BoxView` можно управлять, чтобы он напоминал линейчатую диаграмму.

`Grid` В **GridBarChart** пример общих папок `AbsoluteLayout` родительскому с первоначально невидимо `Frame`. Программа также задает `TapGestureRecognizer` на каждом `BoxView` использовать `Frame` для отображения сведений о строке полученные.

### <a name="alignment-in-the-grid"></a>Выравнивание в сетке

[ **GridAlignment** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridAlignment) образце показано, как использовать `VerticalOptions` и `HorizontalOptions` свойства для выравнивания дочерних элементов в `Grid` ячейки.

[ **SpacingButtons** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SpacingButtons) одинаково пример пробелы `Button` по центру элементы `Grid` ячеек.

### <a name="cell-dividers-and-borders"></a>Разделители ячеек и границы

`Grid` Не включает функцию, которая рисует разделителей ячейки или границы. Тем не менее можно создать свой собственный.

[ **GridCellDividers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellDividers) демонстрируется определение дополнительных строк и столбцов, специально для тонкая `BoxView` элементов для имитации разделительной линии.

[ **GridCellBorders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellBorders) программа не создает дополнительные ячейки, но вместо этого выравнивает `BoxView` элементов в каждой ячейке, чтобы имитировать границы ячейки.

## <a name="almost-real-life-grid-examples"></a>Почти сетки примеры из реальной жизни

[ **KeypadGrid** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/KeypadGrid) примере используется `Grid` для отображения с клавиатурой:

[![Тройной снимок экрана сетки клавиатуре](images/ch17fg12-small.png "сетки клавиатуре")](images/ch17fg12-large.png#lightbox "клавиатуре сетки")

### <a name="responding-to-orientation-changes"></a>Реагирование на изменения ориентации

`Grid` Может помочь структура программы реагировать на изменение ориентации. [ **GridRgbSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridRgbSliders) примере показан способ, который перемещает элемент между второй строкой телефона ориентированных на книжной и альбомной ориентацией телефона во втором.

Инициализирует программу `Slider` элементов в диапазоне от 0 до 255, и привязок данных использует для отображения значения ползунков в шестнадцатеричном формате. Так как `Slider` значения представлены плавающими, точки, а также для шестнадцатеричного работает только с целыми числами, строку форматирования платформы .NET [ `DoubleToIntConvert` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DoubleToIntConverter.cs) в класс [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) библиотеки на помощь.



## <a name="related-links"></a>Связанные ссылки

- [Глава 17 полнотекстового поиска (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch17-Apr2016.pdf)
- [Примеры Глава 17](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)
- [Сетка](~/xamarin-forms/user-interface/layouts/grid.md)
