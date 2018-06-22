---
title: Работа с манифеста Android
ms.prod: xamarin
ms.assetid: CB7CCF60-FEF1-3B28-215F-159391E74347
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/05/2018
ms.openlocfilehash: 18817063900437baa625d8572f0ae28fec77be1e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30769810"
---
# <a name="working-with-the-android-manifest"></a>Работа с манифеста Android


## <a name="overview"></a>Обзор

**AndroidManifest.xml** — файл мощная платформа Android, которая позволяет описываются функциональные возможности и требования приложения для Android. Тем не менее работы с ним не просто. Xamarin.Android помогает свести к минимуму этой сложности, позволяя добавлять настраиваемые атрибуты для классов, которые затем будут использоваться для автоматического создания манифеста для вас. Наша цель — что 99% наши пользователи должны никогда не потребуется вручную изменить **AndroidManifest.xml**. 

**AndroidManifest.xml** созданы как часть процесса построения и XML, найденных в **Properties/AndroidManifest.xml** объединяется с XML, который создается на основе пользовательских атрибутов. Итоговый слияние **AndroidManifest.xml** находится в **obj** подкаталог; например, он находится в **obj/Debug/android/AndroidManifest.xml** для отладочных построений . Процесс слияния является тривиальным: она использует настраиваемые атрибуты в код для создания XML-элементов и *вставляет* этих элементов в **AndroidManifest.xml**. 



## <a name="the-basics"></a>Основы

Во время компиляции сборки проверяются на наличие отличных`abstract` классы, производные от [действия](https://developer.xamarin.com/api/type/Android.App.Activity/) и [ `[Activity]` ](https://developer.xamarin.com/api/type/Android.App.ActivityAttribute/) атрибут объявлен на них. Затем использует эти классы и атрибуты для манифеста сборки. Рассмотрим следующий пример кода: 

```csharp
namespace Demo
{
    public class MyActivity : Activity
    {
    }
}
```

Это приведет к nothing, создаваемыми в **AndroidManifest.xml**. Если вы хотите `<activity/>` элемент должен быть создан, необходимо использовать [ `[Activity]` ](https://developer.xamarin.com/api/type/Android.App.Activity/Attribute) настраиваемых атрибутов: 

```csharp
namespace Demo
{
    [Activity]
    public class MyActivity : Activity
    {
    }
}
```

В этом примере вызывает следующий фрагмент xml для добавления **AndroidManifest.xml**:

```xml
<activity android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

`[Activity]` Атрибут не оказывает влияния `abstract` типы; `abstract` типы учитываются.



### <a name="activity-name"></a>Имя действия

Начиная с версии 5.1 Xamarin.Android, имя типа действия основан на команду MD5SUM экспортируемого типа имени сборки. Это позволяет полностью уточненное имя, совпадающее должен предоставляться из двух разных сборках и не возникает ошибка упаковки. (До версии 5.1 Xamarin.Android, имя типа по умолчанию действия был создан из строчных пространство имен и имя класса). 

Если вы хотите переопределить поведение по умолчанию и явным образом указать имя действия, используйте [ `Name` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Name/) свойства: 

```csharp
[Activity (Name="awesome.demo.activity")]
public class MyActivity : Activity
{
}
```

В этом примере выводятся в следующем фрагменте xml:

```xml
<activity android:name="awesome.demo.activity" />
```

*Примечание*: следует использовать `Name` свойства только из соображений обратной совместимости, таких как переименование может замедлить выполнение типа поиска во время выполнения. При наличии устаревший код, который ожидает, что имя типа по умолчанию действия должна быть основана на строчных пространство имен и имя класса. в разделе [Android вызываемую оболочку именования](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1/#Android_Callable_Wrapper_Naming) советы об обеспечении совместимости. 


### <a name="activity-title-bar"></a>Заголовок действия

По умолчанию Android дает приложению строку заголовка при его выполнении. Значение, используемое для этого является [ `/manifest/application/activity/@android:label` ](http://developer.android.com/guide/topics/manifest/activity-element.html#label). В большинстве случаев это значение будет отличаться от имени класса. Чтобы указать приложения метка в заголовке, используйте [ `Label` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Label/) свойство.
Пример: 

```csharp
[Activity (Label="Awesome Demo App")]
public class MyActivity : Activity
{
}
```

В этом примере выводятся в следующем фрагменте xml:

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```


### <a name="launchable-from-application-chooser"></a>Можно будет запустить из окна Выбор приложения

По умолчанию действие не будет отображаться в окне Запуск приложения Android. Это происходит потому, скорее всего будет много действий в приложении и вы не хотите значок для каждого из них. Чтобы указать, какой из них должен быть можно будет запустить из запуск приложений, используйте [ `MainLauncher` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.MainLauncher/) свойство. Пример: 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true)] 
public class MyActivity : Activity
{
}
```

В этом примере выводятся в следующем фрагменте xml:

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

По умолчанию действие получают значок запуска по умолчанию, предоставляемые системой. Чтобы использовать пользовательский значок, сначала добавьте вашей **.png** для **ресурсы или drawable**, значение его действие при построении **AndroidResource**, затем с помощью [ `Icon` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Icon/) свойство, чтобы указать значок, используемый. Пример: 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
public class MyActivity : Activity
{
}
```

В этом примере выводятся в следующем фрагменте xml:

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

При добавлении разрешения Android манифеста (как описано в [добавьте разрешения в манифест Android](https://developer.xamarin.com/recipes/android/general/projects/add_permissions_to_android_manifest/)), эти разрешения, записываются в **Properties/AndroidManifest.xml**. Например, если задать `INTERNET` разрешение, добавляется следующий элемент **Properties/AndroidManifest.xml**: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Отладочные построения автоматически устанавливается некоторые разрешения, чтобы упростить отладку (такие как `INTERNET` и `READ_EXTERNAL_STORAGE`) &ndash; эти параметры устанавливаются только в созданном **obj/Debug/android/AndroidManifest.xml** и не показано, как включенная в **разрешения, необходимые** параметры. 

Например, если вы проверяете созданный файл манифеста в **obj/Debug/android/AndroidManifest.xml**, может появиться следующее добавлены элементы разрешений: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

В выпуске версия манифеста сборки (в **obj/Debug/android/AndroidManifest.xml**), эти разрешения являются *не* автоматически настроенного. Если обнаружится, переключение в выпускное построение приводит к потере разрешений, которая была доступна в отладочной сборке приложения, убедитесь, что это разрешение явно задано **разрешения, необходимые** параметры для вашего приложения (см. в разделе  **Построение > приложения Android** в Visual Studio для Mac; в разделе **свойства > манифеста Android** в Visual Studio). 




## <a name="advanced-features"></a>Дополнительные возможности


### <a name="intent-actions-and-features"></a>Блокировка с намерением действия и функции

Манифеста Android предоставляет способ для описания возможностей действия. Это выполняется через [целей](http://developer.android.com/guide/topics/manifest/intent-filter-element.html) и [ `[IntentFilter]` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/) настраиваемого атрибута. Можно указать, какие действия подходят для вашего действия с [ `IntentFilter` ](https://developer.xamarin.com/api/constructor/Android.App.IntentFilterAttribute.IntentFilterAttribute/p/System.String[]/) конструктор и категорий, соответствующие с [ `Categories` ](https://developer.xamarin.com/api/property/Android.App.IntentFilterAttribute.Categories/) свойство. По крайней мере одно действие должно быть указано (который именно действия приведены в конструкторе). `[IntentFilter]` может быть несколько раз, и каждый случай использования результатов в отдельном `<intent-filter/>` элемент в пределах `<activity/>`. Пример:

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
[IntentFilter (new[]{Intent.ActionView}, 
        Categories=new[]{Intent.CategorySampleCode, "my.custom.category"})]
public class MyActivity : Activity
{
}
```

В этом примере выводятся в следующем фрагменте xml:

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

Манифест Android также предоставляет способ для объявления свойств для всего приложения. Это выполняется через `<application>` элемента и его аналог, [приложения](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/) настраиваемого атрибута. Обратите внимание, что эти параметры (на уровне сборки) на уровне приложения, а не параметры каждого действия. Обычно объявляется `<application>` свойства для всего приложения и затем переопределить эти настройки (при необходимости) на основе каждого действия. 

Например, следующая `Application` добавляется атрибут **AssemblyInfo.cs** для указания того, приложение может быть отладки, что его имя, доступное для чтения пользователю **Мое приложение**, и что он использует `Theme.Light` стиль как тему по умолчанию для всех действий: 

```csharp
[assembly: Application (Debuggable=true,   
                        Label="My App",   
                        Theme="@android:style/Theme.Light")]
```

Это объявление вызывает должны быть созданы в следующем фрагменте XML **obj/Debug/android/AndroidManifest.xml**:

```xml
<application android:label="My App" 
             android:debuggable="true" 
             android:theme="@android:style/Theme.Light"
                ... />
```
В этом примере все действия в приложении по умолчанию будет `Theme.Light` стиля. При выборе действия темы `Theme.Dialog`, только то, что действие будет использовать `Theme.Dialog` стиля, а все остальные действия в приложении по умолчанию будет `Theme.Light` стиля, как указано в `<application>` элемент. 

`Application` Элемент не является единственным способом настройки `<application>` атрибуты. Кроме того, можно вставить атрибуты непосредственно в `<application>` элемент **Properties/AndroidManifest.xml**. Эти параметры сливаются в конечное `<application>` элемент, расположенный в **obj/Debug/android/AndroidManifest.xml**. Обратите внимание, что содержимое **Properties/AndroidManifest.xml** всегда переопределять данные, предоставляемые настраиваемых атрибутов. 

Существует множество атрибутов уровня приложения, которые можно настроить в `<application>` элемента; Дополнительные сведения об этих параметрах см. в разделе [открытые свойства](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/#Public_Properties) раздел [ApplicationAttribute](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/). 



## <a name="list-of-custom-attributes"></a>Список настраиваемых атрибутов

-   [Android.App.ActivityAttribute](https://developer.xamarin.com/api/type/Android.App.ActivityAttribute/) : приводит к возникновению ошибки [/manifest/application/activity](http://developer.android.com/guide/topics/manifest/activity-element.html) фрагмент XML 
-   [Android.App.ApplicationAttribute](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/) : приводит к возникновению ошибки [-манифест и приложение](http://developer.android.com/guide/topics/manifest/application-element.html) фрагмент XML 
-   [Android.App.InstrumentationAttribute](https://developer.xamarin.com/api/type/Android.App.InstrumentationAttribute/) : приводит к возникновению ошибки [/манифест/инструментария](http://developer.android.com/guide/topics/manifest/instrumentation-element.html) фрагмент XML 
-   [Android.App.IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/) : приводит к возникновению ошибки [//intent-filter](http://developer.android.com/guide/topics/manifest/intent-filter-element.html) фрагмент XML 
-   [Android.App.MetaDataAttribute](https://developer.xamarin.com/api/type/Android.App.MetaDataAttribute/) : приводит к возникновению ошибки [//meta-data](http://developer.android.com/guide/topics/manifest/meta-data-element.html) фрагмент XML 
-   [Android.App.PermissionAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionAttribute/) : приводит к возникновению ошибки [//permission](http://developer.android.com/guide/topics/manifest/permission-element.html) фрагмент XML 
-   [Android.App.PermissionGroupAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionGroupAttribute/) : приводит к возникновению ошибки [//permission-group](http://developer.android.com/guide/topics/manifest/permission-group-element.html) фрагмент XML 
-   [Android.App.PermissionTreeAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionTreeAttribute/) : приводит к возникновению ошибки [//permission-tree](http://developer.android.com/guide/topics/manifest/permission-tree-element.html) фрагмент XML 
-   [Android.App.ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/) : приводит к возникновению ошибки [/manifest/application/service](http://developer.android.com/guide/topics/manifest/service-element.html) фрагмент XML 
-   [Android.App.UsesLibraryAttribute](https://developer.xamarin.com/api/type/Android.App.UsesLibraryAttribute/) : приводит к возникновению ошибки [/manifest/application/uses-library](http://developer.android.com/guide/topics/manifest/uses-library-element.html) фрагмент XML 
-   [Android.App.UsesPermissionAttribute](https://developer.xamarin.com/api/type/Android.App.UsesPermissionAttribute/) : приводит к возникновению ошибки [/manifest/uses-permission](http://developer.android.com/guide/topics/manifest/uses-permission-element.html) фрагмент XML 
-   [Android.Content.BroadcastReceiverAttribute](https://developer.xamarin.com/api/type/Android.Content.BroadcastReceiverAttribute/) : приводит к возникновению ошибки [/manifest/application/receiver](http://developer.android.com/guide/topics/manifest/receiver-element.html) фрагмент XML 
-   [Android.Content.ContentProviderAttribute](https://developer.xamarin.com/api/type/Android.Content.ContentProviderAttribute/) : приводит к возникновению ошибки [/manifest/application/provider](http://developer.android.com/guide/topics/manifest/provider-element.html) фрагмент XML 
-   [Android.Content.GrantUriPermissionAttribute](https://developer.xamarin.com/api/type/Android.Content.GrantUriPermissionAttribute/) : приводит к возникновению ошибки [/manifest/application/provider/grant-uri-permission](http://developer.android.com/guide/topics/manifest/grant-uri-permission-element.html) фрагмент XML

