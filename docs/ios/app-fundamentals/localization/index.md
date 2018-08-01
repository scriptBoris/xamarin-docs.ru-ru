---
title: Локализация в Xamarin.iOS
description: В этом документе описываются возможности локализации в iOS и как использовать эти функции в приложениях Xamarin.iOS. В нем описывается языка, языкового стандарта, файлов строк, изображений при запуске и многое другое.
ms.prod: xamarin
ms.assetid: DFD9EB4A-E536-18E4-C8FD-679BA9C836D8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/28/2017
ms.openlocfilehash: 2a6096efc18f40d18ea37573e77d93796e812cc2
ms.sourcegitcommit: 4cc17681ee4164bdf2f5da52ac1f2ae99c391d1d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39387444"
---
# <a name="localization-in-xamarinios"></a>Локализация в Xamarin.iOS

_В этом документе описываются функции локализации в пакет SDK для iOS и способ доступа к ним с помощью Xamarin._

Ссылаться на [кодировки интернационализации](encodings.md) инструкции, в том числе наборов и кодовых страниц, символ в приложениях, которые нужно обработать данные не в Юникоде.

## <a name="ios-platform-features"></a>Возможности на платформе iOS

В этом разделе описываются некоторые возможности локализации в iOS. Перейдите к разделу [разделу](#basics) определенный код и примеры.

### <a name="language"></a>Язык

Пользователям выбрать язык в **параметры** приложения. Этот параметр влияет на строки языка и изображений, отображаемых в операционной системе и приложениях. 

Чтобы определить язык, используемой в приложении, получить первый элемент `NSBundle.MainBundle.PreferredLocalizations`:

```csharp
var lang = NSBundle.MainBundle.PreferredLocalizations[0];
```

Это значение будет равно код языка, такие как `en` для английского языка, `es` для испанского языка, `ja` для японского языка, и т.д. Возвращаемое значение будет ограничен одной из локализации, поддерживаемый приложением (используя резервные правила для определения оптимального совпадения).

Код приложения не всегда необходимо проверить это значение — Xamarin и iOS обоих предоставляет возможности, которые автоматически обеспечивают правильную строку или ресурсов для языка пользователя. Эти функции описаны в оставшейся части этого документа.

> [!NOTE]
> Используйте `NSLocale.PreferredLanguages` для определения настроек языка пользователя, независимо от того, локализации, поддерживаемый приложением. Значения, возвращаемые этим методом, изменения в iOS 9; см. в разделе [технические TN2418 Примечание](https://developer.apple.com/library/content/technotes/tn2418/_index.html) подробные сведения.

### <a name="locale"></a>Языковой стандарт

Пользователь нажимает кнопку языковый стандарт в **параметры** приложения. Этот параметр влияет на способ форматирования дат, времени, чисел и валюты.

Это позволяет пользователям выбрать ли они видеть форматы времени 12- или 24-часовой, является ли их десятичного разделителя запятую или точку и порядок день, месяц и год в отображения даты.

С помощью Xamarin у вас есть доступ к классам Apple iOS (`NSNumberFormatter`) а также классы .NET в System.Globalization. Разработчики должны оценить которого лучше подходит для своих потребностей, так как существуют различные функции, доступные в каждом. В частности Если извлечение и отображение цен покупки в приложении, с помощью StoreKit следует использовать классы форматирования Apple для возвращаются сведения о цене.

Текущий языковой стандарт, можно запросить с помощью любого из двух способов:

- `NSLocale.CurrentLocale.LocaleIdentifier`
- `NSLocale.AutoUpdatingCurrentLocale.LocaleIdentifier`

Первое значение могут быть кэшированы в операционной системе и поэтому может не всегда отражать текущего выбранного языка пользователя. Используйте второе значение для получения текущего выбранного языкового стандарта.

> [!NOTE]
> Mono (среда выполнения .NET основе Xamarin.iOS) и API для iOS компании Apple не поддерживают одинаковые наборы комбинаций языка или региона.
> Из-за этого, имеется возможность выбрать комбинацию языка или региона в iOS **параметры** приложения, которые не сопоставлены допустимое значение в Mono. Например установка iPhone язык на английский и его регион в Испании приведет к следующим интерфейсам API для получения различных значений:
> 
> - `CurrentThead.CurrentCulture`: en US (Mono API)
> - `CurrentThread.CurrentUICulture`: en US (Mono API)
> - `NSLocale.CurrentLocale.LocaleIdentifier`: en_ES (Apple API)
>
> Так как использует Mono `CurrentThread.CurrentUICulture` для выбора ресурсов и `CurrentThread.CurrentCulture` для форматирования дат и валюты, локализации, основанная на Mono (например, с RESX-файлами) может не обеспечивать ожидаемые результаты для этих сочетаний языка или региона. В таких ситуациях используют интерфейсы API Apple для локализации, при необходимости.

### <a name="nscurrentlocaledidchangenotification"></a>NSCurrentLocaleDidChangeNotification

Создает iOS `NSCurrentLocaleDidChangeNotification` когда пользователь обновляет их языкового стандарта. Приложения могут вести прослушивание для этого уведомления, пока они выполняются и можно внести соответствующие изменения в пользовательский интерфейс.

<a name="basics" />

## <a name="localization-basics-in-ios"></a>Основные сведения о локализации в iOS

Следующие функции iOS легко будет применить в Xamarin, чтобы предоставлять локализованные ресурсы для отображения пользователю. Ссылаться на [TaskyL10n пример](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) чтобы узнать, как реализовать эти идеи.

### <a name="specifying-default-and-supported-languages-in-infoplist"></a>Указания по умолчанию и поддерживаемые языки в файле Info.plist.

В [технические вопросы И ответы QA1828: определение языка для приложений iOS](https://developer.apple.com/library/content/qa/qa1828/_index.html), Apple описывает, как iOS выбирает язык, используемый в приложении. Следующие факторы влияют на язык, который отображается:

- Пользователь Предпочитаемые языки (в **параметры** приложения)
- Локализации, в состав приложения (.lproj папки)
- `CFBundleDevelopmentRegion` (**Info.plist** значение, указывающее язык по умолчанию для приложения)
- `CFBundleLocalizations` (**Info.plist** массиве, указывающий все поддерживаемые локализации)

Как указано в технические вопросы И ответы, `CFBundleDevelopmentRegion` представляет регион по умолчанию и язык приложения. Если приложение не поддерживает явным образом любой из предпочитаемых языков пользователя, он будет использовать язык, указанный по этому полю. 

> [!IMPORTANT]
> iOS 11 применяет этот механизм выбора языка более строго, чем предыдущие версии операционной системы. По этой причине любого приложения iOS 11, явно не объявлен поддерживаемых локализации — включая .lproj папки или задав значение для `CFBundleLocalizations` — может отображаться другой язык в iOS 11, чем это было в iOS 10.

Если `CFBundleDevelopmentRegion` не был указан в **Info.plist** файл, средства сборки Xamarin.iOS в настоящее время использовать значение по умолчанию `en_US`. Возможно, ситуация изменится в будущем, это означает, что язык по умолчанию является английский.

Чтобы убедиться, что приложение выбирает это ожидаемый язык, выполните следующие действия:

- Укажите язык по умолчанию. Откройте **Info.plist** и использовать **источника** представления, чтобы задать значение для `CFBundleDevelopmentRegion` ключ; в формате XML, он должен выглядеть следующим образом:

```xml
<key>CFBundleDevelopmentRegion</key>
<string>es</string>
```

В этом примере использует «es», чтобы указать, что если ни один из пользователей предпочитаемый языков, по умолчанию на испанский язык.

- Объявите все поддерживаемые локализации. В **Info.plist**, использовать **источника** представления, чтобы задать массив для `CFBundleLocalizations` ключ; в формате XML, он должен выглядеть следующим образом:

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>es</string>
    ...
</array>
```

Приложений Xamarin.iOS, локализованные с помощью механизмов .NET, такие как RESX-файлах необходимо предоставить эти **Info.plist** также значения.

Дополнительные сведения об этих **Info.plist** ключи, взгляните на Apple [справочника по ключам сведения свойства списка](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html).

### <a name="getlocalizedstring-method"></a>Метод GetLocalizedString

`NSBundle.MainBundle.GetLocalizedString` Метод выполняет поиск локализованного текста, который будет сохранен в **.strings** файлы в проекте. Эти файлы организованы по языку, в специальную каталогов с помощью **.lproj** суффикс.

#### <a name="strings-file-locations"></a>расположение файлов .strings

- **Base.lproj** — это каталог, содержащий ресурсы для языка по умолчанию.
  Часто он расположен в корневом каталоге проекта (но может также размещаться в **ресурсы** папку).
- **<language>.lproj** создаются каталоги для каждого поддерживаемого языка, обычно в **ресурсы** папки.

Может существовать несколько разных **.strings** файлов в каждом каталоге языка:

- **Localizable.strings** — в основной список локализованный текст.
- **InfoPlist.strings** — некоторые определенные ключи разрешены в этот файл для преобразования вещи, такие как имя приложения.
- **< имя раскадровки > .strings** — необязательный файл, который содержит переводы для элементов пользовательского интерфейса в раскадровке.

**Действие при построении** для этих файлов должна быть **пакета ресурсов**.

#### <a name="strings-file-format"></a>формат файла .strings

Используется следующий синтаксис для локализованных строковых значений:

```console
/* comment */
"key"="localized-value";
```

Следует экранировать следующие символы в строках:

* `\"`  Квота
* `\\`  обратная косая черта
* `\n`  символ новой строки

Ниже приведен пример **es/Localizable.strings** (т. е. Испанский) файл из образца:

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

Для локализации изображение в iOS:

1. См. изображение в коде, например:

  ```csharp
  UIImage.FromBundle("flag");
  ```

2. Поместите файл изображения по умолчанию **flag.png** в **Base.lproj** (языкового каталога собственной разработки).

3. При необходимости поместите локализованные версии изображения в **.lproj** папки для каждого языка (например) **ES.lproj**, **ja.lproj**). Используйте это имя **flag.png** в каждом каталоге языка.

Если изображение отсутствует для определенного языка, iOS будет переключиться на родном языке папку по умолчанию и загрузить изображение из него.


#### <a name="launch-images"></a>Изображения при запуске

Использовать стандартным соглашениям об именовании для изображений при запуске (XIB и раскадровки для моделей iPhone 6) при помещении их в **.lproj** каталоги для каждого языка.

```console
Default.png
Default@2x.png
Default-568h@2x.png
LaunchScreen.xib
```

### <a name="app-name"></a>Имя приложения

Размещение **InfoPlist.strings** файл **.lproj** каталог позволяет переопределить некоторые значения в приложении **Info.plist**, включая имя приложения:

```console
"CFBundleDisplayName" = "LeónTodo";
```

Другие ключи, которые можно использовать для [локализовать строки для конкретного приложения](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW21) являются:

- CFBundleName
- CFBundleShortVersionString
- NSHumanReadableCopyright

### <a name="dates-and-times"></a>Значения даты и времени

Несмотря на то, что можно использовать встроенные функции даты и времени .NET (вместе с текущим `CultureInfo`) для форматирования дат и времени для языкового стандарта, это приводит к пропуску зависящих от языкового стандарта пользовательских параметров (которые можно задать отдельно от языка).

Используйте iOS `NSDateFormatter` для получения выходных данных, соответствующего выбранному языкового стандарта пользователя. В следующем примере кода показаны основные дату и время, в параметры форматирования:

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

Ссылаться на Apple [модули форматирования даты](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/DataFormatting/Articles/dfDateFormatting10_4.html) Дополнительные сведения см. При тестировании, зависящих от национальной настройки даты и времени, форматирование, выберите оба варианта **iPhone языка** и **регион** параметры.

<a name="rtl" />

### <a name="right-to-left-rtl-layout"></a>Макет справа налево (RTL)

iOS предоставляет ряд возможностей для помощи в создании приложений, использующих справа НАЛЕВО:

* Используйте автоматический макет `leading` и `trailing` атрибуты для выравнивание элемента управления (который соответствует влево и вправо для английского языка, но обращено для языков RTL).
  [ `UIStackView` ](~/ios/user-interface/controls/uistackview.md) Управления особенно полезна для размещения элементов управления, которые следует учитывать справа НАЛЕВО.
* Используйте `TextAlignment = UITextAlignment.Natural` для выравнивания текста (которая остается для большинства языков, но для справа НАЛЕВО).
* `UINavigationController` автоматически зеркально отражает "Назад" и меняет направление прокрутки.

Ниже показаны снимки экрана [локализованные Tasky пример](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) в арабском и иврите (несмотря на то, что в полях был введен на английском языке):

[![](images/rtl-ar-sml.png "Локализация на арабском языке")](images/rtl-ar.png#lightbox "Arabic") 

[![](images/rtl-he-sml.png "Локализация на иврит")](images/rtl-he.png#lightbox "Hebrew")

iOS автоматически отменяет `UINavigationController`, и другие элементы управления размещаются внутри `UIStackView` или с автоматическим макетом.
Текст справа НАЛЕВО локализовать с помощью **.strings** файлы в так же, как текст слева Направо.

<a name="code"/>

## <a name="localizing-the-ui-in-code"></a>Локализация пользовательского интерфейса в коде

[Tasky (локализованные в коде)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) примере показано, как для локализации приложения, где создается пользовательского интерфейса в код (а не файлы Xib или раскадровки).

### <a name="project-structure"></a>Структура проекта

![](images/solution-code.png "Дерева ресурсов")

### <a name="localizablestrings-file"></a>Файл Localizable.strings

Как описано выше, **Localizable.strings** формат файла состоит из пары "ключ значение". Раздел описывает назначение строки, а значение — переведенный текст, используемый в приложении.

Испанский (**es**) локализации для примера, показаны ниже:

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

В коде приложения, где бы ни пользовательский интерфейс отображаемый текст задается (будь то текст метки, или заполнитель входных данных и т. д) в коде используется iOS `GetLocalizedString` функции для получения правильное преобразование для отображения:

```csharp
var localizedString = NSBundle.MainBundle.GetLocalizedString ("key", "optional");
someControl.Text = localizedString;
```

<a name="storyboard"/>

## <a name="localizing-storyboard-uis"></a>Локализация пользовательских интерфейсов раскадровки

Образец [Tasky (локализованные раскадровки)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard) показано, как локализовать текст на элементы управления в раскадровке.

### <a name="project-structure"></a>Структура проекта

**Base.lproj** каталог содержит раскадровку, а также должен содержать все образы, используемые в приложении.

Содержать другие каталоги языка **Localizable.strings** файл все строковые ресурсы, на которые ссылается код, а также **MainStoryboard.strings** файла, содержащего переводы для текста в раскадровки.

![](images/solution-storyboard.png "Дерева ресурсов")

Каталоги язык должен содержать копию все образы, которые локализованы, чтобы переопределить имеющемуся в **Base.lproj**.

### <a name="object-id--localization-id"></a>Идентификатор объекта или ИД локализации

При создании и изменении элементов управления в раскадровке, выделите каждый элемент управления и проверьте их использовать для локализации:

* В Visual Studio для Mac, он расположен в **панели свойств** и вызывается **ИД локализации**.
* В Xcode, он называется **идентификатор объекта**.

Это строковое значение часто имеет форму, например «NF3-h8-xmR», как показано на следующем снимке экрана:

![](images/xs-designer-localization-id.png "Представление Xcode локализации раскадровки")

Это значение используется в **.strings** файл, чтобы автоматически назначить переведенный текст каждого элемента управления.

### <a name="mainstoryboardstrings"></a>MainStoryboard.strings

Формат файла перевода раскадровки аналогичен **Localizable.strings** файлов, за исключением того, что ключ (значение слева) не может быть определена пользователем, но вместо этого должен иметь особый формат: `ObjectID.property`.

В примере **Mainstoryboard.strings** ниже представлены `UITextField`имеют `placeholder` свойство text, которое можно локализовать; `UILabel`имеют `text` свойство; и `UIButton`s текст по умолчанию задается с помощью `normalTitle`:

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
> С помощью раскадровки с помощью классов размера может привести переводов, которые не отображаются в приложении. [Заметки о выпуске Apple Xcode](https://developer.apple.com/library/content/releasenotes/DeveloperTools/RN-Xcode/Chapters/Introduction.html) указывают, что storyboard "или" XIB будет не локализовано правильно при три вещи: он использует классы размера, локализации базового и целевого объекта сборки устанавливаются в универсальные и целей сборки iOS 7.0. Исправление заключается в повторяющийся свой файл раскадровки, строки в два идентичных файла: **MainStoryboard~iphone.strings** и **MainStoryboard~ipad.strings**, как показано на следующем снимке экрана:
> 
> ![](images/xs-dup-strings.png "Файлы строк")

<a name="appstore" />

## <a name="app-store-listing"></a>Листинг App Store

Соответствует Apple вопросы и ответы для [локализация приложения Store](https://itunespartner.apple.com/en/apps/faq/App%20Store_Localization) ввести переводы для каждой страны, ваше приложение находится на продажи. Обратите внимание, их предупреждение, переводы будет отображаться только в том случае, если приложение содержит локализованный **.lproj** каталог для языка.

## <a name="summary"></a>Сводка

В этой статье рассматриваются основные сведения о локализации приложений iOS с помощью функции встроенных ресурсов обработки и раскадровки.

Дополнительные сведения о i18n и L10n для iOS, Android и кросс платформенных приложений (включая Xamarin.Forms) в [в этом руководстве кросс платформенные](~/cross-platform/app-fundamentals/localization.md).

## <a name="related-links"></a>Связанные ссылки

- [Tasky (локализованные в коде) (пример)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Tasky (локализованные раскадровки) (пример)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard)
- [Руководство по локализации для Apple](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html)
- [Общие сведения о локализации](~/cross-platform/app-fundamentals/localization.md)
- [Локализация Xamarin.Forms](~/xamarin-forms/app-fundamentals/localization/index.md)
- [Android локализации](~/android/app-fundamentals/localization.md)
