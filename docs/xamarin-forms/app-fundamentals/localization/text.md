---
title: Локализация строк и изображений
description: Приложения Xamarin.Forms можно локализовать с помощью файлов ресурсов .NET.
ms.prod: xamarin
ms.assetid: 852B4ED3-2D2D-48A5-A759-A6591F6A1509
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/06/2016
ms.openlocfilehash: 09fe3587e4e435383822e50bd12616747b807f82
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108461"
---
# <a name="localization"></a>Локализация

_Приложения Xamarin.Forms можно локализовать с помощью файлов ресурсов .NET._

## <a name="overview"></a>Обзор

Встроенный механизм для локализации приложений использует [RESX-файлы](http://msdn.microsoft.com/library/ekyft91f(v=vs.90).aspx) и классы в пространствах имен `System.Resources` и `System.Globalization`. RESX-файлы, содержащие переведенные строки, внедряются в сборку Xamarin.Forms вместе с создаваемым компилятором классом, который обеспечивает строго типизированный доступ к переводам. Затем переведенный текст можно извлечь в коде.

### <a name="sample-code"></a>Пример кода

Существует два примера, связанных с этим документом.

* [UsingResxLocalization](https://github.com/xamarin/xamarin-forms-samples/tree/master/UsingResxLocalization) — это очень простая демонстрация описанных понятий. Все приведенные ниже фрагменты кода входят в этот пример.
* [TodoLocalized](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized) — это базовое работающее приложение, которое использует эти методы локализации.

#### <a name="shared-projects-are-not-recommended"></a>Общие проекты не рекомендуются

Пример TodoLocalized включает [демонстрацию общих проектов](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized/SharedProject/), но из-за ограничений системы сборка файлы ресурсов не получают созданный файл **.designer.cs**, что препятствует доступу к переведенным строкам со строгой типизацией в коде.

Далее в этом документе описываются проекты, использующие шаблон библиотеки .NET Standard в Xamarin.Forms.

## <a name="globalizing-xamarinforms-code"></a>Глобализация кода Xamarin.Forms

**Глобализация** приложения — это процесс подготовки приложения для доступа по всему миру. Это означает написание кода, который может отображать разные языки.

Один из ключевых элементов глобализации приложения — создание пользовательского интерфейса таким образом, чтобы исключить *жестко заданный* текст. Вместо этого все отображаемое для пользователя должно извлекаться из набора строк, которые переведены на выбранный язык.

В этом документе мы рассмотрим, как использовать файлы RESX для хранения этих строк и извлекать их для отображения в зависимости от предпочтений пользователя.

В примере рассматриваются английский, французский, испанский, немецкий, китайский, японский, русский и португальский (Бразилия). Приложения могут быть переведены на любое количество языков.

> [!NOTE]
> На универсальной платформе Windows для локализации push-уведомлений следует использовать RESW-файлы вместо RESX-файлов. Подробнее об этом см. в разделе [Локализация на универсальной платформе Windows](/windows/uwp/design/globalizing/globalizing-portal/).

### <a name="adding-resources"></a>Добавление ресурсов

Первый шаг глобализации приложения библиотеки .NET Standard в Xamarin.Forms — добавление файлов ресурсов RESX, которые будут использоваться для хранения всего текста, используемого в приложении. Нам нужно добавить RESX-файлы, содержащие текст по умолчанию, а затем добавить дополнительные RESX-файлы для каждого языка, который требуется поддерживать.

#### <a name="base-language-resource"></a>Базовый ресурс языка

Файл базовых ресурсов (RESX) будет содержать строки языка по умолчанию (в примерах это английский). Добавьте файл в обычный проект кода Xamarin.Forms, щелкнув правой кнопкой мыши на проекте и выбрав **Добавить > Новый файл...**.

Выберите понятное имя, например **AppResources**, и нажмите **ОК**.

[![Добавить файл ресурсов](text-images/resx-new-file-sml.png "Диалоговое окно \"Новый файл\"")](text-images/resx-new-file.png#lightbox "Диалоговое окно \"Новый файл\"")

В проект будут добавлены два файла:

* файл **AppResources.resx**, где переводимые строки хранятся в формате XML;
* файл **AppResources.designer.cs**, который объявляет разделяемый класс для хранения ссылок на все элементы, созданные в XML RESX-файле.

Дерево решений будет показывать файлы как связанные. RESX-файл *следует* отредактировать, чтобы добавить новые переводимые строки; файл **. designer.cs** *не* нужно редактировать.

![](text-images/appresources-tree.png "Файл AppResources.resx")

##### <a name="string-visibility"></a>Видимость строк

По умолчанию при создании строго типизированных ссылок на строки они будут `internal` для сборки. Это связано с тем, что средство сборки по умолчанию для RESX-файлов создает файл **. designer.cs** со свойствами `internal`.

Выберите файл **AppResources.resx** и откройте панель **свойств**, чтобы увидеть, где настраивается это средство сборки. Снимок экрана ниже показывает **пользовательское средство: ResXFileCodeGenerator**.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](text-images/vs-resx-internal-sml.png "Окно свойств для AppResources.Resx")](text-images/vs-resx-internal.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![](text-images/xs-resx-internal-sml.png "Панель свойств для AppResources.Resx")](text-images/xs-resx-internal.png#lightbox)

-----

Чтобы строго типизированные свойства строки `public`, необходимо вручную изменить конфигурацию на **Пользовательское средство: PublicResXFileCodeGenerator**, как показано на следующем снимке экрана.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](text-images/vs-resx-public-sml.png "Окно свойств для AppResources.Resx")](text-images/vs-resx-public.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![](text-images/xs-resx-internal-sml.png "Панель свойств для AppResources.Resx")](text-images/xs-resx-internal.png#lightbox)


[![](text-images/xs-resx-public-sml.png "Панель свойств для AppResources.Resx")](text-images/xs-resx-public.png#lightbox)

-----

Это изменение является необязательным и требуется только в том случае, если вы хотите ссылаться на локализованные строки в разных сборках (например, если вы помещаете RESX-файлы в другую сборку в коде). В примере для этого раздела строки остаются `internal`, так как они определены в той же сборке библиотеки .NET Standard Xamarin.Forms, где они используются.

Необходимо задать пользовательское средство только в базовом RESX-файле, как показано выше. Не нужно задавать *никакие* средства сборки в языковых RESX-файлах, рассмотренных в следующих разделах.

##### <a name="editing-the-resx-file"></a>Редактирование RESX-файла

К сожалению, в Visual Studio для Mac нет встроенного редактора RESX. Добавление новых переводимых строк требует добавления нового элемента XML `data` для каждой строки. Каждый элемент `data` может содержать следующее.

* Атрибут `name` (обязательно) необходим для этой переводимой строки. Это должно быть допустимое имя свойства C#, поэтому пробелы или специальные символы не допускаются.
* Элемент `value` (обязательно) — это фактическая строка, которая отображается в приложении.
* Элемент `comment` (необязательно) может содержать инструкции для переводчика, которые объясняют, как используется эта строка.
* Атрибут `xml:space` (необязательно) управляет сохранением интервалов в строке.

Некоторые примеры элементов `data`:

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

По мере написания приложения каждый фрагмент текста, отображаемый пользователю, необходимо добавлять в базовый файл ресурсов RESX в новый элемент `data`. Рекомендуется включать как можно больше `comment`, чтобы качество перевода было высоким.

> [!NOTE]
> Visual Studio (включая бесплатный выпуск Community) содержит базовый редактор RESX. Если у вас есть доступ к компьютеру Windows, это удобный способ добавлять и изменять строки в RESX-файлах.

#### <a name="language-specific-resources"></a>Языковые ресурсы

Как правило, фактический перевод строк текста по умолчанию выполняется только после написания больших блоков приложения (в этом случае RESX-файл по умолчанию будет содержать множество строк). По-прежнему рекомендуется добавлять языковые ресурсы на ранних этапах цикла разработки, при необходимости добавляя машинный перевод для тестирования кода локализации.

Один дополнительный RESX-файл добавляется для каждого языка, который будет поддерживаться.
Файлы языковых ресурсов должны соответствовать соглашению об именовании: используйте одно имя файла как базовый файл ресурса (например, **AppResources**), затем точка (.) и код языка. Примеры:

* **AppResources.fr.resx** — французский.
* **AppResources.es.resx** — испанский.
* **AppResources.de.resx** — немецкий.
* **AppResources.ja.resx** — японский.
* **AppResources.zh-Hans.resx** — китайский (упрощенное письмо).
* **AppResources.zh-Hant.resx** — китайский (традиционное письмо).
* **AppResources.pt.resx** — португальский.
* **AppResources.pt-BR.resx** — португальский (Бразилия).

Общий шаблон — использовать двухбуквенные коды языков, но есть несколько примеров (например, китайский), в которых используется другой формат, и другие примеры (например, португальский (Бразилия)), где требуется четырехзначный код языка.

Эти файлы языковых ресурсов *не* требуют разделяемый класс **. designer.cs**, поэтому их можно добавлять как обычные XML-файлы с параметром **Действие при сборке: EmbeddedResource**. На этом снимке экрана показано решение с файлами языковых ресурсов.

![](text-images/appresources-langs.png "Файлы языковых ресурсов")

После разработки приложения и добавления текста в базовый RESX-файл вы должны отправить его переводчикам, которые переведут каждый элемент `data` и вернут файл языкового ресурса (используя соглашение об именовании), который вы включите в приложение. Ниже приведены некоторые примеры с машинным переводом.

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

Переводчик изменяет только элемент `value` — `comment` не предназначен для перевода. Помните, что при редактировании XML-файлов нужно экранировать зарезервированные знаки, такие как `<`, `>`, `&` с `&lt;`, `&gt;` и `&amp;`, если они находятся в `value` или `comment`.

<a name="incode" />

### <a name="using-resources-in-code"></a>Использование ресурсов в коде

Строки в файлах ресурсов RESX будут доступны для использования в коде пользовательского интерфейса с помощью класса `AppResources`. `name`, назначенное каждой строке в RESX-файле, становится свойством для этого класса, на который можно ссылаться в коде Xamarin.Forms, как показано ниже.

```csharp
var myLabel = new Label ();
var myEntry = new Entry ();
var myButton = new Button ();
// populate UI with translated text values from resources
myLabel.Text = AppResources.NotesLabel;
myEntry.Placeholder = AppResources.NotesPlaceholder;
myButton.Text = AppResources.AddButton;
```

Пользовательский интерфейс в iOS, Android и на универсальной платформе Windows (UWP) преобразуется для просмотра так, как ожидалось, только теперь можно перевести приложение на несколько языков, так как текст загружается из ресурса, а не закодирован жестко. Ниже приведен снимок экрана пользовательского интерфейса на каждой платформе до перевода.

![](text-images/simple-example-english.png "Кроссплатформенные пользовательские интерфейсы до перевода")

### <a name="troubleshooting"></a>Устранение неполадок

#### <a name="testing-a-specific-language"></a>Тестирование конкретного языка

Бывает сложно переключить симулятор или устройство на разные языки, особенно во время разработки, когда необходимо быстро протестировать разные языки и региональные параметры.

Вы можете принудительно загрузить конкретный язык, задав `Culture`, как показано в этом фрагменте кода.

```csharp
// force a specific culture, useful for quick testing
AppResources.Culture =  new CultureInfo("fr-FR");
```

Такой подход — задать язык и региональные параметры непосредственно в классе `AppResources` — также можно использовать для реализации селектора языка внутри приложения (а не с помощью языкового стандарта устройства).

#### <a name="loading-embedded-resources"></a>Загрузка внедренных ресурсов

Следующий фрагмент кода полезен при отладке проблем с внедренными ресурсами (такими как RESX-файлы). Добавьте этот код в приложение (на раннем этапе жизненного цикла), и он перечислит все ресурсы, внедренные в сборку, показав полный идентификатор ресурсов:

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

В файле **AppResources.Designer.cs** (около *строки 33*) указано полное *имя диспетчера ресурсов* (например, `"UsingResxLocalization.Resx.AppResources"`), как в приведенном ниже коде.

```csharp
System.Resources.ResourceManager temp =
        new System.Resources.ResourceManager(
                "UsingResxLocalization.Resx.AppResources",
                typeof(AppResources).GetTypeInfo().Assembly);
```

Просмотрите **выходные данные приложения**, чтобы узнать результаты отладки кода и проверить, что указаны правильные ресурсы (т. е. `"UsingResxLocalization.Resx.AppResources"`).

Если нет, класс `AppResources` не сможет загрузить свои ресурсы.
Проверьте следующее, чтобы устранить проблемы, когда не удается найти ресурсы.

* Пространство имен по умолчанию для проекта совпадает с корневым пространством имен в файле **AppResources.Designer.cs**.
* Если файл **AppResources.resx** находится в подкаталоге, имя подкаталога должно быть частью пространства имен *и* частью идентификатора ресурса.
* Для файла **AppResources.resx** указано **Действие при сборке: EmbeddedResource**.
* Установлен флажок **Параметры проекта > Исходный код > Политики именования .NET > Использовать имена ресурсов в стиле Visual Studio**. Вы можете убрать этот флажок при желании, но пространства имен, используемые при ссылке на ресурсы RESX, потребуется обновить в приложении.

#### <a name="doesnt-work-in-debug-mode-android-only"></a>Не работает в режиме отладки (только Android)

Если переведенные строки работают в сборках выпуска Android, но не во время отладки, щелкните правой кнопкой мыши **Проект Android**, выберите **Параметры > Сборка > Сборка Android** и убедитесь, что флажок **Быстрое развертывание сборок** не установлен. Этот параметр вызывает проблемы с загрузкой ресурсов, и его не следует использовать при тестировании локализованных приложений.

### <a name="displaying-the-correct-language"></a>Отображение нужного языка

Пока что мы изучали, как написать код таким образом, чтобы включить возможность переводов, но еще не обсуждали, как сделать так, чтобы они действительно отображались. Код Xamarin.Forms может использовать ресурсы .NET для загрузки переводов на правильный язык, но нам нужно запросить операционную систему на каждой платформе, чтобы определить, какой язык выбрал пользователь.

Поскольку нам нужен зависящий от платформы код, чтобы узнать языковые настройки пользователя, используйте [службу зависимостей](~/xamarin-forms/app-fundamentals/dependency-service/index.md), чтобы предоставить эту информацию в приложении Xamarin.Forms и реализовать ее для каждой платформы.

Во-первых, определите интерфейс для получения выбранного языка и региональных параметров пользователя, как в приведенном ниже коде.

```csharp
public interface ILocalize
{
    CultureInfo GetCurrentCultureInfo ();
    void SetLocale (CultureInfo ci);
}
```

Во-вторых, используйте [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) в классе Xamarin.Forms `App` для вызова интерфейса и установки для языка и региональных параметров ресурса RESX правильного значения. Нам не нужно вручную задавать это значение для универсальной платформы Windows, так как платформа ресурсов автоматически распознает выбранный язык на этих платформах.

```csharp
if (Device.RuntimePlatform == Device.iOS || Device.RuntimePlatform == Device.Android)
{
    var ci = DependencyService.Get<ILocalize>().GetCurrentCultureInfo();
    Resx.AppResources.Culture = ci; // set the RESX for resource localization
    DependencyService.Get<ILocalize>().SetLocale(ci); // set the Thread for locale-aware methods
}
```

Ресурс `Culture` необходимо задать при первой загрузке приложения, чтобы использовать строку на правильном языке. При необходимости вы можете изменить это значение в соответствии с событиями конкретных платформ, которые могут возникать в iOS или Android, если пользователь меняет языковые настройки во время работы приложения.

Реализации для интерфейса `ILocalize` показаны в разделе [Код для конкретных платформ](#Platform-specific_Code) ниже. Эти реализации используют этот вспомогательный класс `PlatformCulture`:

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

### <a name="platform-specific-code"></a>Код для конкретных платформ

Код для определения того, какой язык будет отображаться, должен зависеть от платформы, так как iOS, Android и универсальная платформа Windows по-разному предоставляют эту информацию. Ниже предоставляется код для службы зависимостей `ILocalize` для каждой платформы, а также дополнительные требования платформ для корректного отображения локализованного текста.

Код для платформы также должен обрабатывать случаи, когда операционная система позволяет пользователю настроить идентификатор языкового стандарта, который не поддерживается классом `CultureInfo` .NET. В таких случаях нужно написать пользовательский код для определения неподдерживаемых языковых стандартов и замены наиболее подходящего языкового стандарта, совместимого с .NET.

#### <a name="ios-application-project"></a>Проект приложения iOS

Пользователи iOS выбирают язык отдельно от региональных параметров, определяющих дату и время. Чтобы загрузить правильные ресурсы для локализации приложения Xamarin.Forms, нам просто нужно запросить массив `NSLocale.PreferredLanguages` для первого элемента.

Следующую реализацию службы зависимостей `ILocalize` необходимо поместить в проект приложения iOS. Поскольку iOS использует символы подчеркивания, а не тире (это стандартное представление .NET), код заменяет символ подчеркивания перед созданием экземпляра класса `CultureInfo`:

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
> Блоки `try/catch` в методе `GetCurrentCultureInfo` имитируют резервное поведение, которое обычно используется с описателями языкового стандарта — если точное соответствие не найдено, выполняется поиск близкого соответствия на основе только языка (первый блок символов в языковом стандарте).
>
> В случае Xamarin.Forms некоторые языковые стандарты являются допустимыми в iOS, но не соответствуют допустимому `CultureInfo` в .NET, и приведенный выше код пытается решить проблему.
>
> Например, на экране iOS **Параметры > Общий язык &amp; Регион** можно выбрать **Язык** телефона **Английский**, а **Регион** — **Испания**, поэтому строка языкового стандарта будет выглядеть как `"en-ES"`. Если создание `CultureInfo` завершается с ошибкой, код использует только первые две буквы для выбора отображаемого языка.
>
> Разработчики должны изменить методы `iOSToDotnetLanguage` и `ToDotnetFallbackLanguage` для обработки конкретных случаев для поддерживаемых языков.


Некоторые определяемые системой элементы пользовательского интерфейса автоматически переводятся в iOS, например кнопка **Готово** в элементе управления `Picker`. Чтобы заставить iOS перевести эти элементы, нужно указать поддерживаемые языки в файле **Info.plist**. Вы можете добавить эти значения с помощью **Info.plist > Источник** следующим образом.

![Ключи локализации в Info.plist](text-images/info-plist.png "Ключи локализации в Info.plist")

Или вы можете открыть файл **Info.plist** в редакторе XML и изменить значения напрямую:

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

Когда вы реализуете службу зависимостей и обновите файл **Info.plist**, приложение для iOS сможет отображать локализованный текст.

> [!NOTE]
> Apple обрабатывает португальский не совсем так, как можно ожидать.
> Из [их документации](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW2): _"используйте pt как идентификатор языка для португальского языка в Бразилии и pt-PT как идентификатор языка для португальского языка в Португалии"_.
> Это означает, что, если португальский язык выбран в нестандартном языковом стандарте, резервным языком в iOS будет португальский (Бразилия), если только код не изменяет это поведение (например, как `ToDotnetFallbackLanguage` выше).

Дополнительные сведения о локализации в iOS см. в разделе [Локализация в iOS](~/ios/app-fundamentals/localization/index.md).

#### <a name="android-application-project"></a>Проект приложения Android

Android предоставляет текущий выбранный языковой стандарт через `Java.Util.Locale.Default` и тоже использует разделитель в виде символа подчеркивания вместо тире (который заменяется следующим кодом). Добавьте в проект приложения Android эту реализацию службы зависимостей:

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
> Блоки `try/catch` в методе `GetCurrentCultureInfo` имитируют резервное поведение, которое обычно используется с описателями языкового стандарта — если точное соответствие не найдено, выполняется поиск близкого соответствия на основе только языка (первый блок символов в языковом стандарте).
>
> В случае Xamarin.Forms некоторые языковые стандарты являются допустимыми в Android, но не соответствуют допустимому `CultureInfo` в .NET, и приведенный выше код пытается решить проблему.
>
> Разработчики должны изменить методы `iOSToDotnetLanguage` и `ToDotnetFallbackLanguage` для обработки конкретных случаев для поддерживаемых языков.

Когда этот код будет добавлен в проект приложения Android, он сможет автоматически отображать переведенные строки.

> [!NOTE]
>️**ПРЕДУПРЕЖДЕНИЕ.** Если переведенные строки работают в сборках выпуска Android, но не во время отладки, щелкните правой кнопкой мыши **Проект Android**, выберите **Параметры > Сборка > Сборка Android** и убедитесь, что флажок **Быстрое развертывание сборок** не установлен. Этот параметр вызывает проблемы с загрузкой ресурсов, и его не следует использовать при тестировании локализованных приложений.

Дополнительные сведения о локализации в Android см. в разделе [Локализация в Android](~/android/app-fundamentals/localization.md).

#### <a name="universal-windows-platform"></a>Универсальная платформа Windows 

Проекты универсальной платформы Windows (UWP) не требуют службы зависимостей. Вместо этого эта платформа автоматически правильно задает язык и региональные параметры ресурса.

##### <a name="assemblyinfocs"></a>AssemblyInfo.cs

Разверните узел свойств в проекте библиотеки .NET Standard и дважды щелкните файл **AssemblyInfo.cs**. Добавьте следующую строку в файл, чтобы установить английский в качестве нейтрального языка сборки ресурсов:

```csharp
[assembly: NeutralResourcesLanguage("en")]
```

Это значение сообщает диспетчеру ресурсов язык и региональные параметры приложения по умолчанию, поэтому строки, определенные в RESX-файле, не учитывающем язык (**AppResources.resx**), будут отображаться, когда приложение выполняется в одном из языковых стандартов на английском языке.

### <a name="example"></a>Пример

После обновления проектов для конкретной платформы, как показано выше, и перекомпиляции приложения с переведенными RESX-файлами обновленные переводы будут доступны в каждом приложении. Ниже приведен снимок экрана из примера кода, переведенный на упрощенный китайский язык.

![](text-images/simple-example-hans.png "Кроссплатформенные элементы пользовательского интерфейса, переведенные на упрощенный китайский язык")

Дополнительные сведения о локализации на универсальной платформе Windows см. в разделе [Локализация на универсальной платформе Windows](/windows/uwp/design/globalizing/globalizing-portal/).

## <a name="localizing-xaml"></a>Локализация XAML

При сборке пользовательского интерфейса Xamarin.Forms в XAML разметка будет выглядеть примерно следующим образом и строки будут внедрены напрямую в XML:

```xaml
<Label Text="Notes:" />
<Entry Placeholder="eg. buy milk" />
<Button Text="Add to list" />
```

В идеале мы могли бы перевести элементы пользовательского интерфейса напрямую в XAML с помощью создания *расширения разметки*. Ниже приведен код для расширения разметки, предоставляющего ресурсы RESX для XAML. Этот класс следует добавить в общий код Xamarin.Forms (вместе со страницами XAML):

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

Ниже объясняются важные элементы в приведенном выше коде.

* Класс назван `TranslateExtension`, но по соглашению мы можем называть его **Translate** в нашей разметке.
* Этот класс реализует `IMarkupExtension`, которое необходимо для работы Xamarin.Forms.
* `"UsingResxLocalization.Resx.AppResources"` — это идентификатор ресурса для ресурсов RESX. Он состоит из нашего пространства имен по умолчанию, папки, где находятся файлы ресурсов, и имени файла RESX по умолчанию.
* Класс `ResourceManager` создается с помощью `IntrospectionExtensions.GetTypeInfo(typeof(TranslateExtension)).Assembly)` для определения текущей сборки для загрузки ресурсов и кэшируется в статическом поле `ResMgr`. Он создается как тип `Lazy`, чтобы его создание откладывалось до первого использования в методе `ProvideValue`.
* `ci` использует службу зависимостей для получения выбранного языка пользователя из собственной операционной системы.
* `GetString` — это метод, который извлекает фактическую переведенную строку из файлов ресурсов. На универсальной платформе Windows `ci` будет иметь значение NULL, так как интерфейс `ILocalize` не реализован на этих платформах. Это эквивалентно вызову метода `GetString` только с первым параметром. Вместо этого платформа ресурсов автоматически распознает языковой стандарт и извлекает переведенную строку из соответствующего RESX-файла.
* Включена обработка ошибок для отладки отсутствующих ресурсов путем создания исключения (только в режиме `DEBUG`).

В следующем фрагменте XAML показано, как использовать расширение разметки. Для этого используется два шага:

1. Объявите пользовательское пространство имен `xmlns:i18n` в корневом узле. `namespace` и `assembly` должны в точности соответствовать параметрам проекта — в этом примере они идентичны, но могут отличаться в вашем проекте.
2. Используйте синтаксис `{Binding}` для атрибутов, которые обычно содержат текст для вызова расширения разметки `Translate`. Ключ ресурса является единственным обязательным параметром.

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

Следующий более подробный синтаксис также является допустимым для расширения разметки:

```xaml
<Button Text="{i18n:TranslateExtension Text=AddButton}" />
```

## <a name="localizing-platform-specific-elements"></a>Локализация элементов, зависящих от платформы

Хотя мы можем обработать перевод пользовательского интерфейса в коде Xamarin.Forms, некоторые элементы необходимо выполнить в проекте для конкретной платформы. В этом разделе мы рассмотрим, как локализовать:

* Application Name
* Изображения

Пример проекта содержит локализованное изображение **flag.png**, на которое ссылается код C# следующим образом:

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

На изображение флага также есть ссылки в XAML:

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

Все платформы автоматически разрешают подобные ссылки на изображение до локализованных версий изображений, если реализуются структуры проекта, описанные ниже.

### <a name="ios-application-project"></a>Проект приложения iOS

iOS использует стандарт именования "Проекты локализации" или каталоги **.lproj** для хранения ресурсов изображений и строк. Эти каталоги могут содержать локализованные версии изображений, используемых в приложении, а также файл **InfoPlist.strings**, который можно использовать для локализации имени приложения. Дополнительные сведения о локализации в iOS см. в разделе [Локализация в iOS](~/ios/app-fundamentals/localization/index.md).

#### <a name="images"></a>Изображения

На этом снимке экрана показан пример приложения iOS с языковыми каталогами **.lproj**. Испанский каталог **es.lproj** содержит локализованные версии изображения по умолчанию, а также **flag.png**.

![](text-images/ios-resources.png "Каталоги проекта локализации iOS")

Каждый каталог языка содержит копию файла **flag.png**, локализованного для этого языка. Если изображение не предоставляется, операционная система по умолчанию выберет изображение в каталоге языка по умолчанию. Для полной поддержки Retina необходимо предоставить копии **@2x** и **@3x** каждого изображения.

#### <a name="app-name"></a>Имя приложения

Содержание **InfoPlist.strings** — всего одна пара "ключ — значение" для настройки имени приложения:

```csharp
"CFBundleDisplayName" = "ResxEspañol";
```

При запуске приложения имя приложения и изображение локализуются:

![](text-images/ios-imageicon.png "Локализация изображения и текста в примере приложения iOS")

### <a name="android-application-project"></a>Проект приложения Android

В Android используется другая схема хранения локализованных изображений с помощью разных каталогов **прорисовываемых ресурсов** и **строк** с суффиксом кода языка. Когда нужен четырехбуквенный код языкового стандарта (например, zh-TW или pt-BR), Android требует дополнительную **r** после тире/перед кодом языкового стандарта (например, zh-rTW или pt-rBR). Дополнительные сведения о локализации в Android см. в разделе [Локализация в Android](~/android/app-fundamentals/localization.md).

#### <a name="images"></a>Изображения

На этом снимке экрана показан пример Android с локализованными строками и прорисовываемыми ресурсами.

![](text-images/android-resources.png "Каталоги локализованных прорисовываемых ресурсов и строк в Android")

Android не использует коды zh-Hans и zh-Hant для упрощенного и традиционного китайского, а поддерживает только коды страны zh-CN и zh-TW.

Для поддержки изображений в другом разрешении для экранов с высокой плотностью создайте дополнительные языковые папки с суффиксами `-*dpi`, например **drawables-es-mdpi**, **drawables-es-xdpi**, **drawables-es-xxdpi** и т. д. Дополнительные сведения см. в разделе [Предоставление альтернативных ресурсов Android](http://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources).

#### <a name="app-name"></a>Имя приложения

Содержание **strings.xml** — всего одна пара "ключ — значение" для настройки имени приложения:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">ResxEspañol</string>
</resources>
```

Обновите **MainActivity.cs** в проекте приложения Android, чтобы `Label` ссылался на строки XML.

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

Приложение теперь локализует имя приложения и изображение. Ниже приведен снимок экрана результата (на испанском языке).

![](text-images/android-imageicon.png "Локализация изображения и текста в примере приложения Android")

### <a name="universal-windows-platform-application-projects"></a>Проекты приложений универсальной платформы Windows

Универсальная платформа Windows обладает инфраструктурой ресурсов, которая упрощает локализацию изображений и имени приложения. Дополнительные сведения о локализации на универсальной платформе Windows см. в разделе [Локализация на универсальной платформе Windows](/windows/uwp/design/globalizing/globalizing-portal/).

#### <a name="images"></a>Изображения

Чтобы локализовать изображения, можно поместить их в папку для ресурса, как показано на следующем снимке экрана.

![](text-images/uwp-image-folder-structure.png "Структура папок локализации изображений на универсальной платформе Windows")

Во время выполнения инфраструктура ресурсов Windows выберет подходящее изображение на основе языкового стандарта пользователя.

## <a name="summary"></a>Сводка

Приложения Xamarin.Forms можно локализовать с помощью RESX-файлов и классов глобализации .NET. Помимо небольшого объема кода для конкретных платформ для обнаружения языковых настроек пользователя, большая часть усилий по локализации направлена на общий код.

Изображения обычно обрабатываются платформой, чтобы использовать преимущества различных решений, предоставляемых в iOS и Android.

## <a name="related-links"></a>Связанные ссылки

- [Пример локализации RESX](https://developer.xamarin.com/samples/xamarin-forms/UsingResxLocalization/)
- [Пример приложения TodoLocalized](https://developer.xamarin.com/samples/xamarin-forms/TodoLocalized/)
- [Кроссплатформенная локализация](~/cross-platform/app-fundamentals/localization.md)
- [Локализация в iOS](~/ios/app-fundamentals/localization/index.md)
- [Локализация в Android](~/android/app-fundamentals/localization.md)
- [Локализация на универсальной платформе Windows](/windows/uwp/design/globalizing/globalizing-portal/)
- [Использование класса CultureInfo (MSDN)](http://msdn.microsoft.com/library/87k6sx8t%28v=vs.90%29.aspx)
- [Обнаружение и использование ресурсов для определенного языка и региональных параметров (MSDN)](http://msdn.microsoft.com/library/s9ckwb4b%28v=vs.90%29.aspx)
