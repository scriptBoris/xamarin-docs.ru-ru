---
title: Селекторы Objective-C в Xamarin.iOS
description: В этом документе рассматриваются способы взаимодействия с селекторы Objective-C из C#. Описываются способы вызова селекторы и технические вопросы, которые необходимо принимать во внимание при этом.
ms.prod: xamarin
ms.assetid: A80904C4-6A89-389B-0487-057AFEB70989
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/12/2017
ms.openlocfilehash: 3083770fd2874eca317585b6bf949f3efe56f879
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351734"
---
# <a name="objective-c-selectors-in-xamarinios"></a>Селекторы Objective-C в Xamarin.iOS

Основан на языке Objective-C *селекторы*. Селектора — это сообщение, которое может быть отправлено на объект или *класс*. [Xamarin.iOS](~/ios/internals/api-design/index.md) экземпляра селекторы методы экземпляров, карты и селекторы для статических методов класса.

В отличие от обычных функций C (и, как функции-члены C++), невозможно вызвать непосредственно в с помощью селектора [P/Invoke](http://www.mono-project.com/docs/advanced/pinvoke/).
(*Выделить*: Теоретически можно использовать P/Invoke для невиртуальных функций-членов C++, но потребовалось бы беспокоиться о искажением имени-компилятор, это множество трудностей лучше игнорируется.) Вместо этого селекторы отправляются к классу Objective-C или экземпляра с помощью [ `objc_msgSend` функция](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend).

Возможно, вам [данного полезные руководства на Objective-C обмена сообщениями](http://developer.apple.com/iphone/library/documentation/cocoa/conceptual/ObjCRuntimeGuide/Articles/ocrtHowMessagingWorks.html) полезно.

<a name="Example" />

## <a name="example"></a>Пример

Предположим, что вы хотите вызвать [-[NSString sizeWithFont:forWidth:lineBreakMode:]](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html#//apple_ref/occ/instm/NSString/sizeWithFont:forWidth:lineBreakMode:) селектор.
Объявление (из документации компании Apple) является:

```csharp
- (CGSize)sizeWithFont:(UIFont *)font forWidth:(CGFloat)width lineBreakMode:(UILineBreakMode)lineBreakMode
```

-  Возвращаемый тип — *CGSize* для на единый API.
-  *Шрифта* параметр [UIFont](https://developer.xamarin.com/api/type/UIKit.UIFont/) (и тип, производный от (косвенно) [NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/) ) и, таким образом сопоставляется [System.IntPtr](xref:System.IntPtr) .
-  *Ширины* параметра *CGFloat* , сопоставляется с *nfloat*.
-  *LineBreakMode* параметра *UILineBreakMode* , уже был привязан в Xamarin.iOS как [UILineBreakMode перечисления](https://developer.xamarin.com/api/type/UIKit.UILineBreakMode/) .


Всего вместе, и мы хотим, чтобы объявление objc_msgSend, которое соответствует:

```csharp
CGSize objc_msgSend(IntPtr target, IntPtr selector,
    IntPtr font, nfloat width, UILineBreakMode mode);
```

Будет необходимо объявить его:

```csharp
[DllImport (Constants.ObjectiveCLibrary, EntryPoint="objc_msgSend")]
static extern CGSize cgsize_objc_msgSend_IntPtr_float_int (
    IntPtr target, IntPtr selector,
    IntPtr font,
    nfloat width,
    UILineBreakMode mode);
```

После объявления, мы вызываем Получив соответствующие параметры:

```csharp
NSString      target = ...
Selector    selector = new Selector ("sizeWithFont:forWidth:lineBreakMode:");
UIFont          font = ...
nfloat          width = ...
UILineBreakMode mode = ...

CGSize size = cgsize_objc_msgSend_IntPtr_float_int(
    target.Handle, selector.Handle,
    font == null ? IntPtr.Zero : font.Handle,
    width,
    mode);
```

Возвращаемое значение было структуру, которая была размер меньше, чем 8 байт (более старой версии, такие как `SizeF` используется перед переключением в Unified API-интерфейсы) приведенный выше код запустится в симуляторе, но сбои при выполнении на устройстве. Таким образом, для вызова селектора, возвращающее значение меньше, чем 8 битов в размере, поэтому мы *также* необходимо объявить `objc_msgSend_stret()` функции:

```csharp
[DllImport (MonoTouch.Constants.ObjectiveCLibrary, EntryPoint="objc_msgSend_stret")]
static extern void cgsize_objc_msgSend_stret_IntPtr_float_int (
    out CGSize retval,
    IntPtr target, IntPtr selector,
    IntPtr font,
    nfloat width,
    UILineBreakMode mode);
```

Затем станет вызова:

```csharp
NSString      target = ...
Selector    selector = new Selector ("sizeWithFont:forWidth:lineBreakMode:");
UIFont          font = ...
nfloat          width = ...
UILineBreakMode mode = ...

CGSize size;

if (Runtime.Arch == Arch.SIMULATOR)
    size = cgsize_objc_msgSend_IntPtr_float_int(
        target.Handle, selector.Handle,
        font == null ? IntPtr.Zero : font.Handle,
        width,
        mode);
else
    cgsize_objc_msgSend_stret_IntPtr_float_int(
        out size,
        target.Handle, selector.Handle,
        font == null ? IntPtr.Zero: font.Handle,
        width,
        mode);
```


<a name="Invoking_a_Selector" />

## <a name="invoking-a-selector"></a>Вызов селектора

Вызов селектора состоит из трех шагов.

1.  Получите целевой объект селектора.
1.  Получите имя селектора.
1.  Вызов objc_msgSend() с соответствующими аргументами.


<a name="Selector_Targets" />

### <a name="selector-targets"></a>Выбор целевых объектов

Селектор целевой объект является экземпляром объекта или класс Objective-C. Если целевой объект является экземпляром и поступили связанный тип Xamarin.iOS, используйте [ObjCRuntime.INativeObject.Handle](https://developer.xamarin.com/api/property/ObjCRuntime.INativeObject.Handle/) свойство.

Если целевой объект — это класс, используйте [ObjCRuntime.Class](https://developer.xamarin.com/api/type/ObjCRuntime.Class/) для получения ссылки на экземпляр класса, затем с помощью [Class.Handle](https://developer.xamarin.com/api/property/ObjCRuntime.Class.Handle/) свойство.


<a name="Selector_Names" />

### <a name="selector-names"></a>Селектор имен

Селектор перечислены в документации компании Apple. Например [методы расширения UIKit NSString](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html) включают [sizeWithFont:](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html#//apple_ref/occ/instm/NSString/sizeWithFont:) и [sizeWithFont:forWidth:lineBreakMode:](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html#//apple_ref/occ/instm/NSString/sizeWithFont:forWidth:lineBreakMode:). Внедренные и конечные двоеточия важны и являются частью имени селектора.

Получив имя селектора, можно создать [ObjCRuntime.Selector](https://developer.xamarin.com/api/type/ObjCRuntime.Selector/) экземпляр для него.


<a name="Calling_objc_msgSend()" />

### <a name="calling-objcmsgsend"></a>Вызов objc_msgSend()

 `objc_msgSend()` используется для отправки сообщения (для выбора) для объекта. Это семейство функций принимает по крайней мере два обязательных аргумента: селектор целевой объект (экземпляр или класс обработки), селектор самого и затем аргументы, необходимые для конкретного селектора. Экземпляр и селектор аргументы должны быть `System.IntPtr`, и все остальные аргументы должны соответствовать типу ожидает селектор, например `nint` для `int`, или `System.IntPtr` для всех `NSObject`-производные типы. Используйте [NSObject.Handle](https://developer.xamarin.com/api/property/Foundation.NSObject.Handle/) свойство для получения `IntPtr` для экземпляра типа Objective-C.

К сожалению, нет более одного `objc_msgSend()` функции.

Используйте [ `objc_msgSend_stret()` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_stret) для выделения, которые возвращают структуру.
Чтобы не усложнять «понравилось», на ARM это включает в себя все возвращаемые типы, которые являются *не* перечисления или какой-либо тип builtin C (char, short, int, long, с плавающей запятой). На x86 (имитатор) должна использоваться для всех структур, размер которых превышает размер 8 байт. (CGSize--используется в примере выше — именно это 8 байт и таким образом не использует `objc_msgSend_stret()` в симуляторе.)

Используйте [ `objc_msgSend_fpret()` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_fpret) для выделения, которые возвращают число с плавающей запятой на x86 только. Эта функция не должна использоваться на ARM; Вместо этого используйте `objc_msgSend()`.

Основной [objc_msgSend()](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend) функция используется для всех других селекторов.

Если вы решили, что `objc_msgSend()` функции, необходимые для вызова (и более чем один, например для симулятора и устройства), можно использовать обычный [ `[DllImport]` ](xref:System.Runtime.InteropServices.DllImportAttribute) метод, чтобы объявить функцию для последующего вызова.

Набор готовых `objc_msgSend()` объявления можно найти в [ `ObjCRuntime.Messaging` ](https://developer.xamarin.com/api/type/ObjCRuntime.Messaging/).


<a name="ugly" />

## <a name="the-ugly"></a>Обзор

Objective-C имеет три типа из `objc_msgSend` методы: один для обычных вызовов, один для вызовов, которые возвращают значения с плавающей точкой (x86) и один для вызовов, которые возвращают значения структуры. Последний содержит суффикс `_stret` в `ObjCRuntime.Messaging`.

Если вы вызываете метод, который будет возвращать определенных структур (правил, описанных ниже), необходимо вызвать метод с возвращаемым значением как первый параметр out значению, следующим образом:

```csharp
// The following returns a PointF structure:
PointF ret;
Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, this.Handle, selConvertPointFromWindow.Handle, point, window.Handle);
```

Вещи отвратительно здесь и потому что правило должно использования _stret_ отличается на X86 и ARM, если вы хотите привязок для работы в симуляторе и устройств, необходимо добавить код, который выглядит следующим образом:

```csharp
if (Runtime.Arch == Arch.DEVICE){
    PointF ret;

    Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, myHandle, selector.Handle);

    return ret;
} else
    return Messaging.PointF_objc_msgSend_PointF_IntPtr (myHandle, selector.Handle);
```

### <a name="using-the-objcmsgsendstret-method"></a>С помощью objc\_msgSend\_stret-метод

Когда следует использовать для правила [ `objc_msgSend_stret` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_stret) похожи на это для **ARM**:

-  Любое значение, тип которого не перечисления или любой из базовых типов для перечисления (int, byte, short, long, double, float).


Когда следует использовать для правила [ `objc_msgSend_stret` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_stret) похожи на это для **X86**:

-  Любое значение, тип которого не перечисление, или любой из базовых типов для перечисления (int, byte, short, long, double, float) и собственного, размер которого больше, чем 8 байт.


### <a name="creating-your-own-signatures"></a>Создание собственных подписи.

Следующие [gist](https://gist.github.com/rolfbjarne/981b778a99425a6e630c) позволяют создавать собственные подписи при необходимости.



## <a name="related-links"></a>Связанные ссылки

- [Пример селекторы](https://developer.xamarin.com/samples/mac-ios/Objective-C/Selectors/)
