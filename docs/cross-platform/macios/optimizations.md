---
title: Оптимизация сборки
description: В этом документе объясняется и различные средства оптимизации, которые применяются во время сборки для приложений Xamarin.iOS и Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 84B67E31-B217-443D-89E5-CFE1923CB14E
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/16/2018
ms.openlocfilehash: 432511a35f9f285d2c0060b5521256f834bb35ea
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351643"
---
# <a name="build-optimizations"></a>Оптимизация сборки

В этом документе объясняется и различные средства оптимизации, которые применяются во время сборки для приложений Xamarin.iOS и Xamarin.Mac.

## <a name="remove-uiapplicationensureuithread--nsapplicationensureuithread"></a>Удалить UIApplication.EnsureUIThread / NSApplication.EnsureUIThread

Удаляет вызовы [UIApplication.EnsureUIThread] [ 1] (для Xamarin.iOS) или `NSApplication.EnsureUIThread` (для Xamarin.Mac).

Эта оптимизация изменится для кода следующих типов:

```csharp
public virtual void AddChildViewController (UIViewController childController)
{
    global::UIKit.UIApplication.EnsureUIThread ();
    // ...
}
```

в следующий:

```csharp
public virtual void AddChildViewController (UIViewController childController)
{
    // ...
}
```

Эта оптимизация требует компоновщику включена и применяется только к методам с `[BindingImpl (BindingImplOptions.Optimizable)]` атрибута.

По умолчанию, оно включено для выпуска выполняет сборку.

Поведение по умолчанию можно переопределить, передав `--optimize=[+|-]remove-uithread-checks` для mtouch/mmp.

[1]: https://developer.xamarin.com/api/member/UIKit.UIApplication.EnsureUIThread/

## <a name="inline-intptrsize"></a>Встроенный IntPtr.Size

Значение константы внутристрочных элементов из `IntPtr.Size` в соответствии с целевой платформы.

Эта оптимизация изменится для кода следующих типов:

```csharp
if (IntPtr.Size == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

в следующий (при построении для 64-разрядной платформе):

```csharp
if (8 == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

Эта оптимизация требует компоновщику включена и применяется только к методам с `[BindingImpl (BindingImplOptions.Optimizable)]` атрибута.

По умолчанию он включен, если код предназначен для одной архитектуры или для платформы сборки (**Xamarin.iOS.dll**, **Xamarin.TVOS.dll**, **Xamarin.WatchOS.dll** или  **Xamarin.Mac.dll**).

Если планируется использовать несколько архитектур, эта оптимизация будет создавать разные сборки для 32-разрядная и 64-разрядной версии приложения, и будет иметь обе версии должны быть включены в приложение, эффективно увеличение размера готовом приложении не уменьшается его.

Поведение по умолчанию можно переопределить, передав `--optimize=[+|-]inline-intptr-size` для mtouch/mmp.

## <a name="inline-nsobjectisdirectbinding"></a>Встроенный NSObject.IsDirectBinding

`NSObject.IsDirectBinding` является свойством экземпляра, определяющее, является ли конкретный экземпляр типа оболочки или нет (тип оболочки — управляемый тип, который сопоставляется с собственным типом, для экземпляра управляемого `UIKit.UIView` тип сопоставляется собственному `UIView` тип — обратное — это тип пользователя в этом случае `class MyUIView : UIKit.UIView` бы тип пользователя).

Необходимо знать значение `IsDirectBinding` при вызове в Objective-C, так как значение определяет, какая версия `objc_msgSend` для использования.

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

Мы можем определить, что в `UIView.SomeProperty` значение `IsDirectBinding` не является константой и не может быть встроенным:

```csharp
void uiView = new UIView ();
Console.WriteLine (uiView.SomeProperty); /* prints 'true' */
void myView = new MyUIView ();
Console.WriteLine (myView.SomeProperty); // prints 'false'
```

Тем не менее, существует возможность просмотра всех типов в приложении и определить, что отсутствуют типы, наследующие от `NSUrl`, и таким образом можно безопасно подстановку `IsDirectBinding` значение константы `true`:

```csharp
void myURL = new NSUrl ();
Console.WriteLine (myURL.SomeOtherProperty); // prints 'true'
// There's no way to make SomeOtherProperty print anything but 'true', since there are no NSUrl subclasses.
```

В частности, эта оптимизация изменится для кода следующих типов (это программный код для привязки `NSUrl.AbsoluteUrl`):

```csharp
if (IsDirectBinding) {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSend (this.Handle, Selector.GetHandle ("absoluteURL")));
} else {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("absoluteURL")));
}
```

в следующий (когда он может быть определила, что подклассы не `NSUrl` в приложении):

```csharp
if (true) {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSend (this.Handle, Selector.GetHandle ("absoluteURL")));
} else {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("absoluteURL")));
}
```

Эта оптимизация требует компоновщику включена и применяется только к методам с `[BindingImpl (BindingImplOptions.Optimizable)]` атрибута.

Она всегда включена по умолчанию для Xamarin.iOS и этот параметр всегда отключен по умолчанию для Xamarin.Mac (так как это возможно для динамической загрузки сборок в Xamarin.Mac, это не невозможно определить, что никогда не является подклассом определенного класса).

Поведение по умолчанию можно переопределить, передав `--optimize=[+|-]inline-isdirectbinding` для mtouch/mmp.

## <a name="inline-runtimearch"></a>Встроенный Runtime.Arch

Эта оптимизация изменится для кода следующих типов:

```csharp
if (Runtime.Arch == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

в следующий (при создании устройства):

```csharp
if (Arch.DEVICE == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

Эта оптимизация требует компоновщику включена и применяется только к методам с `[BindingImpl (BindingImplOptions.Optimizable)]` атрибута.

Он всегда включен по умолчанию для Xamarin.iOS (она доступна не для Xamarin.Mac).

Поведение по умолчанию можно переопределить, передав `--optimize=[+|-]inline-runtime-arch` для mtouch.

## <a name="dead-code-elimination"></a>Неиспользуемым кодом исключения

Эта оптимизация изменится для кода следующих типов:

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

Он также будет оценен константы сравнений, следующим образом:

```csharp
if (8 == 8) {
    Console.WriteLine ("Doing this");
} else {
    Console.WriteLine ("Not doing this");
}
```

и определить, что выражение `8 == 8` — всегда значение true и уменьшить его, чтобы:

```csharp
Console.WriteLine ("Doing this");
```

Это мощная оптимизации, при совместном использовании встраивания оптимизации, так как его можно преобразовать для кода следующих типов (это программный код для привязки `NFCIso15693ReadMultipleBlocksConfiguration.Range`):

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

в данную коллекцию (при построении для 64-разрядных устройства, а также при также могут обеспечить существуют не `NFCIso15693ReadMultipleBlocksConfiguration` подклассов в приложении):

```csharp
NSRange ret;
ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
return ret;
```

AOT-компилятор уже может исключить неиспользуемого кода следующим образом, но эта оптимизация выполняется внутри компоновщик, это означает, что компоновщик возможность увидеть, что существует несколько методов, которые больше не используются и таким образом могут быть удалены (если не используется в другом месте) :

* `global::ObjCRuntime.Messaging.NSRange_objc_msgSend_stret`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper_stret`

Эта оптимизация требует компоновщику включена и применяется только к методам с `[BindingImpl (BindingImplOptions.Optimizable)]` атрибута.

Он всегда включен по умолчанию (если включен компоновщик).

Поведение по умолчанию можно переопределить, передав `--optimize=[+|-]dead-code-elimination` для mtouch/mmp.

## <a name="optimize-calls-to-blockliteralsetupblock"></a>Оптимизация вызовов BlockLiteral.SetupBlock

Среда выполнения Xamarin.iOS/Mac необходима информация о блок подписи при создании блока Objective-C для управляемого делегата. Это может быть довольно дорогой операции. Эта оптимизация вычисления блок подписи во время сборки и изменяющие IL-код для вызова `SetupBlock` метод, который принимает подпись в качестве аргумента вместо этого. Это позволяет избежать необходимости расчета подписи во время выполнения.

Тесты показывают, что это ускоряет вызов блок с коэффициентом 10 – 15.

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

Эта оптимизация требует компоновщику включена и применяется только к методам с `[BindingImpl (BindingImplOptions.Optimizable)]` атрибута.

Он включается по умолчанию при использовании статических регистратор (в Xamarin.iOS статический регистратор включен по умолчанию для сборок устройств, в Xamarin.Mac, статические регистратор включен по умолчанию для выпуска построений).

Поведение по умолчанию можно переопределить, передав `--optimize=[+|-]blockliteral-setupblock` для mtouch/mmp.

## <a name="optimize-support-for-protocols"></a>Оптимизация поддержка протоколов

Среда выполнения Xamarin.iOS/Mac должна сведения о протоколах реализует Objective-C управляемых типов. Эти сведения хранятся в интерфейсах (и атрибуты для этих интерфейсов), который не очень эффективный формат и не компоновщика с поддержкой.

Одним из примеров является то, что эти интерфейсы хранить сведения о всех членах протокола в `[ProtocolMember]` атрибут, который, помимо прочего содержат ссылки на типы параметров этих членов. Это означает, что просто реализовать такой интерфейс сделает компоновщик сохранить все типы, используемые в этом интерфейсе, даже для необязательных элементов приложения никогда не вызывает или реализует.

Эта оптимизация сделает статический регистратор хранить все необходимые сведения в эффективный формат, который использует небольшой объем памяти, облегчают и ускоряют поиск во время выполнения.

Он также поможет компоновщик, он не обязательно сохранить эти интерфейсы, ни любой из связанных атрибутов.

Эта оптимизация требуется компоновщик и статические регистратора, включения.

На Xamarin.iOS эта оптимизация включена по умолчанию, когда компоновщик и статические регистратор включены.

На Xamarin.Mac эта оптимизация никогда не включена по умолчанию, так как поддерживает Xamarin.Mac, динамической загрузки сборок, эти сборки могут не были известны во время сборки (и таким образом, не оптимизирован).

Поведение по умолчанию можно переопределить, передав `--optimize=-register-protocols` для mtouch/mmp.

## <a name="remove-the-dynamic-registrar"></a>Удаление динамических регистратор

Xamarin.iOS и Xamarin.Mac среда выполнения включают поддержку [регистрации управляемых типов](https://developer.xamarin.com/guides/ios/advanced_topics/registrar/) в среде выполнения Objective-C. Это можно сделать либо во время сборки или во время выполнения (или частично во время сборки, а остальные во время выполнения), но если она полностью выполняется во время сборки, это означает, что вспомогательные код, выполняющий его во время выполнения может быть удален. Это приводит к значительному снижению размера приложения, в частности для небольших приложений, такие как расширения или приложения для watchOS.

Эта оптимизация требуется статический регистратор и компоновщик, требуется включить.

Компоновщик будет пытаться определить, безопасно для удаления динамического регистратора и if, поэтому будет пытаться удалить его.

Поскольку Xamarin.Mac динамически поддерживает загрузку сборок во время выполнения (что не были известны во время сборки), вы не сможете определить во время сборки, является ли этот безопасные оптимизации. Это означает, что эта оптимизация никогда не включено по умолчанию для приложения Xamarin.Mac.

Поведение по умолчанию можно переопределить, передав `--optimize=[+|-]remove-dynamic-registrar` для mtouch/mmp.

Если значение по умолчанию переопределяется для удаления динамического регистратор, компоновщик выдает предупреждения, если он обнаруживает, что не является безопасным (но динамического регистратор удаляются).

## <a name="inline-runtimedynamicregistrationsupported"></a>Встроенный Runtime.DynamicRegistrationSupported

Inlines значение из `Runtime.DynamicRegistrationSupported` определяется во время сборки.

Если удаляется динамического регистратор (см. в разделе [удалить динамического регистратор](#remove-the-dynamic-registrar) оптимизации), это константа, `false` значение, в противном случае это константа `true` значение.

Эта оптимизация изменится для кода следующих типов:

```csharp
if (Runtime.DynamicRegistrationSupported) {
    Console.WriteLine ("do something");
} else {
    throw new Exception ("dynamic registration is not supported");
}
```

в следующий при удалении динамической регистрации:

```csharp
throw new Exception ("dynamic registration is not supported");
```

в следующий, когда динамический регистратор не удаляется:

```csharp
Console.WriteLine ("do something");
```

Эта оптимизация требует компоновщику включена и применяется только к методам с `[BindingImpl (BindingImplOptions.Optimizable)]` атрибута.

Он всегда включен по умолчанию (если включен компоновщик).

Поведение по умолчанию можно переопределить, передав `--optimize=[+|-]inline-dynamic-registration-supported` для mtouch/mmp.

## <a name="precompute-methods-to-create-managed-delegates-for-objective-c-blocks"></a>Предварительное вычисление методов для создания управляемых делегатов для блоков Objective-C

Когда Objective-C вызывает селектора, принимает блок в качестве параметра, и затем в управляемом коде есть переопределении этого метода, Xamarin.iOS и Xamarin.Mac среды выполнения необходимо создать делегат для этого блока.

Код привязки, создаваемый генератором привязки будет включать `[BlockProxy]` атрибут, который указывает тип с `Create` метода, это можно сделать.

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

Генератор будет создавать:

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

При вызове метода Objective-C `[ObjCBlockTester callClassCallback]`, Xamarin.iOS / Xamarin.Mac среды выполнения будут рассмотрены `[BlockProxy (typeof (Trampolines.NIDActionArity1V0))]` атрибута параметра. Он начнет искать `Create` метод для этого типа, а вызывайте этот метод для создания делегата.

Эта оптимизация будет найти `Create` метод во время сборки, а также статические регистратор создаст код, выполняющий поиск метода во время выполнения с помощью маркеров метаданных, вместо использования атрибута и отражения (это выполняется гораздо быстрее, а также позволяет компоновщика Чтобы удалить соответствующий код среды выполнения, делая приложение меньшего размера).

Если не удается найти mmp/mtouch `Create` метод, затем будет показано предупреждение MT4174/MM4174 и поиск будет выполняться во время выполнения.
Наиболее вероятная причина вручную записывается код привязки без требуемого `[BlockProxy]` атрибуты.

Эта оптимизация требуется статический регистратора, включения.

Он всегда включен по умолчанию (при условии, что статические регистратор включен).

Поведение по умолчанию можно переопределить, передав `--optimize=[+|-]static-delegate-to-block-lookup` для mtouch/mmp.
