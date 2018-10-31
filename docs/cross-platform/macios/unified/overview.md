---
title: Общие сведения о единой API
description: Xamarin единый API делает возможным совместное использование кода в Mac и iOS и поддерживают 32- и 64-разрядных приложений, в одном двоичном файле.
ms.prod: xamarin
ms.assetid: 5F0CEC18-5EF6-4A99-9DCF-1A3B57EA157C
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 1d159d280bd3b8855c32e3e437dfdefcbe0463cb
ms.sourcegitcommit: 4859da8772dbe920fdd653180450e5ddfb436718
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50235029"
---
# <a name="unified-api-overview"></a>Общие сведения о единой API

Xamarin единый API делает возможным совместное использование кода в Mac и iOS и поддерживают 32- и 64-разрядных приложений, в одном двоичном файле. На единый API используется по умолчанию в новых проектах Xamarin.iOS и Xamarin.Mac.

> [!IMPORTANT]
> Классический API Xamarin, перед на единый API, является устаревшим. 
> - Последняя версия Xamarin.iOS, для поддержки классический API (monotouch.dll) был Xamarin.iOS 9.10.
> - Xamarin.Mac по-прежнему поддерживает классический API, но больше не обновляется. Так как она устарела, разработчикам следует переместить свои приложения на единый API.

## <a name="updating-classic-api-based-apps"></a>Обновление классического приложения на основе API

Выполните соответствующие инструкции для вашей платформы.

- [Обновление имеющихся приложений](updating-apps.md)
- [Обновление имеющихся приложений iOS](updating-ios-apps.md)
- [Обновление имеющихся приложений Mac](updating-mac-apps.md)
- [Обновление имеющихся приложений Xamarin.Forms](updating-xamarin-forms-apps.md)
- [Миграция привязки в Unified API](update-binding.md)

## <a name="tips-for-updating-code-to-the-unified-apiupdating-tipsmd"></a>[Советы по обновлению кода в Unified API](updating-tips.md)

Независимо от того, какие приложения вы переносите, ознакомьтесь с [эти советы](updating-tips.md) для успешного обновления на единый API.

## <a name="library-split"></a>Библиотека разбиения

С этого момента наши API-интерфейсы будут отображаться двумя способами:

-  **Классический API:** ограничен 32 бита (только) и предоставлять их в `monotouch.dll` и `XamMac.dll` сборок.
-  **Unified API:** поддерживает 32- и 64 разрядной разработки с помощью одного API, доступные в `Xamarin.iOS.dll` и `Xamarin.Mac.dll` сборок.

Это означает, что для корпоративных разработчиков (не предназначенных App Store), вы можете продолжать использовать существующие классические API, как мы будет поддерживать обслуживание их навсегда, или можно обновить до новых интерфейсов API.

<a name="namespace-changes" />

## <a name="namespace-changes"></a>Изменения пространства имен

Чтобы уменьшить трения совместное использование кода между продуктов Mac и iOS, мы изменяем пространства имен для API-интерфейсов в продуктах.

Мы пропускают префикс «MonoTouch» из «MonoMac» и операций ввода-вывода продукта из нашего продукта Mac для типов данных.

Это упрощает совместное использование кода на платформах Mac и iOS, не прибегая к условной компиляции, а также сократить шума в верхней части файлов исходного кода.

-  **Классический API:** использовать пространства имен `MonoTouch.` или `MonoMac.` префикс.
-  **Unified API:** без префикса пространства имен

## <a name="runtime-defaults"></a>Значения по умолчанию среды выполнения

На единый API по умолчанию использует **SGen** сборщика мусора и [новый подсчет ссылок](~/ios/internals/newrefcount.md) система отслеживания владения объектом. Эта же функция был перенесен на Xamarin.Mac.

Это решает ряд проблем, которые разработчики сталкиваются со старой системы и упростить [управление памятью](~/cross-platform/deploy-test/memory-perf-best-practices.md).

Обратите внимание, что можно включить новый Refcount даже для Classic API, но по умолчанию является консервативным и не требуется вносить изменения. С помощью единого интерфейса API, мы воспользовались возможностью изменения значение по умолчанию и предоставить разработчикам все усовершенствования в то же время они рефакторинг и повторно тестировать их код.

## <a name="api-changes"></a>Изменения API

На единый API удаляет устаревшие методы и несколько экземпляров там, где произошли опечаток в именах API, когда они были привязаны к исходной MonoTouch MonoMac пространства имен и в классическом API. Эти экземпляры были исправлены в новых интерфейсов API единой и нужно будет обновляться в компонент, iOS и Mac-приложений. Ниже приведен список самых распространенных рисков, которыми вы можете столкнуться:

|Имя метода классический API|Имени унифицированный API-метод|
|--- |--- |
|`UINavigationController.PushViewControllerAnimated()`|`UINavigationController.PushViewController()`|
|`UINavigationController.PopViewControllerAnimated()`|`UINavigationController.PopViewController()`|
|`CGContext.SetRGBFillColor()`|`CGContext.SetFillColor()`|
|`NetworkReachability.SetCallback()`|`NetworkReachability.SetNotification()`|
|`CGContext.SetShadowWithColor`|`CGContext.SetShadow`|
|`UIView.StringSize`|`UIKit.UIStringDrawing.StringSize`|

Полный список изменений при переключении из классической модели на единый API, см. в разделе наших [Classic (monotouch.dll) vs отличия API единой (Xamarin.iOS.dll)](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/) документации.

## <a name="updating-to-unified"></a>Обновление до единой

Несколько старого/разбить/устаревшего API-интерфейса в **классической** недоступны в **единой** API. Его можно легко исправить `CS0616` обновляете предупреждения перед запуском вашего (автоматически или вручную), поскольку вы `[Obsolete]` атрибут сообщения (часть предупреждения) помогут вам в правом API.

Обратите внимание, что мы публикуем [ *diff* ](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/) -или классическом unified API изменения, которые могут использоваться до или после обновления проекта. По-прежнему исправления старые вызовы в классическом будет часто можно сэкономить много времени (меньше поиска документации).

Следуйте указаниям, приведенным к [обновление имеющихся приложений iOS](~/cross-platform/macios/unified/updating-ios-apps.md), или [приложений Mac](~/cross-platform/macios/unified/updating-mac-apps.md) на единый API.
Просмотрите оставшейся части этой страницы, и [эти советы](~/cross-platform/macios/unified/updating-tips.md) Дополнительные сведения о переносе кода.

### <a name="nuget"></a>NuGet

Пакеты NuGet, которые ранее поддерживались Xamarin.iOS через классический API публикации своих сборок с помощью **Monotouch10** моникер платформы.

На единый API вводит новый идентификатор платформы для совместимых пакетов — **Xamarin.iOS10**. Существующие пакеты NuGet потребуется обновить для добавления поддержки для этой платформы, создавая в единый API.

> [!IMPORTANT]
> При наличии ошибки в форме _«ошибка 3 нельзя включать в том же проекте Xamarin.iOS «monotouch.dll» и «Xamarin.iOS.dll» — «Xamarin.iOS.dll» указывается явным образом, хотя «monotouch.dll» ссылается "xxx, версия = 0.0.000, Culture = neutral, PublicKeyToken = null'»_ после преобразования приложения Unified API-интерфейсам, это обычно из-за наличия компонента или пакета NuGet в проекте, который не был обновлен на единый API. Необходимо удалить существующий компонент/NuGet, обновление до версии, которая поддерживает унифицированными API и выполните чистую сборку.

### <a name="the-road-to-64-bits"></a>Пути к 64-разрядная

Общие сведения о поддержке 32- и 64 разрядных приложений, так и сведения о платформах см. в разделе [32 и 64-разрядных платформы](~/cross-platform/macios/32-and-64/index.md).

 <a name="new-data-types" />

#### <a name="new-data-types"></a>Новые типы данных

В основе разницу Mac и iOS API-интерфейсы используют конкретной архитектуры типы данных, которые всегда являются 32-разрядной на 32-разрядных платформах и 64-разрядной на 64-разрядных платформах.

Например, сопоставляет Objective-C `NSInteger` тип данных для `int32_t` на 32-разрядных системах, позволяющая `int64_t` на 64-разрядных системах.

В соответствии с это поведение, на наш Unified API вместо прежнего использования `int` (в .NET определяется как всегда должны быть `System.Int32`) в новый тип данных: `System.nint`.  Можно считать из «n» значение «native», поэтому собственного целочисленный тип платформы.

Мы представляем `nint`, `nuint` и `nfloat` также предоставляет типы данных на основе их при необходимости.

Дополнительные сведения об этих изменениях типов данных, см. в разделе [собственные типы](~/cross-platform/macios/nativetypes.md) документа.

### <a name="how-to-detect-the-architecture-of-ios-apps"></a>Как определить архитектуру приложений iOS

Могут возникнуть ситуации, где приложения необходимо знать, если он под управлением 32-разрядная или 64-разрядной системы iOS. Чтобы проверить архитектуру можно использовать следующий код:

```csharp
if (IntPtr.Size == 4) {
    Console.WriteLine ("32-bit App");
} else if (IntPtr.Size == 8) {
    Console.WriteLine ("64-bit App");
}
```

<a name="deprecated-apis" />

### <a name="arrays-and-systemcollectionsgeneric"></a>Массивы и System.Collections.Generic

Так как C# индексаторы ожидать, что тип `int`, необходимо явно привести тип `nint` значения `int` для доступа к элементам в коллекции или массиве. Пример:

```csharp
public List<string> Names = new List<string>();
...

public string GetName(nint index) {
    return Names[(int)index];
}

```

Это ожидаемое поведение, так как на приведение из `int` для `nint` является потерей данных на 64-разрядная версия, неявное преобразование не выполняется.

### <a name="converting-datetime-to-nsdate"></a>Преобразование типа DateTime в NSDate

При использовании Unified API-интерфейсы, выполняется неявное преобразование `DateTime` для `NSDate` значения больше не выполняется. Эти значения необходимо будет явно преобразовываться из одного типа в другой. Чтобы автоматизировать этот процесс можно использовать следующие методы расширения:

```csharp
public static DateTime NSDateToDateTime(this NSDate date)
{
    // NSDate has a wider range than DateTime, so clip
    // the converted date to DateTime.Min|MaxValue.
    double secs = date.SecondsSinceReferenceDate;
    if (secs < -63113904000)
        return DateTime.MinValue;
    if (secs > 252423993599)
        return DateTime.MaxValue;
    return (DateTime) date;
}

public static NSDate DateTimeToNSDate(this DateTime date)
{
    if (date.Kind == DateTimeKind.Unspecified)
        date = DateTime.SpecifyKind (date, /* DateTimeKind.Local or DateTimeKind.Utc, this depends on each app */)
    return (NSDate) date;
}

```

<a name="deprecated-typos" />

### <a name="deprecated-apis-and-typos"></a>Устаревшие интерфейсы API и опечатки

Классический API Xamarin.iOS внутри (monotouch.dll) `[Obsolete]` атрибут использовался двумя разными способами:

-  **Рекомендуется использовать iOS API:** на этом этапе Apple подсказок для остановки теста с помощью API, так как он является заменяют новой. Классический API по-прежнему нормально и часто требуется (Если вы поддерживаете более старой версии iOS).
 Такие API (и `[Obsolete]` атрибут), включаются в новые сборки Xamarin.iOS.
-  **Неправильный API** некоторые API-Интерфейс содержит опечаток по их именам.

Для исходной сборки (monotouch.dll и XamMac.dll) сохранена старого кода, для совместимости, но они будут удалены из сборок Unified API (Xamarin.iOS.dll и Xamarin.Mac)

<a name="NSObject_ctor" />

### <a name="nsobject-subclasses-ctorintptr"></a>.Ctor(IntPtr) подклассы NSObject

Каждый `NSObject` подкласс имеет конструктор, принимающий `IntPtr`. Это как можно создать новый управляемый экземпляр из собственный дескриптор ObjC.

В классической модели это было `public` конструктор. Однако оказалось достаточно неправильно использовать эту функцию в пользовательском коде, например, создание нескольких управляемых экземпляров для одного экземпляра ObjC *или* , создание экземпляра управляемого будет недоставать ожидаемое состояние управляемых (для подклассов).

Чтобы избежать таких проблем `IntPtr` конструкторы являются теперь `protected` в **единой** API, используемый только для подклассов. Это гарантирует, что исправьте/safe API используется для создания управляемого экземпляра из дескрипторов, т. е.

    var label = Runtime.GetNSObject<UILabel> (handle);

Этот API возвращает существующего управляемого экземпляра (если он уже существует) или создаст новый (при необходимости). Он уже находится в классической и унифицированный API.

Обратите внимание, что `.ctor(NSObjectFlag)` теперь `protected` , но это редко используется вне подклассов.

<a name="NSAction" />

### <a name="nsaction-replaced-with-action"></a>Заменить действие NSAction

С помощью API-интерфейсы единой `NSAction` был удален в пользу standard .NET `Action`. Это является значительным улучшением, поскольку `Action` является типом .NET, тогда как `NSAction` была предназначена для Xamarin.iOS. В обоих случаях то же, но они были отличающимися и несовместимых типов и привела к больший объем кода, нужно быть записаны для достижения такого же результата.

Например, если существующее приложение Xamarin включен следующий код:

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (new NSAction (delegate() {
    ShowDropDownAnimated (tblDataView);
}));
```
Теперь его можно заменить на простое лямбда-выражения:

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (() => ShowDropDownAnimated(tblDataView));
```

Ранее это было бы ошибка компилятора поскольку `Action` не могут быть назначены `NSAction`, но поскольку `UITapGestureRecognizer` теперь принимает `Action` вместо `NSAction` допустимо в Unified API.

### <a name="custom-delegates-replaced-with-actiont"></a>Пользовательские делегаты, заменить действие<T>

В **единой** несколько простых (например, один параметр) .net-делегатам, были заменены `Action<T>`. Например,

    public delegate void NSNotificationHandler (NSNotification notification);

Теперь можно использовать как `Action<NSNotification>`. Этот код promote повторно использовать и сокращают дублирование кода внутри Xamarin.iOS и собственных приложений.

### <a name="taskbool-replaced-with-taskbooleannserror"></a>Задача<bool> заменены задачи < Boolean, NSError >>

В **классической** возникли некоторые асинхронные интерфейсы API возвращение `Task<bool>`. Однако некоторые из них где следует использовать, когда `NSError` был частью сигнатуры, т. е. `bool` уже `true` и требовалось перехватить исключение, чтобы получить `NSError`.

Так как некоторые ошибки являются очень часто, и возвращаемое значение невозможно было использовать этот шаблон был изменен в **единой** для возврата `Task<Tuple<Boolean,NSError>>`. Это позволяет проверить успешность и любая ошибка, могло бы произойти во время асинхронного вызова.

### <a name="nsstring-vs-string"></a>Строка NSString vs

В некоторых случаях была изменена из нескольких констант `string` для `NSString`, например `UITableViewCell`

**Классическая модель**

    public virtual string ReuseIdentifier { get; }

**Единая система**

    public virtual NSString ReuseIdentifier { get; }

В целом мы предпочитаем .NET `System.String` типа. Тем не менее, несмотря на рекомендации Apple собственного API сравнение константы указателей (но не саму строку), и это работают, только когда мы предоставляем констант как `NSString`.

 <a name="protocols" />

### <a name="objective-c-protocols"></a>Протоколы Objective-C

Исходное MonoTouch не имеет полную поддержку для протоколов ObjC и некоторые, не является оптимальным, API были добавлены для поддержки наиболее распространенным сценарием. Это ограничение больше не существует, но для обеспечения обратной совместимости, несколько интерфейсов API, хранятся вокруг внутри `monotouch.dll` и `XamMac.dll`.

Эти ограничения были удалены и очищены в Unified API-интерфейсы. Большинство изменений будет выглядеть следующим образом:

**Классическая модель**

    public virtual AVAssetResourceLoaderDelegate Delegate { get; }

**Единая система**

    public virtual IAVAssetResourceLoaderDelegate Delegate { get; }

`I` Префикса означает, что **единой** предоставлять интерфейс, а не конкретный тип, для протокола ObjC. Это упрощает случаев, где вы не хотите подкласс определенного типа, который предоставлен Xamarin.iOS.

Его также можно использовать некоторые API, чтобы быть более точными и проста в использовании, например:

**Классическая модель**

    public virtual void SelectionDidChange (NSObject uiTextInput);

**Единая система**

    public virtual void SelectionDidChange (IUITextInput uiTextInput);

Такие API стали проще для нас, не обращаясь к документации и автозавершение кода в интегрированной среде разработки предоставит вам удобнее предложения на основе протокола и интерфейса.

#### <a name="nscoding-protocol"></a>Протокол NSCoding

Наши исходную привязку .ctor(NSCoder) для каждого типа - включены, даже если он не поддерживает `NSCoding` протокола.  Один `Encode(NSCoder)` метод присутствовал в `NSObject` кодируемый объект.
Но этот метод будет работать только в том случае, если экземпляр согласование протокола NSCoding.

На единый API мы устранили это.  Новые сборки будут иметь только `.ctor(NSCoder)` Если тип соответствует `NSCoding`. Теперь есть такие типы `Encode(NSCoder)` метод, который соответствует `INSCoding` интерфейс.

Отсутствие серьезных последствий: В большинстве случаев это изменение не влияет на приложения как старые, удалены, конструкторы не может быть использована.

## <a name="further-tips"></a>Дополнительные советы

Дополнительные изменения, которые следует учитывать, перечислены в [советы по обновлению приложений на единый API](~/cross-platform/macios/unified/updating-tips.md).

## <a name="sample-code"></a>Пример кода

Начиная с 31 июля, корпорация Майкрософт опубликовала порты примеров операций ввода-вывода к новому интерфейсу API на `magic-types` ветви в [monotouch-samples](https://github.com/xamarin/monotouch-samples/commits/magic-types).

Для Mac, выполняется проверка примеров в обоих [примеры mac](https://github.com/xamarin/mac-samples) репозитория (отображение новых интерфейсов API в Mavericks или Yosemite), а также примеры 32 или 64-разрядную в ветви magic типы [примеры mac](https://github.com/xamarin/monotouch-samples/commits/magic-types).

## <a name="related-links"></a>Связанные ссылки

- [Обновление приложения для iOS](updating-ios-apps.md)
- [Обновление приложения Mac](updating-mac-apps.md)
- [Обновление приложений Xamarin.Forms](updating-xamarin-forms-apps.md)
- [Обновление привязки](update-binding.md)
- [Обновление советы](updating-tips.md)
- [Классический и отличия Unified API](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
- [Работа с собственными типами в кроссплатформенных приложениях](~/cross-platform/macios/native-types-cross-platform.md)
