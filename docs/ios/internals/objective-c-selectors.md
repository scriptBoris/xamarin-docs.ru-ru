---
title: Селекторы Objective-C в Xamarin.iOS
description: В этом документе рассматриваются способы взаимодействия с селекторы Objective-C из C#. Описываются способы вызова селекторы и технические вопросы, которые необходимо принимать во внимание при этом.
ms.prod: xamarin
ms.assetid: A80904C4-6A89-389B-0487-057AFEB70989
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/12/2017
ms.openlocfilehash: 5d3c8b6bd8f7f788a1de74feddf7fcb378fa5818
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233644"
---
# <a name="objective-c-selectors-in-xamarinios"></a>Селекторы Objective-C в Xamarin.iOS

Основан на языке Objective-C *селекторы*. Селектора — это сообщение, которое может быть отправлено на объект или *класс*. [Xamarin.iOS](~/ios/internals/api-design/index.md) экземпляра селекторы методы экземпляров, карты и селекторы для статических методов класса.

В отличие от обычных функций C (и, как функции-члены C++), невозможно вызвать непосредственно в с помощью селектора [P/Invoke](http://www.mono-project.com/docs/advanced/pinvoke/) вместо этого селекторы отправляются к классу Objective-C или экземпляра с помощью [`objc_msgSend`](https://developer.apple.com/documentation/objectivec/1456712-objc_msgsend)
функция.

Дополнительные сведения о сообщениях в Objective-C, взгляните на Apple [работа с объектами](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithObjects/WorkingwithObjects.html#//apple_ref/doc/uid/TP40011210-CH4-SW2) руководства.

## <a name="example"></a>Пример

Предположим, что нужно для вызова [`sizeWithFont:forWidth:lineBreakMode:`](https://developer.apple.com/documentation/foundation/nsstring/1619914-sizewithfont)
Селектор на [ `NSString` ](https://developer.apple.com/documentation/foundation/nsstring).
Объявление (из документации компании Apple) является:

```objc
- (CGSize)sizeWithFont:(UIFont *)font forWidth:(CGFloat)width lineBreakMode:(UILineBreakMode)lineBreakMode
```

Этот API имеет следующие характеристики:

- Возвращаемый тип — `CGSize` для на единый API.
- `font` Параметр [UIFont](xref:UIKit.UIFont) (и тип, производный от (косвенно) [NSObject](xref:Foundation.NSObject)и сопоставлен с [System.IntPtr](xref:System.IntPtr).
- `width` Параметра `CGFloat`, сопоставляется с `nfloat`.
- `lineBreakMode` Параметра [ `UILineBreakMode` ](https://developer.apple.com/documentation/uikit/uilinebreakmode?language=objc), уже был привязан в Xamarin.iOS как [`UILineBreakMode`](xref:UIKit.UILineBreakMode)
Перечисление.

Окончательная сборка, `objc_msgSend` объявление должно соответствовать:

```csharp
CGSize objc_msgSend(
    IntPtr target, 
    IntPtr selector, 
    IntPtr font, 
    nfloat width, 
    UILineBreakMode mode
);
```

Объявите следующим образом:

```csharp
[DllImport (Constants.ObjectiveCLibrary, EntryPoint="objc_msgSend")]
static extern CGSize cgsize_objc_msgSend_IntPtr_float_int (
    IntPtr target, 
    IntPtr selector,
    IntPtr font,
    nfloat width,
    UILineBreakMode mode
);
```

Чтобы вызвать этот метод, используйте следующий код:

```csharp
NSString target = ...
Selector selector = new Selector ("sizeWithFont:forWidth:lineBreakMode:");
UIFont font = ...
nfloat width = ...
UILineBreakMode mode = ...

CGSize size = cgsize_objc_msgSend_IntPtr_float_int(
    target.Handle, 
    selector.Handle,
    font == null ? IntPtr.Zero : font.Handle,
    width,
    mode
);
```

Возвращаемое значение было структуру, которая была размер меньше, чем 8 байт (более старой версии, такие как `SizeF` используется перед переключением в Unified API-интерфейсы), приведенный выше код запустится в симуляторе, но сбои при выполнении на устройстве. Чтобы вызвать селектора, возвращающее значение меньше, чем 8 битов в размере, объявите `objc_msgSend_stret` функции:

```csharp
[DllImport (MonoTouch.Constants.ObjectiveCLibrary, EntryPoint="objc_msgSend_stret")]
static extern void cgsize_objc_msgSend_stret_IntPtr_float_int (
    out CGSize retval,
    IntPtr target, 
    IntPtr selector,
    IntPtr font,
    nfloat width,
    UILineBreakMode mode
);
```

Чтобы вызвать этот метод, используйте следующий код:

```csharp
NSString      target = ...
Selector    selector = new Selector ("sizeWithFont:forWidth:lineBreakMode:");
UIFont          font = ...
nfloat          width = ...
UILineBreakMode mode = ...

CGSize size;

if (Runtime.Arch == Arch.SIMULATOR)
    size = cgsize_objc_msgSend_IntPtr_float_int(
        target.Handle, 
        selector.Handle,
        font == null ? IntPtr.Zero : font.Handle,
        width,
        mode
    );
else
    cgsize_objc_msgSend_stret_IntPtr_float_int(
        out size,
        target.Handle, selector.Handle,
        font == null ? IntPtr.Zero: font.Handle,
        width,
        mode
    );
```

## <a name="invoking-a-selector"></a>Вызов селектора

Вызов селектора состоит из трех шагов.

1. Получите целевой объект селектора.
2. Получите имя селектора.
3. Вызовите `objc_msgSend` с соответствующими аргументами.

### <a name="selector-targets"></a>Выбор целевых объектов

Селектор целевой объект является экземпляром объекта или класс Objective-C. Если целевой объект является экземпляром и поступили связанный тип Xamarin.iOS, используйте [ `ObjCRuntime.INativeObject.Handle` ](https://developer.xamarin.com/api/property/ObjCRuntime.INativeObject.Handle/) свойство.

Если целевой объект — это класс, используйте [ `ObjCRuntime.Class` ](https://developer.xamarin.com/api/type/ObjCRuntime.Class/) для получения ссылки на экземпляр класса, затем с помощью [ `Class.Handle` ](https://developer.xamarin.com/api/property/ObjCRuntime.Class.Handle/) свойство.

### <a name="selector-names"></a>Селектор имен

Селектор перечислены в документации компании Apple. Например [ `NSString` ](https://developer.apple.com/documentation/foundation/nsstring?language=objc) включает в себя [ `sizeWithFont:` ](https://developer.apple.com/documentation/foundation/nsstring/1619917-sizewithfont?language=objc) и [ `sizeWithFont:forWidth:lineBreakMode:` ](https://developer.apple.com/documentation/foundation/nsstring/1619914-sizewithfont?language=objc) селекторов. Внедренные и конечные двоеточие являются частью имени селектора и не может быть опущено.

Получив имя селектора, можно создать [ `ObjCRuntime.Selector` ](https://developer.xamarin.com/api/type/ObjCRuntime.Selector/) экземпляр для него.

### <a name="calling-objcmsgsend"></a>Вызов objc_msgSend

`objc_msgSend` отправляет сообщение (для выбора) для объекта. Это семейство функций принимает по крайней мере два обязательных аргумента: селектор целевой объект (экземпляр или класс обработки), селектор сам и все аргументы, требуемые для селектора. Экземпляр и селектор аргументы должны быть `System.IntPtr`, и все остальные аргументы должны соответствовать типу ожидает селектор, например `nint` для `int`, или `System.IntPtr` для всех `NSObject`-производные типы. Используйте [`NSObject.Handle`](xref:Foundation.NSObject.Handle)
Чтобы получить `IntPtr` для экземпляра типа Objective-C.

Имеется более одного `objc_msgSend` функции:

- Используйте [ `objc_msgSend_stret` ](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc) для селекторов, возвращающие структуры. На ARM, в том числе все возвращаемые типы, которые не перечисления или любой из встроенных типов C (`char`, `short`, `int`, `long`, `float`, `double`). На x86 (имитатор), этот метод должен использоваться для всех структур, размер которых превышает размер 8 байт (`CGSize` имеет размер 8 байт и не использует `objc_msgSend_stret` в симуляторе). 
- Используйте [ `objc_msgSend_fpret` ](https://developer.apple.com/documentation/objectivec/1456697-objc_msgsend_fpret?language=objc) для выделения, которые возвращают число с плавающей запятой на x86 только. Эта функция не должна использоваться на ARM; Вместо этого используйте `objc_msgSend`. 
- Основной [objc_msgSend](https://developer.apple.com/documentation/objectivec/1456712-objc_msgsend) функция используется для всех других селекторов.

Если вы решили, что `objc_msgSend` , необходимые для вызова функции (симуляторе и на устройствах может потребовать другого метода), можно использовать обычный [ `[DllImport]` ](xref:System.Runtime.InteropServices.DllImportAttribute) метод, чтобы объявить функцию для последующего вызова.

Набор готовых `objc_msgSend` объявления можно найти в `ObjCRuntime.Messaging`.

## <a name="different-invocations-on-simulator-and-device"></a>Различные вызовы в симуляторе и на устройствах

Как было сказано выше, Objective-C имеет три типа из `objc_msgSend` методы: один для обычных вызовов, один для вызовов, которые возвращают значения с плавающей точкой (x86) и один для вызовов, которые возвращают значения структуры. Последний содержит суффикс `_stret` в `ObjCRuntime.Messaging`.

Если вы вызываете метод, который будет возвращать определенные структуры (описанными ниже правилами), необходимо вызвать метод с возвращаемым значением в качестве первого параметра `out` значение:

```csharp
// The following returns a PointF structure:
PointF ret;
Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, this.Handle, selConvertPointFromWindow.Handle, point, window.Handle);
```

Когда следует использовать для правила `_stret_` метод отличается на x86 и ARM.
Привязки для работы в симуляторе и устройство, добавьте следующий код:

```csharp
if (Runtime.Arch == Arch.DEVICE)
{
    PointF ret;
    Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, myHandle, selector.Handle);
    return ret;
} 
else
{
    return Messaging.PointF_objc_msgSend_PointF_IntPtr (myHandle, selector.Handle);
}
```

### <a name="using-the-objcmsgsendstret-method"></a>С помощью метода objc_msgSend_stret

При создании для ARM, используйте [`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc)
для любого типа значения, не перечисления или любой из базовых типов для перечисления (`int`, `byte`, `short`, `long`, `double`, `float`).

При построении для x86 используется [`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc)
для любого типа значения, не перечисления или любой из базовых типов для перечисления (`int`, `byte`, `short`, `long`, `double`, `float`) и собственного, размер которого больше, чем 8 байт.

### <a name="creating-your-own-signatures"></a>Создание собственных подписей

Следующие [gist](https://gist.github.com/rolfbjarne/981b778a99425a6e630c) позволяют создавать собственные подписи при необходимости.

## <a name="related-links"></a>Связанные ссылки

- [Селекторы Objective-C](https://developer.xamarin.com/samples/mac-ios/Objective-C/) образца
