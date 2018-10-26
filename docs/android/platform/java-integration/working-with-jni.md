---
title: Работа с JNI
description: Xamarin.Android позволяет написание приложений Android в C# вместо Java. Несколько сборок предоставляются с помощью Xamarin.Android, предоставляющие привязки для библиотеки Java, включая Mono.Android.dll и Mono.Android.GoogleMaps.dll. Тем не менее привязки не доступны для каждого возможных библиотеки Java и привязок, предоставляемых не может выполнить привязку, все типы Java и члены. Чтобы использовать свободные типы Java и члены, может использоваться собственный интерфейс Java (JNI). В этой статье показано, как использовать JNI для взаимодействия с Java типов и членов из приложений Xamarin.Android.
ms.prod: xamarin
ms.assetid: A417DEE9-7B7B-4E35-A79C-284739E3838E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: c674112f629f2054f81d72ee2b71268836e48b7a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106719"
---
# <a name="working-with-jni"></a>Работа с JNI

_Xamarin.Android позволяет написание приложений Android в C# вместо Java. Несколько сборок предоставляются с помощью Xamarin.Android, предоставляющие привязки для библиотеки Java, включая Mono.Android.dll и Mono.Android.GoogleMaps.dll. Тем не менее привязки не доступны для каждого возможных библиотеки Java и привязок, предоставляемых не может выполнить привязку, все типы Java и члены. Чтобы использовать свободные типы Java и члены, может использоваться собственный интерфейс Java (JNI). В этой статье показано, как использовать JNI для взаимодействия с Java типов и членов из приложений Xamarin.Android._


## <a name="overview"></a>Обзор

Это не всегда необходимых или можно создать управляемые вызываемой оболочки (MCW) для вызова кода Java. Во многих случаях «встроенные» JNI вполне приемлемым и полезным для одноразовых использование несвязанного членов Java. Часто бывает проще в использовании JNI для вызова отдельный метод в класс Java, чем Чтобы создать привязку к всей .jar.

Xamarin.Android предоставляет `Mono.Android.dll` сборку, которая обеспечивает привязку для Android `android.jar` библиотеки. Типы и члены не представлять `Mono.Android.dll` и отсутствуют типы в `android.jar` может использоваться, привязывая их вручную. Чтобы привязать Java типов и членов, используйте **Java Native Interface** (**JNI**) для поиска типов, чтения и записи поля, а также для вызова методов.

API JNI в Xamarin.Android аналогичен очень `System.Reflection` API в .NET: она делает доступными для просмотра типов и членов по имени, чтение и запись значений полей и вызывать методы и многое другое. Можно использовать JNI и `Android.Runtime.RegisterAttribute` настраиваемого атрибута для объявления виртуальные методы, которые могут быть привязаны к поддерживает переопределение. Таким образом, они могут быть реализованы в интерфейсы можно привязать C#.

В этом документе описаны:

-  Как JNI ссылается на типы.
-  Как выполнить уточняющий запрос, чтение и запись полей.
-  Как для поиска и вызова методов.
-  Как предоставить виртуальные методы, чтобы разрешить переопределение из управляемого кода.
-  Способ предоставления интерфейсов.



## <a name="requirements"></a>Требования

JNI, предоставляемые [Android.Runtime.JNIEnv имен](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/), доступен в каждой версии Xamarin.Android.
Чтобы привязать Java типы и интерфейсы, необходимо использовать Xamarin.Android версии 4.0 или более поздней версии.


## <a name="managed-callable-wrappers"></a>Управляемые вызываемых оболочек

Объект **управляемых вызываемая оболочка** (**MCW**) является *привязки* для Java класса или интерфейса, который мы завершаем вся начинка JNI таким образом этот клиент C# код не должен беспокоиться о внутренних сложностей аппаратного JNI. Большая часть `Mono.Android.dll` состоит из управляемых вызываемых оболочек.

Управляемые вызываемых оболочек служат двум целям.

1.  Инкапсулируют используйте JNI, таким образом, чтобы клиентский код не нужно знать о внутренних сложностей.
1.  Делают возможным вложенным классом типов Java и реализовывать интерфейсы Java.

Первой задачей является исключительно для удобства и инкапсуляции сложности, таким образом, чтобы пользователи имеют простой и управляемый набор классов для использования. Это требует использования различных [JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/) элементов как описано далее в этой статье. Имейте в виду, что управляемые вызываемых оболочек не являются строго обязательными &ndash; «встроенный», используйте JNI, тогда вполне приемлемым его можно использовать для одноразовых использование несвязанного членов Java. Реализация подкласса и интерфейс требует использования управляемых вызываемых оболочек.



## <a name="android-callable-wrappers"></a>Вызываемые оболочки времени Android

Android вызываемых оболочек (ACW) являются обязательными, каждый раз, когда среда выполнения Android (ГРАФИКА) должна вызывать управляемый код; Эти оболочки необходимы, так как нет способа для регистрации классов с помощью КАРТИНОК во время выполнения.
(В частности, [DefineClass](http://docs.oracle.com/javase/6/docs/technotes/guides/jni/spec/functions.html#wp15986) JNI функция не поддерживается средой выполнения Android. Вызываемые оболочки времени Android таким образом составляют для отсутствия поддержки регистрации типа среды выполнения.)

Когда или выполнить виртуальный метод, который является переопределяемые или реализуемые в управляемом коде интерфейса необходим код для Android, Xamarin.Android необходимо указать прокси-сервер Java, чтобы этот метод возвращает передается соответствующий управляемый тип. Эти типы прокси-сервера Java имеют код Java, которое «одного» базового класса и в списке интерфейсов Java управляемый тип, реализация же конструкторы и объявление переопределенного базового класса и методов интерфейса.

Android вызываемые оболочки создаются **monodroid.exe** программы во время [процесс сборки](~/android/deploy-test/building-apps/build-process.md)и создаются для всех типов, которые (прямо или косвенно) наследовать [ Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/).



### <a name="implementing-interfaces"></a>Реализация интерфейсов

Бывают случаи, когда необходимо реализовать интерфейс Android (такие как [Android.Content.IComponentCallbacks](https://developer.xamarin.com/api/type/Android.Content.IComponentCallbacks/)).

Все Android классы и интерфейсы расширения [Android.Runtime.IJavaObject](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/) интерфейс; таким образом, необходимо реализовать все типы Android `IJavaObject`.
Xamarin.Android использует этот факт &ndash; используется `IJavaObject` для предоставления Android с помощью прокси-сервер Java (Android вызываемую оболочку) для указанного управляемого типа. Так как **monodroid.exe** ищет только `Java.Lang.Object` подклассы (который должен реализовывать `IJavaObject`), работа с подклассами `Java.Lang.Object` предоставляет способ реализации интерфейсов в управляемом коде. Пример:

```csharp
class MyComponentCallbacks : Java.Lang.Object, Android.Content.IComponentCallbacks {
    public void OnConfigurationChanged (Android.Content.Res.Configuration newConfig) {
        // implementation goes here...
    }
    public void OnLowMemory () {
        // implementation goes here...
    }
}
```


### <a name="implementation-details"></a>Сведения о реализации

*В оставшейся части в этой статье приводятся сведения о реализации может быть изменена без предварительного уведомления* (и представленные здесь только в том случае, поскольку разработчики могут весьма интересно, что происходит за кулисами).

Например, даны следующие C# источника:

```csharp
using System;
using Android.App;
using Android.OS;

namespace Mono.Samples.HelloWorld
{
    public class HelloAndroid : Activity
    {
        protected override void OnCreate (Bundle savedInstanceState)
        {
            base.OnCreate (savedInstanceState);
            SetContentView (R.layout.main);
        }
    }
}
```

**Mandroid.exe** программы формирует следующие Android вызываемой оболочки:

```java
package mono.samples.helloWorld;

public class HelloAndroid extends android.app.Activity {
    static final String __md_methods;
    static {
        __md_methods =
            "n_onCreate:(Landroid/os/Bundle;)V:GetOnCreate_Landroid_os_Bundle_Handler\n" +
            "";
        mono.android.Runtime.register (
                "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null",
                HelloAndroid.class,
                __md_methods);
    }

    public HelloAndroid ()
    {
        super ();
        if (getClass () == HelloAndroid.class)
            mono.android.TypeManager.Activate (
                "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null",
                "", this, new java.lang.Object[] { });
    }

    @Override
    public void onCreate (android.os.Bundle p0)
    {
        n_onCreate (p0);
    }

    private native void n_onCreate (android.os.Bundle p0);
}
```

Обратите внимание, что базовый класс сохраняется объявления собственного метода предоставляются для каждого метода, которая переопределяется в рамках управляемого кода.



### <a name="exportattribute-and-exportfieldattribute"></a>ExportAttribute и ExportFieldAttribute

Как правило Xamarin.Android автоматически создает код Java, который состоит из ACW; Это поколение основан на имен классов и методов, если класс является производным от класса Java и переопределяет существующих методов Java. Однако в некоторых сценариях создания кода не подходит, как показано ниже:

-   Android поддерживают действия имена в XML-атрибуты макета, например [android: onClick](https://developer.xamarin.com/api/member/Android.Views.View+IOnClickListener.OnClick/p/Android.Views.View/) XML-атрибута. Если он указан, увеличенную экземпляр представления пытается найти следующий метод Java.

-   [Java.io.Serializable](http://developer.android.com/reference/java/io/Serializable.html) интерфейс требует `readObject` и `writeObject` методы. Так как они не являются членами этого интерфейса, наши соответствующие управляемую реализацию не предоставляет эти методы в код Java.

-   [Android.os.Parcelable](https://developer.xamarin.com/api/type/Android.Os.Parcelable/) интерфейса ожидает, что класс реализации должен иметь статическое поле `CREATOR` типа `Parcelable.Creator`. Созданный код Java требуется некоторые явные поля. С нашей стандартные ситуации нет способа для выходного поля в коде Java из управляемого кода.


Поскольку создание кода не предоставляет решение для создания произвольных методов Java с произвольными именами, начиная с Xamarin.Android версии 4.2, [ExportAttribute](https://developer.xamarin.com/api/type/Java.Interop.ExportAttribute/) и [ExportFieldAttribute](https://developer.xamarin.com/api/type/Java.Interop.ExportFieldAttribute/) были введены предлагают решения для указанных выше сценариев. Оба атрибута находятся в `Java.Interop` пространство имен:

-   `ExportAttribute` &ndash; Задает имя метода и его типы ожидаемого исключения (для того, чтобы предоставить явные «бросает» на языке Java). При использовании метода, метод будет «Экспорт» Java метод, создающий код диспетчеризации для соответствующего вызова JNI для управляемого метода. Это может использоваться с `android:onClick` и `java.io.Serializable`.

-   `ExportFieldAttribute` &ndash; Указывает имя поля. Он располагается на метод, который работает как инициализатор поля. Это может использоваться с `android.os.Parcelable`.

[ExportAttribute](https://developer.xamarin.com/samples/monodroid/ExportAttribute/) пример проекта показано, как использовать эти атрибуты.


#### <a name="troubleshooting-exportattribute-and-exportfieldattribute"></a>Устранение неполадок ExportAttribute и ExportFieldAttribute

-   Упаковка завершается сбоем из-за отсутствия **Mono.Android.Export.dll** &ndash; при использовании `ExportAttribute` или `ExportFieldAttribute` на некоторые методы в коде или зависимые библиотеки, необходимо добавить  **Mono.Android.Export.dll**. Эта сборка является изолированной для поддержки код обратного вызова из Java. Он отделен от **Mono.Android.dll** как добавляет дополнительный объем памяти для приложения.

-   В сборке выпуска `MissingMethodException` происходит для методов экспорта &ndash; в окончательном построении `MissingMethodException` происходит для методов экспорта. (Эта проблема исправлена в последней версии Xamarin.Android).



### <a name="exportparameterattribute"></a>ExportParameterAttribute

`ExportAttribute` и `ExportFieldAttribute` предоставляют функциональные возможности, Java, можно использовать во время выполнения кода. Этот код во время выполнения обращается к управляемого кода через созданные методы JNI, обусловленных этих атрибутов. В результате отсутствует существующий метод Java, привязывает управляемого метода; Таким образом следующий метод Java, созданный Сигнатура управляемого метода.

Тем не менее это не полностью определителя. Прежде всего это верно в некоторые расширенные сопоставления между управляемые типы и типы Java, такие как:

-  InputStream
-  OutputStream
-  XmlPullParser
-  XmlResourceParser

Когда типы, такие как они необходимы для экспортированных методов `ExportParameterAttribute` должна использоваться явным образом предоставить соответствующий параметр или возвращаемое значение типа.



### <a name="annotation-attribute"></a>Атрибут annotation

В Xamarin.Android 4.2, была преобразована `IAnnotation` типов реализации в атрибуты (System.Attribute), а также добавлена поддержка создания заметки в оболочках Java.

Это означает, что следующие изменения направления:

-   Создает генератор привязки `Java.Lang.DeprecatedAttribute` из `java.Lang.Deprecated` (хотя он должен быть `[Obsolete]` в управляемом коде).

-   Это не означает, что существующие `Java.Lang.Deprecated` класса будут упразднены. Эти объекты на основе Java может по-прежнему использоваться как обычные объекты Java (при наличии такого использования). Будет существовать `Deprecated` и `DeprecatedAttribute` классы.

-   `Java.Lang.DeprecatedAttribute` Класс обозначен как `[Annotation]` . При отсутствии настраиваемый атрибут, который наследуется от этого `[Annotation]` атрибут, задача msbuild создаст аннотацию Java для этого настраиваемого атрибута (@Deprecated) в Android вызываемой оболочки (ACW).

-   Заметки могут быть созданы на классы, методы и экспортировать поля (которые — это метод в управляемом коде).

Если для содержащего класса (с заметками сам класс или класс, который содержит элементы, с заметками) не зарегистрирован, всего исходного класса Java не создается, включая заметки. Для методов, можно указать `ExportAttribute` для которого вызывается метод явно создан и снабдить аннотациями. Кроме того он не является компонентом «создать» определение класса заметки Java. Другими словами при определении пользовательского атрибута управляемых определенных заметку в виде, необходимо добавить другую библиотеку JAR-файлов, который содержит соответствующий класс заметки Java. Добавление файла источника Java, который определяет тип заметки не является достаточным. Компилятор Java не работает в так же, как **apt**.

Кроме того применяются следующие ограничения:

-   Процесс преобразования не учитывает `@Target` Аннотация до сих в тип заметки.

-   Атрибуты на свойство не работает. Используйте атрибуты свойства метода получения или задания.



## <a name="class-binding"></a>Класс привязки

Связывание класс означает написание управляемых вызываемую оболочку для упрощения вызова базового типа Java.

Привязка виртуальные и абстрактные методы, чтобы разрешить переопределение из C# требуется Xamarin.Android 4.0. Тем не менее любая версия Xamarin.Android можно привязать виртуальных методов, статические методы или невиртуальные методы без поддержки переопределений.

Обычно привязка содержит следующие элементы:

-  Объект [JNI дескриптор типа Java привязываемого](#_Looking_up_Java_Types).

-  [Поле JNI, идентификаторы и свойства для каждого привязанного поля](#_Instance_Fields).

-  [Идентификаторы метод JNI и методы для каждого привязанного метод](#_Instance_Methods).

-  Если требуется подкласса, тип должен иметь [RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/) настраиваемого атрибута для объявления типа с [RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/) присвоено `true`.



### <a name="declaring-type-handle"></a>Объявляющий тип дескриптора

Поле и метод поиска методы требуют ссылку на объект, ссылающийся на их объявляющий тип. По соглашению, это хранится в `class_ref` поля:

```csharp
static IntPtr class_ref = JNIEnv.FindClass(CLASS);
```

См. в разделе [тип ссылки JNI](#_JNI_Type_References) разделе Дополнительные сведения о `CLASS` токена.


### <a name="binding-fields"></a>Привязка полей

Поля Java доступны в качестве C# свойства, например поле Java [java.lang.System.in](http://developer.android.com/reference/java/lang/System.html#in) привязаны как C# свойство [Java.Lang.JavaSystem.In](https://developer.xamarin.com/api/property/Java.Lang.JavaSystem.In/).
Кроме того поскольку JNI различает статические поля и поля экземпляров, различные методы использовать при реализации свойства.

Поле привязки состоит из трех наборов методов:

1.  *Регистрационный номер плательщика поле* метод. *Регистрационный номер плательщика поле* метод отвечает за возврат дескриптор поля, *получить значение поля* и *задать значение поля* будет использовать методы. Получение идентификатора поля необходимо знать, объявление типа, имя поля и [сигнатура типа JNI](#_JNI_Type_Signatures) поля.

1.  *Получить значение поля* методы. Эти методы требуют дескриптором поля и несете ответственность за считывание Java значение поля.
    Метод, используемый зависит от типа поля.

1.  *Задать значение поля* методы. Эти методы требуют дескриптором поля и отвечают за запись значение поля в Java. Метод, используемый зависит от типа поля.


 [Статические поля](#_Static_Fields) использовать [JNIEnv.GetStaticFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/), `JNIEnv.GetStatic*Field`, и [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/) методы.

 [Поля экземпляров](#_Instance_Fields) использовать [JNIEnv.GetFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFieldID/), `JNIEnv.Get*Field`, и [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/) методы.

Например, статическое свойство `JavaSystem.In` можно реализовать в виде:

```csharp
static IntPtr in_jfieldID;
public static System.IO.Stream In
{
    get {
        if (in_jfieldId == IntPtr.Zero)
            in_jfieldId = JNIEnv.GetStaticFieldID (class_ref, "in", "Ljava/io/InputStream;");
        IntPtr __ret = JNIEnv.GetStaticObjectField (class_ref, in_jfieldId);
        return InputStreamInvoker.FromJniHandle (__ret, JniHandleOwnership.TransferLocalRef);
    }
}
```

Примечание: Мы используем [InputStreamInvoker.FromJniHandle](https://developer.xamarin.com/api/member/Android.Runtime.InputStreamInvoker.FromJniHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership)) для преобразования в ссылки JNI `System.IO.Stream` используется экземпляр и мы `JniHandleOwnership.TransferLocalRef` поскольку [JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/) Возвращает локальную ссылку.

Многие из [Android.Runtime](https://developer.xamarin.com/api/namespace/Android.Runtime/) типы имеют `FromJniHandle` ссылаются на методы, которые преобразует JNI в требуемый тип.



### <a name="method-binding"></a>Метод привязки

Java-методы представляются как C# методы и как C# свойства. Например, следующий метод Java [java.lang.Runtime.runFinalizersOnExit](http://developer.android.com/reference/java/lang/Runtime.html#runFinalizersOnExit(boolean)) метод привязан как [Java.Lang.Runtime.RunFinalizersOnExit](https://developer.xamarin.com/api/member/Java.Lang.Runtime.RunFinalizersOnExit/) метод и [java.lang.Object.getClass ](http://developer.android.com/reference/java/lang/Object.html#getClass) метод привязан как [Java.Lang.Object.Class](https://developer.xamarin.com/api/property/Java.Lang.Object.Class/) свойство.

Вызов метода — это двухэтапный процесс:

1.  *Регистрационный номер плательщика метод* для вызываемого метода. *Регистрационный номер плательщика метод* метод отвечает за возврат дескриптор метода, который будет использовать методы вызова метода. Получить идентификатор метода необходимо знать, объявление типа, имя метода и [сигнатура типа JNI](#_JNI_Type_Signatures) метода.

1.  Вызовите метод.

Как и в случае с полями, используемые методы для получения идентификатора метода и вызвать метод отличается от статических методов и методов экземпляра.

[Статические методы](#_Static_Methods_1) использовать [JNIEnv.GetStaticMethodID()](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/) уточняющего запроса идентификатор метода, и использовать `JNIEnv.CallStatic*Method` семейство методов для вызова.

[Методы экземпляра](#_Instance_Methods) использовать [JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/) уточняющего запроса идентификатор метода, и использовать `JNIEnv.Call*Method` и `JNIEnv.CallNonvirtual*Method` семейств методов для вызова.

Метод привязки является потенциально больше, чем просто вызов метода. Метод привязки также включает метод для переопределения (для методов абстрактные и неконечного) или (для методов интерфейса). [Интерфейсы поддерживают наследование,](#_Supporting_Inheritance,_Interfaces_1) разделе рассматриваются сложности, связанные с поддержкой виртуальных методов и методов интерфейса.

<a name="_Static_Methods_1" />

#### <a name="static-methods"></a>Статические методы

Привязка статический метод предполагает использование `JNIEnv.GetStaticMethodID` для получения дескриптора метода, затем с помощью соответствующего `JNIEnv.CallStatic*Method` метод, в зависимости от типа возвращаемого значения метода. Ниже приведен пример привязки для [Runtime.getRuntime](http://developer.android.com/reference/java/lang/Runtime.html#getRuntime()) метод:

```csharp
static IntPtr id_getRuntime;

[Register ("getRuntime", "()Ljava/lang/Runtime;", "")]
public static Java.Lang.Runtime GetRuntime ()
{
    if (id_getRuntime == IntPtr.Zero)
        id_getRuntime = JNIEnv.GetStaticMethodID (class_ref,
                "getRuntime", "()Ljava/lang/Runtime;");

    return Java.Lang.Object.GetObject<Java.Lang.Runtime> (
            JNIEnv.CallStaticObjectMethod  (class_ref, id_getRuntime),
            JniHandleOwnership.TransferLocalRef);
}
```

Обратите внимание, что мы сохраняем дескриптора метода в статическом поле, `id_getRuntime`. Это для оптимизации производительности, так что дескриптор не нужно искать при каждом вызове. Кэширование дескрипторов метод таким образом необязательно. После получения дескриптора метода [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) используется для вызова метода. `JNIEnv.CallStaticObjectMethod` Возвращает `IntPtr` которого содержит дескриптор возвращаемого экземпляра Java.
[Java.Lang.Object.GetObject&lt;T&gt;(IntPtr, JniHandleOwnership)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) используется для преобразования в экземпляр строго типизированный объект дескриптора Java.



#### <a name="non-virtual-instance-method-binding"></a>Экземпляр невиртуальный метод привязки

Привязка `final` метод экземпляра или метод экземпляра, который не требует переопределения, предполагает использование `JNIEnv.GetMethodID` для получения дескриптора метода, затем с помощью соответствующего `JNIEnv.Call*Method` метод, в зависимости от типа возвращаемого значения метода. Ниже приведен пример привязки для `Object.Class` свойства:

```csharp
static IntPtr id_getClass;
public Java.Lang.Class Class {
    get {
        if (id_getClass == IntPtr.Zero)
            id_getClass = JNIEnv.GetMethodID (class_ref, "getClass", "()Ljava/lang/Class;");
        return Java.Lang.Object.GetObject<Java.Lang.Class> (
                JNIEnv.CallObjectMethod (Handle, id_getClass),
                JniHandleOwnership.TransferLocalRef);
    }
}
```

Обратите внимание, что мы сохраняем дескриптора метода в статическом поле, `id_getClass`.
Это для оптимизации производительности, так что дескриптор не нужно искать при каждом вызове. Кэширование дескрипторов метод таким образом необязательно. После получения дескриптора метода [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) используется для вызова метода. `JNIEnv.CallStaticObjectMethod` Возвращает `IntPtr` которого содержит дескриптор возвращаемого экземпляра Java.
[Java.Lang.Object.GetObject&lt;T&gt;(IntPtr, JniHandleOwnership)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) используется для преобразования в экземпляр строго типизированный объект дескриптора Java.


### <a name="binding-constructors"></a>Конструкторы привязки

Конструкторы — это методы Java с именем `"<init>"`. Как и в Java методы экземпляра, `JNIEnv.GetMethodID` используется для поиска дескриптора конструктора. В отличие от методов Java [JNIEnv.NewObject](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewObject/) методы используются для вызова конструктора дескриптор метода. Возвращаемое значение `JNIEnv.NewObject` является локальной ссылки JNI:


```csharp
int value = 42;
IntPtr class_ref    = JNIEnv.FindClass ("java/lang/Integer");
IntPtr id_ctor_I    = JNIEnv.GetMethodID (class_ref, "<init>", "(I)V");
IntPtr lrefInstance = JNIEnv.NewObject (class_ref, id_ctor_I, new JValue (value));
// Dispose of lrefInstance, class_ref…
```

Обычно класс привязки будет подкласс [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/).
Для подклассов `Java.Lang.Object`, дополнительные семантической вступает в действие: `Java.Lang.Object` экземпляр хранит глобальные ссылки на экземпляр Java через `Java.Lang.Object.Handle` свойство.

1.  `Java.Lang.Object` Конструктор по умолчанию будет выделять экземпляр Java.

1.  Если тип имеет `RegisterAttribute` , и `RegisterAttribute.DoNotGenerateAcw` — `true` , затем экземпляр `RegisterAttribute.Name` типа создается с помощью его конструктора по умолчанию.

1.  В противном случае [Android вызываемая оболочка](~/android/platform/java-integration/android-callable-wrappers.md) (ACW) соответствующий `this.GetType` создается через конструктор по умолчанию. Android вызываемые оболочки создаются во время создания пакета для каждого `Java.Lang.Object` подкласс, для которого `RegisterAttribute.DoNotGenerateAcw` не присвоено значение `true`.

Для типов, которые являются не класса привязки, это ожидаемый семантической: создание экземпляра `Mono.Samples.HelloWorld.HelloAndroid` C# Java необходимо так сконструировать экземпляр `mono.samples.helloworld.HelloAndroid` экземпляра созданного Android вызываемую оболочку.

Для класса привязки это может быть правильное поведение, если типом Java содержит конструктор по умолчанию и/или нет других конструктора необходимо вызвать. В противном случае конструктор должен быть указан которого выполняет следующие действия:

1.  Вызов [Java.Lang.Object (IntPtr, JniHandleOwnership)](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) вместо значения по умолчанию `Java.Lang.Object` конструктор. Это необходимо, чтобы избежать создания экземпляра Java.

1.  Проверьте значение параметра [Java.Lang.Object.Handle](https://developer.xamarin.com/api/property/Java.Lang.Object.Handle/) перед созданием экземпляра любой Java. `Object.Handle` Свойство будет иметь значение, отличное от `IntPtr.Zero` Android обертки было создано в коде Java и класс привязки должен содержать созданный экземпляр вызываемой оболочки времени Android. Например, когда Android создает `mono.samples.helloworld.HelloAndroid` , Android вызываемая оболочка будет создан экземпляр первая и Java `HelloAndroid` конструктор будет создан экземпляр соответствующего `Mono.Samples.HelloWorld.HelloAndroid` тип, с помощью `Object.Handle` , свойство Задайте экземпляру Java до выполнения конструктора.

1.  Если текущий тип среды выполнения не так же, как объявление типа, а затем экземпляр соответствующей вызываемой оболочки Android необходимо создать и использовать [Object.SetHandle](https://developer.xamarin.com/api/member/Java.Lang.Object.SetHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership)) для хранения дескриптор, возвращенный [ JNIEnv.CreateInstance](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CreateInstance/).

1.  Если текущий тип среды выполнения является таким же, как объявляющий тип, затем вызвать конструктор Java и используйте [Object.SetHandle](https://developer.xamarin.com/api/member/Java.Lang.Object.SetHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership)) для хранения дескриптор, возвращенный `JNIEnv.NewInstance` .


Например, рассмотрим [java.lang.Integer(int)](http://developer.android.com/reference/java/lang/Integer.html#Integer(int)) конструктор. Это привязывается как:

```csharp
// Cache the constructor's method handle for later use
static IntPtr id_ctor_I;

// Need [Register] for subclassing
// RegisterAttribute.Name is always ".ctor"
// RegisterAttribute.Signature is tye JNI type signature of constructor
// RegisterAttribute.Connector is ignored; use ""
[Register (".ctor", "(I)V", "")]
public Integer (int value)
    // 1. Prevent Object default constructor execution
    : base (IntPtr.Zero, JniHandleOwnership.DoNotTransfer)
{
    // 2. Don't allocate Java instance if already allocated
    if (Handle != IntPtr.Zero)
        return;

    // 3. Derived type? Create Android Callable Wrapper
    if (GetType () != typeof (Integer)) {
        SetHandle (
                Android.Runtime.JNIEnv.CreateInstance (GetType (), "(I)V", new JValue (value)),
                JniHandleOwnership.TransferLocalRef);
        return;
    }

    // 4. Declaring type: lookup &amp; cache method id...
    if (id_ctor_I == IntPtr.Zero)
        id_ctor_I = JNIEnv.GetMethodID (class_ref, "<init>", "(I)V");
    // ...then create the Java instance and store
    SetHandle (
            JNIEnv.NewObject (class_ref, id_ctor_I, new JValue (value)),
            JniHandleOwnership.TransferLocalRef);
}
```

[JNIEnv.CreateInstance](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CreateInstance/) методы являются вспомогательные методы для выполнения `JNIEnv.FindClass`, `JNIEnv.GetMethodID`, `JNIEnv.NewObject`, и `JNIEnv.DeleteGlobalReference` на значение, возвращаемое из `JNIEnv.FindClass`. См. подробные сведения в следующем подразделе.

<a name="_Supporting_Inheritance,_Interfaces_1" />

### <a name="supporting-inheritance-interfaces"></a>Поддерживает наследование, интерфейсы

Создание подкласса типа Java или реализации интерфейса Java требует создания [Android вызываемых оболочек](~/android/platform/java-integration/android-callable-wrappers.md) (ACWs), которые создаются для каждого `Java.Lang.Object` подкласс процессе упаковки. Созданием ACW осуществляется через [Android.Runtime.RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/) настраиваемого атрибута.

Для C# типов, `[Register]` конструктор настраиваемого атрибута требуется один аргумент: [JNI упрощенное ссылка на тип](#_Simplified_Type_References_1) Java соответствующего типа. Это позволяет, указав разные имена между Java и C#.

До Xamarin.Android 4.0 `[Register]` настраиваемого атрибута была недоступна в «alias» существующие типы Java. Это обусловлено тем, в процессе создания ACW вызовет ACWs для каждого `Java.Lang.Object` обнаружил подкласс.

Xamarin.Android 4.0 были введены [RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/) свойство. Это свойство указывает, что процесс создания ACW *пропустить* тип с заметками, позволяя объявление новых управляемых вызываемых оболочек, не приведет к ACWs, создаваемый во время создания пакета. Благодаря этому существующие типы Java привязки. К примеру, рассмотрим следующий простой класс Java, `Adder`, который содержит один метод `add`, который добавляет в целые числа и возвращает результат:

```java
package mono.android.test;
public class Adder {
    public int add (int a, int b) {
        return a + b;
    }
}
```

`Adder` Тип может быть связан как:

```csharp
[Register ("mono/android/test/Adder", DoNotGenerateAcw=true)]
public partial class Adder : Java.Lang.Object {
    static IntPtr class_ref = JNIEnv.FindClass ( "mono/android/test/Adder");

    public Adder ()
    {
    }

    public Adder (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
    }
}
partial class ManagedAdder : Adder {
}
```

Здесь `Adder` C# тип *псевдонимы* `Adder` типа Java. `[Register]` Атрибут используется для указания имени JNI `mono.android.test.Adder` типом Java и `DoNotGenerateAcw` свойство используется для предотвращения создания ACW. Это приведет к созданию ACW для `ManagedAdder` типа — правильно подклассы `mono.android.test.Adder` типа. Если `RegisterAttribute.DoNotGenerateAcw` свойство еще не использовался, то будет создан новый процесс сборки Xamarin.Android `mono.android.test.Adder` типа Java. Это приведет к ошибкам компиляции, как `mono.android.test.Adder` типа может присутствовать дважды в двух отдельных файлах.



### <a name="binding-virtual-methods"></a>Виртуальные методы привязки

`ManagedAdder` подклассы Java `Adder` , однако оно не особо интересен: C# `Adder` типа не определяет все виртуальные методы, поэтому `ManagedAdder` не может переопределить ничего.

Привязка `virtual` методы, чтобы разрешить переопределение подклассами требует несколько моментов, которые необходимо выполнить которого делятся на две категории:

1.  **Метод привязки**

1.  **Метод регистрации**


#### <a name="method-binding"></a>Метод привязки

Метод привязки требуется добавить два члена поддержки для C# `Adder` определение: `ThresholdType`, и `ThresholdClass`.

##### <a name="thresholdtype"></a>ThresholdType

`ThresholdType` Свойство возвращает текущий тип привязки:

```csharp
partial class Adder {
    protected override System.Type ThresholdType {
        get {
            return typeof (Adder);
        }
    }
}
```

`ThresholdType` используется в метод привязки определить, когда он должен выполнять виртуальное и диспетчеризации невиртуальных методов. Всегда должны возвращать `System.Type` экземпляр, который соответствует объявляющего типа C# типа.

##### <a name="thresholdclass"></a>ThresholdClass

`ThresholdClass` Свойство возвращает ссылки на класс JNI для связанного типа:

```csharp
partial class Adder {
    protected override IntPtr ThresholdClass {
        get {
            return class_ref;
        }
    }
}
```

`ThresholdClass` используется в привязке метод, при вызове невиртуальных методов.

#### <a name="binding-implementation"></a>Реализация привязки

Реализация метода привязки несет ответственность за время выполнения вызова метода Java. Он также содержит `[Register]` объявление настраиваемого атрибута, которое является частью метода регистрации и обсуждаются в разделе метод регистрации:

```csharp
[Register ("add", "(II)I", "GetAddHandler")]
    public virtual int Add (int a, int b)
    {
        if (id_add == IntPtr.Zero)
            id_add = JNIEnv.GetMethodID (class_ref, "add", "(II)I");
        if (GetType () == ThresholdType)
            return JNIEnv.CallIntMethod (Handle, id_add, new JValue (a), new JValue (b));
        return JNIEnv.CallNonvirtualIntMethod (Handle, ThresholdClass, id_add, new JValue (a), new JValue (b));
    }
}
```

`id_add` Поле содержит идентификатор метода для метода Java для вызова. `id_add` Значение получается из `JNIEnv.GetMethodID`, который требует от объявляющего класса (`class_ref`), имя метода Java (`"add"`) и JNI подписи метода (`"(II)I"`).

После получения идентификатор метода `GetType` сравнивается с `ThresholdType` для определения необходимости виртуальной или невиртуальный диспетчеризации. Виртуальной диспетчеризации является обязательным, если `GetType` соответствует `ThresholdType`, как `Handle` может ссылаться на подкласс выделенный Java, который переопределяет метод.

Когда `GetType` не соответствует `ThresholdType`, `Adder` подклассом (например, `ManagedAdder`) и `Adder.Add` реализации будет вызываться, только если вызван подкласс `base.Add`. Это происходит невиртуальный диспетчеризации, что выгодно `ThresholdClass` пригодиться. `ThresholdClass` Указывает, какой класс Java будет предоставить реализацию метода для вызова.



#### <a name="method-registration"></a>Метод регистрации

Предположим, что имеется обновленный `ManagedAdder` определение, которое переопределяет `Adder.Add` метод:

```csharp
partial class ManagedAdder : Adder {
    public override int Add (int a, int b) {
        return (a*2) + (b*2);
    }
}
```

Помните, что `Adder.Add` было `[Register]` настраиваемого атрибута:

```csharp
[Register ("add", "(II)I", "GetAddHandler")]
```

`[Register]` Настраиваемого атрибута конструктор принимает три значения:

1.  Имя метода Java, `"add"` в данном случае.

1.  Сигнатура типа JNI метода, `"(II)I"` в данном случае.

1.  *Метод соединитель* , `GetAddHandler` в данном случае.
    Методы соединитель будет рассматриваться позднее.


Первые два параметра позволяют в процессе создания ACW создать объявление метода для переопределения этого метода. Итоговый ACW должен содержать следующий код:

```csharp
public class ManagedAdder extends mono.android.test.Adder {
    static final String __md_methods;
    static {
        __md_methods = "n_add:(II)I:GetAddHandler\n" +
            "";
        mono.android.Runtime.register (...);
    }
    @Override
    public int add (int p0, int p1) {
        return n_add (p0, p1);
    }
    private native int n_add (int p0, int p1);
    // ...
}
```

Обратите внимание, что `@Override` объявлен метод, который делегирует `n_`-префиксом метод с тем же именем. Это убедитесь, что при вызове кода Java `ManagedAdder.add`, `ManagedAdder.n_add` будет вызываться, что позволит переопределение C# `ManagedAdder.Add` выполняемый метод.

Таким образом, наиболее важный вопрос: как является `ManagedAdder.n_add` подключается к `ManagedAdder.Add`?

Java `native` методы регистрируются со средой выполнения Java (Android среда выполнения) через [JNI RegisterNatives функция](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp17734).
`RegisterNatives` принимает массив структур, содержащий имя метода Java JNI сигнатуре типа и указатель на функцию для вызова, следующий за [JNI, соглашение о вызовах](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/design.html#wp715).
Указатель функции должен быть функции, принимающей два аргумента указателя, следуют параметры метода. Java `ManagedAdder.n_add` метод должен быть реализован через функцию, которая имеет следующий прототип C:

```csharp
int FunctionName(JNIEnv *env, jobject this, int a, int b)
```

Xamarin.Android не предоставляет `RegisterNatives` метод. Вместо этого ACW и MCW вместе предоставляют сведения, необходимые для вызова `RegisterNatives`: ACW содержит имя метода и сигнатура типа JNI, хватает только указатель на функцию для подключения.

Именно здесь *метод соединитель* пригодиться. Третий `[Register]` параметр настраиваемого атрибута является имя метода, определенного в зарегистрированного типа или базового класса зарегистрированного типа, который не принимает никаких параметров и возвращает `System.Delegate`. Возвращенный `System.Delegate` в свою очередь ссылается на метод с корректной сигнатурой функция JNI. Наконец, делегат, который возвращает метод соединитель *необходимо* иметь корень таким образом, чтобы сборщик Мусора не собирает его, как Java, указанный делегат.

```csharp
#pragma warning disable 0169
static Delegate cb_add;
// This method must match the third parameter of the [Register]
// custom attribute, must be static, must return System.Delegate,
// and must accept no parameters.
static Delegate GetAddHandler ()
{
    if (cb_add == null)
        cb_add = JNINativeWrapper.CreateDelegate ((Func<IntPtr, IntPtr, int, int, int>) n_Add);
    return cb_add;
}
// This method is registered with JNI.
static int n_Add (IntPtr jnienv, IntPtr lrefThis, int a, int b)
{
    Adder __this = Java.Lang.Object.GetObject<Adder>(lrefThis, JniHandleOwnership.DoNotTransfer);
    return __this.Add (a, b);
}
#pragma warning restore 0169
```

`GetAddHandler` Метод создает `Func<IntPtr, IntPtr, int, int,
int>` делегат, который ссылается на `n_Add` затем вызывает метод, [JNINativeWrapper.CreateDelegate](https://developer.xamarin.com/api/member/Android.Runtime.JNINativeWrapper.CreateDelegate/).
`JNINativeWrapper.CreateDelegate` заключает в оболочку указанный метод, чтобы все необработанные исключения обрабатываются и приведет к вызов в блок try/catch [AndroidEvent.UnhandledExceptionRaiser](https://developer.xamarin.com/api/event/Android.Runtime.AndroidEnvironment.UnhandledExceptionRaiser/) событий. Результирующий делегат хранится в статический `cb_add` переменной, таким образом, чтобы сборщик Мусора не освобождает делегат.

Наконец `n_Add` метод отвечает за маршалинг параметров JNI для соответствующих управляемых типов, а затем вызовите метод делегирование.

Примечание: Всегда используйте `JniHandleOwnership.DoNotTransfer` при получении MCW поверх экземпляра Java. Рассматривая их как ссылку на локальном (и тем самым вызывая `JNIEnv.DeleteLocalRef`) приведет к разрыву управляемый -&gt; Java -&gt; управляемого стека переходов.



### <a name="complete-adder-binding"></a>Полная привязка Adder

Полнофункциональное управляемое привязку для `mono.android.tests.Adder` тип является:

```csharp
[Register ("mono/android/test/Adder", DoNotGenerateAcw=true)]
public class Adder : Java.Lang.Object {

    static IntPtr class_ref = JNIEnv.FindClass ("mono/android/test/Adder");

    public Adder ()
    {
    }

    public Adder (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
    }

    protected override Type ThresholdType {
        get {return typeof (Adder);}
    }

    protected override IntPtr ThresholdClass {
        get {return class_ref;}
    }

#region Add
    static IntPtr id_add;

    [Register ("add", "(II)I", "GetAddHandler")]
    public virtual int Add (int a, int b)
    {
        if (id_add == IntPtr.Zero)
            id_add = JNIEnv.GetMethodID (class_ref, "add", "(II)I");
        if (GetType () == ThresholdType)
            return JNIEnv.CallIntMethod (Handle, id_add, new JValue (a), new JValue (b));
        return JNIEnv.CallNonvirtualIntMethod (Handle, ThresholdClass, id_add, new JValue (a), new JValue (b));
    }

#pragma warning disable 0169
    static Delegate cb_add;
    static Delegate GetAddHandler ()
    {
        if (cb_add == null)
            cb_add = JNINativeWrapper.CreateDelegate ((Func<IntPtr, IntPtr, int, int, int>) n_Add);
        return cb_add;
    }

    static int n_Add (IntPtr jnienv, IntPtr lrefThis, int a, int b)
    {
        Adder __this = Java.Lang.Object.GetObject<Adder>(lrefThis, JniHandleOwnership.DoNotTransfer);
        return __this.Add (a, b);
    }
#pragma warning restore 0169
#endregion
}
```



### <a name="restrictions"></a>Ограничения

При написании тип, соответствующий следующим критериям:

1.  Подклассы `Java.Lang.Object`

1.  Имеет `[Register]` настраиваемого атрибута

1.  `RegisterAttribute.DoNotGenerateAcw` равно `true`


Затем для сборки Мусора взаимодействия тип *не должны* содержат полей, которые могут ссылаться на `Java.Lang.Object` или `Java.Lang.Object` подкласс во время выполнения. Например, поля типа `System.Object` и любой другой тип интерфейса не разрешены. Типы, которые не могут ссылаться на `Java.Lang.Object` экземпляров разрешены, такие как `System.String` и `List<int>`. Это ограничение необходимо для предотвращения коллекции преждевременное объектов сборщик мусора.

Если тип должен содержать поле экземпляра, которое может ссылаться на `Java.Lang.Object` экземпляра, то тип поля должен быть `System.WeakReference` или `GCHandle`.



## <a name="binding-abstract-methods"></a>Привязка абстрактные методы

Привязка `abstract` методы мало чем отличается от привязки виртуальные методы. Существует два отличия:

1.  Абстрактный метод является абстрактным. Оно по-прежнему сохраняет `[Register]` атрибут и связанный метод регистрации, метод привязки просто перемещается `Invoker` типа.

1.  Отличный от `abstract` `Invoker` тип создается, который подклассы абстрактного типа. `Invoker` Тип должен переопределять все абстрактные методы, объявленные в базовом классе и переопределенная реализация является реализацией метода привязки, то, что можно игнорировать регистр невиртуальный диспетчеризации.


Например, предположим, что выше `mono.android.test.Adder.add` бы метод `abstract`. C# Изменить привязки, чтобы `Adder.Add` были абстрактные и новый `AdderInvoker` тип будет определяться, которые реализованы `Adder.Add`:

```csharp
partial class Adder {
    [Register ("add", "(II)I", "GetAddHandler")]
    public abstract int Add (int a, int b);

    // The Method Registration machinery is identical to the
    // virtual method case...
}

partial class AdderInvoker : Adder {
    public AdderInvoker (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
    }

    static IntPtr id_add;
    public override int Add (int a, int b)
    {
        if (id_add == IntPtr.Zero)
            id_add = JNIEnv.GetMethodID (class_ref, "add", "(II)I");
        return JNIEnv.CallIntMethod (Handle, id_add, new JValue (a), new JValue (b));
    }
}
```

`Invoker` Тип необходимо только в том случае, при получении ссылки JNI на экземпляры, созданные Java.


## <a name="binding-interfaces"></a>Привязка интерфейсов

Привязка интерфейсов принципиально подобен классу привязки классы, содержащие виртуальные методы, но многие особенности имеют отличия заметно (и не столь малозаметные). Рассмотрим следующий [объявление интерфейса Java](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/Adder.java#L14):

```csharp
public interface Progress {
    void onAdd(int[] values, int currentIndex, int currentSum);
}
```

Интерфейс привязки состоят из двух частей: C# определение интерфейса, а также определение средство вызова для интерфейса.



### <a name="interface-definition"></a>Определение интерфейса

C# Определения интерфейса необходимо выполнить следующие требования:

-   Определение интерфейса должен иметь `[Register]` настраиваемого атрибута.

-   Определение интерфейса необходимо расширить `IJavaObject interface`.
    В противном случае будет препятствовать ACWs наследование интерфейса Java.

-   Каждый метод интерфейса должен содержать `[Register]` атрибут, указав имя соответствующего метода Java JNI подпись и метод соединителя.

-   Метод соединителя необходимо также указать тип, метод соединитель можно разместить на.

При привязке `abstract` и `virtual` методы, метод соединитель будет осуществляться в иерархии наследования типа регистрируемого. Интерфейсы могут иметь методы, не содержащий тел, поэтому это не сработает, таким образом требование указанного типа, указывающее, где находится метод соединителя. Тип задается в строке метод соединитель, после двоеточия `':'`, и должно быть имя типа с указанием сборки, типа, содержащего средство вызова.

Объявления метода интерфейса являются перевод соответствующий метод Java с помощью *совместимых* типов. Для Java встроенные типы, совместимые типы являются соответствующего C# типов, например Java `int` — C# `int`. Для ссылочных типов совместимый тип является типом, который может предоставить дескриптор JNI соответствующего типа Java.

Члены интерфейса не вызываются непосредственно приложением Java &ndash; вызова будет осуществлялся с помощью типа вызова &ndash; чтобы разрешался некоторую часть гибкость.

Интерфейс хода выполнения Java может быть [объявленные в C# как](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L83):

```csharp
[Register ("mono/android/test/Adder$Progress", DoNotGenerateAcw=true)]
public interface IAdderProgress : IJavaObject {
    [Register ("onAdd", "([III)V",
            "GetOnAddHandler:Mono.Samples.SanityTests.IAdderProgressInvoker, SanityTests, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null")]
    void OnAdd (JavaArray<int> values, int currentIndex, int currentSum);
}
```

Обратите внимание, что выше, мы были сопоставлены Java `int[]` параметр [JavaArray&lt;int&gt;](https://developer.xamarin.com/api/type/Android.Runtime.JavaArray%601/).
Это не является обязательным: нам удалось оно привязано к C# `int[]`, или `IList<int>`, или что-то другое полностью. Выбирается тот тип, `Invoker` должна быть возможность его преобразования в Java `int[]` типа для вызова.


### <a name="invoker-definition"></a>Определение вызова

`Invoker` Определение типа должен наследовать `Java.Lang.Object`, реализуйте соответствующий интерфейс и предоставляют все способы подключения, на которые ссылается определение интерфейса. Имеется один Дополнительные предложения, отличается от класса привязки: `class_ref` поле и метод идентификаторы должны входить в экземпляр, не статические члены.

Причина предпочтительного использования члены экземпляра связана с `JNIEnv.GetMethodID` поведение в среде выполнения Android. (Это может быть также поведение Java; она не была протестирована.) `JNIEnv.GetMethodID` возвращает значение null, при поиске метода, поступающие от реализованного интерфейса и не объявленный интерфейс. Рассмотрите возможность [java.util.SortedMap&lt;K, V&gt; ](http://developer.android.com/reference/java/util/SortedMap.html) Java-интерфейса, который реализует [java.util.Map&lt;K, V&gt; ](http://developer.android.com/reference/java/util/Map.html) интерфейс. MAP предоставляет [снимите](http://developer.android.com/reference/java/util/Map.html#clear()) метод, тем самым кажущихся разумным `Invoker` определение для SortedMap будет:

```csharp
// Fails at runtime. DO NOT FOLLOW
partial class ISortedMapInvoker : Java.Lang.Object, ISortedMap {
    static IntPtr class_ref = JNIEnv.FindClass ("java/util/SortedMap");
    static IntPtr id_clear;
    public void Clear()
    {
        if (id_clear == IntPtr.Zero)
            id_clear = JNIEnv.GetMethodID(class_ref, "clear", "()V");
        JNIEnv.CallVoidMethod(Handle, id_clear);
    }
     // ...
}
```

Выше завершится ошибкой, поскольку `JNIEnv.GetMethodID` вернет `null` при поиске `Map.clear` метод с помощью `SortedMap` экземпляра класса.

Существует два решения: интерфейс, который каждый метод поступает из отслеживания и иметь `class_ref` для каждого интерфейса, или оставить все как члены экземпляров и выполнять поиск метода на тип класса, а не тип интерфейса. Последний выполняется **Mono.Android.dll**.

Определение Invoker имеет шесть разделов: конструктор, `Dispose` метод, `ThresholdType` и `ThresholdClass` члены, `GetObject` метод, реализация метода интерфейса и реализация метода соединителя.



#### <a name="constructor"></a>Конструктор

Конструктор должен поиска вызываемого экземпляра класса среды выполнения и сохранения в экземпляре класса среды выполнения `class_ref` поля:

```csharp
partial class IAdderProgressInvoker {
    IntPtr class_ref;
    public IAdderProgressInvoker (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass (Handle);
        class_ref   = JNIEnv.NewGlobalRef (lref);
        JNIEnv.DeleteLocalRef (lref);
    }
}
```

Примечание: `Handle` свойство должно использоваться в теле конструктора и не `handle` параметра, как на Android v4.0 `handle` параметр может быть недопустимым, когда базовый конструктор завершится выполнение.


#### <a name="dispose-method"></a>Метод Dispose

`Dispose` Метод должен освободить глобальной ссылки, выделенных в конструкторе:

```csharp
partial class IAdderProgressInvoker {
    protected override void Dispose (bool disposing)
    {
        if (this.class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef (this.class_ref);
        this.class_ref = IntPtr.Zero;
        base.Dispose (disposing);
    }
}
```


#### <a name="thresholdtype-and-thresholdclass"></a>ThresholdType и ThresholdClass

`ThresholdType` И `ThresholdClass` элементы идентичны в том, что содержится в классе привязки:

```csharp
partial class IAdderProgressInvoker {
    protected override Type ThresholdType {
        get {
            return typeof (IAdderProgressInvoker);
        }
    }
    protected override IntPtr ThresholdClass {
        get {
            return class_ref;
        }
    }
}
```


#### <a name="getobject-method"></a>Метод GetObject

Статический `GetObject` метод требуется для поддержки [Extensions.JavaCast&lt;T&gt;()](https://developer.xamarin.com/api/member/Android.Runtime.Extensions.JavaCast%7BTResult%7D/p/Android.Runtime.IJavaObject/):

```csharp
partial class IAdderProgressInvoker {
    public static IAdderProgress GetObject (IntPtr handle, JniHandleOwnership transfer)
    {
        return new IAdderProgressInvoker (handle, transfer);
    }
}
```


#### <a name="interface-methods"></a>Методы интерфейса

Каждый метод интерфейса должен иметь реализацию, которая вызывает соответствующий метод Java через JNI:

```csharp
partial class IAdderProgressInvoker {
    IntPtr id_onAdd;
    public void OnAdd (JavaArray<int> values, int currentIndex, int currentSum)
    {
        if (id_onAdd == IntPtr.Zero)
            id_onAdd = JNIEnv.GetMethodID (class_ref, "onAdd", "([III)V");
        JNIEnv.CallVoidMethod (Handle, id_onAdd, new JValue (JNIEnv.ToJniHandle (values)), new JValue (currentIndex), new JValue (currentSum));
    }
}
```



#### <a name="connector-methods"></a>Методы соединителя

Методы соединителя и инфраструктуру поддержки отвечают за маршалинг параметров JNI соответствующий C# типов. Java `int[]` параметр будет передана в качестве JNI `jintArray`, который является `IntPtr` в C#. `IntPtr` Необходимо маршалировать в `JavaArray<int>` для поддержки вызова C# интерфейса:

```csharp
partial class IAdderProgressInvoker {
    static Delegate cb_onAdd;
    static Delegate GetOnAddHandler ()
    {
        if (cb_onAdd == null)
            cb_onAdd = JNINativeWrapper.CreateDelegate ((Action<IntPtr, IntPtr, IntPtr, int, int>) n_OnAdd);
        return cb_onAdd;
    }

    static void n_OnAdd (IntPtr jnienv, IntPtr lrefThis, IntPtr values, int currentIndex, int currentSum)
    {
        IAdderProgress __this = Java.Lang.Object.GetObject<IAdderProgress>(lrefThis, JniHandleOwnership.DoNotTransfer);
        using (var _values = new JavaArray<int>(values, JniHandleOwnership.DoNotTransfer)) {
            __this.OnAdd (_values, currentIndex, currentSum);
        }
    }
}
```

Если `int[]` будет предпочтительнее, чем `JavaList<int>`, затем [JNIEnv.GetArray()](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetArray/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership%2cSystem.Type)) вместо него может использоваться:

```csharp
int[] _values = (int[]) JNIEnv.GetArray(values, JniHandleOwnership.DoNotTransfer, typeof (int));
```

Обратите внимание, что `JNIEnv.GetArray` копирует весь массив между виртуальными машинами, поэтому для больших массивов в результате множество добавлена давления сборки Мусора.


### <a name="complete-invoker-definition"></a>Завершения определения вызова

[Завершения определения IAdderProgressInvoker](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L88):

```csharp
class IAdderProgressInvoker : Java.Lang.Object, IAdderProgress {

    IntPtr class_ref;

    public IAdderProgressInvoker (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass (Handle);
        class_ref = JNIEnv.NewGlobalRef (lref);
        JNIEnv.DeleteLocalRef (lref);
    }

    protected override void Dispose (bool disposing)
    {
        if (this.class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef (this.class_ref);
        this.class_ref = IntPtr.Zero;
        base.Dispose (disposing);
    }

    protected override Type ThresholdType {
        get {return typeof (IAdderProgressInvoker);}
    }

    protected override IntPtr ThresholdClass {
        get {return class_ref;}
    }

    public static IAdderProgress GetObject (IntPtr handle, JniHandleOwnership transfer)
    {
        return new IAdderProgressInvoker (handle, transfer);
    }

#region OnAdd
    IntPtr id_onAdd;
    public void OnAdd (JavaArray<int> values, int currentIndex, int currentSum)
    {
        if (id_onAdd == IntPtr.Zero)
            id_onAdd = JNIEnv.GetMethodID (class_ref, "onAdd",
                    "([III)V");
        JNIEnv.CallVoidMethod (Handle, id_onAdd,
                new JValue (JNIEnv.ToJniHandle (values)),
                new JValue (currentIndex),
new JValue (currentSum));
    }

#pragma warning disable 0169
    static Delegate cb_onAdd;
    static Delegate GetOnAddHandler ()
    {
        if (cb_onAdd == null)
            cb_onAdd = JNINativeWrapper.CreateDelegate ((Action<IntPtr, IntPtr, IntPtr, int, int>) n_OnAdd);
        return cb_onAdd;
    }

    static void n_OnAdd (IntPtr jnienv, IntPtr lrefThis, IntPtr values, int currentIndex, int currentSum)
    {
        IAdderProgress __this = Java.Lang.Object.GetObject<IAdderProgress>(lrefThis, JniHandleOwnership.DoNotTransfer);
        using (var _values = new JavaArray<int>(values, JniHandleOwnership.DoNotTransfer)) {
            __this.OnAdd (_values, currentIndex, currentSum);
        }
    }
#pragma warning restore 0169
#endregion
}
```



## <a name="jni-object-references"></a>Ссылки на объекты JNI

Многие методы JNIEnv возвращают *JNI* *ссылки на объекты*, похожи на `GCHandle`s. JNI предоставляет три различных типа ссылки на объекты: локальные ссылки, глобальные ссылки и слабые глобальной ссылки. Все три представляются в виде `System.IntPtr`, *, но* (как описано в разделе Типы функций JNI) не все `IntPtr`возвращаемыми `JNIEnv` методы являются ссылками. Например [JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/) возвращает `IntPtr`, но он не возвращает ссылку на объект, он возвращает `jmethodID`. Обратитесь к [документации функции JNI](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html) сведения.

Локальные ссылки, создаваемые *большинство* методы создания ссылки.
Android разрешает только ограниченное число локальные ссылки, должно существовать в любой момент времени, обычно 512. Локальные ссылки можно удалить с помощью [JNIEnv.DeleteLocalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/).
В отличие от JNI не все ссылаются на методы JNIEnv, которая ссылается на возвращаемый объект возвращать локальными ссылками; [JNIEnv.FindClass](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/) возвращает *глобального* ссылки. Настоятельно рекомендуется удалить локальные ссылки, как можно скорее, возможно, создав [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) вокруг объекта и указав `JniHandleOwnership.TransferLocalRef` для [Java.Lang.Object (IntPtr обработки, переноса JniHandleOwnership)](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) конструктор.

Глобальные ссылки, создаваемые [JNIEnv.NewGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewGlobalRef/) и [JNIEnv.FindClass](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/).
Они могут быть удалены с [JNIEnv.DeleteGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteGlobalRef/).
Эмуляторы имеют ограничение в 2000 глобальные ссылки на медиафайл, а устройства имеют ограничение на число около 52,000 глобальной ссылки.

Слабые ссылки на глобальные доступны только на версия Android 2.2 (Froyo) и более поздних версий. Слабые ссылки на глобальные можно удалить с помощью [JNIEnv.DeleteWeakGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteWeakGlobalRef/(System.IntPtr)).


### <a name="dealing-with-jni-local-references"></a>Работа с JNI локальные ссылки

[JNIEnv.GetObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetObjectField/), [JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/), [JNIEnv.CallObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallObjectMethod/), [JNIEnv.CallNonvirtualObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualObjectMethod/)и [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) методы возвращают `IntPtr` содержащий локальной ссылки JNI на объект Java или `IntPtr.Zero` Если возвращено Java `null`. Из-за ограниченного числа локальные ссылки, неиспользуемый во после (512 записей), желательно убедитесь, что ссылки будут удалены в течение отведенного времени. Существует три способа, которые могут быть обработаны локальные ссылки: явным образом их удаления, создания `Java.Lang.Object` экземпляра для хранения их и используя `Java.Lang.Object.GetObject<T>()` создание управляемых вызываемой оболочки вокруг них.



### <a name="explicitly-deleting-local-references"></a>Явное удаление локальные ссылки

[JNIEnv.DeleteLocalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/) используется для удаления локальные ссылки. После удаления ссылки на местную, он больше не может использоваться, поэтому будьте внимательны, чтобы убедиться, что `JNIEnv.DeleteLocalRef` самая последняя операция, сделать с помощью локальной ссылки.

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
try {
    // Do something with `lref`
}
finally {
    JNIEnv.DeleteLocalRef (lref);
}
```



### <a name="wrapping-with-javalangobject"></a>Перенос с Java.Lang.Object

`Java.Lang.Object` предоставляет [Java.Lang.Object (дескриптор IntPtr, передачи JniHandleOwnership)](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) конструктор, который можно использовать для переноса существующей ссылки JNI. [JniHandleOwnership](https://developer.xamarin.com/api/type/Android.Runtime.JniHandleOwnership/) параметр определяет, каким образом `IntPtr` параметра должны обрабатываться:

-   [JniHandleOwnership.DoNotTransfer](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.DoNotTransfer/) &ndash; созданный `Java.Lang.Object` экземпляр создаст новую ссылку из глобального `handle` параметра, и `handle` не изменяется.
    Вызывающий объект отвечает за освобождение `handle` и при необходимости.

-   [JniHandleOwnership.TransferLocalRef](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.TransferLocalRef/) &ndash; созданный `Java.Lang.Object` экземпляр создаст новую ссылку из глобального `handle` параметра, и `handle` будут удалены вместе с [JNIEnv.DeleteLocalRef ](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/) . Вызывающий объект не должен освободить `handle` и не должны использовать `handle` после конструктора завершения выполнения.

-   [JniHandleOwnership.TransferGlobalRef](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.TransferLocalRef/) &ndash; созданный `Java.Lang.Object` экземпляра будет принять владение `handle` параметра. Вызывающий объект не должен освободить `handle` .


Поскольку методы вызова JNI метод возвращает локальный refs `JniHandleOwnership.TransferLocalRef` обычно используется:

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef);
```

Созданный глобальной ссылки не будут освобождены до `Java.Lang.Object` экземпляр удаляется сборщиком мусора. Если существует возможность, освобождение экземпляра будет освободить глобальной ссылки, ускоряя мусора:

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
using (var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef)) {
    // use value ...
}
```



### <a name="using-javalangobjectgetobjectlttgt"></a>С помощью Java.Lang.Object.GetObject&lt;T&gt;)

`Java.Lang.Object` предоставляет [Java.Lang.Object.GetObject&lt;T&gt;(дескриптор IntPtr, передачи JniHandleOwnership)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) метод, который может использоваться для создания управляемых вызываемую оболочку указанного типа.

Тип `T` необходимо выполнить следующие требования:

1.  `T` должен быть ссылочным типом.

1.  `T` необходимо реализовать `IJavaObject` интерфейс.

1.  Если `T` не является абстрактным классом или интерфейсом, затем `T` должны предоставлять конструктор с типами параметров `(IntPtr,
    JniHandleOwnership)` .

1.  Если `T` является абстрактным классом или интерфейсом, существует *необходимо* быть *invoker* для `T` . Средство вызова — это неабстрактный тип, который наследует `T` или реализует `T` , и имеет то же имя, что `T` с суффиксом Invoker. Например, если T — это интерфейс `Java.Lang.IRunnable` , то типом `Java.Lang.IRunnableInvoker` должен существовать и должен содержать необходимые `(IntPtr,
    JniHandleOwnership)` конструктор.


Поскольку методы вызова JNI метод возвращает локальный refs `JniHandleOwnership.TransferLocalRef` обычно используется:

```csharp
IntPtr lrefString = JNIEnv.CallObjectMethod(instance, methodID);
Java.Lang.String value = Java.Lang.Object.GetObject<Java.Lang.String>( lrefString, JniHandleOwnership.TransferLocalRef);
```

<a name="_Looking_up_Java_Types" />

## <a name="looking-up-java-types"></a>Поиск типов Java

Для поиска поля или метода в JNI, объявляющий тип для поля или метода необходимо искать первый. [Android.Runtime.JNIEnv.FindClass(string)](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/(System.String)) метод используется для поиска типов Java. Параметр строки *упрощенное ссылка на тип* или *ссылка на полный тип* для типа Java. См. в разделе [тип ссылки JNI](#_JNI_Type_References) Дополнительные сведения о ссылках упрощенного и полного типа.

Примечание: в отличие от всех остальных `JNIEnv` метод, который возвращает экземпляры объектов, `FindClass` Возвращает глобальные ссылки, не является локальной ссылкой.

<a name="_Instance_Fields" />

## <a name="instance-fields"></a>Поля экземпляра

Поля можно назвать *идентификаторов полей*. Поля извлекаются через [JNIEnv.GetFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFieldID/), который требует от класса, это поле определено в имя поля и [сигнатура типа JNI](#JNI_Type_Signatures) поля.

Поля идентификаторов не обязательно должны быть освобождены они действуют до тех пор, пока загружается соответствующего типа Java. (Android не поддерживается выгрузки класса.)

Существует два набора методов для работы с полями экземпляров: один для чтения поля экземпляра и один для записи полей экземпляра. Все наборы из методов требуется идентификатор поля для чтения или записи значение поля.


### <a name="reading-instance-field-values"></a>Считывание значений поля экземпляра

Набор методов для чтения значения полей экземпляра использует шаблон именования:

```csharp
* JNIEnv.Get*Field(IntPtr instance, IntPtr fieldID);
```
где `*` — это тип поля:

-   [JNIEnv.GetObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetObjectField/) &ndash; прочитать значение любого поля экземпляра, такие как не типом builtin `java.lang.Object` , массивы и типы интерфейсов. Возвращаемое значение локальной ссылки JNI.

-   [JNIEnv.GetBooleanField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetBooleanField/) &ndash; прочитать значение `bool` полей экземпляра.

-   [JNIEnv.GetByteField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetByteField/) &ndash; прочитать значение `sbyte` полей экземпляра.

-   [JNIEnv.GetCharField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetCharField/) &ndash; прочитать значение `char` полей экземпляра.

-   [JNIEnv.GetShortField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetShortField/) &ndash; прочитать значение `short` полей экземпляра.

-   [JNIEnv.GetIntField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetIntField/) &ndash; прочитать значение `int` полей экземпляра.

-   [JNIEnv.GetLongField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetLongField/) &ndash; прочитать значение `long` полей экземпляра.

-   [JNIEnv.GetFloatField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFloatField/) &ndash; прочитать значение `float` полей экземпляра.

-   [JNIEnv.GetDoubleField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetDoubleField/) &ndash; прочитать значение `double` полей экземпляра.





### <a name="writing-instance-field-values"></a>Запись значений поля экземпляра

Набор методов для записи значения полей экземпляра использует шаблон именования:

```csharp
JNIEnv.SetField(IntPtr instance, IntPtr fieldID, Type value);
```

где *тип* тип поля:

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.IntPtr)) &ndash; записать значение любого поля, такие как не типом builtin `java.lang.Object` , массивы и типы интерфейсов. `IntPtr` Значение может быть локальной ссылки JNI, глобальной ссылки JNI, слабые глобальной ссылки JNI, или `IntPtr.Zero` (для `null` ).

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Boolean)) &ndash; записать значение `bool` полей экземпляра.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.SByte)) &ndash; записать значение `sbyte` полей экземпляра.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Char)) &ndash; записать значение `char` полей экземпляра.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Int16)) &ndash; записать значение `short` полей экземпляра.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Int32)) &ndash; записать значение `int` полей экземпляра.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Int64)) &ndash; записать значение `long` полей экземпляра.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Single)) &ndash; записать значение `float` полей экземпляра.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Double)) &ndash; записать значение `double` полей экземпляра.


<a name="_Static_Fields" />

## <a name="static-fields"></a>Статические поля

Статические поля можно назвать *идентификаторов полей*. Поля извлекаются через [JNIEnv.GetStaticFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticFieldID/), который требует от класса, это поле определено в имя поля и [сигнатура типа JNI](#JNI_Type_Signatures) поля.

Поля идентификаторов не обязательно должны быть освобождены они действуют до тех пор, пока загружается соответствующего типа Java. (Android не поддерживается выгрузки класса.)

Существует два набора методов для манипулирования статические поля: одно для чтения поля экземпляра и один для записи полей экземпляра. Все наборы из методов требуется идентификатор поля для чтения или записи значение поля.


### <a name="reading-static-field-values"></a>Чтение значения статических полей

Набор методов для чтения значения статических полей использует шаблон именования:

```csharp
* JNIEnv.GetStatic*Field(IntPtr class, IntPtr fieldID);
```

где `*` — это тип поля:

-   [JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/) &ndash; прочитать значение статического поля, не типом builtin, такие как `java.lang.Object` , массивы и типы интерфейсов. Возвращаемое значение локальной ссылки JNI.

-   [JNIEnv.GetStaticBooleanField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticBooleanField/) &ndash; прочитать значение `bool` статические поля.

-   [JNIEnv.GetStaticByteField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticByteField/) &ndash; прочитать значение `sbyte` статические поля.

-   [JNIEnv.GetStaticCharField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticCharField/) &ndash; прочитать значение `char` статические поля.

-   [JNIEnv.GetStaticShortField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticShortField/) &ndash; прочитать значение `short` статические поля.

-   [JNIEnv.GetStaticLongField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticLongField/) &ndash; прочитать значение `long` статические поля.

-   [JNIEnv.GetStaticFloatField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticFloatField/) &ndash; прочитать значение `float` статические поля.

-   [JNIEnv.GetStaticDoubleField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticDoubleField/) &ndash; прочитать значение `double` статические поля.



### <a name="writing-static-field-values"></a>Запись значений в статическом поле

Набор методов для записи значения статических полей использует шаблон именования:

```csharp
JNIEnv.SetStaticField(IntPtr class, IntPtr fieldID, Type value);
```

где *тип* тип поля:

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.IntPtr)) &ndash; записать значение статического поля, не типом builtin, такие как `java.lang.Object` , массивы и типы интерфейсов. `IntPtr` Значение может быть локальной ссылки JNI, глобальной ссылки JNI, слабые глобальной ссылки JNI, или `IntPtr.Zero` (для `null` ).

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Boolean)) &ndash; записать значение `bool` статические поля.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.SByte)) &ndash; записать значение `sbyte` статические поля.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Char)) &ndash; записать значение `char` статические поля.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Int16)) &ndash; записать значение `short` статические поля.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Int32)) &ndash; записать значение `int` статические поля.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Int64)) &ndash; записать значение `long` статические поля.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Single)) &ndash; записать значение `float` статические поля.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Double)) &ndash; записать значение `double` статические поля.


<a name="_Instance_Methods" />

## <a name="instance-methods"></a>Методы экземпляра

Методы экземпляра вызываются с помощью *метод идентификаторы*. Метод извлекаются через [JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/), который требует, чтобы тип, определенный в имя метода, метод и [сигнатура типа JNI](#JNI_Type_Signatures) метода.

Метод идентификаторы не обязательно должны быть освобождены они действуют до тех пор, пока загружается соответствующего типа Java. (Android не поддерживается выгрузки класса.)

Существует два набора методов для вызова методов: один для практически вызов методов и один для вызова методов не виртуально. Оба набора методов требуется идентификатор метода для вызова метода и невиртуальный вызова также требуется указать, какую реализацию класса должны вызываться.

Методы интерфейса можно только искать в объявляющий тип; методы, которые берутся из расширенных/унаследованных интерфейсов невозможно найти. См. в разделе последующих интерфейсах привязки / section реализация вызова для получения дополнительных сведений.

Любой метод, объявленной в классе или любой базовый класс или реализованного интерфейса можно искать.


### <a name="virtual-method-invocation"></a>Вызов виртуального метода

Набор методов для вызова методов практически использует шаблон именования:

```csharp
* JNIEnv.Call*Method( IntPtr instance, IntPtr methodID, params JValue[] args );
```

где `*` является возвращаемым типом метода.

-   [JNIEnv.CallObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallObjectMethod/) &ndash; вызова метода, которое возвращает тип не builtin, такие как `java.lang.Object` , массивы и интерфейсы. Возвращаемое значение локальной ссылки JNI.

-   [JNIEnv.CallBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallBooleanMethod/) &ndash; вызвать метод, возвращающий `bool` значение.

-   [JNIEnv.CallByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallByteMethod/) &ndash; вызвать метод, возвращающий `sbyte` значение.

-   [JNIEnv.CallCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallCharMethod/) &ndash; вызвать метод, возвращающий `char` значение.

-   [JNIEnv.CallShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallShortMethod/) &ndash; вызвать метод, возвращающий `short` значение.

-   [JNIEnv.CallLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallLongMethod/) &ndash; вызвать метод, возвращающий `long` значение.

-   [JNIEnv.CallFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallFloatMethod/) &ndash; вызвать метод, возвращающий `float` значение.

-   [JNIEnv.CallDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallDoubleMethod/) &ndash; вызвать метод, возвращающий `double` значение.



### <a name="non-virtual-method-invocation"></a>Вызов невиртуального метода

Набор методов для вызова методов не виртуально использует шаблон именования:

```csharp
* JNIEnv.CallNonvirtual*Method( IntPtr instance, IntPtr class, IntPtr methodID, params JValue[] args );
```

где `*` является возвращаемым типом метода. Невиртуальный метод вызова обычно используется для вызова базового метода виртуального метода.

-   [JNIEnv.CallNonvirtualObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualObjectMethod/) &ndash; Non практически вызова метода, которое возвращает тип не builtin, такие как `java.lang.Object` , массивы и интерфейсы. Возвращаемое значение локальной ссылки JNI.

-   [JNIEnv.CallNonvirtualBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualBooleanMethod/) &ndash; Non практически вызвать метод, возвращающий `bool` значение.

-   [JNIEnv.CallNonvirtualByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualByteMethod/) &ndash; Non практически вызвать метод, возвращающий `sbyte` значение.

-   [JNIEnv.CallNonvirtualCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualCharMethod/) &ndash; Non практически вызвать метод, возвращающий `char` значение.

-   [JNIEnv.CallNonvirtualShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualShortMethod/) &ndash; Non практически вызвать метод, возвращающий `short` значение.

-   [JNIEnv.CallNonvirtualLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualLongMethod/) &ndash; Non практически вызвать метод, возвращающий `long` значение.

-   [JNIEnv.CallNonvirtualFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualFloatMethod/) &ndash; Non практически вызвать метод, возвращающий `float` значение.

-   [JNIEnv.CallNonvirtualDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualDoubleMethod/) &ndash; Non практически вызвать метод, возвращающий `double` значение.


<a name="_Static_Methods" />

## <a name="static-methods"></a>Статические методы

Статические методы вызываются с помощью *метод идентификаторы*. Метод извлекаются через [JNIEnv.GetStaticMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/), который требует, чтобы тип, определенный в имя метода, метод и [сигнатура типа JNI](#JNI_Type_Signatures) метода.

Метод идентификаторы не обязательно должны быть освобождены они действуют до тех пор, пока загружается соответствующего типа Java. (Android не поддерживается выгрузки класса.)



### <a name="static-method-invocation"></a>Вызов статического метода

Набор методов для вызова методов практически использует шаблон именования:

```csharp
* JNIEnv.CallStatic*Method( IntPtr class, IntPtr methodID, params JValue[] args );
```

где `*` является возвращаемым типом метода.

-   [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) &ndash; Вызовите статический метод, возвращающий тип не builtin, такие как `java.lang.Object` , массивы и интерфейсы. Возвращаемое значение локальной ссылки JNI.

-   [JNIEnv.CallStaticBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticBooleanMethod/) &ndash; Вызовите статический метод, возвращающий `bool` значение.

-   [JNIEnv.CallStaticByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticByteMethod/) &ndash; Вызовите статический метод, возвращающий `sbyte` значение.

-   [JNIEnv.CallStaticCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticCharMethod/) &ndash; Вызовите статический метод, возвращающий `char` значение.

-   [JNIEnv.CallStaticShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticShortMethod/) &ndash; Вызовите статический метод, возвращающий `short` значение.

-   [JNIEnv.CallStaticLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallLongMethod/) &ndash; Вызовите статический метод, возвращающий `long` значение.

-   [JNIEnv.CallStaticFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticFloatMethod/) &ndash; Вызовите статический метод, возвращающий `float` значение.

-   [JNIEnv.CallStaticDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticDoubleMethod/) &ndash; Вызовите статический метод, возвращающий `double` значение.


<a name="JNI_Type_Signatures" />

## <a name="jni-type-signatures"></a>Сигнатуры типов JNI

[Сигнатуры типов JNI](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/types.html#wp16432) являются [тип ссылки JNI](#_JNI_Type_References) (хотя не упрощенный тип ссылки), за исключением методов. С помощью методов, сигнатура типа JNI является открывающей круглой скобкой `'('`, а затем ссылок на типы для всех типов, соединенных вместе (с без разделительной запятых или что-нибудь еще), следуют закрывающую скобку параметра `')'`, следует ссылка на тип JNI тип возвращаемого значения метода.

Например если метод Java:

```java
long f(int n, String s, int[] array);
```

Сигнатура типа JNI будет следующим:

```csharp
(ILjava/lang/String;[I)J
```

Как правило, это *строго* рекомендуется использовать `javap` команду, чтобы определить JNI подписи. Например, сигнатура типа JNI из [java.lang.Thread.State.valueOf(String)](http://developer.android.com/reference/java/lang/Thread.State.html#valueOf(java.lang.String)) метод является "(Ljava/lang или строка); состояние $ Ljava/lang/потока;», тогда как JNI введите подпись [ java.lang.Thread.State.values](http://developer.android.com/reference/java/lang/Thread.State.html#values) метод является «() [состояние $ Ljava/lang/потока;». Остерегайтесь конечные точки с запятой; Эти *являются* частью сигнатура типа JNI.

<a name="_JNI_Type_References" />

## <a name="jni-type-references"></a>Тип ссылки JNI

Тип ссылки JNI, отличаются от ссылок на типы Java. Нельзя использовать полные имена типов Java, например `java.lang.String` с JNI, необходимо использовать варианты JNI `"java/lang/String"` или `"Ljava/lang/String;"`, в зависимости от контекста; Дополнительные сведения приведены ниже.
Существует четыре типа тип ссылки JNI.

-  **Встроенные**
-  **упрощенное**
-  **type**
-  **array**


### <a name="built-in-type-references"></a>Встроенный тип ссылки

Встроенный тип ссылки являются один символ, используемый для ссылки на встроенных типов значений. Сопоставление выглядит следующим образом:

-  `"B"` для `sbyte` .
-  `"S"` для `short` .
-  `"I"` для `int` .
-  `"J"` для `long` .
-  `"F"` для `float` .
-  `"D"` для `double` .
-  `"C"` для `char` .
-  `"Z"` для `bool` .
-  `"V"` для `void` метод возвращаемые типы.


<a name="_Simplified_Type_References_1" />

### <a name="simplified-type-references"></a>Упрощенное ссылок на типы

Упрощенное ссылок на типы могут использоваться только в [JNIEnv.FindClass(string)](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/(System.String)).
Существует два способа для формирования упрощенный тип ссылки:

1.  В Java полное доменное имя замените каждый `'.'` в имя пакета и перед имя типа с помощью `'/'` и каждый `'.'` внутри имени типа с `'$'` .

1.  Считывать значения на выходе `'unzip -l android.jar | grep JavaName'` .


Одно из двух приведет к типом Java [java.lang.Thread.State](http://developer.android.com/reference/java/lang/Thread.State.html) , сопоставляемого с ссылку на тип упрощенной `java/lang/Thread$State`.


### <a name="type-references"></a>Ссылки на типы

Ссылка на тип является ссылкой встроенного типа или ссылка на тип упрощенной с `'L'` префикс и `';'` суффикс. Для типа Java [java.lang.String](http://developer.android.com/reference/java/lang/String.html), является ссылка на тип упрощенной `"java/lang/String"`, а ссылка на тип `"Ljava/lang/String;"`.

Ссылки на типы используются со ссылками на тип массива, так и с подписями JNI.

Дополнительный способ для получения ссылки типа является, считывая выходные данные `'javap -s -classpath android.jar fully.qualified.Java.Name'`.
В зависимости от типа сложной, можно использовать объявление конструктора или метода типа, чтобы определить имя JNI. Пример:

```shell
$ javap -classpath android.jar -s java.lang.Thread.State
Compiled from "Thread.java"
```

```java
public final class java.lang.Thread$State extends java.lang.Enum{
public static final java.lang.Thread$State NEW;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State RUNNABLE;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State BLOCKED;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State WAITING;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State TIMED_WAITING;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State TERMINATED;
  Signature: Ljava/lang/Thread$State;
public static java.lang.Thread$State[] values();
  Signature: ()[Ljava/lang/Thread$State;
public static java.lang.Thread$State valueOf(java.lang.String);
  Signature: (Ljava/lang/String;)Ljava/lang/Thread$State;
static {};
  Signature: ()V
}
```

`Thread.State` является типом перечисления Java, чтобы мы могли использовать подпись `valueOf` метод для определения, что ссылка на тип $состояние Ljava/lang/потока;.



### <a name="array-type-references"></a>Массив ссылок на типы

Ссылки на тип массива — это `'['` в качестве префикса для типа ссылки JNI.
Упрощенное ссылок на типы нельзя использовать при указании массивов.

Например `int[]` — `"[I"`, `int[][]` — `"[[I"`, и `java.lang.Object[]` является `"[Ljava/lang/Object;"`.



## <a name="java-generics-and-type-erasure"></a>Универсальные типы Java и стирания типа

*Большинство* времени, в JNI, универсальные шаблоны Java *не существуют*.
Существуют некоторые «Морщины», но эти Морщины находятся в взаимодействие Java с помощью универсальных шаблонов, не имеющая ищет и вызывает универсальными элементами JNI.

При работе через JNI, нет никакой разницы между универсального типа или члена и неуниверсального типа или члена. Например, универсальный тип [java.lang.Class&lt;T&gt; ](http://developer.android.com/reference/java/lang/Class.html) также является «сырой» универсальным типом `java.lang.Class`, оба из которых имеют одну и ту же ссылку упрощенного типа, `"java/lang/Class"`.


## <a name="java-native-interface-support"></a>Поддержка собственного интерфейса Java

[Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/) является управляемой оболочки для Jave собственный интерфейс (JNI). Функции JNI, объявляются в [спецификации собственного интерфейса Java](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html), хотя методы были изменены для удаления явный `JNIEnv*` параметр и `IntPtr` используется вместо `jobject`, `jclass`, `jmethodID`и т. д. Например, рассмотрим [JNI NewObject функция](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp4517):

```csharp
jobject NewObjectA(JNIEnv *env, jclass clazz, jmethodID methodID, jvalue *args);
```

Это указывается в виде [JNIEnv.NewObject](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewObject/p/System.IntPtr/System.IntPtr/Android.Runtime.JValue[]/) метод:

```csharp
public static IntPtr NewObject(IntPtr clazz, IntPtr jmethod, params JValue[] parms);
```

Преобразование между вызовами сравнительно проста. В C будет иметь:

```c
jobject CreateMapActivity(JNIEnv *env)
{
    jclass    Map_Class   = (*env)->FindClass(env, "mono/samples/googlemaps/MyMapActivity");
    jmethodID Map_defCtor = (*env)->GetMethodID (env, Map_Class, "<init>", "()V");
    jobject   instance    = (*env)->NewObject (env, Map_Class, Map_defCtor);

    return instance;
}
```

C# Эквивалентное бы:

```csharp
IntPtr CreateMapActivity()
{
    IntPtr Map_Class   = JNIEnv.FindClass ("mono/samples/googlemaps/MyMapActivity");
    IntPtr Map_defCtor = JNIEnv.GetMethodID (Map_Class, "<init>", "()V");
    IntPtr instance    = JNIEnv.NewObject (Map_Class, Map_defCtor);

    return instance;
}
```

Получив экземпляр объекта Java, в объект IntPtr, вы, вероятно, хотите что-то делать. Например, можно использовать методы JNIEnv [ <span class="external">JNIEnv.CallVoidMethod()</span> ](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallVoidMethod/p/System.IntPtr/System.IntPtr/Android.Runtime.JValue[]/) чтобы сделать это, однако при наличии уже аналогичен C# программы-оболочки, то вы захотите создать оболочку по ссылке на JNI. Можно сделать это с помощью [Extensions.JavaCast <t>()</t> ](https://developer.xamarin.com/api/member/Android.Runtime.Extensions.JavaCast%7BTResult%7D/p/Android.Runtime.IJavaObject/) метод расширения:

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = new Java.Lang.Object(lrefActivity, JniHandleOwnership.TransferLocalRef)
    .JavaCast<Activity>();
```

Можно также использовать [Java.Lang.Object.GetObject <t>()</t> ](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) метод:

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = Java.Lang.Object.GetObject<Activity>(lrefActivity, JniHandleOwnership.TransferLocalRef);
```

Кроме того, все функции JNI были изменены, удалив `JNIEnv*` параметр присутствует в каждой функции JNI.


## <a name="summary"></a>Сводка

Работа с JNI напрямую — это ужасно, следует избегать любой ценой. К сожалению это не всегда вызывает; Будем надеяться, что в данном руководстве представлены помощь при достижении несвязанного вариантов Java с Mono для Android.


## <a name="related-links"></a>Связанные ссылки

- [SanityTests (пример)](https://developer.xamarin.com/samples/SanityTests/)
- [Спецификация Java Native Interface](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/jniTOC.html)
- [Функции собственного интерфейса Java](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)
