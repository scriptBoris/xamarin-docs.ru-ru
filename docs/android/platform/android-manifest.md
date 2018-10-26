---
title: Работа с манифестом Android
ms.prod: xamarin
ms.assetid: CB7CCF60-FEF1-3B28-215F-159391E74347
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/05/2018
ms.openlocfilehash: 655f988cc54cf54e346e68109271775dee2918a9
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111256"
---
# <a name="working-with-the-android-manifest"></a>Работа с манифестом Android


## <a name="overview"></a>Обзор

**AndroidManifest.xml** файл предоставляет обширные платформы Android, позволяющий описывают функциональные возможности и требования приложения для Android. Тем не менее работать с ним непросто. Xamarin.Android позволяет свести к минимуму этой сложности, позволяя добавлять настраиваемые атрибуты к классам, которые затем будут использоваться для автоматического создания манифеста для вас. Наша цель — что 99% наших пользователей нет необходимости вручную изменять **AndroidManifest.xml**. 

**AndroidManifest.xml** создается как часть процесса сборки, а также XML, обнаруженное в **Properties/AndroidManifest.xml** объединяется с XML, который создается на основе настраиваемых атрибутов. Полученный в результате слияния **AndroidManifest.xml** находится в **obj** подкаталог; например, он находится в **obj/Debug/android/AndroidManifest.xml** для сборок отладки . Процесс слияния является тривиальным: настраиваемые атрибуты в коде используется для создания XML-элементов и *вставляет* этих элементов в **AndroidManifest.xml**. 



## <a name="the-basics"></a>Основы

Во время компиляции, сборки проверяются на наличие отличных`abstract` классы, производные от [действия](https://developer.xamarin.com/api/type/Android.App.Activity/) и [ `[Activity]` ](https://developer.xamarin.com/api/type/Android.App.ActivityAttribute/) атрибут объявлен на них. Затем использует эти классы и атрибуты для создания манифеста. Рассмотрим следующий пример кода: 

```csharp
namespace Demo
{
    public class MyActivity : Activity
    {
    }
}
```

Это приводит к nothing, создаваемых в **AndroidManifest.xml**. Если вы хотите, чтобы `<activity/>` элемент будет создан, необходимо использовать [`[Activity]`](https://developer.xamarin.com/api/type/Android.App.Activity/Attribute) 
Настраиваемый атрибут: 

```csharp
namespace Demo
{
    [Activity]
    public class MyActivity : Activity
    {
    }
}
```

В этом примере задается в следующем фрагменте xml, добавляемых к **AndroidManifest.xml**:

```xml
<activity android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

`[Activity]` Атрибут не оказывает влияния `abstract` типы; `abstract` типов учитываются.



### <a name="activity-name"></a>Имя действия

Начиная с Xamarin.Android 5.1, имя типа действия зависит контрольной суммы MD5 имени выполняется экспорт типа с указанием сборки. Это позволяет полное доменное имя, совпадающее должен предоставляться из двух разных сборках и не возникает ошибка упаковки. (Перед Xamarin.Android 5.1, имя типа по умолчанию действия был создан из строчных пространство имен и имя класса). 

Если вы хотите переопределить это значение по умолчанию и явным образом указать имя действия, используйте [ `Name` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Name/) свойство: 

```csharp
[Activity (Name="awesome.demo.activity")]
public class MyActivity : Activity
{
}
```

Этот пример выводит следующий фрагмент xml:

```xml
<activity android:name="awesome.demo.activity" />
```

*Примечание*: следует использовать `Name` свойства только в целях обратной совместимости, таким образом переименование может замедлить работу типа поиска репликации во время выполнения. При наличии устаревшего кода, который ожидает, что имя типа по умолчанию действия должен быть основан на нижнем регистре пространство имен и имя класса, см. в разделе [Android вызываемую оболочку именования](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1/#Android_Callable_Wrapper_Naming) советы об обеспечении совместимости. 


### <a name="activity-title-bar"></a>Заголовок действия

По умолчанию Android дает приложению заголовок окна при его выполнении. Значение, используемое для этого является [ `/manifest/application/activity/@android:label` ](http://developer.android.com/guide/topics/manifest/activity-element.html#label). В большинстве случаев это значение будет отличаться от имени класса. Чтобы указать метку вашего приложения в заголовке окна, используйте [ `Label` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Label/) свойство.
Пример: 

```csharp
[Activity (Label="Awesome Demo App")]
public class MyActivity : Activity
{
}
```

Этот пример выводит следующий фрагмент xml:

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```


### <a name="launchable-from-application-chooser"></a>Можно будет запустить из средства выбора приложения

По умолчанию действие не будет отображаться в запуск приложения Android. Это обусловлено тем, скорее всего будет большое количество действий в приложении, и вы не хотите значок для каждого из них. Чтобы указать, какой из них должен быть можно будет запустить из средства запуска приложения, используйте [ `MainLauncher` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.MainLauncher/) свойство. Пример: 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true)] 
public class MyActivity : Activity
{
}
```

Этот пример выводит следующий фрагмент xml:

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
</activity>
```



### <a name="activity-icon"></a>Значок действия

По умолчанию действие получит значок запуска по умолчанию, предоставляемые системой. Чтобы использовать пользовательский значок, сначала добавьте вашей **.png** для **ресурсы/drawable**, присвоить его действие при построении **AndroidResource**, затем с помощью [ `Icon` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Icon/) свойство, чтобы указать значок, используемый. Пример: 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
public class MyActivity : Activity
{
}
```

Этот пример выводит следующий фрагмент xml:

```xml
<activity android:icon="@drawable/myicon" android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
</activity>
```


### <a name="permissions"></a>Разрешения

При добавлении разрешения в манифесте Android (как описано в разделе [добавить разрешения для манифеста Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest)), эти разрешения, записываются в **Properties/AndroidManifest.xml**. Например, если задать `INTERNET` разрешение, добавляется следующий элемент **Properties/AndroidManifest.xml**: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Сборки отладки автоматически задают некоторые разрешения для упрощения отладки (такие как `INTERNET` и `READ_EXTERNAL_STORAGE`) &ndash; эти параметры устанавливаются только в созданном **obj/Debug/android/AndroidManifest.xml** и не показано, как включенная в **необходимые разрешения** параметры. 

Например, если посмотреть на созданного файла манифеста в **obj/Debug/android/AndroidManifest.xml**, может появиться следующее добавлены элементы разрешений: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

В выпуске версия манифеста сборки (в **obj/Debug/android/AndroidManifest.xml**), эти разрешения являются *не* автоматически настраиваемые. Если обнаружится, что переключение в выпускное построение вызывает приложение теряет разрешение, которое было доступно в отладочной сборке, убедитесь, что это разрешение явно задан **необходимые разрешения** параметры для вашего приложения (см.  **Построение > приложение Android** в Visual Studio для Mac, см. в разделе **свойства > манифеста Android** в Visual Studio). 




## <a name="advanced-features"></a>Расширенные возможности


### <a name="intent-actions-and-features"></a>Действия намерений и компоненты

Манифест Android предоставляет способ для описания возможностей вашего действия. Это делается с помощью [Intents](http://developer.android.com/guide/topics/manifest/intent-filter-element.html) и [`[IntentFilter]`](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/) 
Настраиваемый атрибут. Можно указать, какие действия подходят для действия с [`IntentFilter`](https://developer.xamarin.com/api/constructor/Android.App.IntentFilterAttribute.IntentFilterAttribute/p/System.String[]/) 
конструктор и категории, соответствующие с [`Categories`](https://developer.xamarin.com/api/property/Android.App.IntentFilterAttribute.Categories/) 
. По крайней мере одно действие должно быть указано (именно действия предоставляются в конструкторе). `[IntentFilter]` можно указать несколько раз, и каждая из которых использует результаты в отдельном `<intent-filter/>` сервисном `<activity/>`. Пример:

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
[IntentFilter (new[]{Intent.ActionView}, 
        Categories=new[]{Intent.CategorySampleCode, "my.custom.category"})]
public class MyActivity : Activity
{
}
```

Этот пример выводит следующий фрагмент xml:

```xml
<activity android:icon="@drawable/myicon" android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
  <intent-filter>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.SAMPLE_CODE" />
    <category android:name="my.custom.category" />
  </intent-filter>
</activity>
```


### <a name="application-element"></a>Элемент Application

Манифест Android также предоставляет способ для объявления свойств для всего приложения. Это делается с помощью `<application>` элемента и его аналог, [приложения](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/) настраиваемого атрибута. Обратите внимание, что эти параметры (на уровне сборки) на уровне приложения, а не параметры каждого действия. Как правило, можно объявить `<application>` свойства для всего приложения и затем переопределить эти параметры (при необходимости) на основе каждого действия. 

Например, следующая `Application` добавляется атрибут **AssemblyInfo.cs** чтобы указать, что приложение может быть отладки, что ее понятное пользователю имя — **Мое приложение**, и что он использует `Theme.Light` стиля как тему по умолчанию для всех действий: 

```csharp
[assembly: Application (Debuggable=true,   
                        Label="My App",   
                        Theme="@android:style/Theme.Light")]
```

Это объявление вызывает следующий фрагмент XML должны быть созданы в **obj/Debug/android/AndroidManifest.xml**:

```xml
<application android:label="My App" 
             android:debuggable="true" 
             android:theme="@android:style/Theme.Light"
                ... />
```
В этом примере все действия в приложении по умолчанию будет `Theme.Light` стиля. При выборе действия темы `Theme.Dialog`, только то, что действие будет использовать `Theme.Dialog` в стиле по умолчанию все действия в приложении `Theme.Light` style, как задано в `<application>` элемент. 

`Application` Элемент не является единственным способом настроить `<application>` атрибуты. Кроме того, вы можете вставить атрибуты непосредственно в `<application>` элемент **Properties/AndroidManifest.xml**. Эти параметры будут объединены в конечный `<application>` элемент, который находится в **obj/Debug/android/AndroidManifest.xml**. Обратите внимание, что содержимое **Properties/AndroidManifest.xml** всегда переопределять данные, предоставляемые настраиваемых атрибутов. 

Существует множество атрибутов уровня приложения, которые можно настроить в `<application>` элементу; Дополнительные сведения об этих параметрах см. в разделе [открытые свойства](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/#Public_Properties) раздел [ApplicationAttribute](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/). 



## <a name="list-of-custom-attributes"></a>Список настраиваемых атрибутов

-   [Android.App.ActivityAttribute](https://developer.xamarin.com/api/type/Android.App.ActivityAttribute/) : создает [/manifest/application/activity](http://developer.android.com/guide/topics/manifest/activity-element.html) XML-фрагмент 
-   [Android.App.ApplicationAttribute](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/) : создает [/манифест приложения](http://developer.android.com/guide/topics/manifest/application-element.html) XML-фрагмент 
-   [Android.App.InstrumentationAttribute](https://developer.xamarin.com/api/type/Android.App.InstrumentationAttribute/) : создает [/манифест и инструментирования](http://developer.android.com/guide/topics/manifest/instrumentation-element.html) XML-фрагмент 
-   [Android.App.IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/) : создает [//intent-filter](http://developer.android.com/guide/topics/manifest/intent-filter-element.html) XML-фрагмент 
-   [Android.App.MetaDataAttribute](https://developer.xamarin.com/api/type/Android.App.MetaDataAttribute/) : создает [//meta-data](http://developer.android.com/guide/topics/manifest/meta-data-element.html) XML-фрагмент 
-   [Android.App.PermissionAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionAttribute/) : создает [//permission](http://developer.android.com/guide/topics/manifest/permission-element.html) XML-фрагмент 
-   [Android.App.PermissionGroupAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionGroupAttribute/) : создает [//permission-group](http://developer.android.com/guide/topics/manifest/permission-group-element.html) XML-фрагмент 
-   [Android.App.PermissionTreeAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionTreeAttribute/) : создает [//permission-tree](http://developer.android.com/guide/topics/manifest/permission-tree-element.html) XML-фрагмент 
-   [Android.App.ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/) : создает [/manifest/application/service](http://developer.android.com/guide/topics/manifest/service-element.html) XML-фрагмент 
-   [Android.App.UsesLibraryAttribute](https://developer.xamarin.com/api/type/Android.App.UsesLibraryAttribute/) : создает [/manifest/application/uses-library](http://developer.android.com/guide/topics/manifest/uses-library-element.html) XML-фрагмент 
-   [Android.App.UsesPermissionAttribute](https://developer.xamarin.com/api/type/Android.App.UsesPermissionAttribute/) : создает [/manifest/uses-permission](http://developer.android.com/guide/topics/manifest/uses-permission-element.html) XML-фрагмент 
-   [Android.Content.BroadcastReceiverAttribute](https://developer.xamarin.com/api/type/Android.Content.BroadcastReceiverAttribute/) : создает [/manifest/application/receiver](http://developer.android.com/guide/topics/manifest/receiver-element.html) XML-фрагмент 
-   [Android.Content.ContentProviderAttribute](https://developer.xamarin.com/api/type/Android.Content.ContentProviderAttribute/) : создает [/manifest/application/provider](http://developer.android.com/guide/topics/manifest/provider-element.html) XML-фрагмент 
-   [Android.Content.GrantUriPermissionAttribute](https://developer.xamarin.com/api/type/Android.Content.GrantUriPermissionAttribute/) : создает [/manifest/application/provider/grant-uri-permission](http://developer.android.com/guide/topics/manifest/grant-uri-permission-element.html) XML-фрагмент

