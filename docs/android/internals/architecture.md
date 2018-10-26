---
title: Архитектура
ms.prod: xamarin
ms.assetid: 7DC22A08-808A-DC0C-B331-2794DD1F9229
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 219c6bb4cd5718c969ba83a55596ad7b0bab8baf
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121130"
---
# <a name="architecture"></a>Архитектура

В среде выполнения Mono запуска приложений Xamarin.Android.
Этот выполнения среды запусков side-by-side с виртуальной машиной Android среды выполнения (ГРАФИКА). Обе эти среды выполнения выполняются на основе ядра Linux и предоставлять различные интерфейсы API для пользовательского кода, который позволяет разработчикам получать доступ к базовой системы. Среда выполнения Mono записывается на языке C.

Вы можете использовать [системы](xref:System), [System.IO](xref:System.IO), [System.Net](xref:System.Net) и остальная часть .NET классов библиотеки для доступа к базовой средства операционной системы Linux.

В Android большинство функций системы, такие как аудио-, графики, OpenGL и телефонии недоступны напрямую для собственных приложений, они доступны только через Android API среды выполнения Java, находящихся в одном из [Java](https://developer.xamarin.com/api/namespace/Java.Lang/). * пространства имен или [Android](https://developer.xamarin.com/api/namespace/Android/). * пространства имен. Архитектура является примерно следующим образом:

[![Схема Mono и ИСКУССТВО выше ядра и ниже .NET и Java + привязки](architecture-images/architecture1.png)](architecture-images/architecture1.png#lightbox)

Xamarin.Android разработчики получают доступ к функциям операционной системы, либо вызов интерфейсов API .NET, они знают (для низкого уровня доступа), либо с помощью классов в пространства имен Android, который предоставляет доступ к которым предоставляет API-интерфейсы Java моста среды выполнения Android.

Дополнительные сведения о взаимодействие классов Android с помощью классов среды выполнения Android см. в разделе [по проектированию API](~/android/internals/api-design.md) документа.


## <a name="application-packages"></a>Пакеты приложений

Пакетов приложений для Android — это контейнеры ZIP с *.apk* расширение файла. Пакеты приложения Xamarin.Android имеют одну и ту же структуру и макет как обычный Android пакеты, со следующими дополнениями:

-   (Содержащий IL) сборок приложения *хранятся* без сжатия в *сборки* папки. Во время процесса построения запуска в выпуске *.apk* — *mmap()* ed в процесс и сборки загружаются из памяти. Это позволяет более быстрый запуск приложений, как сборки не обязательно должно быть извлечено перед выполнением. - *Примечание:* сведения о расположении сборки, такие как [Assembly.Location](xref:System.Reflection.Assembly.Location) и [Assembly.CodeBase](xref:System.Reflection.Assembly.CodeBase)
    *нельзя рассчитывать* в выпуске выполняет построение. Они не существуют как операции distinct файловой системы и имеют расположение не может использоваться.


-   Собственные библиотеки, содержащий среду выполнения Mono присутствуют в *.apk* . Приложение Xamarin.Android должно содержать собственные библиотеки для требуемого целевые Android архитектур, например *armeabi* , *armeabi-v7a* , *x86* . Выполнение приложений Xamarin.Android на платформе невозможно, если оно не содержит необходимой среды выполнения библиотек.


Приложения Xamarin.Android также содержат *Android вызываемых оболочек* позволяет Android для вызова управляемого кода.



## <a name="android-callable-wrappers"></a>Вызываемые оболочки времени Android

- **Вызываемые оболочки времени Android** являются [JNI](http://en.wikipedia.org/wiki/Java_Native_Interface) моста, которые используются в любое время, в среде выполнения Android необходимо вызывать управляемый код. Android вызываемых оболочек, как виртуальные методы можно переопределить и Java-интерфейсов может быть реализован. См. в разделе [обзора интеграции Java](~/android/platform/java-integration/index.md) документа для получения дополнительных сведений.


<a name="Managed_Callable_Wrappers" />

## <a name="managed-callable-wrappers"></a>Управляемые вызываемых оболочек

Управляемые вызываемых оболочек являются моста JNI, которые используются в любое время, управляемый код должен вызывать код для Android и обеспечивают поддержку для переопределения виртуальных методов и реализация интерфейсов Java. Всего [Android](https://developer.xamarin.com/api/namespace/Android/). * и связанные пространства имен являются управляемых вызываемых оболочек, сформированный с помощью [.jar привязки](~/android/platform/binding-java-library/index.md).
Управляемые вызываемых оболочек несут ответственность за преобразование между типами управляемых и Android и вызов методов базовой платформы Android через JNI.

Каждый создан управляемый вызываемая оболочка содержит глобальные ссылки Java, который доступен через [Android.Runtime.IJavaObject.Handle](https://developer.xamarin.com/api/property/Android.Runtime.IJavaObject.Handle/) свойство. Глобальные ссылки используются для предоставления сопоставление между экземплярами Java и управляемых экземпляров. Глобальные ссылки — это ограниченный ресурс: эмуляторы допускаются только 2000 глобальные ссылки существовать одновременно, то время как большинство оборудования позволяет более 52,000 глобальные ссылки на одновременно существовать.

Для отслеживания, когда глобальные ссылки создаются и удаляются, можно задать [debug.mono.log](~/android/troubleshooting/index.md) системное свойство, чтобы содержать [gref, установленное](~/android/troubleshooting/index.md).

Глобальные ссылки можно явно освободить путем вызова [Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/) на управляемых вызываемой оболочки. Это удалит сопоставление экземпляра Java и управляемый экземпляр и разрешение экземпляру службы Java для сбора. Если экземпляр Java повторный доступ из управляемого кода, для него создается новый управляемый вызываемую оболочку.

CARE должны быть реализованы при освобождение управляемых вызываемых оболочек, если экземпляр случайно могут передаваться между потоками, как удаление экземпляра повлияет на ссылки из другим потокам. Для максимальной безопасности только `Dispose()` экземпляров, которые были выделены с помощью `new` *или* из методов какие вы *знать* всегда выделить новые экземпляры и не кэшированные экземпляры, которые могут вызвать случайного экземпляра, совместное использование между потоками.



## <a name="managed-callable-wrapper-subclasses"></a>Управляемых подклассов вызываемой оболочки

Подклассы управляемых вызываемой оболочки являются проживания «интересный» логика конкретного приложения может. К ним относятся пользовательские [Android.App.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/) подклассы (такие как [Activity1](https://github.com/xamarin/monodroid-samples/blob/master/HelloM4A/Activity1.cs#L13) типа в шаблоне проекта по умолчанию). (В частности, это любой *Java.Lang.Object* подклассов, которые выполняют *не* содержат [RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/) настраиваемый атрибут или [ RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/) — *false*, который используется по умолчанию.)

Как управлять вызываемых оболочек, управляемых подклассов вызываемая оболочка также содержать глобальные ссылки, доступные через [Java.Lang.Object.Handle](https://developer.xamarin.com/api/property/Java.Lang.Object.Handle/) свойство. Так же, как с помощью управляемых вызываемых оболочек, глобальные ссылки можно явно освободить путем вызова [Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/).
В отличие от управляемого вызываемых оболочек *внимательно следить за возникновением* должна применяться перед удалением из таких экземпляров, как *Dispose()*- ing экземпляра приведет к разрыву сопоставление между экземплярами Java (экземпляр Android вызываемую оболочку) и управляемый экземпляр.


### <a name="java-activation"></a>Активация Java

Когда [Android вызываемая оболочка](~/android/platform/java-integration/android-callable-wrappers.md) (ACW) создается на основе Java, конструктор ACW вызовет соответствующий конструктор C# для вызова. Например, ACW для *MainActivity* будет содержать конструктор по умолчанию, который будет вызывать *MainActivity*в конструктор по умолчанию. (Это осуществляется посредством *TypeManager.Activate()* вызова конструкторов ACW.)

Имеется одна сигнатура конструктора последствий: *(IntPtr, JniHandleOwnership)* конструктор. *(IntPtr, JniHandleOwnership)* конструктор вызывается всякий раз, когда объект Java предоставляются управляемому коду и вызываемую оболочку управляемого должно быть создано для управления дескриптор JNI. Обычно это делается в автоматически.

Существует два сценария, в котором *(IntPtr, JniHandleOwnership)* конструктор должен быть предоставлен вручную в подкласс управляемых вызываемой оболочки:

1. [Android.App.Application](https://developer.xamarin.com/api/type/Android.App.Application/) является подклассом. *Приложение* является специальной; значение по умолчанию *приложения* конструктор будет *никогда не* вызываться и [(IntPtr, JniHandleOwnership) должен предоставлять конструктор ](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/SanityTests/Hello.cs#L105).

2. Вызов виртуального метода из конструктора базового класса.


Обратите внимание, что (2) — это абстракция, обнаружена ошибка определения. В Java, как в C# вызовы виртуальных методов из конструктора всегда вызывать наиболее производный метод реализации. Например [TextView (контекст, AttributeSet, int) конструктор](https://developer.xamarin.com/api/constructor/Android.Widget.TextView.TextView/p/Android.Content.Context/Android.Util.IAttributeSet/System.Int32/) вызывает виртуальный метод [TextView.getDefaultMovementMethod()](http://developer.android.com/reference/android/widget/TextView.html#getDefaultMovementMethod()), который привязан как [ Свойство TextView.DefaultMovementMethod](https://developer.xamarin.com/api/property/Android.Widget.TextView.DefaultMovementMethod/).
Таким образом Если задан тип [LogTextBox](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs) были (1) [подкласс TextView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L26), (2) [переопределить TextView.DefaultMovementMethod](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L45)и (3) [активировать экземпляр этого объекта класс в формате XML,](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Resources/layout/log_text_box_1.xml#L29) переопределенный *DefaultMovementMethod* свойство будет вызываться до того, как конструктор ACW имели возможность выполнения будет предшествовать C# конструктор имели возможность выполнение.

Это поддерживается путем создания его экземпляра LogTextBox через [LogTextView (IntPtr, JniHandleOwnership)](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L28) конструктор, когда экземпляр ACW LogTextBox сначала переходит в управляемый код и последующим вызовом [ LogTextBox (контекст, IAttributeSet, int)](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L41) конструктор *в одном экземпляре* при выполнении ACW конструктор.

Порядок событий:

1.  Макет XML загружается в [ContentView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox1.cs#L41).

2.  Создает граф объекта макета Android и создает экземпляр *monodroid.apidemo.LogTextBox* , ACW для *LogTextBox* .

3.  *Monodroid.apidemo.LogTextBox* конструктор выполняет [android.widget.TextView](http://developer.android.com/reference/android/widget/TextView.html#TextView%28android.content.Context,%20android.util.AttributeSet%29) конструктор.

4.  *TextView* конструктор вызывает *monodroid.apidemo.LogTextBox.getDefaultMovementMethod()* .

5.  *monodroid.apidemo.LogTextBox.getDefaultMovementMethod()* вызывает *LogTextBox.n_getDefaultMovementMethod()* , который вызывает *TextView.n_GetDefaultMovementMethod()* , что вызывает [Java.Lang.Object.GetObject&lt;TextView&gt; (обрабатывать, JniHandleOwnership.DoNotTransfer)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) .

6.  *Java.Lang.Object.GetObject&lt;TextView&gt;()* проверяет, существует ли уже соответствующих C# для экземпляра *обрабатывать* . Если имеется, он возвращается. В этом случае отсутствует, поэтому *Object.GetObject&lt;T&gt;()* необходимо создать его.

7.  *Object.GetObject&lt;T&gt;()* ищет *LogTextBox (IntPtr, JniHandleOwneship)* конструктор, он вызывается, создает сопоставление между *обрабатывать* и созданный экземпляр и возвращает созданный экземпляр.

8.  *TextView.n_GetDefaultMovementMethod()* вызывает *LogTextBox.DefaultMovementMethod* метода считывания свойства.

9.  Элемент управления возвращается к *android.widget.TextView* конструктор, который завершает выполнение.

10. *Monodroid.apidemo.LogTextBox* конструктор выполняет вызов *TypeManager.Activate()* .

11. *LogTextBox (контекст, IAttributeSet, int)* конструктор выполняет *на том же экземпляре, созданные в (7)* .

12. Если (IntPtr, JniHandleOwnership) не удалось найти конструктор, то будет создано System.MissingMethodException](xref:System.MissingMethodException).

<a name="Premature_Dispose_Calls" />

### <a name="premature-dispose-calls"></a>Вызывает преждевременное Dispose()

Отсутствует сопоставление дескриптор JNI и соответствующий экземпляр C#. Java.Lang.Object.Dispose() нарушает это сопоставление. Если дескриптор JNI входит в управляемый код после сопоставление было разорвано, он будет выглядеть активации Java и *(IntPtr, JniHandleOwnership)* будет установлен для и вызывается конструктор. Если конструктор не существует, будет создано исключение.

Рассмотрим следующий подкласс управляемых вызываемой Wraper:

```csharp
class ManagedValue : Java.Lang.Object {

    public string Value {get; private set;}

    public ManagedValue (string value)
    {
        Value = value;
    }

    public override string ToString ()
    {
        return string.Format ("[Managed: Value={0}]", Value);
    }
}
```

Если мы создаем экземпляр Dispose() и вызвать управляемый вызываемая оболочка повторного создания:

```csharp
var list = new JavaList<IJavaObject>();
list.Add (new ManagedValue ("value"));
list [0].Dispose ();
Console.WriteLine (list [0].ToString ());
```

Программа будет die:

```shell
E/mono    ( 2906): Unhandled Exception: System.NotSupportedException: Unable to activate instance of type Scratch.PrematureDispose.ManagedValue from native handle 4051c8c8 --->
System.MissingMethodException: No constructor found for Scratch.PrematureDispose.ManagedValue::.ctor(System.IntPtr, Android.Runtime.JniHandleOwnership)
E/mono    ( 2906):   at Java.Interop.TypeManager.CreateProxy (System.Type type, IntPtr handle, JniHandleOwnership transfer) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   at Java.Interop.TypeManager.CreateInstance (IntPtr handle, JniHandleOwnership transfer, System.Type targetType) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   --- End of inner exception stack trace ---
E/mono    ( 2906):   at Java.Interop.TypeManager.CreateInstance (IntPtr handle, JniHandleOwnership transfer, System.Type targetType) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   at Java.Lang.Object.GetObject (IntPtr handle, JniHandleOwnership transfer, System.Type type) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   at Java.Lang.Object._GetObject[IJavaObject] (IntPtr handle, JniHandleOwnership transfer) [0x00000
```

Если содержит подкласс *(IntPtr, JniHandleOwnership)* конструктор, а затем *новый* будет создан экземпляр типа. Таким образом экземпляр будет «допустима потеря» все данные экземпляра, так как это новый экземпляр. (Обратите внимание, что значение равно null).

```shell
I/mono-stdout( 2993): [Managed: Value=]
```

Только *Dispose()* из управляемых подклассов вызываемой оболочки, если известно, что больше не будет использоваться объект Java или подкласса не содержит экземпляр данных и *(IntPtr, JniHandleOwnership)* предоставляется конструктор.



## <a name="application-startup"></a>Запуск приложения

Когда действие, служба, и т.д. запускается, Android сначала проверяет, чтобы увидеть, существует ли уже процессом, выполняемым для размещения действие/service/etc. Если процесс не существует, то будет создан новый процесс, [AndroidManifest.xml](http://developer.android.com/guide/topics/manifest/manifest-intro.html) считывается и типа, указанного в [ /manifest/application/@android:name ](http://developer.android.com/guide/topics/manifest/application-element.html#nm) загружается и создании экземпляра атрибута. Далее, все типы, заданные свойством [ /manifest/application/provider/@android:name ](http://developer.android.com/guide/topics/manifest/provider-element.html#nm) значения атрибута создаются экземпляры и их [ContentProvider.attachInfo%28)](https://developer.xamarin.com/api/member/Android.Content.ContentProvider.AttachInfo/p/Android.Content.Context/Android.Content.PM.ProviderInfo/) вызванного метода. Xamarin.Android использует это, добавив *mono. MonoRuntimeProvider* *ContentProvider* в AndroidManifest.xml в процессе сборки. *Mono. MonoRuntimeProvider.attachInfo()* метод отвечает за загрузку среды выполнения Mono в процесс.
Любые попытки использовать Mono и до этого момента не удастся. ( *Примечание*: именно типов, являющихся подклассами [Android.App.Application](https://developer.xamarin.com/api/type/Android.App.Application/) необходимо предоставить [(IntPtr, JniHandleOwnership) конструктор](https://github.com/xamarin/monodroid-samples/blob/a9e8ef23/SanityTests/Hello.cs#L103), как экземпляр приложения находится в создается перед инициализацией Mono.)

После завершения процесса инициализации `AndroidManifest.xml` , чтобы найти имя класса действия и службы и т.д. для запуска. Например [ /manifest/application/activity/@android:name атрибут](http://developer.android.com/guide/topics/manifest/activity-element.html#nm) используется для определения имени действия для загрузки. Для действий, этот тип должен наследоваться [android.app.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/).
Указанный тип загружается через [Class.forName()](http://developer.android.com/reference/java/lang/Class.html#forName(java.lang.String)) (которого требует, чтобы тип был Java введите, поэтому Android вызываемых оболочек), затем создать экземпляр. Создание экземпляра Android вызываемая оболочка активирует создание экземпляра соответствующего типа C#. Android, затем вызовет [Activity.onCreate(Bundle)](http://developer.android.com/reference/android/app/Activity.html#onCreate(android.os.Bundle)) , что вызовет соответствующий [Activity.OnCreate(Bundle)](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) должен быть вызван, и Готово состояния гонки.
