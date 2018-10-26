---
title: Вызываемые оболочки времени Android
ms.prod: xamarin
ms.assetid: C33E15FA-1E2B-819A-C656-CA588D611492
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: 7edbdaa5a690a641523cb5baad7909ed01992aa5
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121819"
---
# <a name="android-callable-wrappers"></a>Вызываемые оболочки времени Android

Android вызываемых оболочек (ACWs) являются обязательными, каждый раз, когда среда выполнения Android вызывает управляемый код. Эти оболочки необходимы, так как нет способа для регистрации классов с помощью КАРТИНОК (Android среда выполнения) во время выполнения. (В частности, [JNI DefineClass() функция](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp15986) средой выполнения Android не поддерживается.} Вызываемые оболочки времени Android таким образом составляют для отсутствия поддержки регистрации типа среды выполнения. 

*Каждый раз* Android кода необходимо выполнить `virtual` или метод, который является интерфейса `overridden` или реализуется в управляемом коде, Xamarin.Android необходимо указать учетную запись-посредник для Java, чтобы этот метод передается в соответствующий управляемый тип. Эти типы прокси-сервера Java имеют код Java, который имеет «одного» базового класса и в списке интерфейсов Java, что управляемые типы, реализация же конструкторы и объявление переопределенного базового класса и методов интерфейса. 

Android вызываемые оболочки создаются **monodroid.exe** программы во время [процесс сборки](~/android/deploy-test/building-apps/build-process.md): они создаются для всех типов, которые (прямо или косвенно) наследовать [ Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/). 



## <a name="android-callable-wrapper-naming"></a>Именование Android вызываемой оболочки

Имена пакетов для вызываемых оболочек времени Android основаны на контрольной суммы MD5 имени выполняется экспорт типа с указанием сборки. Этот способ именования делает возможным для одного имени полного типа должны быть доступны с помощью различных сборок без нарушения ошибка упаковки. 

Из-за такая схема именования контрольной суммы MD5 недоступны непосредственно к типам по имени. Например, следующая `adb` команда не будет работать, так как имя типа `my.ActivityType` не создается по умолчанию: 

```shell
adb shell am start -n My.Package.Name/my.ActivityType
```

Кроме того может появиться примерно такие ошибки при попытке сослаться на тип по имени:

```shell
java.lang.ClassNotFoundException: Didn't find class "com.company.app.MainActivity"
on path: DexPathList[[zip file "/data/app/com.company.App-1.apk"] ...
```

Если вы *сделать* требуется доступ к типам по имени, можно объявить имя для этого типа в объявлении атрибута. Например, ниже приведен код, который объявляет действие полное доменное имя, которое `My.ActivityType`:

```csharp
namespace My {
    [Activity]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

`ActivityAttribute.Name` Свойство может быть присвоено явно объявлять имя этого действия: 

```csharp
namespace My {
    [Activity(Name="my.ActivityType")]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

После добавления этого параметра свойства `my.ActivityType` возможен по имени из внешнего кода, а также из `adb` сценариев. `Name` Атрибут можно задать для множества различных типов, в том числе `Activity`, `Application`, `Service`, `BroadcastReceiver`, и `ContentProvider`: 

-   [ActivityAttribute.Name](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Name/)
-   [ApplicationAttribute.Name](https://developer.xamarin.com/api/property/Android.App.ApplicationAttribute.Name/)
-   [ServiceAttribute.Name](https://developer.xamarin.com/api/property/Android.App.ServiceAttribute.Name/)
-   [BroadcastReceiverAttribute.Name](https://developer.xamarin.com/api/property/Android.Content.BroadcastReceiverAttribute.Name/)
-   [ContentProviderAttribute.Name](https://developer.xamarin.com/api/property/Android.Content.ContentProviderAttribute.Name/)

На основе контрольной суммы MD5 именования ACW впервые появился в Xamarin.Android 5.0. Дополнительные сведения о именование атрибутов, см. в разделе [RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/). 



## <a name="implementing-interfaces"></a>Реализация интерфейсов

Бывают случаи, когда необходимо реализовать интерфейс как Android, такие как [Android.Content.IComponentCallbacks](https://developer.xamarin.com/api/type/Android.Content.IComponentCallbacks/). Так как все Android классы и интерфейс расширения [Android.Runtime.IJavaObject](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/) интерфейс, возникает вопрос: каким образом мы реализуем `IJavaObject`? 

Ответ на вопрос был выше: причина, по всем типам Android необходимо реализовать `IJavaObject` — таким образом, чтобы Xamarin.Android Android вызываемой оболочки для Android, т. е. прокси-сервера Java для заданного типа. Так как **monodroid.exe** ищет только `Java.Lang.Object` подклассов, и `Java.Lang.Object` реализует `IJavaObject,` ответ очевиден: подкласс `Java.Lang.Object`: 

```csharp
class MyComponentCallbacks : Java.Lang.Object, Android.Content.IComponentCallbacks {

    public void OnConfigurationChanged (Android.Content.Res.Configuration newConfig)
    {
        // implementation goes here...
    } 

    public void OnLowMemory ()
    {
        // implementation goes here...
    }
}
```


## <a name="implementation-details"></a>Сведения о реализации

*В оставшейся части этой странице представлен детали реализации может быть изменена без предварительного уведомления* (и представленные здесь только потому, что разработчики будут узнать о том, что). 

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

public class HelloAndroid
    extends android.app.Activity
{
    static final String __md_methods;
    static {
        __md_methods = "n_onCreate:(Landroid/os/Bundle;)V:GetOnCreate_Landroid_os_Bundle_Handler\n" + "";
        mono.android.Runtime.register (
            "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, 
            Culture=neutral, PublicKeyToken=null", HelloAndroid.class, __md_methods);
    }

    public HelloAndroid ()
    {
        super ();
        if (getClass () == HelloAndroid.class)
            mono.android.TypeManager.Activate (
                "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, 
                Culture=neutral, PublicKeyToken=null", "", this, new java.lang.Object[] {  });
    }

    @Override
    public void onCreate (android.os.Bundle p0)
    {
        n_onCreate (p0);
    }

    private native void n_onCreate (android.os.Bundle p0);
}
```

Обратите внимание на то, что базовый класс сохраняется, и `native` объявления метода предоставляются для каждого метода, которая переопределяется в рамках управляемого кода. 
