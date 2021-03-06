---
title: Сводка Глава 7. XAML и кода
description: 'Создание мобильных приложений с помощью Xamarin.Forms: Сводка Глава 7. XAML и кода'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: E91F387B-CE90-481C-8D90-CB25519BFD2B
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: ce4dde3716176daf826678809339afb84c25d84a
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53052573"
---
# <a name="summary-of-chapter-7-xaml-vs-code"></a>Сводка Глава 7. XAML и кода

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)

> [!NOTE]
> Заметки на этой странице указывать области, где различаются Xamarin.Forms материал, представленный в книге.

Xamarin.Forms поддерживает язык разметки на основе XML, называемый Extensible Application Markup Language или XAML (произносится «это»). XAML представляет собой альтернативу C# в определении макета пользовательского интерфейса приложения Xamarin.Forms и определения привязок между элементами пользовательского интерфейса и базовые данные.

## <a name="properties-and-attributes"></a>Свойства и атрибуты

Xamarin.Forms классы и структуры становятся XML-элементов в XAML, а свойства этих классов и структур XML-атрибуты. Для реализации в XAML, класс обычно должен иметь открытый конструктор без параметров. Все свойства, заданные в XAML должен иметь открытый `set` методы доступа.

Для свойств базовых типов данных (`string`, `double`, `bool`, и так далее), средство синтаксического анализа XAML использует стандарт `TryParse` методы преобразования в эти типы параметров атрибутов. Средство синтаксического анализа XAML можно также легко обрабатывать типы перечислений, и она может объединять членов перечисления, если тип перечисления помечается как содержащий `Flags` атрибута.

Чтобы получить дополнительные средства синтаксического анализа XAML, может включать более сложные типы (или свойства этих типов) [ `TypeConverterAttribute` ](xref:Xamarin.Forms.TypeConverterAttribute) , определяющий класс, производный от [ `TypeConverter` ](xref:Xamarin.Forms.TypeConverter) который поддерживает преобразование из строковые значения для этих типов. Например [ `ColorTypeConverter` ](xref:Xamarin.Forms.ColorTypeConverter) преобразует цвета в строки, например «#rrggbb», имени и `Color` значения.

## <a name="property-element-syntax"></a>Синтаксис элемента свойства

В XAML классы и объекты, созданные на их основе выражаются в виде XML-элементов. Они известны как *объектных элемента*. Большинство свойств этих объектов, выражаются в виде атрибутов XML. Они называются *атрибуты свойства*.

Иногда свойство должно быть присвоено объект, который нельзя выразить в виде простой строки. В таком случае XAML поддерживает тег *элемент свойства* состоит из имени класса и имя свойства, разделенные точкой. Объектный элемент, затем может отображаться заключенный в теги элемента свойства.

## <a name="adding-a-xaml-page-to-your-project"></a>Добавление страницы XAML в проект

Может содержать страницы XAML Xamarin.Forms переносимой библиотеки классов, при ее создании или страницу XAML можно добавить в существующий проект. В диалоговом окне, чтобы добавить новый элемент, выберите элемент, который ссылается на страницу XAML или `ContentPage` и XAML. (Не `ContentView`.)

> [!NOTE]
> В этой главе было написано с момента изменения параметров Visual Studio.

Будут созданы два файла: файл XAML с .xaml расширение имени файла и файла с расширением на C#. xaml.cs. Файл C# часто называют *кода* файла XAML. Файл с выделенным кодом является определение разделяемого класса, производного от `ContentPage`. Во время сборки XAML анализируется и другом определении разделяемого класса создается для того же класса. Этот созданный класс содержит метод с именем `InitializeComponent` , вызывается из конструктора файл с выделенным кодом.

Во время выполнения, по завершении `InitializeComponent` вызвать, все элементы в XAML-файл был создан и инициализирован так, как если бы они была создана в коде C#.

Корневой элемент в файле XAML — `ContentPage`. Корневой тег содержит по крайней мере два объявления пространства имен XML, один для Xamarin.Forms элементов, а также другие определения `x` префикс для элементов и атрибутов, характерными для всех реализаций XAML. Также содержит корневой тег `x:Class` атрибут, указывающий пространство имен и имя класса, производного от `ContentPage`. Это совпадает с именем пространства имен и класса в файле кода.

Сочетание XAML и кода демонстрируется путем [ **CodePlusXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07) образца.

## <a name="the-xaml-compiler"></a>Компилятор XAML

Xamarin.Forms есть компилятор XAML, но его использование является необязательным, основанная на использовании [ `XamlCompilationAttribute` ](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute). Если XAML не компилируется, XAML анализируется во время сборки, и файл XAML будет внедрен в PCL, а также где он разбирается во время выполнения. Если XAML компилируется, процесс построения преобразует XAML в двоичной форме, и время обработки является более эффективным.

## <a name="platform-specificity-in-the-xaml-file"></a>Специфичность платформы в файле XAML

В XAML [ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1) класс может использоваться для выбора разметки зависят от платформы. Это универсальный класс и должен быть создан при помощи `x:TypeArguments` атрибут, который соответствует целевому типу. [ `OnIdiom` ](xref:Xamarin.Forms.OnIdiom`1) Класс похожие, но используется гораздо реже.

Использование `OnPlatform` был изменен с момента публикации книги. Изначально он использовался в сочетании с свойства с именем `iOS`, `Android`, и `WinPhone`. Теперь он используется с дочерним элементом [ `On` ](xref:Xamarin.Forms.On) объектов. Задайте [ `Platform` ](xref:Xamarin.Forms.On.Platform) строку соответствуют открытые свойства `const` поля [ `Device` ](xref:Xamarin.Forms.Device) класса. Задайте [ `Value` ](xref:Xamarin.Forms.On.Value) соответствует значение `x:TypeArguments` атрибута `OnPlatform` тега.

`OnPlatform` демонстрируется в [ **ScaryColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/ScaryColorList) образец, поэтому вызов, так как она содержит блоки практически одинаковых XAML. Существование этого повторы разметки предполагает, что методы должны быть доступны для его уменьшения.

## <a name="the-content-property-attributes"></a>Атрибуты свойства содержимого

Некоторые элементы свойства возникает довольно часто, такие как `<ContentPage.Content>` тега корневой элемент `ContentPage`, или `<StackLayout.Children>` тег, который содержит дочерние элементы `StackLayout`.

Каждый класс может определить одно свойство с [ `ContentPropertyAttribute` ](xref:Xamarin.Forms.ContentPropertyAttribute) к классу. Для этого свойства теги элемента свойства не являются обязательными. `ContentPage` Определяет свойство содержимого в `Content`, и `Layout<T>` (класс, от которого `StackLayout` производный) определяет свойство содержимого в `Children`. Эти теги элементов свойства не являются обязательными.

Элемент свойства из `Label` является `Text`.

## <a name="formatted-text"></a>Форматированный текст

[ **TextVariations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/TextVariations) образец содержит несколько примеров параметр `Text` и `FormattedText` свойства `Label`. В XAML `Span` объекты отображаются как дочерние элементы `FormattedString` объекта.

 Если присвоить многострочной строке `Text` свойство, символы в конец строки преобразуются в пробелы, но символы окончания строки сохраняются в том случае, когда многострочной строке отображается как содержание `Label` или `Label.Text` теги:

 [![Тройной снимок экрана: текст варианты совместного использования](images/ch07fg03-small.png "вариации текст в формате")](images/ch07fg03-large.png#lightbox "вариантов форматирования текста")

## <a name="related-links"></a>Связанные ссылки

- [Глава 7 полнотекстового поиска (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf)
- [Глава 7 примеры](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)
- [Глава 7 F# образца](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/FS/CodePlusXaml)
- [Основы XAML](~/xamarin-forms/xaml/xaml-basics/index.md)
