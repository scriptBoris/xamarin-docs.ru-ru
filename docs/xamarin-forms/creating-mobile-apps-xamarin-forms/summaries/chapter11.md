---
title: Сводка Глава 11. Инфраструктура связывания
description: 'Создание мобильных приложений с помощью Xamarin.Forms: Сводка Глава 11. Инфраструктура связывания'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 34671C48-0ED4-4B76-A33D-D6505390DC5B
author: charlespetzold
ms.author: chape
ms.date: 07/19/2018
ms.openlocfilehash: 9f3c077d7bae3557178236b81073afaf4892a272
ms.sourcegitcommit: 8555a4dd1a579b2206f86c867125ee20fbc3d264
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39156565"
---
# <a name="summary-of-chapter-11-the-bindable-infrastructure"></a>Сводка Глава 11. Инфраструктура связывания

Все программисты на C# знакомы с C# *свойства*. Свойства содержат *задать* метода доступа и/или *получить* метода доступа. Они часто называются *свойства CLR* для среды CLR.

Xamarin.Forms определяет определение расширенные свойства с именем *свойство, используемое* инкапсулируется [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) класса и поддерживается [ `BindableObject` ](xref:Xamarin.Forms.BindableObject)класса. Эти классы являются родственная, но довольно различные: `BindableProperty` используется для определения свойства. `BindableObject` аналогичен `object` тем, что он является базовым классом для классов, которые определяют связываемые свойства.

## <a name="the-xamarinforms-class-hierarchy"></a>Иерархия классов Xamarin.Forms

[ **ClassHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/ClassHierarchy) образец использует отражение для отображения иерархии класс Xamarin.Forms и демонстрации важную роль, играемая `BindableObject` в этой иерархии. `BindableObject` является производным от `Object` и является родительским классом для [ `Element` ](xref:Xamarin.Forms.Element) откуда [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) является производным. Это является родительским классом для [ `Page` ](xref:Xamarin.Forms.Page) и [ `View` ](xref:Xamarin.Forms.View), который является родительским классом для [ `Layout` ](xref:Xamarin.Forms.Layout):

[![Тройной снимок экрана общего доступа к иерархии классов](images/ch11fg01-small.png "общий доступ к иерархии класс")](images/ch11fg01-large.png#lightbox "общий доступ к иерархии классов")

## <a name="a-peek-into-bindableobject-and-bindableproperty"></a>Считывания в BindableObject и BindableProperty

В классах, производных от `BindableObject` многие свойства CLR, называются «поддерживаться» связываемые свойства. Например [ `Text` ](xref:Xamarin.Forms.Label.Text) свойство `Label` класс является свойством CLR, но `Label` класс также определяет открытое статическое доступное только для чтения поле с именем [ `TextProperty` ](xref:Xamarin.Forms.Label.TextProperty) из Тип `BindableProperty`.

Приложение может задать или получить `Text` свойство `Label` как правило, или приложение может задать `Text` путем вызова [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) метода, определенного `BindableObject` с `Label.TextProperty` аргумент. Аналогичным образом, приложение может получить значение `Text` свойства путем вызова [ `GetValue` ](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty)) метод, с использованием `Label.TextProperty` аргумент. Это демонстрируется путем [ **PropertySettings** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PropertySettings) образца.

Действительно `Text` полностью свойство CLR реализуется с помощью `SetValue` и `GetValue` методы, определенные `BindableObject` в сочетании с `Label.TextProperty` статическое свойство.

`BindableObject` и `BindableProperty` обеспечивают поддержку:

- Присвоив значения свойства по умолчанию
- Хранение их текущие значения
- Предоставляет механизмы для проверки значений свойств
- Поддержание согласованности между связанные свойства в одном классе
- Реагирование на изменения свойств
- Активация уведомления, когда изменения или изменением свойства
- Поддержка привязки данных
- Поддержка стилей
- Динамические ресурсы поддержки

При изменении свойства, поддерживаемый изменении привязываемые свойства `BindableObject` активируется [ `PropertyChanged` ](xref:Xamarin.Forms.BindableObject.PropertyChanged) событий, определяющий свойство, которая была изменена. Это событие не возникает, когда свойство имеет значение то же значение.

Некоторые свойства не поддерживаются некоторые классы Xamarin.Forms и связываемые свойства &mdash; например `Span` &mdash; не являются производными от `BindableObject`. Только класс, производный от `BindableObject` поддерживает свойства связывания, так как `BindableObject` определяет `SetValue` и `GetValue` методы.

Так как `Span` является производным от `BindableObject`, ни одно из его свойств &mdash; например `Text` &mdash; обеспечиваются может быть привязано. Вот почему `DynamicResource` на `Text` свойство `Span` приводит к появлению исключения в [ **DynamicVsStatic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic) в предыдущей главе. [ **DynamicVsStaticCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/DynamicVsStaticCode) образце показано, как задать динамические ресурсы в коде с помощью [ `SetDynamicResource` ](xref:Xamarin.Forms.Element.SetDynamicResource(Xamarin.Forms.BindableProperty,System.String)) метода, определенного `Element`. Первым аргументом является объект типа `BindableProperty`.

Аналогичным образом [ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) метода, определенного `BindableObject` имеет тип первого аргумента `BindableProperty`.

## <a name="defining-bindable-properties"></a>Определение привязываемые свойства

Можно определить собственные связываемые свойства, с помощью статического [ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) метод для создания статического поля только для чтения типа `BindableProperty`.

Это показано в [ `AltLabel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AltLabel.cs) в класс [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) библиотеки. Класс является производным от `Label` и позволяет указать размер шрифта в пунктах. Рассматривается в [ **PointSizedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PointSizedText) образца.

Четыре аргументы `BindableProperty.Create` метод требуются:

- `propertyName`: текстовое имя свойства (то же как имя свойства CLR)
- `returnType`: тип свойства CLR
- `declaringType`: тип класса, объявляющего свойство
- `defaultValue`: значение свойства по умолчанию

Так как `defaultValue` имеет тип `object`, компилятор должен иметь возможность определить тип значения по умолчанию. Например если `returnType` — `double`, `defaultValue` должно быть присвоено примерно 0,0, а не только 0 или несоответствие типов будет вызвано исключение во время выполнения.

Также очень характерно для связывания свойства для включения:

- `propertyChanged`: статический метод вызывается при изменении значения свойства. Первый аргумент является экземпляром класса, свойство которого было изменено.

Другие аргументы `BindableProperty.Create` не встречаются:

- `defaultBindingMode`: используется при привязке данных (как описано в [ **Глава 16. Привязка данных**](chapter16.md))
- `validateValue`: обратный вызов для проверки допустимое значение
- `propertyChanging`: обратный вызов для указания того, если это свойство имеет изменением
- `coerceValue`: обратный вызов для присвоения значения набора с другим значением
- `defaultValueCreate`: обратный вызов для создания значения по умолчанию, не могут совместно использоваться экземплярами класса (например, коллекции)

### <a name="the-read-only-bindable-property"></a>Только для чтения свойство, используемое

Может быть привязано может быть только для чтения. Создание только для чтения свойство, используемое требует вызова статического метода [ `BindableProperty.CreateReadOnly` ](xref:Xamarin.Forms.BindableProperty.CreateReadOnly(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) Определение частного статического поля только для чтения типа [ `BindablePropertyKey` ](xref:Xamarin.Forms.BindablePropertyKey).

Затем определите свойство CLR `set` accesor как `private` для вызова [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindablePropertyKey,System.Object)) перегрузка с `BindablePropertyKey` объекта. Этому свойство значение за пределами класса.

Это показано в [ `CountedLabel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CountedLabel.cs) класс, используемый в [ **BaskervillesCount** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/BaskervillesCount) образца.

## <a name="related-links"></a>Связанные ссылки

- [Глава 11 полнотекстового поиска (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch11-Apr2016.pdf)
- [Примеры Глава 11](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)
- [Привязываемые свойства](~/xamarin-forms/xaml/bindable-properties.md)
