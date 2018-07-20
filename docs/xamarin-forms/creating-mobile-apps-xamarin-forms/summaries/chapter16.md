---
title: Сводка Глава 16. привязка данных,
description: 'Создание мобильных приложений с помощью Xamarin.Forms: Сводка Глава 16. привязка данных,'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: ED997DB0-C229-4868-A5FB-928703B377D6
author: charlespetzold
ms.author: chape
ms.date: 07/18/2018
ms.openlocfilehash: 083cb4ed57df989a55a26394cbf8440d53a9e769
ms.sourcegitcommit: 8555a4dd1a579b2206f86c867125ee20fbc3d264
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39156669"
---
# <a name="summary-of-chapter-16-data-binding"></a>Сводка Глава 16. привязка данных,

> [!NOTE] 
> Заметки на этой странице указывать области, где различаются Xamarin.Forms материал, представленный в книге.

Программистов часто оказываются написания обработчиков событий, которые позволяют обнаружить при изменении свойства одного объекта и использовать его для изменения значения свойства в другой объект. Этот процесс можно автоматизировать при помощи метод *привязки данных*. Привязки данных обычно определяется в XAML и становятся частью определения пользовательского интерфейса.

Очень часто эти привязки данных соединиться с объектами пользовательского интерфейса базовых данных. Это метод, который рассматривается в более [ **Глава 18. MVVM**](chapter18.md). Тем не менее привязки данных можно также подключиться два или более элементов пользовательского интерфейса. В большинстве примеров раннего связывания с данными в этой главе демонстрации этого метода.

## <a name="binding-basics"></a>Основы привязки

Несколько свойств, методов и классов участвуют в привязке данных.

- [ `Binding` ](xref:Xamarin.Forms.Binding) Класс является производным от [ `BindingBase` ](xref:Xamarin.Forms.BindingBase) и инкапсулирует многие характеристики привязки данных
- [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) Определяется свойство [ `BindableObject` ](xref:Xamarin.Forms.BindableObject) класса
- [ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) Также определяется метод [ `BindableObject` ](xref:Xamarin.Forms.BindableObject) класса
- [ `BindableObjectExtensions` ](xref:Xamarin.Forms.BindableObjectExtensions) Класс определяет три дополнительных `SetBinding` методы

Следующие два класса поддержки расширения разметки XAML для привязки:

- [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension) поддерживает `Binding` расширение разметки
- [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension) поддерживает `x:Reference` расширение разметки

В привязке данных участвуют два интерфейса:

- [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) в `System.ComponentModel` является пространство имен для реализации уведомлений при изменении свойства
- [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) используется для определения небольших классов, которые преобразуют значения из одного типа в другой в привязках данных

Привязка данных связывает два свойства и тот же объект или (обычно) двух различных объектов. Эти свойства называются *источника* и *целевой*. Как правило изменение свойства источника вызывает изменение в целевое свойство, но иногда задается обратное направление. Независимо от того:

- *целевой* свойство должно поддерживаться [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)
- *источника* свойства обычно является членом класса, реализующего [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged)

Класс, реализующий `INotifyPropertyChanged` активируется [ `PropertyChanged` ](xref:System.ComponentModel.INotifyPropertyChanged.PropertyChanged) событие при изменении значения свойства. `BindableObject` реализует `INotifyPropertyChanged` и автоматически запускает `PropertyChanged` событие, когда свойство `BindableProperty` изменения значения, но вы можете написать свои собственные классы, реализующие `INotifyPropertyChanged` не на основе `BindableObject`.

## <a name="code-and-xaml"></a>Код и XAML

[ **OpacityBindingCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingCode) образце показано, как задать привязку данных в коде:

- Источником является `Value` свойство `Slider`
- Целевой объект — `Opacity` свойство `Label`

Два объекта подключены, задав `BindingContext` из `Label` объект `Slider` объекта. Два свойства подключены, вызвав [ `SetBinding` ](xref:Xamarin.Forms.BindableObjectExtensions.SetBinding*) метод расширения в `Label` ссылки на `OpacityProperty` свойство, используемое и `Value` свойство `Slider` выражается Строка.

Управление `Slider` затем вызывает `Label` для исчезновения и появления.

[ **OpacityBindingXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingXaml) является той же программе, с привязкой данных, задать в XAML. `BindingContext` Из `Label` присваивается `x:Reference` ссылки на расширения разметки `Slider`и `Opacity` свойство `Label` присваивается `Binding` расширения разметки с его [ `Path` ](xref:Xamarin.Forms.Binding.Path) ссылается на свойство `Value` свойство `Slider`.

## <a name="source-and-bindingcontext"></a>Источник и BindingContext

[ **BindingSourceCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceCode) пример альтернативный подход в коде. Объект `Binding` объекта создается путем установки [ `Source` ](xref:Xamarin.Forms.Binding.Source) свойства `Slider` объекта и [ `Path` ](xref:Xamarin.Forms.Binding.Path) свойство «Value». [ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) Метод `BindableObject` потом вызывается через `Label` объекта.

[ `Binding` Конструктор](xref:Xamarin.Forms.Binding.%23ctor(System.String,Xamarin.Forms.BindingMode,Xamarin.Forms.IValueConverter,System.Object,System.String,System.Object)) может также использоваться для определения `Binding` объекта.

[ **BindingSourceXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceXaml) примере показан метод сопоставимых в XAML. `Opacity` Свойство `Label` присваивается `Binding` расширения разметки с [ `Path` ](xref:Xamarin.Forms.Binding.Path) присвоено `Value` свойство и [ `Source` ](xref:Xamarin.Forms.Binding.Source) значение внедренные `x:Reference` расширение разметки.

Таким образом существует два способа для ссылки на объект источника привязки:

- Через `BindingContext` свойства целевого объекта
- Через `Source` свойство `Binding` сам объект

Если указаны оба аргумента, второй имеет приоритет. Преимущество `BindingContext` — что она распространяется по визуальному дереву. Это *очень* удобно в том случае, если несколько целевые свойства привязаны к тому же объекту источника.

[ **WebViewDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WebViewDemo) программа демонстрирует этот прием с [ `WebView` ](xref:Xamarin.Forms.WebView) элемент. Два `Button` элементы для перемещения назад и вперед наследуют `BindingContext` из своего родительского объекта, который ссылается на `WebView`. `IsEnabled` Свойства двух кнопок затем имеют простой `Binding` расширения разметки, предназначенных для кнопки `IsEnabled` свойства на основе параметров из [ `CanGoBack` ](xref:Xamarin.Forms.WebView.CanGoBack) и [ `CanGoForward` ](xref:Xamarin.Forms.WebView.CanGoForward) только для чтения свойства `WebView`.

## <a name="the-binding-mode"></a>Режим привязки

Задайте [ `Mode` ](xref:Xamarin.Forms.BindingBase.Mode) свойство `Binding` члену [ `BindingMode` ](xref:Xamarin.Forms.BindingMode) перечисления:

- [`OneWay`](xref:Xamarin.Forms.BindingMode.OneWay) Чтобы изменения в свойство источника на целевой объект
- [`OneWayToSource`](xref:Xamarin.Forms.BindingMode.OneWayToSource) Чтобы изменения в целевом свойстве влияет на источник
- [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) Чтобы изменения в исходной и целевой влияют друг на друга
- [`Default`](xref:Xamarin.Forms.BindingMode.Default) Чтобы использовать [ `DefaultBindingMode` ](xref:Xamarin.Forms.BindableProperty.DefaultBindingMode) указываться, если целевой объект `BindableProperty` был создан. Если не указаны, по умолчанию используется `OneWay` для обычного свойства для привязки и `OneWayToSource` для связывания свойства только для чтения.

> [!NOTE]
> `BindingMode` Перечисления теперь также включает `OnTime` применения привязки только в том случае, при изменении контекста привязки, а не при изменении свойства источника.

Свойства, которые могут быть целевыми для привязки данных в сценариях MVVM, обычно имеют `DefaultBindingMode` из `TwoWay`. Эти особые значения приведены ниже.

- `Value` Свойство `Slider` и `Stepper`
- `IsToggled` Свойство `Switch`
- `Text` Свойство `Entry`, `Editor`, и `SearchBar`
- `Date` Свойство `DatePicker`
- `Time` Свойство `TimePicker`

[ **BindingModes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingModes) в нем демонстрируется режимы четыре привязки с привязкой данных, где целевой объект — `FontSize` свойство `Label` , а источник `Value` Свойство `Slider`. Это позволит каждой `Slider` для управления размером шрифта соответствующего `Label`. Но `Slider` элементы не инициализирован, поскольку `DefaultBindingMode` из `FontSize` свойство `OneWay`.

[ **ReverseBinding** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ReverseBinding) пример задает привязки на `Value` свойство `Slider` ссылки на `FontSize` свойства каждого `Label`. Это кажется обратной, но лучше работает в инициализации `Slider` элементы поскольку `Value` свойство `Slider` имеет `DefaultBindingMode` из `TwoWay`.

[![Тройной снимок обратная привязка](images/ch16fg06-small.png "обратная привязка")](images/ch16fg06-large.png#lightbox "обратный привязки")

Это аналогично определение привязки в MVVM и будем использовать этот тип привязки, часто.

## <a name="string-formatting"></a>Форматирование строки

Если целевое свойство имеет тип `string`, можно использовать [ `StringFormat` ](xref:Xamarin.Forms.BindingBase.StringFormat) свойство, определенное `BindingBase` для преобразования источника в `string`. Задайте `StringFormat` свойства форматирования строки, которая использовалась бы с помощью статического .NET [ `String.Format` ](xref:System.String.Format(System.String,System.Object)) формата для отображения объекта. При использовании этой строки форматирования внутри расширения разметки, заключите его в одинарные кавычки, не означает расширение разметки, внедренного, фигурные скобки.

[ **ShowViewValues** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ShowViewValues) образце показано, как использовать `StringFormat` в XAML.

[ **WhatSizeBindings** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WhatSizeBindings) в нем демонстрируется отображение размер страницы с помощью привязки к `Width` и `Height` свойства `ContentPage`.

## <a name="why-is-it-called-path"></a>Почему он называется «Путь»?

[ `Path` ](xref:Xamarin.Forms.Binding.Path) Свойство `Binding` так называемых, так как это может быть ряд свойств и индексаторов, разделенных точками. [ **BindingPathDemos** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingPathDemos) примере показано несколько примеров.

## <a name="binding-value-converters"></a>Привязка преобразователей значений

Когда исходные и целевые свойства привязки расположены в различных типов, можно преобразовать с помощью привязки преобразователя типов. Это класс, реализующий [ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter) интерфейс, а также содержит два метода: [ `Convert` ](xref:Xamarin.Forms.IValueConverter.Convert(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) для преобразования источника к целевому объекту, и [ `ConvertBack` ](xref:Xamarin.Forms.IValueConverter.ConvertBack(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) для преобразования целевой объект в источнике.

[ `IntToBoolConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IntToBoolConverter.cs) В класс [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) библиотеки является примером для преобразования `int` для `bool`. Он демонстрируется путем [ **ButtonEnabler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ButtonEnabler) образец, который позволяет `Button` Если хотя бы один символ вводилось в `Entry`.

[ `BoolToStringConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToStringConverter.cs) Класса преобразует `bool` для `string` и определяет два свойства, чтобы указать, какой текст должны быть возвращены для `false` и `true` значения.
[ `BoolToColorConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToColorConverter.cs) Похож. [ **SwitchText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/SwitchText) образце показано использование этих двух преобразователи для отображения разных текстов в разные цвета на основе `Switch` параметр.

Универсальный [ `BoolToObjectConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToObjectConverter.cs) можно заменить `BoolToStringConverter` и `BoolToColorConverter` и служить обобщенный `bool`-к-преобразователю отображаемых объектов любого типа.

## <a name="bindings-and-custom-views"></a>Привязки и пользовательские представления

Вы можете упростить пользовательских элементов управления, с помощью привязки данных. [ `NewCheckBox.cs` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml.cs) Файл кода определяет `Text`, `TextColor`, `FontSize`, `FontAttributes`, и `IsChecked` свойства, но не имеет логики для визуальных элементов элемента управления.
Вместо этого [ `NewCheckBox.cs.xaml` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml) файл содержит всю разметку для визуальных элементов элемента управления через привязки данных на `Label` элементы на основании свойства, определенные в файле кода.

[ **NewCheckBoxDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/NewCheckBoxDemo) в нем демонстрируется `NewCheckBox` пользовательского элемента управления.



## <a name="related-links"></a>Связанные ссылки

- [Глава 16 полнотекстового поиска (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch16-Apr2016.pdf)
- [Глава 16-примеры](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)
- [Привязка данных](~/xamarin-forms/app-fundamentals/data-binding/index.md)
