---
title: Внедрение .NET рекомендации для Objective-C
description: В этом документе описываются различные рекомендации по использованию внедрение .NET с Objective-C. В нем описывается предоставление подмножество управляемого кода, предоставляя chunkier API, именования и многое другое.
ms.prod: xamarin
ms.assetid: 63C7F5D2-8933-4D4A-8348-E9CBDA45C472
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 33138b7858b8bc04a5be30f9fad1709e916f5575
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105408"
---
# <a name="net-embedding-best-practices-for-objective-c"></a>Внедрение .NET рекомендации для Objective-C

Это черновик и может не быть синхронизированной с функциями в настоящее время поддерживается с помощью средства. Мы надеемся, что в этом документе будет отдельно развивать и в конечном счете соответствовать окончательный средство, т. е. Мы предложим вам специализацию долгосрочной перспективе оптимальных способов — не немедленного решения.

Большая часть этого документа также применяется для прочих поддерживаемых языков. Тем не менее все входящие относятся, например в C# и Objective-C.

## <a name="exposing-a-subset-of-the-managed-code"></a>Предоставление подмножество управляемого кода

Созданный собственный библиотеки и платформы содержит код Objective-C для вызова каждого из управляемых интерфейсов API, предоставляемый. Дополнительные API, вы surface (сделать общедоступными) затем большего размера собственного _связующий_ станет библиотеки.

Возможно, следует создать сборку отличные, меньшего размера, предоставлять только необходимые API для собственного разработчика. Этот интерфейс также позволит вам больший контроль над видимостью, именования, произошла ошибка при проверке... сформированного кода.

## <a name="exposing-a-chunkier-api"></a>Предоставление доступа к chunkier API

Нет приходится платить переход из машинного, управляемого (и назад). Таким образом, лучше предоставлять _громоздкие вместо отправки множественных_ API, чтобы разработчики исходных приложений, например

**Неаккуратные**

```csharp
public class Person {
  public string FirstName { get; set; }
  public string LastName { get; set; }
}
```

```objc
// this requires 3 calls / transitions to initialize the instance
Person *p = [[Person alloc] init];
p.firstName = @"Sebastien";
p.lastName = @"Pouliot";
```

**Громоздкие**

```csharp
public class Person {
  public Person (string firstName, string lastName) {}
}
```

```objc
// a single call / transition will perform better
Person *p = [[Person alloc] initWithFirstName:@"Sebastien" lastName:@"Pouliot"];
```

Поскольку меньше количество переходов, производительность может быть лучше. Оно также требует меньше кода, создаваемом, в результате получится также меньшего размера собственной библиотеки.

## <a name="naming"></a>Именование

Именования объектов является одним из двух самых сложных проблем в компьютерной науки, остальные, кэш ошибки недействительности и отключить на 1. Будем надеяться, что внедрение .NET можно позволит предотвратить все, кроме именования.

### <a name="types"></a>Типы

Objective-C не поддерживает пространства имен. Как правило, его типы должны иметь префикс 2 (для Apple) или 3 (для сторонних производителей) символ префикса, например `UIView` для представления в UIKit, который обозначает платформы.

Для типов .NET пропуск пространство имен не поддерживается как может привести к появлению повторяющихся или запутанными, имена. В результате существующие типы .NET очень много, например

```csharp
namespace Xamarin.Xml.Configuration {
  public class Reader {}
}
```

будет использоваться следующим:

```objc
id reader = [[Xamarin_Xml_Configuration_Reader alloc] init];
```

Тем не менее можно повторно предоставить к типу, что:

```csharp
public class XAMXmlConfigReader : Xamarin.Xml.Configuration.Reader {}
```

что делает более понятной Objective-C для использования, например:

```objc
id reader = [[XAMXmlConfigReader alloc] init];
```

### <a name="methods"></a>Методы

Возможно, даже подходящих имен .NET идеально подходит для Objective-C API.

Соглашения об именовании в Objective-C, отличаются от .NET (верблюжьем вместо Pascal, в более подробный).
См. в статье [кодирования, касающиеся Cocoa](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingMethods.html#//apple_ref/doc/uid/20001282-BCIGIJJF).

Разработчик Objective-C точки зрения, метод с `Get` префикс подразумевает вы не являетесь владельцем экземпляра, т. е. [получение правила](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-SW1).

Это правило именования не имеет соответствия в мире сборщик Мусора .NET; метод .NET с `Create` префикса будет идентично в .NET. Тем не менее, для разработчиков Objective-C, она обычно означает, что вы владеете возвращаемого экземпляра, т. е. [создать правило](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029).

## <a name="exceptions"></a>Исключения

Это довольно часто в .NET, чтобы использовать исключения широко для сообщения об ошибках. Однако они медленным и довольно неидентичны в Objective-C. По возможности их следует скрыть от разработчика Objective-C.

Например, .NET `Try` модель будет гораздо удобнее использовать из кода Objective-C:

```csharp
public int Parse (string number)
{
  return Int32.Parse (number);
}
```

Сравнение

```csharp
public bool TryParse (string number, out int value)
{
  return Int32.TryParse (number, out value);
}
```

### <a name="exceptions-inside-init"></a>Исключения внутри `init*`

В .NET конструктор должен либо завершиться успешно и вернуть (_надеюсь_) допустимый экземпляр или создает исключение.

Напротив, позволяет Objective-C `init*` для возврата `nil` когда экземпляр не может быть создан. Это распространенные, но не общие, шаблон, используемый во многих платформ Apple. В некоторых других случаях `assert` может произойти (и завершить текущий процесс).

Генератор следуйте той же самой `return nil` шаблон для созданных `init*` методы. Если управляемое исключение возникает, то он будет напечатан (с помощью `NSLog`) и `nil` будет возвращаться вызывающему объекту.

## <a name="operators"></a>Операторы

Objective-C не поддерживает операторы можно перегружать как C# выполняет, поэтому они преобразуются в класс селекторов.

[«Понятное»](https://docs.microsoft.com/dotnet/standard/design-guidelines/operator-overloads) предпочтение перегрузки операторов, создаются именованные методы когда найден и может привести к более удобную для использования API.

Классы, которые переопределяют операторы `==` и (или) `!=` должны переопределять также стандартный метод Equals (объект).
