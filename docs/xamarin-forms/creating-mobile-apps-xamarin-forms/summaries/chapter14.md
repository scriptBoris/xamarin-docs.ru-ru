---
title: Сводка Глава 14. Абсолютный макет
description: 'Создание мобильных приложений с помощью Xamarin.Forms: Сводка Глава 14. Абсолютный макет'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 88882A48-3226-42D1-96ED-241250B64A84
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 2dd94d5fb8eecc5cf4a3e376bc67c2cb6afb153b
ms.sourcegitcommit: 3e980fbf92c69c3dd737554e8c6d5b94cf69ee3a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935443"
---
# <a name="summary-of-chapter-14-absolute-layout"></a>Сводка Глава 14. Абсолютный макет

Как и `StackLayout`, [ `AbsoluteLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/) является производным от `Layout<View>` и наследует `Children` свойство. `AbsoluteLayout` реализует систему макета, которая требует от программиста для указания положения его дочерних элементов и, возможно, их размера. Позиция задается верхнего левого угла дочернего элемента относительно верхнего левого угла `AbsoluteLayout` в аппаратно независимых единицах. `AbsoluteLayout` также реализует пропорционально позиционирования и возможность изменения размера.

`AbsoluteLayout` следует рассматривать как система макета специального назначения для использования только в том случае, если программист может создать размер на дочерние элементы (например, `BoxView` элементы) или когда размер этого элемента не влияет на положение других дочерних элементов. `HorizontalOptions` И `VerticalOptions` свойства не оказывают влияния на дочерние элементы `AbsoluteLayout`.

В данной главе вводится также важной особенностью *присоединенного свойства связывания* , позволяющие настраивать свойства, определенные в одном классе (в данном случае `AbsoluteLayout`) должны быть присоединены к другой класс (является потомком `AbsoluteLayout`).

## <a name="absolutelayout-in-code"></a>AbsoluteLayout в коде

Можно добавить дочерний элемент `Children` коллекцию `AbsoluteLayout` с использованием стандарта [ `Add` ](https://developer.xamarin.com/api/member/System.Collections.Generic.ICollection%3CT%3E.Add/p/T/) метод, но `AbsoluteLayout` также предоставляет расширенную [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout+IAbsoluteList%3CT%3E.Add/p/Xamarin.Forms.View/Xamarin.Forms.Rectangle/Xamarin.Forms.AbsoluteLayoutFlags/) метод, который позволяет указать [ `Rectangle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Rectangle/). Другой [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout+IAbsoluteList%3CT%3E.Add/p/Xamarin.Forms.View/Xamarin.Forms.Point/) метод требует только [ `Point` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Point/), при этом дочерние неограниченного и изменяет свой размер.

Можно создать `Rectangle` со значением [конструктор](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Rectangle.Rectangle/p/System.Double/System.Double/System.Double/System.Double/) , требуется четыре значения &mdash; первые два, указывающий положение верхнего левого угла дочернего элемента относительно его родительского элемента, а вторые два, указывающее, размер дочернего элемента. Или можно использовать [конструктор](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Rectangle.Rectangle/p/Xamarin.Forms.Point/Xamarin.Forms.Size/) требующее `Point` и [ `Size` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Size/) значение.

Эти `Add` методы показаны в [ **AbsoluteDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteDemo), какие позиции `BoxView` элементов с помощью `Rectangle` значения и `Label` элемент, используя только что `Point` значение.

[ **ChessboardFixed** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardFixed) примере используется 32 `BoxView` элементы должны быть созданы в формате доске. Эта программа предоставляет `BoxView` размер элементов, жестко 35 единиц квадрата. `AbsoluteLayout` Имеет его `HorizontalOptions` и `VerticalOptions` присвоено `LayoutOptions.Center`, чего `AbsoluteLayout` на общий размер 280 единиц квадрата.

## <a name="attached-bindable-properties"></a>Вложенные привязываемые свойства

Можно также задать положение и, возможно, размер дочернего элемента `AbsoluteLayout` после его добавления к `Children` коллекции с помощью статического метода [ `AbsoluteLayout.SetLayoutBounds` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout.SetLayoutBounds/p/Xamarin.Forms.BindableObject/Xamarin.Forms.Rectangle/). Первым аргументом является дочерним элементом; второй — `Rectangle` объекта. Можно указать, что дочерние изменяет свой размер по горизонтали или по вертикали, задав значения высоты и ширины [ `AbsoluteLayout.AutoSize` ](https://developer.xamarin.com/api/property/Xamarin.Forms.AbsoluteLayout.AutoSize/) константы.

[ **ChessboardDynamic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardDynamic) пример помещает `AbsoluteLayout` в `ContentView` с `SizeChanged` обработчик для вызова `AbsoluteLayout.SetLayoutBounds` на все дочерние элементы, чтобы сделать их как можно большего размера.  

Присоединенное свойство привязки, `AbsoluteLayout` определяет является статическим полем только для чтения типа `BindableProperty` с именем [ `AbsoluteLayout.LayoutBoundsProperty` ](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty/). Статический `AbsoluteLayout.SetLayoutBounds` метод реализуется путем вызова `SetValue` дочерних элементов с `AbsoluteLayout.LayoutBoundsProperty`. Дочерние содержит словарь, в которой хранятся присоединенного привязываемые свойства и его значение. Во время структурирования `AbsoluteLayout` можно получить это значение путем вызова [ `AbsoluteLayout.GetLayoutBounds` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout.GetLayoutBounds/p/Xamarin.Forms.BindableObject/), которое реализуется с помощью `GetValue` вызова.

## <a name="proportional-sizing-and-positioning"></a>Пропорциональное изменения размеров и положения

`AbsoluteLayout` реализует пропорциональное изменения размеров и положения компонента. Класс определяет второй присоединенного свойства привязки, [ `LayoutFlagsProperty` ](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty/), со статическими методами связанных [ `AbsoluteLayout.SetLayoutFlags` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout.SetLayoutFlags/p/Xamarin.Forms.BindableObject/Xamarin.Forms.AbsoluteLayoutFlags/) и [ `AbsoluteLayout.GetLayoutFlags` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout.GetLayoutFlags/p/Xamarin.Forms.BindableObject/).

Аргумент `AbsoluteLayout.SetLayoutFlags` и возвращаемое значение `AbsoluteLayout.GetLayoutFlags` является значением типа [ `AbsoluteLayoutFlags` ](xref:Xamarin.Forms.AbsoluteLayoutFlags), перечисление со следующими членами:

- [`None`](xref:Xamarin.Forms.AbsoluteLayoutFlags.None) (равно 0)
- [`XProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.XProportional) (1)
- [`YProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.YProportional) (2)
- [`PositionProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.PositionProportional) (3)
- [`WidthProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.WidthProportional) (4)
- [`HeightProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.HeightProportional) (8)
- [`SizeProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.SizeProportional) (12)
- [`All`](xref:Xamarin.Forms.AbsoluteLayoutFlags.All) (\xFFFFFFFF)

Вы можете комбинировать эти с C# побитового оператора OR.

Эти флаги установлены, определенные свойства `Rectangle` структуре границы макета, можно изменить положение и размер дочернего интерпретируются пропорционально.

Когда `WidthProportional` флаг установлен, `Width` значение 1 означает, что такой же ширины, что дочерние `AbsoluteLayout`. Подобный подход используется для высоты.

Пропорциональное позиционирования учитывает размер. Когда `XProportional` флаг установлен, `X` свойство `Rectangle` пропорционально границы макета. Значение 0 означает, что дочерние левого края расположен над левым краем `AbsoluteLayout`, но позиция 1 означает, что правым краем дочернего элемента расположен на правом краю `AbsoluteLayout`, не за правый край `AbsoluteLayout` как это делается expec t. `X` Свойство 0,5 выравнивание по горизонтали в дочерние `AbsoluteLayout`.

[ **ChessboardProportional** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardProportional) образце демонстрируется использование пропорциональное изменения размера и положения.

## <a name="working-with-proportional-coordinates"></a>Работа с пропорциональным координаты

В некоторых случаях проще рассматривать пропорционально позиционирования иначе, чем его реализации в `AbsoluteLayout`. Вы можете работать с пропорциональным координаты где `X` свойство 1 помещает левым краем дочернего элемента (а не правого края) от правого края `AbsoluteLayout`.

Этой альтернативной схемой размещения может вызываться «долей дочернего координаты». Можно преобразовать из координат долей дочернего границы макета, необходимые для `AbsoluteLayout` со следующими формулами:

layoutBounds.X = (fractionalChildCoordinate.X / (1 - layoutBounds.Width))

layoutBounds.Y = (fractionalChildCoordinate.Y / (1 - layoutBounds.Height))

[ **ProportionalCoordinateCalc** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/PropCoordCalc) в нем демонстрируется это.

## <a name="absolutelayout-and-xaml"></a>AbsoluteLayout и XAML

Можно использовать `AbsoluteLayout` в XAML и задать присоединенное привязываемые свойства для дочерних элементов `AbsoluteLayout` с помощью значения атрибутов, `AbsoluteLayout.LayoutBounds` и `AbsoluteLayout.LayoutFlags`. Это показано в [ **AbsoluteXamlDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteXamlDemo) и [ **ChessboardXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardXaml) примеры. Последний программа содержит 32 `BoxView` элементы только использует неявный `Style` , включающий `AbsoluteLayout.LayoutFlags` свойство для сохранения разметки вниз к минимуму.

Атрибут в XAML, который состоит из имени класса, точки и имени свойства — *всегда* присоединенное свойство привязки.

## <a name="overlays"></a>Наложения

Можно использовать `AbsoluteLayout` для создания *наложения*, где приведены все страницы с другими элементами управления, возможно для защиты пользователя от взаимодействия с помощью обычных элементов управления на странице.

[ **SimpleOverlay** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/SimpleOverlay) примере продемонстрировано использование этого приема, а также демонстрирует [ `ProgressBar` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ProgressBar/), отображающий экстента, к которому завершения программы задача.

## <a name="some-fun"></a>Забавные

[ **DotMatrixClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/DotMatrixClock) пример отображает текущее время с отображением имитации 5 x 7 матричного. Каждая точка является `BoxView` (есть 228 из них) изменять размеры и расположение на `AbsoluteLayout`.

[![Тройной снимок экрана часов матричного](images/ch14fg08-small.png "часы матричного")](images/ch14fg08-large.png#lightbox "матричного часов")

[ **BouncingText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/BouncingText) программы анимирует два `Label` объектов к экрану скачками по горизонтали и вертикали.



## <a name="related-links"></a>Связанные ссылки

- [Глава 14 полнотекстового поиска (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch14-Apr2016.pdf)
- [Глава 14-примеры](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)
- [AbsoluteLayout](~/xamarin-forms/user-interface/layouts/absolute-layout.md)
