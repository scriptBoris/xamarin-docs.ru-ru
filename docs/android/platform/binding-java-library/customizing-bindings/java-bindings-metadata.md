---
title: Привязки метаданных Java
description: Кода C# в Xamarin.Android вызывается библиотеки Java с помощью привязки, которые представляют собой механизм, который абстрагирует сведения низкого уровня, которые указаны в Java собственного интерфейса (JNI). Xamarin.Android предоставляет средство, которое приводит к возникновению ошибки эти привязки. Этот инструментарий позволяет контроль разработчика, как привязка создается с помощью метаданных, что позволяет процедур, таких как изменение пространства имен и переименование членов. В этом документе рассматривается, как работает метаданных, перечислены атрибуты метаданных поддерживает и описываются способы решения проблем с привязкой, изменив эти метаданные.
ms.prod: xamarin
ms.assetid: 27CB3C16-33F3-F580-E2C0-968005A7E02E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: 6dea13fcda43cad22b8bea9838bbcb23b97820c7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30771022"
---
# <a name="java-bindings-metadata"></a>Привязки метаданных Java

_Кода C# в Xamarin.Android вызывается библиотеки Java с помощью привязки, которые представляют собой механизм, который абстрагирует сведения низкого уровня, которые указаны в Java собственного интерфейса (JNI). Xamarin.Android предоставляет средство, которое приводит к возникновению ошибки эти привязки. Этот инструментарий позволяет контроль разработчика, как привязка создается с помощью метаданных, что позволяет процедур, таких как изменение пространства имен и переименование членов. В этом документе рассматривается, как работает метаданных, перечислены атрибуты метаданных поддерживает и описываются способы решения проблем с привязкой, изменив эти метаданные._


## <a name="overview"></a>Обзор

Xamarin.Android **библиотеки привязки Java** пытается автоматизировать значительную часть действия, необходимые для привязки существующей библиотеки Android с помощью программы, которую иногда называют _генератор привязки_. При привязке библиотеки Java, Xamarin.Android будет проверять классов Java и создавать список пакетов, типов и членов которой для привязки. Этот список интерфейсов API, хранится в XML-файле, который можно найти на  **\{проекта directory}\obj\Release\api.xml** для **выпуска** сборки и на  **\{проекта Directory}\obj\Debug\api.XML** для **отладки** сборки.

![Расположение файла api.xml в папке obj и отладки](java-bindings-metadata-images/java-bindings-metadata-01.png)

Генератор привязки будет использовать **api.xml** файл в качестве образца для создания необходимых классов оболочки C#. Содержимое этого XML-файла является разновидностью Google _Android откройте исходный проект_ формат.
Ниже приведен пример содержимого **api.xml**:

```xml
<api>
    <package name="android">
        <class abstract="false" deprecated="not deprecated" extends="java.lang.Object"
            extends-generic-aware="java.lang.Object" 
            final="true" 
            name="Manifest" 
            static="false" 
            visibility="public">
            <constructor deprecated="not deprecated" final="false"
                name="Manifest" static="false" type="android.Manifest"
                visibility="public">
            </constructor>
        </class>
...
</api>
```

В этом примере **api.xml** объявляет класс в `android` пакет с именем `Manifest` , расширяющий `java.lang.Object`.

Во многих случаях человека помощника требуется вносить чувствовать себя дополнительные «.NET как» API Java или исправление проблемы, препятствующие сборке привязки компиляцию. Например он может потребоваться изменить имена пакетов Java для пространств имен .NET, переименование класса или изменить тип возврата метода.

Эти изменения не достигается путем изменения **api.xml** напрямую.
Вместо этого изменения записываются в специальных XML-файлы, предоставляемые шаблоном привязки библиотеки Java. При компиляции сборки привязки Xamarin.Android, генератор привязки будет влиять на эти файлы сопоставления при создании привязки сборки

Файлы сопоставления XML, можно найти в **преобразует** папку проекта:

-   **MetaData.xml** &ndash; позволяет производить изменения окончательного API, изменяющиеся в пространство имен созданного привязки. 

-   **EnumFields.xml** &ndash; содержит сопоставление между Java `int` константы и C# `enums` . 

-   **EnumMethods.xml** &ndash; позволяет изменять параметры методов и возвращаемые типы из Java `int` константы в C# `enums` . 

**MetaData.xml** файл является наиболее импорта этих файлов, как он позволяет общего назначения изменения привязки, такие как:

-   Переименование пространств имен, классов, методов или полей, поэтому они соглашениям .NET. 

-   Удаление пространства имен, классы, методы или поля, которые не нужны. 

-   Перемещение классы в разных пространствах имен. 

-   Добавление дополнительных вспомогательных классов внесение привязки выполните .NET framework шаблоны. 

Позволяет перейти к обсудить **Metadata.xml** более подробно.


## <a name="metadataxml-transform-file"></a>Файл Metadata.XML преобразования

Как вы уже узнали, файл **Metadata.xml** используется генератором привязок для оказания влияния на создание привязки сборки.
Использует формат метаданных [XPath](https://www.w3.org/TR/xpath/) синтаксис и почти идентичен *GAPI Используются метаданные* описано в [GAPI Используются метаданные](http://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata) руководства. Эта реализация почти полную реализацию XPath 1.0 и таким образом поддерживает элементов в стандартных 1.0. Этот файл является мощный механизм для изменения, добавить, скрыть или переместить любого элемента или атрибута в файле API на основе XPath. Все элементы правил в спецификации метаданных включают атрибут path для идентификации узла, к которому будет применяться правило. Правила применяются в следующем порядке:

* **Добавить узел** &ndash; добавляет дочерний узел в узел, указанный в атрибуте path.
* **attr** &ndash; устанавливает значение атрибута элемента, указанного в атрибуте path.
* **удалить узел** &ndash; удаляет узлов, соответствующих указанным XPath.

Ниже приведен пример **Metadata.xml** файла:

```xml
<metadata>
    <!-- Normalize the namespace for .NET -->
    <attr path="/api/package[@name='com.evernote.android.job']" 
        name="managedName">Evernote.AndroidJob</attr>

    <!-- Don't  need these packages for the Xamarin binding/public API --> 
    <remove-node path="/api/package[@name='com.evernote.android.job.v14']" />
    <remove-node path="/api/package[@name='com.evernote.android.job.v21']" />

    <!-- Change a parameter name from the generic p0 to a more meaningful one. -->
    <attr path="/api/package[@name='com.evernote.android.job']/class[@name='JobManager']/method[@name='forceApi']/parameter[@name='p0']" 
        name="name">api</attr>
</metadata>
```

Ниже перечислены некоторые из наиболее часто используемых элементов XPath для API-интерфейса Java.

-   `interface` &ndash; Используется для поиска интерфейса Java. например `/interface[@name='AuthListener']`.

-   `class` &ndash; Используется для поиска класса. например `/class[@name='MapView']`.

-   `method` &ndash; Используется для нахождения метода Java-класса или интерфейса. например `/class[@name='MapView']/method[@name='setTitleSource']`.

-   `parameter` &ndash; Определение параметра для метода. Например `/parameter[@name='p0']`



### <a name="adding-types"></a>Добавление типов

`add-node` Сообщает проекта Xamarin.Android привязки, чтобы добавить класс-оболочку для элемента **api.xml**. Например следующий фрагмент кода будет направлять генератора привязки, чтобы создать класс с одним полем и конструктора:

```xml
<add-node path="/api/package[@name='org.alljoyn.bus']">
    <class abstract="false" deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="true" visibility="public" extends="java.lang.Object">
        <constructor deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="false" type="org.alljoyn.bus.AuthListener.AuthRequest" visibility="public" />
        <field name="p0" type="org.alljoyn.bus.AuthListener.Credentials" />
    </class>
</add-node>
```


### <a name="removing-types"></a>Удаление типов

Это можно выполнить настройку привязок генератора Xamarin.Android игнорировать типа Java и не удается привязать. Это делается путем добавления `remove-node` XML-элементе **metadata.xml** файла:

```xml
<remove-node path="/api/package[@name='{package_name}']/class[@name='{name}']" />
```

### <a name="renaming-members"></a>Переименование членов

Переименование членов не может выполняться путем непосредственного редактирования **api.xml** файла, потому что Xamarin.Android требует исходные имена собственного интерфейса Java (JNI). Таким образом `//class/@name` невозможно изменить атрибут; Если это так, привязка не будет работать.

Рассмотрим случай, когда требуется переименовать тип, `android.Manifest`.
Чтобы сделать это, мы повторите непосредственного редактирования **api.xml** и переименуйте класс следующим образом:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="name">NewName</attr>
```

Это приведет к создание следующий код C# для класса-оболочки генератор привязки:

```csharp
[Register ("android/NewName")]
public class NewName : Java.Lang.Object { ... }
```

Обратите внимание, что класс-оболочку был переименован в `NewName`, а в исходный тип Java по-прежнему `Manifest`. Она больше не класс привязки Xamarin.Android может обратиться к любой методам в `android.Manifest`; класс-оболочку привязана к несуществующей типа Java.

Чтобы правильно изменить имя управляемого упакованного типа (или метод), необходимо задать `managedName` атрибута, как показано в следующем примере:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="managedName">NewName</attr>
```

<a name="Renaming_EventArg_Wrapper_Classes" />

#### <a name="renaming-eventarg-wrapper-classes"></a>Переименование `EventArg` классы-оболочки

Когда определяет генератор привязки Xamarin.Android `onXXX` метод задания для _тип прослушивателя_, события C# и `EventArgs` создается подкласс для поддержки .NET flavoured API-Интерфейс для Java-прослушиватель шаблон. Например рассмотрим следующий класс Java и метод.

```xml
com.someapp.android.mpa.guidance.NavigationManager.on2DSignNextManuever(NextManueverListener listener);
```

Префикс приведет к удалению Xamarin.Android `on` из метода задания и воспользуйтесь `2DSignNextManuever` в качестве основы для имени `EventArgs` подкласс. Подкласс будет называться примерно:

```csharp
NavigationManager.2DSignNextManueverEventArgs
```

Не является допустимым именем класса C#. Чтобы устранить эту проблему, необходимо использовать привязку автор `argsType` атрибута и укажите допустимое имя C# для `EventArgs` подкласс:
 
```xml
<attr path="/api/package[@name='com.someapp.android.mpa.guidance']/
    interface[@name='NavigationManager.Listener']/
    method[@name='on2DSignNextManeuver']" 
    name="argsType">NavigationManager.TwoDSignNextManueverEventArgs</attr>
```

 

## <a name="supported-attributes"></a>Поддерживаемые атрибуты

В следующих разделах описаны некоторые атрибуты для преобразования Java API-интерфейсы.

### <a name="argstype"></a>argsType

Этот атрибут следует поместить в методы задания имени `EventArg` подкласс, в котором будет создан для поддержки прослушивателей Java. Это более подробно описан ниже в разделе [переименование классы-оболочки EventArg](#Renaming_EventArg_Wrapper_Classes) далее в этом руководстве.

### <a name="eventname"></a>eventName

Указывает имя для события. Если не указано, он приводит к запрещению создания события.
Это описывается более подробно в заголовке раздела [переименование классы-оболочки EventArg](#Renaming_EventArg_Wrapper_Classes).

### <a name="managedname"></a>managedName

Это позволяет изменить имя пакета, класса, метода или параметра. Например изменить имя класса Java `MyClass` для `NewClassName`:

```xml
<attr path="/api/package[@name='com.my.application']/class[@name='MyClass']" 
    name="managedName">NewClassName</attr>
```

В следующем примере показано выражение XPath для переименования метод `java.lang.object.toString` для `Java.Lang.Object.NewManagedName`:

```xml
<attr path="/api/package[@name='java.lang']/class[@name='Object']/method[@name='toString']" 
    name="managedName">NewMethodName</attr>
```

### <a name="managedtype"></a>managedType

`managedType` позволяет изменить тип возвращаемого значения метода. В некоторых ситуациях генератор привязок неверно определит тип возвращаемого значения метода Java, что приведет к ошибке во время компиляции. Одно из возможных решений в этой ситуации — изменить тип возвращаемого значения метода.

Например, генератор привязок полагает, метод Java `de.neom.neoreadersdk.resolution.compareTo()` должен возвращать `int`, что приводит в сообщении об ошибке **ошибки CS0535: "DE. Neom.Neoreadersdk.Resolution "не реализует член интерфейса «Java.Lang.IComparable.CompareTo(Java.Lang.Object)»**. В следующем фрагменте показано, как изменить тип возврата создаваемого метода C# из `int` для `Java.Lang.Object`: 

```xml
<attr path="/api/package[@name='de.neom.neoreadersdk']/
    class[@name='Resolution']/
    method[@name='compareTo' and count(parameter)=1 and
    parameter[1][@type='de.neom.neoreadersdk.Resolution']]/
    parameter[1]"name="managedType">Java.Lang.Object</attr> 
```

### <a name="managedreturn"></a>managedReturn

Изменяет тип возвращаемого значения метода. Это не приводит к изменению возвращаемый атрибут (как изменений для возврата атрибутов может привести к несовместимые изменения подписи JNI). В следующем примере, возвращаемый тип `append` метод меняется с `SpannableStringBuilder` для `IAppendable` (Помните, что C# не поддерживает ковариантные типы возвращаемого значения):

```xml
<attr path="/api/package[@name='android.text']/
    class[@name='SpannableStringBuilder']/
    method[@name='append']" 
    name="managedReturn">Java.Lang.IAppendable</attr>
```

### <a name="obfuscated"></a>некоторую путаницу

Средства, которые замаскировать библиотеки Java может помешать генератор привязки Xamarin.Android и его способность создавать классы-оболочки C#. Перечислены характеристики неопределенные классы: * имя class содержит **$**, т. е. **$.class** * имя класса полностью скомпрометированы символов нижнего регистра, т. е.  **a.class**

В этом фрагменте приведен пример создания «Отмена скрытый» тип C#:

```xml
<attr path="/api/package[@name='{package_name}']/class[@name='{name}']" 
    name="obfuscated">false</attr>
```

### <a name="propertyname"></a>propertyName

Этот атрибут может использоваться для изменения имени управляемого свойства.

Специализированный вариант использования `propertyName` ситуации, где класс Java имеет только метод считывания для поля включает в себя. В этом случае генератор привязки потребуется создать свойство только для записи, то, что не рекомендуется в .NET. В следующем фрагменте показано, как «удалить» свойств .NET, задав `propertyName` на пустую строку:

```xml
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='setResourceDescriptor' 
    and count(parameter)=1 
    and parameter[1][@type='java.lang.String']]" 
    name="propertyName"></attr>
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='getResourceDescriptor' 
    and count(parameter)=0]" 
    name="propertyName"></attr>
```

Обратите внимание, что методы задания и считывания будет создан генератором привязок.

### <a name="sender"></a>отправитель

Указывает, какой параметр метода должен быть `sender` параметра при сопоставлении метода события. Значение может быть `true` или `false`. Пример:

```xml
<attr path="/api/package[@name='android.app']/
    interface[@name='TimePickerDialog.OnTimeSetListener']/
    method[@name='onTimeSet']/
    parameter[@name='view']" 
    name="sender">true</ attr>
```

### <a name="visibility"></a>видимость

Этот атрибут используется для изменения видимости класса, метода или свойства. Например, может потребоваться повышение уровня `protected` метод Java, чтобы соответствующий оболочки C# является `public`:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method --> 
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

## <a name="enumfieldsxml-and-enummethodsxml"></a>EnumFields.xml и EnumMethods.xml

Существуют случаи, где библиотеки Android целые константы служат для представления состояния, которые передаются к свойствам или методам библиотек. Во многих случаях полезно для привязки этих целочисленных констант перечислений в C#. Для этого сопоставления, используют **EnumFields.xml** и **EnumMethods.xml** файлы в проекте привязки. 

### <a name="defining-an-enum-using-enumfieldsxml"></a>Определение перечисления с помощью EnumFields.xml

**EnumFields.xml** файл содержит сопоставление между Java `int` константы и C# `enums`. Давайте рассмотрим пример перечисления C# создается для набора `int` константы: 

```xml 
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit">
    <field jni-name="UNIT_SECOND" clr-name="Second" value="0" />
    <field jni-name="UNIT_METER" clr-name="Meter" value="1" />
    <field jni-name="UNIT_MILIWATT_HOURS" clr-name="MilliwattHour" value="2" />
</mapping>
```

Здесь мы предприняли класс Java `SKRealReachSettings` и определенные перечисления C# называется `SKMeasurementUnit` в пространстве имен `Skobbler.Ngx.Map.RealReach`. `field` Записей определяет имя константы Java (пример `UNIT_SECOND`), имя элемента перечисления (пример `Second`) и целое значение, представленное обе сущности (пример `0`). 

### <a name="defining-gettersetter-methods-using-enummethodsxml"></a>Определение методов Getter/Setter, с помощью EnumMethods.xml

**EnumMethods.xml** файла позволяет изменять параметры методов и возвращаемые типы из Java `int` константы в C# `enums`. Другими словами, он сопоставляет считывать и записывать перечислений C# (определенные в **EnumFields.xml** файл) для Java `int` константой `get` и `set` методы.

Получает `SKRealReachSettings` перечисления, определенного выше, следующие **EnumMethods.xml** файла будет определять getter/setter для данного перечисления:

```xml
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings">
    <method jni-name="getMeasurementUnit" parameter="return" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
    <method jni-name="setMeasurementUnit" parameter="measurementUnit" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
</mapping>
```

Первый `method` строки карты возвращаемое значение Java `getMeasurementUnit` метод `SKMeasurementUnit` перечисления. Второй `method` строки карты первый параметр `setMeasurementUnit` же перечислением.

Со всеми изменениями, можно использовать следующий код в Xamarin.Android для задания `MeasurementUnit`: 

```csharp
realReachSettings.MeasurementUnit = SKMeasurementUnit.Second;
```


## <a name="summary"></a>Сводка

В этой статье описано, как Xamarin.Android использует метаданные для преобразования определения API из *Google* *AOSP формат*. После обсуждения возможные изменения с помощью *Metadata.xml*, он проверяет ограничения, возникающие при переименовании элементов, и оно представлено в список поддерживаемых атрибутов XML, описывающий, когда каждый атрибут должен использоваться.



## <a name="related-links"></a>Связанные ссылки

- [Работа с JNI](~/android/platform/java-integration/working-with-jni.md)
- [Привязка библиотеки Java](~/android/platform/binding-java-library/index.md)
- [GAPI Metadata](http://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
