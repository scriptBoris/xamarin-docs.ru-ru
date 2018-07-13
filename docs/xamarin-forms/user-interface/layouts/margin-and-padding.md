---
title: Поля и заполнение
description: Поля и свойства заполнения управлять поведением макета, когда элемент отображается в пользовательском интерфейсе. В этой статье показано различие между двумя свойствами, а также об их настройке.
ms.prod: xamarin
ms.assetid: BEB096BB-51DF-410F-B0F1-D235287B0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 595e673c59d23a45cbaf923a0d58faff2000c296
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996620"
---
# <a name="margin-and-padding"></a>Поля и заполнение

_Поля и свойства заполнения управлять поведением макета, когда элемент отображается в пользовательском интерфейсе. В этой статье показано различие между двумя свойствами, а также об их настройке._

## <a name="overview"></a>Обзор

Поля и заполнение — макет связанные понятия:

- [ `Margin` ](xref:Xamarin.Forms.View.Margin) Свойство представляет расстояние между элементом и его соседние элементы и используется для управления положением отрисовки элемента и положения своих соседей. `Margin` значение должно лежать в [макета](~/xamarin-forms/user-interface/controls/layouts.md) и [представление](~/xamarin-forms/user-interface/controls/views.md) классы.
- [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) Свойство представляет расстояние между элементом и его дочерние элементы и используется для разделения его собственного содержимого элемента управления. `Padding` значение должно лежать в [макета](~/xamarin-forms/user-interface/controls/layouts.md) классы.

На следующей схеме эти два понятия:

[![](margin-and-padding-images/margins-and-padding-sml.png "Поля и заполнение понятия")](margin-and-padding-images/margins-and-padding.png#lightbox "поля и заполнение основные понятия")

Обратите внимание, что [ `Margin` ](xref:Xamarin.Forms.View.Margin) значения являются аддитивными. Таким образом Если два соседних элемента задать поля 20 точек, расстояние между элементами будет 40 пикселей. Кроме того, поля и заполнение являются аддитивный, если оба применяются, в том, что расстояние между элементом и любое содержимое будет иметь поля, а также заполнения.

## <a name="specifying-a-thickness"></a>Указание толщину

[ `Margin` ](xref:Xamarin.Forms.View.Margin) И [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) свойства имеют тип [ `Thickness` ](xref:Xamarin.Forms.Thickness). Возможны три варианта при создании `Thickness` структуры:

- Создание [ `Thickness` ](xref:Xamarin.Forms.Thickness) структуры, определяемой одно значение универсального кода. Одно значение применяется к левой, верхней, правой и нижней сторон элемента.
- Создание [ `Thickness` ](xref:Xamarin.Forms.Thickness) структуры, определяемой по горизонтали и вертикали значениям. Значением по вертикали симметрично, применяемая к верхней и нижней сторон элемента левой и правой сторон элемента, симметрично применяется значение по горизонтали.
- Создание [ `Thickness` ](xref:Xamarin.Forms.Thickness) структуры, определяемой четыре разных значения, которые применяются к левой, верхней, правой и нижней сторон элемента.

В следующем примере кода XAML показаны все три варианта:

```xaml
<StackLayout Padding="0,20,0,0">
  <Label Text="Xamarin.Forms" Margin="20" />
  <Label Text="Xamarin.iOS" Margin="10, 15" />
  <Label Text="Xamarin.Android" Margin="0, 20, 15, 5" />
</StackLayout>
```

Эквивалентный код на языке C# показан в следующем примере:

```csharp
var stackLayout = new StackLayout {
  Padding = new Thickness(0,20,0,0),
  Children = {
    new Label { Text = "Xamarin.Forms", Margin = new Thickness (20) },
    new Label { Text = "Xamarin.iOS", Margin = new Thickness (10, 25) },
    new Label { Text = "Xamarin.Android", Margin = new Thickness (0, 20, 15, 5) }
  }
};
```

> [!NOTE]
> `Thickness` значения могут быть отрицательным, который обычно отсекает или обрезку элементов содержимого.

## <a name="summary"></a>Сводка

В этой статье показано различие между [ `Margin` ](xref:Xamarin.Forms.View.Margin) и [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) свойства и их настройке. Свойства управления поведением макета, когда элемент отображается в пользовательском интерфейсе.


## <a name="related-links"></a>Связанные ссылки

- [Поле](xref:Xamarin.Forms.View.Margin)
- [Заполнение](xref:Xamarin.Forms.Layout.Padding)
- [Толщина](xref:Xamarin.Forms.Thickness)
