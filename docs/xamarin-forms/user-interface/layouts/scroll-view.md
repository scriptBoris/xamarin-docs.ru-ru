---
title: Xamarin.Forms ScrollView
description: В этой статье объясняется, как использовать класс Xamarin.Forms ScrollView для представления макеты, не может поместиться на экран только один, и которые имеют содержимое освободить место для клавиатуры.
ms.prod: xamarin
ms.assetid: 7B542872-B3D1-49B3-B15E-0E98F53C1F6E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 34339b9ca3a15c7f7f24edee5401c542fd09ba74
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53048991"
---
# <a name="xamarinforms-scrollview"></a>Xamarin.Forms ScrollView

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)

[`ScrollView`](xref:Xamarin.Forms.ScrollView) содержит макеты и позволяет им прокрутки вне экрана. `ScrollView` также позволяет разрешить представления автоматически перейти на видимой области экрана, когда отображается клавиатуры.

[![](scroll-view-images/layouts-sml.png "Макеты Xamarin.Forms")](scroll-view-images/layouts.png#lightbox "макеты Xamarin.Forms")

В этой статье рассматриваются следующие действия:

- **[Назначение](#purpose)**  &ndash; цели и задачи `ScrollView` и когда она используется.
- **[Использование](#usage)**  &ndash; способы использования `ScrollView` на практике.
- **[Свойства](#properties)**  &ndash; открытых свойств, которые можно считывать и изменять.
- **[Методы](#methods)**  &ndash; открытые методы, которые могут быть вызваны для прокручивания представления.
- **[События](#events)**  &ndash; события, которые могут использоваться для прослушивания изменения состояния представления.

## <a name="purpose"></a>Цель

`ScrollView` можно использовать для обеспечения отображения большего представления хорошо на телефонах меньшего размера. Например макет, который работает на устройстве iPhone 6s могут быть обрезаны на iPhone 4s. С помощью `ScrollView` позволит усеченные части макет для отображения на экране меньшего размера.

## <a name="usage"></a>Использование

> [!NOTE]
> `ScrollView`s не должен быть вложенным. Кроме того `ScrollView`s не должен быть вложенным с другими элементами управления, которые предоставляют прокрутка, подобно `ListView` и `WebView`.

`ScrollView` предоставляет `Content` свойство, которое может быть присвоено одно представление или макета. Рассмотрим следующий пример макета с очень больших boxView, за которым следует `Entry`:

```xaml
<ContentPage.Content>
    <ScrollView>
        <StackLayout>
            <BoxView BackgroundColor="Red" HeightRequest="600" WidthRequest="150" />
            <Entry />
        </StackLayout>
    </ScrollView>
</ContentPage.Content>
```

В C#:

```csharp
var scroll = new ScrollView();
Content = scroll;
var stack = new StackLayout();
stack.Children.Add(new BoxView { BackgroundColor = Color.Red,    HeightRequest = 600, WidthRequest = 600 });
stack.Children.Add(new Entry());
```

Прежде чем пользователь выполняет прокрутку вниз, только `BoxView` отображается:

![](scroll-view-images/scroll-start.png "BoxView в ScrollView")

Обратите внимание, что, когда пользователь начинает вводить текст в `Entry`, представление прокручивается, чтобы закрепить на экране:

![](scroll-view-images/scroll-end.png "Запись в ScrollView")

## <a name="properties"></a>Свойства

`ScrollView` определяет следующие свойства:

- [`ContentSize`](xref:Xamarin.Forms.ScrollView.ContentSizeProperty) Получает [ `Size` ](xref:Xamarin.Forms.Size) значение, представляющее размер содержимого.
- [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) Возвращает или задает [ `ScrollOrientation` ](xref:Xamarin.Forms.ScrollOrientation) значение перечисления, представляющее направление прокрутки `ScrollView`.
- [`ScrollX`](xref:Xamarin.Forms.ScrollView.ScrollXProperty) Возвращает `double` , представляющий текущий позиция прокрутки по оси Х.
- [`ScrollY`](xref:Xamarin.Forms.ScrollView.ScrollYProperty) Возвращает `double` , представляющий текущую позицию прокрутки Y.
- [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibilityProperty) Возвращает или задает [ `ScrollBarVisibility` ](xref:Xamarin.Forms.ScrollBarVisibility) значение, представляющее горизонтальная полоса прокрутки видима.
- [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.VerticalScrollBarVisibilityProperty) Возвращает или задает [ `ScrollBarVisibility` ](xref:Xamarin.Forms.ScrollBarVisibility) значение, представляющее вертикальная полоса прокрутки видима.

## <a name="methods"></a>Методы

`ScrollView` предоставляет `ScrollToAsync` метод, который может использоваться для прокручивания представления с помощью координат или укажите конкретное представление, которое следует сделать видимой.

При использовании координаты, указать `x` и `y` координаты, а также логическое значение, указывающее, является ли прокрутка анимации:

```csharp
scroll.ScrollToAsync(0, 150, true); //scrolls so that the position at 150px from the top is visible

scroll.ScrollToAsync(label, ScrollToPosition.Start, true); //scrolls so that the label is at the start of the list
```

Во время прокрутки с определенным элементом `ScrollToPosition` перечисления указывает, где в представление элемента будет отображаться:

- **Center** &ndash; прокручивает элемент относительно центральной части видимой части представления.
- **Конец** &ndash; прокручивает элемент в конец видимой части представления.
- **MakeVisible** &ndash; прокручивает элемент, чтобы он отображается в представлении.
- **Запуск** &ndash; прокручивает элемент в начало видимой части представления.

`IsAnimated` Свойство определяет, как представление прокручивается. Если задано значение true, создавать плавную анимацию будет использоваться, вместо того, чтобы мгновенно Перемещение содержимого в представление.

## <a name="events"></a>События

`ScrollView` определяет только одно событие `Scrolled`. `Scrolled` вызывается после завершения прокрутки представления. Обработчик событий для `Scrolled` принимает `ScrolledEventArgs`, который имеет `ScrollX` и `ScrollY` свойства. Следующий подход демонстрирует обновление метки с использованием текущей позиции прокрутки `ScrollView`:

```csharp
Label label = new Label { Text = "Position: " };
ScrollView scroll = new ScrollView();
scroll.Scrolled += (object sender, ScrolledEventArgs e) => {
    label.Text = "Position: " + e.ScrollX + " x " + e.ScrollY;
};
```

Обратите внимание, что позиции прокрутки может быть отрицательным, из-за эффекта bounce при прокрутке в конце списка.


## <a name="related-links"></a>Связанные ссылки

- [Макет (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [Пример BusinessTumble (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
