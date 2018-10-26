---
title: Программные ограничения макета в Xamarin.iOS
description: В этом руководстве представлен работа с iOS ограничения автоматический макет в C# кода не создаются в конструкторе iOS.
ms.prod: xamarin
ms.assetid: 119C8365-B470-4CD4-85F7-086F0A46DCBB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 3d8e69af7f790415343abf464ea2bb22e879e025
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106965"
---
# <a name="programmatic-layout-constraints-in-xamarinios"></a>Программные ограничения макета в Xamarin.iOS

_В этом руководстве представлен работа с iOS ограничения автоматический макет в C# кода не создаются в конструкторе iOS._

Автоматический макет (также называется «адаптивный макет») — это гибкий Дизайн подход. В отличие от системы transitional макета, где каждый элемент расположение — это жестко в точку на экране, автоматический макет посвящен *связи* -позиции элементов относительно других элементов в рабочей области конструирования. В сердце автоматический макет является идея ограничения или правила, определяющие расположение элемента или набора элементов в контексте других элементов на экране. Так как элементы не привязаны к конкретной должности на экране, ограничения помогают создать адаптивный макет, который хорошо выглядит на экранах различных размеров и ориентации устройства.

Обычно при работе с автоматический макет в iOS, вы будете использовать конструктор iOS графически разместить ограничения макета элементов пользовательского интерфейса. Тем не менее, возможны и такие ситуации, когда вам нужно создать и применить ограничения в C# кода. Например, при использовании динамически созданных элементов пользовательского интерфейса, добавляемый `UIView`.

В этом руководстве показано, как создавать и работать с помощью ограничения C# кода, вместо того чтобы создавать их графически в конструкторе iOS.

<a name="Creating-Constraints-Programmatically" />

## <a name="creating-constraints-programmatically"></a>Создание ограничения программным способом

Как уже говорилось, обычно вы будете работать с ограничениями автоматического макета в конструкторе iOS. Для случаев, которые необходимо выполнять программным способом создать вашими ограничениями у вас есть три варианта:

* [Макет привязки](#Layout-Anchors) -этот API предоставляет доступ к свойствам привязки (таких как `TopAnchor`, `BottomAnchor` или `HeightAnchor`) элементов пользовательского интерфейса ограничены.
* [Ограничения макета](#Layout-Constraints) -можно создать напрямую с помощью ограничения `NSLayoutConstraint` класса.
* [Язык Visual форматирование](#Visual-Format-Language) -предоставляет искусством ASCII, как и метод для определения ограничения.

Следующие разделы будут рассмотрены каждого параметра подробно.

<a name="Layout-Anchors" />

### <a name="layout-anchors"></a>Макет привязки

С помощью `NSLayoutAnchor` класс, у вас есть переменный интерфейс для создания на основе привязки свойств элементов пользовательского интерфейса, испытывая ограничения. Например, контроллер представления макета верхней и нижней частях руководство предоставляет `TopAnchor`, `BottomAnchor` и `HeightAnchor` свойства привязки, хотя представления предоставляет свойства edge, center, размер и базовых показателей.

> [!IMPORTANT]
> Помимо стандартного набора свойств привязки, операций ввода-вывода также включает в себя `LayoutMarginsGuides` и `ReadableContentGuide` свойства. Эти свойства предоставляют `UILayoutGuide` объектов для работы с поля представления и для чтения содержимого руководства по соответственно.

Макет привязки предоставляют несколько методов для создания ограничений в удобный для чтения компактном формате:

- **ConstraintEqualTo** -определяет связь где `first attribute = second attribute + [constant]` с при необходимости заданный `constant` значение смещения.
- **ConstraintGreaterThanOrEqualTo** -определяет связь где `first attribute >= second attribute + [constant]` с при необходимости заданный `constant` значение смещения.
- **ConstraintLessThanOrEqualTo** -определяет связь где `first attribute <= second attribute + [constant]` с при необходимости заданный `constant` значение смещения.

Пример:

```csharp
// Get the parent view's layout
var margins = View.LayoutMarginsGuide;

// Pin the leading edge of the view to the margin
OrangeView.LeadingAnchor.ConstraintEqualTo (margins.LeadingAnchor).Active = true;

// Pin the trailing edge of the view to the margin
OrangeView.TrailingAnchor.ConstraintEqualTo (margins.TrailingAnchor).Active = true;

// Give the view a 1:2 aspect ratio
OrangeView.HeightAnchor.ConstraintEqualTo (OrangeView.WidthAnchor, 2.0f);
```

Типичным ограничением макет может быть выражен просто как линейной выражение. Рассмотрим следующий пример:

[![](programmatic-layout-constraints-images/graph01.png "Ограничение макета, выраженное как линейной выражение")](programmatic-layout-constraints-images/graph01.png#lightbox)

Который будет преобразован в следующую строку C# кода, используя макет привязки:

```csharp
PurpleView.LeadingAnchor.ConstraintEqualTo (OrangeView.TrailingAnchor, 10).Active = true; 
```

Где части C# код соответствует данной части формулы следующим образом:

|Уравнение|Код|
|---|---|
|Элемент 1|PurpleView|
|Атрибут 1|LeadingAnchor|
|Relationship|ConstraintEqualTo|
|Множитель|По умолчанию используется 1.0 таким образом не указан|
|Элемент 2|OrangeView|
|Атрибут 2|TrailingAnchor|
|Константа|10.0|

Помимо только параметры, необходимые для решения уравнении ограничения заданного макета, каждый из методов привязки макета обеспечивать безопасность типов параметров, передаваемых в них. Поэтому горизонтальной ограничение привязывает например `LeadingAnchor` или `TrailingAnchor` может использоваться только с другими горизонтальной привязки типов и множители предоставляются только для ограничений по объему.

<a name="Layout-Constraints" />

### <a name="layout-constraints"></a>Ограничения макета

Ограничения автоматического макета можно добавить вручную, напрямую создав `NSLayoutConstraint` в C# кода. В отличие от использования макета привязки, даже если он не повлияет на ограничение, определенное необходимо указать значение для каждого параметра. В результате получится создания значительное трудно читать, стандартный код. Пример:

```csharp
//// Pin the leading edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Leading, NSLayoutRelation.Equal, View, NSLayoutAttribute.LeadingMargin, 1.0f, 0.0f).Active = true;

//// Pin the trailing edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Trailing, NSLayoutRelation.Equal, View, NSLayoutAttribute.TrailingMargin, 1.0f, 0.0f).Active = true;

//// Give the view a 1:2 aspect ratio
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Height, NSLayoutRelation.Equal, OrangeView, NSLayoutAttribute.Width, 2.0f, 0.0f).Active = true;
```

Где `NSLayoutAttribute` перечисления определяет значение для поля представления и соответствуют `LayoutMarginsGuide` свойства, такие как `Left`, `Right`, `Top` и `Bottom` и `NSLayoutRelation` перечисление определяет связи, будет создан между заданными атрибутами, как `Equal`, `LessThanOrEqual` или `GreaterThanOrEqual`.

В отличие от с помощью API привязки макета, `NSLayoutConstraint` методы создания не выделять важные аспекты ограничения и существуют некомпилируемый время проверки, выполняемые для ограничения. Таким образом это просто создать недопустимое ограничение, приведет к возникновению исключения во время выполнения.

<a name="Visual-Format-Language" />

### <a name="visual-format-language"></a>Язык Visual формата

Visual языка можно задать ограничения с помощью искусство ASCII, например, строки, которые дают визуальное представление создаваемого ограничения. Это имеет следующие преимущества и недостатки:

- Язык Visual формата обеспечивает создание только допустимые ограничения.
 - Автоматический макет выводит ограничения в консоль, используя Visual языка, поэтому сообщения отладки будут похожи на код, используемый для создания ограничения.
 - Visual языка позволяет создавать несколько ограничений в то же время очень компактные выражением.
 - Так как нет проверки на стороне компиляции языку формат строк, проблемы могут быть обнаружены только во время выполнения.
 - Поскольку Visual языка особое внимание уделяется визуализации по полноте, некоторые типы ограничений не может быть создан с ним (например, коэффициенты).

При использовании Visual языка для создания ограничения, выполните следующие действия:

1. Создание `NSDictionary` , содержащий представление объектов и направляющих макета и строковый ключ, который будет использоваться при определении форматы.
2. При необходимости создайте `NSDictionary` , определяющее набор ключей и значений (`NSNumber`) использоваться как значение константы для ограничения.
3. Создайте строку формата для макета одного столбца или строки элементов.
4. Вызовите `FromVisualFormat` метод `NSLayoutConstraint` класса для создания ограничения.
5. Вызовите `ActivateConstraints` метод `NSLayoutConstraint` класса, чтобы активировать и применить ограничения.

Например чтобы создать начальные и конечные ограничение в Visual языка, можно использовать следующие:

```csharp
// Get views being constrained
var views = new NSMutableDictionary (); 
views.Add (new NSString ("orangeView"), OrangeView);

// Define format and assemble constraints
var format = "|-[orangeView]-|";
var constraints = NSLayoutConstraint.FromVisualFormat (format, NSLayoutFormatOptions.AlignAllTop, null, views);

// Apply constraints
NSLayoutConstraint.ActivateConstraints (constraints);
```

Поскольку Visual языка всегда создает ноль точки ограничения для полей родительского представления при использовании интервал по умолчанию, этот код выдает одинаковый результат для приведенных выше примерах.

Для более сложных конструкций пользовательского интерфейса, такие как несколько дочерних представлений в одной строке Visual языка задает интервал по горизонтали и по вертикали. Как показано в примере выше, где он задает `AlignAllTop` `NSLayoutFormatOptions` выравнивание всех представлений в строки или столбца, чтобы их верхних границ.

Apple см. в разделе [Visual приложение языка формат](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1) некоторые примеры указания общие ограничения и Visual грамматики в формате строки.

<a name="Summary" />

## <a name="summary"></a>Сводка

В этом руководстве представлены Создание и работа с ограничениями автоматический макет в C# в отличие от создания их графически в конструкторе iOS. Во-первых, хотя рассматривались с помощью привязки макета (`NSLayoutAnchor`) для обработки автоматический макет. Затем он показал, как работать с ограничениями макета (`NSLayoutConstraint`). Наконец оно представлено на языке Visual формат для автоматический макет.

## <a name="related-links"></a>Связанные ссылки

- [Введение в раскадровку](~/ios/user-interface/storyboards/index.md)
- [Проектирование Пошаговое руководство элементы управления iOS](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Автоматический макет в конструкторе Xamarin для iOS](~/ios/user-interface/designer/designer-auto-layout.md#modifying-in-code)
- [Apple - программного создания ограничений](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html#//apple_ref/doc/uid/TP40010853-CH16-SW1)
- [Apple - приложении визуальный формат языка](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1)
