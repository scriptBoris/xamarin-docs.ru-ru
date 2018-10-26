---
title: ProGuard
description: Xamarin.Android ProGuard — это средство сжатия, оптимизации и предварительной проверки файла класса Java. Оно обнаруживает и удаляет неиспользуемый код, анализирует и оптимизирует байт-код. В этом руководстве описываются принципы работы ProGuard, включение этого средства в проекте и его настройка. Здесь также приводится несколько примеров конфигураций ProGuard.
ms.prod: xamarin
ms.assetid: 29C0E850-3A49-4618-9078-D59BE0284D5A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 998232119159c5dc3a284dd51d55dc381271a4e0
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117399"
---
# <a name="proguard"></a>ProGuard

_Xamarin.Android ProGuard — это средство для сжатия, оптимизации и предварительной проверки файла класса Java. Оно обнаруживает и удаляет неиспользуемый код, анализирует и оптимизирует байт-код. В этом руководстве описываются принципы работы ProGuard, включение этого средства в проекте и его настройка. Здесь также приводится несколько примеров конфигураций ProGuard._


## <a name="overview"></a>Обзор

ProGuard обнаруживает и удаляет неиспользуемые классы, поля, методы и атрибуты из упакованного приложения. Средство может выполнять эти же задачи для ссылочных библиотек (чтобы обойти ограничение на предел в 64 тысячи ссылок). Средство ProGuard из пакета SDK для Android будет также оптимизировать байт-код и удалять инструкции неиспользуемого кода. ProGuard считывает **входные JAR-файлы**, а затем сжимает, оптимизирует и предварительно проверяет их. Оно записывает результаты в один **выходной JAR-файл** или несколько. 

ProGuard обрабатывает входные пакеты APK, выполняя следующие действия: 

1.  **Действие сжатия**  &ndash; ProGuard рекурсивно определяет, какие классы и члены классов используются. Все остальные классы и члены классов не учитываются. 

2.  **Действие оптимизации** &ndash; ProGuard оптимизирует код. 
    Помимо других оптимизаций классы и методы, которые не являются точками входа, можно сделать закрытыми, статическими или окончательными, неиспользуемые параметры можно удалить, а некоторые методы можно встроить. 

3.  **Шаг обфускации** &ndash;При разработке в собственной среде Android средство ProGuard переименовывает классы и члены классов, которые не являются точками входа. Сохраненные точки входа будут по-прежнему доступны по своим исходным именам. Тем не менее этот шаг не поддерживается в Xamarin.Android, так как приложение компилируется до промежуточного языка (IL).

4.  **Действие предварительной проверки** &ndash; ProGuard выполняет AOT-проверки байт-кода Java и помечает файлы класса для виртуальной машины Java. Это единственное действие, где не используются точки входа. 

Каждое из этих действий является *необязательным*. Как будет описываться в следующем разделе, ProGuard в Xamarin.Android использует только часть этих действий. 



## <a name="proguard-in-xamarinandroid"></a>ProGuard в Xamarin.Android

Конфигурация ProGuard в Xamarin.Android не скрывает пакет APK. По сути, скрытие невозможно включить с помощью ProGuard (даже при использовании настраиваемых файлов конфигурации). Таким образом, ProGuard в Xamarin.Android выполняет только шаги по **сжатию** и **оптимизации**: 

[![Действия сжатия и оптимизации](proguard-images/01-xa-chain-sml.png)](proguard-images/01-xa-chain.png#lightbox)

Прежде чем использовать ProGuard, важно заранее узнать, как это средство работает в процессе сборки `Xamarin.Android`. В этом процессе используется два отдельных действия: 

1.  Компоновщик Xamarin Android

2.  ProGuard

Каждое из действий описывается далее.



### <a name="linker-step"></a>Действие компоновщика

Компоновщик Xamarin.Android выполняет статический анализ приложения, чтобы определить следующие моменты: 

-   фактически используемые сборки;

-   фактически используемые типы;

-   фактически используемые элементы. 

Компоновщик всегда будет выполняться до запуска ProGuard. Поэтому компоновщик может удалить сборку, тип или элемент, в которых может предполагаться запуск ProGuard. (Дополнительные сведения о компоновке в Xamarin.Android см. в статье [Компоновка в Android](~/android/deploy-test/linker.md).)



### <a name="proguard-step"></a>Действие ProGuard

После успешного завершения действия компоновщика запускается ProGuard для удаления неиспользуемого байт-кода Java. Это действие оптимизирует пакет APK. 



## <a name="using-proguard"></a>Использование ProGuard

Чтобы использовать ProGuard в проекте приложения, сначала нужно включить это средство. Затем можно либо указать процессу сборки Xamarin.Android использовать файл конфигурации ProGuard по умолчанию, либо создать собственный файл конфигурации для ProGuard. 



### <a name="enabling-proguard"></a>Включение ProGuard

Чтобы включить ProGuard в проекте приложения, выполните следующие действия:

1.  Убедитесь, что для проекта задана конфигурация **Выпуск** (это важно, так как перед запуском ProGuard должен быть запущен компоновщик): 

    [![Выбор конфигурации выпуска](proguard-images/02-set-release-sml.png)](proguard-images/02-set-release.png#lightbox)
   
2.  Включите ProGuard, установив флажок **Включить ProGuard** на вкладке **Упаковка**, которая откроется после выбора пунктов **Свойства > Параметры Android**: 

    [![Выбранный параметр "Включить Proguard"](proguard-images/03-enable-proguard-sml.png)](proguard-images/03-enable-proguard.png#lightbox)

Для большинства приложений Xamarin.Android файла конфигурации ProGuard по умолчанию, предоставляемого Xamarin.Android, будет достаточно, чтобы удалить весь неиспользуемый код (и только его). Чтобы просмотреть конфигурацию ProGuard по умолчанию, откройте файл в папке **obj\\Release\\proguard\\proguard_xamarin.cfg**. В следующем разделе описывается создание настраиваемого файла конфигурации ProGuard. 



### <a name="customizing-proguard"></a>Настройка ProGuard

При необходимости можно добавить настраиваемый файл конфигурации ProGuard, чтобы лучше контролировать инструментарий ProGuard. Например, может потребоваться явно указать ProGuard, какие классы следует сохранить. Для этого создайте **CFG**-файл и примените действие сборки `ProGuardConfiguration` в области **Свойства** в **обозревателе решений**: 

[![Выбранное действие сборки ProguardConfiguration](proguard-images/04-build-action-sml.png)](proguard-images/04-build-action.png#lightbox)

Следует помнить, что этот файл конфигурации не заменяет файл Xamarin.Android **proguard_xamarin.cfg**, так как оба эти файла используются с ProGuard. 

В приведенном ниже примере показан стандартный файл конфигурации ProGuard:
    

    # This is Xamarin-specific (and enhanced) configuration.

    -dontobfuscate

    -keep class mono.MonoRuntimeProvider { *; <init>(...); }
    -keep class mono.MonoPackageManager { *; <init>(...); }
    -keep class mono.MonoPackageManager_Resources { *; <init>(...); }
    -keep class mono.android.** { *; <init>(...); }
    -keep class mono.java.** { *; <init>(...); }
    -keep class mono.javax.** { *; <init>(...); }
    -keep class opentk.platform.android.AndroidGameView { *; <init>(...); }
    -keep class opentk.GameViewBase { *; <init>(...); }
    -keep class opentk_1_0.platform.android.AndroidGameView { *; <init>(...); }
    -keep class opentk_1_0.GameViewBase { *; <init>(...); }

    -keep class android.runtime.** { <init>(***); }
    -keep class assembly_mono_android.android.runtime.** { <init>(***); }
    # hash for android.runtime and assembly_mono_android.android.runtime.
    -keep class md52ce486a14f4bcd95899665e9d932190b.** { *; <init>(...); }
    -keepclassmembers class md52ce486a14f4bcd95899665e9d932190b.** { *; <init>(...); }

    # Android's template misses fluent setters...
    -keepclassmembers class * extends android.view.View {
       *** set*(***);
    }

    # also misses those inflated custom layout stuff from xml...
    -keepclassmembers class * extends android.view.View {
       <init>(android.content.Context,android.util.AttributeSet);
       <init>(android.content.Context,android.util.AttributeSet,int);
    }
    

В некоторых случаях ProGuard не удается правильно проанализировать приложение. Он способен удалить код, который действительно требуется приложению. В такой ситуации можно добавить строку `-keep` в настраиваемый файл конфигурации ProGuard: 

    -keep public class MyClass

В этом примере классу `MyClass` задано фактическое имя класса, которое должно быть пропущено средством ProGuard.

Можно также зарегистрировать собственные имена с заметками `[Register]` и использовать их для настройки правил ProGuard. Поддерживается регистрация имен для адаптеров, представлений, широковещательных приемников, поставщиков содержимого, действий и фрагментов. Дополнительные сведения об использовании настраиваемого атрибута `[Register]` см. в статье [Работа с JNI](~/android/platform/java-integration/working-with-jni.md).


### <a name="proguard-options"></a>Параметры ProGuard

ProGuard предлагает ряд параметров, которые можно настроить для более точного контроля его работы. Полная справочная документация по использованию ProGuard доступна в [руководстве по ProGuard](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/index.html#manual/introduction.html). 

Xamarin.Android поддерживает следующие параметры ProGuard: 


-    [Входные и выходные параметры](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#iooptions)

-    [Параметры -keep](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#keepoptions)

-    [Параметры сжатия](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#shrinkingoptions)

-    [Общие параметры](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#generaloptions)

-    [Пути классов](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#classpath)

-    [Имена файлов](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#filename)

-    [Фильтры файлов](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#filefilters)

-    [Фильтры](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#filters)

-    [Обзор параметров `Keep`](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#keepoverview)

-    [Модификаторы параметра -keep](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#keepoptionmodifiers)

-    [Спецификации класса](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#classspecification)

Xamarin.Android *не учитывает* следующие параметры:

-    [Параметры оптимизации](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#optimizationoptions)

-    [Параметры скрытия](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#obfuscationoptions) 

-    [Параметры предварительной проверки](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#preverificationoptions)



## <a name="proguard-and-android-nougat"></a>ProGuard и Android Nougat

При попытке использовать ProGuard в Android 7.0 или более поздней версии необходимо скачать более новую версию ProGuard, поскольку в пакет SDK для Android не входит новая версия, совместимая с пакетом JDK 1.8.

Этот [пакет NuGet](https://www.nuget.org/packages/name.atsushieno.proguard.facebook/5.3.0) можно использовать для установки более новой версии `proguard.jar`. Дополнительные сведения об обновлении пакета SDK для Android по умолчанию `proguard.jar` см. в этом обсуждении на сайте [Stack Overflow](http://stackoverflow.com/questions/39514518/xamarin-android-proguard-unsupported-class-version-number-52-0/39514706#39514706).

Все версии ProGuard представлены на [странице SourceForge](https://sourceforge.net/projects/proguard/files/). 



## <a name="example-proguard-configurations"></a>Примеры конфигураций ProGuard

Ниже приведены два примера файлов конфигурации ProGuard. Обратите внимание, что в этих случаях процесс сборки Xamarin.Android предоставит **входные**, **выходные** JAR-файлы и JAR-файлы **библиотеки**. Таким образом, можно сосредоточиться на других параметрах, например `-keep`. 

### <a name="a-simple-android-activity"></a>Простое действие Android

В следующем примере показана конфигурация для простого действия Android:

    -injars  bin/classes
    -outjars bin/classes-processed.jar
    -libraryjars /usr/local/java/android-sdk/platforms/android-9/android.jar

    -dontpreverify
    -repackageclasses ''
    -allowaccessmodification
    -optimizations !code/simplification/arithmetic

    -keep public class mypackage.MyActivity

### <a name="a-complete-android-application"></a>Законченное приложение Android

В следующем примере показана конфигурация для завершенного приложения Android:

    -injars  bin/classes
    -injars  libs
    -outjars bin/classes-processed.jar
    -libraryjars /usr/local/java/android-sdk/platforms/android-9/android.jar

    -dontpreverify
    -repackageclasses ''
    -allowaccessmodification
    -optimizations !code/simplification/arithmetic
    -keepattributes *Annotation*

    -keep public class * extends android.app.Activity
    -keep public class * extends android.app.Application
    -keep public class * extends android.app.Service
    -keep public class * extends android.content.BroadcastReceiver
    -keep public class * extends android.content.ContentProvider

    -keep public class * extends android.view.View {
    public <init>(android.content.Context);
    public <init>(android.content.Context, android.util.AttributeSet);
    public <init>(android.content.Context, android.util.AttributeSet, int);
    public void set*(...);
    }

    -keepclasseswithmembers class * {
    public <init>(android.content.Context, android.util.AttributeSet);
    }

    -keepclasseswithmembers class * {
    public <init>(android.content.Context, android.util.AttributeSet, int);
    }

    -keepclassmembers class * implements android.os.Parcelable {
    static android.os.Parcelable$Creator CREATOR;
    }

    -keepclassmembers class **.R$* {
    public static <fields>;
    }


## <a name="proguard-and-the-xamarinandroid-build-process"></a>ProGuard и процесс сборки Xamarin.Android

В следующих разделах содержатся сведения о работе ProGuard в сборке **Выпуск** Xamarin.Android.


### <a name="what-command-is-proguard-running"></a>Какая команда используется для запуска ProGuard?

ProGuard — это просто файл `.jar` в составе пакета SDK для Android. Поэтому он вызывается с помощью команды: 

```shell
java -jar proguard.jar options ...
```

### <a name="the-proguard-task"></a>Задача ProGuard

Задача ProGuard находится в сборке **Xamarin.Android.Build.Tasks.dll**. Она является частью целевого объекта `_CompileToDalvikWithDx`, который входит в состав целевого объекта `_CompileDex`. 

Далее приводится пример параметров по умолчанию, сгенерированных после создания проекта с помощью команд **Файл > Создать проект**: 

    ProGuardJarPath = C:\Android\android-sdk\tools\proguard\lib\proguard.jar
    AndroidSdkDirectory = C:\Android\android-sdk\
    JavaToolPath = C:\Program Files (x86)\Java\jdk1.8.0_92\\bin
    ProGuardToolPath = C:\Android\android-sdk\tools\proguard\
    JavaPlatformJarPath = C:\Android\android-sdk\platforms\android-25\android.jar
    ClassesOutputDirectory = obj\Release\android\bin\classes
    AcwMapFile = obj\Release\acw-map.txt
    ProGuardCommonXamarinConfiguration = obj\Release\proguard\proguard_xamarin.cfg
    ProGuardGeneratedReferenceConfiguration = obj\Release\proguard\proguard_project_references.cfg
    ProGuardGeneratedApplicationConfiguration = obj\Release\proguard\proguard_project_primary.cfg
    ProGuardConfigurationFiles

      {sdk.dir}tools\proguard\proguard-android.txt;
      {intermediate.common.xamarin};
      {intermediate.references};
      {intermediate.application};
      ;
     
    JavaLibrariesToEmbed = C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\MonoAndroid\v7.0\mono.android.jar
    ProGuardJarInput = obj\Release\proguard\__proguard_input__.jar
    ProGuardJarOutput = obj\Release\proguard\__proguard_output__.jar
    DumpOutput = obj\Release\proguard\dump.txt
    PrintSeedsOutput = obj\Release\proguard\seeds.txt
    PrintUsageOutput = obj\Release\proguard\usage.txt
    PrintMappingOutput = obj\Release\proguard\mapping.txt

В следующем примере показана стандартная команда ProGuard, которая выполняется в интегрированной среде разработки:

```cmd
C:\Program Files (x86)\Java\jdk1.8.0_92\\bin\java.exe -jar C:\Android\android-sdk\tools\proguard\lib\proguard.jar -include obj\Release\proguard\proguard_xamarin.cfg -include obj\Release\proguard\proguard_project_references.cfg -include obj\Release\proguard\proguard_project_primary.cfg "-injars 'obj\Release\proguard\__proguard_input__.jar';'C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\MonoAndroid\v7.0\mono.android.jar'" "-libraryjars 'C:\Android\android-sdk\platforms\android-25\android.jar'" -outjars "obj\Release\proguard\__proguard_output__.jar" -optimizations !code/allocation/variable
```

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="file-issues"></a>Проблемы с файлами

Когда ProGuard считывает файлы конфигурации, может отображаться следующее сообщение об ошибке: 

    Unknown option '-keep' in line 1 of file 'proguard.cfg'

Как правило, эта проблема возникает в Windows из-за неправильной кодировки файла `.cfg`. ProGuard не удается обработать _метку порядка следования байтов_ (BOM), которая может находиться в текстовых файлах. Если метка BOM присутствует, ProGuard завершит работу с указанной выше ошибкой. 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Чтобы избежать этой проблемы, измените настраиваемый файл конфигурации в текстовом редакторе, который позволит сохранить файл без метки BOM. Чтобы устранить эту проблему, для текстового редактора необходимо задать кодировку `UTF-8`. Например, в текстовом редакторе [Notepad ++](https://notepad-plus-plus.org/) можно сохранять файлы без отметки BOM, выбрав **Кодировка &gt; Кодировать в UTF-8 без BOM** при сохранении файла. 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

Чтобы избежать этой проблемы, сохраните настраиваемый файл конфигурации в текстовом редакторе, который позволяет пропускать метку BOM. 

-----


### <a name="other-issues"></a>Другие проблемы

Распространенные проблемы, которые могут возникнуть, (и их решения) при использовании ProGuard рассматриваются на странице ProGuard [Устранение неполадок](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/index.html#manual/troubleshooting.html).


## <a name="summary"></a>Сводка

В этом руководстве рассматривались принципы работы ProGuard в Xamarin.Android, активация этого средства в проекте и его настройка. Здесь приводился пример конфигураций ProGuard и описывались решения распространенных проблем. Дополнительные сведения о средстве ProGuard и Android см. в статье о [сжатии кода и ресурсов](http://developer.android.com/tools/help/proguard.html). 


## <a name="related-links"></a>Связанные ссылки

- [Подготовка приложения к выпуску](~/android/deploy-test/release-prep/index.md)
