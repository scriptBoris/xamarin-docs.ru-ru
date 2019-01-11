---
title: Сводка Глава 3. Более подробная информация
description: Создание мобильных приложений с помощью Xamarin.Forms. Сводка Глава 3. Более подробная информация
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2E5581A6-4D3E-4BD5-9FDB-ACBA0F0FC734
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 11b0a9c5b165df2b0d02bb09fb282cc18290e6ca
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207854"
---
# <a name="summary-of-chapter-3-deeper-into-text"></a>Сводка Глава 3. Более подробная информация

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)

В этой главе рассматриваются [ `Label` ](xref:Xamarin.Forms.Label) представления более подробно, включая цвета, шрифты и форматирование.

## <a name="wrapping-paragraphs"></a>Упаковки абзацев

Когда [ `Text` ](xref:Xamarin.Forms.Label.Text) свойство `Label` содержащий длинный текст `Label` автоматически переносит в несколько строк, как показано [ **Baskervilles** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/Baskervilles) образец. Вы можете внедрить коды Юникода, например «\u2014» для тире, или C# символы, такие как «\r» переключиться на новую строку.

Когда [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) и [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) свойства `Label` присваивается `LayoutOptions.Fill`, общего размера `Label` регулируется пространство, контейнера делает доступными. `Label` Считается *ограниченного*. Размер `Label` — это размер контейнера.

Когда `HorizontalOptions` и `VerticalOptions` свойства присваиваются значения, отличные от `LayoutOptions.Fill`, размер `Label` регулируется места, необходимого для отображения текста, вплоть до размера, который делает доступными для контейнера `Label`. `Label` Считается *неограниченного* и определяет свой собственный размер.

(Примечание: Условия *ограниченного* и *неограниченного* может показаться неочевидным, так, как создавать неограниченное представление обычно меньше, чем ограниченный Обзор. Кроме того эти условия не используются постоянно в первые главы книги.)

Представления, такие как `Label` может быть ограничен в одно измерение и без ограничений в другой. Объект `Label` только будет переносить текст на нескольких строках, если он ограничен по горизонтали.

Если `Label` является ограничены, он может занимать значительно больше места, чем требуется для текста. Текст может располагаться в общей области `Label`. Задайте [ `HorizontalTextAlignment` ](xref:Xamarin.Forms.Label.HorizontalTextAlignment) свойства является членом [ `TextAlignment` ](xref:Xamarin.Forms.TextAlignment) перечисления ([`Start`](xref:Xamarin.Forms.TextAlignment.Start), [ `Center` ](xref:Xamarin.Forms.TextAlignment.Center), или [ `End` ](xref:Xamarin.Forms.TextAlignment.Center)) для управления выравниванием все строки абзаца. По умолчанию используется `Start` и по левому краю текста.

Задайте [ `VerticalTextAlignment` ](xref:Xamarin.Forms.Label.VerticalTextAlignment) свойства является членом `TextAlignment` перечисления для позиционирования текста в верхнем, по центру или по нижней части области, `Label`.

Задайте [ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode) свойства является членом [ `LineBreakMode` ](xref:Xamarin.Forms.LineBreakMode) перечисления ([`WordWrap`](xref:Xamarin.Forms.LineBreakMode.WordWrap), [ `CharacterWrap` ](xref:Xamarin.Forms.LineBreakMode.CharacterWrap), [ `NoWrap` ](xref:Xamarin.Forms.LineBreakMode.NoWrap), [ `HeadTruncation` ](xref:Xamarin.Forms.LineBreakMode.HeadTruncation), [ `MiddleTruncation` ](xref:Xamarin.Forms.LineBreakMode.MiddleTruncation), или [ `TailTruncation` ](xref:Xamarin.Forms.LineBreakMode.TailTruncation)) для элемент управления, как несколько линий в разрыва абзаца или усекаются.

## <a name="text-and-background-colors"></a>Цвета текста и фона

Задайте [ `TextColor` ](xref:Xamarin.Forms.Label.TextColor) и [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor) свойства `Label` для [ `Color` ](xref:Xamarin.Forms.Color) значения для управления цветом текста и фона.

`BackgroundColor` Применяется к фону всю область, занимаемая `Label`. В зависимости от `HorizontalOptions` и `VerticalOptions` свойства, что размер может быть значительно больше, чем области, необходимые для отображения текста. Цвет можно использовать для экспериментов с различными значениями `HorizontalOptions`, `VerticalOptions`, `HorizontalExeAlignment`, и `VerticalTextAlignment` чтобы увидеть, как они влияют на размер и положение `Label`и размер и положение текста в `Label`.

## <a name="the-color-structure"></a>Структуры Color

[ `Color` ](xref:Xamarin.Forms.Color) Структуры можно задать цвета, как значения красного, зеленого, синего (RGB) или оттенок-насыщенность-яркость (HSL)) или с именем цвета. Канал альфа-версия доступна также для указания прозрачности.

Используйте `Color` конструктор для указания:

- [серый оттенок](xref:Xamarin.Forms.Color.%23ctor(System.Double))
- [значение RGB](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double))
- [RGB-значение с прозрачностью](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double))

Аргументы являются `double` значения в диапазоне от 0 до 1.

Можно также использовать несколько статических методов для создания `Color` значения:

- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double)) для `double` RGB-значения от 0 до 1
- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Int32,System.Int32,System.Int32)) для значений RGB целое число от 0 до 255.
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Double,System.Double,System.Double,System.Double)) для `double` значения RGB с прозрачностью
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32)) для целых значений RGB с прозрачностью
- [`Color.FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) для `double` значения HSL с прозрачностью
- [`Color.FromUint`](xref:Xamarin.Forms.Color.FromUint(System.UInt32)) для `uint` значение (B + 256 * (G + 256 * (R + 256 * A)))
- [`Color.FromHex`](xref:Xamarin.Forms.Color.FromHex(System.String)) для `string` формате шестнадцатеричных цифр в формате «#AARRGGBB» или «#RRGGBB» или «#ARGB» или «#RGB», где каждая буква соответствует шестнадцатеричная цифра для альфа, красный, зеленый и синий каналы. Этот метод является первичным, используемый для преобразования цветов XAML, как описано в [Глава 7, XAML и кода](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md).

После создания `Color` значение является неизменяемым. Характеристики цвет можно получить из следующих свойств:

- [`R`](xref:Xamarin.Forms.Color.R)
- [`G`](xref:Xamarin.Forms.Color.G)
- [`B`](xref:Xamarin.Forms.Color.B)
- [`A`](xref:Xamarin.Forms.Color.A)
- [`Hue`](xref:Xamarin.Forms.Color.Hue)
- [`Saturation`](xref:Xamarin.Forms.Color.Saturation)
- [`Luminosity`](xref:Xamarin.Forms.Color.Luminosity)

Ниже приведены все `double` значения в диапазоне от 0 до 1.

`Color` также определяет 240 общедоступного статического поля только для чтения для обычных цветов. Во время написания книги только 17 часто применяемые цвета были доступны.

Другой открытое статическое доступное только для чтения поле определяет цвет с помощью всех цветовых каналов, имеет нулевое значение:

- [`Color.Transparent`](xref:Xamarin.Forms.Color.Transparent)

Несколько методов экземпляра позволяет изменять существующий цвет для создания нового цвета:

- [`AddLuminosity`](xref:Xamarin.Forms.Color.AddLuminosity(System.Double))
- [`MultiplyAlpha`](xref:Xamarin.Forms.Color.MultiplyAlpha(System.Double))
- [`WithHue`](xref:Xamarin.Forms.Color.WithHue(System.Double))
- [`WithLuminosity`](xref:Xamarin.Forms.Color.WithLuminosity(System.Double))
- [`WithSaturation`](xref:Xamarin.Forms.Color.WithSaturation(System.Double))

Наконец два статических свойств только для чтения определить значение специальные цвета:

- [`Color.Default`](xref:Xamarin.Forms.Color.Default), значение все каналы &ndash;1
- [`Color.Accent`](xref:Xamarin.Forms.Color.Accent)

`Color.Default` предусмотрено для обеспечения платформы цветовую схему, а следовательно имеет другой смысл в различных контекстах на разных платформах. По умолчанию, цветовых схем платформы:

- iOS: Темно-текст на фоне света
- Android: Текст на темном фоне (в книге) или темно-текст на фоне светлой (для проектирования материалы по совместимости приложений в **master** ветвь репозиторий примеров кода)
- UWP: Темно-текст на фоне света

`Color.Accent` Среди конкретных платформ (и иногда с возможностью выбора пользователем) цвет, который будет отображаться на Темная или Светлая фона.

## <a name="changing-the-application-color-scheme"></a>Изменение цветовой схемы приложения

Различные платформы имеют цветовую схему по умолчанию, как показано в списке выше.

При разработке для Android, можно переключиться на темно на light схему, указав "светлой" темой в файле Android.Manifest.xml или [Добавление AppCompat и материальному дизайну](~/xamarin-forms/platform/android/appcompat-material-design.md).

Для платформ Windows, цветовую тему обычно выбирается пользователем, однако вы можете добавить `RequestedTheme` атрибут принимать значение `Light` или `Dark` в файле App.xaml платформы. По умолчанию содержит файл App.xaml в проекте UWP `RequestedTheme` атрибут `Light`.

## <a name="font-sizes-and-attributes"></a>Размеры шрифтов и атрибуты

Задайте [ `FontFamily` ](xref:Xamarin.Forms.Label.FontFamily) свойство `Label` в строку, например «Times Roman» выберите семейство шрифтов. Тем не менее необходимо указать семейство шрифтов, который поддерживается на определенную платформу и платформы не согласованы в этом отношении.

Задайте [ `FontSize` ](xref:Xamarin.Forms.Label.FontSize) свойство `Label` для `double` для указания приблизительную высоту шрифта. См. в разделе [Глава 5, работа с размерами](chapter05.md), Дополнительные сведения о интеллектуально выборе размеров шрифта.

Также можно получить одним из нескольких размерах шрифта предустановленного зависят от платформы. Статический [ `Device.GetNamedSize` ](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,System.Type)) метод и [перегружать](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,Xamarin.Forms.Element)) обе эти функции возвращают `double` значение размера шрифта, соответствующие платформы на основе членов класса [ `NamedSize` ](xref:Xamarin.Forms.NamedSize)перечисления ([`Default`](xref:Xamarin.Forms.NamedSize.Default), [ `Micro` ](xref:Xamarin.Forms.NamedSize.Micro), [ `Small` ](xref:Xamarin.Forms.NamedSize.Small), [ `Medium` ](xref:Xamarin.Forms.NamedSize.Medium),  и [ `Large` ](xref:Xamarin.Forms.NamedSize.Large)). Значение, возвращаемое из `Medium` член не обязательно так же, как `Default`. [ **NamedFontSizes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) пример отображает текст с этими с именем Size.

Задайте [ `FontAttributes` ](xref:Xamarin.Forms.Label.FontAttributes) свойство `Label` на член [ `FontAttributes` ](xref:Xamarin.Forms.FontAttributes) перечисления, [ `Bold` ](xref:Xamarin.Forms.FontAttributes.Bold), [ `Italic` ](xref:Xamarin.Forms.FontAttributes.Italic), или [ `None` ](xref:Xamarin.Forms.FontAttributes.None). Вы можете объединить `Bold` и `Italic` члены с C# побитового оператора OR.

## <a name="formatted-text"></a>Форматированный текст

Во всех примерах, весь текст, отображаемый с `Label` отформатирован равномерно. Для изменения форматирования внутри текстовой строки, не устанавливайте `Text` свойство `Label`. Вместо этого задайте [ `FormattedText` ](xref:Xamarin.Forms.Label.FormattedText) свойство для объекта типа [ `FormattedString` ](xref:Xamarin.Forms.FormattedString).

`FormattedString` имеет [ `Spans` ](xref:Xamarin.Forms.FormattedString.Spans) свойство, которое является коллекцией [ `Span` ](xref:Xamarin.Forms.Span) объектов. Каждый `Span` объект имеет свой собственный [ `Text` ](xref:Xamarin.Forms.Span.Text), [ `FontFamily` ](xref:Xamarin.Forms.Span.FontFamily), [ `FontSize` ](xref:Xamarin.Forms.Span.FontSize), [ `FontAttributes` ](xref:Xamarin.Forms.Span.FontAttributes), [ `ForegroundColor` ](xref:Xamarin.Forms.Span.ForegroundColor), и [ `BackgroundColor` ](xref:Xamarin.Forms.Span.BackgroundColor) свойства.

[ **VariableFormattedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormText) образце показано использование `FormattedText` свойство для одной строки текста, и [ **VariableFormattedParagraph** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormPara) демонстрирует способ абзаца, как показано ниже:

[![Тройной снимок экрана: переменная в формате абзаца](images/ch03fg06-small.png "переменной форматированного текста метки")](images/ch03fg06-large.png#lightbox "переменной форматированного текста метки")

[ **NamedFontSizes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) программа использует один `Label` и `FormattedString` объекта для отображения всех размеров шрифта именованный для каждой платформы.



## <a name="related-links"></a>Связанные ссылки

- [Глава 3 полнотекстового поиска (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch03-Apr2016.pdf)
- [Примеры Глава 3](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)
- [Глава 3 F# примеры](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/FS)
- [Label](~/xamarin-forms/user-interface/text/label.md)
- [Работа с цветами](~/xamarin-forms/user-interface/colors.md)
