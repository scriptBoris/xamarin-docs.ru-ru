---
title: Построение оптимизации
description: В этом документе описаны различные оптимизации, которые применяются во время построения для приложения Xamarin.iOS и Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 84B67E31-B217-443D-89E5-CFE1923CB14E
author: bradumbaugh
ms.author: brumbaug
dateupdated: 04/16/2018
ms.openlocfilehash: abdd1223c0105156580b8f23fc2c020f2f45caa6
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2018
ms.locfileid: "33918777"
---
# <a name="build-optimizations"></a>Построение оптимизации

В этом документе описаны различные оптимизации, которые применяются во время построения для приложения Xamarin.iOS и Xamarin.Mac.

## <a name="remove-uiapplicationensureuithread--nsapplicationensureuithread"></a>Удалить UIApplication.EnsureUIThread / NSApplication.EnsureUIThread

Удаляет вызовы [UIApplication.EnsureUIThread] [ 1] (для Xamarin.iOS) или `NSApplication.EnsureUIThread` (для Xamarin.Mac).

Эта оптимизация изменится кода следующего типа:

```csharp
public virtual void AddChildViewController (UIViewController childController)
{
    global::UIKit.UIApplication.EnsureUIThread ();
    // ...
}
```

в следующих:

```csharp
public virtual void AddChildViewController (UIViewController childController)
{
    // ...
}
```

Эта оптимизация требует компоновщику включается и применяется только к методам с `[BindingImpl (BindingImplOptions.Optimizable)]` атрибута.

По умолчанию, оно включено в версии сборки.

Можно переопределить поведение по умолчанию, передав `--optimize=[+|-]remove-uithread-checks` для mtouch/mmp.

[1]: https://developer.xamarin.com/api/member/UIKit.UIApplication.EnsureUIThread/

## <a name="inline-intptrsize"></a>Встроенная IntPtr.Size

Внедряет постоянное значение для `IntPtr.Size` зависимости от целевой платформы.

Эта оптимизация изменится кода следующего типа:

```csharp
if (IntPtr.Size == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

в следующих (при создании решения для 64-разрядной платформе):

```csharp
if (8 == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

Эта оптимизация требует компоновщику включается и применяется только к методам с `[BindingImpl (BindingImplOptions.Optimizable)]` атрибута.

По умолчанию он включен, если для различных версий одной архитектуры или для сборки платформы (**Xamarin.iOS.dll**, **Xamarin.TVOS.dll**, **Xamarin.WatchOS.dll** или  **Xamarin.Mac.dll**).

Если для нескольких архитектур, оптимизация будет создавать разные сборки для 32-разрядная и 64-разрядной версии приложения, и будут у обеих версий для включения в приложения, фактически увеличение размера окончательного приложения не уменьшается его.

Можно переопределить поведение по умолчанию, передав `--optimize=[+|-]inline-intptr-size` для mtouch/mmp.

## <a name="inline-nsobjectisdirectbinding"></a>Встроенная NSObject.IsDirectBinding

`NSObject.IsDirectBinding` является свойством экземпляра, определяющую, является ли конкретный экземпляр типа оболочки (типа оболочки — управляемый тип, который сопоставляется с типом собственного; для экземпляра управляемого `UIKit.UIView` сопоставляется собственный `UIView` тип - обратное — это тип пользователя в этом случае `class MyUIView : UIKit.UIView` бы тип пользователя).

Необходимо знать значение `IsDirectBinding` при вызове Objective-C, так как значение определяет, какая версия `objc_msgSend` для использования.

Имеется только следующий код:

```csharp
class UIView : NSObject {
    public virtual string SomeProperty {
        get {
            if (IsDirectBinding) {
                return "true";
            } else {
                return "false"
            }
        }
    }
}

class NSUrl : NSObject {
    public virtual string SomeOtherProperty {
        get {
            if (IsDirectBinding) {
                return "true";
            } else {
                return "false"
            }
        }
    }
}

class MyUIView : UIView {
}
```

Можно определить, что в `UIView.SomeProperty` значение `IsDirectBinding` не является константой и не может быть встроенным:

```csharp
void uiView = new UIView ();
Console.WriteLine (uiView.SomeProperty); /* prints 'true' */
void myView = new MyUIView ();
Console.WriteLine (myView.SomeProperty); // prints 'false'
```

Тем не менее, существует возможность просмотра всех типов в приложении и определить, что нет типов, наследующие от `NSUrl`, и таким образом можно безопасно встроенного `IsDirectBinding` значение константы `true`:

```csharp
void myURL = new NSUrl ();
Console.WriteLine (myURL.SomeOtherProperty); // prints 'true'
// There's no way to make SomeOtherProperty print anything but 'true', since there are no NSUrl subclasses.
```

В частности, эта оптимизация изменится кода следующего типа (это код для привязки `NSUrl.AbsoluteUrl`):

```csharp
if (IsDirectBinding) {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSend (this.Handle, Selector.GetHandle ("absoluteURL")));
} else {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("absoluteURL")));
}
```

в следующих (при его может быть определила, что подклассы не `NSUrl` в приложении):

```csharp
if (true) {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSend (this.Handle, Selector.GetHandle ("absoluteURL")));
} else {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("absoluteURL")));
}
```

Эта оптимизация требует компоновщику включается и применяется только к методам с `[BindingImpl (BindingImplOptions.Optimizable)]` атрибута.

Он будет всегда включены по умолчанию для Xamarin.iOS и всегда запрещены по умолчанию для Xamarin.Mac (так как можно динамически загружать сборки в Xamarin.Mac, это не невозможно определить, что никогда не является подклассом определенного класса).

Можно переопределить поведение по умолчанию, передав `--optimize=[+|-]inline-isdirectbinding` для mtouch/mmp.

## <a name="inline-runtimearch"></a>Встроенная Runtime.Arch

Эта оптимизация изменится кода следующего типа:

```csharp
if (Runtime.Arch == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

в следующих (при создании решения для устройства):

```csharp
if (Arch.DEVICE == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

Эта оптимизация требует компоновщику включается и применяется только к методам с `[BindingImpl (BindingImplOptions.Optimizable)]` атрибута.

Он всегда включен по умолчанию для Xamarin.iOS (она доступна не для Xamarin.Mac).

Можно переопределить поведение по умолчанию, передав `--optimize=[+|-]inline-runtime-arch` для mtouch.

## <a name="dead-code-elimination"></a>Невызываемый код исключения

Эта оптимизация изменится кода следующего типа:

```csharp
if (true) {
    Console.WriteLine ("Doing this");
} else {
    Console.WriteLine ("Not doing this");
}
```

в:

```csharp
Console.WriteLine ("Doing this");
```

Он также будет вычислять сравнения с константой, следующим образом:

```csharp
if (8 == 8) {
    Console.WriteLine ("Doing this");
} else {
    Console.WriteLine ("Not doing this");
}
```

и определить, что выражение `8 == 8` — всегда true и уменьшить его:

```csharp
Console.WriteLine ("Doing this");
```

Это мощный оптимизации, при использовании вместе с встраивания оптимизации, так как он мог преобразовывать кода следующего типа (это код для привязки `NFCIso15693ReadMultipleBlocksConfiguration.Range`):

```csharp
NSRange ret;
if (IsDirectBinding) {
    if (Runtime.Arch == Arch.DEVICE) {
        if (IntPtr.Size == 8) {
            ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
        } else {
            global::ObjCRuntime.Messaging.NSRange_objc_msgSend_stret (out ret, this.Handle, Selector.GetHandle ("range"));
        }
    } else if (IntPtr.Size == 8) {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
    } else {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
    }
} else {
    if (Runtime.Arch == Arch.DEVICE) {
        if (IntPtr.Size == 8) {
            ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("range"));
        } else {
            global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper_stret (out ret, this.SuperHandle, Selector.GetHandle ("range"));
        }
    } else if (IntPtr.Size == 8) {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("range"));
    } else {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("range"));
    }
}
return ret;
```

в этот (при построении для 64-разрядных устройства, а также при убедитесь, существует также возможность не `NFCIso15693ReadMultipleBlocksConfiguration` подклассов в приложении):

```csharp
NSRange ret;
ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
return ret;
```

Компилятор AOT уже может во избежание Невызываемый код следующим образом, но оптимизация выполняется внутри компоновщик, это означает, что компоновщик может видеть, что существует несколько методов, которые больше не используются и таким образом могут быть удалены (если не используется в другом месте) :

* `global::ObjCRuntime.Messaging.NSRange_objc_msgSend_stret`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper_stret`

Эта оптимизация требует компоновщику включается и применяется только к методам с `[BindingImpl (BindingImplOptions.Optimizable)]` атрибута.

Он всегда включены по умолчанию (если включена компоновщик).

Можно переопределить поведение по умолчанию, передав `--optimize=[+|-]dead-code-elimination` для mtouch/mmp.

## <a name="optimize-calls-to-blockliteralsetupblock"></a>Оптимизация вызовы BlockLiteral.SetupBlock

Среда выполнения Xamarin.iOS/Mac должно быть известно блок подписи при создании блока Objective-C для управляемого делегата. Это может быть довольно дорогой операции. Эта оптимизация вычисления блок подписи во время построения и изменить IL для вызова `SetupBlock` метод, который принимает в качестве аргумента подпись вместо. Это исключает необходимость для расчета подписи во время выполнения.

Тесты показывают, что при этом скорость вызова блок с коэффициентом 10-15.

Он преобразует следующие [кода](https://github.com/xamarin/xamarin-macios/blob/018f7153441d9d7e0f58e2046f39eeb46f1ff480/src/UIKit/UIAccessibility.cs#L198-L211):

```csharp
public static void RequestGuidedAccessSession (bool enable, Action<bool> completionHandler)
{
    // ...
    block_handler.SetupBlock (callback, completionHandler);
    // ...
}
```

в:

```csharp
public static void RequestGuidedAccessSession (bool enable, Action<bool> completionHandler)
{
    // ...
    block_handler.SetupBlockImpl (callback, completionHandler, true, "v@?B");
    // ...
}
```

Эта оптимизация требует компоновщику включается и применяется только к методам с `[BindingImpl (BindingImplOptions.Optimizable)]` атрибута.

Оно включено по умолчанию при использовании статического регистратора (в Xamarin.iOS статических регистратор включен по умолчанию для сборок устройств, в статических регистратора включена по умолчанию для выпуска Xamarin.Mac построений).

Можно переопределить поведение по умолчанию, передав `--optimize=[+|-]blockliteral-setupblock` для mtouch/mmp.

## <a name="optimize-support-for-protocols"></a>Оптимизация поддержка протоколов

Среда выполнения Xamarin.iOS/Mac должна сведения о протоколах реализует Objective-C управляемых типов. Эти сведения хранятся в интерфейсах (и атрибуты этих интерфейсов), который не очень эффективно формат и поддерживается не понятного имени компоновщика.

Одним из примеров является то, что эти интерфейсы хранения сведений обо всех членах протокола в `[ProtocolMember]` атрибут, который среди прочего содержат ссылки на те элементы, типы параметров. Это означает, что просто реализации такой интерфейс сделает компоновщик сохранения всех типов, используемых в этом интерфейсе, даже для дополнительных членов приложения никогда не вызывает или реализует.

Эта оптимизация сделает статических регистратора хранить все необходимые сведения в эффективный формат, который использует мало памяти, которые облегчают и ускоряют поиск во время выполнения.

Он будет также учат компоновщик, он не обязательно сохранить эти интерфейсы, ни любой из связанных атрибутов.

Эта оптимизация требует компоновщика и статических регистратора, должны быть включены.

На Xamarin.iOS оптимизация включена по умолчанию при включении компоновщика и статических регистратора.

На Xamarin.Mac оптимизация никогда не включена по умолчанию, поскольку поддерживает Xamarin.Mac динамически загрузки сборок и эти сборки могут не были известны во время построения (и таким образом, которая не оптимизирована).

Можно переопределить поведение по умолчанию, передав `--optimize=-register-protocols` для mtouch/mmp.

## <a name="remove-the-dynamic-registrar"></a>Удаление динамических регистратора

Xamarin.iOS и среда выполнения Xamarin.Mac включают поддержку [регистрации управляемых типов](https://developer.xamarin.com/guides/ios/advanced_topics/registrar/) для Objective-C среды выполнения. Это можно сделать либо во время построения или во время выполнения (или частично во время построения и rest во время выполнения), но если оно полностью выполняется во время построения, это означает, что можно удалить вспомогательные код, выполняющий его во время выполнения. Это приводит к значительному снижению размер приложения, в частности для небольших приложений, таких как расширения или watchOS приложений.

Эта оптимизация требуется статический регистратора и компоновщик, должно быть включено.

Компоновщик будет пытаться определить, безопасно удалить динамического регистратора и так будет пытаться удалить его.

Поскольку Xamarin.Mac динамически поддерживает загрузку сборок во время выполнения (что не были известны во время построения), невозможно определить во время построения ли это безопасно оптимизации. Это означает, что оптимизация никогда не включена по умолчанию для Xamarin.Mac приложений.

Можно переопределить поведение по умолчанию, передав `--optimize=[+|-]remove-dynamic-registrar` для mtouch/mmp.

Если значение по умолчанию переопределяется для удаления динамического регистратора, компоновщик будет выдавать предупреждения, если обнаруживает, что не является безопасным (но динамического регистратора удаляются).

## <a name="inline-runtimedynamicregistrationsupported"></a>Встроенная Runtime.DynamicRegistrationSupported

Внедряет значение из `Runtime.DynamicRegistrationSupported` определяется во время построения.

При удалении динамического регистратора (см. [удаления динамического регистратора](#remove-the-dynamic-registrar) оптимизации), это константа `false` значений, в противном случае оно является константой `true` значение.

Эта оптимизация изменится кода следующего типа:

```csharp
if (Runtime.DynamicRegistrationSupported) {
    Console.WriteLine ("do something");
} else {
    throw new Exception ("dynamic registration is not supported");
}
```

в следующих при удалении динамической регистрации:

```csharp
throw new Exception ("dynamic registration is not supported");
```

в следующих при динамической регистратора удаляется:

```csharp
Console.WriteLine ("do something");
```

Эта оптимизация требует компоновщику включается и применяется только к методам с `[BindingImpl (BindingImplOptions.Optimizable)]` атрибута.

Он всегда включены по умолчанию (если включена компоновщик).

Можно переопределить поведение по умолчанию, передав `--optimize=[+|-]inline-dynamic-registration-supported` для mtouch/mmp.

## <a name="precompute-methods-to-create-managed-delegates-for-objective-c-blocks"></a>Предварительное вычисление методы для создания управляемых делегатов для блоков Objective-c.

Когда Objective-C вызывает селектора, принимает блок в качестве параметра, и затем управляемый код имеет переопределении этого метода, Xamarin.iOS / Xamarin.Mac среда выполнения должна создать делегат для этого блока.

Код привязки, порожденное генератором привязки будет включать `[BlockProxy]` атрибут, который указывает тип с `Create` метода, это можно сделать.

Рассмотрим следующий код Objective-C:

```objc
@interface ObjCBlockTester : NSObject {
}
-(void) classCallback: (void (^)())completionHandler;
-(void) callClassCallback;
@end

@implementation ObjCBlockTester
-(void) classCallback: (void (^)())completionHandler
{
}

-(void) callClassCallback
{
    [self classCallback: ^()
    {
        NSLog (@"called!");
    }];
}
@end
```

и следующий связующий код:

```csharp
[BaseType (typeof (NSObject))]
interface ObjCBlockTester
{
    [Export ("classCallback:")]
    void ClassCallback (Action completionHandler);
}
```

Генератор выдаст:

```csharp
[Register("ObjCBlockTester", true)]
public unsafe partial class ObjCBlockTester : NSObject {
    // unrelated code...

    [Export ("callClassCallback")]
    [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
    public virtual void CallClassCallback ()
    {
        if (IsDirectBinding) {
            ApiDefinition.Messaging.void_objc_msgSend (this.Handle, Selector.GetHandle ("callClassCallback"));
        } else {
            ApiDefinition.Messaging.void_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("callClassCallback"));
        }
    }
    
    [Export ("classCallback:")]
    [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
    public unsafe virtual void ClassCallback ([BlockProxy (typeof (Trampolines.NIDActionArity1V0))] System.Action completionHandler)
    {
        // ...
        
    }
}

static class Trampolines
{
    [UnmanagedFunctionPointerAttribute (CallingConvention.Cdecl)]
    [UserDelegateType (typeof (System.Action))]
    internal delegate void DActionArity1V0 (IntPtr block);

    static internal class SDActionArity1V0 {
        static internal readonly DActionArity1V0 Handler = Invoke;
        
        [MonoPInvokeCallback (typeof (DActionArity1V0))]
        static unsafe void Invoke (IntPtr block) {
            var descriptor = (BlockLiteral *) block;
            var del = (System.Action) (descriptor->Target);
            if (del != null)
                del (obj);
        }
    }
    
    internal class NIDActionArity1V0 {
        IntPtr blockPtr;
        DActionArity1V0 invoker;
        
        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        public unsafe NIDActionArity1V0 (BlockLiteral *block)
        {
            blockPtr = _Block_copy ((IntPtr) block);
            invoker = block->GetDelegateForBlock<DActionArity1V0> ();
        }
        
        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        ~NIDActionArity1V0 ()
        {
            _Block_release (blockPtr);
        }
        
        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        public unsafe static System.Action Create (IntPtr block)
        {
            if (block == IntPtr.Zero)
                return null;
            if (BlockLiteral.IsManagedBlock (block)) {
                var existing_delegate = ((BlockLiteral *) block)->Target as System.Action;
                if (existing_delegate != null)
                    return existing_delegate;
            }
            return new NIDActionArity1V0 ((BlockLiteral *) block).Invoke;
        }
        
        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        unsafe void Invoke ()
        {
            invoker (blockPtr);
        }
    }
}
```

При вызове метода Objective-C `[ObjCBlockTester callClassCallback]`, Xamarin.iOS / Xamarin.Mac среды выполнения будут рассмотрены `[BlockProxy (typeof (Trampolines.NIDActionArity1V0))]` атрибут параметра. Затем он ищет `Create` метод для этого типа и вызывайте этот метод для создания делегата.

Эта оптимизация найдет `Create` метод во время построения и статических регистратора создаст код, выполняющий поиск метода во время выполнения с помощью лексемы метаданных, вместо использования атрибута и отражения (это гораздо меньше времени, а также позволяет компоновщику для удаления соответствующего кода среды выполнения, делая приложение меньшего размера).

Если не удается найти mmp/mtouch `Create` метода, затем будет показано предупреждение MT4174/MM4174 и уточняющие запросы будут выполняться во время выполнения.
Наиболее вероятная причина вручную записывается код привязки без требуемого `[BlockProxy]` атрибуты.

Эта оптимизация требуется статический регистратор, чтобы включить.

Он всегда включен по умолчанию (при условии, что статические регистратора включена).

Можно переопределить поведение по умолчанию, передав `--optimize=[+|-]static-delegate-to-block-lookup` для mtouch/mmp.
