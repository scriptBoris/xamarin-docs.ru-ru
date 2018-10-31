---
title: Android локализации
description: В этом документе представлены возможности локализации пакета SDK для Android и как получить доступ к их с помощью Xamarin.
ms.prod: xamarin
ms.assetid: D1277939-A1E8-468E-B136-820D816AF853
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 4f50efb7b78db67b310ccddec3eb94bfe4d2e521
ms.sourcegitcommit: 19b37f33b0eb9a927633a3198574b779374775ff
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50301257"
---
# <a name="android-localization"></a>Android локализации

_В этом документе представлены возможности локализации пакета SDK для Android и как получить доступ к их с помощью Xamarin._

## <a name="android-platform-features"></a>Возможности на платформе Android

В этом разделе описываются возможности локализации основной Android. Перейдите к разделу [разделу](#basics) определенный код и примеры.

### <a name="locale"></a>Языковой стандарт

Пользователям выбрать язык в **параметры > язык & входной**. Этот выбор определяет, отображается язык и региональные параметры, используемые (например) для даты и форматирование чисел).

Текущий языковой стандарт можно запрашивать через контекст текущего `Resources`:

```csharp
var lang = Resources.Configuration.Locale; // eg. "es_ES"
```

Это значение будет идентификатор языкового стандарта, который содержит код языка и код языкового стандарта, отделенный символом подчеркивания. Для справки ниже приведен [список языковых стандартов Java](http://www.oracle.com/technetwork/java/javase/locales-137662.html) и [Android поддерживается языковых стандартов с помощью StackOverflow](http://stackoverflow.com/questions/7973023/what-is-the-list-of-supported-languages-locales-on-android).

Вот наиболее распространенные примеры.

* `en_US` для английского языка (США)
* `es_ES` испанский (Испания)
* `ja_JP` для японского (Япония)
* `zh_CN` для китайского (Китай)
* `zh_TW` для китайского (Тайвань)
* `pt_PT` Португальский (Португалия)
* `pt_BR` Португальский (Бразилия)

### <a name="localechanged"></a>LOCALE_CHANGED

Создает Android `android.intent.action.LOCALE_CHANGED` когда пользователь изменяет их выбор языка.

Действия можно выбрать для обработки это, задав `android:configChanges` атрибут в действии, следующим образом:

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
        ConfigurationChanges = ConfigChanges.Locale | ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

<a name="basics" />

## <a name="internationalization-basics-in-android"></a>Основные сведения о интернационализации в Android

Стратегия локализации Android состоит из следующих ключевых частей:

- Папки ресурсов, которые содержат локализованные строки, изображения и другие ресурсы.

- `GetText` метод, который используется для получения локализованных строк в коде

- `@string/id` в файлах AXML для автоматического размещения локализованных строк в макете.

### <a name="resource-folders"></a>Папки ресурсов

Большая часть содержимого в папки ресурсов управления приложений Android, например:

* **макет** -содержит файлы макета AXML.
* **drawable** -содержит изображения и другие прорисовываемые ресурсы.
* **значения** -содержит строки.
* **необработанные** -файлами данных.

Большинство разработчиков уже знакомы с использованием **dpi** суффиксы на **drawable** directory, чтобы выполнить несколько версий образа, позволяя выбрать правильную версию для каждого устройства Android. Тот же механизм используется для предоставления нескольких язык переводы, добавив к каталогов ресурсов с идентификаторами языка и региональных параметров.

![Снимок экрана drawable/ресурсы и ресурсы и значения, которые папок для нескольких идентификаторов культуры](localization-images/resources.png)

> [!NOTE]
> При указании верхнего уровня языка, например `es` только два знака являются обязательными; Однако при указании полного языкового стандарта, формат имени каталога требуется dash и строчные **r** для разделения двух частей, например **pt rBR** или **zh-rCN**. Сравните это значение, возвращаемое в коде, который содержит символ подчеркивания (например) `pt_BR`). Обе эти функции отличается от значения .NET `CultureInfo` классом, который имеет тире только (например) `pt-BR`). Сохраните эти различия в виду при работе на платформах Xamarin.

#### <a name="stringsxml-file-format"></a>Формат файла strings.XML

Локализованный **значения** каталог (например) **значения es** или **значения pt-rBR**) должен содержать файл с именем **Strings.xml** , содержащий переведенный текст для данного языкового стандарта.

Каждой доступной для перевода строке представляет собой элемент XML с ресурсом, как указан идентификатор `name` атрибут и переведенную строку как значение:

```xml
<string name="app_name">TaskyL10n</string>
```

Необходимо экранировать согласно обычным правилам XML и `name` должен быть допустимым Android идентификатора ресурса (без пробелов и дефисов). Ниже приведен пример файла строки (на английском языке), используемом по умолчанию, например:

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

Испанский directory **значения es** содержит файл с тем же именем (**Strings.xml**), содержащий переводы:

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

![Снимок экрана несколько значений папок, каждая из которых содержит файла Strings.xml](localization-images/values.png)

С помощью файлов настройки строк переведенные значения можно ссылаться в макеты и кода.

### <a name="axml-layout-files"></a>Файлы макета AXML

Для локализованных строк в файлы макета следует использовать `@string/id` синтаксис. Этот фрагмент кода XML из в образце показано `text` свойства задаются с помощью локализованных ресурсов идентификаторы (некоторые другие атрибуты опущены):

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

Android строковых ресурсов также позволяют создавать *количество строк* которого переводчикам предоставляют различным переводам такой фразы для различных значений, таких как:

* «Есть 1 задача влево».
* «Есть 2 задачи еще предстоит сделать.»

(а не универсального «имеются n задач слева»).

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

Для подготовки к просмотру использование полной строки `GetQuantityString` , передавая идентификатор ресурса и значение для отображения (которое будет передано дважды). Второй параметр используется с Android для определения *которой* `quantity` строка для использования, третий параметр является значением, фактически подстановки в строке (необходимо указать оба значения).

```csharp
var translated = Resources.GetQuantityString (
                    Resource.Plurals.numberOfTasks, taskcount, taskcount);`
```

Допустимые `quantity` переключатели следующие:

* нуль
* one
* Два
* несколько
* many
* другие

Они описаны более подробно в [Android документация](http://developer.android.com/guide/topics/resources/string-resource.html#Plurals). Если для данного языка не требуется «дополнительно» обработка, с помощью тех `quantity` строки будут пропущены (например, на английском языке использует только `one` и `other`; каждое следующее указание `zero` строки не окажет никакого воздействия, оно не будет использоваться).

### <a name="images"></a>Изображений

Локализованных изображений следуют тем же правилам, что файлы строки: все образы, на которые ссылается приложение должны находиться в **drawable** каталоги, поэтому переход на резервный ресурс.

Изображения для определенного языкового стандарта должен быть помещен в папки рисунков и полное например **drawable es** или **drawable Япония** (точек на дюйм спецификаторы также могут добавляться).

На этом снимке экрана четыре изображения сохраняются в **drawable** каталога, но только один, **flag.png**, локализованными копии в других каталогах.

![Снимок экрана: drawable несколько папок, каждая из которых содержит один или несколько файлов локализованных .png](localization-images/drawable.png)


#### <a name="other-resource-types"></a>Другие типы ресурсов

Можно также предоставляют другие типы другой вариант — языковые ресурсы, включая макеты, анимации и необработанные файлы. Это означает, что можно предоставить макет экрана для одного или нескольких из ваших целевых языков, например можно создать макет специально для немецкого языка, позволяет очень длинные текстовые метки.

Android 4.2 появилась поддержка [справа налево (RTL)](http://android-developers.blogspot.fr/2013/03/native-rtl-support-in-android-42.html) Если задать параметр приложений `android:supportsRtl="true"`. Квалификатор ресурсов `"ldrtl"` может быть включено в имя direcory должен содержать пользовательские макеты, предназначенные для отображения справа НАЛЕВО.

Дополнительные сведения о resource directory именования и резервных точек, см. документы Android для [обеспечении дополнительных ресурсов](http://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources).


### <a name="app-name"></a>Имя приложения

Имя приложения — легко локализовать с помощью `@string/id` в для `MainLauncher` действия:

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
    ConfigurationChanges =  ConfigChanges.Orientation | ConfigChanges.Locale)]
```

### <a name="right-to-left-rtl-languages"></a>Языки справа налево (RTL)

Android 4.2 и более поздних версий обеспечивает полную поддержку для макетов с направлением письма справа НАЛЕВО, подробно описаны в [поддерживает собственный справа НАЛЕВО блог](http://android-developers.blogspot.dk/2013/03/native-rtl-support-in-android-42.html).

При использовании Android 4.2 (уровень API 17) и более поздней, выравнивание, могут указываться значения `start` и `end` вместо `left` и `right` (например `android:paddingStart`). Также существуют новые интерфейсы API, такие как `LayoutDirection`, `TextDirection`, и `TextAlignment` для создания экранов, адаптировать для модулей чтения справа НАЛЕВО.

На следующем снимке экрана показан [локализованные **Tasky** пример](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) на арабском языке:

[![Снимок экрана: Tasky приложение на арабском языке](localization-images/rtl-ar-sml.png)](localization-images/rtl-ar.png#lightbox) 

На следующем снимке экрана показан [локализованные **Tasky** пример](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) на иврите:

[![Снимок экрана Tasky приложения на иврите](localization-images/rtl-he-sml.png)](localization-images/rtl-he.png#lightbox)

Текст справа НАЛЕВО локализовать с помощью **Strings.xml** файлы в так же, как текст слева Направо.

## <a name="testing"></a>Тестирование

Убедитесь в том, что для тщательного тестирования языкового стандарта по умолчанию. Приложение аварийно, если не удается загрузить ресурсы по умолчанию для какой-либо причине (т. е. они отсутствуют).

### <a name="emulator-testing"></a>Эмулятор тестирования

Ссылаться на Google [тестирование в эмуляторе Android](https://developer.android.com/guide/topics/resources/localization.html#testing) разделе приведены инструкции по настройке для конкретного языкового стандарта с помощью оболочки ADB эмулятором.

```shell
adb shell setprop persist.sys.locale fr-CA;stop;sleep 5;start
```

### <a name="device-testing"></a>Тестирование устройств

Чтобы протестировать на устройстве, изменить язык в **параметры** приложения.
**Совет:** запишите значков и расположение элементов меню, таким образом, вы можете вернуться язык исходное значение.


## <a name="summary"></a>Сводка

В этой статье рассматриваются основные сведения о локализации приложений Android с помощью встроенных ресурсов обработки. Дополнительные сведения о i18n и L10n для iOS, Android и приложения для нескольких платформ (включая Xamarin.Forms) в [в этом руководстве кросс платформенные](~/cross-platform/app-fundamentals/localization.md).



## <a name="related-links"></a>Связанные ссылки

- [Tasky (локализованные в коде) (пример)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Локализация ресурсов Android](http://developer.android.com/guide/topics/resources/localization.html)
- [Общие сведения о локализации](~/cross-platform/app-fundamentals/localization.md)
- [Локализация Xamarin.Forms](~/xamarin-forms/app-fundamentals/localization/index.md)
- [iOS локализации](~/ios/app-fundamentals/localization/index.md)
