---
title: Сводка Глава 2. Анатомия приложения
description: 'Создание мобильных приложений с помощью Xamarin.Forms: Сводка Глава 2. Анатомия приложения'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8764EB7D-8331-4CF7-9BE1-26D0DEE9E0BB
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: 948d25ce379944691053a5ff76ba3b2284385251
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53052641"
---
# <a name="summary-of-chapter-2-anatomy-of-an-app"></a>Сводка Глава 2. Анатомия приложения

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)

> [!NOTE]
> Заметки на этой странице указывать области, где различаются Xamarin.Forms материал, представленный в книге.

В приложении Xamarin.Forms объектов, занимающих место на экране, называются *визуальные элементы*, инкапсулированный с [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) класса. Визуальные элементы можно разделить на три категории, соответствующие этих классов:

- [Страница](xref:Xamarin.Forms.Page)
- [Макет](xref:Xamarin.Forms.Layout)
- [Вид](xref:Xamarin.Forms.View)

Объект `Page` производных занимает весь экран или почти весь экран. Часто является дочерним элементом страницы `Layout` производных для упорядочения дочерних визуальных элементов. Дочерние элементы `Layout` может быть другой `Layout` классы или `View` производные (часто называется *элементы*), — знакомые объекты, такие как текст, растровые изображения, ползунки, кнопок, списков и т. д.

В этой главе показано, как создать приложение, уделяя [ `Label` ](xref:Xamarin.Forms.Label), который является `View` производных, отображающий текст.

## <a name="say-hello"></a>Поприветствуйте

На платформе Xamarin установлен можно создать новое решение Xamarin.Forms в Visual Studio или Visual Studio для Mac. [ **Hello** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello) решение использует переносимую библиотеку классов, общий код.

> [!NOTE]
> Переносимые библиотеки классов были заменены библиотеки .NET Standard. Все примеры кода из книги будет преобразована использовать стандартные библиотеки .NET.

В этом примере демонстрируется решение Xamarin.Forms, созданные в Visual Studio без изменений. Решение состоит из шести проектов:

- [**Hello**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello), переносимой библиотеки классов (PCL), общим для других проектов
- [**Hello.Droid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Droid), проект приложения для Android
- [**Hello.iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.iOS), проект приложения для iOS
- [**Hello.UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.UWP), проект приложения для универсальной платформы Windows (Windows 10 и Windows 10 Mobile)
- [**Hello.Windows**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Windows), проект приложения для Windows 8.1
- [**Hello.WinPhone**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.WinPhone), проект приложения для Windows Phone 8.1

> [!NOTE]
> Xamarin.Forms больше не поддерживает Windows 8.1, Windows Phone 8.1 или Windows 10 Mobile, но приложения Xamarin.Forms выполняются на рабочем столе Windows 10.

Внесите любые из этих проектов приложений запускаемого проекта и затем постройте и запустите программу на устройства или симулятора.

Во многих приложениях Xamarin.Forms не изменяя проектов приложений. Они часто остаются tiny заглушки просто для того, чтобы запустить программу. Большая часть фокус будет библиотека, общими для всех приложений.

## <a name="inside-the-files"></a>В файлах

Визуальные элементы, отображаемые **Hello** программы определяются в конструкторе класса [ `App` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello/App.cs) класса. `App` является производным от класса Xamarin.Forms [ `Application` ](xref:Xamarin.Forms.Application).

> [!NOTE]
> Шаблоны решений Visual Studio для Xamarin.Forms создайте страницу с файлом XAML. В этой книге, пока не рассматривается XAML [Глава 7](chapter07.md).

**Ссылки** раздел **Hello** проект переносимой библиотеки Классов включает следующие сборки Xamarin.Forms:

- **Xamarin.Forms.Core**
- **Xamarin.Forms.Xaml**
- **Xamarin.Forms.Platform**

**Ссылки** разделы проектов пять приложений содержат дополнительные сборки, которые применяются для отдельных платформ:

- **Xamarin.Forms.Platform.Android**
- **Xamarin.Forms.Platform.iOS**
- **Xamarin.Forms.Platform.UWP**
- **Xamarin.Forms.Platform.WinRT**
- **Xamarin.Forms.Platform.WinRT.Tablet**
- **Xamarin.Forms.Platform.WinRT.Phone**

> [!NOTE]
> **Ссылки** разделах эти проекты не перечислены сборки. Вместо этого файл проекта содержит **PackageReference** теги, ссылающиеся на пакет Xamarin.Forms NuGet. **Ссылки** раздел в списках Visual Studio **Xamarin.Forms** пакета вместо сборки Xamarin.Forms.

Каждый из проектов приложений содержит вызов статического `Forms.Init` метод в `Xamarin.Forms` пространства имен. Это инициализирует библиотеку Xamarin.Forms. Другая версия `Forms.Init` определяется для каждой платформы. Вызовы этого метода можно найти в следующих классов:

- iOS: [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android: [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP: [ `App` класс, `OnLaunched` метод](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)

Кроме того, необходимо создать экземпляр каждой платформы `App` класса расположение в общей библиотеке. Это происходит при вызове `LoadApplication` в следующие классы:

- iOS: [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android: [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP: [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.UWP/MainPage.xaml.cs)

В противном случае эти проекты приложений — это обычный программы «ничего не делать».

## <a name="pcl-or-sap"></a>PCL или SAP?

Имеется возможность создать решение Xamarin.Forms с общим кодом в переносимой библиотеки классов (PCL) или общего ресурса проекта (SAP). Создание решения SAP, выберите параметр Shared в Visual Studio. [ **HelloSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/HelloSap) решение демонстрирует SAP шаблон без изменений.

> [!NOTE]
> Переносимые библиотеки классов была заменена библиотеки .NET Standard. Все примеры кода из книги будет преобразована использовать стандартные библиотеки .NET. В противном случае библиотеки PCL и .NET Standard концептуально очень похожи.

Пакеты библиотеки подход, общего кода в проекте библиотеки ссылаться проекты приложения платформы. При использовании SAP общий код фактически существует во всех проектах приложения платформы и распределяется между ними.

Большинство разработчиков Xamarin.Forms предпочитают подход библиотеки. В этой книге большую часть решений использовать библиотеку. Те, которые используют SAP включают **Sap** суффикс имени проекта.

При использовании SAP код в общем проекте можно выполнить разный код для различных платформ с помощью C# директивы препроцессора (`#if`, #`elif`, и `#endif`) с помощью этих предопределенных идентификаторов:

- iOS: `__IOS__`
- Android: `__ANDROID__`
- UWP: `WINDOWS_UWP`

В общей библиотеке выяснить, какие платформы, вы используете во время выполнения, как вы увидите далее в этой главе.

## <a name="labels-for-text"></a>Метки для текста

[ **Greetings** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Greetings) решении показано, как добавление нового C# файл **Greetings** проекта. Этот файл определяет класс с именем `GreetingsPage` , наследуемый от класса `ContentPage`. В этой книге, большинство проектов содержат один `ContentPage` производных, имя которого является имя проекта с суффиксом `Page` добавляется.

`GreetingsPage` Конструктор создает [ `Label` ](xref:Xamarin.Forms.Label) представление, которое является представление Xamarin.Forms, которое отображает текст. [ `Text` ](xref:Xamarin.Forms.Label.Text) Свойству присвоено тексту, отображаемому элементом `Label`. Эта программа задает `Label` для `Content` свойство `ContentPage`. Конструктор `App` создает экземпляр класса `GreetingsPage` и задает для его `MainPage` свойство.

Текст отображается в левом верхнем углу страницы. В iOS это означает, что он перекрывает строки состояния страницы. Существует несколько решений этой проблемы:

### <a name="solution-1-include-padding-on-the-page"></a>Решение 1. Включить внутренние поля на странице

Задайте [ `Padding` ](xref:Xamarin.Forms.Page.Padding) свойство на странице. `Padding` имеет тип [ `Thickness` ](xref:Xamarin.Forms.Thickness), структуру, используя четыре свойства:

- [`Left`](xref:Xamarin.Forms.Thickness.Left)
- [`Top`](xref:Xamarin.Forms.Thickness.Top)
- [`Right`](xref:Xamarin.Forms.Thickness.Right)
- [`Bottom`](xref:Xamarin.Forms.Thickness.Bottom)

`Padding` Определяет область внутри страницы, где исключается содержимого. Это позволяет `Label` Чтобы избежать перезаписи строки состояния iOS.

### <a name="solution-2-include-padding-just-for-ios-sap-only"></a>Решение 2. Включить внутренние поля только для iOS (только для SAP)

Задать свойство «Заполнение» только для iOS с помощью SAP с C# директивы препроцессора. Это показано в [ **GreetingsSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/GreetingsSap) решения.

### <a name="solution-3-include-padding-just-for-ios-pcl-or-sap"></a>Решение 3. Включить внутренние поля только для iOS (переносимой библиотеки Классов или SAP)

В версии Xamarin.Forms, используемый для книги `Padding` свойства, относящиеся к операций ввода-вывода в SAP или переносимой библиотеки Классов можно выбрать с помощью [ `Device.OnPlatform` ](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) или [ `Device.OnPlatform<T>` ](xref:Xamarin.Forms.Device.OnPlatform*) статический метод. Эти методы являются нерекомендуемыми

`Device.OnPlatform` Методы используются для выполнения кода для конкретных платформ или для выбора значений, зависящих от платформы. На внутреннем уровне они делают использование [ `Device.OS` ](xref:Xamarin.Forms.Device.OS) статический только для чтения свойство, которое возвращает элемент [ `TargetPlatform` ](xref:Xamarin.Forms.TargetPlatform) перечисления:

- [`iOS`](xref:Xamarin.Forms.TargetPlatform.iOS)
- [`Android`](xref:Xamarin.Forms.TargetPlatform.Android)
- [`Windows`](xref:Xamarin.Forms.TargetPlatform.Windows) для устройств универсальной платформы Windows.

`Device.OnPlatform` Методы, `Device.OS` свойство и `TargetPlatform` перечисления отменяется, теперь устарело. Вместо этого используйте [ `Device.RuntimePlatform` ](xref:Xamarin.Forms.Device.RuntimePlatform) свойство и сравнение `string` возвращаемое значение с следующие статические поля:

- [`iOS`](xref:Xamarin.Forms.Device.iOS), строка «iOS»
- [`Android`](xref:Xamarin.Forms.Device.Android), строка «Android»
- [`UWP`](xref:Xamarin.Forms.Device.UWP), строка «UWP», ссылающийся на универсальную платформу Windows

[ `Device.Idiom` ](xref:Xamarin.Forms.Device.Idiom) Связана статическое свойство только для чтения. Возвращает член [ `TargetIdiom` ](xref:Xamarin.Forms.TargetIdiom), который содержит следующие члены:

- [`Desktop`](xref:Xamarin.Forms.TargetIdiom.Desktop)
- [`Tablet`](xref:Xamarin.Forms.TargetIdiom.Tablet)
- [`Phone`](xref:Xamarin.Forms.TargetIdiom.Phone)
- [`Unsupported`](xref:Xamarin.Forms.TargetIdiom.Unsupported) не используется

Для iOS и Android, отсечение между `Tablet` и `Phone` с книжной шириной 600 единиц. На платформе Windows `Desktop` указывает приложения универсальной платформы Windows, под управлением Windows 10 и `Phone` указывает приложения универсальной платформы Windows, под управлением Windows 10 приложение.

## <a name="solution-3a-set-margin-on-the-label"></a>Решение 3a. Задание границы метки

[ `Margin` ](xref:Xamarin.Forms.View.Margin) Свойство было введено слишком поздно должны быть включены в книге, но он также является типом `Thickness` и его можно установить при `Label` для определения области за пределами представления, который включен в расчет макет представления.

`Padding` Свойство доступно только на [ `Layout` ](xref:Xamarin.Forms.Layout) и [ `Page` ](xref:Xamarin.Forms.Page) производные от него. `Margin` Свойство доступно для всех [ `View` ](xref:Xamarin.Forms.View) производные от него.

## <a name="solution-4-center-the-label-within-the-page"></a>Решение 4. Center метку в странице

Можно выровнять по центру `Label` в `Page` (или поместить его в одном из восьми мест), задав [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) и [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) свойства `Label` значение типа [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions). `LayoutOptions` Структура определяет два свойства:

- [ `Alignment` ](xref:Xamarin.Forms.LayoutOptions.Alignment) Свойство типа [ `LayoutAlignment` ](xref:Xamarin.Forms.LayoutAlignment), это перечисление с четырьмя членами: [ `Start` ](xref:Xamarin.Forms.LayoutAlignment.Start), что означает левого или верхнего в зависимости от Ориентация [ `Center` ](xref:Xamarin.Forms.LayoutAlignment.Center), [ `End` ](xref:Xamarin.Forms.LayoutAlignment.End), означающее правую или нижнюю в зависимости от ориентации, и [ `Fill` ](xref:Xamarin.Forms.LayoutAlignment.Fill).

- [ `Expands` ](xref:Xamarin.Forms.LayoutOptions.Expands) Свойство типа `bool`.

Обычно эти свойства не используются напрямую. Вместо этого сочетания этих двух свойств предоставляемых восьми статических свойств только для чтения типа `LayoutOptions`:

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

`HorizontalOptions` и `VerticalOptions` приведены наиболее важные свойства в макете Xamarin.Forms и обсуждаются более подробно в [ **Глава 4. Раскрутка стека**](chapter04.md).

Ниже приведен результат с `HorizontalOptions` и `VerticalOptions` свойства `Label` равными `LayoutOptions.Center`:

[![Тройной снимок экрана приветствия программы](images/ch02fg05-small.png "горизонтально и вертикально по центру метки")](images/ch02fg05-large.png#lightbox "метки по горизонтали и вертикали по центру")

## <a name="solution-5-center-the-text-within-the-label"></a>Решение 5. Центрирование текста в метке

Выравнивание текста по центру (или поместить его в восемь места на странице), задав [ `HorizontalTextAlignment` ](xref:Xamarin.Forms.Label.HorizontalTextAlignment) и [ `VerticalTextAlignment` ](xref:Xamarin.Forms.Label.VerticalTextAlignment) свойства `Label` членом [ `TextAlignment` ](xref:Xamarin.Forms.TextAlignment) перечисления:

- [`Start`](xref:Xamarin.Forms.TextAlignment.Start), значение слева или сверху (в зависимости от ориентации)
- [`Center`](xref:Xamarin.Forms.TextAlignment.Center)
- [`End`](xref:Xamarin.Forms.TextAlignment.End), то есть правую или нижнюю (в зависимости от ориентации)

Эти свойства определяются только `Label`, тогда как `HorizontalAlignment` и `VerticalAlignment` свойства, определенные в `View` и наследуют все `View` производные от него. Результаты visual покажутся схожими, но они сильно отличаются как показано в следующей главе.

## <a name="related-links"></a>Связанные ссылки

- [Глава 2 полнотекстового поиска (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch02-Apr2016.pdf)
- [Примеры Глава 2](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)
- [Глава 2 F# примеры](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/FS)
- [Начало работы с Xamarin.Forms](~/xamarin-forms/get-started/index.md)
