---
title: Поддержка Objective-C
description: Этот документ содержит описание поддержки для Objective-C в внедрение .NET. В нем описывается автоматический подсчет ссылок, NSString, протоколы, NSObject протокола, исключения и многое другое.
ms.prod: xamarin
ms.assetid: 3367A4A4-EC88-4B75-96D0-51B1FCBCE614
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 48caa70cf2bd408f8afc673b400f7d5a4369e108
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110730"
---
# <a name="objective-c-support"></a>Поддержка Objective-C

## <a name="specific-features"></a>Отдельные функции

Создание Objective-C имеет некоторые специальные возможности, которые стоит обратить внимание.

### <a name="automatic-reference-counting"></a>Подсчет автоматических ссылок

Использование из автоматического подсчета ссылок (ДУГА) является **требуется** для вызова созданных привязки. Проект, используя библиотеку основе внедрение .NET должен быть скомпилирован с `-fobjc-arc`.

### <a name="nsstring-support"></a>Поддержка NSString

API-интерфейсы, которые предоставляют `System.String` типы преобразуются в `NSString`. Это делает управление памятью проще, чем при работе с `char*`.

### <a name="protocols-support"></a>Поддержка протоколов

Управляемые интерфейсы преобразуются в протоколы Objective-C, где все члены являются `@required`.

### <a name="nsobject-protocol-support"></a>Поддержка протокола NSObject

По умолчанию по умолчанию хеширования и равенства .NET и среде выполнения Objective-c. предполагается, что быть взаимозаменяемыми, так как они имеют похожую семантику.

Когда управляемый тип переопределяет `Equals(Object)` или `GetHashCode`, обычно это значит, что поведение по умолчанию (.NET) не было достаточно; это означает, что по умолчанию Objective-C, вероятно, не достаточно либо.

В таком случае переопределяет генератор [ `isEqual:` ](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418795-isequal?language=objc) метод и [ `hash` ](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418859-hash?language=objc) свойство, определенное в [ `NSObject` протокола](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc). Это позволяет пользовательские управляемые реализации для использования из кода Objective-C прозрачно.

### <a name="exceptions-support"></a>Поддержка исключений

Передача `--nativeexception` как аргумент `objcgen` преобразует управляемые исключения в исключения Objective-C, которые могут быть перехвачено и обработано. 

### <a name="comparison"></a>Оператор

Управляемые типы, реализующие `IComparable` (или его универсальная версия `IComparable<T>`) создаст Objective-C понятное методы, возвращающие `NSComparisonResult` и примите `nil` аргумент. Это делает более понятное для разработчиков Objective-C сгенерированном API. Пример:

```objc
- (NSComparisonResult)compare:(XAMComparableType * _Nullable)other;
```

### <a name="categories"></a>Категории

Управляемые расширения методов преобразуются на категории. Например, следующие методы расширения в `Collection`:

```csharp
public static class SomeExtensions {
    public static int CountNonNull (this Collection collection) { ... }
    public static int CountNull (this Collection collection) { ... }
}
```

создать категорию Objective-C такого рода:

```objc
@interface Collection (SomeExtensions)

- (int)countNonNull;
- (int)countNull;

@end
```

Если один управляемый тип расширяет несколько типов, создается несколько категорий Objective-C.

### <a name="subscripting"></a>Индексация ;

Управляемые индексированного свойства преобразуются в перегрузке объекта. Пример:

```csharp
public bool this[int index] {
    get { return c[index]; }
    set { c[index] = value; }
}
```

создать Objective-C аналогичную:

```objc
- (id)objectAtIndexedSubscript:(int)idx;
- (void)setObject:(id)obj atIndexedSubscript:(int)idx;
```

который можно использовать с помощью синтаксиса подписи пропущена Objective-C:

```objc
if ([intCollection [0] isEqual:@42])
    intCollection[0] = @13;
```

В зависимости от типа индексатор индексация индексированных или с ключом будет создаваться, где это необходимо.

Это [статье](http://nshipster.com/object-subscripting/) является отличным введением индексация.

## <a name="main-differences-with-net"></a>Основные различия с помощью .NET

### <a name="constructors-vs-initializers"></a>Инициализаторы конструкторов vs

В Objective-C, можно вызывать любые инициализатора прототипы любого из родительских классов в цепочке наследования, если оно не помечено как недоступный (`NS_UNAVAILABLE`).

В C# необходимо явно объявить конструктор члена внутри класса, которое означает, что конструкторы не наследуются.

В правом представления C# API для Objective-C, `NS_UNAVAILABLE` добавляется к любой инициализатор, отсутствующий в дочернем классе, от родительского класса.

C#API:

```csharp
public class Unique {
    public Unique () : this (1)
    {
    }

    public Unique (int id)
    {
    }
}

public class SuperUnique : Unique {
    public SuperUnique () : base (911)
    {
    }
}
```

Objective-C отображена API:

```objc
@interface SuperUnique : Unique

- (instancetype)initWithId:(int)id NS_UNAVAILABLE;
- (instancetype)init;

@end
```

Здесь `initWithId:` была помечена как недоступная.

### <a name="operator"></a>Оператор

Objective-C не поддерживает как перегрузка операторов C# так, чтобы операторы преобразуются в селекторы класса:

```csharp
public static AllOperators operator + (AllOperators c1, AllOperators c2)
{
    return new AllOperators (c1.Value + c2.Value);
}
```

в

```objc
+ (instancetype)add:(Overloads_AllOperators *)anObjectC1 c2:(Overloads_AllOperators *)anObjectC2;
```

Тем не менее, некоторые языки .NET не поддерживают перегрузку операторов, поэтому обычно включают [«понятное»](https://docs.microsoft.com/dotnet/standard/design-guidelines/operator-overloads) с именем метода, в дополнение к перегруженного оператора.

Если оператор версии и версии «понятное» обнаружены, будет создаваться только понятную версию, так как они получат одно и то же имя Objective-C.

```csharp
public static AllOperatorsWithFriendly operator + (AllOperatorsWithFriendly c1, AllOperatorsWithFriendly c2)
{
    return new AllOperatorsWithFriendly (c1.Value + c2.Value);
}

public static AllOperatorsWithFriendly Add (AllOperatorsWithFriendly c1, AllOperatorsWithFriendly c2)
{
    return new AllOperatorsWithFriendly (c1.Value + c2.Value);
}
```

принимает следующий вид:

```objc
+ (instancetype)add:(Overloads_AllOperatorsWithFriendly *)anObjectC1 c2:(Overloads_AllOperatorsWithFriendly *)anObjectC2;
```

### <a name="equality-operator"></a>Оператор равенства

В операторе Общие `==` в C# обрабатывается как общие оператор как было сказано выше.

Тем не менее если оператор равенства «понятное» найден, и оператором `==` и оператор `!=` будут пропущены в поколении.

### <a name="datetime-vs-nsdate"></a>DateTime vs NSDate

Из [ `NSDate` ](https://developer.apple.com/reference/foundation/nsdate?language=objc) документации:

> `NSDate` объекты инкапсулировать единой точки во времени, независимым от конкретного calendrical системы или часового пояса. Объекты даты являются неизменяемыми, представляющий интервал времени инвариантного относительно дату абсолютная ссылка (00: 00:00 UTC 1 января 2001 года).

Из-за `NSDate` ссылаются на дату, все преобразования между ней и `DateTime` должны выполняться в формате UTC.

#### <a name="datetime-to-nsdate"></a>Дата и время NSDate

При преобразовании из `DateTime` для `NSDate`, `Kind` свойство `DateTime` учитывается:

|Тип|Результаты|
|---|---|
|`Utc`|Преобразование выполняется с использованием предоставленного `DateTime` как.|
|`Local`|Результат вызова метода `ToUniversalTime()` в предоставленном `DateTime` объект используется для преобразования.|
|`Unspecified`|Предоставленный `DateTime` предполагается, что объект является UTC, поэтому такое же поведение при `Kind` является `Utc`.|

Преобразование использует следующую формулу:

```
TimeInterval = DateTimeObjectTicks - NSDateReferenceDateTicks / TicksPerSecond
```

В этой формуле: 

- `NSDateReferenceDateTicks` вычисляется на основе `NSDate` ссылаются на дату в формате UTC 00:00:00 1 января 2001 года: 
    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```
- [`TicksPerSecond`](https://docs.microsoft.com/dotnet/api/system.timespan.tickspersecond) определен в [`TimeSpan`](https://docs.microsoft.com/dotnet/api/system.timespan)

Чтобы создать `NSDate` объекта, `TimeInterval` используется с `NSDate` [dateWithTimeIntervalSinceReferenceDate:](https://developer.apple.com/reference/foundation/nsdate/1591577-datewithtimeintervalsincereferen?language=objc) селектор.

#### <a name="nsdate-to-datetime"></a>NSDate к типу DateTime

Преобразование из `NSDate` для `DateTime` использует следующую формулу:

```
DateTimeTicks = NSDateTimeIntervalSinceReferenceDate * TicksPerSecond + NSDateReferenceDateTicks
```

В этой формуле: 

- `NSDateReferenceDateTicks` вычисляется на основе `NSDate` ссылаются на дату в формате UTC 00:00:00 1 января 2001 года: 
    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```
- [`TicksPerSecond`](https://docs.microsoft.com/dotnet/api/system.timespan.tickspersecond) определен в [`TimeSpan`](https://docs.microsoft.com/dotnet/api/system.timespan)

После вычисления `DateTimeTicks`, `DateTime` [конструктор](https://docs.microsoft.com/dotnet/api/system.datetime.-ctor?#System_DateTime__ctor_System_Int64_System_DateTimeKind_) вызывается, установка его `kind` для `DateTimeKind.Utc`.

> [!NOTE]
> `NSDate` может быть `nil`, но `DateTime` – это структура, в .NET, которая по определению не может быть `null`. Если вы предоставите `nil` `NSDate`, он преобразуется в значение по умолчанию `DateTime` значение, которое сопоставляется `DateTime.MinValue`.

`NSDate` поддерживает до более поздней версии и меньше минимального значения `DateTime`. При преобразовании из `NSDate` для `DateTime`, эти значения выше и ниже заменяются `DateTime` [MaxValue](https://docs.microsoft.com/dotnet/api/system.datetime.maxvalue) или [MinValue](https://docs.microsoft.com/dotnet/api/system.datetime.minvalue), соответственно.
