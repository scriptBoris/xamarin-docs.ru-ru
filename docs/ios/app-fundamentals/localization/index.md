---
title: Локализация в Xamarin.iOS
description: В этом документе описываются возможности локализации операций ввода-вывода и инструкции по использованию этих возможностей в приложениях Xamarin.iOS. Он описывает язык, языковой стандарт, файлы строки, изображения запуска и многое другое.
ms.prod: xamarin
ms.assetid: DFD9EB4A-E536-18E4-C8FD-679BA9C836D8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/28/2017
ms.openlocfilehash: 7f05243196a9b916ac5c7b73df957262604ccb11
ms.sourcegitcommit: d70fcc6380834127fdc58595aace55b7821f9098
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268814"
---
# <a name="localization-in-xamarinios"></a>Локализация в Xamarin.iOS

_В этом документе рассматриваются возможности локализации с помощью пакета SDK и способах доступа к ним с помощью Xamarin._

Ссылаться на [кодировки интернационализации](encodings.md) инструкции о включении наборов и кодовых страниц, символ в приложениях, которые нужно обработать данные не в Юникоде.

## <a name="ios-platform-features"></a>Возможности на платформе iOS

В этом разделе описываются некоторые возможности локализации в iOS. Перейдите к [разделу](#basics) определенный код и примеры.

### <a name="language"></a>Язык

Пользователям выбрать язык в **параметры** приложения. Этот параметр влияет на язык строк и изображений, отображаемых в операционной системе и в приложениях. 

Определение языка, используемого в приложении, получить первый элемент `NSBundle.MainBundle.PreferredLocalizations`:

```csharp
var lang = NSBundle.MainBundle.PreferredLocalizations[0];
```

Это значение будет равно код языка `en` для английского языка, `es` для испанского языка, `ja` для японского языка, и т. д. Возвращаемое значение будет ограничен одним локализации, поддерживаемые приложением (с использованием резервных правил для определения оптимального совпадения).

Код приложения не всегда необходимо выполнять проверку этого значения — Xamarin и оба iOS предоставляют функции, помогающие автоматически предоставлять правильный строку или ресурсов для языка пользователя. Эти возможности описаны в остальной части этого документа.

> [!NOTE]
> Используйте `NSLocale.PreferredLanguages` для определения настроек языка пользователя, независимо от того, локализации, поддерживаемые приложением. Значения, возвращаемые этим методом, изменения в iOS 9; в разделе [Технические заметки TN2418](https://developer.apple.com/library/content/technotes/tn2418/_index.html) подробные сведения.

### <a name="locale"></a>Языковой стандарт

Пользователи выбирают их языкового стандарта **параметры** приложения. Этот параметр влияет на способ форматирования дат, времени, чисел и валюты.

Это позволяет пользователям выбирать ли они видят форматы времени 12- или 24-часовом, является ли их десятичного разделителя запятую или точку и порядок день, месяц и год в отображение дат.

С помощью Xamarin у вас есть доступ к классам обоих Apple iOS (`NSNumberFormatter`) и классы .NET в System.Globalization. Разработчики должны оценить которого лучше подходит для их потребностям, как различные функции, доступные в каждом. В частности Если извлечение и отображение цены покупки из приложений с помощью StoreKit следует использовать классы форматирования Apple для возвращаемых сведений цены.

Текущий языковой стандарт можно запрашивать с одним из двух способов:

- `NSLocale.CurrentLocale.LocaleIdentifier`
- `NSLocale.AutoUpdatingCurrentLocale.LocaleIdentifier`

Первое значение могут кэшироваться операционной системой и поэтому может не всегда отражают языкового стандарта текущего выбранного пользователя. Второе значение используйте для получения текущего выбранного языка.

> [!NOTE]
> Моно (выполнения .NET, на которой основан Xamarin.iOS) и Apple iOS API-интерфейсы не поддерживают идентичными наборами сочетания языка или региона.
> По этой причине невозможно выбрать сочетание языка или региона в iOS **параметры** приложение, которое не соответствует значение на допустимое Mono. Например английский язык, iPhone и ее области в Испании, то следующие интерфейсы API для получения различных значений:
> 
> - `CurrentThead.CurrentCulture`: en US (моно API)
> - `CurrentThread.CurrentUICulture`: en US (моно API)
> - `NSLocale.CurrentLocale.LocaleIdentifier`: en_ES (Apple API)
>
> Поскольку использует моно `CurrentThread.CurrentUICulture` выберите ресурсы и `CurrentThread.CurrentCulture` для форматирования дат и валют, на основе моно локализации (например, с RESX-файлы) не может возврат ожидаемых результатов для этих сочетаний языка или региона. В таких ситуациях используют Apple API-интерфейсы для локализации при необходимости.

### <a name="nscurrentlocaledidchangenotification"></a>NSCurrentLocaleDidChangeNotification

Создает iOS `NSCurrentLocaleDidChangeNotification` когда пользователь обновляет свой языковой стандарт. Приложения можно ожидать передачи данных для этого уведомления пока они выполняются и можно внести соответствующие изменения в пользовательский интерфейс.

<a name="basics" />

## <a name="localization-basics-in-ios"></a>Основные сведения о локализации в iOS

Следующие функции iOS легко будет применить в Xamarin, чтобы предоставлять локализованные ресурсы для отображения пользователю. Ссылаться на [TaskyL10n пример](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) чтобы узнать, как реализовать эти идеи.

### <a name="specifying-default-and-supported-languages-in-infoplist"></a>Указания по умолчанию и поддерживаемые языки в файле Info.plist.

В [технические вопросы и ответы QA1828: определение языка для приложения iOS](https://developer.apple.com/library/content/qa/qa1828/_index.html), Apple описывает Выбор языка для использования в приложении iOS. Следующие факторы влияют на какой язык отображается:

- Предпочитаемые языки пользователя (в **параметры** приложения)
- Локализации, входит в состав приложения (.lproj папки)
- `CFBundleDevelopmentRegion` (**Info.plist** значение, указывающее язык по умолчанию для приложения)
- `CFBundleLocalizations` (**Info.plist** массиве, указывающий все поддерживаемые локализации)

Как указано в технических вопросов и ответов `CFBundleDevelopmentRegion` представляет приложения по умолчанию язык и региональные стандарты. Если приложение не поддерживает явным образом любого предпочтительный языка пользователя, он будет использовать язык, указанный по этому полю. 

> [!IMPORTANT]
> iOS 11 применяется этот механизм выбора языка более строго, чем предыдущие версии операционной системы. По этой причине любого приложения iOS 11, явно не объявлен поддерживаемых локализации — включая .lproj папки или задав значение для `CFBundleLocalizations` — может отображаться другой язык в iOS 11, чем это было в iOS 10.

Если `CFBundleDevelopmentRegion` не был указан в **Info.plist** файла средства построения Xamarin.iOS в настоящее время используется значение по умолчанию `en_US`. Хотя такое соответствие может измениться в будущем выпуске, это означает, языком по умолчанию является английский язык.

Чтобы убедиться, что приложение выбирает ожидаемый язык, выполните следующие действия:

- Укажите язык по умолчанию. Откройте **Info.plist** и использовать **источника** представление, чтобы задать значение для `CFBundleDevelopmentRegion` ключа; в формате XML, он должен выглядеть следующим образом:

```xml
<key>CFBundleDevelopmentRegion</key>
<string>es</string>
```

В этом примере используется «es» для указания, если ни один из пользователей предпочитаемый языков по умолчанию на испанский язык.

- Объявите все поддерживаемые локализации. В **Info.plist**, используйте **источника** представление, чтобы задать массив `CFBundleLocalizations` ключа; в формате XML, он должен выглядеть следующим образом:

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>es</string>
    ...
</array>
```

Приложения Xamarin.iOS, локализованные с помощью механизмов .NET, такие как RESX-файлы необходимо предоставить эти **Info.plist** также значения.

Дополнительные сведения об этих **Info.plist** ключей, взгляните на Apple [справочника по ключам список свойств сведения](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html).

### <a name="localizedstring-method"></a>Метод LocalizedString

`NSBundle.MainBundle.LocalizedString` Метод выполняет поиск локализованного текста, который будет сохранено в **.strings** файлы в проекте. Эти файлы организованы в специальную каталоги с языком **.lproj** суффикс.

#### <a name="strings-file-locations"></a>расположения файлов .strings

- **Base.lproj** — это каталог, содержащий ресурсы для языка по умолчанию.
  Часто находится в корневом каталоге проекта (но также может быть помещен в **ресурсов** папки).
- **<language>.lproj** каталоги создаются для каждого поддерживаемого языка, обычно в **ресурсов** папки.

Может существовать несколько разных **.strings** файлы в каталоге каждого языка:

- **Localizable.strings** — основной список локализованный текст.
- **InfoPlist.strings** — некоторые специальные клавиши разрешены в этом файле для перевода такие элементы, как имя приложения.
- **< имя storyboard > .strings** — необязательный файл, который содержит переводы для элементов пользовательского интерфейса в раскадровку.

**Действие при построении** для этих файлов должна быть **пакета ресурсов**.

#### <a name="strings-file-format"></a>формат файла .strings

Для значения локализованных строк используется синтаксис:

```console
/* comment */
"key"="localized-value";
```

Следует экранировать следующие символы в строках:

* `\"`  кавычки
* `\\`  Обратная косая черта
* `\n`  Новая строка

Ниже приведен пример **es/Localizable.strings** (т. е. Испанский) файл из примера:

```console
"<new task>" = "<new task>";
"Task Details" = "Detalles de la tarea";
"Name" = "Nombre";
"task name" = "nombre de la tarea";
"Notes" = "Notas";
"other task info"= "otra información de tarea";
"Done" = "Completo";
"Save" = "Guardar";
"Delete" = "Eliminar";
```

### <a name="images"></a>Изображения

Для локализации изображения в iOS:

1. Обратитесь к изображению в коде, например:

  ```csharp
  UIImage.FromBundle("flag");
  ```

2. Поместите файл изображения по умолчанию **flag.png** в **Base.lproj** (каталог CLR собственной разработки).

3. При необходимости установите локализованные версии изображения в **.lproj** папки для каждого языка (например) **ES.lproj**, **ja.lproj**). Используйте это имя **flag.png** в каждом каталоге языка.

Если изображение не отображается для определенного языка, iOS переключиться на родном языке папку по умолчанию и загрузить изображение из него.


#### <a name="launch-images"></a>Запустите изображений

Использовать стандартные соглашения об именовании для запуска образов (XIB и раскадровки для моделей iPhone 6) при помещении их в **.lproj** каталоги для каждого языка.

```console
Default.png
Default@2x.png
Default-568h@2x.png
LaunchScreen.xib
```

### <a name="app-name"></a>Имя приложения

Размещение **InfoPlist.strings** файла в **.lproj** каталог позволяет переопределить некоторые значения в приложении **Info.plist**, включая имя приложения:

```console
"CFBundleDisplayName" = "LeónTodo";
```

Другие ключи, которые можно использовать для [локализовать строки для конкретного приложения](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW21) являются:

- CFBundleName
- CFBundleShortVersionString
- NSHumanReadableCopyright

### <a name="dates-and-times"></a>Значения даты и времени

Несмотря на то, что можно использовать встроенные функции даты и времени .NET (вместе с текущим `CultureInfo`) для форматирования даты и времени для языкового стандарта, это приводит к пропуску языковому стандарту пользовательских параметров (которые могут быть настроены отдельно от языка).

Используйте iOS `NSDateFormatter` для вывода, который соответствует предпочтениями пользователя языкового стандарта. В следующем примере кода показаны основные Дата и время параметры форматирования:

```csharp
var date = NSDate.Now;
var df = new NSDateFormatter ();
df.DateStyle = NSDateFormatterStyle.Full;
df.TimeStyle = NSDateFormatterStyle.Long;
Debug.WriteLine ("Full,Long: " + df.StringFor(date));
df.DateStyle = NSDateFormatterStyle.Short;
df.TimeStyle = NSDateFormatterStyle.Short;
Debug.WriteLine ("Short,Short: " + df.StringFor(date));
df.DateStyle = NSDateFormatterStyle.Medium;
df.TimeStyle = NSDateFormatterStyle.None;
Debug.WriteLine ("Medium,None: " + df.StringFor(date));
```

Результаты для английского языка в США:

```console
Full,Long: Friday, August 7, 2015 at 10:29:32 AM PDT
Short,Short: 8/7/15, 10:29 AM
Medium,None: Aug 7, 2015
```

Результаты для Испанский (Испания):

```console
Full,Long: viernes, 7 de agosto de 2015, 10:26:58 GMT-7
Short,Short: 7/8/15 10:26
Medium,None: 7/8/2015
```

Ссылаться на Apple [модули форматирования даты](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/DataFormatting/Articles/dfDateFormatting10_4.html) Дополнительные сведения см. При тестировании форматирование зависящие от языкового стандарта даты и времени, установите флажки **iPhone язык** и **область** параметры.

<a name="rtl" />

### <a name="right-to-left-rtl-layout"></a>Макет справа налево (RTL)

iOS предоставляет ряд возможностей для помощи в создании приложения с поддержкой справа НАЛЕВО:

* Использовать автоматический макет `leading` и `trailing` атрибуты для выравнивание элемента управления (который соответствует слева и справа для английского языка, но обращено для языков с направлением письма справа НАЛЕВО).
  [ `UIStackView` ](~/ios/user-interface/controls/uistackview.md) Управления особенно полезна для размещение элементов управления, которые следует учитывать справа НАЛЕВО.
* Используйте `TextAlignment = UITextAlignment.Natural` для выравнивания текста (которая остается для большинства языков, но для RTL).
* `UINavigationController` автоматически отражает кнопки "Назад" и меняет направление считывание.

В следующих снимках экрана показано [локализованных учебных Tasky](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) арабском языке и иврите (несмотря на то, что в полях было введено английский):

[![](images/rtl-ar-sml.png "Локализация на арабском языке")](images/rtl-ar.png#lightbox "Arabic") 

[![](images/rtl-he-sml.png "Локализация на иврите")](images/rtl-he.png#lightbox "Hebrew")

автоматически меняет iOS `UINavigationController`, и другие элементы управления размещаются внутри `UIStackView` или выровнена с автоматическим макетом.
Справа НАЛЕВО текст локализуется с помощью **.strings** файлы в так же, как текст слева Направо.

<a name="code"/>

## <a name="localizing-the-ui-in-code"></a>Локализация пользовательского интерфейса в коде

[Tasky (локализованное в коде)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) образце показан способ локализации приложения, где пользовательского интерфейса создается в коде (а не XIBs или раскадровки).

### <a name="project-structure"></a>Структура проекта

![](images/solution-code.png "Дерево ресурсов")

### <a name="localizablestrings-file"></a>Файл Localizable.strings

Как описано выше, **Localizable.strings** формат файла состоит из пары "ключ значение". Раздел описывает намерение строки, а значение — переведенный текст для использования в приложении.

Испанский (**es**) локализации для примера приведены ниже:

```console
"<new task>" = "<new task>";
"Task Details" = "Detalles de la tarea";
"Name" = "Nombre";
"task name" = "nombre de la tarea";
"Notes" = "Notas";
"other task info"= "otra información de tarea";
"Done" = "Completo";
"Save" = "Guardar";
"Delete" = "Eliminar";
```

### <a name="performing-the-localization"></a>Выполнение локализации

В коде приложения, там, где это имеет значение отображаемого текста пользовательского интерфейса (будь то текст метки, или заполнитель входных данных и т. д) в коде используется iOS `LocalizedString` функции для получения правильное преобразование для отображения:

```csharp
var localizedString = NSBundle.MainBundle.LocalizedString ("key", "optional");
someControl.Text = localizedString;
```

<a name="storyboard"/>

## <a name="localizing-storyboard-uis"></a>Локализация пользовательских интерфейсов раскадровки

Образец [Tasky (локализованные раскадровки)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard) показан способ локализации текста в элементе управления, в раскадровку.

### <a name="project-structure"></a>Структура проекта

**Base.lproj** каталог содержит раскадровки, а также должен содержать никаких изображений, используемых в приложении.

Содержать другие каталоги языка **Localizable.strings** файл все строковые ресурсы, в коде, а также **MainStoryboard.strings** файл, который содержит переводы для текста в Раскадровка.

![](images/solution-storyboard.png "Дерево ресурсов")

Каталоги языка должен содержать копию все образы, локализованные, чтобы переопределить имеющемуся в **Base.lproj**.

### <a name="object-id--localization-id"></a>Идентификатор объекта или ИД локализации

При создании и изменении элементов управления в раскадровку, выделите каждый элемент управления и проверить идентификатор, используемый для локализации:

* В Visual Studio для Mac, он находится в **панель свойств** и называется **ИД локализации**.
* в Xcode, называется **идентификатор объекта**.

Это строковое значение часто имеет форму, например «NF3-h8-xmR», как показано на следующем снимке экрана.

![](images/xs-designer-localization-id.png "Представление Xcode локализации раскадровки")

Это значение используется в **.strings** файл, чтобы автоматически назначить переведенный текст каждого элемента управления.

### <a name="mainstoryboardstrings"></a>MainStoryboard.strings

Похож на формат файла перевода раскадровки **Localizable.strings** файла, за исключением того, что ключ (значение слева) не может быть задано пользователем, но вместо этого должен иметь особый формат: `ObjectID.property`.

В примере **Mainstoryboard.strings** ниже можно видеть `UITextField`имеют `placeholder` свойство text, которое можно локализовать; `UILabel`имеют `text` свойство; и `UIButton`s текст по умолчанию задается с помощью `normalTitle`:

```console
"SXg-TT-IwM.placeholder" = "nombre de la tarea";
"Pqa-aa-ury.placeholder"= "otra información de tarea";
"zwR-D9-hM1.text" = "Detalles de la tarea";
"bAM-2j-Rzw.text" = "Notas";           /* Notes */
"NF3-h8-xmR.text" = "Completo";        /* Done */
"MWt-Ya-pMf.normalTitle" = "Guardar";  /* Save */
"IGr-pR-05L.normalTitle" = "Eliminar"; /* Delete */
```

> [!IMPORTANT]
> С помощью раскадровки с классами размер может привести к переводов, которые не отображаются в приложении. [Заметки о выпуске Xcode Apple](https://developer.apple.com/library/content/releasenotes/DeveloperTools/RN-Xcode/Chapters/Introduction.html) указывают, что раскадровки или XIB будет не локализовано правильно Если три вещи: он использует классы размер базового локализации и целевой объект построения задаются в универсальные и целей сборки iOS 7.0. Исправление предназначено для дублирования свой файл раскадровки, строки в два идентичных файла: **MainStoryboard~iphone.strings** и **MainStoryboard~ipad.strings**, как показано на следующем снимке экрана:
> 
> ![](images/xs-dup-strings.png "Файлы строк")

<a name="appstore" />

## <a name="app-store-listing"></a>Листинг App Store

Соответствует Apple вопросы и ответы на [локализации приложения магазина](https://itunespartner.apple.com/en/apps/faq/App%20Store_Localization) ввести переводы для каждой страны, ваше приложение находится на продажи. Обратите внимание, их предупреждение, переводы будут отображаться только в том случае, если приложение содержит локализованный **.lproj** каталог для языка.

## <a name="summary"></a>Сводка

В этой статье описываются основы работы локализация приложений iOS с помощью функции обработки и раскадровки встроенных ресурсов.

Можно узнать больше о i18n и L10n для iOS, Android и кросс платформенных приложений (включая Xamarin.Forms) в [это руководство кросс платформенных](~/cross-platform/app-fundamentals/localization.md).

## <a name="related-links"></a>Связанные ссылки

- [Tasky (локализованное в коде) (пример)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Tasky (локализованные раскадровки) (пример)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard)
- [Руководство по локализации Apple](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html)
- [Общие сведения о локализации](~/cross-platform/app-fundamentals/localization.md)
- [Локализация Xamarin.Forms](~/xamarin-forms/app-fundamentals/localization/index.md)
- [Android локализации](~/android/app-fundamentals/localization.md)
