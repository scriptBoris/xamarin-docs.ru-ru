---
title: Устранение неполадок с привязками
description: В этой статье перечислены несколько распространенных ошибок, которые могут возникнуть при создании привязки, а также возможные причины и предлагаемые способы их устранения.
ms.prod: xamarin
ms.assetid: BB81FCCF-F7BF-4C78-884E-F02C49AA819A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: f54da980834b44bbca7dc8619943769f8f429a7a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115293"
---
# <a name="troubleshooting-bindings"></a>Устранение неполадок с привязками

_В этой статье перечислены несколько распространенных ошибок, которые могут возникнуть при создании привязки, а также возможные причины и предлагаемые способы их устранения._


## <a name="overview"></a>Обзор

Привязка библиотеки Android ( **.aar** или **.jar**) файл редко является простой задачей; она обычно требует дополнительных усилий по уменьшению проблем, возникающих из-за различий между Java и .NET.
Эти проблемы запрещает привязка библиотеки Android Xamarin.Android и имитируют сообщения об ошибках в журнале сборки. В этом руководстве будет предоставить некоторые советы по устранению неполадок, перечислены некоторые из наиболее распространенных проблем и сценарии и могут оказаться полезными для успешной привязки библиотеки Android.

Во время привязки существующей библиотеки Android, бывает необходимо помнить следующее:

- **Внешние зависимости для библиотеки** &ndash; Any Java зависимости, необходимые для библиотеки Android должен быть включен в проект Xamarin.Android, как **ReferenceJar** или как  **EmbeddedReferenceJar**.

- **Уровень Android API, что библиотеки Android ориентирован** &ndash; он уже не сможете «понизить» уровень Android API; убедитесь, что проект привязки Xamarin.Android предназначен тот же API, уровень (или выше) как библиотеки Android.

- **Версия Android JDK, который был использован для пакета библиотеки Android** &ndash; ошибок привязки может возникать, если библиотеки Android создана с помощью другой версии JDK, отличного от того, используется для Xamarin.Android. Если это возможно выполните повторную компиляцию библиотеки Android, используя ту же версию JDK, который используется для установки Xamarin.Android.

Первым шагом устранения неполадок с привязка библиотеки Xamarin.Android является возможность [вывод диагностики MSBuild](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output).
После включения выходных данных диагностики, перестройте проект Xamarin.Android привязки и проверьте журнал построения, чтобы найти сведения о том, что причиной проблемы.

Он также могут оказаться полезными декомпилировать библиотеки Android и изучить типы и методы, которые пытается привязать Xamarin.Android. Это рассматривается более подробно далее в этом руководстве.


## <a name="decompiling-an-android-library"></a>Восстановление библиотеку для Android

Проверять классы и методы классов Java может предоставить ценную информацию, которая будет полезна при привязке библиотеки.
[Графического интерфейса JD](http://jd.benow.ca/) — это графическая программа, которая может отображать исходный код Java из **класс** файлы, содержащиеся в JAR-ФАЙЛ. Он может выполняться как никакое отдельное приложение или как подключаемый модуль IntelliJ или Eclipse.

Чтобы декомпилировать открытую библиотеку Android **. JAR-** файл с декомпилятор Java. Если библиотека **. AAR** файл, это необходимо, чтобы извлечь файл **classes.jar** из файла архива. Ниже приведен пример снимка экрана с помощью графического интерфейса JD для анализа [Пикассо](http://square.github.io/picasso/) JAR:

![Использование Java декомпилятор для анализа Пикассо 2.5.2.jar](troubleshooting-bindings-images/troubleshoot-bindings-01.png)

Как только декомпилированным библиотеки Android, изучите исходный код. Вообще говоря поиск:

- **Классы, которые имеют характеристики запутывания** &ndash; характеристики скрытый классов включают:

    - Имя класса включает **$**, т. е. **$.class**
    - Имя класса полностью включает в себя символы в нижнем регистре, т. е. **a.class**      

- **`import` инструкции для библиотек, на которые нет ссылок** &ndash; идентификации без ссылки библиотеки и добавить эти зависимости в проект Xamarin.Android привязки с **действие при построении** из **ReferenceJar**  или **EmbedddedReferenceJar**.

> [!NOTE]
> Декомпиляции библиотеки Java может быть запрещено или юридические ограничения на основе местного законодательства или лицензии, под которой была опубликована библиотеке Java. При необходимости выполнить прикрепление в службах юридические professional перед попыткой декомпилировать библиотеки Java и ознакомиться с исходным кодом.


## <a name="inspect-apixml"></a>Изучите API. XML

Как часть сборки проекта привязки, Xamarin.Android создаст имя файла XML **obj/Debug/api.xml**:

![Созданный api.xml под obj/Debug](troubleshooting-bindings-images/troubleshoot-bindings-02.png)

Этот файл предоставляет список всех API-интерфейсы Java, что Xamarin.Android пытается привязки. Содержимое этого файла может помочь выявить любые отсутствующие типы или методы, повторяющаяся привязка. Несмотря на то, что проверки этого файла сил и времени, может предоставлять для подсказки, на что может быть причиной возникновения проблемы любой привязки. Например **api.xml** может показать, что свойство возвращает неподходящего типа, или что имеются два типов данной общей папки с тем же именем управляемого.


## <a name="known-issues"></a>Известные проблемы

В этом разделе будут перечислены некоторые распространенные сообщения об ошибках или симптомы, Мой при попытке привязать библиотеку Android.


### <a name="problem-java-version-mismatch"></a>Проблема: Несовпадение версий Java

Иногда типы не будут созданы или непредвиденных сбоев может возникать при использовании либо новее или старше версию по сравнению с библиотеки был скомпилирован с помощью Java. Выполните повторную компиляцию библиотеки Android с помощью той же версии JDK, использующего проект Xamarin.Android.


### <a name="problem-at-least-one-java-library-is-required"></a>Проблема: необходим хотя бы одна библиотека Java

При возникновении ошибки «хотя бы одна библиотека Java является обязательным,», даже если. JAR-ФАЙЛ был добавлен.

#### <a name="possible-causes"></a>Возможные причины:

Убедитесь, что действие построения присваивается `EmbeddedJar`. Так как существует несколько действий сборки для. JAR-файлы (такие как `InputJar`, `EmbeddedJar`, `ReferenceJar` и `EmbeddedReferenceJar`), генератор привязки не может автоматически угадать какой из них следует использовать по умолчанию. Дополнительные сведения о действиях при сборке, см. в разделе [действия построения](~/android/platform/binding-java-library/index.md).


### <a name="problem-binding-tools-cannot-load-the-jar-library"></a>Проблема: Привязка средства не удается загрузить. Библиотеки JAR-файлов

Не удается загрузить библиотеку генератор привязки. Библиотеки JAR-файлов.

#### <a name="possible-causes"></a>Возможные причины

Некоторые. Не удалось загрузить библиотеки JAR, использующих запутывания кода (с помощью инструментов, как Proguard) с помощью инструментов Java. Поскольку наше средство делает использование отражения Java и байт-кода ASM, проектирование библиотеки, эти зависимые средства может отклонить скрытый библиотеки, хотя средства среды выполнения Android может передать. Решением этой проблемы является наличии привязку этих библиотек, вместо того чтобы использовать генератор привязки.



### <a name="problem-missing-c-types-in-generated-output"></a>Проблема: Отсутствует C# типов в выходные данные.

Привязка **.dll** сборки, но пропускает некоторые типы Java или созданный C# источника не выполняет сборку из-за сообщение о том, существует типа отсутствует.

#### <a name="possible-causes"></a>Возможные причины:

Эта ошибка может возникать несколько причин, как показано ниже:

-   Библиотеки привязки может ссылаться на второй библиотеки Java. Если открытый API для привязанного библиотека использует типы из второй библиотеки, управляемом привязку для второй библиотеки, а также необходимо ссылаться.

-   Вполне возможно, что из-за отражения Java, аналогичную причина Ошибка загрузки библиотеки выше, вызывает непредвиденные загрузку метаданных внедренном в библиотеку. Xamarin.Android инструментарий не способен разрешать в настоящее время такой ситуации. В таком случае библиотеки необходимо вручную привязать.

-   Произошла ошибка в среде выполнения .NET 4.0, которая не удалось загрузить сборки, у него должно быть. Эта проблема будет исправлена в среде выполнения .NET 4.5.

-   Java позволяет открытый класс производным от класса, не являющиеся открытыми, но это не поддерживается в .NET. Так как генератор привязки не создает привязки для закрытые классы, производные классы, такие, как они не могут быть созданы правильно. Чтобы устранить эту проблему, удалите запись метаданных для производных классов с помощью функции remove узлов в **Metadata.xml**, или исправьте метаданные, которые делает класс закрытые открытый. Несмотря на то, что последнее решение будет создать привязку, чтобы C# построит источника, не следует использовать класс не являющиеся открытыми.

    Пример:

    ```xml
    <attr path="/api/package[@name='com.some.package']/class[@name='SomeClass']"
        name="visibility">public</attr>
    ```

-   Средства, закрывающие библиотеки Java может помешать генератор привязки Xamarin.Android и его способность создавать C# классы-оболочки. В следующем фрагменте показано, как обновить **Metadata.xml** для unobfuscate имя класса:

    ```xml
    <attr path="/api/package[@name='{package_name}']/class[@name='{name}']"
        name="obfuscated">false</attr>
    ```

### <a name="problem-generated-c-source-does-not-build-due-to-parameter-type-mismatch"></a>Проблема: Создается C# источника не выполняет сборку из-за несоответствия типа параметра

Созданный C# источника не выполняет сборку. Переопределить параметр метода, которые не совпадают типы.

#### <a name="possible-causes"></a>Возможные причины:

Xamarin.Android включает в себя различные поля Java, которые сопоставляются с перечислений в C# привязки. Это может привести к несовместимости по типам в созданный привязках. Чтобы устранить эту проблему, подписи методов, созданные из генератора, привязки должны быть изменены для использования перечислений. Дополнительные imformation см. в разделе [исправление перечисления](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md).

### <a name="problem-noclassdeffounderror-in-packaging"></a>Проблема: NoClassDefFoundError в упаковке

`java.lang.NoClassDefFoundError` вызывается на этапе упаковки.

#### <a name="possible-causes"></a>Возможные причины:

Наиболее вероятная причина этой ошибки является обязательным библиотеки Java необходимо добавить в проект приложения (**.csproj**). . JAR-файлы, не разрешаются автоматически. Привязка библиотеки Java не всегда создается для пользовательской сборкой, не существует в целевом устройстве или эмуляторе (например Google Maps **maps.jar**). Это не так, для поддержки проекта библиотеки Android, как у библиотеки. JAR-ФАЙЛ внедряются в библиотеки dll. Например: [4288 ошибки](https://bugzilla.xamarin.com/show_bug.cgi?id=4288)

### <a name="problem-duplicate-custom-eventargs-types"></a>Проблема: Дублирование пользовательских типов EventArgs

Сборка завершается сбоем из-за повторяющихся пользовательских типов EventArgs. Ошибка следующим образом:

```shell
error CS0102: The type `Com.Google.Ads.Mediation.DismissScreenEventArgs' already contains a definition for `p0'
```

#### <a name="possible-causes"></a>Возможные причины:

Это из-за некоторых конфликта между типами событий, полученных из более чем один тип интерфейса «прослушиватель», который использует методы с одинаковыми именами. Например, если имеются два интерфейса Java, как показано в следующем примере, генератор создает `DismissScreenEventArgs` для обоих `MediationBannerListener` и `MediationInterstitialListener`, полученный в ошибке.

```java
// Java:
public interface MediationBannerListener {
    void onDismissScreen(MediationBannerAdapter p0);
}
public interface MediationInterstitialListener {
    void onDismissScreen(MediationInterstitialAdapter p0);
}
```

Это сделано намеренно, чтобы длинные имена на типы аргументов событий, удается избежать. Чтобы избежать таких конфликтов, некоторые метаданные преобразования не требуется. Изменить [ **Transforms\Metadata.xml** ](https://github.com/xamarin/monodroid-samples/blob/master/AdMob/AdMob/Transforms/Metadata.xml) и добавьте `argsType` атрибут на одном из интерфейсов (или метода интерфейса):

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

Сообщение об ошибке создается, указывающее, что созданный класс не реализует метод, который необходим для интерфейса, который реализует созданный класс. Тем не менее изучение созданного кода, можно увидеть, что метод реализуется.

Вот пример ошибки:

```shell
obj\Debug\generated\src\Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.cs(8,23):
error CS0738: 'Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter' does not
implement interface member 'Oauth.Signpost.Http.IHttpRequest.Unwrap()'.
'Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.Unwrap()' cannot implement
'Oauth.Signpost.Http.IHttpRequest.Unwrap()' because it does not have the matching
return type of 'Java.Lang.Object'
```

#### <a name="possible-causes"></a>Возможные причины:

Это проблема, которая происходит при использовании привязки методов Java с помощью ковариантные типы возвращаемого значения. В этом примере метод `Oauth.Signpost.Http.IHttpRequest.UnWrap()` должно возвращать `Java.Lang.Object`. Тем не менее метод `Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.UnWrap()` имеет тип возвращаемого значения `HttpURLConnection`. Чтобы устранить эту проблему двумя способами:

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

-   Удалить ковариацию из созданного C# кода. Этот процесс включает добавление следующее преобразование для **Transforms\Metadata.xml** вызывающее созданный C# коду иметь тип возвращаемого значения `Java.Lang.Object`:

    ```xml
    <attr
        path="/api/package[@name='oauth.signpost.basic']/class[@name='HttpURLConnectionRequestAdapter']/method[@name='unwrap']"
        name="managedReturn">Java.Lang.Object
    </attr>
    ```

### <a name="problem-name-collisions-on-inner-classes--properties"></a>Проблема: Имя конфликтов на внутренние классы и свойства

Конфликтующие включена видимость унаследованных объектов.

В Java это не обязательно наличие ту же видимость, что родительского в производном классе. Java будет просто исправить это для вас. В C#, должна быть явными, поэтому убедитесь, что все классы в иерархии имеют соответствующие видимость. Приведенный ниже показано, как изменить имя пакета Java из `com.evernote.android.job` для `Evernote.AndroidJob`:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

### <a name="problem-a-so-library-required-by-the-binding-is-not-loading"></a>Проблема: A **.so** — библиотеки, необходимые в привязке не удалось загрузить

Некоторые проекты привязки может также зависеть от функциональные возможности **.so** библиотеки. Возможно, что Xamarin.Android не будут загружаться автоматически **.so** библиотеки. При выполнении упакованного кода Java для вызова JNI и сообщение об ошибке не удастся Xamarin.Android _java.lang.UnsatisfiedLinkError: собственный метод, не найден:_ будет отображаться в logcat out для приложения.

Способ устранения этой проблемы является вручную загрузить **.so** библиотеки с помощью вызова `Java.Lang.JavaSystem.LoadLibrary`. Например при условии, что проект Xamarin.Android общая библиотека **libpocketsphinx_jni.so** включен в проект привязки с действием сборки **EmbeddedNativeLibrary**, следующий фрагмент кода (выполняется перед использованием общей библиотеки) будет загружать **.so** библиотеки:

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="summary"></a>Сводка

В этой статье перечислены распространенные проблемы, связанные с привязками, Java и описаны способы их устранения.


## <a name="related-links"></a>Связанные ссылки

- [Проекты библиотеки](http://developer.android.com/tools/projects/index.html#LibraryProjects)
- [Работа с JNI](~/android/platform/java-integration/working-with-jni.md)
- [Включить вывод диагностики](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)
- [Xamarin для разработчиков решений Android](~/android/get-started/java-developers.md)
- [JD-GUI](http://jd.benow.ca/)
