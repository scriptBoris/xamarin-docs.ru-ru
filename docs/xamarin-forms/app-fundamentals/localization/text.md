---
title: Строки и локализации изображения
description: Xamarin.Forms приложений можно локализовать с помощью файлов ресурсов .NET.
ms.prod: xamarin
ms.assetid: 852B4ED3-2D2D-48A5-A759-A6591F6A1509
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/06/2016
ms.openlocfilehash: 47ea437be8a1570517f37cc59aab17431c5af7f0
ms.sourcegitcommit: c2d1249cb67b877ee0d9cb8d095ec66fd51d8c31
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36291329"
---
# <a name="localization"></a>Локализация

_Xamarin.Forms приложений можно локализовать с помощью файлов ресурсов .NET._

## <a name="overview"></a>Обзор

Встроенный механизм для локализации приложения использует .NET [RESX-файлы](http://msdn.microsoft.com/library/ekyft91f(v=vs.90).aspx) и классы в `System.Resources` и `System.Globalization` пространства имен. RESX-файлы, содержащие переведенных строк внедряются в сборку Xamarin.Forms, а также класс, созданный компилятором, который предоставляет строго типизированный доступ к переводы. Переведенный текст может быть извлечен в коде.

### <a name="sample-code"></a>Пример кода

Существует два образца, связанный с данным документом.

* [UsingResxLocalization](https://github.com/xamarin/xamarin-forms-samples/tree/master/UsingResxLocalization) — это очень простой Демонстрация объясняются основные понятия. В этом примере приведены фрагменты кода, показано ниже.
* [TodoLocalized](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized) представляет собой базовый рабочее приложение, которое использует эти методы локализации.

#### <a name="shared-projects-are-not-recommended"></a>Общие проекты не рекомендуются.

Пример TodoLocalized включает [Демонстрация общий проект](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized/SharedProject/) Однако из-за ограничений системы построения файлы ресурсов не получить **. designer.cs** созданный файл, который запрещает доступ к переведенные строки строгой типизацией в коде.

В оставшейся части этого документа относится к проектов с помощью Xamarin.Forms .NET стандартной библиотеки шаблонов.

## <a name="globalizing-xamarinforms-code"></a>Глобализация Xamarin.Forms кода

**Глобализация** приложения — это процесс использования «общедоступными.» Это значит, написания кода, который может отображать различные языки.

Одним из ключевых элементов глобализации приложения Создание пользовательского интерфейса таким образом, что не *жестко* текста. Вместо этого все, отображаемых для пользователя должны быть получены из набора строк, которые была переведена на своем языке.

В этом документе мы изучим, как использовать RESX-файлы для хранения этих строк и получить их для отображения в зависимости от предпочтений пользователя.

Образцы целевых языков английский, французский, испанский, немецкий, китайский, японский, русский и португальский (Бразилия). Приложения могут быть переведены на как можно меньшее число или число языков, при необходимости.

> [!NOTE]
> На универсальной платформе Windows RESW-файлов можно использовать для локализации уведомлений push, а не в RESX-файлы. Дополнительные сведения см. в разделе [UWP локализации](/windows/uwp/design/globalizing/globalizing-portal/).

### <a name="adding-resources"></a>Добавление ресурсов

Первым шагом в Глобализация Xamarin.Forms .NET Standard Библиотечное приложение добавляет файлы ресурсов RESX, которые будут использоваться для хранения текста, используемого в приложении. Необходимо добавить файл RESX, содержит текст по умолчанию, а затем добавьте дополнительные RESX-файлы для каждого языка, который требуется поддерживать.

#### <a name="base-language-resource"></a>Базовый языковой ресурс

В файле базовых ресурсов (RESX) будет содержать строки языка по умолчанию (образцы предполагается, что используется английский язык по умолчанию). Добавьте файл в проект кода, общие Xamarin.Forms, щелкнув правой кнопкой мыши проект и выбрав **Добавить > новый файл...** .

Выберите понятное имя, например **AppResources** и нажмите клавишу **ОК**.

[![Добавьте файл ресурсов](text-images/resx-new-file-sml.png "новое диалоговое окно файла")](text-images/resx-new-file.png#lightbox "диалоговое окно создания файла")

В проект будут добавлены два файла:

* **AppResources.resx** файла, где переводимые строки хранятся в формате XML.
* **AppResources.designer.cs** файл, который объявляет разделяемый класс, чтобы содержать ссылки на все элементы, созданные в XML RESX-файла.

Дерево решений будет показано, как связанные файлы. RESX-файла *следует* изменить, чтобы добавить новые строки переводимые; **. designer.cs** файла следует *не* редактироваться.

![](text-images/appresources-tree.png "Файл AppResources.resx")

##### <a name="string-visibility"></a>Видимость строки

По умолчанию при создании строго типизированные ссылки на строки, они будут `internal` на сборку. Это так, как средство построения по умолчанию для RESX-файлы приводит к возникновению ошибки **. designer.cs** файл с `internal` свойства.

Выберите **AppResources.resx** файла и отобразить **свойства** прокладки, чтобы узнать, где этот инструмент сборки настройки. На снимке экрана ниже показано **пользовательский инструмент: ResXFileCodeGenerator**.


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](text-images/vs-resx-internal-sml.png "Окно свойств для AppResources.Resx")](text-images/vs-resx-internal.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![](text-images/xs-resx-internal-sml.png "Панель свойств для AppResources.Resx")](text-images/xs-resx-internal.png#lightbox)

-----

Чтобы сделать свойства строго типизированных строки `public`, необходимо вручную изменить конфигурацию **пользовательский инструмент: PublicResXFileCodeGenerator**, как показано на снимке экрана ниже:


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](text-images/vs-resx-public-sml.png "Окно свойств для AppResources.Resx")](text-images/vs-resx-public.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![](text-images/xs-resx-internal-sml.png "Панель свойств для AppResources.Resx")](text-images/xs-resx-internal.png#lightbox)


[![](text-images/xs-resx-public-sml.png "Панель свойств для AppResources.Resx")](text-images/xs-resx-public.png#lightbox)

-----

Это изменение является необязательным и используется только требуется, если требуется ссылаться локализованные строки в разных сборках (например, если поместить RESX-файлы в другой сборке в код). В примере для этого раздела оставляет строки `internal` так, как они определены в той же Xamarin.Forms .NET Standard библиотеки сборки, где они используются.

Необходимо задать пользовательский инструмент на базовый RESX-файла, как показано выше; не нужно задавать *любое* средство построения на RESX-файлы конкретного языка, описанные в следующих разделах.

##### <a name="editing-the-resx-file"></a>Редактирование RESX-файла

К сожалению, тут нет встроенного редактора RESX в Visual Studio для Mac. Добавление новых строк переводимые требуется добавить новый XML-код `data` элемент для каждой строки. Каждый `data` элемент может содержать следующее:

* `name` атрибут (обязательно) — это ключ для данной переводимые строки. Он должен быть допустимое имя свойства C# -, поэтому допускаются пробелы или специальные символы.
* `value` элемент (обязательно), что является фактическим строковым, которое отображается в приложении.
* `comment` (необязательно) элемент может содержать инструкции транслятора, объясняется, как использовать эту строку.
* `xml:space` атрибут (необязательно) для управления сохраняется как интервал в строке.

Некоторые примеры `data` элементов приведены здесь:

```xml
<data name="NotesLabel" xml:space="preserve">
    <value>Notes:</value>
    <comment>label for input field</comment>
</data>
<data name="NotesPlaceholder" xml:space="preserve">
    <value>eg. buy milk</value>
    <comment>example input for notes field</comment>
</data>
<data name="AddButton" xml:space="preserve">
    <value>Add new item</value>
</data>
```

Как приложение создано, каждый фрагмент текста, отображаемого для пользователя должны быть добавлены в базовый файл ресурсов RESX в новом `data` элемента. Рекомендуется включать `comment`s, насколько возможно, для обеспечения высокого качества перевода.

> [!NOTE]
> Visual Studio (включая бесплатный выпуск Community edition) содержит базового редактора RESX. При наличии доступа к компьютеру Windows, который можно легко добавлять и изменять строки в RESX-файлы.

#### <a name="language-specific-resources"></a>Ресурсы для конкретного языка

Как правило собственно перевод текстовые строки по умолчанию не будет происходить до больших объемов приложения были записаны (в этом случае RESX-файла по умолчанию будет содержать большое количество строк). По-прежнему рекомендуется добавить языковых ресурсов еще на стадии разработки, при необходимости заполнение текстом машинного перевода для тестирования кода локализации.

Для каждого языка, который требуется поддерживать добавляется один дополнительный файл RESX.
Файлы ресурсов для определенных языков должны соответствовать определенное соглашение об именах: используйте такое же имя файла, как ресурсы базовый файл (например) **AppResources**) следуют точка (.), а затем код языка. Простые примеры включают следующее.

* **AppResources.fr.resx** -французский язык переводы.
* **AppResources.es.resx** -переводов на испанском языке.
* **AppResources.de.resx** -переводы для немецкого языка.
* **AppResources.ja.resx** -переводы для японского языка.
* **AppResources.zh Hans.resx** — китайский (упрощенное письмо) языковых переводов.
* **AppResources.zh Hant.resx** — китайский (традиционный) языковых переводах.
* **AppResources.pt.resx** -переводы португальского языка.
* **AppResources.pt BR.resx** — Бразильский Португальский язык переводы.

Общий шаблон является использование кодов языков двух букв, но существуют некоторые примеры (например, китайский), в которых используется другой формат, а другие примеры (например, португальский (Бразилия)) которых требуется четырехзначный код языка.

Эти файлы языковых ресурсов *не* требуют **. designer.cs** частичного класса, поэтому они могут быть добавлены как обычные файлы XML с **действие при построении: EmbeddedResource**значение. На этом снимке экрана показано решение, содержащее файлы ресурсов для конкретного языка:

![](text-images/appresources-langs.png "Файлы ресурсов для конкретного языка")

Приложение разработано и базовый RESX-файла был добавлен текст, вы должны отправить ее переводчикам, преобразует каждый `data` элемент и возврат файла ресурсов для определенных языков (с помощью показано соглашение об именовании) должны быть включены в приложение. Ниже приведены некоторые примеры машинным переводом:

**AppResources.es.resx (испанский)**

```xml
<data name="AddButton" xml:space="preserve">
    <value>Escribir un artículo</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

**AppResources.ja.resx (японский)**

```xml
<data name="AddButton" xml:space="preserve">
    <value>新しい項目を追加</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

**AppResources.pt-BR.resx (португальский (Бразилия))**

```xml
<data name="AddButton" xml:space="preserve">
    <value>adicionar novo item</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

Только `value` элемент должен быть обновлен транслятором - `comment` не является преобразуемым. Следует помнить: при редактировании XML-файлы для экранирования зарезервированные знаки, такие как `<`, `>`, `&` с `&lt;`, `&gt;`, и `&amp;` , если они введены в `value` или `comment`.

<a name="incode" />

### <a name="using-resources-in-code"></a>Использование ресурсов в коде

Строки в файлах ресурсов RESX будут доступны для использования в коде интерфейс пользователя с помощью `AppResources` класса. `name` Назначена каждая строка в RESX файл становится свойством для этого класса, который можно ссылаться в коде Xamarin.Forms, как показано ниже:

```csharp
var myLabel = new Label ();
var myEntry = new Entry ();
var myButton = new Button ();
// populate UI with translated text values from resources
myLabel.Text = AppResources.NotesLabel;
myEntry.Placeholder = AppResources.NotesPlaceholder;
myButton.Text = AppResources.AddButton;
```

Пользовательский интерфейс на iOS, Android и отображаемое универсальной платформы Windows (UWP), как можно ожидать, только теперь имеется возможность перевода приложения на несколько языков, поскольку текст загружается из ресурса, а не жестко. Ниже приведен снимок экрана пользовательского интерфейса на каждой платформе до перевода.

![](text-images/simple-example-english.png "Пользовательские интерфейсы кросс платформенных до преобразования")

### <a name="troubleshooting"></a>Устранение неполадок

#### <a name="testing-a-specific-language"></a>Тестирование конкретного языка

Он может быть непростой задачей для переключения симулятор или устройство для различных языков, particulary во время разработки, когда требуется быстро протестировать языков и региональных параметров.

Можно принудительно конкретного языка, должна быть загружена, задав `Culture` как показано в этом фрагменте кода:

```csharp
// force a specific culture, useful for quick testing
AppResources.Culture =  new CultureInfo("fr-FR");
```

Этот подход — задать культуру непосредственно на `AppResources` класса — также можно использовать для реализации языка — selector внутри приложения (а не с помощью языкового стандарта устройства).

#### <a name="loading-embedded-resources"></a>Загрузка внедренные ресурсы

В следующем фрагменте кода удобно для отладки проблем с внедренные ресурсы (например, RESX-файлы). Добавьте следующий код в приложение (ранних этапах жизненного цикла приложений), и он будет вывод всех ресурсов, внедренных в сборку, показывающая полный идентификатор ресурсов:

```csharp
using System.Reflection;
// ...
// NOTE: use for debugging, not in released app code!
var assembly = typeof(EmbeddedImages).GetTypeInfo().Assembly; // "EmbeddedImages" should be a class in your app
foreach (var res in assembly.GetManifestResourceNames())
{
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

В **AppResources.Designer.cs** файла (вокруг *строки 33*), полный *имя диспетчера ресурсов* указано (например) `"UsingResxLocalization.Resx.AppResources"`) примерно в следующем примере кода:

```csharp
System.Resources.ResourceManager temp =
        new System.Resources.ResourceManager(
                "UsingResxLocalization.Resx.AppResources",
                typeof(AppResources).GetTypeInfo().Assembly);
```

Проверьте **выходных данных приложения** для результатов отладки кода, приведенном выше, чтобы подтвердить соответствующие ресурсы перечислены (т. е. `"UsingResxLocalization.Resx.AppResources"`).

Если нет, `AppResources` класса не сможет загрузить его ресурсы.
Установите этот флажок, чтобы устранить проблемы, когда не удается найти ресурсы:

* Пространство имен по умолчанию для проекта совпадает с корневым пространством имен в **AppResources.Designer.cs** файла.
* Если **AppResources.resx** файл расположен в подкаталоге, имя подкаталога должно быть частью пространства имен *и* часть идентификатора ресурса.
* **AppResources.resx** файл имеет **действие при построении: EmbeddedResource**.
* **Параметры проекта > исходного кода > .NET Naming Policies > имена ресурсов используйте Visual Studio в стиле** установлен. Это может untick, если вы предпочитаете, но пространства имен, используемые при обращении к ресурсам RESX нужно будет обновлено в пределах всей приложения.

#### <a name="doesnt-work-in-debug-mode-android-only"></a>Не работает в режиме отладки (только Android)

Если переведенные строки работаете в выпуске Android сборки, но не во время отладки, щелкните правой кнопкой мыши **проекта Android** и выберите **параметры > сборки > Android построения** и убедитесь, что **Быстрое развертывание сборок** не установлен. Этот параметр вызывает проблемы с загрузкой ресурсы и не следует использовать при тестировании локализованных приложений.

### <a name="displaying-the-correct-language"></a>Отображение на правильном языке

Пока мы рассмотрели способы написания кода, который можно предоставить переводы, но не на самом деле, чтобы они отображались. Xamarin.Forms кода можно воспользоваться преимуществами. NET ресурсы для загрузки переводы нужный язык, но необходимо запросить операционную систему на каждой платформе, чтобы определить, какой язык, выбранном пользователем.

Так как некоторые код платформы необходим для получения предпочитаемый язык пользователя, используйте [зависимостей службы](~/xamarin-forms/app-fundamentals/dependency-service/index.md) предоставлять эту информацию в приложении Xamarin.Forms и его реализации для каждой платформы.

Во-первых Определите интерфейс для предоставления пользовательского языка и региональных параметров, примерно в следующем примере кода:

```csharp
public interface ILocalize
{
    CultureInfo GetCurrentCultureInfo ();
    void SetLocale (CultureInfo ci);
}
```

Во-вторых, используйте [помощью DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) в Xamarin.Forms `App` класса для вызова интерфейса и задать верное значение в нашей культуры ресурсов RESX. Обратите внимание, что не нужно вручную задать это значение для универсальной платформы Windows, так как платформа ресурсы автоматически распознает выбранного языка на этих платформах.

```csharp
if (Device.RuntimePlatform == Device.iOS || Device.RuntimePlatform == Device.Android)
{
    var ci = DependencyService.Get<ILocalize>().GetCurrentCultureInfo();
    Resx.AppResources.Culture = ci; // set the RESX for resource localization
    DependencyService.Get<ILocalize>().SetLocale(ci); // set the Thread for locale-aware methods
}
```

Ресурс `Culture` должно быть задано при первой загрузке приложения, чтобы использовались строки нужный язык. При необходимости может обновить это значение в соответствии с платформой события, которые могут возникать на iOS или Android, если пользователь обновляет их языковые параметры во время выполнения приложения.

Реализации `ILocalize` интерфейса отображаются в [код платформы](#Platform-specific_Code) разделе ниже. Эти реализации используют это преимущество `PlatformCulture` вспомогательный класс:

```csharp
public class PlatformCulture
{
    public PlatformCulture (string platformCultureString)
    {
        if (String.IsNullOrEmpty(platformCultureString))
        {
            throw new ArgumentException("Expected culture identifier", "platformCultureString"); // in C# 6 use nameof(platformCultureString)
        }
        PlatformString = platformCultureString.Replace("_", "-"); // .NET expects dash, not underscore
        var dashIndex = PlatformString.IndexOf("-", StringComparison.Ordinal);
        if (dashIndex > 0)
        {
            var parts = PlatformString.Split('-');
            LanguageCode = parts[0];
            LocaleCode = parts[1];
        }
        else
        {
            LanguageCode = PlatformString;
            LocaleCode = "";
        }
    }
    public string PlatformString { get; private set; }
    public string LanguageCode { get; private set; }
    public string LocaleCode { get; private set; }
    public override string ToString()
    {
        return PlatformString;
    }
}
```

<a name="Platform-specific_Code" />

### <a name="platform-specific-code"></a>Код платформы

Код, чтобы определить, какой язык для отображения должны специфический для платформы, так как iOS, Android и UWP предоставлять эту информацию различными способами. Код для `ILocalize` зависимостей службы предоставляется ниже для каждой платформы, а также дополнительные требования к конкретной платформы, чтобы убедиться локализованный текст отображается правильно.

Код платформы также требуется обрабатывать случаи, где операционная система позволяет пользователю настроить код языка, который не поддерживается. В NET `CultureInfo` класса. В таких случаях необходимо написать пользовательский код обнаружения неподдерживаемый язык и региональные стандарты и подставить наилучшие результаты. NET-совместимом языковой стандарт.

#### <a name="ios-application-project"></a>Проект приложения iOS

iOS пользователям выбирать язык отдельно от языка и региональных параметров форматирования даты и времени. Загрузка правильный ресурсов для локализации приложения Xamarin.Forms, нам нужно просто запросить `NSLocale.PreferredLanguages` массива для первого элемента.

Следующая реализация `ILocalize` зависимостей службы должны размещаться в проекте приложения iOS. Поскольку операций ввода-вывода использует символы подчеркивания, а не тире (это стандартное представление .NET) код заменяет символ подчеркивания перед созданием экземпляра `CultureInfo` класса:

```csharp
[assembly:Dependency(typeof(UsingResxLocalization.iOS.Localize))]

namespace UsingResxLocalization.iOS
{
public class Localize : UsingResxLocalization.ILocalize
    {
        public void SetLocale (CultureInfo ci)
        {
            Thread.CurrentThread.CurrentCulture = ci;
            Thread.CurrentThread.CurrentUICulture = ci;
        }
        public CultureInfo GetCurrentCultureInfo ()
        {
            var netLanguage = "en";
            if (NSLocale.PreferredLanguages.Length > 0)
            {
                var pref = NSLocale.PreferredLanguages [0];
                netLanguage = iOSToDotnetLanguage(pref);
            }
            // this gets called a lot - try/catch can be expensive so consider caching or something
            System.Globalization.CultureInfo ci = null;
            try
            {
                ci = new System.Globalization.CultureInfo(netLanguage);
            }
            catch (CultureNotFoundException e1)
            {
                // iOS locale not valid .NET culture (eg. "en-ES" : English in Spain)
                // fallback to first characters, in this case "en"
                try
                {
                    var fallback = ToDotnetFallbackLanguage(new PlatformCulture(netLanguage));
                    ci = new System.Globalization.CultureInfo(fallback);
                }
                catch (CultureNotFoundException e2)
                {
                    // iOS language not valid .NET culture, falling back to English
                    ci = new System.Globalization.CultureInfo("en");
                }
            }
            return ci;
        }
        string iOSToDotnetLanguage(string iOSLanguage)
        {
            var netLanguage = iOSLanguage;
            //certain languages need to be converted to CultureInfo equivalent
            switch (iOSLanguage)
            {
                case "ms-MY":   // "Malaysian (Malaysia)" not supported .NET culture
                case "ms-SG":    // "Malaysian (Singapore)" not supported .NET culture
                    netLanguage = "ms"; // closest supported
                    break;
                case "gsw-CH":  // "Schwiizertüütsch (Swiss German)" not supported .NET culture
                    netLanguage = "de-CH"; // closest supported
                    break;
                // add more application-specific cases here (if required)
                // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }
        string ToDotnetFallbackLanguage (PlatformCulture platCulture)
        {
            var netLanguage = platCulture.LanguageCode; // use the first part of the identifier (two chars, usually);
            switch (platCulture.LanguageCode)
            {
                case "pt":
                    netLanguage = "pt-PT"; // fallback to Portuguese (Portugal)
                    break;
                case "gsw":
                    netLanguage = "de-CH"; // equivalent to German (Switzerland) for this app
                    break;
                // add more application-specific cases here (if required)
                // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }
    }
}
```

> [!NOTE]
> `try/catch` Блоков в `GetCurrentCultureInfo` метод имитируют поведение обычно используется с описателями языковой стандарт — Если точное соответствие не найден, поиск для близкое соответствие на основе в зависимости от языка (первый блок символов в языковом стандарте).
>
> В случае с Xamarin.Forms, некоторые языки являются допустимыми в iOS, но не соответствуют допустимому `CultureInfo` .NET и приведенный выше попыток для обработки этого.
>
> Например, iOS **параметры > Общие языка &amp; области** экрана позволяет установить ваш телефон **языка** для **английского** , но  **Область** для **Испания**, что приводит в строке языкового стандарта `"en-ES"`. Когда `CultureInfo` Создание завершается с ошибкой, код возвращается к использованию только две первые буквы выбрать язык интерфейса.
>
> Разработчики должны изменить `iOSToDotnetLanguage` и `ToDotnetFallbackLanguage` методов для обработки определенных вариантов, необходимых для их поддерживаемых языков.


Некоторые элементы системные пользовательского интерфейса, автоматически преобразуются службами операций ввода-вывода, таких как **сделать** кнопку `Picker` элемента управления. Чтобы принудительно iOS, чтобы преобразовать эти элементы, нам нужно указать, какие языки поддерживаются в **Info.plist** файла. Можно добавить эти значения через **Info.plist > источник** как показано ниже:

![Локализация ключи в файле Info.plist](text-images/info-plist.png "локализации ключи в файле Info.plist.")

Кроме того, можно открыть **Info.plist** файл в редакторе XML и напрямую изменять значения:

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>de</string>
    <string>es</string>
    <string>fr</string>
    <string>ja</string>
    <string>pt</string> <!-- Brazil -->
    <string>pt-PT</string> <!-- Portugal -->
    <string>ru</string>
    <string>zh-Hans</string>
    <string>zh-Hant</string>
</array>
<key>CFBundleDevelopmentRegion</key>
<string>en</string>
```

После реализации службы зависимостей и обновить **Info.plist**, приложение iOS будет показывать локализованный текст.

> [!NOTE]
> Обратите внимание, что будет обрабатываться Apple португальский немного иначе, чем ожидалось.
> Из [свои документы](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW2): _«использовать pt как идентификатор языка для португальского языка как он используется в Бразилии и pt-PT как идентификатор языка для португальского языка при использовании в Португалия»_.
> Это означает, что когда португальский язык выбирается стандартным языкового стандарта базовый язык будут португальский (Бразилия) на iOS, если код для изменения этого поведения (например, `ToDotnetFallbackLanguage` выше).

Дополнительные сведения о локализации iOS см. в разделе [iOS локализации](~/ios/app-fundamentals/localization/index.md).

#### <a name="android-application-project"></a>Проект приложения Android

Android предоставляет выбранного языка через `Java.Util.Locale.Default`и использует разделитель подчеркивания вместо тире (который заменяется в следующем примере кода). Добавьте в проект приложения Android Эта реализация службы зависимостей:

```csharp
[assembly:Dependency(typeof(UsingResxLocalization.Android.Localize))]

namespace UsingResxLocalization.Android
{
    public class Localize : UsingResxLocalization.ILocalize
    {
        public void SetLocale(CultureInfo ci)
        {
            Thread.CurrentThread.CurrentCulture = ci;
            Thread.CurrentThread.CurrentUICulture = ci;
        }
        public CultureInfo GetCurrentCultureInfo()
        {
            var netLanguage = "en";
            var androidLocale = Java.Util.Locale.Default;
            netLanguage = AndroidToDotnetLanguage(androidLocale.ToString().Replace("_", "-"));
            // this gets called a lot - try/catch can be expensive so consider caching or something
            System.Globalization.CultureInfo ci = null;
            try
            {
                ci = new System.Globalization.CultureInfo(netLanguage);
            }
            catch (CultureNotFoundException e1)
            {
                // iOS locale not valid .NET culture (eg. "en-ES" : English in Spain)
                // fallback to first characters, in this case "en"
                try
                {
                    var fallback = ToDotnetFallbackLanguage(new PlatformCulture(netLanguage));
                    ci = new System.Globalization.CultureInfo(fallback);
                }
                catch (CultureNotFoundException e2)
                {
                    // iOS language not valid .NET culture, falling back to English
                    ci = new System.Globalization.CultureInfo("en");
                }
            }
            return ci;
        }
        string AndroidToDotnetLanguage(string androidLanguage)
        {
            var netLanguage = androidLanguage;
            //certain languages need to be converted to CultureInfo equivalent
            switch (androidLanguage)
            {
                case "ms-BN":   // "Malaysian (Brunei)" not supported .NET culture
                case "ms-MY":   // "Malaysian (Malaysia)" not supported .NET culture
                case "ms-SG":   // "Malaysian (Singapore)" not supported .NET culture
                    netLanguage = "ms"; // closest supported
                    break;
                case "in-ID":  // "Indonesian (Indonesia)" has different code in  .NET
                    netLanguage = "id-ID"; // correct code for .NET
                    break;
                case "gsw-CH":  // "Schwiizertüütsch (Swiss German)" not supported .NET culture
                    netLanguage = "de-CH"; // closest supported
                    break;
                    // add more application-specific cases here (if required)
                    // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }
        string ToDotnetFallbackLanguage(PlatformCulture platCulture)
        {
            var netLanguage = platCulture.LanguageCode; // use the first part of the identifier (two chars, usually);
            switch (platCulture.LanguageCode)
            {
                case "gsw":
                    netLanguage = "de-CH"; // equivalent to German (Switzerland) for this app
                    break;
                    // add more application-specific cases here (if required)
                    // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }
    }
}
```

> [!NOTE]
> `try/catch` Блоков в `GetCurrentCultureInfo` метод имитируют поведение обычно используется с описателями языковой стандарт — Если точное соответствие не найден, поиск для близкое соответствие на основе в зависимости от языка (первый блок символов в языковом стандарте).
>
> В случае с Xamarin.Forms, некоторые языки являются допустимыми в Android, но не соответствуют допустимому `CultureInfo` .NET и приведенный выше попыток для обработки этого.
>
> Разработчики должны изменить `iOSToDotnetLanguage` и `ToDotnetFallbackLanguage` методов для обработки определенных вариантов, необходимых для их поддерживаемых языков.

После этот код будет добавлен в проект приложения Android, он будет автоматически отображаться переведенных строк.

> [!NOTE]
>️ **предупреждение:** переведенные строки работе в Android версии сборки, но не во время отладки, щелкните правой кнопкой мыши **проекта Android** и выберите **параметры > сборки > Android Построение** и убедитесь, что **быстрое развертывание сборок** не установлен. Этот параметр вызывает проблемы с загрузкой ресурсы и не следует использовать при тестировании локализованных приложений.

Дополнительные сведения о локализации Android см. в разделе [Android локализации](~/android/app-fundamentals/localization.md).

#### <a name="universal-windows-platform"></a>Универсальная платформа Windows 

Проекты универсальной платформы Windows (UWP) не требуют зависимостей службы. Вместо этого эта платформа автоматически задает культуру ресурсов правильно.

##### <a name="assemblyinfocs"></a>AssemblyInfo.cs

Разверните узел свойств в проекте библиотеки .NET Standard и дважды щелкните на **AssemblyInfo.cs** файла. Добавьте следующую строку в файл, чтобы установить английский язык ассемблера нейтральные ресурсы:

```csharp
[assembly: NeutralResourcesLanguage("en")]
```

Это сообщает диспетчеру ресурсов по умолчанию приложения языка и региональных параметров, поэтому гарантии, что строки, определенные в файле RESX нейтрального языка (**AppResources.resx**) будет отображаться, когда приложение выполняется в одном английских языковых стандартов.

### <a name="example"></a>Пример

После обновления проектов платформы как показано выше и перекомпилировать приложение с преобразованием RESX-файлы, обновленные переводы будут доступны в одном приложении. Ниже приведен снимок экрана из образца кода, преобразуется в китайском (упрощенном).

![](text-images/simple-example-hans.png "Кросс платформенных пользовательских интерфейсов, преобразуются в китайский (упрощенный)")

Дополнительные сведения о локализации UWP в разделе [UWP локализации](/windows/uwp/design/globalizing/globalizing-portal/).

## <a name="localizing-xaml"></a>Локализация XAML

При построении разметку Xamarin.Forms пользовательский интерфейс в XAML будет похоже на это, со строками, внедренные непосредственно в коде XML:

```xaml
<Label Text="Notes:" />
<Entry Placeholder="eg. buy milk" />
<Button Text="Add to list" />
```

В идеальном случае мы перевести элементы управления пользовательского интерфейса непосредственно в языке XAML, можно сделать, создав *расширение разметки*. Ниже приведен код для расширения разметки, предоставляющую ресурсов RESX в XAML. Этот класс следует добавить общий код Xamarin.Forms (вместе с XAML-страницы):

```csharp
using System;
using System.Globalization;
using System.Reflection;
using System.Resources;
using Xamarin.Forms;
using Xamarin.Forms.Xaml;

namespace UsingResxLocalization
{
    // You exclude the 'Extension' suffix when using in XAML
    [ContentProperty("Text")]
    public class TranslateExtension : IMarkupExtension
    {
        readonly CultureInfo ci = null;
        const string ResourceId = "UsingResxLocalization.Resx.AppResources";

        static readonly Lazy<ResourceManager> ResMgr = new Lazy<ResourceManager>(
            () => new ResourceManager(ResourceId, IntrospectionExtensions.GetTypeInfo(typeof(TranslateExtension)).Assembly));

        public string Text { get; set; }

        public TranslateExtension()
        {
            if (Device.RuntimePlatform == Device.iOS || Device.RuntimePlatform == Device.Android)
            {
                ci = DependencyService.Get<ILocalize>().GetCurrentCultureInfo();
            }
        }

        public object ProvideValue(IServiceProvider serviceProvider)
        {
            if (Text == null)
                return string.Empty;

            var translation = ResMgr.Value.GetString(Text, ci);
            if (translation == null)
            {
#if DEBUG
                throw new ArgumentException(
                    string.Format("Key '{0}' was not found in resources '{1}' for culture '{2}'.", Text, ResourceId, ci.Name),
                    "Text");
#else
                translation = Text; // HACK: returns the key, which GETS DISPLAYED TO THE USER
#endif
            }
            return translation;
        }
    }
}
```

Следующие маркеры рассматриваются важные элементы в приведенном выше коде:

* Класс называется `TranslateExtension`, но мы называем соглашением является **перевод** в разметку.
* Этот класс реализует `IMarkupExtension`, необходимые для Xamarin.Forms для его работы.
* `"UsingResxLocalization.Resx.AppResources"` Представляет идентификатор ресурса для ресурсов RESX. Он состоит из наших пространство имен по умолчанию, папку, где находятся файлы ресурсов и имя файла по умолчанию RESX.
* `ResourceManager` Класс создается с помощью `IntrospectionExtensions.GetTypeInfo(typeof(TranslateExtension)).Assembly)` для определения текущей сборки для загрузки ресурсов и кэшированные в статическом `ResMgr` поля. Оно создается как `Lazy` тип, так что его создание откладывается до первого использования в `ProvideValue` метод.
* `ci` использует службы зависимостей для получения выбранного языка пользователя от исходной операционной системой.
* `GetString` Представляет метод, который получает фактическое переведенные строки из файлов ресурсов. На универсальной платформе Windows `ci` будет иметь значение null, так как `ILocalize` не реализован интерфейс на этих платформах. Это эквивалентно вызову `GetString` метод только с первым параметром. Вместо этого платформа ресурсы автоматически определяют языковой стандарт и получит переведенных строк из соответствующего файла RESX.
* Обработка ошибок был включен для отладки, создавая исключение отсутствующие ресурсы (в `DEBUG` только в режиме).

В следующем XAML-фрагменте показано использование расширения разметки. Существует два шага для его работы.

1. Объявите пользовательский `xmlns:i18n` пространства имен в корневом узле. `namespace` И `assembly` должны соответствовать параметрам проекта точно - в этом примере они идентичны, но может отличаться в проекте.
2. Используйте `{Binding}` синтаксис для атрибутов, которые обычно будет содержать текст для вызова `Translate` расширения разметки. Ключ ресурса является единственным обязательным параметром.

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        x:Class="UsingResxLocalization.FirstPageXaml"
        xmlns:i18n="clr-namespace:UsingResxLocalization;assembly=UsingResxLocalization">
    <StackLayout Padding="0, 20, 0, 0">
        <Label Text="{i18n:Translate NotesLabel}" />
        <Entry Placeholder="{i18n:Translate NotesPlaceholder}" />
        <Button Text="{i18n:Translate AddButton}" />
    </StackLayout>
</ContentPage>
```

Следующие более подробный синтаксис допустим также для расширения разметки:

```xaml
<Button Text="{i18n:TranslateExtension Text=AddButton}" />
```

## <a name="localizing-platform-specific-elements"></a>Локализация элементов платформой

Несмотря на то, что мы можем решить преобразования пользовательского интерфейса в коде Xamarin.Forms, существуют некоторые элементы, которые необходимо выполнить в каждом проекте конкретную платформу. В этом разделе рассматривается локализации:

* Application Name
* Изображения

Пример проекта содержит локализованные изображение под названием **flag.png**, который упоминается в C#, следующим образом:

```csharp
var flag = new Image();
switch (Device.RuntimePlatform)
{
    case Device.iOS:
    case Device.Android:
        flag.Source = ImageSource.FromFile("flag.png");
        break;
    case Device.UWP:
        flag.Source = ImageSource.FromFile("Assets/Images/flag.png");
        break;
}
```

Изображение флаг также есть ссылки в XAML следующим образом:

```xaml
<Image>
  <Image.Source>
    <OnPlatform x:TypeArguments="ImageSource">
        <On Platform="iOS, Android" Value="flag.png" />
        <On Platform="UWP" Value="Assets/Images/flag.png" />
    </OnPlatform>
  </Image.Source>
</Image>
```

Все платформы автоматически обрабатывает ссылки на изображения подобные до локализованных версий образов, при условии, что реализуются структуры проекта, описанный ниже.

### <a name="ios-application-project"></a>Проект приложения iOS

операций ввода-вывода использует стандарт именования вызывается локализации проектов или **.lproj** каталоги, которые должны содержать изображения и строковые ресурсы. Эти каталоги могут содержать локализованные версии изображений, используемых в приложении, а также **InfoPlist.strings** файл, который можно использовать для локализации имени приложения. Дополнительные сведения о локализации iOS см. в разделе [iOS локализации](~/ios/app-fundamentals/localization/index.md).

#### <a name="images"></a>Изображения

Этом снимке экрана показан пример приложения iOS с конкретного языка **.lproj** каталоги. Испанский каталог называется **es.lproj**, содержит локализованные версии изображения по умолчанию, а также **flag.png**:

![](text-images/ios-resources.png "Каталоги проектов локализации iOS")

Каждый каталог CLR содержит копию **flag.png**, локализованный для этого языка. Если изображение не предоставляется, изображение в каталоге по умолчанию язык операционной системы по умолчанию. Для полной поддержки Сетчатка необходимо предоставить **@2x** и **@3x** копий каждого изображения.

#### <a name="app-name"></a>Имя приложения

Содержимое **InfoPlist.strings** представляет собой просто один ключ значение для настройки имени приложения:

```csharp
"CFBundleDisplayName" = "ResxEspañol";
```

При запуске приложения, имя приложения и изображения как локализуются:

![](text-images/ios-imageicon.png "iOS образец приложения текста и изображений локализации")

### <a name="android-application-project"></a>Проект приложения Android

Android соответствует другую схему для хранения локализованного образов с помощью различных **drawable** и **строки** каталоги с суффиксом кода языка. При необходимости (например, zh-TW или pt-BR) код языкового стандарта четырех букв, обратите внимание, что Android требуется дополнительное **r** следующие тире или предыдущем кода языкового стандарта (например) zh-rTW или pt rBR). Дополнительные сведения о локализации Android см. в разделе [Android локализации](~/android/app-fundamentals/localization.md).

#### <a name="images"></a>Изображения

На этом снимке экрана показано Android пример с некоторыми локализованных строк и drawables:

![](text-images/android-resources.png "Android локализованные Drawables и строка каталогов")

Обратите внимание, что Android не использует zh-Hans и zh-Hant коды для упрощенное и традиционное письмо; Вместо этого он поддерживает только локальная коды zh-CN и zh-TW.

Для поддержки другое разрешение изображения для экранов с высокой плотности, создать дополнительный язык папки с `-*dpi` суффиксами, такие как **drawables-es-mdpi**, **drawables-es-xdpi**, **drawables-es-xxdpi**и т. д. В разделе [Android ресурсов вместо предоставления](http://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources) для получения дополнительной информации.

#### <a name="app-name"></a>Имя приложения

Содержимое **strings.xml** представляет собой просто один ключ значение для настройки имени приложения:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">ResxEspañol</string>
</resources>
```

Обновление **MainActivity.cs** в проекте приложения Android, чтобы `Label` ссылается на строки XML.

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

Приложение теперь есть перевод имя приложения и изображения. Ниже приведен снимок экрана результата (на испанском языке).

![](text-images/android-imageicon.png "Приложение Android образец текста и изображений локализации")

### <a name="universal-windows-platform-application-projects"></a>Проекты приложений для платформы универсальных приложений Windows

Универсальная платформа Windows может иметь инфраструктуры ресурсов, который упрощает локализацию изображения и имя приложения. Дополнительные сведения о локализации UWP в разделе [UWP локализации](/windows/uwp/design/globalizing/globalizing-portal/).

#### <a name="images"></a>Изображения

Изображения можно локализовать, поместив их в папке для определенного ресурса, как показано на следующем снимке экрана:

![](text-images/uwp-image-folder-structure.png "Структура папок локализации изображения UWP")

Во время выполнения инфраструктура ресурсов Windows будет выберите соответствующий образ на основе языкового стандарта пользователя.

## <a name="summary"></a>Сводка

Xamarin.Forms приложений можно локализовать с помощью RESX-файлы и классов глобализации .NET. Помимо небольшой объем кода под конкретную платформу для обнаружения предпочитаемого пользователем языка большая часть усилий локализации объединены в общий код.

Образы обычно обрабатываются образом платформой, чтобы воспользоваться преимуществами поддержки нескольких разрешения, предоставляемые в iOS и Android.

## <a name="related-links"></a>Связанные ссылки

- [Пример локализации RESX](https://developer.xamarin.com/samples/xamarin-forms/UsingResxLocalization/)
- [Пример TodoLocalized приложения](https://developer.xamarin.com/samples/xamarin-forms/TodoLocalized/)
- [Кросс платформенных локализации](~/cross-platform/app-fundamentals/localization.md)
- [iOS локализации](~/ios/app-fundamentals/localization/index.md)
- [Android локализации](~/android/app-fundamentals/localization.md)
- [Локализация UWP](/windows/uwp/design/globalizing/globalizing-portal/)
- [С помощью класса CultureInfo (MSDN)](http://msdn.microsoft.com/library/87k6sx8t%28v=vs.90%29.aspx)
- [Обнаружение и использование ресурсов для определенного языка и региональных параметров (MSDN)](http://msdn.microsoft.com/library/s9ckwb4b%28v=vs.90%29.aspx)
