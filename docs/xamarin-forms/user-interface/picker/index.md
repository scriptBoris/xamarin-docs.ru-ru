---
title: Средство выбора Xamarin.Forms
description: Средство выбора Xamarin.Forms отображает краткий список элементов, из которых пользователь может выбрать элемент. В этой статье объясняется, как использовать класс выбора для выбора элемента из списка данных.
ms.prod: xamarin
ms.assetid: D4815A4B-104B-4294-951B-BD8F2EC33C86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/04/2018
ms.openlocfilehash: c852cd29197b000ed1ff53853d64cfa25fb699e7
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996601"
---
# <a name="xamarinforms-picker"></a>Средство выбора Xamarin.Forms

_Представление выбора является элемент управления для выбора элемента из списка данных._

Xamarin.Forms [ `Picker` ](xref:Xamarin.Forms.Picker) отображает короткий список элементов, из которых пользователь может выбрать элемент. `Picker` Определяет восемь свойств:

- [`Title`](xref:Xamarin.Forms.Picker.Title) типа `string`, который по умолчанию `null`.
- [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) типа `IList`, исходного списка отображаемых элементов, значение по умолчанию — `null`.
- [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) типа `int`, индекс выбранного элемента, значение по умолчанию — -1.
- [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) типа `object`, выбранный элемент, значение по умолчанию — `null`.
- [`TextColor`](xref:Xamarin.Forms.Picker.TextColor) типа [ `Color` ](xref:Xamarin.Forms.Color), цвет, используемый для отображения текста, по умолчанию — [ `Color.Default` ](xref:Xamarin.Forms.Color.Default).
- [`FontAttributes`](xref:Xamarin.Forms.Picker.FontAttributes) типа [ `FontAttributes` ](xref:Xamarin.Forms.FontAttributes), который по умолчанию [ `FontAtributes.None` ](xref:Xamarin.Forms.FontAttributes.None).
- [`FontFamily`](xref:Xamarin.Forms.Picker.FontFamily) типа `string`, который по умолчанию `null`.
- [`FontSize`](xref:Xamarin.Forms.Picker.FontSize) типа `double`, который по умолчанию -1,0.

Все восемь свойств обеспечиваются [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) объектов, что означает, что их можно применить различные стили, и свойства могут быть целями привязки данных. [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) И [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) свойства имеют режим привязки по умолчанию [ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay), что означает, что они могли быть целями привязок данных в приложении, которое использует [Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) архитектуры. Сведения о настройке свойств шрифта, см. в разделе [шрифты](~/xamarin-forms/user-interface/text/fonts.md).

Объект [ `Picker` ](xref:Xamarin.Forms.Picker) не отображает данные, при его первом отображении. Вместо этого значение его [ `Title` ](xref:Xamarin.Forms.Picker.Title) свойство отображается в виде рамки на платформах iOS и Android:

[![](images/picker-initial.png "Начальный экран выбора")](images/picker-initial-large.png#lightbox "первоначального отображения средства выбора")

Когда [ `Picker` ](xref:Xamarin.Forms.Picker) отображается фокус прибыли, данных и пользователь может выбрать элемент:

[![](images/picker-selection.png "При выборе элемента выбора")](images/picker-selection-large.png#lightbox "выбора элемента выбора")

[ `Picker` ](xref:Xamarin.Forms.Picker) Активируется [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged) событие, когда пользователь выбирает элемент. После выбора, выбранный элемент отображается с `Picker`:

![](images/picker-after-selection.png "Средство выбора после выбора")

Существует два способа заполнения [ `Picker` ](xref:Xamarin.Forms.Picker) с данными:

- Установка [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) свойство для отображения данных. Это рекомендуемый способ, который появился в Xamarin.Forms 2.3.4. Дополнительные сведения см. в разделе [задание свойства ItemsSource средства выбора](populating-itemssource.md).
- Добавление данных, которое будет отображаться для [ `Items` ](xref:Xamarin.Forms.Picker.Items) коллекции. Этот метод был исходный процесс заполнения [ `Picker` ](xref:Xamarin.Forms.Picker) с данными. Дополнительные сведения см. в разделе [Добавление данных в коллекцию элементов средства выбора](populating-items.md).

## <a name="related-links"></a>Связанные ссылки

- [Средство выбора](xref:Xamarin.Forms.Picker)
