---
title: Xamarin.Android vs. Desktop — различия в среду выполнения Mono
ms.prod: xamarin
ms.assetid: F953F9B4-3596-8B3A-A8E4-8219B5B9F7CA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 115d715214d7af3174c41d9d82e894ce429dab42
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120909"
---
# <a name="limitations"></a>Ограничения

Так как приложения в Android требуют создания прокси-типов Java во время сборки, не можно создать весь код во время выполнения.

Существуют следующие ограничения Xamarin.Android, по сравнению с рабочего стола Mono:


## <a name="limited-dynamic-language-support"></a>Поддержка ограниченного динамического языка

 [Android вызываемых оболочек](~/android/platform/java-integration/android-callable-wrappers.md) необходимы в любое время, в среде выполнения Android необходимо вызывать управляемый код. Android вызываемые оболочки создаются во время компиляции, на основе статического анализа IL. В результате выполнения этого: вы *нельзя* использовать динамических языков (IronPython, IronRuby и др.) в любом сценарии, где необходима подклассов для них типы Java (включая косвенных подклассы), так как не существует способа извлечения этих динамических типов во время компиляции для создания необходимых Android вызываемых оболочек.


## <a name="limited-java-generation-support"></a>Поддержка создания ограниченного Java

[Android вызываемых оболочек](~/android/platform/java-integration/android-callable-wrappers.md) вам нужно создать кода Java для вызова управляемого кода. *По умолчанию*, вызываемых оболочек времени Android будет содержать только (определенные) объявленных конструкторов и методов, которые переопределяют виртуальный метод Java (т. е. он имеет [ `RegisterAttribute` ](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/)) или реализовать (Java) метод интерфейса Аналогичным образом имеет интерфейс `Attribute`).
  
До выхода версии 4.1 можно объявить без дополнительных методов. Выпустив 4.1 [ `Export` и `ExportField` настраиваемые атрибуты можно использовать для объявления методов Java и полей в Android вызываемая оболочка](~/android/platform/java-integration/working-with-jni.md).

### <a name="missing-constructors"></a>Отсутствуют конструкторы

Конструкторы остаются непростой задачей, если не [ `ExportAttribute` ](https://developer.xamarin.com/api/type/Java.Interop.ExportAttribute) используется. Алгоритм создания конструкторов Android вызываемая оболочка является конструктор Java, который будет использовано если:

1. Отсутствует сопоставление Java для всех типов параметров
2. Базовый класс объявляет такой же конструктор &ndash; это необходимо, так как Android вызываемая оболочка *необходимо* вызова в соответствующий конструктор базового класса; (так как нет простого способа не может использоваться без аргументов по умолчанию определить значения, которые должны быть использовать в Java).

Например, рассмотрим следующий класс.

```csharp
[Service]
class MyIntentService : IntentService {
    public MyIntentService (): base ("value")
    {
    }
}
```

Хотя это выглядит совершенно логично, полученный Android вызываемая оболочка *в сборках выпуска* не будет содержать конструктор по умолчанию. Следовательно при попытке запустить эту службу (например [ `Context.StartService` ](https://developer.xamarin.com/api/member/Android.Content.Context.StartService/p/Android.Content.Intent/)), то возникнет ошибка:

```shell
E/AndroidRuntime(31766): FATAL EXCEPTION: main
E/AndroidRuntime(31766): java.lang.RuntimeException: Unable to instantiate service example.MyIntentService: java.lang.InstantiationException: can't instantiate class example.MyIntentService; no empty constructor
E/AndroidRuntime(31766):        at android.app.ActivityThread.handleCreateService(ActivityThread.java:2347)
E/AndroidRuntime(31766):        at android.app.ActivityThread.access$1600(ActivityThread.java:130)
E/AndroidRuntime(31766):        at android.app.ActivityThread$H.handleMessage(ActivityThread.java:1277)
E/AndroidRuntime(31766):        at android.os.Handler.dispatchMessage(Handler.java:99)
E/AndroidRuntime(31766):        at android.os.Looper.loop(Looper.java:137)
E/AndroidRuntime(31766):        at android.app.ActivityThread.main(ActivityThread.java:4745)
E/AndroidRuntime(31766):        at java.lang.reflect.Method.invokeNative(Native Method)
E/AndroidRuntime(31766):        at java.lang.reflect.Method.invoke(Method.java:511)
E/AndroidRuntime(31766):        at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:786)
E/AndroidRuntime(31766):        at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:553)
E/AndroidRuntime(31766):        at dalvik.system.NativeStart.main(Native Method)
E/AndroidRuntime(31766): Caused by: java.lang.InstantiationException: can't instantiate class example.MyIntentService; no empty constructor
E/AndroidRuntime(31766):        at java.lang.Class.newInstanceImpl(Native Method)
E/AndroidRuntime(31766):        at java.lang.Class.newInstance(Class.java:1319)
E/AndroidRuntime(31766):        at android.app.ActivityThread.handleCreateService(ActivityThread.java:2344)
E/AndroidRuntime(31766):        ... 10 more
```

Обойти это можно объявить конструктор по умолчанию, создавать его с `ExportAttribute`и задайте [ `ExportAttribute.SuperStringArgument` ](https://developer.xamarin.com/api/property/Java.Interop.ExportAttribute.SuperArgumentsString/): 

```csharp
[Service]
class MyIntentService : IntentService {
    [Export (SuperArgumentsString = "\"value\"")]
    public MyIntentService (): base("value")
    {
    }

    // ...
}
```


### <a name="generic-c-classes"></a>Универсальный C# классы

Универсальный C# классы поддерживаются только частично. Существуют следующие ограничения:


-   Универсальные типы не имеете права использовать `[Export]` или `[ExportField`]. Попытка выполнить такую операцию будет создавать `XA4207` ошибки.

    ```csharp
    public abstract class Parcelable<T> : Java.Lang.Object, IParcelable
    {
        // Invalid; generates XA4207
        [ExportField ("CREATOR")]
        public static IParcelableCreator CreateCreator ()
        {
            ...
    }
    ```

-   Универсальные методы не могут использовать `[Export]` или `[ExportField]`:

    ```csharp
    public class Example : Java.Lang.Object
    {
        
        // Invalid; generates XA4207
        [Export]
        public static void Method<T>(T value)
        {
            ...
        }
    }
    ```

-   `[ExportField]` не может использоваться в методах, которые возвращают `void`:

    ```csharp
    public class Example : Java.Lang.Object
    {
        // Invalid; generates XA4208
        [ExportField ("CREATOR")]
        public static void CreateSomething ()
        {
        }
    }
    ```

-   Экземпляры универсальных типов _не должны_ создать из кода Java.
    Они могут создаваться только безопасно из управляемого кода:

    ```csharp
    [Activity (Label="Die!", MainLauncher=true)]
    public class BadGenericActivity<T> : Activity
    {
        protected override void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);
        }
    }
    ```


## <a name="partial-java-generics-support"></a>Поддержка универсальных шаблонов частичного Java

Java, которые поддерживают универсальные шаблоны привязки ограничено. В частности, члены класса универсального экземпляра, который является производным от другого универсального класса (не создан), остаются в виде Java.Lang.Object. Например [Android.Content.Intent.GetParcelableExtra](https://developer.xamarin.com/api/member/Android.Content.Intent.GetParcelableExtra/p/System.String/) метод возвращает Java.Lang.Object. Это связано с удаленных универсальных шаблонов Java.
У нас есть некоторые классы, которые не применяются это ограничение, но они настраиваются вручную.


## <a name="related-links"></a>Связанные ссылки

- [Вызываемые программы-оболочки Android](~/android/platform/java-integration/android-callable-wrappers.md)
- [Работа с JNI](~/android/platform/java-integration/working-with-jni.md)
- [ExportAttribute](https://developer.xamarin.com/api/type/Java.Interop.ExportAttribute/)
- [SuperString](https://developer.xamarin.com/api/property/Java.Interop.ExportAttribute.SuperArgumentsString/)
- [RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/)
