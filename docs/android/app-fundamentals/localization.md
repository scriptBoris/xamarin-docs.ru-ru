---
title: Android локализации
description: В этом документе рассматриваются возможности локализации пакета SDK для Android и способах доступа к ним с помощью Xamarin.
ms.prod: xamarin
ms.assetid: D1277939-A1E8-468E-B136-820D816AF853
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 076cadd16c3953ee4e06193190b59035ad57f2c1
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33798609"
---
# <a name="android-localization"></a>Android локализации

_В этом документе рассматриваются возможности локализации пакета SDK для Android и способах доступа к ним с помощью Xamarin._

## <a name="android-platform-features"></a>Возможности на платформе Android

Этот раздел описывает функции основной локализации Android. Перейдите к [разделу](#basics) определенный код и примеры.

### <a name="locale"></a>Языковой стандарт

Пользователям выбрать язык в **параметры > язык & ввода**. Этот параметр определяет, отображается язык и региональные параметры, используемые (например) для даты и форматирование чисел).

Можно запросить через контекст текущего языкового стандарта текущего `Resources`:

```csharp
var lang = Resources.Configuration.Locale; // eg. "es_ES"
```

Это значение будет идентификатор языкового стандарта, который содержит код языка и код языкового стандарта, разделенных символом подчеркивания. Справочник, вот [список языковых стандартов Java](http://www.oracle.com/technetwork/java/javase/locales-137662.html) и [Android поддерживается язык и региональные стандарты через StackOverflow](http://stackoverflow.com/questions/7973023/what-is-the-list-of-supported-languages-locales-on-android).

Вот наиболее распространенные примеры.

* `en_US` для английского языка (США Statees)
* `es_ES` испанский (Испания)
* `ja_JP` для японский (Япония)
* `zh_CN` для китайского языка (Китай)
* `zh_TW` для китайский (Тайвань)
* `pt_PT` для португальского языка (Португалия)
* `pt_BR` для португальского языка (Бразилия)

### <a name="localechanged"></a>LOCALE_CHANGED

Android приводит к возникновению ошибки `android.intent.action.LOCALE_CHANGED` когда пользователь изменяет их выбор языка.

Действия можно выбрать дескриптор, задав `android:configChanges` атрибут действия следующим образом:

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
        ConfigurationChanges = ConfigChanges.Locale | ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

<a name="basics" />

## <a name="internationalization-basics-in-android"></a>Основные сведения о локализации в Android

Стратегия локализации Android имеет следующие ключевые элементы.

- Папки ресурсов, которые содержат локализованные строки, изображения и другие ресурсы.

- `GetText` метод, который используется для извлечения локализованных строк в коде

- `@string/id` в файлах AXML автоматически разместить локализованные строки в макете.

### <a name="resource-folders"></a>Папки ресурсов

Приложения Android управлять большая часть содержимого в папках ресурсов, таких как:

* **макет** -файлами AXML макета.
* **drawable** -содержит изображения и другие ресурсы drawable.
* **значения** -содержит строки.
* **Необработанный** -файлами данных.

Большинство разработчиков уже знакомы с использованием **dpi** суффиксами на **drawable** directory, чтобы ввести несколько версий образа, позволяя выбрать правильную версию для каждого устройства Android. Тот же механизм используется для предоставления нескольких языковых переводах формируемый каталогов ресурсов с помощью идентификаторов языка и региональных параметров.

![Снимок экрана папок drawable/ресурсы и ресурсы или значений для нескольких идентификаторов региональные](localization-images/resources.png)

> [!NOTE]
> При указании верхнего уровня языка, например `es` только два знака являются обязательными; Однако при указании полного языкового стандарта, формат имени каталога требует тире и строчные **r** для разделения двух частей, например **pt rBR** или **zh-rCN**. Сравните это значение, возвращаемое в коде, который содержит символ подчеркивания (например) `pt_BR`). Оба эти отличаются в .NET значение `CultureInfo` класс использует, имеющий тире только (например) `pt-BR`). Сохраните эти различия в виду при работе на платформах Xamarin.

#### <a name="stringsxml-file-format"></a>Формат файла strings.XML

Локализованный **значения** каталог (например) **значения es** или **значения pt-rBR**) должен содержать файл с именем **Strings.xml** , содержащий переведенный текст для данного языкового стандарта.

Каждая строка переводимые представляет собой элемент XML с ресурсом, как указан идентификатор `name` атрибут и переведенных строк как значение:

```xml
<string name="app_name">TaskyL10n</string>
```

Следует задавать в соответствии с обычными правилами XML и `name` должно содержать допустимый ресурс Android ID (без пробелов и дефисов). Ниже приведен пример файла строки (на английском языке) по умолчанию для примера:

**values/Strings.xml**

```xml
<resources>
    <string name="app_name">TaskyL10n</string>
    <string name="taskadd">Add Task</string>
    <string name="taskname">Name</string>
    <string name="tasknotes">Notes</string>
    <string name="taskdone">Done</string>
    <string name="taskcancel">Cancel</string>
</resources>
```

Испанский каталог **es значения** содержит файл с тем же именем (**Strings.xml**), содержащего переводы:

**values-es/Strings.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">TaskyLeon</string>
    <string name="taskadd">agregar tarea</string>
    <string name="taskname">Nombre</string>
    <string name="tasknotes">Notas</string>
    <string name="taskdone">Completo</string>
    <string name="taskcancel">Cancelar</string>
</resources>
```

![Снимок экрана из нескольких значений папок, каждая из которых содержит файл Strings.xml](localization-images/values.png)

С помощью установки файлов строки переведенные значения можно ссылаться в макеты и кода.

### <a name="axml-layout-files"></a>Файлы AXML макета

Для локализованных строк в макет файлы следует использовать `@string/id` синтаксиса. Следующий фрагмент кода XML из в образце показано `text` свойства задаются с помощью локализованный ресурс идентификаторы (опустили некоторые другие атрибуты):

```xml
<TextView
    android:id="@+id/NameLabel"
    android:text="@string/taskname"
    ... />
<CheckBox
    android:id="@+id/chkDone"
    android:text="@string/taskdone"
    ... />
```

### <a name="gettext-method"></a>GetText-метод

Для получения переведенных строк в коде, используйте `GetText` метод и передать идентификатор ресурса:

```csharp
var cancelText = Resources.GetText (Resource.String.taskcancel);
```

#### <a name="quantity-strings"></a>Количество строк

Android строковые ресурсы также позволяют создавать *количество строк* которая разрешает трансляторы для обеспечения различным переводам для различных значений, таких как:

* «Имеется слева задач 1».
* «Их две задачи по-прежнему делать.»

(вместо универсального «имеются n задачи слева»).

В **Strings.xml**

```xml
<plurals name="numberOfTasks">
   <!--
      As a developer, you should always supply "one" and "other"
      strings. Your translators will know which strings are actually
      needed for their language.
    -->
   <item quantity="one">There is %d task left.</item>
   <item quantity="other">There are %d tasks still to do.</item>
 </plurals>
```

Для подготовки к просмотру используйте полную строку `GetQuantityString` метод, передавая идентификатор ресурса и отображается значение (которое будет передано дважды). Второй параметр, используемый для определения системой Android *которой* `quantity` строка для использования, третий параметр — это значение фактически заменены в строке (необходимо указать оба значения).

```csharp
var translated = Resources.GetQuantityString (
                    Resource.Plurals.numberOfTasks, taskcount, taskcount);`
```

Допустимые `quantity` переключатели следующие:

* нуль
* one
* два
* несколько
* many
* другие

Они описаны более подробно в [Android docs](http://developer.android.com/guide/topics/resources/string-resource.html#Plurals). Если для данного языка не требуется «дополнительно» обработки, те `quantity` строки будут пропущены (например, на английском языке используются только `one` и `other`; указание `zero` строка не будет действовать, его не следует использовать).

### <a name="images"></a>Изображения

Локализованные образы подчиняются тем же правилам, как файлы строки: все изображения, указанные в приложении должен быть помещен в **drawable** каталоги, так что переход на резервный ресурс.

Изображения для определенного языкового стандарта должен быть помещен в полное drawable папки например **drawable es** или **drawable Япония** (также можно добавлять спецификаторы точек на дюйм).

На этом снимке экрана четыре изображения сохраняются в **drawable** каталога, но только один **flag.png**, с локализованными копии в других каталогах.

![Снимок экрана drawable несколько папок, каждая из которых содержит один или несколько файлов локализованных .png](localization-images/drawable.png)


#### <a name="other-resource-types"></a>Другие типы ресурсов

Можно также предоставить другие виды вместо, зависящие от языка ресурсы, включая макеты, анимации и необработанные файлы. Это означает, можно предоставить макет экрана для одного или нескольких целевых языки, например можно создать макет специально для немецкого языка, позволяющее очень длинные текстовые метки.

Android 4.2 появилась поддержка [справа налево (RTL)](http://android-developers.blogspot.fr/2013/03/native-rtl-support-in-android-42.html) при установке параметра приложения `android:supportsRtl="true"`. Квалификатор ресурсов `"ldrtl"` может быть включено в имя direcory, содержат пользовательские макеты, которые предназначены для отображения справа НАЛЕВО.

Дополнительные сведения о именование ресурсов каталог и базовые посвящены Android документы, относящиеся к [обеспечении дополнительных ресурсов](http://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources).


### <a name="app-name"></a>Имя приложения

Имя приложения можно легко локализовать с помощью `@string/id` в для `MainLauncher` действия:

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
    ConfigurationChanges =  ConfigChanges.Orientation | ConfigChanges.Locale)]
```

### <a name="right-to-left-rtl-languages"></a>Языки справа налево (RTL)

Android 4.2 и более поздних обеспечивает полную поддержку для макетов с направлением письма справа НАЛЕВО, подробно описаны в [Native поддерживает RTL блог](http://android-developers.blogspot.dk/2013/03/native-rtl-support-in-android-42.html).

При использовании Android 4.2 (API уровня 17) и более поздних версиях выравнивание значения могут быть заданы с `start` и `end` вместо `left` и `right` (например `android:paddingStart`). Также существуют новые интерфейсы API, например `LayoutDirection`, `TextDirection`, и `TextAlignment` для создания экранов, адаптировать для модулей чтения справа НАЛЕВО.

На следующем снимке экрана показано [локализованные **Tasky** пример](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) на арабском языке:

[![Снимок экрана Tasky приложение на арабском языке](localization-images/rtl-ar-sml.png)](localization-images/rtl-ar.png#lightbox) 

На следующем снимке экрана показано [локализованные **Tasky** пример](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) на иврите:

[![Снимок экрана Tasky приложения на иврите](localization-images/rtl-he-sml.png)](localization-images/rtl-he.png#lightbox)

Справа НАЛЕВО текст локализуется с помощью **Strings.xml** файлы в так же, как текст слева Направо.

## <a name="testing"></a>Тестирование

Убедитесь, что для тщательного тестирования языковой стандарт по умолчанию. Если не удается загрузить ресурсы по умолчанию для какой-либо причине (т. е. они отсутствуют), произойдет сбой приложения.

### <a name="emulator-testing"></a>Тестирование эмулятора

Ссылаться на Google [тестирование в эмуляторе Android](https://developer.android.com/guide/topics/resources/localization.html#testing) разделе инструкции по установке для определенного языкового стандарта с помощью оболочки ADB эмулятором.

```shell
adb shell setprop persist.sys.locale fr-CA;stop;sleep 5;start
```

### <a name="device-testing"></a>Тестирование устройства

Тестирование на устройстве язык **параметры** приложения.
**Совет:** сделать Примечание значков и расположение элементов меню, чтобы вы можете восстановить языка исходное значение.


## <a name="summary"></a>Сводка

В этой статье описываются основы работы локализация приложений Android с помощью встроенных ресурсов обработки. Сведения об i18n и L10n iOS, Android и приложений на разных платформах (включая Xamarin.Forms) в [это руководство кросс платформенных](~/cross-platform/app-fundamentals/localization.md).



## <a name="related-links"></a>Связанные ссылки

- [Tasky (локализованное в коде) (пример)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Локализация ресурсов Android](http://developer.android.com/guide/topics/resources/localization.html)
- [Общие сведения о локализации](~/cross-platform/app-fundamentals/localization.md)
- [Локализация Xamarin.Forms](~/xamarin-forms/app-fundamentals/localization/index.md)
- [iOS локализации](~/ios/app-fundamentals/localization/index.md)
