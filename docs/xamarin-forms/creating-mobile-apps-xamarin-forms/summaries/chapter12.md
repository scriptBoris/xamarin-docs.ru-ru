---
title: Сводка Глава 12. Стили
description: 'Создание мобильных приложений с помощью Xamarin.Forms: Сводка Глава 12. Стили'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3EAE6BDC-8EFB-464B-A87B-1C35B8387BB3
author: charlespetzold
ms.author: chape
ms.date: 07/19/2018
ms.openlocfilehash: 8ee169d15c4b5060f2a7696bfebd314ed7029570
ms.sourcegitcommit: 8555a4dd1a579b2206f86c867125ee20fbc3d264
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39156945"
---
# <a name="summary-of-chapter-12-styles"></a>Сводка Глава 12. Стили

В Xamarin.Forms стили позволяют несколько представлений для совместно используют одну коллекцию параметров свойств. Это уменьшает разметки и обеспечивает обслуживание согласованных визуальные темы.

Стили почти всегда определяются и используются в разметке. Объект типа [ `Style` ](xref:Xamarin.Forms.Style) создается в словаре ресурсов и укажите значение [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) свойства элемента визуального элемента с использованием `StaticResource` или `DyanamicResource` разметки расширение.

## <a name="the-basic-style"></a>Основной стиль

Объект `Style` требует его [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) присвоить тип визуального объекта, он применяется к. Когда `Style` создается в словаре ресурсов (что характерно) также требуется `x:Key` атрибута.

`Style` Имеет свойство content от типа [ `Setters` ](xref:Xamarin.Forms.Style.Setters), который является коллекцией [ `Setter` ](xref:Xamarin.Forms.Setter) объектов. Каждый `Setter` связывает [ `Property` ](xref:Xamarin.Forms.Setter.Property) с [ `Value` ](xref:Xamarin.Forms.Setter.Value).

В XAML `Property` задано имя свойства CLR (такие как `Text` свойство `Button`), но может быть привязано свойство оформленного должно использовать. Кроме того, свойство должно быть определено в классе, обозначается `TargetType` параметр или наследуемые этим классом.

Можно указать `Value` Настройка с помощью элемента свойства `<Setter.Value>`. Это позволяет задать `Value` на объект, не могут быть выражены в текстовую строку или `OnPlatform` объекта, или на объект создан с помощью `x:Arguments` или `x:FactoryMethod`. `Value` Свойство также может устанавливаться с `StaticResource` выражения в другой элемент в словаре.

[ **BasicStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyle) программы показан основной синтаксис и показано, как ссылаться на `Style` с `StaticResource` расширение разметки:

[![Тройной снимок экрана: основной стиль](images/ch12fg01-small.png "основные стили")](images/ch12fg01-large.png#lightbox "основные стили")

`Style` Объекта и любой объект, созданный в `Style` объекта в виде `Value` параметр являются общими для всех представлений, ссылаясь на него `Style`. `Style` Не может содержать те же элементы, который нельзя использовать совместно, такие как `View` производных продуктов.

Обработчики событий не может задаваться в `Style`. `GestureRecognizers` Свойство не может быть установлено в `Style` , так как он не поддерживается, может быть привязано.

## <a name="styles-in-code"></a>Стили в коде

Несмотря на то, что не часто, можно создать и инициализировать `Style` объектов в коде. Это демонстрируется путем [ **BasicStyleCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyleCode) образца.

## <a name="style-inheritance"></a>Наследование стилей

`Style` имеет [ `BasedOn` ](xref:Xamarin.Forms.Style.BasedOn) свойство, которое можно задать `StaticResource` расширение разметки, ссылающиеся на другой стиль. Это позволяет стили, чтобы наследовать от предыдущего стили при добавлении или удалении параметров свойств. [ **StyleInheritance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleInheritance) в нем демонстрируется это.

Если `Style2` основан на `Style1`, `TargetType` из `Style2` должен быть таким же, как `Style1` или производный от `Style1`. Словарь ресурсов, в котором `Style1` хранится должен быть один словарь ресурсов как `Style2` или более поздней версии в визуальном дереве словаря ресурсов.

## <a name="implicit-styles"></a>Неявные стили

Если `Style` в ресурсе словаря имеет `x:Key` атрибута, ключ словаря оно назначается автоматически и `Style` объект становится *неявный стиль*. Представление без `Style` параметр, тип которых соответствует `TargetType` точно найдет этот стиль как [ **ImplicitStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/ImplicitStyle) в нем демонстрируется.

Неявный стиль может быть производным от `Style` с `x:Key` параметр, но не наоборот. Неявный стиль нельзя сослаться явно.

Можно реализовать три вида иерархии с использованием стилей и `BasedOn`:

- Из стили, определенные на `Application` и `Page` вниз, чтобы стили, определенные на макетах более низкого уровня в визуальном дереве.
- Из стили, определенные для базовых классов, таких как `VisualElement` и `View` для стили, определенные для специализированных классов.
- Из стилей с помощью явных словарь ключей для неявных стилей.

Демонстрируются эти иерархии в [ **StyleHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleHierarchy) образца.

## <a name="dynamic-styles"></a>Динамические стили

Позволяет ссылаться на стиль в словаре ресурсов `DynamicResource` вместо `StaticResource`. В результате стиль *динамического стиля*. Если этот стиль заменяется в словаре ресурсов другой стиль с тем же ключом, представления, ссылающиеся на этот стиль с `DynamicResource` автоматического изменения. Кроме того, приведет к отсутствие записи с указанным ключом в словаре `StaticResource` для вызова исключения, но не `DynamicResource`.

Этот метод можно использовать для динамического изменения стиля или тем, как [ **DynamicStyles** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynamicStyles) в нем демонстрируется.

Тем не менее, нельзя задать `BasedOn` свойства `DynamicResource` состав расширения поскольку `BasedOn` не поддерживается, может быть привязано. Для наследования стиля динамически, не устанавливайте `BasedOn`. Вместо этого задайте [ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey) значение ключа словаря требуется являются производными от стиля. [ **DynamicStylesInheritance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynaStylesInh) этот метод продемонстрирован в примере.

## <a name="device-styles"></a>Стили устройства

[ `Device.Styles` ](xref:Xamarin.Forms.Device.Styles) Вложенный класс определяет двенадцать статические поля только для чтения для шести стилей с `TargetType` из `Label` , можно использовать для распространенных типов использования текста.

Шесть из этих полей относятся к типу `Style` , можно задать непосредственно в `Style` свойства в коде:

- [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle)
- [`TitleStyle`](xref:Xamarin.Forms.Device.Styles.TitleStyle)
- [`SubtitleStyle`](xref:Xamarin.Forms.Device.Styles.SubtitleStyle)
- [`CaptionStyle`](xref:Xamarin.Forms.Device.Styles.CaptionStyle)
- [`ListItemTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyle)
- [`ListItemDetailTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyle)

Остальные шесть поля имеют тип `string` и может использоваться в качестве ключей словаря для динамические стили:

- [`BodyStyleKey`](xref:Xamarin.Forms.Device.Styles.BodyStyleKey) равным «BodyStyle»
- [`TitleStyleKey`](xref:Xamarin.Forms.Device.Styles.TitleStyleKey) равным «TitleStyle»
- [`SubtitleStyleKey`](xref:Xamarin.Forms.Device.Styles.SubtitleStyleKey) равным «SubtitleStyle»
- [`CaptionStyleKey`](xref:Xamarin.Forms.Device.Styles.CaptionStyleKey) равным «CaptionStyle»
- [`ListItemTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyleKey) равным «ListItemTextStyle»
- [`ListItemDetailTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyleKey) равным «ListItemDetailTextStyle»

Показаны эти стили [ **DeviceStylesList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DeviceStylesList) образца.

## <a name="related-links"></a>Связанные ссылки

- [Глава 12 полнотекстового поиска (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch12-Apr2016.pdf)
- [Глава 12 выборок](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)
- [Стили](~/xamarin-forms/user-interface/styles/index.md)
