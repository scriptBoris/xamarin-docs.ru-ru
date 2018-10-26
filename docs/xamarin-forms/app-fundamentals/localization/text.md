---
title: Локализация строк и изображений
description: Приложения Xamarin.Forms можно было локализовать с помощью файлов ресурсов .NET.
ms.prod: xamarin
ms.assetid: 852B4ED3-2D2D-48A5-A759-A6591F6A1509
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/06/2016
ms.openlocfilehash: 09fe3587e4e435383822e50bd12616747b807f82
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108461"
---
# <a name="localization"></a>Локализация

_Приложения Xamarin.Forms можно было локализовать с помощью файлов ресурсов .NET._

## <a name="overview"></a>Обзор

Встроенный механизм для локализации приложений использует .NET [файлы RESX –](http://msdn.microsoft.com/library/ekyft91f(v=vs.90).aspx) и классы в `System.Resources` и `System.Globalization` пространства имен. Файлы RESX, содержащий переведенные строки внедряются в сборку Xamarin.Forms, а также класс, созданный компилятором, который обеспечивает строго типизированный доступ к переводы. Переведенный текст может быть извлечен в коде.

### <a name="sample-code"></a>Пример кода

Существует два образца, связанных с этим документом.

* [UsingResxLocalization](https://github.com/xamarin/xamarin-forms-samples/tree/master/UsingResxLocalization) — это очень простой Демонстрация сведения о понятиях. Все из этого примера приведены фрагменты кода, показано ниже.
* [TodoLocalized](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized) — это основные работающее приложение, которое использует эти методы локализации.

#### <a name="shared-projects-are-not-recommended"></a>Общие проекты не рекомендуются.

Пример TodoLocalized включает [Демонстрация общий проект](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized/SharedProject/) Однако из-за ограничений системы построения файлы ресурсов не получают **. designer.cs** созданный файл, который запрещает доступ к переведенные строки со строгой типизацией в коде.

Далее в этом документе относится к проектов с помощью Xamarin.Forms .NET Standard шаблон библиотеки.

## <a name="globalizing-xamarinforms-code"></a>Глобализация код Xamarin.Forms

**Глобализация** приложения — это процесс воплощение «world ready». Это означает, написав код, который может отображать на разных языках.

Одно из ключевых элементов глобализации приложения Создание пользовательского интерфейса, таким образом, что не *жестко* текста. Вместо этого все, отображаемых для пользователя должен быть извлечен из набора строк, которые переведена на своем языке.

В этом документе мы рассмотрим, как использовать файлы RESX для хранения этих строк и получить их для отображения в зависимости от предпочтений пользователя.

Примеры целевых языков на английский, французский, испанский, немецкий, китайский, японский, русский и португальский (Бразилия). Приложения могут быть переведены в минимальном или столько необходимых языках.

> [!NOTE]
> На универсальной платформе Windows файлов RESW-файлы можно использовать для отправки уведомлений локализации, а не файлы RESX. Дополнительные сведения см. в разделе [UWP локализации](/windows/uwp/design/globalizing/globalizing-portal/).

### <a name="adding-resources"></a>Добавление ресурсов

Первым шагом в Глобализация Xamarin.Forms .NET Standard Библиотечное приложение добавляет файлы ресурсов RESX, которые будут использоваться для хранения текста, используемого в приложении. Нам нужно добавить файл RESX, содержащий текст по умолчанию, а затем добавьте дополнительные RESX-файлы для каждого языка, которую требуется поддерживать.

#### <a name="base-language-resource"></a>Базовый язык ресурса

Файл базовых ресурсов (RESX) будет содержать строки языка по умолчанию (примерах предполагается, что является английский язык по умолчанию). Добавьте файл в проект Xamarin.Forms распространенных кода, щелкнув правой кнопкой мыши на проект и выбрав **Добавить > новый файл...** .

Выберите понятное имя, например **AppResources** и нажмите клавишу **ОК**.

[![Добавьте файл ресурсов](text-images/resx-new-file-sml.png "диалоговое окно создания файла")](text-images/resx-new-file.png#lightbox "диалоговое окно создания файла")

В проект будут добавлены два файла:

* **AppResources.resx** файл, где переводимые строки хранятся в формате XML.
* **AppResources.designer.cs** файл, который объявляет разделяемый класс для всех элементов, созданных в XML RESX-файле.

Дерево решений будет показывать все файлы, соответствующие. RESX-файла *следует* изменить, добавив новые переводимые строки; **. designer.cs** файл следует *не* редактироваться.

![](text-images/appresources-tree.png "Файл AppResources.resx")

##### <a name="string-visibility"></a>Видимость строки

По умолчанию при создании строго типизированных ссылок на строки, они будут `internal` на сборку. Это обусловлено создает средство сборки по умолчанию для файлов RESX **. designer.cs** файл с `internal` свойства.

Выберите **AppResources.resx** файл и Показать **свойства** настроить панель, чтобы увидеть, где — это средство построения. Снимок экрана ниже показан **пользовательское средство: ResXFileCodeGenerator**.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](text-images/vs-resx-internal-sml.png "Окно свойств для AppResources.Resx")](text-images/vs-resx-internal.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![](text-images/xs-resx-internal-sml.png "Панель свойств для AppResources.Resx")](text-images/xs-resx-internal.png#lightbox)

-----

Чтобы сделать свойства со строгой типизацией строки `public`, необходимо вручную изменить конфигурацию, чтобы **пользовательское средство: PublicResXFileCodeGenerator**, как показано на следующем снимке экрана:


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](text-images/vs-resx-public-sml.png "Окно свойств для AppResources.Resx")](text-images/vs-resx-public.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![](text-images/xs-resx-internal-sml.png "Панель свойств для AppResources.Resx")](text-images/xs-resx-internal.png#lightbox)


[![](text-images/xs-resx-public-sml.png "Панель свойств для AppResources.Resx")](text-images/xs-resx-public.png#lightbox)

-----

Это изменение является необязательным и только требуется, если вы хотите ссылаться на локализованных строк в разных сборках (например, если вы помещаете файлы RESX в другой сборке, в коде). В примере для этого раздела оставляет строки `internal` так, как они определены в той же Xamarin.Forms .NET Standard library сборке, где они используются.

Необходимо задавать пользовательского инструмента в базовый файл RESX, как показано выше. необходимо задать *любой* инструмент сборки на языковых файлов RESX, рассматриваются в следующих разделах.

##### <a name="editing-the-resx-file"></a>Редактирование RESX-файла

К сожалению, тут нет встроенного редактора RESX в Visual Studio для Mac. Добавление новых строк, переводимые требуется добавить новый XML `data` элемент для каждой строки. Каждый `data` элемент может содержать следующее:

* `name` (обязательный) атрибут является ключевым для данной переводимые строки. Он должен быть допустимым C# имя свойства — поэтому допускаются пробелы или специальные символы.
* `value` элемент (обязательно), являющийся фактическое строкой, отображаемой в приложении.
* `comment` (необязательно) элемент может содержать инструкции для перевода, которая объясняет, как эта строка используется.
* `xml:space` атрибут (необязательно) для управления сохраняется как интервал в строке.

Некоторые примеры `data` элементы показаны здесь:

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

Как приложение написано, каждый фрагмент текста, отображаемого пользователю необходимо добавить в базовый файл ресурсов RESX в новый `data` элемент. Рекомендуется включить `comment`s, насколько возможно, для обеспечения высокого качества перевода.

> [!NOTE]
> Visual Studio (включая бесплатный выпуск Community) содержит базовый редактор файлов RESX. Если у вас есть доступ к компьютеру Windows, которое может быть удобный способ добавлять и изменять строки в RESX-файлах.

#### <a name="language-specific-resources"></a>Ресурсы для конкретных языков

Как правило собственно перевод строки текста по умолчанию не выполняется, пока не были записаны большие блоки приложения (в этом случае RESX-файла по умолчанию будет содержать множество строк). По-прежнему рекомендуется добавлять ресурсы для конкретных языков на ранних этапах цикла разработки, при необходимости заполнения с помощью машинного перевода текста для помощи в тестировании локализации кода.

Один дополнительный файл RESX добавляется для каждого языка, которую требуется поддерживать.
Файлы языковых ресурсов должны соответствовать конкретного соглашения об именовании: используйте это имя, как базовые ресурсы файл (например) **AppResources**) следуют точка (.), а затем также код языка. Простые примеры включают следующее:

* **AppResources.fr.resx** -французский переводы языка.
* **AppResources.es.resx** -переводы испанского языка.
* **AppResources.de.resx** -переводы на немецком языке.
* **AppResources.ja.resx** -переводы на японском языке.
* **AppResources.zh Hans.resx** — китайский (упрощенное письмо) языке переводы.
* **AppResources.zh Hant.resx** — китайский (традиционный) переводах.
* **AppResources.pt.resx** -переводы португальский язык.
* **AppResources.pt BR.resx** -переводы Бразильский Португальский язык.

Общий шаблон — использовать коды языков двух букв, но есть несколько примеров (например, китайский), в которых используется другой формат и другие примеры (например, португальском (Бразилия)) где требуется четырехзначный код языка.

Эти файлы языковых ресурсов *не* требуют **. designer.cs** частичного класса, поэтому они могут быть добавлены как обычные файлы XML с **действие при построении: EmbeddedResource**значение. На этом снимке экрана показан пример решения, содержащая файлы языковых ресурсов:

![](text-images/appresources-langs.png "Файлы ресурсов для конкретного языка")

Приложение разработано и базовый RESX-файла был добавлен текст, вы должны отправить ее переводчикам, преобразует каждый `data` элемент и возврат файла языковых ресурсов (с помощью показано соглашение об именовании) для включения в приложении. Ниже приведены некоторые примеры машинным переводом:

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

Только `value` элемент должен быть обновлен транслятором - `comment` не предназначен для перевода. Помните: при редактировании XML-файлы для экранирования зарезервированные знаки, такие как `<`, `>`, `&` с `&lt;`, `&gt;`, и `&amp;` появлялись в `value` или `comment`.

<a name="incode" />

### <a name="using-resources-in-code"></a>Использование ресурсов в коде

Строки в файлах ресурсов RESX, будут доступны для использования кода интерфейса пользователя с помощью `AppResources` класса. `name` Назначены каждой строки в RESX файл становится свойством для этого класса, который можно ссылаться в коде Xamarin.Forms, как показано ниже:

```csharp
var myLabel = new Label ();
var myEntry = new Entry ();
var myButton = new Button ();
// populate UI with translated text values from resources
myLabel.Text = AppResources.NotesLabel;
myEntry.Placeholder = AppResources.NotesPlaceholder;
myButton.Text = AppResources.AddButton;
```

Пользовательский интерфейс в iOS, Android и универсальной платформы Windows (UWP), подготавливает к просмотру, как ожидалось, только теперь это возможно для перевода приложения на несколько языков, так как текст, загружается из ресурса, а не жестко. Ниже приведен снимок экрана пользовательского интерфейса на каждой платформе до перевода.

![](text-images/simple-example-english.png "Пользовательские интерфейсы кросс платформенных до перевода")

### <a name="troubleshooting"></a>Устранение неполадок

#### <a name="testing-a-specific-language"></a>Тестирование конкретного языка

Он может оказаться непростой переключиться симулятор или устройство для разных языков, особенно во время разработки, когда необходимо быстро протестировать разных языков и региональных параметров.

Вы можете принудительно конкретного языка, который будет загружен, задав `Culture` как показано в этом фрагменте кода:

```csharp
// force a specific culture, useful for quick testing
AppResources.Culture =  new CultureInfo("fr-FR");
```

Этот подход — задать культуру непосредственно на `AppResources` класса — также можно использовать для реализации Выбор языка внутри приложения (а не с помощью региональными настройками устройства).

#### <a name="loading-embedded-resources"></a>Загрузка встроенных ресурсов

В следующем фрагменте кода полезно при отладке проблем с внедренных ресурсов (таких как RESX-файлы). Добавьте следующий код в приложение (раньше в жизненном цикле приложений) и будут перечислены все ресурсы, внедренных в сборку, показывающий полный идентификатор ресурсов:

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

В **AppResources.Designer.cs** файл (вокруг *строка 33*), полный *имя диспетчера ресурсов* указывается (например) `"UsingResxLocalization.Resx.AppResources"`) аналогично приведенному ниже коду:

```csharp
System.Resources.ResourceManager temp =
        new System.Resources.ResourceManager(
                "UsingResxLocalization.Resx.AppResources",
                typeof(AppResources).GetTypeInfo().Assembly);
```

Проверьте **выходные данные приложения** результатов отладки кода, показанного выше, чтобы подтвердить соответствующие ресурсы перечислены (т. е. `"UsingResxLocalization.Resx.AppResources"`).

В противном случае — `AppResources` класса не сможет загрузить его ресурсы.
Проверьте следующую команду, чтобы устранить проблемы, когда не удается найти ресурсы:

* Пространство имен по умолчанию для проекта совпадает с корневым пространством имен для **AppResources.Designer.cs** файла.
* Если **AppResources.resx** файл находится в подкаталоге, имя вложенного каталога должны быть частью пространства имен *и* часть идентификатора ресурса.
* **AppResources.resx** файл имеет **действие при построении: EmbeddedResource**.
* **Параметры проекта > код > политики именования .NET > имена ресурсов используйте Visual Studio в стиле** установлен. Это можно untick, если вы предпочитаете, но пространства имен, используемые при указании ссылки на ресурсы RESX потребуется обновить в приложении.

#### <a name="doesnt-work-in-debug-mode-android-only"></a>Не работает в режиме отладки (Android)

Если переведенные строки при работе в сборках выпуска Android, но не во время отладки, щелкните правой кнопкой мыши **проект Android** и выберите **параметры > Создать > сборка Android** и убедитесь, что **Быстрое развертывание сборок** не установлен. Этот параметр вызывает проблемы с загрузкой ресурсов, а не следует использовать при тестировании локализованных приложений.

### <a name="displaying-the-correct-language"></a>Отображение нужный язык

Пока мы изучали писать код таким образом, чтобы переводы можно предусмотреть, но не делают они отображались. Код Xamarin.Forms можно воспользоваться преимуществами. NET ресурсы для загрузки переводы правильный язык, но нам нужно запросить операционную систему на каждой платформе, чтобы определить, какой язык, который пользователь выбрал.

Так как некоторые специфические для платформы код необходим для получения предпочитаемый язык пользователя, используйте [зависимостей службы](~/xamarin-forms/app-fundamentals/dependency-service/index.md) предоставлять эту информацию в приложении Xamarin.Forms и его реализации для каждой платформы.

Во-первых Определите интерфейс, делающий пользователя предпочтительный язык и региональные параметры, аналогичные приведенным ниже кодом:

```csharp
public interface ILocalize
{
    CultureInfo GetCurrentCultureInfo ();
    void SetLocale (CultureInfo ci);
}
```

Во-вторых, использовать [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) в Xamarin.Forms `App` класса для вызова интерфейса и присвоить правильное значение нашей культуры ресурсов RESX. Обратите внимание на то, что нам не нужно вручную задать это значение для универсальной платформы Windows, так как платформа ресурсы автоматически распознает выбранного языка на этих платформах.

```csharp
if (Device.RuntimePlatform == Device.iOS || Device.RuntimePlatform == Device.Android)
{
    var ci = DependencyService.Get<ILocalize>().GetCurrentCultureInfo();
    Resx.AppResources.Culture = ci; // set the RESX for resource localization
    DependencyService.Get<ILocalize>().SetLocale(ci); // set the Thread for locale-aware methods
}
```

Ресурс `Culture` необходимо задать при первой загрузке приложения, чтобы использовалась строк правильном языке. При необходимости может обновлять это значение в соответствии с событиями конкретных платформ, которые могут возникать в iOS или Android, если пользователь обновляет свои языковые параметры во время работы приложения.

Реализации `ILocalize` интерфейса отображаются в [кода для конкретных платформ](#Platform-specific_Code) разделе ниже. Эти реализации используют это преимущество `PlatformCulture` вспомогательный класс:

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

Код, чтобы определить, какой язык будет отображаться должно быть специфические для платформы, так как iOS, Android и универсальной платформы Windows предоставлять эту информацию различными способами. Код для `ILocalize` зависимостей службы предоставляется ниже для каждой платформы, а также дополнительные требования платформы, чтобы обеспечить локализованный текст отображается правильно.

Код платформы также требуется обрабатывать случаи, когда операционная система позволяет пользователю настроить идентификатор языкового стандарта, который не поддерживается. NET `CultureInfo` класса. В таких случаях пользовательский код должен быть написан обнаруживать неподдерживаемых языковых стандартов и заменить лучшее. Языковой стандарт, совместимые с NET.

#### <a name="ios-application-project"></a>Проект приложения iOS

iOS пользователям выбрать предпочтительный язык отдельно от языка и региональных параметров форматирования даты и времени. Загрузка правильный ресурсов для локализации приложения Xamarin.Forms, нам нужно запросить `NSLocale.PreferredLanguages` массив для первого элемента.

В следующей реализации `ILocalize` зависимостей службы должен быть помещен в проект приложения iOS. Поскольку iOS использует символы подчеркивания, а не штрихов (это стандартное представление .NET) код заменяет символ подчеркивания перед созданием экземпляра `CultureInfo` класса:

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
> `try/catch` Блоков в `GetCurrentCultureInfo` метода имитируют поведение обычно используется с описателями языковой стандарт — Если точное соответствие не найден, поиск для близкое соответствие только на основе языка (первый блок символов в языковом стандарте).
>
> В случае Xamarin.Forms, некоторые языковые стандарты являются допустимыми в iOS, но не соответствуют допустимому `CultureInfo` .NET и приведенный выше пытается удовлетворить это.
>
> Например, iOS **параметры > Общие языка &amp; регион** экран позволяет установить ваш телефон **языка** для **Английский** , но  **Регион** для **Испания**, что приводит к строке языкового стандарта из `"en-ES"`. Когда `CultureInfo` Создание завершается с ошибкой, код возвращается к использованию только первые две буквы для выбора отображаемого языка.
>
> Разработчики должны изменить `iOSToDotnetLanguage` и `ToDotnetFallbackLanguage` методов для обработки конкретных вариантов, необходимых для их поддерживаемых языков.


Некоторые элементы системный пользовательского интерфейса, которые автоматически преобразуются в iOS, такие как **сделать** кнопку `Picker` элемента управления. Чтобы принудительно iOS для преобразования этих элементов, нам нужно указать, какие языки поддерживаются в **Info.plist** файла. Вы можете добавить эти значения с помощью **Info.plist > источник** следующим образом:

![Локализация ключи в файле Info.plist](text-images/info-plist.png "локализации ключи в файле Info.plist.")

Кроме того, откройте **Info.plist** файл в редакторе XML и измените значения напрямую:

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

После реализации службы зависимостей и обновить **Info.plist**, приложение для iOS будут иметь возможность показывать локализованный текст.

> [!NOTE]
> Обратите внимание, что Apple рассматривает португальский немного иначе, чем вы ожидаете.
> Из [свои документы](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW2): _«использовать pt как идентификатор языка для португальского языка так как он используется в Бразилия и Португалия как идентификатор языка для португальского языка так как он используется в Португалии»_.
> Это означает, что если португальский язык выбран в нестандартные языковом стандарте, резервном языке будет португальский (Бразилия) на iOS, если код написан так, чтобы изменить это поведение (например, `ToDotnetFallbackLanguage` выше).

Дополнительные сведения о локализации iOS, см. в разделе [iOS локализации](~/ios/app-fundamentals/localization/index.md).

#### <a name="android-application-project"></a>Проект приложения Android

Android предоставляет текущего выбранного языкового стандарта с помощью `Java.Util.Locale.Default`, а также использует разделителя символ подчеркивания вместо тире (который заменяется в следующем примере кода). Добавьте в проект приложения Android Эта реализация службы зависимостей:

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
> `try/catch` Блоков в `GetCurrentCultureInfo` метода имитируют поведение обычно используется с описателями языковой стандарт — Если точное соответствие не найден, поиск для близкое соответствие только на основе языка (первый блок символов в языковом стандарте).
>
> В случае Xamarin.Forms, некоторые языковые стандарты являются допустимыми в Android, но не соответствуют допустимому `CultureInfo` .NET и приведенный выше пытается удовлетворить это.
>
> Разработчики должны изменить `iOSToDotnetLanguage` и `ToDotnetFallbackLanguage` методов для обработки конкретных вариантов, необходимых для их поддерживаемых языков.

После этот код был добавлен в проект приложения Android, он будет автоматически отображать переведенных строк.

> [!NOTE]
>️ **предупреждение:** переведенные строки при работе в сборках выпуска Android, но не во время отладки, щелкните правой кнопкой мыши на **проект Android** и выберите **параметры > Создать > Android Построение** и убедитесь, что **быстрое развертывание сборок** не установлен. Этот параметр вызывает проблемы с загрузкой ресурсов, а не следует использовать при тестировании локализованных приложений.

Дополнительные сведения о локализации Android, см. в разделе [Android локализации](~/android/app-fundamentals/localization.md).

#### <a name="universal-windows-platform"></a>Универсальная платформа Windows 

Проекты универсальной платформы Windows (UWP) не требуют службы зависимостей. Вместо этого эта платформа автоматически задает язык и региональные параметры ресурсов правильно.

##### <a name="assemblyinfocs"></a>AssemblyInfo.cs

Разверните узел свойств в проекте библиотеки .NET Standard и дважды щелкните **AssemblyInfo.cs** файл. Добавьте следующую строку в файл, чтобы установить английский язык ассемблера нейтральных ресурсов:

```csharp
[assembly: NeutralResourcesLanguage("en")]
```

Это значение сообщает диспетчеру ресурсов языка и региональных параметров по умолчанию приложения, поэтому гарантий, что строки, определенные в RESX-файла нейтрального языка (**AppResources.resx**) отображается, если приложение выполняется в одном языковых стандартов на английском языке.

### <a name="example"></a>Пример

После обновления платформы проектов как показано выше и перекомпилировать приложение с переведенные файлы RESX, обновленные переводы будут доступны в каждом приложении. Ниже приведен снимок экрана из образца кода, преобразуется в упрощенного китайского языка.

![](text-images/simple-example-hans.png "Кросс платформенных пользовательских интерфейсов, преобразуются в китайский — упрощенное письмо")

Дополнительные сведения о локализации универсальной платформы Windows, см. в разделе [UWP локализации](/windows/uwp/design/globalizing/globalizing-portal/).

## <a name="localizing-xaml"></a>Локализация XAML

Когда создание пользовательского интерфейса Xamarin.Forms в XAML, разметка будет выглядеть примерно следующим образом, со строками, внедренными непосредственно в XML-код:

```xaml
<Label Text="Notes:" />
<Entry Placeholder="eg. buy milk" />
<Button Text="Add to list" />
```

В идеальном случае выполняется перевод элементов управления пользовательского интерфейса напрямую в XAML, который мы можно сделать, создав *расширение разметки*. Ниже приведен код для расширения разметки, предоставляющей ресурсов RESX в XAML. Этот класс следует добавить общий код Xamarin.Forms (вместе со страницы XAML):

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

Следующие маркеры объясняются важные элементы в приведенном выше коде:

* Класс назван `TranslateExtension`, но по соглашению, мы можем обратиться к — как **Translate** в разметку.
* Этот класс реализует `IMarkupExtension`, которая необходима для Xamarin.Forms для его работы.
* `"UsingResxLocalization.Resx.AppResources"` — Это идентификатор ресурса для ресурсов RESX. Он состоит из наших пространство имен по умолчанию, папку, где находятся файлы ресурсов и имя файла RESX по умолчанию.
* `ResourceManager` Класс создается с помощью `IntrospectionExtensions.GetTypeInfo(typeof(TranslateExtension)).Assembly)` для определения текущей сборки для загрузки ресурсов и кэшируются в статическом `ResMgr` поля. Она создается как `Lazy` так, чтобы его создание откладывается до первого использования в `ProvideValue` метод.
* `ci` использует службу зависимости для получения выбранного языка пользователя из собственную операционную систему.
* `GetString` Представляет метод, который получает фактический переведенных строк из файлов ресурсов. На универсальной платформе Windows `ci` будет иметь значение null, так как `ILocalize` интерфейс не реализован на этих платформах. Это эквивалентно вызову `GetString` только первый параметр метода. Вместо этого ресурсы платформы автоматически распознает языкового стандарта и извлечет переведенных строк из соответствующего файла RESX.
* Обработка ошибок был включен для отладки отсутствующих ресурсов путем создания исключения (в `DEBUG` только в режиме).

В следующем фрагменте XAML показано, как использовать расширение разметки. Чтобы это работало из двух этапов:

1. Объявить пользовательский `xmlns:i18n` пространства имен в корневом узле. `namespace` И `assembly` должны соответствовать параметрам проекта точно - в этом примере они идентичны, но может отличаться в проекте.
2. Используйте `{Binding}` синтаксис атрибутов, которые обычно должен содержать текст для вызова `Translate` расширение разметки. Ключ ресурса является единственным обязательным параметром.

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

Следующие более многословный синтаксис, также является допустимым для расширения разметки:

```xaml
<Button Text="{i18n:TranslateExtension Text=AddButton}" />
```

## <a name="localizing-platform-specific-elements"></a>Локализация элементов платформы

Несмотря на то, что мы сможем перевод пользовательского интерфейса в код Xamarin.Forms, существуют некоторые элементы, которые необходимо выполнить в каждом проекте под конкретную платформу. В этом разделе мы рассмотрим, как для локализации:

* Application Name
* Изображений

Пример проекта содержит локализованные образ с именем **flag.png**, который есть ссылка в C# следующим образом:

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

Все платформы автоматически обрабатывает ссылки на изображения, подобные до локализованных версий образов, до тех пор, пока реализуются структуры проекта, описанный ниже.

### <a name="ios-application-project"></a>Проект приложения iOS

iOS использует стандарт именования (проекты локализации) или **.lproj** каталогов для хранения образа и строковые ресурсы. Эти каталоги можно содержат локализованные версии изображений, используемых в приложении, а также **InfoPlist.strings** файл, который можно использовать для локализации имя приложения. Дополнительные сведения о локализации iOS, см. в разделе [iOS локализации](~/ios/app-fundamentals/localization/index.md).

#### <a name="images"></a>Изображений

На этом снимке экрана показан пример приложения iOS с помощью конкретного языка **.lproj** каталоги. Испанский каталог называется **es.lproj**, содержит локализованные версии изображения по умолчанию, а также **flag.png**:

![](text-images/ios-resources.png "iOS каталогов проекта по локализации")

Каждый каталог языка содержит копию **flag.png**, локализованных для этого языка. Если изображение не предоставляется, операционной системы по умолчанию к изображению в каталоге язык по умолчанию. Для полной поддержки Retina, необходимо предоставить **@2x** и **@3x** копий каждого изображения.

#### <a name="app-name"></a>Имя приложения

Содержание **InfoPlist.strings** является только одного значения ключа для настройки имени приложения:

```csharp
"CFBundleDisplayName" = "ResxEspañol";
```

При запуске приложения, имя приложения и изображение оба локализованы:

![](text-images/ios-imageicon.png "iOS образец приложения текста и изображения локализации")

### <a name="android-application-project"></a>Проект приложения Android

Android соблюдает другую схему для хранения локализованных изображений с помощью разных **drawable** и **строки** каталоги с суффиксом кода языка. Когда код языкового стандарта четырехбуквенный необходим (например, zh-TW или pt-BR), обратите внимание, что для Android требуется дополнительная **r** следующие dash/предыдущий языковой стандарт кода (например) zh-rTW или pt rBR). Дополнительные сведения о локализации Android, см. в разделе [Android локализации](~/android/app-fundamentals/localization.md).

#### <a name="images"></a>Изображений

На этом снимке экрана показано Android пример с некоторыми локализованных строк и прорисовываемых ресурсов:

![](text-images/android-resources.png "Android локализованной прорисовываемых ресурсов и строка каталоги")

Обратите внимание, что Android не использует zh-Hans и zh-Hant коды для упрощенное и традиционное письмо; Вместо этого он поддерживает только коды страны zh-CN и zh-TW.

Для поддержки изображений другое разрешение для экранов с высокой плотностью, создание дополнительных языковых папок с `-*dpi` суффиксы, такие как **прорисовываемых ресурсов — es-mdpi**, **прорисовываемых ресурсов — es-xdpi**, **прорисовываемых ресурсов — es-xxdpi**и т. д. См. в разделе [ресурсы Android предоставляя альтернативы](http://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources) Дополнительные сведения.

#### <a name="app-name"></a>Имя приложения

Содержание **strings.xml** является только одного значения ключа для настройки имени приложения:

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

Приложение теперь есть перевод имени приложения и изображение. Ниже приведен снимок экрана результата (на испанском языке).

![](text-images/android-imageicon.png "Приложение Android образец текста и изображения локализации")

### <a name="universal-windows-platform-application-projects"></a>Проекты приложений Windows универсальной платформы

Универсальная платформа Windows обладает необходимой инфраструктуры ресурсов, которая упрощает локализацию образов и имя приложения. Дополнительные сведения о локализации универсальной платформы Windows, см. в разделе [UWP локализации](/windows/uwp/design/globalizing/globalizing-portal/).

#### <a name="images"></a>Изображений

Образы можно локализовать, поместив их в папке для определенного ресурса, как показано на следующем снимке экрана:

![](text-images/uwp-image-folder-structure.png "Структура папок локализации образ универсальной платформы Windows")

Во время выполнения инфраструктура ресурсов Windows выберет подходящий образ на основе языкового стандарта пользователя.

## <a name="summary"></a>Сводка

Приложения Xamarin.Forms можно было локализовать с помощью файлов RESX и классами глобализации .NET. Помимо небольшой объем кода для конкретных платформ для обнаружения предпочитаемого пользователем языка большая часть усилий локализации сосредоточена в общий код.

Образы обычно обрабатываются платформой, как воспользоваться преимуществами различным разрешением, предоставляемый в iOS и Android.

## <a name="related-links"></a>Связанные ссылки

- [Пример локализации RESX](https://developer.xamarin.com/samples/xamarin-forms/UsingResxLocalization/)
- [TodoLocalized примера приложения](https://developer.xamarin.com/samples/xamarin-forms/TodoLocalized/)
- [Кросс платформенных локализации](~/cross-platform/app-fundamentals/localization.md)
- [iOS локализации](~/ios/app-fundamentals/localization/index.md)
- [Android локализации](~/android/app-fundamentals/localization.md)
- [Локализация универсальной платформы Windows](/windows/uwp/design/globalizing/globalizing-portal/)
- [С помощью класса CultureInfo (MSDN)](http://msdn.microsoft.com/library/87k6sx8t%28v=vs.90%29.aspx)
- [Обнаружение и использование ресурсов для определенного языка и региональных параметров (MSDN)](http://msdn.microsoft.com/library/s9ckwb4b%28v=vs.90%29.aspx)
