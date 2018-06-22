---
title: Устранение неполадок привязки
description: В этой статье приведены несколько распространенных ошибок, которые могут возникнуть при создании привязки, а также возможные причины и предлагаемые способы их устранения.
ms.prod: xamarin
ms.assetid: BB81FCCF-F7BF-4C78-884E-F02C49AA819A
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: da6286eed091114c117c723f462bbb8cac77034b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30771100"
---
# <a name="troubleshooting-bindings"></a>Устранение неполадок привязки

_В этой статье приведены несколько распространенных ошибок, которые могут возникнуть при создании привязки, а также возможные причины и предлагаемые способы их устранения._


## <a name="overview"></a>Обзор

Привязка библиотека Android ( **.aar** или **.jar**) файл редко является простой задачей; как правило, требуется дополнительное время на устранение проблем, возникающих из-за различий между Java и .NET.
Эти проблемы будут предотвратить Xamarin.Android привязки библиотеки Android и имитируют сообщения об ошибках в журнале сборки. В этом руководстве предоставляют некоторые советы по устранению неполадок, перечислены некоторые из наиболее распространенных проблем в следующих сценариях и оказаться полезными для успешной привязки библиотеку Android.

При привязке существующей библиотеки Android, это необходимо учитывать следующие моменты:

- **Внешние зависимости для библиотеки** &ndash; Any Java зависимости, необходимые для библиотеки Android должны быть включены в проект Xamarin.Android, как **ReferenceJar** или как  **EmbeddedReferenceJar**.

- **Уровень Android API, что библиотека Android является для различных версий** &ndash; он не поддерживается «перейти» уровень Android API; убедитесь, что такой же API предназначен для привязки проекта Xamarin.Android уровень (или выше) как библиотеку Android.

- **Версия Android JDK, который был использован для упаковки библиотеки Android** &ndash; ошибок привязки может возникнуть, если библиотека Android был создан с помощью другой версии JDK, от того, используется с Xamarin.Android. Если это возможно необходимо перекомпилируйте библиотеку Android, используя ту же версию JDK, используемая для установки Xamarin.Android.

Первым шагом для устранения неполадок с помощью привязки библиотеки Xamarin.Android является включение [выходных данных диагностики MSBuild](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output).
После включения выходных данных диагностики, перестройте проект привязки Xamarin.Android и изучите журнал сборки, чтобы найти сведения о том, что причиной проблемы.

Также может оказаться полезным декомпилировать библиотеки Android и изучить типы и методы, которые Xamarin.Android пытается привязать. Это рассматривается более подробно далее в этом руководстве.


## <a name="decompiling-an-android-library"></a>Восстановление библиотека Android

Изучение классы и методы классов Java позволяет ценную информацию, которая поможет вам привязки библиотеки.
[JD GUI](http://jd.benow.ca/) — это графическая программа, который может отобразить исходный код Java из **КЛАССА** файлы, содержащиеся в БАНКЕ. Он может выполняться в изолированном приложении или как подключаемый модуль IntelliJ или Eclipse.

Для декомпиляции открытого библиотеки Android **. JAR** файл с декомпилятор Java. Если библиотека еще **. AAR** файл, он необходим для извлечения файла **classes.jar** из файла архива. Ниже приведен пример снимка экрана с помощью графического интерфейса JD для анализа [Picasso](http://square.github.io/picasso/) JAR:

![Использование декомпилятор Java для анализа picasso 2.5.2.jar](troubleshooting-bindings-images/troubleshoot-bindings-01.png)

Как только decompiled библиотеки Android, изучите исходный код. Вообще говоря поиск:

- **Классы, которые имеют характеристики запутывания** &ndash; перечислены характеристики неопределенные классы:

    - Имя class содержит **$**, т. е. **$.class**
    - Имя класса полностью скомпрометированы символов нижнего регистра, т. е. **a.class**      

- **`import` операторы без ссылки библиотек** &ndash; неиспользуемые библиотеки идентифицировать и добавить эти зависимости проекта Xamarin.Android привязки с **действие при построении** из **ReferenceJar**  или **EmbedddedReferenceJar**.

> [!NOTE]
> Восстановление библиотеки Java может быть запрещено или юридические ограничения на основе местному законодательству или лицензии, под которой был опубликован библиотеки Java. При необходимости прикрепить служб юридических professional перед попыткой декомпилировать библиотеки Java и проверьте исходный код.


## <a name="inspect-apixml"></a>Проверьте API. XML

В рамках сборки проекта привязки Xamarin.Android создаст файл в формате **obj/Debug/api.xml**:

![Созданный api.xml под obj и отладки](troubleshooting-bindings-images/troubleshoot-bindings-02.png)

Этот файл предоставляет список всех API Java, что Xamarin.Android пытается привязки. Содержимое этого файла может помочь идентифицировать отсутствующих типы и методы, повторяющиеся привязки. Несмотря на то, что проверка этого файла трудоемкой и требующим много времени, он может предоставлять получить подсказки на то, что вызывает проблемы привязки. Например **api.xml** можно выяснить, что свойство возвращает тип неуместные или что имеются два типы, разделяющие же имя управляемого.


## <a name="known-issues"></a>Известные проблемы

В этом разделе будут перечислены некоторые из распространенных сообщений об ошибках или симптомы, my при попытке привязать библиотеку Android.


### <a name="problem-java-version-mismatch"></a>Проблема: Несоответствие версии Java

Иногда типы не будут созданы или непредвиденные сбои может возникать, если вы используете либо Java, по сравнению с библиотеке была скомпилирована в более новой или более поздней версии. Перекомпилируйте с той же версии JDK, который проекта Xamarin.Android библиотеки Android.


### <a name="problem-at-least-one-java-library-is-required"></a>Проблема: требуется хотя бы одна библиотека Java

Возникает ошибка «хотя бы одна библиотека Java не требуются,», даже если. JAR-ФАЙЛ был добавлен.

#### <a name="possible-causes"></a>Возможны следующие причины.

Убедитесь, что задано действие построения `EmbeddedJar`. Так как имеются несколько действий построения для. JAR-файла (например, `InputJar`, `EmbeddedJar`, `ReferenceJar` и `EmbeddedReferenceJar`), генератор привязки невозможно подобрать автоматически один из них по умолчанию. Дополнительные сведения о действиях сборки см. в разделе [действия построения](~/android/platform/binding-java-library/index.md).


### <a name="problem-binding-tools-cannot-load-the-jar-library"></a>Проблема: Привязка средства не удается загрузить. JAR-библиотеки

Не удается загрузить библиотеку генератор привязки. Библиотека JAR-ФАЙЛ.

#### <a name="possible-causes"></a>Возможные причины

Некоторые. Не удается загрузить библиотеки JAR, использующих запутывания кода (с помощью средства, такие как Proguard) средствами Java. Поскольку наш средство создает использование отражения Java и проектирование библиотеки ASM байтовый код, служебных программ зависимых отклонить скрытый библиотеки хотя средства Android среды выполнения может передать. Для этого достаточно выполнить привязку этих библиотек, вместо того чтобы использовать генератор привязки вручную.



### <a name="problem-missing-c-types-in-generated-output"></a>Проблема: Отсутствует типов C# в выходные данные.

Привязка **.dll** сборок, но промахов некоторых типов Java или созданного исходного кода C# не была выполнена сборка из-за сообщение о том, есть недостающие типы.

#### <a name="possible-causes"></a>Возможны следующие причины.

Эта ошибка может возникнуть несколько причин, указанных ниже:

-   Выполняется привязка библиотеки может ссылаться на второй библиотеки Java. Если открытый API для привязанного библиотеки используются типы из второй библиотеки, необходимо сослаться на управляемом привязки для второй библиотеки, а также.

-   Это возможно, что библиотеки подставленный из-за отражения Java, аналогично причина Ошибка загрузки библиотеки выше, вызывает непредвиденные загрузки метаданных. Инструментарий Xamarin.Android в настоящее время не удается разрешить этой ситуации. В таком случае библиотеки необходимо вручную привязать.

-   В среде выполнения .NET 4.0, которые не удалось загрузить сборки, когда он должен иметь произошла ошибка. Эта проблема исправлена в среде выполнения .NET 4.5.

-   Java позволяет открытый класс производным от класса, не являющиеся открытыми, но это не поддерживается в .NET. Так как генератор привязки не создает привязок для классов, не являющиеся открытыми, производных классов, таких, как они не могут быть созданы правильно. Чтобы устранить эту проблему, удалите запись метаданных для производных классов с помощью функции удаления узлов в **Metadata.xml**, или исправьте метаданные, создание класса неоткрытые открытый. Несмотря на то, что последнее решение будет создать привязку, что будет создавать исходного кода C#, класс неоткрытые использовать не следует.

    Пример:

    ```xml
    <attr path="/api/package[@name='com.some.package']/class[@name='SomeClass']"
        name="visibility">public</attr>
    ```

-   Средства, которые замаскировать библиотеки Java может помешать генератор привязки Xamarin.Android и его способность создавать классы-оболочки C#. В следующем фрагменте показано, как обновить **Metadata.xml** для unobfuscate имя класса:

    ```xml
    <attr path="/api/package[@name='{package_name}']/class[@name='{name}']"
        name="obfuscated">false</attr>
    ```

### <a name="problem-generated-c-source-does-not-build-due-to-parameter-type-mismatch"></a>Проблема: Созданный C# источника не была выполнена сборка из-за несоответствия типа параметра

Созданный исходный C# не создает. Переопределить параметр метода, которые не совпадают типы.

#### <a name="possible-causes"></a>Возможны следующие причины.

Xamarin.Android включает множество полей Java, которые сопоставляются с перечислений в C# привязок. Это может привести к несовместимости типа в привязках, созданный. Чтобы устранить эту проблему, необходимо изменить для использования перечислений сигнатуры методов, созданные на основе генератор привязки. Для получения дополнительной imformation см. в разделе [исправление перечисления](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md).

### <a name="problem-noclassdeffounderror-in-packaging"></a>Проблема: NoClassDefFoundError в пакете

`java.lang.NoClassDefFoundError` вызывается на этапе упаковки.

#### <a name="possible-causes"></a>Возможны следующие причины.

Наиболее вероятная причина этой ошибки является обязательным библиотеки Java необходимо добавить в проект приложения (**.csproj**). . JAR-файла, не разрешаются автоматически. Привязка библиотеки Java не всегда создается по сборке пользователя, который не существует в целевом устройстве или эмуляторе (например, карты Google **maps.jar**). Это не так, для поддержки проекта библиотеки Android, как у библиотеки. JAR внедряется в библиотеку dll. Например: [4288 ошибки](https://bugzilla.xamarin.com/show_bug.cgi?id=4288)

### <a name="problem-duplicate-custom-eventargs-types"></a>Проблема: Дублирование пользовательских типов EventArgs

Из-за повторяющихся пользовательских типов EventArgs ошибки сборки. Возникает ошибка следующим образом:

```shell
error CS0102: The type `Com.Google.Ads.Mediation.DismissScreenEventArgs' already contains a definition for `p0'
```

#### <a name="possible-causes"></a>Возможны следующие причины.

Это из-за конфликта некоторых типов событий, поступающих из более чем один тип «прослушиватель» интерфейса, который использует методы с одинаковыми именами. Например, если имеются два интерфейса Java, как показано в следующем примере, генератор создает `DismissScreenEventArgs` для обоих `MediationBannerListener` и `MediationInterstitialListener`, полученный в сообщении об ошибке.

```java
// Java:
public interface MediationBannerListener {
    void onDismissScreen(MediationBannerAdapter p0);
}
public interface MediationInterstitialListener {
    void onDismissScreen(MediationInterstitialAdapter p0);
}
```

Это сделано намеренно, чтобы длинные имена на типов аргументов событий, не используются. Чтобы избежать таких конфликтов, некоторые метаданные преобразования не требуется. Изменить [ **Transforms\Metadata.xml** ](https://github.com/xamarin/monodroid-samples/blob/master/AdMob/AdMob/Transforms/Metadata.xml) и добавьте `argsType` атрибут на один из интерфейсов (или метода интерфейса):

```xml
<attr path="/api/package[@name='com.google.ads.mediation']/
        interface[@name='MediationBannerListener']/method[@name='onDismissScreen']"
        name="argsType">BannerDismissScreenEventArgs</attr>

<attr path="/api/package[@name='com.google.ads.mediation']/
        interface[@name='MediationInterstitialListener']/method[@name='onDismissScreen']"
        name="argsType">IntersitionalDismissScreenEventArgs</attr>

<attr path="/api/package[@name='android.content']/
        interface[@name='DialogInterface.OnClickListener']"
        name="argsType">DialogClickEventArgs</attr>
```

### <a name="problem-class-does-not-implement-interface-method"></a>Проблема: Класс не реализует метод интерфейса

Сообщение об ошибке, указывающее, что созданный класс не реализует метод, который необходим для интерфейса, реализующего создаваемого класса. Тем не менее в созданный код, видно, что метод реализуется.

Ниже приведен пример ошибки:

```shell
obj\Debug\generated\src\Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.cs(8,23):
error CS0738: 'Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter' does not
implement interface member 'Oauth.Signpost.Http.IHttpRequest.Unwrap()'.
'Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.Unwrap()' cannot implement
'Oauth.Signpost.Http.IHttpRequest.Unwrap()' because it does not have the matching
return type of 'Java.Lang.Object'
```

#### <a name="possible-causes"></a>Возможны следующие причины.

Это проблема, возникающая с привязка методов Java с ковариантные возвращаемые типы. В этом примере метод `Oauth.Signpost.Http.IHttpRequest.UnWrap()` должно возвращать `Java.Lang.Object`. Тем не менее метод `Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.UnWrap()` имеет тип возвращаемого значения `HttpURLConnection`. Существует два способа для решения этой проблемы:

-   Добавьте объявление разделяемого класса для `HttpURLConnectionRequestAdapter` и явно реализуйте `IHttpRequest.Unwrap()`:

    ```csharp
    namespace Oauth.Signpost.Basic {
        partial class HttpURLConnectionRequestAdapter {
            Java.Lang.Object OauthSignpost.Http.IHttpRequest.Unwrap() {
                return Unwrap();
            }
        }
    }
    ```

-   Удалите ковариацию из созданного кода C#. Этот процесс включает добавление следующее преобразование для **Transforms\Metadata.xml** что приведет к сформированного кода C# иметь тип возвращаемого значения `Java.Lang.Object`:

    ```xml
    <attr
        path="/api/package[@name='oauth.signpost.basic']/class[@name='HttpURLConnectionRequestAdapter']/method[@name='unwrap']"
        name="managedReturn">Java.Lang.Object
    </attr>
    ```

### <a name="problem-name-collisions-on-inner-classes--properties"></a>Проблема: Имя конфликтов для внутреннего классы и свойства

Конфликтующие видимости наследуемых объектов.

На языке Java это не обязательно наличие такой же видимостью, что и родительский объект производного класса. Java будет просто решить эту проблему для вас. В C#, который должен быть явными, поэтому убедитесь, что все классы в иерархии имеют соответствующие видимость. В следующем примере показано изменение имени пакета Java из `com.evernote.android.job` для `Evernote.AndroidJob`:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

### <a name="problem-a-so-library-required-by-the-binding-is-not-loading"></a>Проблема: A **.so** — библиотеки, необходимые для привязки не удалось загрузить

Некоторые проекты привязки также могут потребоваться функции в **.so** библиотеки. Возможно, что не будет автоматически загружать Xamarin.Android **.so** библиотеки. При выполнении упакованного кода Java для осуществления вызовов JNI и сообщение об ошибке завершится Xamarin.Android _java.lang.UnsatisfiedLinkError: собственный метод, не найден:_ будет отображаться в logcat ожидания для приложения.

Для этого исправления ситуации нужно вручную загрузить **.so** библиотеки с помощью вызова `Java.Lang.JavaSystem.LoadLibrary`. Например при условии, что проект Xamarin.Android общие библиотеки **libpocketsphinx_jni.so** включен в проект привязки с помощью действия построения **EmbeddedNativeLibrary**, следующие фрагмент кода (выполняется перед началом использования общей библиотеки) будет загружать **.so** библиотеки:

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="summary"></a>Сводка

В этой статье перечислены распространенные проблемы, связанные с привязками, Java и описаны способы их устранения.


## <a name="related-links"></a>Связанные ссылки

- [Проекты библиотеки](http://developer.android.com/tools/projects/index.html#LibraryProjects)
- [Работа с JNI](~/android/platform/java-integration/working-with-jni.md)
- [Включить вывод диагностических сообщений](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)
- [Xamarin для разработчиков решений Android](~/android/get-started/java-developers.md)
- [JD-GUI](http://jd.benow.ca/)
