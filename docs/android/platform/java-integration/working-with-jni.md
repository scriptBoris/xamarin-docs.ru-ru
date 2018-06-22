---
title: Работа с JNI
description: Xamarin.Android разрешает создание приложений Android в C# вместо Java. Некоторые сборки предоставляется Xamarin.Android, предоставляющие привязок для библиотеки Java, включая Mono.Android.dll и Mono.Android.GoogleMaps.dll. Однако привязки для каждой возможности библиотеки Java не предоставляются и привязок, предоставляемых не может выполнить привязку, все типы Java и члены. Использование несвязанного Java типы и члены, могут использоваться собственного интерфейса Java (JNI). В этой статье показано, как использовать JNI для взаимодействия с Java типов и членов из Xamarin.Android приложений.
ms.prod: xamarin
ms.assetid: A417DEE9-7B7B-4E35-A79C-284739E3838E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: 4b5874a0f0e4289201f68299e2e37660cabc9ecf
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30774178"
---
# <a name="working-with-jni"></a>Работа с JNI

_Xamarin.Android разрешает создание приложений Android в C# вместо Java. Некоторые сборки предоставляется Xamarin.Android, предоставляющие привязок для библиотеки Java, включая Mono.Android.dll и Mono.Android.GoogleMaps.dll. Однако привязки для каждой возможности библиотеки Java не предоставляются и привязок, предоставляемых не может выполнить привязку, все типы Java и члены. Использование несвязанного Java типы и члены, могут использоваться собственного интерфейса Java (JNI). В этой статье показано, как использовать JNI для взаимодействия с Java типов и членов из Xamarin.Android приложений._


## <a name="overview"></a>Обзор

Не всегда требуется или невозможно создать управляемый вызываемой оболочки (MCW) для вызова кода Java. Во многих случаях «inline» JNI вполне допустимо и полезна для одноразовых использование несвязанного членов Java. Часто бывает проще использовать JNI для вызова одного метода в класс Java, чем для создать привязку всей .jar.

Предоставляет Xamarin.Android `Mono.Android.dll` сборку, которая обеспечивает привязку для Android `android.jar` библиотеки. Типы и члены отсутствуют в `Mono.Android.dll` и типы, не присутствующих в `android.jar` может использоваться при привязке их вручную. Чтобы привязать Java типов и членов, используйте **Java Native Interface** (**JNI**) для поиска типов, чтения и записи поля и вызова методов.

По существу, очень похоже на API-интерфейса JNI Xamarin.Android `System.Reflection` API в .NET: она позволяет искать типы и члены по имени, читать и записывать значения полей, вызывать методы и многое другое. Можно использовать JNI и `Android.Runtime.RegisterAttribute` настраиваемого атрибута для объявления виртуальные методы, которые могут быть привязаны к поддерживает переопределение. Интерфейсы можно привязать, чтобы они могут быть реализованы в C#.

В этом документе описаны:

-  Как JNI ссылается на типы.
-  Способ поиска, чтения и записи поля.
-  Способы поиска и вызова методов.
-  Как предоставить виртуальные методы, позволяющие переопределения из управляемого кода.
-  Как предоставить интерфейсы.



## <a name="requirements"></a>Требования

JNI, как предоставляются через [Android.Runtime.JNIEnv имен](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/), доступные в каждой версии Xamarin.Android.
Чтобы привязать Java типы и интерфейсы, необходимо использовать Xamarin.Android 4.0 или более поздней версии.


## <a name="managed-callable-wrappers"></a>Управляемые вызываемой оболочки

Объект **управляемых вызываемой оболочки** (**MCW**) является *привязки* для Java-класса или интерфейса, который мы и добрались до всех механизмы JNI, чтобы не нужно беспокоиться о код клиента C# Базовый сложность JNI. Большинство `Mono.Android.dll` состоит из управляемых вызываемых оболочек.

Управляемый вызываемых оболочек выполняет две функции:

1.  Инкапсулируют используйте JNI, таким образом, чтобы клиентский код не нужно знать об общей сложности.
1.  Делают возможным вложенным классом типов Java и реализовать интерфейсы Java.

Первый предназначен исключительно для удобства и инкапсуляции сложности таким образом, что потребители простой, управляемый набор классов для использования. Это требует использования различных [JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/) элементов как описано далее в этой статье. Имейте в виду, что управляемые вызываемых оболочек не являются строго обязательными &ndash; «inline», используйте JNI, полностью приемлемо и полезна для одноразовых использование несвязанного членов Java. Реализация подкласса и интерфейса необходимо использовать управляемые вызываемой оболочки.



## <a name="android-callable-wrappers"></a>Android с помощью вызываемых оболочек

Android с помощью вызываемых оболочек (ACW) необходимы в Android (ГРАФИКА) среда выполнения должна вызывать управляемый код; Эти оболочки являются обязательными, так как нет возможности для регистрации классов с РИСУНКА во время выполнения.
(В частности, [DefineClass](http://docs.oracle.com/javase/6/docs/technotes/guides/jni/spec/functions.html#wp15986) JNI функция не поддерживается средой выполнения Android. Android с помощью вызываемых оболочек таким образом составляют для отсутствия поддержки регистрации типа времени выполнения.)

Каждый раз, когда Android код должен выполнять виртуальным или интерфейс метод, который является переопределяемые или реализуемые в управляемом коде, Xamarin.Android необходимо указать учетную запись-посредник Java, чтобы этот метод возвращает отправлен соответствующий управляемый тип. Эти типы прокси-сервера Java имеют код Java, которое «же» базовый класс и список интерфейса Java управляемого типа реализации же конструкторов и объявление переопределенного базового класса и методов интерфейса.

Android с помощью вызываемых оболочек, созданные **monodroid.exe** при включенном [процесс построения](~/android/deploy-test/building-apps/build-process.md)и создаются для всех типов, которые наследуют (прямо или косвенно) [ Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/).



### <a name="implementing-interfaces"></a>Реализация интерфейсов

Бывают случаи, когда необходимо реализовать интерфейс Android (такие как [Android.Content.IComponentCallbacks](https://developer.xamarin.com/api/type/Android.Content.IComponentCallbacks/)).

Все Android классы и интерфейсы расширения [Android.Runtime.IJavaObject](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/) интерфейс; таким образом, необходимо реализовать все типы Android `IJavaObject`.
Xamarin.Android используется это обстоятельство &ndash; используется `IJavaObject` для предоставления Android Java прокси-сервера (Android вызываемая оболочка) для данного управляемого типа. Поскольку **monodroid.exe** осуществляет только поиск `Java.Lang.Object` подклассы (который должен реализовывать `IJavaObject`), работа с подклассами `Java.Lang.Object` предоставляет способ реализации интерфейсов в управляемом коде. Пример:

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

*В оставшейся части этой статьи предоставляет сведения о реализации изменяться без предварительного уведомления* (и представленные здесь только потому, что разработчики могут быть интересно, что происходит за кулисами).

Например при наличии исходного кода C# следующие:

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

**Mandroid.exe** программа создаст следующие Android вызываемой оболочки:

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

Обратите внимание, сохраняется базовый класс, и объявлениях метода машинного кода предоставляются для каждого метода, которая переопределяется в управляемом коде.



### <a name="exportattribute-and-exportfieldattribute"></a>ExportAttribute и ExportFieldAttribute

Как правило Xamarin.Android автоматически создает код Java, который состоит из ACW; Это поколение основан на имена классов и методов, если класс является производным от класса Java и переопределяет существующие методы Java. Однако в некоторых случаях создание кода недостаточна, как показано ниже:

-   Android поддерживает имена действий в атрибуты XML, макет, например [android: onClick](https://developer.xamarin.com/api/member/Android.Views.View+IOnClickListener.OnClick/p/Android.Views.View/) атрибута XML. Если указано, увеличенную представление экземпляра пытается найти метод Java.

-   [Java.io.Serializable](http://developer.android.com/reference/java/io/Serializable.html) интерфейс требует `readObject` и `writeObject` методы. Так как они не являются членами этого интерфейса, наши соответствующие управляемой реализации не предоставляет эти методы в код Java.

-   [Android.os.Parcelable](https://developer.xamarin.com/api/type/Android.Os.Parcelable/) интерфейс ожидает, что класс реализации должен иметь статическое поле `CREATOR` типа `Parcelable.Creator`. Созданный код Java требует некоторых явного поля. С нашей стандартные сценарии нет возможности для вывода значений в коде Java из управляемого кода.


Так как создание кода не предоставляет решение для создания произвольных методов Java, имеющие произвольные имена, начиная с версии 4.2 Xamarin.Android, [ExportAttribute](https://developer.xamarin.com/api/type/Java.Interop.ExportAttribute/) и [ExportFieldAttribute](https://developer.xamarin.com/api/type/Java.Interop.ExportFieldAttribute/) были предоставляет решение для указанных выше сценариев. Оба атрибута находятся в `Java.Interop` пространство имен:

-   `ExportAttribute` &ndash; Задает имя метода и его ожидаемого исключения типов (чтобы предоставить явные «вызывает» в Java). При использовании метода, метод будет «export» Java метод, создающий код диспетчеризации, соответствующим вызовом JNI в управляемый метод. Он может использоваться с `android:onClick` и `java.io.Serializable`.

-   `ExportFieldAttribute` &ndash; Указывает имя поля. Он располагается в методе, который работает в качестве инициализатора поля. Он может использоваться с `android.os.Parcelable`.

[ExportAttribute](https://developer.xamarin.com/samples/monodroid/ExportAttribute/) образец проекта описывается использование этих атрибутов.


#### <a name="troubleshooting-exportattribute-and-exportfieldattribute"></a>Устранение неполадок ExportAttribute и ExportFieldAttribute

-   Упаковка завершается с ошибкой из-за отсутствия **Mono.Android.Export.dll** &ndash; при использовании `ExportAttribute` или `ExportFieldAttribute` для некоторых методов в коде или зависимые библиотеки, необходимо добавить  **Mono.Android.Export.dll**. Эта сборка является изолированной для поддержки обратный вызов кода из Java. Отделен от **Mono.Android.dll** как добавляет дополнительный объем в приложение.

-   В сборке выпуска `MissingMethodException` происходит для методов экспорта &ndash; сборки в выпуске `MissingMethodException` происходит для методов экспорта. (Эта проблема исправлена в последней версии Xamarin.Android).



### <a name="exportparameterattribute"></a>ExportParameterAttribute

`ExportAttribute` и `ExportFieldAttribute` предоставляют функциональные возможности, Java, можно использовать во время выполнения кода. Этот код во время выполнения обращается к управляемому коду через созданные методы JNI, обусловленных этих атрибутов. В результате отсутствует существующий метод Java, связывающую управляемого метода; Таким образом метод Java создается на основе Сигнатура управляемого метода.

Однако этот вариант не полностью определителя. В частности это касается некоторые расширенные сопоставления между управляемые типы и типы Java, таких как:

-  InputStream
-  Свойство OutputStream
-  XmlPullParser
-  XmlResourceParser

Если необходима для экспортированных методов типов такие `ExportParameterAttribute` следует явно предоставить соответствующего параметра или возвращаемого значения типа.



### <a name="annotation-attribute"></a>Аннотации атрибута

Преобразования в Xamarin.Android 4.2 `IAnnotation` типы реализации в атрибуты (System.Attribute), а также добавлена поддержка создания заметки в Java оболочки.

Это означает направления следующие изменения:

-   Создает генератор привязки `Java.Lang.DeprecatedAttribute` из `java.Lang.Deprecated` (хотя он должен быть `[Obsolete]` в управляемом коде).

-   Это не означает, что существующие `Java.Lang.Deprecated` класс исчезнет. Эти объекты на основе Java может по-прежнему использоваться как обычные объекты Java (если существует такое использование). Будет существовать `Deprecated` и `DeprecatedAttribute` классы.

-   `Java.Lang.DeprecatedAttribute` Класс помечен как `[Annotation]` . Если настраиваемый атрибут, который наследуется от этого `[Annotation]` атрибут, задача msbuild создаст аннотацию Java для этого настраиваемого атрибута (@Deprecated) в Android вызываемой оболочки (ACW).

-   Заметки могут быть созданы на классы, методы и экспортировать поля (что метода в управляемом коде).

Если для содержащего класса (заметками самого класса или класса, содержащего элементы заметками) не зарегистрирован, всей исходной класс Java не создается вообще, вместе с примечаниями. Для методов, можно указать `ExportAttribute` для которого вызывается метод явно создается и заметками. Кроме того он не является компонентом «создать» определения класса Java заметки. Другими словами при определении настраиваемого атрибута для определенных заметки в виде управляемого, вам придется добавить другой .jar библиотеку, содержащую класс соответствующие заметки Java. Добавление файла источника Java, который определяет тип заметки не является достаточным. Компилятор Java не работает так же, как **apt**.

Кроме того применяются следующие ограничения:

-   Процесс преобразования не учитывает `@Target` заметки на тип заметки к текущему моменту.

-   Атрибуты на свойство не работает. Вместо этого используйте для получения значения свойства или задания атрибутов.



## <a name="class-binding"></a>Класс привязки

Привязка класс означает написание управляемого вызываемую оболочку для упрощения вызова базового типа Java.

Связывание виртуальные и абстрактные методы, чтобы разрешить переопределение в C# требует Xamarin.Android 4.0. Тем не менее любой версии Xamarin.Android можно привязать невиртуальных методов, статические методы или виртуальные методы без поддержки переопределений.

Обычно привязка содержит следующие элементы:

-  Объект [JNI дескриптор типом Java, привязанной](#_Looking_up_Java_Types).

-  [Поле JNI, идентификаторы и свойства для каждого связанного поля](#_Instance_Fields).

-  [Идентификаторы метод JNI и методы для каждого привязанного метод](#_Instance_Methods).

-  Если требуется подкласса, тип должен иметь [RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/) настраиваемым атрибутом в объявлении типа [RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/) значение `true`.



### <a name="declaring-type-handle"></a>Объявляющий тип дескриптора

Поле и метод методы поиска требуется ссылка на объект, ссылающиеся на их объявляющего типа. По соглашению, это удерживается в `class_ref` поля:

```csharp
static IntPtr class_ref = JNIEnv.FindClass(CLASS);
```

В разделе [JNI ссылок на типы](#_JNI_Type_References) статьи подробные сведения о `CLASS` токена.


### <a name="binding-fields"></a>Привязка полей

Поля Java в виде свойств C#, например поле Java [java.lang.System.in](http://developer.android.com/reference/java/lang/System.html#in) привязан как свойство C# [Java.Lang.JavaSystem.In](https://developer.xamarin.com/api/property/Java.Lang.JavaSystem.In/).
Кроме того поскольку JNI различает статические поля и поля экземпляров, различные методы использовать при реализации свойства.

Привязка поля включает в себя три набора методов:

1.  *Получить идентификатор поля* метод. *Получить идентификатор поля* метод отвечает за возврат обработки поля *получить значение поля* и *задать значение поля* методы используют. Получение идентификатора поля необходимо знать, объявляемый тип, имя поля и [JNI сигнатура типа](#_JNI_Type_Signatures) поля.

1.  *Получить значение поля* методы. Эти методы требуют дескриптора поля и отвечают за чтение значение поля из Java.
    Метод зависит от типа поля.

1.  *Задать значение поля* методы. Эти методы требуют дескриптора поля и отвечает за запись в поле значение в Java. Метод зависит от типа поля.


 [Статические поля](#_Static_Fields) использовать [JNIEnv.GetStaticFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/), `JNIEnv.GetStatic*Field`, и [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/) методы.

 [Поля экземпляра](#_Instance_Fields) использовать [JNIEnv.GetFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFieldID/), `JNIEnv.Get*Field`, и [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/) методы.

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

Примечание: Мы используем [InputStreamInvoker.FromJniHandle](https://developer.xamarin.com/api/member/Android.Runtime.InputStreamInvoker.FromJniHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership)) для преобразования ссылок в JNI `System.IO.Stream` используется экземпляр и мы `JniHandleOwnership.TransferLocalRef` из-за [JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/) Возвращает локальную ссылку.

Многие [Android.Runtime](https://developer.xamarin.com/api/namespace/Android.Runtime/) типы имеют `FromJniHandle` ссылаются на методы, которые преобразует JNI в требуемый тип.



### <a name="method-binding"></a>Метод привязки

Методы Java, предоставляются как методы C# и как свойств C#. Например, метод Java [java.lang.Runtime.runFinalizersOnExit](http://developer.android.com/reference/java/lang/Runtime.html#runFinalizersOnExit(boolean)) привязывается к метод [Java.Lang.Runtime.RunFinalizersOnExit](https://developer.xamarin.com/api/member/Java.Lang.Runtime.RunFinalizersOnExit/) метода и [java.lang.Object.getClass ](http://developer.android.com/reference/java/lang/Object.html#getClass) привязывается к метод [Java.Lang.Object.Class](https://developer.xamarin.com/api/property/Java.Lang.Object.Class/) свойство.

При вызове метода происходит в два этапа:

1.  *Получить идентификатор метода* для вызова метода. *Получить идентификатор метода* метод отвечает за возврат дескриптор метода, который будет использовать методы вызова метода. Для получения идентификатор метода требуется знание объявляемый тип, имя метода и [сигнатура типа JNI](#_JNI_Type_Signatures) метода.

1.  Вызовите метод.

Как и в случае поля, методы, используемые для получения идентификатора метода и вызывает метод различаются статических методов и методов экземпляра.

[Статические методы](#_Static_Methods_1) использовать [JNIEnv.GetStaticMethodID()](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/) поиск id метод и использовать `JNIEnv.CallStatic*Method` семейство методов для вызова.

[Методы экземпляра](#_Instance_Methods) использовать [JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/) поиск id метод и использовать `JNIEnv.Call*Method` и `JNIEnv.CallNonvirtual*Method` семейств методов для вызова.

Метод привязки является потенциально больше, чем просто вызов метода. Метод привязки также включает метод для переопределения (для методов абстрактным и неконечного) или реализуется (для методов интерфейса). [Поддержки наследование, интерфейсы](#_Supporting_Inheritance,_Interfaces_1) раздел охватывает сложность работы с поддержкой виртуальных методов и методов интерфейса.

<a name="_Static_Methods_1" />

#### <a name="static-methods"></a>Статические методы

Привязка статический метод предполагает использование `JNIEnv.GetStaticMethodID` для получения дескриптора метода, затем с помощью соответствующей `JNIEnv.CallStatic*Method` метод в зависимости от типа возвращаемого значения метода. Ниже приведен пример привязки для [Runtime.getRuntime](http://developer.android.com/reference/java/lang/Runtime.html#getRuntime()) метод:

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

Обратите внимание, что мы хранения дескриптора метода в статическом поле `id_getRuntime`. Это оптимизирует производительность дескриптора метода не нужно искать при каждом вызове. Кэш дескриптора метода в этом случае необязательно. После получения дескриптора метода [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) используется для вызова метода. `JNIEnv.CallStaticObjectMethod` Возвращает `IntPtr` , содержащее дескриптор, возвращенный экземпляр Java.
[Java.Lang.Object.GetObject&lt;T&gt;(IntPtr, JniHandleOwnership)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) используется для преобразования в экземпляр строго типизированный объект дескриптора Java.



#### <a name="non-virtual-instance-method-binding"></a>Экземпляр невиртуальный метод привязки

Привязка `final` метод экземпляра, или метод экземпляра, не требующий переопределения, предполагает использование `JNIEnv.GetMethodID` для получения дескриптора метода, затем с помощью соответствующей `JNIEnv.Call*Method` метод в зависимости от типа возвращаемого значения метода. Ниже приведен пример привязки для `Object.Class` свойства:

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

Обратите внимание, что мы хранения дескриптора метода в статическом поле `id_getClass`.
Это оптимизирует производительность дескриптора метода не нужно искать при каждом вызове. Кэш дескриптора метода в этом случае необязательно. После получения дескриптора метода [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) используется для вызова метода. `JNIEnv.CallStaticObjectMethod` Возвращает `IntPtr` , содержащее дескриптор, возвращенный экземпляр Java.
[Java.Lang.Object.GetObject&lt;T&gt;(IntPtr, JniHandleOwnership)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) используется для преобразования в экземпляр строго типизированный объект дескриптора Java.


### <a name="binding-constructors"></a>Конструкторы привязки

Конструкторы — это методы Java с именем `"<init>"`. Как и в случае с методами экземпляра Java, `JNIEnv.GetMethodID` используется для поиска дескриптора конструктора. В отличие от методов Java [JNIEnv.NewObject](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewObject/) методы используются для вызова конструктора дескриптор метода. Возвращаемое значение `JNIEnv.NewObject` JNI локальная ссылка:


```csharp
int value = 42;
IntPtr class_ref    = JNIEnv.FindClass ("java/lang/Integer");
IntPtr id_ctor_I    = JNIEnv.GetMethodID (class_ref, "<init>", "(I)V");
IntPtr lrefInstance = JNIEnv.NewObject (class_ref, id_ctor_I, new JValue (value));
// Dispose of lrefInstance, class_ref…
```

Обычно класс привязки будет подкласс [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/).
Для подклассов `Java.Lang.Object`, дополнительные семантической вступает в действие: `Java.Lang.Object` экземпляр сохраняет ссылку к экземпляру Java через глобальный `Java.Lang.Object.Handle` свойство.

1.  `Java.Lang.Object` Конструктор по умолчанию приведет к выделению экземпляр Java.

1.  Если тип имеет `RegisterAttribute` , и `RegisterAttribute.DoNotGenerateAcw` — `true` , затем экземпляр `RegisterAttribute.Name` тип создается с помощью его конструктора по умолчанию.

1.  В противном случае [Android вызываемой оболочки](~/android/platform/java-integration/android-callable-wrappers.md) (ACW), соответствующий `this.GetType` создается с помощью его конструктора по умолчанию. Android с помощью вызываемых оболочек создаются во время создания пакета для каждого `Java.Lang.Object` подкласс, для которого `RegisterAttribute.DoNotGenerateAcw` не задано значение `true`.

Для типов, которые являются не класса привязки, это ожидаемая семантической: при создании экземпляра `Mono.Samples.HelloWorld.HelloAndroid` экземпляр C# следует создавать Java `mono.samples.helloworld.HelloAndroid` экземпляра созданного Android вызываемую оболочку.

Для класса привязки это может быть правильное поведение, если Java тип содержит конструктор по умолчанию и/или других конструктор не должен быть вызван. В противном случае конструктор должно быть указано которого выполняет следующие действия:

1.  Вызов [Java.Lang.Object (IntPtr, JniHandleOwnership)](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) вместо значения по умолчанию `Java.Lang.Object` конструктор. Это необходимо, чтобы избежать создания экземпляра Java.

1.  Проверьте значение [Java.Lang.Object.Handle](https://developer.xamarin.com/api/property/Java.Lang.Object.Handle/) перед созданием экземпляра любой Java. `Object.Handle` Свойство будет иметь значение, отличное от `IntPtr.Zero` Если Android вызываемой оболочки был создан в коде Java и привязки класса создается для размещения созданного экземпляра Android вызываемой оболочки. Например, когда Android создает `mono.samples.helloworld.HelloAndroid` экземпляра Android вызываемой оболочки будут создаваться первыми, а Java `HelloAndroid` конструктора создать экземпляр соответствующего `Mono.Samples.HelloWorld.HelloAndroid` типа, с `Object.Handle` свойство устанавливается перед выполнением конструктора экземпляра Java.

1.  Если текущий тип среды выполнения не является таким же, как объявляемый тип, а затем экземпляр соответствующего Android вызываемой оболочки необходимо создать и использовать [Object.SetHandle](https://developer.xamarin.com/api/member/Java.Lang.Object.SetHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership)) для хранения дескриптор, возвращенный [ JNIEnv.CreateInstance](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CreateInstance/).

1.  Если текущий тип среды выполнения, то же, что объявляющий тип вызова конструктора Java и использовать [Object.SetHandle](https://developer.xamarin.com/api/member/Java.Lang.Object.SetHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership)) для хранения дескриптор, возвращенный `JNIEnv.NewInstance` .


Например, рассмотрим [java.lang.Integer(int)](http://developer.android.com/reference/java/lang/Integer.html#Integer(int)) конструктор. Это привязывается к:

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

[JNIEnv.CreateInstance](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CreateInstance/) методы являются вспомогательные методы для выполнения `JNIEnv.FindClass`, `JNIEnv.GetMethodID`, `JNIEnv.NewObject`, и `JNIEnv.DeleteGlobalReference` по значению, возвращенному из `JNIEnv.FindClass`. См. подробные сведения в следующем подразделе.

<a name="_Supporting_Inheritance,_Interfaces_1" />

### <a name="supporting-inheritance-interfaces"></a>Для поддержки наследование, интерфейсы

Создание подклассов типа Java или реализации интерфейса Java требует создания [Android с помощью вызываемых оболочек](~/android/platform/java-integration/android-callable-wrappers.md) (ACWs), которые создаются для каждого `Java.Lang.Object` подкласс процессе упаковки. Создание ACW осуществляется с помощью [Android.Runtime.RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/) настраиваемого атрибута.

Для типов C# `[Register]` конструктор настраиваемого атрибута требует один аргумент: [ссылку на тип, упрощенное письмо JNI](#_Simplified_Type_References_1) Java соответствующего типа. Это позволяет, предоставляя различные имена между Java и C#.

До Xamarin.Android 4.0 `[Register]` настраиваемого атрибута был недоступен в слово «alias» существующие типы Java. Это, поскольку процесс создания ACW вызовет ACWs для каждого `Java.Lang.Object` обнаружил подкласс.

Xamarin.Android 4.0 были введены [RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/) свойство. Это свойство указывает, что процесс создания ACW для *пропустить* заметками типа, позволяя объявление новых управляемых с помощью вызываемых оболочек, не приводит к ACWs, создаваемого во время создания пакета. Это позволяет Привязка существующих типов Java. Например, рассмотрим следующий простой класс Java, `Adder`, который содержит один метод `add`, добавляет в целые числа и возвращает результат:

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

Здесь `Adder` тип C# *псевдонимы* `Adder` типа Java. `[Register]` Атрибут используется для указания имени JNI `mono.android.test.Adder` типа Java и `DoNotGenerateAcw` свойство используется для предотвращения создания ACW. Это приведет к созданию ACW для `ManagedAdder` типа — правильно подклассов `mono.android.test.Adder` типа. Если `RegisterAttribute.DoNotGenerateAcw` свойство еще не использовался, то будет создан новый процесс построения Xamarin.Android `mono.android.test.Adder` типа Java. Это приведет к ошибкам компиляции как `mono.android.test.Adder` тип будет присутствовать дважды в двух отдельных файлах.



### <a name="binding-virtual-methods"></a>Виртуальные методы привязки

`ManagedAdder` подклассы Java `Adder` типа, но он не особенно интересны: C# `Adder` типа не определяет любые виртуальные методы, поэтому `ManagedAdder` не может переопределить ничего.

Привязка `virtual` методы, чтобы разрешить переопределение подклассами требует, чтобы несколько вещей, которые необходимо выполнить которого делятся на две категории:

1.  **Метод привязки**

1.  **Метод регистрации**


#### <a name="method-binding"></a>Метод привязки

Метод привязка требует добавления двух членов поддержки в C# `Adder` определение: `ThresholdType`, и `ThresholdClass`.

##### <a name="thresholdtype"></a>ThresholdType

`ThresholdType` Возвращает текущий тип привязки:

```csharp
partial class Adder {
    protected override System.Type ThresholdType {
        get {
            return typeof (Adder);
        }
    }
}
```

`ThresholdType` используется в привязке метод для определения, когда следует выполнять виртуальные и невиртуальные методы распределения. Всегда должны возвращать `System.Type` экземпляр, который соответствует объявляющий тип C#.

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

`ThresholdClass` используется в привязке метод при вызове невиртуальных методов.

#### <a name="binding-implementation"></a>Реализация привязки

Реализация метода привязки отвечает за вызов среды выполнения Java метода. Он также содержит `[Register]` объявление настраиваемого атрибута, которое является частью метода регистрации и обсуждаются в разделе метод регистрации:

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

`id_add` Поле содержит идентификатор метода для метода Java для вызова. `id_add` Значение получается из `JNIEnv.GetMethodID`, что требует объявляющего класса (`class_ref`), имя метода Java (`"add"`) и подпись метода JNI (`"(II)I"`).

После получения идентификатор метода `GetType` сравнивается с `ThresholdType` для определения необходимости виртуальные и невиртуальные диспетчеризации. Виртуальный диспетчеризации является обязательным, если `GetType` соответствует `ThresholdType`, как `Handle` может ссылаться на подкласс выделенной Java, который переопределяет метод.

Когда `GetType` не соответствует `ThresholdType`, `Adder` подклассами (например, по `ManagedAdder`) и `Adder.Add` реализации будет вызываться, только если вызван подкласса `base.Add`. Это условие выполняется невиртуальный диспетчеризации, место, куда `ThresholdClass` поставляется в. `ThresholdClass` Указывает, какой класс Java будет предоставить реализацию метода для вызова.



#### <a name="method-registration"></a>Метод регистрации

Предположим, имеется обновленное `ManagedAdder` определение переопределяющую `Adder.Add` метод:

```csharp
partial class ManagedAdder : Adder {
    public override int Add (int a, int b) {
        return (a*2) + (b*2);
    }
}
```

Помните, что `Adder.Add` бы `[Register]` настраиваемых атрибутов:

```csharp
[Register ("add", "(II)I", "GetAddHandler")]
```

`[Register]` Настраиваемого атрибута конструктор принимает три значения:

1.  Имя метода Java, `"add"` в этом случае.

1.  Сигнатура типов метода, JNI `"(II)I"` в этом случае.

1.  *Метод соединитель* , `GetAddHandler` в этом случае.
    Соединитель методы будут описаны ниже.


Первые два параметра разрешает процесс создания ACW создать объявление метода для переопределения этого метода. Полученный ACW должен содержать следующий код:

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

Обратите внимание, что `@Override` объявлен метод, который делегирует `n_`-метод с тем же именем в качестве префикса. Это убедитесь, что при вызове кода Java `ManagedAdder.add`, `ManagedAdder.n_add` будет вызываться, что позволит переопределения C# `ManagedAdder.Add` выполняемый метод.

Таким образом, наиболее важный вопрос: как будет `ManagedAdder.n_add` подключается к `ManagedAdder.Add`?

Java `native` методы зарегистрированы с помощью среды выполнения Java (Android среда выполнения) через [JNI RegisterNatives функция](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp17734).
`RegisterNatives` принимает массив структур, содержащих имя метода Java, сигнатура типа JNI и указатель на функцию для вызова неуправляемого кода, следующего [JNI, соглашение о вызовах](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/design.html#wp715).
Указатель функции должен быть функцией, принимающей два аргумента указателя, следуют параметры метода. Java `ManagedAdder.n_add` метод должен быть реализован через функцию, которая имеет следующий прототип C:

```csharp
int FunctionName(JNIEnv *env, jobject this, int a, int b)
```

Не предоставляет Xamarin.Android `RegisterNatives` метод. Вместо этого ACW и MCW вместе содержат сведения, необходимые для вызова `RegisterNatives`: ACW содержит имя метода и сигнатура типа JNI, только что отсутствует является указатель на функцию, чтобы подключить.

Это место, куда *метод соединитель* поставляется в. Третий `[Register]` параметр настраиваемого атрибута является имя метода, определенного в зарегистрированного типа или базового класса зарегистрированного типа, который не принимает параметры и возвращает `System.Delegate`. Возвращенный `System.Delegate` в свою очередь ссылается на метод, который имеет правильной сигнатуры функции JNI. Наконец, делегат, который возвращает метод соединитель *должен* корнем, чтобы сборщик Мусора не получить их, как делегат предоставляется для Java.

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
`JNINativeWrapper.CreateDelegate` Создает оболочку для указанного метода в блок try/catch, чтобы все необработанные исключения, обрабатываются и приведет к вызов [AndroidEvent.UnhandledExceptionRaiser](https://developer.xamarin.com/api/event/Android.Runtime.AndroidEnvironment.UnhandledExceptionRaiser/) событий. Результирующий делегат хранится в статических `cb_add` переменной, чтобы сборщик Мусора не освобождает делегат.

Наконец `n_Add` метод отвечает за маршалинг параметров JNI для соответствующих управляемых типов, а затем вызовите метод делегирование.

Примечание: Всегда использовать `JniHandleOwnership.DoNotTransfer` при получении MCW через экземпляр Java. Рассматривая их как локальную ссылку (и тем самым вызывая `JNIEnv.DeleteLocalRef`) нарушит управляемый -&gt; Java -&gt; управляемого стека переходов.



### <a name="complete-adder-binding"></a>Выполните метод добавления привязки

Полный привязки для управляемых `mono.android.tests.Adder` тип:

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

При записи типа, который соответствует следующим условиям:

1.  Подклассы `Java.Lang.Object`

1.  Имеет `[Register]` настраиваемого атрибута

1.  `RegisterAttribute.DoNotGenerateAcw` равно `true`


Затем для сборки Мусора взаимодействия тип *не должны* содержат полей, которые могут ссылаться на `Java.Lang.Object` или `Java.Lang.Object` подкласс во время выполнения. Например, поля типа `System.Object` и любой другой тип интерфейса не разрешены. Типы, которые не могут ссылаться на `Java.Lang.Object` разрешены экземпляров, таких как `System.String` и `List<int>`. Это ограничение действует для предотвращения коллекции преждевременное объекта сборщиком Мусора.

Если тип должен содержать поле экземпляра, которое может ссылаться на `Java.Lang.Object` экземпляра должно иметь тип поля `System.WeakReference` или `GCHandle`.



## <a name="binding-abstract-methods"></a>Привязка абстрактные методы

Привязка `abstract` методы мало чем отличается от привязки виртуальные методы. Существует только два различия:

1.  Абстрактный метод является абстрактным. Он по-прежнему сохраняет `[Register]` атрибут и связанный метод регистрации, привязка метод просто перемещается `Invoker` типа.

1.  Значение, отличное от `abstract` `Invoker` тип создается какие подклассов абстрактного типа. `Invoker` Тип должны переопределять все абстрактные методы, объявленные в базовом классе и переопределенная реализация является реализация метода привязки на то, что можно игнорировать регистр невиртуальный диспетчеризации.


Например, предположим, что выше `mono.android.test.Adder.add` бы метод `abstract`. Изменить привязки C#, чтобы `Adder.Add` были абстрактных и новый `AdderInvoker` типа будут определены, который реализован `Adder.Add`:

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

`Invoker` Типа необходим только при получении JNI ссылки на экземпляры, созданные Java.


## <a name="binding-interfaces"></a>Привязка интерфейсов

Привязка интерфейсов аналогичен привязки классы, содержащие виртуальные методы, но многие особенности различаются способами заметно (и не столь малозаметные). Рассмотрим следующий [объявление интерфейса Java](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/Adder.java#L14):

```csharp
public interface Progress {
    void onAdd(int[] values, int currentIndex, int currentSum);
}
```

Интерфейс привязки состоят из двух частей: определение интерфейса C# и определения вызова для интерфейса.



### <a name="interface-definition"></a>Определение интерфейса

Определение интерфейса C# необходимо выполнить следующие требования:

-   Определение интерфейса должен иметь `[Register]` настраиваемого атрибута.

-   Определение интерфейса необходимо расширить `IJavaObject interface`.
    Невыполнение этого требования сделает невозможным ACWs наследование от интерфейса Java.

-   Каждый метод интерфейса должен содержать `[Register]` атрибут, указав имя соответствующего метода Java JNI подписи и метод соединителя.

-   Метод соединителя необходимо также указать тип, метод соединителя может располагаться на.

При привязке `abstract` и `virtual` метода: метод соединитель будет выполнять поиск в иерархии наследования, типа регистрируемого. Интерфейсы могут иметь методы, не содержащий тел, поэтому это не сработает, тем самым необходимость указанного типа, указывающее, где находится метод соединителя. Тип задается в строке метод соединителя после двоеточия `':'`, и должно быть имя типа с указанием сборки, типа, содержащего средство вызова.

Объявления метода интерфейса являются перевод соответствующий метод Java с помощью *совместимых* типов. Для Java встроенные типы, совместимые типы являются соответствующие типы C#, например Java `int` C# `int`. Для ссылочных типов совместимый тип является типом, который можно предоставить дескриптор JNI соответствующего типа Java.

Члены интерфейса не будет вызываться непосредственно с Java &ndash; вызов будет осуществлялся с помощью вызова типа &ndash; , разрешенных некоторое количество гибкость.

Интерфейс Java хода выполнения может быть [объявлены в C# как](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L83):

```csharp
[Register ("mono/android/test/Adder$Progress", DoNotGenerateAcw=true)]
public interface IAdderProgress : IJavaObject {
    [Register ("onAdd", "([III)V",
            "GetOnAddHandler:Mono.Samples.SanityTests.IAdderProgressInvoker, SanityTests, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null")]
    void OnAdd (JavaArray<int> values, int currentIndex, int currentSum);
}
```

Обратите внимание, в указанных выше, мы сопоставляют Java `int[]` параметр [JavaArray&lt;int&gt;](https://developer.xamarin.com/api/type/Android.Runtime.JavaArray%601/).
Это не является обязательным: нам удалось связывания с C# `int[]`, или `IList<int>`, либо другие полностью. Любой тип выбирается, `Invoker` должна быть возможность преобразовать ее в Java `int[]` типа для вызова.


### <a name="invoker-definition"></a>Определение Invoker

`Invoker` Определение типа, должен наследовать `Java.Lang.Object`, реализуйте соответствующий интерфейс и обеспечения всех методов соединения, на которые ссылается определение интерфейса. Имеется один Дополнительные предложения, отличается от класса привязки: `class_ref` поле и метод идентификаторы должны входить в экземпляр, не статические члены.

Причина предпочтительного использования члены экземпляра связана с `JNIEnv.GetMethodID` поведения в среде выполнения с Android. (Это может быть также поведение Java, еще не прошло проверку.) `JNIEnv.GetMethodID` возвращает значение null, при поиске метод, который поступает из реализованного интерфейса и не объявленный интерфейс. Рассмотрим [java.util.SortedMap&lt;K, V&gt; ](http://developer.android.com/reference/java/util/SortedMap.html) интерфейса Java, который реализует [java.util.Map&lt;K, V&gt; ](http://developer.android.com/reference/java/util/Map.html) интерфейса. Карта предоставляет [снимите](http://developer.android.com/reference/java/util/Map.html#clear()) метод, поэтому первый взгляд разумно `Invoker` бы определение SortedMap:

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

Выше завершится сбоем, поскольку `JNIEnv.GetMethodID` вернет `null` при поиске `Map.clear` метода с помощью `SortedMap` экземпляра класса.

Существует два решения: отслеживание интерфейс, который каждый метод поступают из и иметь `class_ref` для каждого интерфейса, или держать все как члены экземпляра и выполнять поиск метода на тип класса, более низкого уровня, а не тип интерфейса. Последний выполняется в **Mono.Android.dll**.

Определение Invoker состоит из шести разделов: конструктор, `Dispose` метода `ThresholdType` и `ThresholdClass` члены, `GetObject` метод реализации метода интерфейса и реализации метода соединителя.



#### <a name="constructor"></a>Конструктор

Конструктор должен найти класс среды выполнения, вызываемого экземпляра и сохранения в экземпляре класса среды выполнения `class_ref` поля:

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

Примечание: `Handle` свойство должно использоваться в теле конструктора и не `handle` параметра, как и на Android версии 4.0 `handle` параметр может быть недопустимым по завершении конструктор базового класса.


#### <a name="dispose-method"></a>Метод Dispose

`Dispose` Метод необходимо освободить глобальной ссылки, выделенных в конструкторе:

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

`ThresholdType` И `ThresholdClass` идентичны привязке, найденной в класс привязку элементов:

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

Методы соединителя и инфраструктуры, поддерживающей отвечают за маршалинг параметров JNI для соответствующих типов C#. Java `int[]` параметра будут передаваться как JNI `jintArray`, который является `IntPtr` в C#. `IntPtr` Должны маршалироваться `JavaArray<int>` для поддержки вызова интерфейса C#:

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

Обратите внимание, что `JNIEnv.GetArray` копирует весь массив между виртуальными машинами, поэтому для больших массивов в результате большое количество добавленных нехватку глобального Каталога.


### <a name="complete-invoker-definition"></a>Завершите определение Invoker

[Завершите определение IAdderProgressInvoker](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L88):

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

Множество методов JNIEnv возвращает *JNI* *ссылки на объекты*, похожи на `GCHandle`s. JNI предоставляет три типа ссылок на объекты: локальных ссылок, ссылки на глобальные и слабых ссылок на глобальный. Все три представляются в виде `System.IntPtr`, *, но* (как описано в разделе Типы функций JNI) не все `IntPtr`, возвращаемыми `JNIEnv` методы являются ссылками. Например [JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/) возвращает `IntPtr`, но он не возвращает ссылку на объект, он возвращает `jmethodID`. Обратитесь к [документации по функциям JNI](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html) подробные сведения.

Локальные ссылки создаются с *большинство* методы создания ссылки.
Android позволяет только ограниченное число локальных ссылок должно храниться в любой момент времени, обычно 512. Локальные ссылки могут быть удалены через [JNIEnv.DeleteLocalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/).
В отличие от JNI не все ссылаются на методы JNIEnv какие возвращаемого объекта ссылки возвращают локальных ссылок; [JNIEnv.FindClass](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/) возвращает *глобальной* ссылки. Настоятельно рекомендуется удалить локальные ссылки, как можно скорее, возможно, создав [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) вокруг объекта и указав `JniHandleOwnership.TransferLocalRef` для [Java.Lang.Object (IntPtr обрабатывать передачи JniHandleOwnership)](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) конструктор.

Созданные ссылки на глобальные [JNIEnv.NewGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewGlobalRef/) и [JNIEnv.FindClass](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/).
Их можно удалить с помощью [JNIEnv.DeleteGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteGlobalRef/).
Эмуляторы иметь ограничение в 2 000 оставшихся ссылок глобального, тогда как ограничение около 52,000 ссылок на глобальный устройств.

Слабые ссылки на глобальные доступны только на Android версия 2.2 (Froyo) и более поздних версий. Слабые ссылки на глобальные можно удалить с [JNIEnv.DeleteWeakGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteWeakGlobalRef/(System.IntPtr)).


### <a name="dealing-with-jni-local-references"></a>Работа с ссылками на локальные JNI

[JNIEnv.GetObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetObjectField/), [JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/), [JNIEnv.CallObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallObjectMethod/), [JNIEnv.CallNonvirtualObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualObjectMethod/)и [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) методы возвращают `IntPtr` , содержащее локальную ссылку на объект Java JNI или `IntPtr.Zero` при возвращении Java `null`. Из-за ограниченного числа локальных ссылок, которые могут быть необработанных на после (512 записей), желательно, чтобы убедиться, что ссылки будут удалены в течение отведенного времени. Существует три способа, которые могут быть обработаны локальных ссылок: явно удалять их, создание `Java.Lang.Object` экземпляра для хранения их и используя `Java.Lang.Object.GetObject<T>()` для создания управляемого вызываемую оболочку вокруг них.



### <a name="explicitly-deleting-local-references"></a>Явное удаление локальных ссылок

[JNIEnv.DeleteLocalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/) используется для удаления локальных ссылок. После удаления ссылки на местную он больше не может использоваться, поэтому будьте внимательны, чтобы убедиться, что `JNIEnv.DeleteLocalRef` самая последняя операция над локальную ссылку.

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

`Java.Lang.Object` предоставляет [Java.Lang.Object (дескриптор IntPtr, передача JniHandleOwnership)](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) конструктор, который может использоваться для ссылки на существующие JNI по словам. [JniHandleOwnership](https://developer.xamarin.com/api/type/Android.Runtime.JniHandleOwnership/) параметр определяет, как `IntPtr` следует рассматривать параметра:

-   [JniHandleOwnership.DoNotTransfer](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.DoNotTransfer/) &ndash; созданный `Java.Lang.Object` экземпляр будет создан новый глобальный ссылку из `handle` параметра, и `handle` не изменяется.
    Вызывающий объект отвечает за освобождение `handle` при необходимости.

-   [JniHandleOwnership.TransferLocalRef](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.TransferLocalRef/) &ndash; созданный `Java.Lang.Object` экземпляр будет создан новый глобальный ссылку из `handle` параметра, и `handle` будут удалены вместе с [JNIEnv.DeleteLocalRef ](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/) . Вызывающий объект не должен освободить `handle` и не должны использовать `handle` по завершении конструктора.

-   [JniHandleOwnership.TransferGlobalRef](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.TransferLocalRef/) &ndash; созданный `Java.Lang.Object` экземпляр возьмет на себя владение `handle` параметра. Вызывающий объект не должен освободить `handle` .


Поскольку методы вызова метода JNI возвращают локальные refs `JniHandleOwnership.TransferLocalRef` обычно используется:

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef);
```

Созданный глобальной ссылки не будут освобождены до `Java.Lang.Object` экземпляр выполняет сборку мусора. Если имеется возможность удаления экземпляра будет освободить глобальной ссылки, ускорение сборки мусора:

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
using (var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef)) {
    // use value ...
}
```



### <a name="using-javalangobjectgetobjectlttgt"></a>С помощью Java.Lang.Object.GetObject&lt;T&gt;)

`Java.Lang.Object` предоставляет [Java.Lang.Object.GetObject&lt;T&gt;(дескриптор IntPtr, передача JniHandleOwnership)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) метод, который может использоваться для создания управляемого вызываемую оболочку указанного типа.

Тип `T` необходимо выполнить следующие требования:

1.  `T` должен быть ссылочным типом.

1.  `T` необходимо реализовать `IJavaObject` интерфейса.

1.  Если `T` не абстрактного класса или интерфейса, то `T` должен предоставлять конструктор с типом параметра `(IntPtr,
    JniHandleOwnership)` .

1.  Если `T` является абстрактным классом или интерфейсом, существует *должен* быть *invoker* для `T` . Средство вызова — это неабстрактный тип, который наследует `T` или реализует `T` , и имеет то же имя, что `T` с суффиксом вызова. Например, если T — это интерфейс `Java.Lang.IRunnable` , то типом `Java.Lang.IRunnableInvoker` должен существовать и должен содержать необходимые `(IntPtr,
    JniHandleOwnership)` конструктор.


Поскольку методы вызова метода JNI возвращают локальные refs `JniHandleOwnership.TransferLocalRef` обычно используется:

```csharp
IntPtr lrefString = JNIEnv.CallObjectMethod(instance, methodID);
Java.Lang.String value = Java.Lang.Object.GetObject<Java.Lang.String>( lrefString, JniHandleOwnership.TransferLocalRef);
```

<a name="_Looking_up_Java_Types" />

## <a name="looking-up-java-types"></a>Поиск типов Java

Для поиска поля или метода в JNI, объявляющий тип для поля или метода необходимо искать первым. [Android.Runtime.JNIEnv.FindClass(string)](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/(System.String)) метод используется для поиска типов Java. Параметр строки *упрощенное ссылочный тип* или *ссылку на тип полного* для типа Java. В разделе [раздел ссылок на тип JNI](#_JNI_Type_References) подробные сведения о ссылках на упрощенный и полный тип.

Примечание: в отличие от всех остальных `JNIEnv` метод, который возвращает экземпляры объекта `FindClass` возвращает глобальной ссылки локальную ссылку.

<a name="_Instance_Fields" />

## <a name="instance-fields"></a>Поля экземпляров

Поля подвергнуты *поля идентификаторов*. Поля извлекаются через [JNIEnv.GetFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFieldID/), что требует класса, это поле определено в имя поля и [JNI сигнатура типа](#JNI_Type_Signatures) поля.

Идентификаторы поле не обязательно должны будут освобождены и действительны до тех пор, пока загрузка соответствующего типа Java. (Android не поддерживается выгрузки класса.)

Существует два набора методы для работы с поля экземпляра: один для чтения полей экземпляра и для записи поля экземпляра. Все наборы методов требуется идентификатор поля для чтения или записи значение поля.


### <a name="reading-instance-field-values"></a>Считывание значений поля экземпляра

Набор методов для считывания значений поля экземпляра шаблону именования:

```csharp
* JNIEnv.Get*Field(IntPtr instance, IntPtr fieldID);
```
где `*` тип поля:

-   [JNIEnv.GetObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetObjectField/) &ndash; считать значение поля экземпляра, не является встроенной тип, такой как `java.lang.Object` , массивов и типов интерфейса. Возвращаемое значение JNI локальную ссылку.

-   [JNIEnv.GetBooleanField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetBooleanField/) &ndash; прочесть значение `bool` поля экземпляра.

-   [JNIEnv.GetByteField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetByteField/) &ndash; прочесть значение `sbyte` поля экземпляра.

-   [JNIEnv.GetCharField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetCharField/) &ndash; прочесть значение `char` поля экземпляра.

-   [JNIEnv.GetShortField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetShortField/) &ndash; прочесть значение `short` поля экземпляра.

-   [JNIEnv.GetIntField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetIntField/) &ndash; прочесть значение `int` поля экземпляра.

-   [JNIEnv.GetLongField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetLongField/) &ndash; прочесть значение `long` поля экземпляра.

-   [JNIEnv.GetFloatField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFloatField/) &ndash; прочесть значение `float` поля экземпляра.

-   [JNIEnv.GetDoubleField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetDoubleField/) &ndash; прочесть значение `double` поля экземпляра.





### <a name="writing-instance-field-values"></a>Запись значений поля экземпляра

Набор методов для записи значения полей экземпляра шаблону именования:

```csharp
JNIEnv.SetField(IntPtr instance, IntPtr fieldID, Type value);
```

где *тип* тип поля:

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.IntPtr)) &ndash; записи значения любого поля, которое не является тип builtin, такие как `java.lang.Object` , массивов и типов интерфейса. `IntPtr` Значение может иметь ссылки на местную JNI, глобальной ссылки JNI, слабую ссылку глобального JNI, или `IntPtr.Zero` (для `null` ).

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Boolean)) &ndash; записи значения `bool` поля экземпляра.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.SByte)) &ndash; записи значения `sbyte` поля экземпляра.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Char)) &ndash; записи значения `char` поля экземпляра.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Int16)) &ndash; записи значения `short` поля экземпляра.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Int32)) &ndash; записи значения `int` поля экземпляра.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Int64)) &ndash; записи значения `long` поля экземпляра.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Single)) &ndash; записи значения `float` поля экземпляра.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Double)) &ndash; записи значения `double` поля экземпляра.


<a name="_Static_Fields" />

## <a name="static-fields"></a>Статические поля

Статические поля подвергнуты *поля идентификаторов*. Поля извлекаются через [JNIEnv.GetStaticFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticFieldID/), что требует класса, это поле определено в имя поля и [JNI сигнатура типа](#JNI_Type_Signatures) поля.

Идентификаторы поле не обязательно должны будут освобождены и действительны до тех пор, пока загрузка соответствующего типа Java. (Android не поддерживается выгрузки класса.)

Существует два набора методы для управления статические поля: одно для чтения полей экземпляра и для записи поля экземпляра. Все наборы методов требуется идентификатор поля для чтения или записи значение поля.


### <a name="reading-static-field-values"></a>Чтение значения статических полей

Набор методов для чтения статического поля значений шаблону именования:

```csharp
* JNIEnv.GetStatic*Field(IntPtr class, IntPtr fieldID);
```

где `*` тип поля:

-   [JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/) &ndash; прочитать значение статического поля, не является встроенной тип, такой как `java.lang.Object` , массивов и типов интерфейса. Возвращаемое значение JNI локальную ссылку.

-   [JNIEnv.GetStaticBooleanField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticBooleanField/) &ndash; прочесть значение `bool` статические поля.

-   [JNIEnv.GetStaticByteField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticByteField/) &ndash; прочесть значение `sbyte` статические поля.

-   [JNIEnv.GetStaticCharField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticCharField/) &ndash; прочесть значение `char` статические поля.

-   [JNIEnv.GetStaticShortField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticShortField/) &ndash; прочесть значение `short` статические поля.

-   [JNIEnv.GetStaticLongField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticLongField/) &ndash; прочесть значение `long` статические поля.

-   [JNIEnv.GetStaticFloatField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticFloatField/) &ndash; прочесть значение `float` статические поля.

-   [JNIEnv.GetStaticDoubleField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticDoubleField/) &ndash; прочесть значение `double` статические поля.



### <a name="writing-static-field-values"></a>Запись значения статических полей

Набор методов для записи значения статических полей шаблону именования:

```csharp
JNIEnv.SetStaticField(IntPtr class, IntPtr fieldID, Type value);
```

где *тип* тип поля:

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.IntPtr)) &ndash; записать значение статического поля, не является встроенной тип, такой как `java.lang.Object` , массивов и типов интерфейса. `IntPtr` Значение может иметь ссылки на местную JNI, глобальной ссылки JNI, слабую ссылку глобального JNI, или `IntPtr.Zero` (для `null` ).

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Boolean)) &ndash; записи значения `bool` статические поля.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.SByte)) &ndash; записи значения `sbyte` статические поля.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Char)) &ndash; записи значения `char` статические поля.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Int16)) &ndash; записи значения `short` статические поля.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Int32)) &ndash; записи значения `int` статические поля.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Int64)) &ndash; записи значения `long` статические поля.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Single)) &ndash; записи значения `float` статические поля.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Double)) &ndash; записи значения `double` статические поля.


<a name="_Instance_Methods" />

## <a name="instance-methods"></a>Методы экземпляра

Методы экземпляра вызываются через *метод идентификаторы*. Метод извлекаются через [JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/), требующий тип, определенный в имя метода, метод и [сигнатура типа JNI](#JNI_Type_Signatures) метода.

Метод идентификаторы не обязательно должны будут освобождены и действительны до тех пор, пока загрузка соответствующего типа Java. (Android не поддерживается выгрузки класса.)

Существует два набора методов для вызова методов: один для вызова методов, фактически и один для вызова методов невиртуальным. Оба набора методов требуется идентификатор метода для вызова метода и невиртуальный вызов также необходимо указать, какую реализацию класса должен вызываться.

Методы интерфейса можно только искать в объявляющий тип; методы, которые берутся из расширенных наследуется интерфейсы нельзя найти поиском. См. Далее интерфейсы привязки / section Invoker реализацию для получения дополнительных сведений.

Любой метод объявлен в классе или любого базового класса или интерфейса можно искать.


### <a name="virtual-method-invocation"></a>Вызов виртуального метода

Набор методов для вызова методов практически соответствующей шаблону именования:

```csharp
* JNIEnv.Call*Method( IntPtr instance, IntPtr methodID, params JValue[] args );
```

где `*` имеет тип возвращаемого значения метода.

-   [JNIEnv.CallObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallObjectMethod/) &ndash; вызова метода, которое возвращает тип не builtin, такие как `java.lang.Object` , массивы и интерфейсы. Возвращаемое значение JNI локальную ссылку.

-   [JNIEnv.CallBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallBooleanMethod/) &ndash; вызывать метод, возвращающий `bool` значение.

-   [JNIEnv.CallByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallByteMethod/) &ndash; вызывать метод, возвращающий `sbyte` значение.

-   [JNIEnv.CallCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallCharMethod/) &ndash; вызывать метод, возвращающий `char` значение.

-   [JNIEnv.CallShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallShortMethod/) &ndash; вызывать метод, возвращающий `short` значение.

-   [JNIEnv.CallLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallLongMethod/) &ndash; вызывать метод, возвращающий `long` значение.

-   [JNIEnv.CallFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallFloatMethod/) &ndash; вызывать метод, возвращающий `float` значение.

-   [JNIEnv.CallDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallDoubleMethod/) &ndash; вызывать метод, возвращающий `double` значение.



### <a name="non-virtual-method-invocation"></a>Невиртуальный метод вызова

Набор методов для вызова методов невиртуальным соответствующей шаблону именования:

```csharp
* JNIEnv.CallNonvirtual*Method( IntPtr instance, IntPtr class, IntPtr methodID, params JValue[] args );
```

где `*` имеет тип возвращаемого значения метода. Вызов невиртуальный метод обычно используется для вызова метода базового виртуального метода.

-   [JNIEnv.CallNonvirtualObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualObjectMethod/) &ndash; не практически вызова метода, которое возвращает тип не builtin, такие как `java.lang.Object` , массивы и интерфейсы. Возвращаемое значение JNI локальную ссылку.

-   [JNIEnv.CallNonvirtualBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualBooleanMethod/) &ndash; не практически вызывать метод, возвращающий `bool` значение.

-   [JNIEnv.CallNonvirtualByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualByteMethod/) &ndash; не практически вызывать метод, возвращающий `sbyte` значение.

-   [JNIEnv.CallNonvirtualCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualCharMethod/) &ndash; не практически вызывать метод, возвращающий `char` значение.

-   [JNIEnv.CallNonvirtualShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualShortMethod/) &ndash; не практически вызывать метод, возвращающий `short` значение.

-   [JNIEnv.CallNonvirtualLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualLongMethod/) &ndash; не практически вызывать метод, возвращающий `long` значение.

-   [JNIEnv.CallNonvirtualFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualFloatMethod/) &ndash; не практически вызывать метод, возвращающий `float` значение.

-   [JNIEnv.CallNonvirtualDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualDoubleMethod/) &ndash; не практически вызывать метод, возвращающий `double` значение.


<a name="_Static_Methods" />

## <a name="static-methods"></a>Статические методы

Статические методы вызываются через *метод идентификаторы*. Метод извлекаются через [JNIEnv.GetStaticMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/), требующий тип, определенный в имя метода, метод и [сигнатура типа JNI](#JNI_Type_Signatures) метода.

Метод идентификаторы не обязательно должны будут освобождены и действительны до тех пор, пока загрузка соответствующего типа Java. (Android не поддерживается выгрузки класса.)



### <a name="static-method-invocation"></a>Вызов статического метода

Набор методов для вызова методов практически соответствующей шаблону именования:

```csharp
* JNIEnv.CallStatic*Method( IntPtr class, IntPtr methodID, params JValue[] args );
```

где `*` имеет тип возвращаемого значения метода.

-   [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) &ndash; вызвать статический метод, который возвращает тип не builtin, такие как `java.lang.Object` , массивы и интерфейсы. Возвращаемое значение JNI локальную ссылку.

-   [JNIEnv.CallStaticBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticBooleanMethod/) &ndash; Вызовите статический метод, который возвращает `bool` значение.

-   [JNIEnv.CallStaticByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticByteMethod/) &ndash; Вызовите статический метод, который возвращает `sbyte` значение.

-   [JNIEnv.CallStaticCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticCharMethod/) &ndash; Вызовите статический метод, который возвращает `char` значение.

-   [JNIEnv.CallStaticShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticShortMethod/) &ndash; Вызовите статический метод, который возвращает `short` значение.

-   [JNIEnv.CallStaticLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallLongMethod/) &ndash; Вызовите статический метод, который возвращает `long` значение.

-   [JNIEnv.CallStaticFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticFloatMethod/) &ndash; Вызовите статический метод, который возвращает `float` значение.

-   [JNIEnv.CallStaticDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticDoubleMethod/) &ndash; Вызовите статический метод, который возвращает `double` значение.


<a name="JNI_Type_Signatures" />

## <a name="jni-type-signatures"></a>JNI тип подписи

[Сигнатуры типа JNI](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/types.html#wp16432) , [ссылок на типы JNI](#_JNI_Type_References) (не менее упрощенный тип ссылки), за исключением методов. С помощью методов, подпись типа JNI является открывающей круглой скобкой `'('`, а затем ссылок на типы для всех типов, соединенных вместе (с без запятых, разделяющих отделение или других действий), следуют закрывающую скобку параметра `')'`, следует ссылка на тип JNI тип возвращаемого значения метода.

Например если метод Java:

```java
long f(int n, String s, int[] array);
```

Сигнатура типа JNI будет выглядеть так:

```csharp
(ILjava/lang/String;[I)J
```

Как правило, это *строго* рекомендуется использовать `javap` команду, чтобы определить JNI подписи. Например, сигнатура типа JNI для [java.lang.Thread.State.valueOf(String)](http://developer.android.com/reference/java/lang/Thread.State.html#valueOf(java.lang.String)) метод» (Ljava/lang или строка); состояние $ Ljava/lang/потока;», а подпись типа JNI [ java.lang.Thread.State.values](http://developer.android.com/reference/java/lang/Thread.State.html#values) метод является «() [состояние $ Ljava/lang/потока;». Наблюдайте за конечные точки с запятой; Эти *являются* частью сигнатуры типа JNI.

<a name="_JNI_Type_References" />

## <a name="jni-type-references"></a>Ссылки на типы JNI

Ссылки на типы JNI, отличаются от ссылок на типы Java. Нельзя использовать полные имена типов Java, таких как `java.lang.String` с JNI, необходимо использовать варианты JNI `"java/lang/String"` или `"Ljava/lang/String;"`, в зависимости от контекста; Дополнительные сведения см.
Существует четыре типа JNI тип ссылки:

-  **built-in**
-  **упрощенное письмо**
-  **type**
-  **array**


### <a name="built-in-type-references"></a>Ссылки на встроенные типы

Один символ, используемый для ссылки на встроенные типы значений являются встроенного типа ссылки. Сопоставление выглядит следующим образом:

-  `"B"` для `sbyte` .
-  `"S"` для `short` .
-  `"I"` для `int` .
-  `"J"` для `long` .
-  `"F"` для `float` .
-  `"D"` для `double` .
-  `"C"` для `char` .
-  `"Z"` для `bool` .
-  `"V"` для `void` типы возвращаемых значений метода.


<a name="_Simplified_Type_References_1" />

### <a name="simplified-type-references"></a>Упрощенная типа ссылки

Упрощенная тип ссылки может использоваться только в [JNIEnv.FindClass(string)](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/(System.String)).
Для получения ссылка на тип, упрощенное двумя способами.

1.  В полное имя Java замените каждый `'.'` в имени пакета и до имя типа с помощью `'/'` и каждый `'.'` в имя типа с `'$'` .

1.  Чтение выходных данных `'unzip -l android.jar | grep JavaName'` .


Один из двух приведет к типом Java [java.lang.Thread.State](http://developer.android.com/reference/java/lang/Thread.State.html) , сопоставляемого ссылку на тип, упрощенное `java/lang/Thread$State`.


### <a name="type-references"></a>Тип ссылки

Ссылка на тип является ссылкой встроенный тип или ссылку на упрощенный тип с `'L'` префикс и `';'` суффикс. Для типа Java [java.lang.String](http://developer.android.com/reference/java/lang/String.html), ссылка на тип, упрощенное `"java/lang/String"`, а ссылка на тип `"Ljava/lang/String;"`.

Ссылки на тип, используются со ссылками на тип массива, так и с подписями JNI.

— Дополнительный способ получить ссылку на тип, считывая выходные данные `'javap -s -classpath android.jar fully.qualified.Java.Name'`.
В зависимости от типа используется, можно использовать объявление конструктора или метода возвращаемый тип для определения имени JNI. Пример:

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

`Thread.State` является типом перечисления Java, поэтому мы используем подпись `valueOf` метод, чтобы определить, является ли ссылка на тип состояние $ Ljava/lang/потока;.



### <a name="array-type-references"></a>Ссылки на тип массива

Ссылки на тип массива являются `'['` к ссылке на тип JNI в качестве префикса.
Упрощенная тип ссылки нельзя использовать при указании массивов.

Например `int[]` — `"[I"`, `int[][]` — `"[[I"`, и `java.lang.Object[]` — `"[Ljava/lang/Object;"`.



## <a name="java-generics-and-type-erasure"></a>Универсальные типы Java и усилий

*Большинство* времени, в JNI, универсальные типы Java *не существуют*.
Существуют некоторые «Морщины», но эти Морщины находятся в взаимодействие Java с помощью универсальных шаблонов, не имеющая JNI находит и вызывает универсальных членов.

При работе через JNI нет разницы между универсального типа или члена и неуниверсального типа или члена. Например, универсальный тип [java.lang.Class&lt;T&gt; ](http://developer.android.com/reference/java/lang/Class.html) «raw» универсальный тип `java.lang.Class`, из которых имеют ту же ссылку типа упрощенной `"java/lang/Class"`.


## <a name="java-native-interface-support"></a>Поддержка собственного интерфейса Java

[Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/) является управляемой оболочки для Jave собственного интерфейса (JNI). Функции JNI, объявляются в [спецификация собственного интерфейса Java](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html), хотя методы были изменены для удаления явных `JNIEnv*` параметр и `IntPtr` используется вместо `jobject`, `jclass`, `jmethodID`и т. д. Например, рассмотрим [JNI NewObject функция](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp4517):

```csharp
jobject NewObjectA(JNIEnv *env, jclass clazz, jmethodID methodID, jvalue *args);
```

Оно предоставляется как [JNIEnv.NewObject](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewObject/p/System.IntPtr/System.IntPtr/Android.Runtime.JValue[]/) метод:

```csharp
public static IntPtr NewObject(IntPtr clazz, IntPtr jmethod, params JValue[] parms);
```

Преобразования между этими двумя вызовами достаточно прост. В языке C будет иметь:

```c
jobject CreateMapActivity(JNIEnv *env)
{
    jclass    Map_Class   = (*env)->FindClass(env, "mono/samples/googlemaps/MyMapActivity");
    jmethodID Map_defCtor = (*env)->GetMethodID (env, Map_Class, "<init>", "()V");
    jobject   instance    = (*env)->NewObject (env, Map_Class, Map_defCtor);

    return instance;
}
```

В C# эквивалентом будет выглядеть так:

```csharp
IntPtr CreateMapActivity()
{
    IntPtr Map_Class   = JNIEnv.FindClass ("mono/samples/googlemaps/MyMapActivity");
    IntPtr Map_defCtor = JNIEnv.GetMethodID (Map_Class, "<init>", "()V");
    IntPtr instance    = JNIEnv.NewObject (Map_Class, Map_defCtor);

    return instance;
}
```

Как только экземпляр объекта Java, которые содержатся в объект IntPtr, возможно, имеет смысл выполнять с ним. Можно использовать методы JNIEnv, такие как [ <span class="external">JNIEnv.CallVoidMethod()</span> ](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallVoidMethod/p/System.IntPtr/System.IntPtr/Android.Runtime.JValue[]/) чтобы сделать это, но, если уже аналогового оболочки C#, а затем вы захотите создать оболочку по ссылке на JNI. Можно сделать это с помощью [Extensions.JavaCast <t>()</t> ](https://developer.xamarin.com/api/member/Android.Runtime.Extensions.JavaCast%7BTResult%7D/p/Android.Runtime.IJavaObject/) метода расширения:

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

Кроме того, все функции JNI были изменены, удалив `JNIEnv*` параметр имеется в каждой функции JNI.


## <a name="summary"></a>Сводка

Работа непосредственно с JNI является ужасных интерфейс, в котором следует избегать любой ценой. К сожалению не всегда вызывает; Надеемся, у в этом руководстве предоставит помощь при попадании несвязанного случаев Java и Mono для Android.


## <a name="related-links"></a>Связанные ссылки

- [SanityTests (пример)](https://developer.xamarin.com/samples/SanityTests/)
- [Спецификация собственного интерфейса Java](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/jniTOC.html)
- [Функции собственного интерфейса Java](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)
