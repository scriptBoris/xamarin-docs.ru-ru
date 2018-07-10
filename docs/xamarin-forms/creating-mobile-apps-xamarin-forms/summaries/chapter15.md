---
title: Сводка Глава 15. Интерактивный интерфейс
description: 'Создание мобильных приложений с помощью Xamarin.Forms: Сводка Глава 15. Интерактивный интерфейс'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F54E86F4-1CDA-474E-9B09-242060C2C13D
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 6d26e3b9a82917ec3f70190e5e90c59d274de990
ms.sourcegitcommit: 3e980fbf92c69c3dd737554e8c6d5b94cf69ee3a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935190"
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
|`Double`|[`Slider`](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/), [`Stepper`](https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/)|
|`Boolean`|[`Switch`](https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/)|
|`String`|[`Entry`](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/), [`Editor`](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/), [`SearchBar`](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/)|
|`DateTime`|[`DatePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/), [`TimePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/)|

Эти представления можно считать интерактивные визуальные представления базовых типов данных. Эта концепция более рассматривается в следующей главе [ **Глава 16. Привязка данных**](chapter16.md).

Остальные шесть представления рассматриваются в следующих разделах:

- `WebView`: [ **Глава 16. Привязка данных**](chapter16.md)
- `Picker`: [ **Главе 19. Представления коллекций**](chapter19.md)
- `ListView`: [ **Главе 19. Представления коллекций**](chapter19.md)
- `TableView`: [ **Главе 19. Представления коллекций**](chapter19.md)
- `Map`: [ **Глава 28. Расположение и карты**](chapter28.md)
- `OpenGLView`: Не рассматриваются в этой книге (и не поддерживаются для платформ Windows)

## <a name="slider-and-stepper"></a>Ползунок и несопоставимого

Оба [ `Slider` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/) и [ `Stepper` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/) разрешить пользователю выбрать числовое значение из диапазона. `Slider` Является непрерывного диапазона, а `Stepper` включает в себя дискретные значения.

### <a name="slider-basics"></a>Основные сведения "ползунок"

[ `Slider` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/) Является горизонтальную линию, представляющий диапазон значений от минимального в левой части до максимального в правой части. Он определяет три открытых свойства:

- [`Value`](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Value/) типа `double`, значение 0 по умолчанию
- [`Minimum`](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Minimum/) типа `double`, значение 0 по умолчанию
- [`Maximum`](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Maximum/) типа `double`, значение 1 по умолчанию

Привязываемые свойства, которые поддерживают эти свойства убедитесь, что они согласованы:

- Для всех трех свойств [ `coerceValue` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty+CoerceValueDelegate/) указано свойство, используемое гарантирует, что метод `Value` между `Minimum` и `Maximum`.
- [ `validateValue` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty+ValidateValueDelegate/) Метод `MinimumProperty` возвращает `false` Если `Minimum` устанавливается в значение больше или равно `Maximum`и сказанное `MaximumProperty`. Возвращение `false` из `validateValue` метода заставляет `ArgumentException` вызываемого.

`Slider` активируется [ `ValueChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Slider.ValueChanged/) событие с [ `ValueChangedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ValueChangedEventArgs/) аргумент при `Value` изменений свойств, либо программно, либо когда пользователь управляет `Slider`.

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

[ `Stepper` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/) Определяет свойства и события как `Slider` , но `Maximum` свойство устанавливается равным 100 и `Stepper` определяет четвертый свойство:

- [`Increment`](https://developer.xamarin.com/api/property/Xamarin.Forms.Stepper.Increment/) типа `double`, инициализированный 1

Визуально `Stepper` состоит из двух кнопок с меткой **&ndash;** и **+**. Нажав клавишу **&ndash;** уменьшает `Value` по `Increment` до минимального значения `Minimum`. Нажав клавишу **+** увеличивает `Value` по `Increment` максимум `Maximum`.

Это демонстрируется путем [ **StepperDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/StepperDemo) образца.

## <a name="switch-and-checkbox"></a>Переключатель и флажок

[ `Switch` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/) Позволяет пользователю указать логическое значение.

### <a name="switch-basics"></a>Основы коммутатора

Визуально `Switch` состоит из переключатель, который может быть включен и включить. Этот класс определяет одно свойство:

- [`IsToggled`](https://developer.xamarin.com/api/property/Xamarin.Forms.Switch.IsToggled/) типа `bool`

`Switch` Определяет одно событие:

- [`Toggled`](https://developer.xamarin.com/api/event/Xamarin.Forms.Switch.Toggled/) сопровождается [ `ToggledEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ToggledEventArgs/) объекта, когда инициируется `IsToggled` изменения свойств.

[ **SwitchDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SwitchDemo) программа демонстрирует `Switch`.

### <a name="a-traditional-checkbox"></a>Традиционные флажок

Некоторые разработчики предпочитают более традиционных `CheckBox` для `Switch`. [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) библиотека содержит `CheckBox` класс, производный от `ContentView`. `CheckBox` реализуется [CheckBox.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml) и [CheckBox.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml.cs) файлов. `CheckBox` определяет три свойства (`Text`, `FontSize`, и `IsChecked`) и `CheckedChanged` событий.

[ **CheckBoxDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/CheckBoxDemo) в нем демонстрируется это `CheckBox`.

## <a name="typing-text"></a>Ввода текста

Xamarin.Forms определяет три представления дают пользователям возможность вводить и редактировать текст:

- [`Entry`](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) для одной строки текста
- [`Editor`](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/) для нескольких строк текста
- [`SearchBar`](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) для одной строки текста для поиска.

`Entry` и `Editor` являются производными от [ `InputView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.InputView/), который является производным от `View`. `SearchBar` является производным непосредственно от `View`.

### <a name="keyboard-and-focus"></a>И фокус клавиатуры

На телефонах и планшетах без физической клавиатуры `Entry`, `Editor`, и `SearchBar` виртуальной клавиатуры всплывает привести все элементы. Наличие этой клавиатуры на экране связана с фокус ввода. Представления должны иметь его [ `IsVisible` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsVisible/) и [ `IsEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsEnabled/) свойства значения `true` получить фокус ввода.

С фокусом ввода участвуют два метода, одно свойство только для чтения и два события. Все они определяются с `VisualElement`:

- [ `Focus` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Focus()/) Метод пытается задать фокус ввода на элемент и возвращает `true` успеха
- [ `Unfocus` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Unfocus()/) Метод удаляет фокус ввода из элемента
- [ `IsFocused` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsFocused/) Указывает свойство, доступное только для чтения, если элемент имеет фокус ввода
- [ `Focused` ](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.Focused/) Событие указывает, когда элемент получает фокус ввода
- [ `Unfocused` ](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.Unfocused/) Событие указывает, когда элемент теряет фокус ввода

### <a name="choosing-the-keyboard"></a>Выбор клавиатуры

[ `InputView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.InputView/) Класс, от которого `Entry` и `Editor` являются производными определено только одно свойство:

- [`Keyboard`](https://developer.xamarin.com/api/property/Xamarin.Forms.InputView.Keyboard/) типа [`Keyboard`](https://developer.xamarin.com/api/type/Xamarin.Forms.Keyboard/)

Указывает тип клавиатуры, который отображается. Неотъемлемыми оптимизированы для URI или их числа.

`Keyboard` Класса позволяет определять клавиатуры со статическим [ `Keyboard.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Keyboard.Create/p/Xamarin.Forms.KeyboardFlags/) метода с аргументом типа [ `KeyboardFlags` ](https://developer.xamarin.com/api/type/Xamarin.Forms.KeyboardFlags/), перечисление с помощью следующих флагов:

- `None` значение 0
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence) значение 1
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck) значение 2
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions) значение 4
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All) значение \xFFFFFFFF

При использовании многострочного [ `Editor` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/) при ожидается абзаца или несколько из текста, вызов `Keyboard.Create` — Хороший подход в выборе клавиатуры. Для однострочного [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/), следующие статические свойства только для чтения из `Keyboard` полезны:

- [`Default`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Default/)
- [`Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Text/)
- [`Chat`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Chat/)
- [`Url`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Url/)
- [`Email`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Email/)
- [`Telephone`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Telephone/)
- [`Numeric`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Numeric/) для положительных чисел с или без десятичной запятой.

[ `KeyboardTypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.KeyboardTypeConverter/) Можно указать эти свойства в XAML, как показано [ **EntryKeyboards** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/EntryKeyboards) программы.

### <a name="entry-properties-and-events"></a>Запись свойства и события

Однострочный [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) определяет следующие свойства:

- [`Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.Text/) типа `string`, текст, отображаемый в `Entry`
- [`TextColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.TextColor/) типа `Color`
- [`FontFamily`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.FontFamily/) типа `string`
- [`FontSize`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.FontSize/) типа `double`
- [`FontAttributes`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.FontAttributes/) типа `FontAttributes`
- [`IsPassword`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.IsPassword/) типа `bool`, чего символов применения маскирования
- [`Placeholder`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.Placeholder/) типа `string`, для излишне цветной текст, отображаемый в `Entry` прежде, чем что-либо типизирован
- [`PlaceholderColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.PlaceholderColor/) типа `Color`

`Entry` Также определяет два события:

- [`TextChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.TextChanged/) с помощью [ `TextChangedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TextChangedEventArgs/) объектов, каждый раз, когда инициируется `Text` изменения свойств
- [`Completed`](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.Completed/), возникает, когда пользователь завершил работу и закрытия клавиатуры. Пользователь указывает завершения в виде платформы

[ **QuadraticEquations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/QuadaticEquations) образец демонстрирует эти два события.

### <a name="the-editor-difference"></a>Разница редактора

Многострочный [ `Editor` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/) определяет же `Text` и `Font` свойства как `Entry` , но не другие свойства. `Editor` также определяет те же два свойства, что `Entry`.

[**JustNotes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/JustNotes) — это программа ведения заметок свободной формы, который сохраняет и восстанавливает содержимое `Editor`.

### <a name="the-searchbar"></a>SearchBar

[ `SearchBar` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) Является производным от `InputView`, поэтому он не имеет `Keyboard` свойства. Но у него все `Text`, `Font`, и `Placeholder` свойства, `Entry` определяет. Кроме того `SearchBar` определяет три дополнительных свойства:

- [`CancelButtonColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.SearchBar.CancelButtonColor/) типа `Color`
- [`SearchCommand`](https://developer.xamarin.com/api/property/Xamarin.Forms.SearchBar.SearchCommand/) типа [ `ICommand` ](https://developer.xamarin.com/api/type/System.Windows.Input.ICommand/) для привязки данных и MVVM
- [`SearchCommandParameter`](https://developer.xamarin.com/api/property/Xamarin.Forms.SearchBar.SearchCommandParameter/) типа `Object`, для использования с `SearchCommand`

Специфические для платформы отменить кнопки удаляет текст. `SearchBar` Также доступна кнопка поиска конкретных платформ. Нажав любую из этих кнопок создает по одному из двух событий, `SearchBar` определяет:

- [`TextChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.SearchBar.TextChanged/) сопровождается [ `TextChangedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TextChangedEventArgs/) объекта
- [`SearchButtonPressed`](https://developer.xamarin.com/api/event/Xamarin.Forms.SearchBar.SearchButtonPressed/)

[ **SearchBarDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SearchBarDemo) в нем демонстрируется `SearchBar`.

## <a name="date-and-time-selection"></a>Выбор даты и времени

[ `DatePicker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/) И [ `TimePicker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/) представления реализации элементов управления платформы, которые позволяют пользователю указать даты или времени.

### <a name="the-datepicker"></a>DatePicker

[`DatePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/) Определяет четыре свойства:

- [`MinimumDate`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.MinimumDate/) типа `DateTime`, инициализированный 1 января 1900 г.
- [`MaximumDate`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.MaximumDate/) типа `DateTime`, инициализированный 31 декабря 2100
- [`Date`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.Date/) типа `DateTime`, инициализированный `DateTime.Today`
- [`Format`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.Format/) типа `string`, равным «d», шаблон короткого формата даты, строка форматирования .NET приводит отображения даты, например «7/20/1969"в США.

Можно задать `DateTime` свойства в XAML, выражение свойства как свойства элементов и с помощью языка и региональных параметров дату формата («7/20/1969»).   

[ **DaysBetweenDates** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/DaysBetweenDates) пример вычисляет количество дней между двумя датами, выбранного пользователем.

### <a name="the-timepicker-or-is-it-a-timespanpicker"></a>Элемента управления TimePicker (или TimeSpanPicker?)

[`TimePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/) Определяет два свойства и события не:

- [`Time`](https://developer.xamarin.com/api/property/Xamarin.Forms.TimePicker.Time/) имеет тип `TimeSpan` вместо `DateTime`, указывающее время, истекших после полуночи
- [`Format`](https://developer.xamarin.com/api/property/Xamarin.Forms.TimePicker.Format/) типа `string`, .NET форматирования строки, равным «t», короткий шаблон времени, приводит к отображения времени, например «1:45 PM» в США.

[ **SetTimer** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SetTimer) программы демонстрирует использование `TimePicker` для указания времени таймера. Программа работает только в том случае, если вы храните на переднем плане.

**SetTimer** также демонстрируется использование [ `DisplayAlert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert/p/System.String/System.String/System.String/) метод `Page` чтобы отобразить окно с предупреждением.



## <a name="related-links"></a>Связанные ссылки

- [Глава 15 полнотекстового поиска (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch15-Apr2016.pdf)
- [Глава 15-примеры](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)
- [Ввод](~/xamarin-forms/user-interface/text/entry.md)
- [Редактор](~/xamarin-forms/user-interface/text/editor.md)
