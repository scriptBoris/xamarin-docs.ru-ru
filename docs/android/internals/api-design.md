---
title: Принципы разработки API Xamarin.Android
ms.prod: xamarin
ms.assetid: 3E52D815-D95D-5510-0D8F-77DAC7E62EDE
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 611046954e8ef359476d2bd12a69f04041d869f1
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32437182"
---
# <a name="xamarinandroid-api-design-principles"></a>Принципы разработки API Xamarin.Android


## <a name="overview"></a>Обзор

Помимо основных компонентов библиотеках базовых классов, которые являются частью моно Xamarin.Android поставляется с привязок для различных Android API-интерфейсов позволяет разработчикам создавать собственные приложения Android с моно.

В основной части Xamarin.Android существует представляет механизм взаимодействия, мир мосты C# с кодом Java и предоставляет разработчикам доступ к интерфейсам API Java от C# и других языков .NET.


## <a name="design-principles"></a>Принципы разработки

Ниже приведено несколько наши принципы проектирования для привязки Xamarin.Android

-  Соответствует [рекомендации по разработке .NET Framework](http://msdn.microsoft.com/en-us/library/ms229042.aspx).

-  Позволяют разработчикам подкласс классов Java.

-  Подкласс должны работать с стандартных конструкций C#.

-  Являются производными от существующего класса.

-  Вызовите конструктор базового класса в цепочке.

-  Переопределение методов должны выполняться с системой переопределение в C#.

-  Делают возможным типичные задачи Java просто и жесткие задачи Java.

-  Предоставляют свойства вируальная машина Java в виде свойств C#.

-  Предоставьте строго типизированные API:

    - Повышения безопасности типов.

    - Свести к минимуму ошибки времени выполнения.

    - Получите доступ к intellisense интегрированной среды разработки для типов возвращаемых значений.

    - Позволяет документации всплывающее окно интегрированной среды разработки.

-  Рекомендуем исследования API-интерфейсы в интегрированной среде разработки:

    - Используйте альтернативные Framework для минимизации Java для раскрытия.

    - Предоставлять делегаты C# (лямбда-выражения, анонимные методы и System.Delegate) вместо интерфейсов одним методом, когда подходящие.

    - Предоставляет механизм для вызова произвольных библиотеки Java ( [Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/)).


## <a name="assemblies"></a>Сборки

Xamarin.Android включает несколько сборок, составляющих *MonoMobile профиль*. [Сборки](~/cross-platform/internals/available-assemblies.md) страница содержит дополнительные сведения.

Привязки для платформы Android содержатся в `Mono.Android.dll` сборки. Эта сборка содержит всей привязки для значительного Android API-интерфейсов и обмена данными с Android среды выполнения виртуальной Машины.


## <a name="binding-design"></a>Привязка разработки


### <a name="collections"></a>Коллекции

Android API-интерфейсов используйте java.util коллекций широко предоставляют списки, наборы и карты. Мы предоставляем эти элементы с помощью [System.Collections.Generic](https://developer.xamarin.com/api/namespace/System.Collections.Generic/) интерфейсов в нашей привязкой. Приведены основные сопоставления.

-   [java.util.Set<E> ](http://developer.android.com/reference/java/util/Set.html) сопоставляется с типом системы [ICollection<T>](http://msdn.microsoft.com/en-us/library/92t2ye13.aspx), вспомогательный класс [Android.Runtime.JavaSet<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaSet%601/).

-   [java.util.List<E> ](http://developer.android.com/reference/java/util/List.html) сопоставляется с типом системы [IList<T>](http://msdn.microsoft.com/en-us/library/5y536ey6.aspx), вспомогательный класс [Android.Runtime.JavaList<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaList%601/).

-   [java.util.Map < K, V >](http://developer.android.com/reference/java/util/Map.html) сопоставляется с типом системы [IDictionary < TKey, TValue >](http://msdn.microsoft.com/en-us/library/s4ys34ea.aspx), вспомогательный класс [Android.Runtime.JavaDictionary < K, V >](https://developer.xamarin.com/api/type/Android.Runtime.JavaDictionary%602/).

-   [java.util.Collection<E> ](http://developer.android.com/reference/java/util/Collection.html) сопоставляется с типом системы [ICollection<T>](http://msdn.microsoft.com/en-us/library/92t2ye13.aspx), вспомогательный класс [Android.Runtime.JavaCollection<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaCollection%601/).

Мы предоставили вспомогательные классы для упрощения быстрее copyless маршалинга этих типов. Если это возможно, мы рекомендуем использовать эти предоставляемые коллекции вместо реализации платформы, такие как [ `List<T>` ](https://developer.xamarin.com/api/type/System.Collections.Generic.List%601/) или [ `Dictionary<TKey, TValue>` ](https://developer.xamarin.com/api/type/System.Collections.Generic.Dictionary%602/). [Android.Runtime](https://developer.xamarin.com/api/namespace/Android.Runtime/) реализации внутренним образом используют собственную коллекцию Java и поэтому не требуют передачи между собственную коллекцию при передаче элемента Android API.

Можно передать любой реализации интерфейса Android метод, принимающий интерфейса, например передать `List<int>` для [ArrayAdapter&lt;int&gt;(контекста, int, IList&lt;int&gt;)](https://developer.xamarin.com/api/constructor/Android.Widget.ArrayAdapter%3CT%3E.ArrayAdapter%3CT%3E/p/Android.Content.Context/System.Int32/System.Collections.Generic.IList%7BT%7D/)конструктор. *Однако*, для всех реализаций *за исключением* для реализаций Android.Runtime, это подразумевает *копирование* список из виртуальной Машины Mono в Android выполнения виртуальной Машины. Если список является более поздней версии, измененных в течение Android среды выполнения (например, путем вызова [ArrayAdapter&lt;T&gt;. Add(T)](https://developer.xamarin.com/api/member/Android.Widget.ArrayAdapter%3CT%3E.Add/p/T/) метод), эти изменения *не будет* быть видимым в управляемом коде. Если `JavaList<int>` были используется, эти изменения будет видима.

Rephrased, реализации, интерфейсов коллекций *не* одним из перечисленных выше в списке **вспомогательный класс**es только маршалировать [In]:

```csharp
// This fails:
var badSource  = new List<int> { 1, 2, 3 };
var badAdapter = new ArrayAdapter<int>(context, textViewResourceId, badSource);
badAdapter.Add (4);
if (badSource.Count != 4) // true
    throw new InvalidOperationException ("this is thrown");

// this works:
var goodSource  = new JavaList<int> { 1, 2, 3 };
var goodAdapter = new ArrayAdapter<int> (context, textViewResourceId, goodSource);
goodAdapter.Add (4);
if (goodSource.Count != 4) // false
    throw new InvalidOperationException ("should not be reached.");
```


### <a name="properties"></a>Свойства

Методы Java преобразуются в свойства, когда это необходимо:

-  Пары методов Java `T getFoo()` и `void setFoo(T)` преобразуются в `Foo` свойство. Пример: [Activity.Intent](https://developer.xamarin.com/api/property/Android.App.Activity.Intent/).

-  Метод Java `getFoo()` преобразуется в Foo свойство только для чтения. Пример: [Context.PackageName](https://developer.xamarin.com/api/property/Android.Content.Context.PackageName/).

-  Свойства, доступные только для набора не создаются.

-  Свойства являются *не* возникает, если тип свойства должен быть массивом.



### <a name="events-and-listeners"></a>События и прослушиватели

Android API-интерфейсы построены на основе Java и его компоненты используют шаблон Java для привязки прослушивателей событий. Этот шаблон в большей степени становится громоздким, как пользователь создать анонимный класс и объявить переопределяемые методы, например, это, как выполняются действия в Android с помощью Java:

```csharp
final android.widget.Button button = new android.widget.Button(context);

button.setText(this.count + " clicks!");
button.setOnClickListener (new View.OnClickListener() {
    public void onClick (View v) {
        button.setText(++this.count + " clicks!");
    }
});
```

Эквивалентный код на языке C# с помощью событий будет:

```csharp
var button = new Android.Widget.Button (context) {
    Text = string.Format ("{0} clicks!", this.count),
};
button.Click += (sender, e) => {
    button.Text = string.Format ("{0} clicks!", ++this.count);
};
```

Обратите внимание, что оба приведенных выше механизмов доступны с Xamarin.Android. Можно реализовать интерфейс прослушивателя и присоединить ее с View.SetOnClickListener или вложении делегата, созданные с помощью любой из обычные парадигм C# для события щелчка кнопкой мыши.

Если метод обратного вызова прослушиватель имеет тип возврата void, мы создаем элементы API на основе [EventHandler&lt;TEventArgs&gt; ](https://developer.xamarin.com/api/type/System.EventHandler%601/) делегата. Сначала создается событие, как показано в примере выше для этих типов прослушивателя. Тем не менее если обратный вызов прослушиватель возвращает отличным от void и не- **логическое** значение "," события "и" EventHandlers не используются. Вместо этого создать определенный делегат для сигнатуры функции обратного вызова и добавить свойства, а не события. Причина заключается в необходимости обрабатывать порядок вызова делегата и возвращать обработки. Этот подход отражает сходен с помощью Xamarin.iOS API.

Свойства или события C# автоматически только регистрируется, если метод Android регистрации событий:

1. Имеет `set` префикса, например [ *задать*OnClickListener](https://developer.xamarin.com/api/member/Android.Views.View.SetOnClickListener/).

1. Имеет `void` тип возвращаемого значения.

1. Принимает только один параметр, тип параметра — это интерфейс, этот интерфейс содержит только один метод и имя интерфейса заканчивается `Listener` , например [View.OnClick *прослушивателя*](https://developer.xamarin.com/api/type/Android.Views.View+IOnClickListener/).


Кроме того, если метод интерфейса прослушиватель имеет тип возвращаемого значения **логическое** вместо **void**, созданный *EventArgs* подкласс будет содержать *Handled* свойство. Значение *Handled* свойство используется в качестве возвращаемых значений для *прослушивателя* метод, который по умолчанию используется `true`.

Например, Android [View.setOnKeyListener()](https://developer.xamarin.com/api/member/Android.Views.View.SetOnKeyListener/p/Android.Views.View+IOnKeyListener/) метод принимает [View.OnKeyListener](https://developer.xamarin.com/api/type/Android.Views.View+IOnKeyListener) интерфейс и [View.OnKeyListener.onKey (представления, int, KeyEvent)](https://developer.xamarin.com/api/member/Android.Views.View+IOnKeyListener.OnKey/p/Android.Views.View/Android.Views.Keycode/Android.Views.KeyEvent/) метод имеет логический тип возврата. Xamarin.Android формирует соответствующий [View.KeyPress](https://developer.xamarin.com/api/event/Android.Views.View.KeyPress/) событие, которое является [EventHandler&lt;View.KeyEventArgs&gt;](https://developer.xamarin.com/api/type/Android.Views.View+KeyEventArgs/).
*KeyEventArgs* класс, в свою очередь имеет [View.KeyEventArgs.Handled](https://developer.xamarin.com/api/property/Android.Views.View+KeyEventArgs.Handled/) свойства, которое используется в качестве возвращаемых значений для *View.OnKeyListener.onKey()* метод.

Мы собираемся добавить перегрузки для других методов и упаковывать для предоставления подключения на основе делегата. Кроме того прослушиватели с нескольких обратных вызовов требуются некоторые дополнительные проверки, чтобы определить, если реализация отдельных обратных вызовов целесообразно, так как они определяются эти выполняется преобразование. Если нет соответствующего события, прослушиватели должны использоваться в C#, но переведите любой, которая может быть использование делегата с поступившими. Мы также были выполнены некоторые преобразования интерфейсы без суффикса «Прослушиватель», когда стало ясно, что они получают преимущества от альтернативой делегата.

Все интерфейсы прослушиватели реализуют [ `Android.Runtime.IJavaObject` ](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/) интерфейс, из-за особенностей реализации привязки, поэтому классы прослушиватель должен реализовывать этот интерфейс. Это можно сделать путем реализации интерфейса прослушивателя на подкласс [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) или любой другой объект Java, например, Android действия в оболочку.


### <a name="runnables"></a>Runnables

Использует Java [java.lang.Runnable](https://developer.xamarin.com/api/type/Java.Lang.Runnable/) интерфейс предоставлять механизм делегирования. [Java.lang.Thread](https://developer.xamarin.com/api/type/Java.Lang.Thread/) класс является важным потребителя этого интерфейса. Android принят на работу с интерфейсом API также.
[Activity.runOnUiThread()](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/p/Java.Lang.IRunnable/) и [View.post()](https://developer.xamarin.com/api/member/Android.Views.View.Post/p/Java.Lang.IRunnable) являются примерами важных.

`Runnable` Интерфейс содержит один метод значение void, [run()](https://developer.xamarin.com/api/member/Java.Lang.Runnable.Run%28%29/). Он пригоден для привязки в C# в форме таким образом [System.Action](http://msdn.microsoft.com/en-us/library/system.action.aspx) делегата. Мы предоставили в привязке перегрузок, принимающих `Action` параметр для всех элементов API, которые занимают `Runnable` в собственном API-Интерфейсе, например [Activity.RunOnUiThread()](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/(System.Action)) и [View.Post()](https://developer.xamarin.com/api/member/Android.Views.View.Post/(System.Action)).

Мы оставили [интерфейс IRunnable](https://developer.xamarin.com/api/type/Java.Lang.IRunnable/) перегрузки в месте, а не их замены, поскольку несколько типов реализуйте интерфейс и поэтому может передаваться как runnables напрямую.


### <a name="inner-classes"></a>Внутренние классы

Java имеет два различных типа [вложенные классы](http://download.oracle.com/javase/tutorial/java/javaOO/nested.html): static вложенные классы и классы не статическими.

В C# вложенные типы идентичны статических вложенных классов Java.

Для не являющегося статическим вложенные классы, также называемый *внутренние классы*, значительно отличаются. Они содержат неявную ссылку на экземпляр содержащим их типом и не может содержать статические члены (среди других различий вне области в этом обзоре).

Когда дело доходит до привязки и C# используют, статические вложенные классы рассматриваются как обычные вложенные типы. Внутренние классы в свою очередь, имеют два значительные отличия:

1. В качестве параметра конструктора неявные ссылки на вмещающий тип должен быть предоставлен явно.

1. При наследовании от внутреннего класса, внутреннего класса *должен* быть вложен в тип, наследуемый от вмещающего типа базового класса внутреннее и производного типа должен предоставлять конструктор того же типа, как в C# содержит тип.


Например, рассмотрим [Android.Service.Wallpaper.WallpaperService.Engine](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService+Engine/) внутреннего класса. Поскольку это внутреннего класса, [конструктор WallpaperService.Engine()](https://developer.xamarin.com/api/constructor/Android.Service.Wallpaper.WallpaperService+Engine.Engine/p/Android.Service.Wallpaper.WallpaperService/) принимает ссылку на [WallpaperService](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService/) экземпляр (сравнение и сопоставление прогнозов для Java [WallpaperService.Engine () конструктор)](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService+Engine/) которого не принимает параметров).

Пример производным от внутреннего класса является CubeWallpaper.CubeEngine:

```csharp
class CubeWallpaper : WallpaperService {
        public override WallpaperService.Engine OnCreateEngine ()
        {
                return new CubeEngine (this);
        }

        class CubeEngine : WallpaperService.Engine {
                public CubeEngine (CubeWallpaper s)
                        : base (s)
                {
                }
        }
}
```

Обратите внимание как `CubeWallpaper.CubeEngine` вкладывается в `CubeWallpaper`, `CubeWallpaper` наследует от класса, содержащего `WallpaperService.Engine`, и `CubeWallpaper.CubeEngine` имеет конструктор, который принимает объявляющий тип-- `CubeWallpaper` в этом случае--все как указано выше.


### <a name="interfaces"></a>интерфейсов,

Java-интерфейсов может содержать три набора элементов, два из которых привести к проблемам из C#:

1. Методы

1. Типы

1. Поля


Java-интерфейсов преобразуются в двух типов:

1. (Необязательно) интерфейс, содержащий объявления методов. Этот интерфейс имеет то же имя, как интерфейс Java *за исключением* также имеет " *я* " префикс.

1. (Необязательно) статический класс, содержащий все поля, объявленные в интерфейсе Java.

Вложенные типы «перемещены» находятся на одном уровне внешнего интерфейса вместо вложенных типов, с префиксом имя внешнего интерфейса.

Например, рассмотрим [android.os.Parcelable](https://developer.xamarin.com/api/type/Android.OS.Parcelable/) интерфейса.
*Parcelable* интерфейс содержит методы, вложенные типы и константы. *Parcelable* методы интерфейса помещаются в [Android.OS.IParcelable](https://developer.xamarin.com/api/type/Android.OS.IParcelable/) интерфейса.
*Parcelable* константы интерфейса помещаются в [Android.OS.ParcelableConsts](https://developer.xamarin.com/api/type/Android.OS.ParcelableConsts/) типа. Вложенный [android.os.Parcelable.ClassLoaderCreator <t> </t> ](http://developer.android.com/reference/android/os/Parcelable.ClassLoaderCreator.html) и [android.os.Parcelable.Creator <t> </t> ](http://developer.android.com/reference/android/os/Parcelable.Creator.html) типов в данный момент привязать из-за ограничений в поддержке универсальные типы; Если они поддерживались, они будут присутствовать, если *Android.OS.IParcelableClassLoaderCreator* и *Android.OS.IParcelableCreator* интерфейсов. Например, вложенные [android.os.IBinder.DeathRecpient](http://developer.android.com/reference/android/os/IBinder.DeathRecipient.html) интерфейс привязан как [Android.OS.IBinderDeathRecipient](https://developer.xamarin.com/api/type/Android.OS.IBinderDeathRecipient/) интерфейса.


> [!NOTE]
> Начиная с Xamarin.Android 1.9, являются константами интерфейса Java <em>дублируется</em> с целью упростить перенос Java кода. Это поможет улучшить перенос кода Java, которое зависит от [android поставщика](http://developer.android.com/reference/android/provider/package-summary.html) интерфейс константы.

Помимо перечисленных выше типов существует четыре дальнейших изменений.

1. Тип с тем же именем, как интерфейс Java создается для хранения констант.

1. Типы, содержащие константы, интерфейс также содержат все константы, полученные из реализованных интерфейсов Java.

1. Все классы, реализующие интерфейс Java, содержащие константы, получить новый вложенных InterfaceConsts тип, который содержит константы из всех интерфейсов, реализованных.

1. *Констант* типа больше не используется.


Для *android.os.Parcelable* интерфейса, это означает, что теперь будет [ *Android.OS.Parcelable* ](https://developer.xamarin.com/api/type/Android.OS.Parcelable/) тип, содержащий константы. Например [Parcelable.CONTENTS_FILE_DESCRIPTOR](http://developer.android.com/reference/android/os/Parcelable.html#CONTENTS_FILE_DESCRIPTOR) константа будет привязан как [ *Parcelable.ContentsFileDescriptor* ](https://developer.xamarin.com/api/field/Android.OS.Parcelable.ContentsFileDescriptor/) константой, а не как  *ParcelableConsts.ContentsFileDescriptor* константой.

Для интерфейса, содержащих константы, которые реализуют другие интерфейсы содержащий еще несколько констант создается объединением всех константы. Например [android.provider.MediaStore.Video.VideoColumns](http://developer.android.com/reference/android/provider/MediaStore.Video.VideoColumns.html) интерфейс реализует [android.provider.MediaStore.MediaColumns](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+MediaColumns/) интерфейса. Тем не менее, прежде чем 1.9 [Android.Provider.MediaStore.Video.VideoColumnsConsts](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+Video+VideoColumnsConsts/) тип не имеет возможности доступа к константы, объявленные в [Android.Provider.MediaStore.MediaColumnsConsts](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+MediaColumnsConsts/).
Таким образом, выражение Java *MediaStore.Video.VideoColumns.TITLE* должен быть привязан к выражение C# *MediaStore.Video.MediaColumnsConsts.Title* которого будет сложно обнаружить без чтения большое количество документации по Java. 1.9, эквивалентное выражение C# будет [ *MediaStore.Video.VideoColumns.Title*](https://developer.xamarin.com/api/field/Android.Provider.MediaStore+Video+VideoColumns.Title/).

Кроме того, учтите [android.os.Bundle](https://developer.xamarin.com/api/type/Android.OS.Bundle/) тип, который реализует Java *Parcelable* интерфейса. Так как он реализует интерфейс, все константы в этом интерфейсе доступны «через» тип пакета, например *Bundle.CONTENTS_FILE_DESCRIPTOR* вполне допустимое выражение Java.
Ранее, перенос этого выражения в C# необходимо рассмотреть все интерфейсы, реализуемые от какого типа *CONTENTS_FILE_DESCRIPTOR* , откуда. Начиная с версии Xamarin.Android 1.9, классы, реализующие интерфейсы Java, которые содержат константы будет иметь вложенную *InterfaceConsts* тип, который будет содержать все константы наследуемого интерфейса. Это позволит выполнять перевод *Bundle.CONTENTS_FILE_DESCRIPTOR* для [ *Bundle.InterfaceConsts.ContentsFileDescriptor*](https://developer.xamarin.com/api/field/Android.OS.Bundle+InterfaceConsts.ContentsFileDescriptor/).

Наконец, типы с *констант* например *Android.OS.ParcelableConsts* теперь устарело, отличный от новых InterfaceConsts вложенные типы. Они будут удалены в Xamarin.Android 3.0.


## <a name="resources"></a>Ресурсы

Изображения, макет описания, двоичные и словари строк может быть включено в приложении как [файлы ресурсов](http://developer.android.com/guide/topics/resources/providing-resources.html).
Различные интерфейсы API Android предназначены для [оперируют идентификаторы ресурсов](http://developer.android.com/guide/topics/resources/accessing-resources.html) вместо работы с изображениями, строки или двоичного большие двоичные объекты напрямую.

К примеру, образец приложения Android, содержащий макет пользовательского интерфейса ( `main.axml`), строка таблицы интернационализации ( `strings.xml`) и несколько значков ( `drawable-*/icon.png`) обеспечит оперативность его ресурсам в каталоге «Ресурсы» приложения:

    Resources/
        drawable-hdpi/
            icon.png

        drawable-ldpi/
            icon.png

        drawable-mdpi/
            icon.png

        layout/
            main.axml

        values/
            strings.xml

Собственный Android API-интерфейсы работают непосредственно с именами файлов, но вместо оперируют идентификаторы ресурсов. При компиляции приложения Android, использующих ресурсы системы построения упаковывать ресурсы для распространения и создать класс с именем `Resource` , содержащий маркеры для каждого из ресурсов, включаемых. Например выше макета ресурсов, это класс R, которые будут предоставлять:

```csharp
public class Resource {
    public class Drawable {
        public const int icon = 0x123;
    }

    public class Layout {
        public const int main = 0x456;
    }

    public class String {
        public const int first_string = 0xabc;
        public const int second_string = 0xbcd;
    }
}
```

Затем используется `Resource.Drawable.icon` ссылка `drawable/icon.png` файл, или `Resource.Layout.main` ссылку на `layout/main.xml` файл, или `Resource.String.first_string` для ссылки на первой строки в файле словаря `values/strings.xml`.


## <a name="constants-and-enumerations"></a>Константы и перечисления

Собственный Android API имеют множество методов, которые принимают или возвращают int, должно быть сопоставлено полю константы для определения того, что означает int. Чтобы использовать эти методы, пользователю требуется обратиться к документации, чтобы узнать, какие константы соответствующие значения, который не является идеальным.

Например, рассмотрим [Activity.requestWindowFeature (int featureID)](http://developer.android.com/reference/android/app/Activity.html#requestWindowFeature(int)).

В таких случаях мы процесс для группирования связанных констант в перечислении .NET и повторно сопоставить метод вступили в перечислении.
Таким образом, не способна обеспечить выбор IntelliSense возможных значений.

Приведенный выше пример становится: [Activity.RequestWindowFeature (WindowFeatures featureId)](https://developer.xamarin.com/api/member/Android.App.Activity.RequestWindowFeature/p/Android.Views.WindowFeatures/)).

Обратите внимание, что это очень ручной процесс, чтобы понять, какие константы связанные друг с другом, какие интерфейсы API используют эти константы. Можно регистрировать ошибки любое использование констант в API, что было бы лучше выражается как перечисление.
