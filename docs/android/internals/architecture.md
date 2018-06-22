---
title: Архитектура
ms.prod: xamarin
ms.assetid: 7DC22A08-808A-DC0C-B331-2794DD1F9229
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/25/2018
ms.openlocfilehash: 9ce1d790f5dea00ac47d5639ae8424793006445a
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/27/2018
ms.locfileid: "32019299"
---
# <a name="architecture"></a>Архитектура

Приложения Xamarin.Android, выполняются в среде Mono.
Это выполнения среды запусков-параллельных с виртуальной машиной Android среды выполнения (ГРАФИКА). Обе эти среды выполнения, работающие поверх ядро Linux и предоставляют различные интерфейсы API в пользовательский код, позволяющий разработчикам доступ к базовой системы. Среда выполнения моно написан на языке C.

Вы можете использовать [системы](http://msdn.microsoft.com/en-us/library/system.aspx), [System.IO](http://msdn.microsoft.com/en-us/library/system.io.aspx), [System.Net](http://msdn.microsoft.com/en-us/library/system.net.aspx) и остальная часть .NET класса библиотеки для доступа к базовой средств операционной системы Linux.

В Android большинство средств системы как для звука, графики, OpenGL и телефонии недоступны напрямую для собственных приложений, они становятся доступными через Android API среды выполнения Java, находящихся в одном из [Java](https://developer.xamarin.com/api/namespace/Java.Lang/). * пространства имен или [Android](https://developer.xamarin.com/api/namespace/Android/). * пространства имен. Архитектура будет примерно следующим образом:

[![Схема моно и ИЛЛЮСТРАЦИИ выше ядра и ниже .NET/Java + привязок](architecture-images/architecture1.png)](architecture-images/architecture1.png#lightbox)

Xamarin.Android разработчики получают доступ к функциям в операционной системе по вызову API .NET, которые знают (для нижнего уровня доступа) или с помощью классов, предоставляемых в Android пространства имен, которые предоставляет мост для Java API-интерфейсы, предоставляемые Среда выполнения Android.

Дополнительные сведения о взаимодействие классов Android с помощью классов среды выполнения Android. в разделе [структура API](~/android/internals/api-design.md) документа.


## <a name="application-packages"></a>Пакеты приложений

Пакеты Android приложений являются контейнерами ZIP с *.apk* расширение имени файла. Пакеты приложения Xamarin.Android имеют ту же структуру и макет, что обычный Android пакетов с учетом следующих отличий:

-   Сборок приложения (содержащий IL) *хранимых* несжатые внутри *сборки* папки. Во время процесса построения при запуске в версии *.apk* — *mmap()* ed в процесс и сборки загружаются из памяти. Это обеспечивает более быстрый запуск приложений, как сборки не обязательно должно быть извлечено перед выполнением. - *Примечание:* сведения о расположении сборки, такие как [Assembly.Location](https://developer.xamarin.com/api/property/System.Reflection.Assembly.Location/) и [Assembly.CodeBase](https://developer.xamarin.com/api/property/System.Reflection.Assembly.CodeBase/)
    *нельзя полагаться* в выпуске выполняет построение. Они не существуют как операции distinct файловой системы и имеют местоположение не может использоваться.


-   Собственные библиотеки, содержащей моно выполнения присутствуют в *.apk* . Приложение Xamarin.Android должен содержать собственные библиотеки для Android архитектур требуемого целевые, например *armeabi* , *armeabi v7a* , *x86* . Выполнение приложений Xamarin.Android на платформе невозможно, если оно не содержит необходимой среды выполнения библиотек.


Xamarin.Android приложения также содержат *Android с помощью вызываемых оболочек* разрешающее Android для вызова управляемого кода.



## <a name="android-callable-wrappers"></a>Android с помощью вызываемых оболочек

- **Android с помощью вызываемых оболочек** , [JNI](http://en.wikipedia.org/wiki/Java_Native_Interface) моста, которые используются в любое время Android среда выполнения должна вызывать управляемый код. Android с помощью вызываемых оболочек, как виртуальные методы можно переопределить и Java-интерфейсов может быть реализован. В разделе [интеграции Java](~/android/platform/java-integration/index.md) документа для получения дополнительных сведений.


<a name="Managed_Callable_Wrappers" />

## <a name="managed-callable-wrappers"></a>Управляемые вызываемой оболочки

Управляемый вызываемых оболочек являются моста JNI, которые используются в любое время, управляемый код должен вызывать код для Android и обеспечивают поддержку переопределения виртуальных методов и реализации интерфейсов Java. Всего [Android](https://developer.xamarin.com/api/namespace/Android/). * и связанных пространств имен, сформированного с помощью управляемых вызываемых оболочек [.jar привязки](~/android/platform/binding-java-library/index.md).
Управляемый вызываемых оболочек несут ответственность за преобразование между типами управляемых и Android и вызов методов базовой платформы Android через JNI.

Каждый создан управляемый вызываемая оболочка содержит глобальной ссылки Java, который доступен через [Android.Runtime.IJavaObject.Handle](https://developer.xamarin.com/api/property/Android.Runtime.IJavaObject.Handle/) свойство. Глобальные ссылки используются для сопоставления между экземплярами Java и управляемых экземпляров. Глобальные ссылки являются ограниченным ресурсом: эмуляторы разрешает только 2000 глобальной ссылки на существуют одновременно, хотя большинство оборудования позволяет более 52,000 глобальной ссылки на существуют одновременно.

Для отслеживания при создании и удалении ссылок на глобальный, можно задать [debug.mono.log](~/android/troubleshooting/index.md) системное свойство, чтобы содержать [gref](~/android/troubleshooting/index.md).

Глобальные ссылки можно явно освободить путем вызова [Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/) в управляемом вызываемой оболочке. Будет удалено сопоставление между экземплярами Java и управляемом экземпляре и разрешить экземпляру Java для сбора. Если повторно доступ к экземпляру Java из управляемого кода, для него создается новый управляемый вызываемую оболочку.

Следить за должны быть реализованы при избавляться от управляемых с помощью вызываемых оболочек, если экземпляр случайно могут передаваться между потоками, как удаление экземпляра повлияет ссылки из каких-либо других потоков. Для максимальной безопасности только `Dispose()` экземпляров, которые были выделены через `new` *или* из методов которую *знать* всегда выделения новых экземпляров и не кэшируемых экземпляров, которые могут заставить случайного экземпляр, для управления доступом между потоками.



## <a name="managed-callable-wrapper-subclasses"></a>Управляемые подклассов вызываемой оболочки

Подклассы управляемого вызываемой оболочки, где «Город» логикой конкретных приложений может находиться. К ним относятся пользовательские [Android.App.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/) подклассы (такие как [Activity1](https://github.com/xamarin/monodroid-samples/blob/master/HelloM4A/Activity1.cs#L13) типа в шаблоне проекта по умолчанию). (В частности, это любой *Java.Lang.Object* подклассов, которые выполняют *не* содержат [RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/) настраиваемого атрибута или [ RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/) — *false*, используемого по умолчанию.)

Как управлять вызываемых оболочек, управляемых подклассов вызываемой оболочки также содержать глобальной ссылки, доступные через [Java.Lang.Object.Handle](https://developer.xamarin.com/api/property/Java.Lang.Object.Handle/) свойство. Так же, как с помощью управляемого вызываемых оболочек глобальной ссылки можно явно освободить путем вызова [Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/).
В отличие от управляемого вызываемых оболочек *следить за* должно выполняться перед удалением из таких экземпляров как *Dispose()* ing экземпляра нарушит сопоставление между экземплярами Java (экземпляр Android вызываемую оболочку) и управляемый экземпляр.


### <a name="java-activation"></a>Активация Java

Когда [Android вызываемой оболочки](~/android/platform/java-integration/android-callable-wrappers.md) (ACW) создается на основе Java, конструктор ACW приводит к созданию соответствующего C# вызываемый конструктор. Например, ACW для *MainActivity* будет содержать конструктор по умолчанию, который будет вызывать *MainActivity*конструктор по умолчанию. (Это осуществляется посредством *TypeManager.Activate()* вызова конструкторов ACW.)

Имеется один сигнатуры конструктора последствий: *(IntPtr, JniHandleOwnership)* конструктор. *(IntPtr, JniHandleOwnership)* конструктор вызывается в том случае, когда объект Java предоставляется в управляемый код и вызываемую оболочку управляемого должно быть создано для управления JNI дескриптор. Это обычно выполняется автоматически.

Существует два сценария, в котором *(IntPtr, JniHandleOwnership)* конструктор должен быть предоставлен вручную в подкласс управляемых вызываемой оболочки:

1. [Android.App.Application](https://developer.xamarin.com/api/type/Android.App.Application/) является подклассом. *Приложение* является специальной; значение по умолчанию *приложение* конструктор будет *никогда не* вызываться и [(IntPtr, JniHandleOwnership) вместо этого необходимо указать конструктор ](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/SanityTests/Hello.cs#L105).

2. Вызов виртуального метода из конструктора базового класса.


Обратите внимание, что (2) — обнаружена ошибка определения абстракция. На языке Java, как в C# вызовы виртуальных методов из конструктора всегда вызвать наибольшие производные реализации метода. Например [TextView (контекста AttributeSet, int) конструктор](https://developer.xamarin.com/api/constructor/Android.Widget.TextView.TextView/p/Android.Content.Context/Android.Util.IAttributeSet/System.Int32/) вызывает виртуальный метод [TextView.getDefaultMovementMethod()](http://developer.android.com/reference/android/widget/TextView.html#getDefaultMovementMethod()), который привязывается к [ Свойство TextView.DefaultMovementMethod](https://developer.xamarin.com/api/property/Android.Widget.TextView.DefaultMovementMethod/).
Таким образом Если тип [LogTextBox](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs) (1) были [подкласс TextView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L26), (2) [переопределить TextView.DefaultMovementMethod](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L45)и (3) [активировать экземпляр этого объекта класс с помощью XML,](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Resources/layout/log_text_box_1.xml#L29) переопределенный *DefaultMovementMethod* свойство будет вызываться перед конструктор ACW имели возможность выполнять, и она произойдет раньше, конструктор C# для выполнения.

Эта возможность поддерживается создание экземпляра LogTextBox через [LogTextView (IntPtr, JniHandleOwnership)](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L28) конструктор, когда впервые попадают в экземпляр ACW LogTextBox управляемый код и затем вызывает [ LogTextBox (контекста IAttributeSet, int)](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L41) конструктор *на том же экземпляре* при выполнении ACW конструктор.

Порядок событий:

1.  Макет XML загружается в [ContentView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox1.cs#L41).

2.  Android создает граф объекта макета и создает экземпляр *monodroid.apidemo.LogTextBox* , ACW для *LogTextBox* .

3.  *Monodroid.apidemo.LogTextBox* конструктор выполняет [android.widget.TextView](http://developer.android.com/reference/android/widget/TextView.html#TextView%28android.content.Context,%20android.util.AttributeSet%29) конструктор.

4.  *TextView* конструктор вызывает *monodroid.apidemo.LogTextBox.getDefaultMovementMethod()* .

5.  *monodroid.apidemo.LogTextBox.getDefaultMovementMethod()* вызывает *LogTextBox.n_getDefaultMovementMethod()* , которое вызывает *TextView.n_GetDefaultMovementMethod()* , что вызывает [Java.Lang.Object.GetObject&lt;TextView&gt; (обрабатывать, JniHandleOwnership.DoNotTransfer)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) .

6.  *Java.Lang.Object.GetObject&lt;TextView&gt;()* экземпляра проверяет, существует ли уже соответствующих C# для *обработки* . Если нет, то он возвращается. В этом сценарии не существует, поэтому *Object.GetObject&lt;T&gt;()* необходимо создать его.

7.  *Object.GetObject&lt;T&gt;()* ищет *LogTextBox (IntPtr, JniHandleOwneship)* конструктор, он вызывает, создает сопоставление между *обработки* и созданный экземпляр и возвращает созданный экземпляр.

8.  *TextView.n_GetDefaultMovementMethod()* вызывает *LogTextBox.DefaultMovementMethod* метод считывания свойства.

9.  Элемент управления возвращается к *android.widget.TextView* конструктор, который завершает выполнение.

10. *Monodroid.apidemo.LogTextBox* конструктор выполняет вызов *TypeManager.Activate()* .

11. *LogTextBox (контекста IAttributeSet, int)* конструктор выполняет *на том же экземпляре, созданные в (7)* .

12. Если (IntPtr, JniHandleOwnership) не удалось найти конструктор, затем System.MissingMethodException] (https://developer.xamarin.com/api/type/System.MissingMethodException/) будет создано.

<a name="Premature_Dispose_Calls" />

### <a name="premature-dispose-calls"></a>Вызывает преждевременное Dispose()

Отсутствует сопоставление дескриптор JNI и соответствующий экземпляр C#. Java.Lang.Object.Dispose() нарушает это сопоставление. Если дескриптор JNI входит в управляемый код после сопоставления было разорвано, он будет выглядеть активации Java и *(IntPtr, JniHandleOwnership)* будет установлен для и вызывается конструктор. Если конструктор не существует, будет вызвано исключение.

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

Если создать экземпляр Dispose() ее и вызвать управляемый вызываемой оболочки повторного создания:

```csharp
var list = new JavaList<IJavaObject>();
list.Add (new ManagedValue ("value"));
list [0].Dispose ();
Console.WriteLine (list [0].ToString ());
```

Программа будет умереть:

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

Если подкласса содержат *(IntPtr, JniHandleOwnership)* конструктор, а затем *новый* будет создан экземпляр типа. В результате экземпляра будет отображаться «допустима потеря» все данные экземпляра, поскольку это новый экземпляр. (Обратите внимание, что значение равно null).

```shell
I/mono-stdout( 2993): [Managed: Value=]
```

Только *Dispose()* из управляемых подклассов вызываемой оболочки, если известно, что больше не будет использоваться объект Java или подкласса не содержит экземпляр данных и *(IntPtr, JniHandleOwnership)* предоставляется конструктор.



## <a name="application-startup"></a>Запуск приложения

Когда действие, службы, т. д., Android сначала будет проверять для запускается существует ли уже процесс, выполняемый для размещения действия, службы и т. д. Если процесс не существует, то будет создан новый процесс, [AndroidManifest.xml](http://developer.android.com/guide/topics/manifest/manifest-intro.html) считывается и типу, указанному в [ /manifest/application/@android:name ](http://developer.android.com/guide/topics/manifest/application-element.html#nm) загружается и создавать экземпляры атрибута. Далее, все типы, определенные системой [ /manifest/application/provider/@android:name ](http://developer.android.com/guide/topics/manifest/provider-element.html#nm) значения атрибута создаются и их [ContentProvider.attachInfo%28)](https://developer.xamarin.com/api/member/Android.Content.ContentProvider.AttachInfo/p/Android.Content.Context/Android.Content.PM.ProviderInfo/) вызванного метода. Xamarin.Android обработчиков в это путем добавления *mono. MonoRuntimeProvider* *поставщик содержимого* для AndroidManifest.xml во время сборки. *Mono. MonoRuntimeProvider.attachInfo()* метод отвечает за загрузку Mono среды выполнения в процесс.
Любые попытки использовать моно до этого момента не удастся. ( *Примечание*: именно поэтому типы какие подкласс [Android.App.Application](https://developer.xamarin.com/api/type/Android.App.Application/) необходимо указать [(IntPtr, JniHandleOwnership) конструктор](https://github.com/xamarin/monodroid-samples/blob/a9e8ef23/SanityTests/Hello.cs#L103), как экземпляр приложения создать перед инициализацией Mono.)

После завершения процесса инициализации `AndroidManifest.xml` , чтобы найти имя класса действия и службы и т. д. для запуска. Например [ /manifest/application/activity/@android:name атрибута](http://developer.android.com/guide/topics/manifest/activity-element.html#nm) используется для определения имени действия для загрузки. Для действий, этот тип должен наследоваться [android.app.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/).
Указанный тип загружается с помощью [Class.forName()](http://developer.android.com/reference/java/lang/Class.html#forName(java.lang.String)) (которого требует, чтобы тип был Java тип, поэтому Android вызываемых оболочек), затем создаются экземпляры. Создания экземпляра Android вызываемой оболочкой запустит создания экземпляра соответствующего типа C#. Android будет затем вызывать [Activity.onCreate(Bundle)](http://developer.android.com/reference/android/app/Activity.html#onCreate(android.os.Bundle)) , которое может вызвать соответствующий [Activity.OnCreate(Bundle)](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) должен быть вызван, и выполняется отключение состояния гонки.
