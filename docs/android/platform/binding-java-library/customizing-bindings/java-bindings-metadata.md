---
title: Метаданные привязок Java
description: C#код в Xamarin.Android вызывает библиотеки Java через привязки, которые представляют собой механизм, который абстрагирует низкоуровневые детали, которые указаны в Java собственный интерфейс (JNI). Xamarin.Android предоставляет средство, создающее эти привязки. Это средство позволяет контроль разработчика, как привязка создается с помощью метаданных, что позволяет процедур, таких как изменение пространства имен и переименование членов. В этом документе рассматривается, как работает метаданных, перечислены атрибуты, что метаданные поддерживает и способы устранения проблем с привязкой, изменив эти метаданные.
ms.prod: xamarin
ms.assetid: 27CB3C16-33F3-F580-E2C0-968005A7E02E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 858f1e5c0bd2af85b419bb9a1cffb7d484f3f7e4
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113408"
---
# <a name="java-bindings-metadata"></a>Метаданные привязок Java

_C#код в Xamarin.Android вызывает библиотеки Java через привязки, которые представляют собой механизм, который абстрагирует низкоуровневые детали, которые указаны в Java собственный интерфейс (JNI). Xamarin.Android предоставляет средство, создающее эти привязки. Это средство позволяет контроль разработчика, как привязка создается с помощью метаданных, что позволяет процедур, таких как изменение пространства имен и переименование членов. В этом документе рассматривается, как работает метаданных, перечислены атрибуты, что метаданные поддерживает и способы устранения проблем с привязкой, изменив эти метаданные._


## <a name="overview"></a>Обзор

Xamarin.Android **привязка библиотеки Java** пытается автоматизировать большую часть работы, необходимой для привязки существующей библиотеки Android с помощью средства, которую иногда называют _генератор привязок_. Если привязка библиотеки Java, Xamarin.Android будет проверять классов Java и создать список всех пакетов, типов и членов которой необходимо привязать. Этот список интерфейсов API, хранятся в XML-файл, который можно найти в  **\{проекта directory}\obj\Release\api.xml** для **выпуска** сборки и в  **\{проекта Directory}\obj\Debug\api.XML** для **Отладка** сборки.

![Расположение файла api.xml в папке obj/Debug](java-bindings-metadata-images/java-bindings-metadata-01.png)

Будет использовать генератор привязок **api.xml** файл в качестве образца для создания необходимых C# классы-оболочки. Содержимое этого XML-файла является разновидностью Google _проекта Android Open источника_ формат.
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

Во многих случаях человека помощь необходим API Java вы дополнительные «.NET как» и как устранить проблемы, препятствующие сборка привязки компиляцию. Например может потребоваться изменить имена пакетов Java для пространств имен .NET, переименуйте класс или изменить тип возвращаемого значения метода.

Эти изменения не обеспечивается путем изменения **api.xml** напрямую.
Вместо этого изменения записываются в специальные файлы XML, которые предоставляются с помощью шаблона привязка библиотеки Java. При компиляции сборки Xamarin.Android привязки генератор привязок будут подвергаться влиянию этих файлах сопоставления при создании привязки сборки

Файлы сопоставления XML можно найти в **преобразует** папку проекта:

-   **MetaData.xml** &ndash; позволяет вносить изменения для окончательного API, например изменение пространства имен созданную привязку. 

-   **EnumFields.xml** &ndash; содержит сопоставление между Java `int` константы и C# `enums` . 

-   **EnumMethods.xml** &ndash; позволяет изменять параметры методов и возвращаемые типы из Java `int` константы для C# `enums` . 

**MetaData.xml** файл является наиболее импорта этих файлов, как он позволяет общего назначения изменения привязки, такие как:

-   Переименование пространств имен, классы, методы или поля, поэтому они соглашениям .NET. 

-   Удаление пространства имен, классы, методы или поля, которые не требуются. 

-   Перемещение классы в разных пространствах имен. 

-   Добавление дополнительных вспомогательных классов внесение привязки выполните шаблоны .NET framework. 

Позволяет перейти к обсудить **Metadata.xml** более подробно.


## <a name="metadataxml-transform-file"></a>Файл Metadata.XML преобразования

Как мы уже узнали, файл **Metadata.xml** используется генератор привязок для время создания привязки сборки.
Использует формат метаданных [XPath](https://www.w3.org/TR/xpath/) синтаксис и почти идентичен *GAPI Используются метаданные* описано в разделе [GAPI Используются метаданные](http://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata) руководства. Эта реализация является почти полную реализацию XPath 1.0 и таким образом поддерживает элементы в стандартные 1.0. Этот файл — это мощный механизм на основе XPath, чтобы изменить, добавить, скрыть или переместить любого элемента или атрибута в файле API. Все элементы правила в спецификации метаданных включают атрибут path для определения узла, к которым будет применяться правило. Правила применяются в следующем порядке:

* **Добавить узел** &ndash; добавляет дочерний узел в узел, указанный в атрибуте path.
* **attr** &ndash; задает значение атрибута элемента, указанного в атрибуте пути.
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

Далее перечислены некоторые из наиболее часто используемых элементов XPath для API-интерфейса Java.

-   `interface` &ndash; Позволяет найти интерфейса Java. например `/interface[@name='AuthListener']`.

-   `class` &ndash; Используется для поиска класса. например `/class[@name='MapView']`.

-   `method` &ndash; Позволяет найти метод в класс Java или интерфейс. например `/class[@name='MapView']/method[@name='setTitleSource']`.

-   `parameter` &ndash; Определение параметра для метода. Например `/parameter[@name='p0']`



### <a name="adding-types"></a>Добавление типов

`add-node` Сообщает привязки проект Xamarin.Android, чтобы добавить новый класс-оболочку для элемента **api.xml**. Например следующий фрагмент кода будет направлять генератор привязки для создания класса с одним полем и конструктора:

```xml
<add-node path="/api/package[@name='org.alljoyn.bus']">
    <class abstract="false" deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="true" visibility="public" extends="java.lang.Object">
        <constructor deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="false" type="org.alljoyn.bus.AuthListener.AuthRequest" visibility="public" />
        <field name="p0" type="org.alljoyn.bus.AuthListener.Credentials" />
    </class>
</add-node>
```


### <a name="removing-types"></a>Удаление типов

Это можно выполнить настройку генератор привязок Xamarin.Android игнорировать тип Java и его не привязке. Это делается путем добавления `remove-node` XML-элемент, **metadata.xml** файла:

```xml
<remove-node path="/api/package[@name='{package_name}']/class[@name='{name}']" />
```

### <a name="renaming-members"></a>Переименование членов

Переименование членов не может выполняться путем непосредственного редактирования **api.xml** файла, потому что для Xamarin.Android требуется исходные имена собственный интерфейс Java (JNI). Таким образом `//class/@name` невозможно изменить атрибут; в противном случае привязка не будет работать.

Рассмотрим случай, когда необходимо переименовать тип, `android.Manifest`.
Чтобы добиться этого, мы стараемся для непосредственного редактирования **api.xml** и переименуйте класс следующим образом:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="name">NewName</attr>
```

Это приведет к генератор привязок, создать следующие C# код для класса-оболочки:

```csharp
[Register ("android/NewName")]
public class NewName : Java.Lang.Object { ... }
```

Обратите внимание, что класс-оболочку, был переименован в `NewName`, а в исходный тип Java — по прежнему `Manifest`. Он больше не класс привязки Xamarin.Android может получить доступ к любой из методов на `android.Manifest`; класс-оболочку, привязан к несуществующему типа Java.

Чтобы правильно изменить управляемое имя упакованного типа (или метод), бывает необходимо задать `managedName` атрибута, как показано в следующем примере:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="managedName">NewName</attr>
```

<a name="Renaming_EventArg_Wrapper_Classes" />

#### <a name="renaming-eventarg-wrapper-classes"></a>Переименование `EventArg` классы-оболочки

Когда определяет привязки генератор Xamarin.Android `onXXX` метод задания для _тип прослушивателя_, C# событий и `EventArgs` подкласс создается для поддержки .NET flavoured API для Java на базе шаблон прослушивателя. Например рассмотрим следующий класс Java и метод.

```xml
com.someapp.android.mpa.guidance.NavigationManager.on2DSignNextManuever(NextManueverListener listener);
```

Xamarin.Android приведет к удалению префикс `on` из метода задания и вместо этого использовать `2DSignNextManuever` в качестве основы для имени `EventArgs` подкласс. Подкласс будет называться примерно такой результат:

```csharp
NavigationManager.2DSignNextManueverEventArgs
```

Это не является допустимым C# имя класса. Чтобы устранить эту проблему, необходимо использовать привязку автор `argsType` атрибут и предоставить допустимые C# имя `EventArgs` подкласс:
 
```xml
<attr path="/api/package[@name='com.someapp.android.mpa.guidance']/
    interface[@name='NavigationManager.Listener']/
    method[@name='on2DSignNextManeuver']" 
    name="argsType">NavigationManager.TwoDSignNextManueverEventArgs</attr>
```

 

## <a name="supported-attributes"></a>Поддерживаемые атрибуты

В следующих разделах описаны некоторые атрибуты для преобразования API-интерфейсы Java.

### <a name="argstype"></a>argsType

Этот атрибут следует поместить в методов задания для именования `EventArg` подкласс, в котором будет создан для поддержки прослушивателей Java. Это описано более подробно ниже в разделе [переименование классы-оболочки EventArg](#Renaming_EventArg_Wrapper_Classes) далее в этом руководстве.

### <a name="eventname"></a>eventName

Указывает имя для события. Если не указано, она подавляет создание событий.
Это описано более подробно в заголовке раздела [переименование классы-оболочки EventArg](#Renaming_EventArg_Wrapper_Classes).

### <a name="managedname"></a>managedName

Это позволяет изменить имя пакета, класса, метода или параметра. Например изменить имя класса Java `MyClass` для `NewClassName`:

```xml
<attr path="/api/package[@name='com.my.application']/class[@name='MyClass']" 
    name="managedName">NewClassName</attr>
```

В следующем примере показано выражение XPath для переименования метода `java.lang.object.toString` для `Java.Lang.Object.NewManagedName`:

```xml
<attr path="/api/package[@name='java.lang']/class[@name='Object']/method[@name='toString']" 
    name="managedName">NewMethodName</attr>
```

### <a name="managedtype"></a>ManagedType

`managedType` позволяет изменить тип возвращаемого значения метода. В некоторых ситуациях генератор привязок неправильно определяет тип возвращаемого значения метода Java, что приведет к ошибке времени компиляции. Чтобы изменить тип возвращаемого значения метода является одним из возможных решений в этой ситуации.

К примеру, генератор привязок полагает, что метод Java `de.neom.neoreadersdk.resolution.compareTo()` должен возвращать `int`, что приводит к сообщение об ошибке **ошибки CS0535: "DE. Neom.Neoreadersdk.Resolution "не реализует член интерфейса «Java.Lang.IComparable.CompareTo(Java.Lang.Object)»**. В следующем фрагменте показано, как изменить тип возвращаемого значения создаваемого C# метода из `int` для `Java.Lang.Object`: 

```xml
<attr path="/api/package[@name='de.neom.neoreadersdk']/
    class[@name='Resolution']/
    method[@name='compareTo' and count(parameter)=1 and
    parameter[1][@type='de.neom.neoreadersdk.Resolution']]/
    parameter[1]"name="managedType">Java.Lang.Object</attr> 
```

### <a name="managedreturn"></a>managedReturn

Изменяет тип возвращаемого значения метода. Это не приводит к изменению возвращаемого значения атрибута (в качестве изменений для возврата атрибутов можно привести несовместимые изменения в подпись JNI). В следующем примере, возвращаемого типа метода `append` метода изменяется с `SpannableStringBuilder` для `IAppendable` (Помните, что C# не поддерживает ковариантные типы возвращаемого значения):

```xml
<attr path="/api/package[@name='android.text']/
    class[@name='SpannableStringBuilder']/
    method[@name='append']" 
    name="managedReturn">Java.Lang.IAppendable</attr>
```

### <a name="obfuscated"></a>с маскированием

Средства, закрывающие библиотеки Java может помешать генератор привязки Xamarin.Android и его способность создавать C# классы-оболочки. Характеристики классов скрытый включают: * включает в себя имя класса **$**, т. е. **$.class** * имя класса полностью включает в себя символы в нижнем регистре, т. е.  **a.class**

В этом фрагменте приведен пример создания «без скрытый» C# типа:

```xml
<attr path="/api/package[@name='{package_name}']/class[@name='{name}']" 
    name="obfuscated">false</attr>
```

### <a name="propertyname"></a>propertyName

Этот атрибут можно использовать для изменения имени управляемого свойства.

Специализированный вариант использования `propertyName` включает в себя ситуации, где это класс Java имеет только метод получения для поля. В этом случае генератор привязки может потребоваться создать свойство только для записи, то, что не рекомендуется в .NET. В следующем фрагменте показано, как «удаление» свойства .NET, задав `propertyName` пустую строку:

```xml
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='setResourceDescriptor' 
    and count(parameter)=1 
    and parameter[1][@type='java.lang.String']]" 
    name="propertyName"></attr>
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='getResourceDescriptor' 
    and count(parameter)=0]" 
    name="propertyName"></attr>
```

Обратите внимание на то, что методы задания и получения будет создан генератором привязки.

### <a name="sender"></a>отправитель

Указывает, какой параметр метода должен быть `sender` параметр, если метод имеет сопоставления событие. Значение может быть `true` или `false`. Пример:

```xml
<attr path="/api/package[@name='android.app']/
    interface[@name='TimePickerDialog.OnTimeSetListener']/
    method[@name='onTimeSet']/
    parameter[@name='view']" 
    name="sender">true</ attr>
```

### <a name="visibility"></a>видимость

Этот атрибут используется для изменения видимости класса, метода или свойства. Например, может потребоваться повышение уровня `protected` метод Java, так что он соответствующему C# оболочка `public`:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method --> 
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

## <a name="enumfieldsxml-and-enummethodsxml"></a>EnumFields.xml и EnumMethods.xml

Бывают случаи, где библиотеки Android целые константы служат для представления состояний, которые передаются свойства или методы библиотек. Во многих случаях полезно для привязки этих целочисленных констант перечисления в C#. Чтобы упростить это сопоставление, используйте **EnumFields.xml** и **EnumMethods.xml** файлов проекта привязки. 

### <a name="defining-an-enum-using-enumfieldsxml"></a>Определение перечисления с помощью EnumFields.xml

**EnumFields.xml** файл содержит сопоставление между Java `int` константы и C# `enums`. Давайте рассмотрим пример C# создан для набора перечисления `int` константы: 

```xml 
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit">
    <field jni-name="UNIT_SECOND" clr-name="Second" value="0" />
    <field jni-name="UNIT_METER" clr-name="Meter" value="1" />
    <field jni-name="UNIT_MILIWATT_HOURS" clr-name="MilliwattHour" value="2" />
</mapping>
```

Здесь мы предприняли класс Java `SKRealReachSettings` и определенные C# enum вызывается `SKMeasurementUnit` в пространстве имен `Skobbler.Ngx.Map.RealReach`. `field` Записей определяет имя константы Java (пример `UNIT_SECOND`), имя элемента enum (пример `Second`) и целочисленное значение, представленное обеих сущностей (пример `0`). 

### <a name="defining-gettersetter-methods-using-enummethodsxml"></a>Определение методов Getter/Setter, с помощью EnumMethods.xml

**EnumMethods.xml** файл позволяет изменять параметры методов и возвращаемые типы из Java `int` константы для C# `enums`. Другими словами, он сопоставляет считывать и записывать C# перечислений (определенных в **EnumFields.xml** файл) для Java `int` константы `get` и `set` методы.

Учитывая `SKRealReachSettings` перечисления, определенный выше, следующие **EnumMethods.xml** файл будет определять getter/setter для этого перечисления:

```xml
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings">
    <method jni-name="getMeasurementUnit" parameter="return" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
    <method jni-name="setMeasurementUnit" parameter="measurementUnit" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
</mapping>
```

Первый `method` строки карты возвращаемое значение Java `getMeasurementUnit` метод `SKMeasurementUnit` перечисления. Второй `method` строки карты первый параметр `setMeasurementUnit` же перечисление.

Со всеми изменениями, можно использовать следующий код в Xamarin.Android для задания `MeasurementUnit`: 

```csharp
realReachSettings.MeasurementUnit = SKMeasurementUnit.Second;
```


## <a name="summary"></a>Сводка

В этой статье описано, как Xamarin.Android использует метаданные для преобразования определения API из *Google* *AOSP формат*. Рассмотрев возможные изменения с помощью *Metadata.xml*, он проверяет ограничения, возникающие при переименовании элементов, и оно представлено в список поддерживаемых атрибутов XML, описывающий, когда следует использовать каждый атрибут.



## <a name="related-links"></a>Связанные ссылки

- [Работа с JNI](~/android/platform/java-integration/working-with-jni.md)
- [Привязка библиотеки Java](~/android/platform/binding-java-library/index.md)
- [GAPI Используются метаданные](http://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
