---
title: Xamarin.Android vs. Рабочий стол - различия в Mono среды выполнения
ms.prod: xamarin
ms.assetid: F953F9B4-3596-8B3A-A8E4-8219B5B9F7CA
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/25/2018
ms.openlocfilehash: b1302bcf8d6835cac356d96b538d134891648420
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32436769"
---
# <a name="limitations"></a>Ограничения

Поскольку приложения на Android требуется создание типов Java прокси-сервера во время сборки, не удается создать весь код во время выполнения.

Существуют следующие ограничения Xamarin.Android, по сравнению с рабочего стола моно:


## <a name="limited-dynamic-language-support"></a>Только динамические языковая поддержка

 [Android с помощью вызываемых оболочек](~/android/platform/java-integration/android-callable-wrappers.md) необходимы в любое время Android среда выполнения должна вызывать управляемый код. Android с помощью вызываемых оболочек создаются во время компиляции, на основе статического анализа IL. В результате выполнения этого: вы *нельзя* служит динамическими языками (IronPython, IronRuby, т. д.) в любом сценарии, которых требуется создание подклассов типов Java (включая косвенные подклассы), нет способа извлечения этих динамических типов во время компиляции для создания необходимых Android вызываемых оболочек.


## <a name="limited-java-generation-support"></a>Поддержка создания ограниченной Java

[Android с помощью вызываемых оболочек](~/android/platform/java-integration/android-callable-wrappers.md) нужно будет создать кода Java для вызова управляемого кода. *По умолчанию*, Android с помощью вызываемых оболочек будет содержать только (определенные) объявленных конструкторов и методов, которые переопределяют виртуальный метод Java (т. е. он имеет [ `RegisterAttribute` ](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/)) или реализовать (метод интерфейса Java интерфейс имеет аналогично `Attribute`).
  
До выхода версии 4.1 может быть объявлен без дополнительных методов. После выпуска версии 4.1 [ `Export` и `ExportField` настраиваемых атрибутов можно использовать для объявления Java методов и полей в Android вызываемой оболочки](~/android/platform/java-integration/working-with-jni.md).

### <a name="missing-constructors"></a>Отсутствуют конструкторы

Конструкторы остаются непростой задачей, если не [ `ExportAttribute` ](https://developer.xamarin.com/api/type/Java.Interop.ExportAttribute) используется. Алгоритм создания конструкторов Android вызываемой оболочки — Если будут выдаваться конструктор Java:

1. Отсутствует сопоставление для всех типов параметров Java
2. Базовый класс объявляет же конструктор &ndash; это требуется, так как Android вызываемой оболочки *должен* вызывать соответствующий конструктор базового класса; (как нет простого способа не может использоваться без аргументов по умолчанию определить значения, которые должны быть в Java).

Например, рассмотрим следующий класс.

```csharp
[Service]
class MyIntentService : IntentService {
    public MyIntentService (): base ("value")
    {
    }
}
```

Хотя это выглядит совершенно логично, полученный Android вызываемой оболочки *в сборках выпуска* не будет содержать конструктор по умолчанию. Следовательно при попытке запустить эту службу (например [ `Context.StartService` ](https://developer.xamarin.com/api/member/Android.Content.Context.StartService/p/Android.Content.Intent/)), то возникнет ошибка:

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

Обходной путь заключается в том, чтобы объявить конструктор по умолчанию, оформления его с `ExportAttribute`и задайте [ `ExportAttribute.SuperStringArgument` ](https://developer.xamarin.com/api/property/Java.Interop.ExportAttribute.SuperArgumentsString/): 

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


### <a name="generic-c-classes"></a>Универсальные классы C#

Универсальные классы C# поддерживаются только частично. Существуют следующие ограничения:


-   Универсальные типы не могут использовать `[Export]` или `[ExportField`]. Попытка выполнить такую операцию будет создавать `XA4207` ошибки.

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

-   Экземпляры универсальных типов _не должны_ созданные из кода Java.
    Они могут быть созданы только безопасно в управляемом коде:

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

Ограничено Java, поддерживаемых в универсальные шаблоны привязки. В частности, члены класса универсального экземпляра, который является производным от другого универсального класса (не создан), остаются в виде Java.Lang.Object. Например [Android.Content.Intent.GetParcelableExtra](https://developer.xamarin.com/api/member/Android.Content.Intent.GetParcelableExtra/p/System.String/) метод возвращает Java.Lang.Object. Это происходит из-за стертой универсальных типов Java.
У нас есть некоторые классы, которые не применяются это ограничение, но настраиваются вручную.


## <a name="related-links"></a>Связанные ссылки

- [Вызываемые программы-оболочки Android](~/android/platform/java-integration/android-callable-wrappers.md)
- [Работа с JNI](~/android/platform/java-integration/working-with-jni.md)
- [ExportAttribute](https://developer.xamarin.com/api/type/Java.Interop.ExportAttribute/)
- [SuperString](https://developer.xamarin.com/api/property/Java.Interop.ExportAttribute.SuperArgumentsString/)
- [RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/)
