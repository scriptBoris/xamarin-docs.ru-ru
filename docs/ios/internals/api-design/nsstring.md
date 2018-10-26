---
title: NSString в Xamarin.iOS и Xamarin.Mac
description: В этом документе описывается, как Xamarin.iOS прозрачно преобразует объекты NSString C# строковыми объектами, в том случае, если этого не происходит.
ms.prod: xamarin
ms.assetid: 785744B3-42E2-4590-8F41-435325E609B9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: cc9e3f992642f3cdc3d16fe6f829b6a6c06b50fc
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115039"
---
# <a name="nsstring-in-xamarinios-and-xamarinmac"></a>NSString в Xamarin.iOS и Xamarin.Mac

Вызывает проекта Xamarin.iOS и Xamarin.Mac для использования API для предоставления собственного строкового типа .NET, `string`, для обработки в C# и других языках, программирования .NET и предоставления строки как тип данных, а не в API  `NSString` тип данных.

Это означает, что разработчикам не следует хранить строки, которые предназначены для вызова Xamarin.iOS и Xamarin.Mac API (Unified) в отдельном типе (`Foundation.NSString`), можно продолжать использовать Mono `System.String` для всех операций и в любое время API в Xamarin.iOS и Xamarin.Mac требует строку, берет на себя сведения о маршалинге наши API привязки.

Например, свойство «text» Objective-C на `UILabel` типа `NSString`, объявляется следующим образом:

```objc
@property(nonatomic, copy) NSString *text
```

Это представляется в Xamarin.iOS как:

```csharp
class UILabel {
    public string Text { get; set; }
}
```

На самом деле реализация этого свойства маршалирует C# строку в `NSString` и вызывает `objc_msgSend` метод таким же образом, который бы Objective-C.

Существует множество сторонних Objective-C API, которые не используют `NSString`, но вместо этого использовать строка C ("*char*«). В таких случаях можно по-прежнему использовать C# строковый тип данных, но необходимо использовать [[PlainString]](~/cross-platform/macios/binding/objective-c-libraries.md) атрибут для информирования генератора привязки, эта строка не должна быть маршалирован как `NSString`, но вместо этого, как строка C.

 <a name="Exceptions_to_the_Rule" />

## <a name="exceptions-to-the-rule"></a>Исключения для правила

В Xamarin.iOS и Xamarin.Mac внесено исключения этого правила. Решение между когда мы предоставляем `string`s, и если мы указали except и предоставлять `NSString`s, выполняется в том случае, если `NSString` метод выполнять сравнение указателей вместо содержимого сравнения.

Это может произойти, когда API-интерфейсы Objective-C использует общедоступный `NSString` константы как токен, представляющий какое-либо действие вместо сравнения фактическое содержимое строки.

В таком случае `NSString`  API-интерфейсы доступны, и существуют нечасто, интерфейсы API, это. Также можно заметить, что свойства NSString предоставляются в некоторых классах. Те `NSString` свойства предоставляются для элементов, как уведомления. Те, свойства обычно выглядят так:

```csharp
class Foo {
     public NSString FooNotification { get; }
}
```
Уведомления являются ключами, используемыми для `NSNotification` класса, если вы хотите зарегистрировать для определенного события начинается передача средой выполнения.

Ключи обычно выглядеть следующим образом:

```csharp
class Foo {
     public NSString FooBarKey { get; }
}
```

Другое место где `NSString`s, представлены в API-Интерфейс является как маркеры, которые используются в качестве параметров для некоторых интерфейсов API в iOS или OS X, которые принимают `NSDictionary` объектов в качестве параметров. Обычно в словаре `NSString` ключи. Xamarin.iOS, по соглашению имена тех статический `NSString` свойства путем добавления имени «Ключ».
