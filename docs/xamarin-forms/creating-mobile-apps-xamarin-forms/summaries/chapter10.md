---
title: Сводка Глава 10. Расширения разметки XAML
description: 'Создание мобильных приложений с помощью Xamarin.Forms: Сводка Глава 10. Расширения разметки XAML'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 575EAE55-BD4D-470F-A583-3D065FA102E2
author: charlespetzold
ms.author: chape
ms.date: 07/19/2018
ms.openlocfilehash: 74f7e2846a9e8d8390a8322c57db0845718bbba7
ms.sourcegitcommit: 8555a4dd1a579b2206f86c867125ee20fbc3d264
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39157007"
---
# <a name="summary-of-chapter-10-xaml-markup-extensions"></a>Сводка Глава 10. Расширения разметки XAML

Как правило, средство синтаксического анализа XAML преобразует любой строки задается как значение атрибута для типа свойства, в зависимости от стандартных преобразований для базовых типов данных .NET, или [ `TypeConverter` ](xref:Xamarin.Forms.TypeConverter) производных присоединенного к свойству, либо его тип [`TypeConverterAttribute`](xref:Xamarin.Forms.TypeConverterAttribute).

Но иногда бывает удобно задать атрибут из другого источника, например, элемент в словарь, или значение статического свойства или поля, или из расчета какого-либо рода.

Это задание из *расширения разметки XAML*. Несмотря на название, расширения разметки XAML — *не* расширение XML. XAML — это всегда юридические XML.

## <a name="the-code-infrastructure"></a>Код инфраструктуры

Расширение разметки XAML — это класс, реализующий [ `IMarkupExtension` ](xref:Xamarin.Forms.Xaml.IMarkupExtension) интерфейс. Такой класс часто содержит слово `Extension` в конце имени но обычно отображается в XAML без этого суффикса.

Следующие расширения разметки XAML, поддерживаются все реализации XAML:

- `x:Static` поддерживается [`StaticExtension`](xref:Xamarin.Forms.Xaml.StaticExtension)
- `x:Reference` поддерживается [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension)
- `x:Type` поддерживается [`TypeExtension`](xref:Xamarin.Forms.Xaml.TypeExtension)
- `x:Null` поддерживается [`NullExtension`](xref:Xamarin.Forms.Xaml.NullExtension)
- `x:Array` поддерживается [`ArrayExtension`](xref:Xamarin.Forms.Xaml.ArrayExtension)

Эти четыре расширения разметки XAML поддерживает многие реализации XAML, включая Xamarin.Forms:

- `StaticResource` поддерживается [`StaticResourceExtension`](xref:Xamarin.Forms.Xaml.StaticResourceExtension)
- `DynamicResource` поддерживается [`DynamicResourceExtension`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension)
- `Binding` поддерживаемые [ `BindingExtension` ](xref:Xamarin.Forms.Xaml.BindingExtension) &mdash;подробно [Глава 16. Привязка данных](#chapter16)
- `TemplateBinding` поддерживаемые [ `TemplateBindingExtension` ](xref:Xamarin.Forms.Xaml.TemplateBindingExtension) &mdash;не рассматривается в книге

Дополнительное расширение разметки XAML включен в Xamarin.Forms, в связи с [ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout):

- [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression)&mdash;не рассматривается в книге

## <a name="accessing-static-members"></a>Доступ к статическим членам

Используйте [ `x:Static` ](xref:Xamarin.Forms.Xaml.StaticExtension) задаваемого атрибута к значению общедоступного статического свойства, поля или перечисления члена элемента. Задайте [ `Member` ](xref:Xamarin.Forms.Xaml.StaticExtension.Member) свойство к статическому члену. Обычно проще указать `x:Static` и имя члена в фигурные скобки. Имя `Member` свойство не нужно быть включен, только сам элемент. Этот общий синтаксис показан в [ **SharedStatics** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SharedStatics) образца. Статические поля, сами определяются в [ `AppConstants` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter10/SharedStatics/SharedStatics/SharedStatics/AppConstants.cs) класса. Этот метод позволяет установить константы, используемые в программе.

С помощью дополнительных объявления пространства имен XML, вы может ссылаться на открытые статические свойства, поля или перечисления элементов, определенных в .NET framework, как показано в [ **SystemStatics** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SystemStatics) образца .

## <a name="resource-dictionaries"></a>Словари ресурсов

`VisualElement` Класс определяет свойство, именуемое [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) , можно присвоить объект типа [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). В XAML, можно хранить элементы в данном словаре и определить их с помощью `x:Key` атрибута. Элементы, хранящиеся в словаре ресурсов являются общими для всех ссылок на элемент.

### <a name="staticresource-for-most-purposes"></a>StaticResource для большинства целей

В большинстве случаев вы будете использовать [ `StaticResource` ](xref:Xamarin.Forms.Xaml.StaticResourceExtension) расширения разметки для ссылки на элемент из словаря ресурсов, как показано [ **ResourceSharing** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceSharing) образца . Можно использовать `StaticResourceExtension` элемент или `StaticResource` в фигурных скобках:

[![Тройной снимок общий доступ к ресурсам](images/ch10fg03-small.png "общий доступ к ресурсам")](images/ch10fg03-large.png#lightbox "общий доступ к ресурсам")

Не следует путать `x:Static` расширения разметки и `StaticResource` расширение разметки.

### <a name="a-tree-of-dictionaries"></a>Дерево словарей

Когда средство синтаксического анализа XAML обнаруживает `StaticResource`, он начинает поиск вверх по визуальному дереву, ключ на соответствие и затем поиск выполняется в `ResourceDictionary` в приложения `App` класса. Это позволяет элементов в словаре ресурсов глубже в визуальном дереве для переопределения словарь ресурсов, выше в визуальном дереве. Это показано в [ **ResourceTrees** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceTrees) образца.

### <a name="dynamicresource-for-special-purposes"></a>DynamicResource для особых целей

`StaticResource` Расширение разметки вызывает элемент, извлекаемый из словаря, при построении визуального дерева во время `InitializeComponent` вызова. Альтернативой `StaticResource` — [ `DynamicResource` ](xref:Xamarin.Forms.Xaml.DynamicResourceExtension), который хранит ссылку на ключ словаря и обновляет целевой объект в том случае, когда ссылка на элемент ключевых изменений.

Разница между `StaticResource` и `DynamicResource` демонстрируется в [ **DynamicVsStatic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic) образца.

Свойство, которому присвоен `DynamicResource` должна опираться на может быть привязано, как описано в [Глава 11, инфраструктура связывания](chapter11.md).

## <a name="lesser-used-markup-extensions"></a>Расширения разметки, используется меньшее

Используйте [ `x:Null` ](xref:Xamarin.Forms.Xaml.NullExtension) расширение разметки, чтобы задать для свойства `null`.

Используйте [ `x:Type` ](xref:Xamarin.Forms.Xaml.TypeExtension) расширение разметки, чтобы задать для свойства .NET `Type` объекта.

Используйте [ `x:Array` ](xref:Xamarin.Forms.Xaml.ArrayExtension) определить массив. Укажите тип элементов массива, задав [`Type`] свойства `x:Type` расширение разметки.

## <a name="a-custom-markup-extension"></a>Пользовательское расширение разметки

Можно создать собственные расширения разметки XAML, написав класс, реализующий [ `IMarkupExtension` ](xref:Xamarin.Forms.Xaml.IMarkupExtension) взаимодействовать с [ `ProvideValue` ](xref:Xamarin.Forms.Xaml.IMarkupExtension.ProvideValue(System.IServiceProvider)) метод.

[ `HslColorExtension` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/HslColorExtension.cs) Класс удовлетворяют этим требованиям. Он создает значение типа `Color` на основе значений свойств с именем `H`, `S`, `L`, и `A`. Этот класс является первый элемент в библиотеку Xamarin.Forms [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) , сформированными и используется в ходе этой книги.

[ **CustomExtensionDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/CustomExtensionDemo) образце показано, как сослаться на эту библиотеку, и использовать пользовательское расширение разметки.

## <a name="related-links"></a>Связанные ссылки

- [Глава 10 полнотекстового поиска (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf)
- [Глава 10 выборок](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)
- [Расширения разметки XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
