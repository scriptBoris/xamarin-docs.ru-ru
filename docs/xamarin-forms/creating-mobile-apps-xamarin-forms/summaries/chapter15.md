---
title: Сводка Глава 15. Интерактивный интерфейс
description: 'Создание мобильных приложений с помощью Xamarin.Forms: Сводка Глава 15. Интерактивный интерфейс'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F54E86F4-1CDA-474E-9B09-242060C2C13D
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 6da3753d723ed44ca640d8c80ae07258a03cbbbc
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998671"
---
# <a name="summary-of-chapter-15-the-interactive-interface"></a>Сводка Глава 15. Интерактивный интерфейс

В этой главе рассматриваются восьми `View` производных материалов, которые обеспечивают взаимодействие с пользователем.

## <a name="view-overview"></a>Просмотр общих сведений

Xamarin.Forms содержит 20 допускающий создание экземпляров классов, производных от `View` , но не `Layout`. Шесть из них уже описаны в предыдущих главах:

- `Label`: [ **Глава 2. Анатомия приложения**](chapter02.md)
- `BoxView`: [ **Глава 3. Раскрутка стека**](chapter03.md)
- `Button`: [ **Глава 6. Нажатие кнопки**](chapter06.md)
- `Image`: [ **Главе 13. Растровые изображения**](chapter13.md)
- `ActivityIndicator`: [ **Главе 13. Растровые изображения**](chapter13.md)
- `ProgressBar`: [ **Глава 14. AbsoluteLayout**](chapter14.md)

Восемь представлений в этой главе эффективно разрешить пользователю взаимодействовать с базовых типов данных .NET:

|Тип данных|Представления|
|--- |--- |
|`Double`|[`Slider`](xref:Xamarin.Forms.Slider), [`Stepper`](xref:Xamarin.Forms.Stepper)|
|`Boolean`|[`Switch`](xref:Xamarin.Forms.Switch)|
|`String`|[`Entry`](xref:Xamarin.Forms.Entry), [`Editor`](xref:Xamarin.Forms.Editor), [`SearchBar`](xref:Xamarin.Forms.SearchBar)|
|`DateTime`|[`DatePicker`](xref:Xamarin.Forms.DatePicker), [`TimePicker`](xref:Xamarin.Forms.TimePicker)|

Эти представления можно считать интерактивные визуальные представления базовых типов данных. Эта концепция более рассматривается в следующей главе [ **Глава 16. Привязка данных**](chapter16.md).

Остальные шесть представления рассматриваются в следующих разделах:

- `WebView`: [ **Глава 16. Привязка данных**](chapter16.md)
- `Picker`: [ **Главе 19. Представления коллекций**](chapter19.md)
- `ListView`: [ **Главе 19. Представления коллекций**](chapter19.md)
- `TableView`: [ **Главе 19. Представления коллекций**](chapter19.md)
- `Map`: [ **Глава 28. Расположение и карты**](chapter28.md)
- `OpenGLView`: Не рассматриваются в этой книге (и не поддерживаются для платформ Windows)

## <a name="slider-and-stepper"></a>Ползунок и несопоставимого

Оба [ `Slider` ](xref:Xamarin.Forms.Slider) и [ `Stepper` ](xref:Xamarin.Forms.Stepper) разрешить пользователю выбрать числовое значение из диапазона. `Slider` Является непрерывного диапазона, а `Stepper` включает в себя дискретные значения.

### <a name="slider-basics"></a>Основные сведения "ползунок"

[ `Slider` ](xref:Xamarin.Forms.Slider) Является горизонтальную линию, представляющий диапазон значений от минимального в левой части до максимального в правой части. Он определяет три открытых свойства:

- [`Value`](xref:Xamarin.Forms.Slider.Value) типа `double`, значение 0 по умолчанию
- [`Minimum`](xref:Xamarin.Forms.Slider.Minimum) типа `double`, значение 0 по умолчанию
- [`Maximum`](xref:Xamarin.Forms.Slider.Maximum) типа `double`, значение 1 по умолчанию

Привязываемые свойства, которые поддерживают эти свойства убедитесь, что они согласованы:

- Для всех трех свойств [ `coerceValue` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty+CoerceValueDelegate/) указано свойство, используемое гарантирует, что метод `Value` между `Minimum` и `Maximum`.
- [ `validateValue` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty+ValidateValueDelegate/) Метод `MinimumProperty` возвращает `false` Если `Minimum` устанавливается в значение больше или равно `Maximum`и сказанное `MaximumProperty`. Возвращение `false` из `validateValue` метода заставляет `ArgumentException` вызываемого.

`Slider` активируется [ `ValueChanged` ](xref:Xamarin.Forms.Slider.ValueChanged) событие с [ `ValueChangedEventArgs` ](xref:Xamarin.Forms.ValueChangedEventArgs) аргумент при `Value` изменений свойств, либо программно, либо когда пользователь управляет `Slider`.

[ **SliderDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SliderDemo) в нем демонстрируется простое использование `Slider`.

### <a name="common-pitfalls"></a>Распространенные проблемы

В коде и в XAML `Minimum` и `Maximum` свойств в заданном порядке. Не забудьте инициализировать эти свойства, чтобы `Maximum` был всегда больше, чем `Minimum`. В противном случае возникнет исключение.

Инициализация `Slider` свойства может привести к `Value` свойство, изменяемое и `ValueChanged` , возникает событие. Необходимо убедиться, что `Slider` обработчик событий не обращается к представлениям, которые еще не были созданы во время инициализации страницы.

`ValueChanged` Событие не срабатывает во время `Slider` инициализации пока `Value` изменения свойств. Вы можете вызвать `ValueChanged` обработчик непосредственно из кода.

### <a name="slider-color-selection"></a>Выбранный цвет ползунка

[ **RgbSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/RgbSliders) программа содержит три `Slider` элементы, которые дают возможность интерактивно выбирать цвета, указывая его значения RGB:

[![Тройной снимок R G B ползунки](images/ch15fg03-small.png "ползунки RGB")](images/ch15fg03-large.png#lightbox "ползунки RGB")

[ **TextFade** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/TextFade) в образце используется две `Slider` элементов для перемещения двух `Label` по `AbsoluteLayout` и изменяет яркость один в другой.

### <a name="the-stepper-difference"></a>Разница несопоставимого

[ `Stepper` ](xref:Xamarin.Forms.Stepper) Определяет свойства и события как `Slider` , но `Maximum` свойство устанавливается равным 100 и `Stepper` определяет четвертый свойство:

- [`Increment`](xref:Xamarin.Forms.Stepper.Increment) типа `double`, инициализированный 1

Визуально `Stepper` состоит из двух кнопок с меткой **&ndash;** и **+**. Нажав клавишу **&ndash;** уменьшает `Value` по `Increment` до минимального значения `Minimum`. Нажав клавишу **+** увеличивает `Value` по `Increment` максимум `Maximum`.

Это демонстрируется путем [ **StepperDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/StepperDemo) образца.

## <a name="switch-and-checkbox"></a>Переключатель и флажок

[ `Switch` ](xref:Xamarin.Forms.Switch) Позволяет пользователю указать логическое значение.

### <a name="switch-basics"></a>Основы коммутатора

Визуально `Switch` состоит из переключатель, который может быть включен и включить. Этот класс определяет одно свойство:

- [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) типа `bool`

`Switch` Определяет одно событие:

- [`Toggled`](xref:Xamarin.Forms.Switch.Toggled) сопровождается [ `ToggledEventArgs` ](xref:Xamarin.Forms.ToggledEventArgs) объекта, когда инициируется `IsToggled` изменения свойств.

[ **SwitchDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SwitchDemo) программа демонстрирует `Switch`.

### <a name="a-traditional-checkbox"></a>Традиционные флажок

Некоторые разработчики предпочитают более традиционных `CheckBox` для `Switch`. [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) библиотека содержит `CheckBox` класс, производный от `ContentView`. `CheckBox` реализуется [CheckBox.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml) и [CheckBox.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml.cs) файлов. `CheckBox` определяет три свойства (`Text`, `FontSize`, и `IsChecked`) и `CheckedChanged` событий.

[ **CheckBoxDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/CheckBoxDemo) в нем демонстрируется это `CheckBox`.

## <a name="typing-text"></a>Ввода текста

Xamarin.Forms определяет три представления дают пользователям возможность вводить и редактировать текст:

- [`Entry`](xref:Xamarin.Forms.Entry) для одной строки текста
- [`Editor`](xref:Xamarin.Forms.Editor) для нескольких строк текста
- [`SearchBar`](xref:Xamarin.Forms.SearchBar) для одной строки текста для поиска.

`Entry` и `Editor` являются производными от [ `InputView` ](xref:Xamarin.Forms.InputView), который является производным от `View`. `SearchBar` является производным непосредственно от `View`.

### <a name="keyboard-and-focus"></a>И фокус клавиатуры

На телефонах и планшетах без физической клавиатуры `Entry`, `Editor`, и `SearchBar` виртуальной клавиатуры всплывает привести все элементы. Наличие этой клавиатуры на экране связана с фокус ввода. Представления должны иметь его [ `IsVisible` ](xref:Xamarin.Forms.VisualElement.IsVisible) и [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) свойства значения `true` получить фокус ввода.

С фокусом ввода участвуют два метода, одно свойство только для чтения и два события. Все они определяются с `VisualElement`:

- [ `Focus` ](xref:Xamarin.Forms.VisualElement.Focus) Метод пытается задать фокус ввода на элемент и возвращает `true` успеха
- [ `Unfocus` ](xref:Xamarin.Forms.VisualElement.Unfocus) Метод удаляет фокус ввода из элемента
- [ `IsFocused` ](xref:Xamarin.Forms.VisualElement.IsFocused) Указывает свойство, доступное только для чтения, если элемент имеет фокус ввода
- [ `Focused` ](xref:Xamarin.Forms.VisualElement.Focused) Событие указывает, когда элемент получает фокус ввода
- [ `Unfocused` ](xref:Xamarin.Forms.VisualElement.Unfocused) Событие указывает, когда элемент теряет фокус ввода

### <a name="choosing-the-keyboard"></a>Выбор клавиатуры

[ `InputView` ](xref:Xamarin.Forms.InputView) Класс, от которого `Entry` и `Editor` являются производными определено только одно свойство:

- [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) типа [`Keyboard`](xref:Xamarin.Forms.Keyboard)

Указывает тип клавиатуры, который отображается. Неотъемлемыми оптимизированы для URI или их числа.

`Keyboard` Класса позволяет определять клавиатуры со статическим [ `Keyboard.Create` ](xref:Xamarin.Forms.Keyboard.Create(Xamarin.Forms.KeyboardFlags)) метода с аргументом типа [ `KeyboardFlags` ](xref:Xamarin.Forms.KeyboardFlags), перечисление с помощью следующих флагов:

- `None` значение 0
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence) значение 1
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck) значение 2
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions) значение 4
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All) значение \xFFFFFFFF

При использовании многострочного [ `Editor` ](xref:Xamarin.Forms.Editor) при ожидается абзаца или несколько из текста, вызов `Keyboard.Create` — Хороший подход в выборе клавиатуры. Для однострочного [ `Entry` ](xref:Xamarin.Forms.Entry), следующие статические свойства только для чтения из `Keyboard` полезны:

- [`Default`](xref:Xamarin.Forms.Keyboard.Default)
- [`Text`](xref:Xamarin.Forms.Keyboard.Text)
- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat)
- [`Url`](xref:Xamarin.Forms.Keyboard.Url)
- [`Email`](xref:Xamarin.Forms.Keyboard.Email)
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone)
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric) для положительных чисел с или без десятичной запятой.

[ `KeyboardTypeConverter` ](xref:Xamarin.Forms.KeyboardTypeConverter) Можно указать эти свойства в XAML, как показано [ **EntryKeyboards** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/EntryKeyboards) программы.

### <a name="entry-properties-and-events"></a>Запись свойства и события

Однострочный [ `Entry` ](xref:Xamarin.Forms.Entry) определяет следующие свойства:

- [`Text`](xref:Xamarin.Forms.Entry.Text) типа `string`, текст, отображаемый в `Entry`
- [`TextColor`](xref:Xamarin.Forms.Entry.TextColor) типа `Color`
- [`FontFamily`](xref:Xamarin.Forms.Entry.FontFamily) типа `string`
- [`FontSize`](xref:Xamarin.Forms.Entry.FontSize) типа `double`
- [`FontAttributes`](xref:Xamarin.Forms.Entry.FontAttributes) типа `FontAttributes`
- [`IsPassword`](xref:Xamarin.Forms.Entry.IsPassword) типа `bool`, чего символов применения маскирования
- [`Placeholder`](xref:Xamarin.Forms.Entry.Placeholder) типа `string`, для излишне цветной текст, отображаемый в `Entry` прежде, чем что-либо типизирован
- [`PlaceholderColor`](xref:Xamarin.Forms.Entry.PlaceholderColor) типа `Color`

`Entry` Также определяет два события:

- [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) с помощью [ `TextChangedEventArgs` ](xref:Xamarin.Forms.TextChangedEventArgs) объектов, каждый раз, когда инициируется `Text` изменения свойств
- [`Completed`](xref:Xamarin.Forms.Entry.Completed), возникает, когда пользователь завершил работу и закрытия клавиатуры. Пользователь указывает завершения в виде платформы

[ **QuadraticEquations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/QuadaticEquations) образец демонстрирует эти два события.

### <a name="the-editor-difference"></a>Разница редактора

Многострочный [ `Editor` ](xref:Xamarin.Forms.Editor) определяет же `Text` и `Font` свойства как `Entry` , но не другие свойства. `Editor` также определяет те же два свойства, что `Entry`.

[**JustNotes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/JustNotes) — это программа ведения заметок свободной формы, который сохраняет и восстанавливает содержимое `Editor`.

### <a name="the-searchbar"></a>SearchBar

[ `SearchBar` ](xref:Xamarin.Forms.SearchBar) Является производным от `InputView`, поэтому он не имеет `Keyboard` свойства. Но у него все `Text`, `Font`, и `Placeholder` свойства, `Entry` определяет. Кроме того `SearchBar` определяет три дополнительных свойства:

- [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor) типа `Color`
- [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand) типа [ `ICommand` ](xref:System.Windows.Input.ICommand) для привязки данных и MVVM
- [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter) типа `Object`, для использования с `SearchCommand`

Специфические для платформы отменить кнопки удаляет текст. `SearchBar` Также доступна кнопка поиска конкретных платформ. Нажав любую из этих кнопок создает по одному из двух событий, `SearchBar` определяет:

- [`TextChanged`](xref:Xamarin.Forms.SearchBar.TextChanged) сопровождается [ `TextChangedEventArgs` ](xref:Xamarin.Forms.TextChangedEventArgs) объекта
- [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed)

[ **SearchBarDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SearchBarDemo) в нем демонстрируется `SearchBar`.

## <a name="date-and-time-selection"></a>Выбор даты и времени

[ `DatePicker` ](xref:Xamarin.Forms.DatePicker) И [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) представления реализации элементов управления платформы, которые позволяют пользователю указать даты или времени.

### <a name="the-datepicker"></a>DatePicker

[`DatePicker`](xref:Xamarin.Forms.DatePicker) Определяет четыре свойства:

- [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) типа `DateTime`, инициализированный 1 января 1900 г.
- [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) типа `DateTime`, инициализированный 31 декабря 2100
- [`Date`](xref:Xamarin.Forms.DatePicker.Date) типа `DateTime`, инициализированный `DateTime.Today`
- [`Format`](xref:Xamarin.Forms.DatePicker.Format) типа `string`, равным «d», шаблон короткого формата даты, строка форматирования .NET приводит отображения даты, например «7/20/1969"в США.

Можно задать `DateTime` свойства в XAML, выражение свойства как свойства элементов и с помощью языка и региональных параметров дату формата («7/20/1969»).   

[ **DaysBetweenDates** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/DaysBetweenDates) пример вычисляет количество дней между двумя датами, выбранного пользователем.

### <a name="the-timepicker-or-is-it-a-timespanpicker"></a>Элемента управления TimePicker (или TimeSpanPicker?)

[`TimePicker`](xref:Xamarin.Forms.TimePicker) Определяет два свойства и события не:

- [`Time`](xref:Xamarin.Forms.TimePicker.Time) имеет тип `TimeSpan` вместо `DateTime`, указывающее время, истекших после полуночи
- [`Format`](xref:Xamarin.Forms.TimePicker.Format) типа `string`, .NET форматирования строки, равным «t», короткий шаблон времени, приводит к отображения времени, например «1:45 PM» в США.

[ **SetTimer** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SetTimer) программы демонстрирует использование `TimePicker` для указания времени таймера. Программа работает только в том случае, если вы храните на переднем плане.

**SetTimer** также демонстрируется использование [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) метод `Page` чтобы отобразить окно с предупреждением.



## <a name="related-links"></a>Связанные ссылки

- [Глава 15 полнотекстового поиска (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch15-Apr2016.pdf)
- [Глава 15-примеры](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)
- [Ввод](~/xamarin-forms/user-interface/text/entry.md)
- [Редактор](~/xamarin-forms/user-interface/text/editor.md)
