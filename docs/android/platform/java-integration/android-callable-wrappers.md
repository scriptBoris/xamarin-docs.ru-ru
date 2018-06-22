---
title: Android с помощью вызываемых оболочек
ms.prod: xamarin
ms.assetid: C33E15FA-1E2B-819A-C656-CA588D611492
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: bb15c7f3a36cc7f1ed235d92e343bbae67a718b8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30764564"
---
# <a name="android-callable-wrappers"></a>Android с помощью вызываемых оболочек

Android с помощью вызываемых оболочек (ACWs) требуются каждый раз, когда среда выполнения Android вызывает управляемый код. Эти оболочки являются обязательными, так как нет возможности для регистрации классов с РИСУНКА (Android среда выполнения) во время выполнения. (В частности, [JNI DefineClass() функция](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp15986) не поддерживается средой выполнения Android.} Android с помощью вызываемых оболочек таким образом составляют для отсутствия поддержки регистрации типа времени выполнения. 

*Каждый раз* Android кода необходимо выполнить `virtual` или метод, который является интерфейса `overridden` или реализована в управляемом коде Xamarin.Android необходимо предоставить учетной записи-посредника Java, чтобы этот метод передается в соответствующий управляемый тип. Эти типы Java прокси-сервера являются кода Java, имеет «же» базовый класс и список интерфейса Java, что управляемые типы, реализация же конструкторы и объявление переопределенного базового класса и методов интерфейса. 

Android с помощью вызываемых оболочек, созданные **monodroid.exe** при включенном [процесс построения](~/android/deploy-test/building-apps/build-process.md): они формируются для всех типов, которые наследуют (прямо или косвенно) [ Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/). 



## <a name="android-callable-wrapper-naming"></a>Именование Android вызываемой оболочки

Имена пакетов для Android с помощью вызываемых оболочек основаны на команду MD5SUM экспортируемого типа имени сборки. Этот способ именования позволяет же полное имя должно быть доступно по разным сборкам без возникновения ошибки упаковки. 

Из-за такая схема именования команду MD5SUM недоступны напрямую к типам по имени. Например, следующая `adb` команда не будет работать, так как имя типа `my.ActivityType` не создается по умолчанию: 

```shell
adb shell am start -n My.Package.Name/my.ActivityType
```

Кроме того может появиться примерно следующих ошибок при попытке сослаться на тип по имени:

```shell
java.lang.ClassNotFoundException: Didn't find class "com.company.app.MainActivity"
on path: DexPathList[[zip file "/data/app/com.company.App-1.apk"] ...
```

Если вы *сделать* требуется доступ к типам по имени можно объявить имя для этого типа в объявлении атрибута. Например, ниже приведен код, который объявляет действия с полным именем `My.ActivityType`:

```csharp
namespace My {
    [Activity]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

`ActivityAttribute.Name` Свойству можно присвоить значение явно объявить имя действия: 

```csharp
namespace My {
    [Activity(Name="my.ActivityType")]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

После добавления этого параметра свойства `my.ActivityType` может быть доступен по имени из внешнего кода и `adb` сценариев. `Name` Атрибут можно установить для множества различных типов, в том числе `Activity`, `Application`, `Service`, `BroadcastReceiver`, и `ContentProvider`: 

-   [ActivityAttribute.Name](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Name/)
-   [ApplicationAttribute.Name](https://developer.xamarin.com/api/property/Android.App.ApplicationAttribute.Name/)
-   [ServiceAttribute.Name](https://developer.xamarin.com/api/property/Android.App.ServiceAttribute.Name/)
-   [BroadcastReceiverAttribute.Name](https://developer.xamarin.com/api/property/Android.Content.BroadcastReceiverAttribute.Name/)
-   [ContentProviderAttribute.Name](https://developer.xamarin.com/api/property/Android.Content.ContentProviderAttribute.Name/)

На основе команду MD5SUM именования ACW впервые появился в Xamarin.Android 5.0. Дополнительные сведения о именование атрибутов см. в разделе [RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/). 



## <a name="implementing-interfaces"></a>Реализация интерфейсов

Бывают случаи, когда необходимо реализовать интерфейс Android, такие как [Android.Content.IComponentCallbacks](https://developer.xamarin.com/api/type/Android.Content.IComponentCallbacks/). Так как все классы Android и интерфейс расширить [Android.Runtime.IJavaObject](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/) интерфейс, возникает вопрос: как мы реализовали `IJavaObject`? 

Выше ответа на вопрос: причина, по всем типам Android необходимо реализовать `IJavaObject` — так, чтобы Xamarin.Android Android вызываемой оболочки для Android, т. е. прокси Java для заданного типа. Поскольку **monodroid.exe** осуществляет только поиск `Java.Lang.Object` подклассов, и `Java.Lang.Object` реализует `IJavaObject,` ответ очевиден: подкласс `Java.Lang.Object`: 

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

*В оставшейся части этой страницы предоставляет сведения о реализации изменяться без предварительного уведомления* (и представленные здесь только потому, что разработчики будут хотите узнать, что происходит). 

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

Обратите внимание, что базовый класс, сохраняются, и `native` объявления метода предоставляются для каждого метода, которая переопределяется в управляемом коде. 
