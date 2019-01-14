---
title: Принципы проектирования API Xamarin.Android
ms.prod: xamarin
ms.assetid: 3E52D815-D95D-5510-0D8F-77DAC7E62EDE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 54479a7ed66c83d1d97d51cc93e3df3241ec740f
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207938"
---
# <a name="xamarinandroid-api-design-principles"></a>Принципы проектирования API Xamarin.Android


## <a name="overview"></a>Обзор

Помимо основных библиотеках базовых классов, которые являются частью Mono Xamarin.Android поставляется с привязками для различные интерфейсы API Android позволить разработчикам создавать собственные приложения Android с Mono.

В основе Xamarin.Android является подсистемой взаимодействия этим миром мосты C# со всем миром Java и предоставляет разработчикам доступ к API-интерфейсы Java в C# и других языков .NET.


## <a name="design-principles"></a>Принципы проектирования

Вот некоторые из наших принципов проектирования для привязки Xamarin.Android

-  Соответствует [рекомендации по разработке .NET Framework](https://docs.microsoft.com/dotnet/standard/design-guidelines/).

-  Позволяют разработчикам подкласс классов Java.

-  Подкласс должны работать с стандартные конструкции C#.

-  Являются производными от существующего класса.

-  Вызовите конструктор базового класса для цепочки.

-  Переопределение методов должны выполняться с помощью C# переопределение системы.

-  Сделайте возможным типичные задачи Java легко и жесткие задачах Java.

-  Предоставлять свойства вируальная машина Java в качестве свойства C#.

-  Предоставлять строго типизированные API:

    - Повышения безопасности типов.

    - Свести к минимуму ошибки времени выполнения.

    - Получение intellisense в интегрированной среде разработки на типы возвращаемого значения.

    - Позволяет документации всплывающее окно интегрированной среды разработки.

-  Рекомендуем IDE в изучении API-интерфейсы:

    - Используйте Framework альтернативы раскрытия свести к минимуму Classlib Java.

    - Предоставлять делегаты в C# (лямбда-выражения, анонимные методы и System.Delegate) вместо интерфейсов одним методом, когда подходящие.

    - Предоставляет механизм для вызова произвольных библиотеки Java ( [Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/)).


## <a name="assemblies"></a>Сборки

Xamarin.Android включает несколько сборок, которые составляют *MonoMobile профиль*. [Сборки](~/cross-platform/internals/available-assemblies.md) страница содержит дополнительные сведения.

Привязки для платформы Android содержатся в `Mono.Android.dll` сборки. Эта сборка содержит всю привязку для значительного Android API-интерфейсов и обмена данными с Android среды выполнения виртуальной Машины.


## <a name="binding-design"></a>Привязка проектирования


### <a name="collections"></a>Коллекции

Android интерфейсы API используют java.util коллекций широко предоставляют списки, наборы и карты. Мы предоставляем эти элементы с помощью [System.Collections.Generic](xref:System.Collections.Generic) интерфейсов в нашей привязкой. Приведены основные сопоставления.

-   [java.util.Set<E> ](http://developer.android.com/reference/java/util/Set.html) сопоставляется с системным типом [ICollection<T>](xref:System.Collections.Generic.ICollection`1), вспомогательный класс [Android.Runtime.JavaSet<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaSet%601/).

-   [java.util.List<E> ](http://developer.android.com/reference/java/util/List.html) сопоставляется с системным типом [IList<T>](xref:System.Collections.Generic.IList`1), вспомогательный класс [Android.Runtime.JavaList<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaList%601/).

-   [java.util.Map < K, V >](http://developer.android.com/reference/java/util/Map.html) сопоставляется с системным типом [IDictionary < TKey, TValue >](xref:System.Collections.Generic.IDictionary`2), вспомогательный класс [Android.Runtime.JavaDictionary < K, V >](https://developer.xamarin.com/api/type/Android.Runtime.JavaDictionary%602/).

-   [java.util.Collection<E> ](http://developer.android.com/reference/java/util/Collection.html) сопоставляется с системным типом [ICollection<T>](xref:System.Collections.Generic.ICollection`1), вспомогательный класс [Android.Runtime.JavaCollection<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaCollection%601/).

Мы предоставили вспомогательные классы для упрощения быстрее copyless маршалинга этих типов. Если это возможно, мы рекомендуем использовать эти предоставляемые коллекции вместо реализации платформы, такие как [ `List<T>` ](xref:System.Collections.Generic.List`1) или [ `Dictionary<TKey, TValue>` ](xref:System.Collections.Generic.Dictionary`2). [Android.Runtime](https://developer.xamarin.com/api/namespace/Android.Runtime/) реализаций внутренним образом используют собственную коллекцию Java и поэтому не требуют копирования из собственного коллекции и при передаче в член Android API.

Вы можете передать любую реализацию интерфейса Android методу принимать этот интерфейс, например передать `List<int>` для [ArrayAdapter&lt;int&gt;(контекст, int, IList&lt;int&gt;)](https://developer.xamarin.com/api/constructor/Android.Widget.ArrayAdapter%3CT%3E.ArrayAdapter%3CT%3E/p/Android.Content.Context/System.Int32/System.Collections.Generic.IList%7BT%7D/)конструктор. *Тем не менее*, для всех реализаций *за исключением* для реализаций Android.Runtime, это подразумевает *копирование* списка с Mono виртуальной Машины в среде выполнения Android виртуальной Машины. Если список является более поздней версии, измененных в течение среды выполнения Android (например, путем вызова [ArrayAdapter&lt;T&gt;. Add(T)](https://developer.xamarin.com/api/member/Android.Widget.ArrayAdapter%3CT%3E.Add/p/T/) метод), эти изменения *не* быть видимым в управляемом коде. Если `JavaList<int>` были используется, эти изменения будет видима.

Перефразировано, коллекции интерфейса реализации, которые являются *не* одно из перечисленных выше перечисленных **вспомогательный класс**es только маршалинг [In]:

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

-  Пары методов Java `T getFoo()` и `void setFoo(T)` преобразуются в `Foo` свойство. Пример [Activity.Intent](https://developer.xamarin.com/api/property/Android.App.Activity.Intent/).

-  Следующий метод Java `getFoo()` преобразуется в свойство Foo только для чтения. Пример [Context.PackageName](https://developer.xamarin.com/api/property/Android.Content.Context.PackageName/).

-  Только для задания свойств не создаются.

-  Свойства являются *не* создано, если тип свойства должен быть массивом.



### <a name="events-and-listeners"></a>События и прослушиватели

Android интерфейсы API создаются на основе Java, и его компоненты используют шаблон Java подключения прослушивателей событий. Этот шаблон обычно быть неудобно, так как требует, чтобы пользователю создавать анонимный класс и объявить переопределяемых методов, например, это, как выполняются действия в Android с помощью Java:

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

Обратите внимание на то, что оба указанных выше механизмов доступны с помощью Xamarin.Android. Можно реализовать интерфейс прослушивателя и подключить его с View.SetOnClickListener, или вы можете подключить делегат, созданных с помощью любой из обычного парадигм C# для события щелчка.

Когда метод обратного вызова прослушиватель имеет возвращаемый тип void, мы создаем элементы API на основе [EventHandler&lt;TEventArgs&gt; ](xref:System.EventHandler`1) делегировать. Мы создавать событие, как показано в примере выше для этих типов прослушивателя. Тем не менее если прослушиватель обратный вызов возвращает отличное от void и не- **логическое** значение "," события "и" EventHandlers не используются. Вместо создания определенного делегата для сигнатуры функции обратного вызова и добавить свойства, а не события. Причина — имеют дело с порядок вызова делегата и возвращать обработки. Этот подход отражает то, что необходимо сделать с API-Интерфейс Xamarin.iOS.

C# события или свойства, только автоматически создаются при условии метод Android регистрации событий:

1. Имеет `set` префикс, например [ *задать*OnClickListener](https://developer.xamarin.com/api/member/Android.Views.View.SetOnClickListener/).

1. Имеет `void` тип возвращаемого значения.

1. Принимает только один параметр, тип параметра является интерфейсом, интерфейс содержит только один метод и имя интерфейса заканчивается `Listener` , например [View.OnClick *прослушивателя*](https://developer.xamarin.com/api/type/Android.Views.View+IOnClickListener/).


Кроме того, если метод интерфейса прослушиватель имеет тип возвращаемого значения **логическое** вместо **void**, созданный *EventArgs* подкласс будет содержать *Обработанных* свойство. Значение *обработанных* свойство используется в качестве возвращаемых значений для *прослушивателя* метод и он по умолчанию используется `true`.

Например, Android [View.setOnKeyListener()](https://developer.xamarin.com/api/member/Android.Views.View.SetOnKeyListener/p/Android.Views.View+IOnKeyListener/) метод принимает [View.OnKeyListener](https://developer.xamarin.com/api/type/Android.Views.View+IOnKeyListener) интерфейс и [View.OnKeyListener.onKey (представления, int, KeyEvent)](https://developer.xamarin.com/api/member/Android.Views.View+IOnKeyListener.OnKey/p/Android.Views.View/Android.Views.Keycode/Android.Views.KeyEvent/) метод имеет логический тип возвращаемого значения. Xamarin.Android создает соответствующий [View.KeyPress](https://developer.xamarin.com/api/event/Android.Views.View.KeyPress/) событие, которое является [EventHandler&lt;View.KeyEventArgs&gt;](https://developer.xamarin.com/api/type/Android.Views.View+KeyEventArgs/).
*KeyEventArgs* класс в свою очередь имеет [View.KeyEventArgs.Handled](https://developer.xamarin.com/api/property/Android.Views.View+KeyEventArgs.Handled/) свойство, которое используется в качестве возвращаемых значений для *View.OnKeyListener.onKey()* метод.

Мы намерены добавить перегрузки другие методы и конструкторы для предоставления подключения на основе делегата. Кроме того прослушиватели несколько обратных вызовов требуются некоторые дополнительные проверки, чтобы определить, если реализация отдельных обратных вызовов целесообразно, так как они определяются эти выполняется преобразование. Если нет соответствующего события, прослушиватели должны использоваться в C#, но переведите, вы думаете, может иметь использования делегата для нашего внимания. Мы проделали некоторые преобразования интерфейсов без суффикса «Прослушиватель» также когда стало ясно, что они могут с выгодой альтернатива делегата.

Реализовать все интерфейсы, прослушиватели [`Android.Runtime.IJavaObject`](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/)
интерфейс, из-за детали реализации привязки, чтобы прослушиватель классы должны реализовывать этот интерфейс. Это можно сделать путем реализации интерфейса прослушивателя на подкласс [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) или любой другой в оболочку объекта Java, например действие Android.


### <a name="runnables"></a>Runnables

Java использует [java.lang.Runnable](https://developer.xamarin.com/api/type/Java.Lang.Runnable/) интерфейс, чтобы предоставить механизм делегирования. [Java.lang.Thread](https://developer.xamarin.com/api/type/Java.Lang.Thread/) класс — важные получатель этого интерфейса. Android нанимает интерфейс в API также.
[Activity.runOnUiThread()](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/p/Java.Lang.IRunnable/) и [View.post()](https://developer.xamarin.com/api/member/Android.Views.View.Post/p/Java.Lang.IRunnable) примечательных примеров.

`Runnable` Интерфейс содержит один метод void, [run()](https://developer.xamarin.com/api/member/Java.Lang.Runnable.Run%28%29/). Он пригоден для привязки в C# в качестве таким образом [System.Action](xref:System.Action) делегировать. Мы предоставили перегрузки в привязке, которые принимают `Action` параметр для всех элементов API, которые используют `Runnable` в собственном API, например [Activity.RunOnUiThread()](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/(System.Action)) и [View.Post()](https://developer.xamarin.com/api/member/Android.Views.View.Post/(System.Action)).

Мы оставили [интерфейс IRunnable](https://developer.xamarin.com/api/type/Java.Lang.IRunnable/) перегрузки на месте не заменяет их, так как несколько типов, реализуйте интерфейс и поэтому может передаваться как runnables напрямую.


### <a name="inner-classes"></a>Внутренние классы

Java поддерживает два различных типа [вложенные классы](http://download.oracle.com/javase/tutorial/java/javaOO/nested.html): static вложенные классы и нестатические классы.

В C# вложенные типы идентичны статическим вложенным классам Java.

Нестатические вложенные классы, также называемый *внутренние классы*, значительно отличаются. Они содержат неявную ссылку на экземпляр содержащим их типом и не может содержать статические члены (среди других различий выходит за рамки этого обзора).

Когда дело доходит до привязки и C# используют, статическим вложенным классам рассматриваются как обычные вложенные типы. Внутренние классы, в то же время, имеют два существенных отличия:

1. Неявную ссылку на содержащий тип должен быть предоставлен явно в параметре конструктора.

1. При наследовании от внутреннего класса, внутреннему классу *необходимо* быть вложен в тип, производный от вмещающего типа базового класса внутреннее и производный тип должны предоставлять конструктор того же типа, как в C# содержащий тип.


Например, рассмотрим [Android.Service.Wallpaper.WallpaperService.Engine](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService+Engine/) внутреннего класса. Так как это внутренний класс, [конструктор WallpaperService.Engine()](https://developer.xamarin.com/api/constructor/Android.Service.Wallpaper.WallpaperService+Engine.Engine/p/Android.Service.Wallpaper.WallpaperService/) принимает ссылку на [WallpaperService](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService/) экземпляра (сравнения и сравните с Java [WallpaperService.Engine () конструктор)](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService+Engine/) которого не принимает параметров).

Пример наследования вложенного класса является CubeWallpaper.CubeEngine:

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

Обратите внимание как `CubeWallpaper.CubeEngine` вкладывается в `CubeWallpaper`, `CubeWallpaper` наследует от содержащего класса из `WallpaperService.Engine`, и `CubeWallpaper.CubeEngine` имеет конструктор, принимающий объявляющий тип-- `CubeWallpaper` в этом случае--все как указано выше.


### <a name="interfaces"></a>интерфейсов,

Java-интерфейсов может содержать три набора элементов, два из которых вызвать проблемы с помощью C#:

1. Методы

1. Типы

1. Поля


Java-интерфейсов, преобразуются в двух типов:

1. (Необязательно) интерфейс, содержащий объявления метода. Этот интерфейс имеет то же имя, как Java-интерфейса *за исключением* также имеет " *я* " префикс.

1. (Необязательно) статический класс, содержащий все поля, объявленные в Java-интерфейса.

Вложенные типы «перемещены» находятся на одном уровне включающего интерфейса вместо вложенных типов, включающего имя интерфейса как префикс.

Например, рассмотрим [android.os.Parcelable](https://developer.xamarin.com/api/type/Android.OS.Parcelable/) интерфейс.
*Parcelable* интерфейс содержит методы, вложенные типы и константы. *Parcelable* методы интерфейса помещаются в [Android.OS.IParcelable](https://developer.xamarin.com/api/type/Android.OS.IParcelable/) интерфейс.
*Parcelable* константы интерфейса помещаются в [Android.OS.ParcelableConsts](https://developer.xamarin.com/api/type/Android.OS.ParcelableConsts/) типа. Вложенный [android.os.Parcelable.ClassLoaderCreator <t> </t> ](http://developer.android.com/reference/android/os/Parcelable.ClassLoaderCreator.html) и [android.os.Parcelable.Creator <t> </t> ](http://developer.android.com/reference/android/os/Parcelable.Creator.html) типов в настоящее время не вывода из-за ограничения в поддержке универсальных типов; Если они поддерживались, они будут представлены *Android.OS.IParcelableClassLoaderCreator* и *Android.OS.IParcelableCreator* интерфейсов. Например, вложенные [android.os.IBinder.DeathRecpient](http://developer.android.com/reference/android/os/IBinder.DeathRecipient.html) интерфейс привязан как [Android.OS.IBinderDeathRecipient](https://developer.xamarin.com/api/type/Android.OS.IBinderDeathRecipient/) интерфейс.


> [!NOTE]
> Начиная с Xamarin.Android 1.9, Java интерфейс константы — это <em>дублируются</em> с целью упростить перенос Java кода. Это поможет улучшить перенос кода Java, которая зависит от [android поставщика](http://developer.android.com/reference/android/provider/package-summary.html) интерфейс константы.

Помимо перечисленных выше типов существует четыре дальнейшие изменения:

1. Тип с тем же именем, как Java-интерфейса создается для хранения констант.

1. Типы, содержащие константы, интерфейс, также содержат все константы, полученных из реализованных интерфейсов Java.

1. Все классы, реализующие интерфейс Java, содержащие константы, получение нового вложенного типа InterfaceConsts, который содержит константы из всех реализованных интерфейсов.

1. *Расходы* тип больше не используется.


Для *android.os.Parcelable* интерфейс, это означает, что теперь будет [ *Android.OS.Parcelable* ](https://developer.xamarin.com/api/type/Android.OS.Parcelable/) тип должен содержать константы. Например [Parcelable.CONTENTS_FILE_DESCRIPTOR](http://developer.android.com/reference/android/os/Parcelable.html#CONTENTS_FILE_DESCRIPTOR) константа будет привязан как [ *Parcelable.ContentsFileDescriptor* ](https://developer.xamarin.com/api/field/Android.OS.Parcelable.ContentsFileDescriptor/) константы, а не как  *ParcelableConsts.ContentsFileDescriptor* константы.

Для интерфейсов, содержащий константы, которые реализуют другие интерфейсы содержащий еще несколько констант теперь создается объединение все константы. Например [android.provider.MediaStore.Video.VideoColumns](http://developer.android.com/reference/android/provider/MediaStore.Video.VideoColumns.html) интерфейс реализует [android.provider.MediaStore.MediaColumns](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+MediaColumns/) интерфейс. Тем не менее, предшествующих 1.9 [Android.Provider.MediaStore.Video.VideoColumnsConsts](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+Video+VideoColumnsConsts/) тип не имеет доступа к константы, объявленных в [Android.Provider.MediaStore.MediaColumnsConsts](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+MediaColumnsConsts/).
В результате выражения Java *MediaStore.Video.VideoColumns.TITLE* необходимо привязать к выражение C# *MediaStore.Video.MediaColumnsConsts.Title* это трудно обнаруживать без чтения множество документации по Java. В версии 1.9, будет эквивалентное выражение C# [ *MediaStore.Video.VideoColumns.Title*](https://developer.xamarin.com/api/field/Android.Provider.MediaStore+Video+VideoColumns.Title/).

Кроме того, рассмотрите [android.os.Bundle](https://developer.xamarin.com/api/type/Android.OS.Bundle/) тип, который реализует Java *Parcelable* интерфейс. Так как он реализует интерфейс, все константы в этом интерфейсе доступны «через» тип пакета, например *Bundle.CONTENTS_FILE_DESCRIPTOR* является вполне допустимым выражением Java.
Ранее это выражение, чтобы перенести C# потребовалось бы рассмотреть все интерфейсы, реализуемые от какого типа *CONTENTS_FILE_DESCRIPTOR* поступили. Начиная с Xamarin.Android 1.9, классы, реализующие интерфейсы Java, которые содержат константы будет иметь вложенную *InterfaceConsts* тип, который будет содержать все константы наследуемого интерфейса. Таким образом, перевод *Bundle.CONTENTS_FILE_DESCRIPTOR* для [ *Bundle.InterfaceConsts.ContentsFileDescriptor*](https://developer.xamarin.com/api/field/Android.OS.Bundle+InterfaceConsts.ContentsFileDescriptor/).

Наконец, типы с *расходы* например *Android.OS.ParcelableConsts* теперь устарело, отличные от новых InterfaceConsts вложенные типы. Они будут удалены в Xamarin.Android 3.0.


## <a name="resources"></a>Ресурсы

Изображения, описания макета, большие двоичные объекты и словари строк может быть включено в приложении как [файлы ресурсов](http://developer.android.com/guide/topics/resources/providing-resources.html).
Различные интерфейсы API Android предназначены для [оперируют идентификаторы ресурсов](http://developer.android.com/guide/topics/resources/accessing-resources.html) вместо того чтобы разбираться с изображениями, строки или двоичного файла BLOB-объектов напрямую.

Например, Android пример приложения, содержащий макет пользовательского интерфейса ( `main.axml`), строку таблицы интернационализации ( `strings.xml`) и некоторые значки ( `drawable-*/icon.png`) сохранит его ресурсы в каталоге «Ресурсы» приложения:

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

Собственный Android API-интерфейсы работают непосредственно с именами файлов, но вместо этого оперируют идентификаторы ресурсов. При компиляции приложения Android, использующее ресурсы, система сборки будет упаковывать ресурсы для распространения и создать класс с именем `Resource` , содержащий маркеры для каждого из ресурсов включаются. Например для макета выше ресурсов, это класс R, которое приведет к:

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

Затем используется `Resource.Drawable.icon` ссылка `drawable/icon.png` файл, или `Resource.Layout.main` ссылка `layout/main.xml` файл, или `Resource.String.first_string` для ссылки на первой строки в файле словаря `values/strings.xml`.


## <a name="constants-and-enumerations"></a>Константы и перечисления

Собственный Android API-интерфейсы имеют множество методов, которые принимают или возвращают значение типа int, должна быть сопоставлена с константного поля, чтобы определить, что int. Чтобы использовать эти методы, пользователь является обязательным для см. в документации, чтобы увидеть, какие константы имеют соответствующие значения, который идеально подходит меньше.

Например, рассмотрим [Activity.requestWindowFeature (int featureID)](http://developer.android.com/reference/android/app/Activity.html#requestWindowFeature(int)).

В таких случаях для группирования связанных констант в перечислении .NET и переназначить метод выполнить перечисление.
Таким образом, мы могут предложить выделение IntelliSense из возможных значений.

Приведенный выше пример выглядит следующим образом: [Activity.RequestWindowFeature (WindowFeatures featureId)](https://developer.xamarin.com/api/member/Android.App.Activity.RequestWindowFeature/p/Android.Views.WindowFeatures/).

Обратите внимание, что это очень ручной процесс, чтобы выяснить, какие константы связаны друг с другом, а какие интерфейсы API используют эти константы. Отправьте сведения об ошибках все константы, используемые в API, которая была бы лучше выражается как перечисление.
