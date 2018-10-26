---
title: Исключение, маршалинг в Xamarin.iOS
description: В этом документе описывается, как работать с машинного и управляемого исключения в приложении Xamarin.iOS. Здесь рассматриваются проблемы, которые могут возникнуть и решение этих проблем.
ms.prod: xamarin
ms.assetid: BE4EE969-C075-4B9A-8465-E393556D8D90
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/05/2017
ms.openlocfilehash: 167d6ac421bdd2652e7f8474e1ea21bd9040723f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114305"
---
# <a name="exception-marshaling-in-xamarinios"></a>Исключение, маршалинг в Xamarin.iOS

_Xamarin.iOS содержит новые события для реагирования на исключения, особенно в машинном коде._

Управляемый код и Objective-C имеют поддержку исключений среды выполнения (try/catch/finally предложения).

Тем не менее их реализации различаются, то библиотеки времени выполнения (среда выполнения Mono и библиотеки времени выполнения Objective-C) имеют проблемы, если им требуется обрабатывать исключения, а затем запустите код, написанный на других языках.

Здесь содержатся сведения, которые могут возникнуть проблемы и возможные решения.

Он также включает пример проекта, [маршалинг исключений](https://github.com/xamarin/mac-ios-samples/tree/master/ExceptionMarshaling), который может использоваться для тестирования различных сценариев и способы их решения.

## <a name="problem"></a>Проблема

Проблема возникает, если исключение не выдается и во время очистки кадра стека встречается которого не соответствует типу исключения.

Типичный пример того, это для Xamarin.iOS и Xamarin.Mac при собственный API создает исключение Objective-C, а затем это исключение Objective-C нужно каким-либо образом обработать, если процесс освобождения стека достигает управляемый блок кода.

Действие по умолчанию не нужно ничего делать. Для приведенном выше примере это означает, что кадров очистки управляемые среды выполнения Objective-C. Это проблематично, поскольку среда выполнения Objective-C не знает, как выполнять управляемые фреймы; Например выполняться не могут любой `catch` или `finally` предложения в этот кадр.

### <a name="broken-code"></a>Неработающий код

Рассмотрим следующий пример кода:

``` csharp
var dict = new NSMutableDictionary ();
dict.LowLevelSetObject (IntPtr.Zero, IntPtr.Zero); 
```

Это вызовет NSInvalidArgumentException Objective-C в машинном коде:

    NSInvalidArgumentException *** setObjectForKey: key cannot be nil

И трассировка стека будет примерно следующим образом:

    0   CoreFoundation          __exceptionPreprocess + 194
    1   libobjc.A.dylib         objc_exception_throw + 52
    2   CoreFoundation          -[__NSDictionaryM setObject:forKey:] + 1015
    3   libobjc.A.dylib         objc_msgSend + 102
    4   TestApp                 ObjCRuntime.Messaging.void_objc_msgSend_IntPtr_IntPtr (intptr,intptr,intptr,intptr)
    5   TestApp                 Foundation.NSMutableDictionary.LowlevelSetObject (intptr,intptr)
    6   TestApp                 ExceptionMarshaling.Exceptions.ThrowObjectiveCException ()

Кадры 0-3: кадры машинного кода и средство очистки стека, в среде выполнения Objective-C _можно_ очистки кадры. В частности, он будет выполняться Objective-C `@catch` или `@finally` предложения.

Тем не менее, средство очистки стека Objective-C является _не_ может правильно очистки управляемых кадров (кадры 4 – 6), в том, что кадры будет развернут, но об управляемых исключениях она не будет выполняться.

Что означает, что он обычно не является возможность перехватывать эти исключения следующим образом:

```csharp
try {
    var dict = new NSMutableDictionary ();
    dict.LowLevelSetObject (IntPtr.Zero, IntPtr.Zero);
} catch (Exception ex) {
    Console.WriteLine (ex);
} finally {
    Console.WriteLine ("finally");
}
```

Это обусловлено тем, средство очистки стека Objective-C не знает об управляемых `catch` предложение, а также не будет `finally` выполняться предложение.

При выше образец кода _—_ эффективнее, его, так как Objective-C имеет метод уведомления о необработанных исключений Objective-C [`NSSetUncaughtExceptionHandler`][2], который Xamarin.iOS и Xamarin.Mac использовать и в этот момент пытается преобразовать все исключения Objective-C для управляемых исключений.

## <a name="scenarios"></a>Сценарии

### <a name="scenario-1---catching-objective-c-exceptions-with-a-managed-catch-handler"></a>Сценарий 1 - перехват исключения Objective-C с помощью обработчика catch управляемых

В следующем сценарии, имеется возможность перехватывать исключения Objective-C с использованием управляемого `catch` обработчиков:

1. Objective-C создается исключение.
2. Среда выполнения Objective-C обращается к стеку (но не его очистки), поиск в машинном коде `@catch` обработчик, который может обработать исключение.
3. Среда выполнения Objective-C не находит любой `@catch` обработчики, вызовы `NSGetUncaughtExceptionHandler`и вызывает обработчик, устанавливаемая Xamarin.iOS/Xamarin.Mac.
4. Обработчик Xamarin.iOS/Xamarin.Mac's будет преобразовать исключение, Objective-C в управляемое исключение и передать его. С момента Objective-C среды выполнения не раскрутки стека (только рассмотреть его), текущий кадр аналогичен тому, где Objective-C возникло исключение.

Другая проблема возникает здесь, так как среда выполнения Mono не знает, как правильно выполнять кадров Objective-C.

При вызове обратного вызова исключения неперехваченных Objective-C Xamarin.iOS стек — это следующим образом:

     0 libxamarin-debug.dylib   exception_handler(exc=name: "NSInvalidArgumentException" - reason: "*** setObjectForKey: key cannot be nil")
     1 CoreFoundation           __handleUncaughtException + 809
     2 libobjc.A.dylib          _objc_terminate() + 100
     3 libc++abi.dylib          std::__terminate(void (*)()) + 14
     4 libc++abi.dylib          __cxa_throw + 122
     5 libobjc.A.dylib          objc_exception_throw + 337
     6 CoreFoundation           -[__NSDictionaryM setObject:forKey:] + 1015
     7 libxamarin-debug.dylib   xamarin_dyn_objc_msgSend + 102
     8 TestApp                  ObjCRuntime.Messaging.void_objc_msgSend_IntPtr_IntPtr (intptr,intptr,intptr,intptr)
     9 TestApp                  Foundation.NSMutableDictionary.LowlevelSetObject (intptr,intptr) [0x00000]
    10 TestApp                  ExceptionMarshaling.Exceptions.ThrowObjectiveCException () [0x00013]

Здесь только управляемые фреймы являются кадры 8 – 10, но управляемые исключения в кадре 0. Это означает, что среда выполнения Mono должны выполнять очистку кадрах машинного кода 0-7, который вызывает ошибку при эквивалентно проблемы, описанные выше: несмотря на то, что среда выполнения Mono очистит в кадрах машинного кода, он не будет выполняться Objective-C `@catch` или `@finally` предложения .

Пример кода:

```objc
-(id) setObject: (id) object forKey: (id) key
{
    @try {
        if (key == nil)
            [NSException raise: @"NSInvalidArgumentException"];
    } @finally {
        NSLog (@"This will not be executed");
    }
}
```

И `@finally` предложение не будет выполнена, так как среда выполнения Mono, который возвращает этот кадр не знает о нем.

— Это вариант для вызова управляемого исключения в управляемом коде, а также освобождение затем через кадры машинного кода для получения первого управляемого `catch` предложение:

```csharp
class AppDelegate : UIApplicationDelegate {
    public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
    {
        throw new Exception ("An exception");
    }
    static void Main (string [] args)
    {
        try {
            UIApplication.Main (args, null, typeof (AppDelegate));
        } catch (Exception ex) {
            Console.WriteLine ("Managed exception caught.");
        }
    }
}
```

Управляемый `UIApplication:Main` метод будет вызывать собственные `UIApplicationMain` метод, а затем — iOS будет делать массу выполнение машинного кода перед вызовом управляемого `AppDelegate:FinishedLaunching` метод по-прежнему разнообразные кадрах машинного кода в стеке при управляемого исключения Возникло исключение:

     0: TestApp                 ExceptionMarshaling.IOS.AppDelegate:FinishedLaunching (UIKit.UIApplication,Foundation.NSDictionary)
     1: TestApp                 (wrapper runtime-invoke) <Module>:runtime_invoke_bool__this___object_object (object,intptr,intptr,intptr) 
     2: libmonosgen-2.0.dylib   mono_jit_runtime_invoke(method=<unavailable>, obj=<unavailable>, params=<unavailable>, exc=<unavailable>, error=<unavailable>)
     3: libmonosgen-2.0.dylib   do_runtime_invoke(method=<unavailable>, obj=<unavailable>, params=<unavailable>, exc=<unavailable>, error=<unavailable>)
     4: libmonosgen-2.0.dylib   mono_runtime_invoke [inlined] mono_runtime_invoke_checked(method=<unavailable>, obj=<unavailable>, params=<unavailable>, error=0xbff45758)
     5: libmonosgen-2.0.dylib   mono_runtime_invoke(method=<unavailable>, obj=<unavailable>, params=<unavailable>, exc=<unavailable>)
     6: libxamarin-debug.dylib  xamarin_invoke_trampoline(type=<unavailable>, self=<unavailable>, sel="application:didFinishLaunchingWithOptions:", iterator=<unavailable>), context=<unavailable>)
     7: libxamarin-debug.dylib  xamarin_arch_trampoline(state=0xbff45ad4)
     8: libxamarin-debug.dylib  xamarin_i386_common_trampoline
     9: UIKit                   -[UIApplication _handleDelegateCallbacksWithOptions:isSuspended:restoreState:]
    10: UIKit                   -[UIApplication _callInitializationDelegatesForMainScene:transitionContext:]
    11: UIKit                   -[UIApplication _runWithMainScene:transitionContext:completion:]
    12: UIKit                   __84-[UIApplication _handleApplicationActivationWithScene:transitionContext:completion:]_block_invoke.3124
    13: UIKit                   -[UIApplication workspaceDidEndTransaction:]
    14: FrontBoardServices      __37-[FBSWorkspace clientEndTransaction:]_block_invoke_2
    15: FrontBoardServices      __40-[FBSWorkspace _performDelegateCallOut:]_block_invoke
    16: FrontBoardServices      __FBSSERIALQUEUE_IS_CALLING_OUT_TO_A_BLOCK__
    17: FrontBoardServices      -[FBSSerialQueue _performNext]
    18: FrontBoardServices      -[FBSSerialQueue _performNextFromRunLoopSource]
    19: FrontBoardServices      FBSSerialQueueRunLoopSourceHandler
    20: CoreFoundation          __CFRUNLOOP_IS_CALLING_OUT_TO_A_SOURCE0_PERFORM_FUNCTION__
    21: CoreFoundation          __CFRunLoopDoSources0
    22: CoreFoundation          __CFRunLoopRun
    23: CoreFoundation          CFRunLoopRunSpecific
    24: CoreFoundation          CFRunLoopRunInMode
    25: UIKit                   -[UIApplication _run]
    26: UIKit                   UIApplicationMain
    27: TestApp                 (wrapper managed-to-native) UIKit.UIApplication:UIApplicationMain (int,string[],intptr,intptr)
    28: TestApp                 UIKit.UIApplication:Main (string[],intptr,intptr)
    29: TestApp                 UIKit.UIApplication:Main (string[],string,string)
    30: TestApp                 ExceptionMarshaling.IOS.Application:Main (string[])

Кадры 0-1 и 27-30 управляются, хотя их все между ними являются собственными. Если Mono освобождает через эти кадры не Objective-C `@catch` или `@finally` предложений будет выполняться.

### <a name="scenario-2---not-able-to-catch-objective-c-exceptions"></a>Сценарий 2 - не удается перехватывать исключения Objective-C

В следующем сценарии _не_ возможность перехватывать исключения Objective-C с помощью управляемого `catch` обработчиков так, как Objective-C исключение было обработано другим способом:

1. Objective-C создается исключение.
2. Среда выполнения Objective-C обращается к стеку (но не его очистки), поиск в машинном коде `@catch` обработчик, который может обработать исключение.
3. Objective-C среда выполнения находит `@catch` обработчик, освобождает стек и начинается выполнение `@catch` обработчика.

Этот сценарий часто встречаются в приложениях Xamarin.iOS, так как в основном потоке обычно следующий код:

``` objective-c
void UIApplicationMain ()
{
    @try {
        while (true) {
            ExecuteRunLoop ();
        }
    } @catch (NSException *ex) {
        NSLog (@"An unhandled exception occured: %@", exc);
        abort ();
    }
}

```

Это означает, что в основном потоке нет никогда не действительно необработанного исключения Objective-C, и таким образом наши обратный вызов, который преобразует исключения Objective-C в управляемые исключения никогда не вызывается.

Это также довольно часто при отладке приложений Xamarin.Mac в более ранней версии macOS не поддерживает Xamarin.Mac, так как изучение большинство объектов пользовательского интерфейса в отладчике будет пытаться получить свойства, которые соответствуют на селекторы, которые не существуют на выполнение (платформа Поскольку Xamarin.Mac включает поддержку для более поздней версии macOS). Вызов таких селекторы вызовет `NSInvalidArgumentException` («Неизвестный селектор отправлен...»), что в конечном итоге приводит к сбою процесса.

Таким образом, наличие среды выполнения Objective-C или кадры очистки среды выполнения Mono, которые не запрограммированы на дескриптор может привести к неопределенным поведения, такие как сбои, утечки памяти и другие виды поведения неопределенное (mis).

## <a name="solution"></a>Решение

В Xamarin.iOS 10 и Xamarin.Mac 2.10 мы добавили поддержку для перехвата исключений управляемых, так и Objective-C на границу управляемого и машинного кода, а также для преобразования этого исключения к другому типу.

В виде псевдокода он выглядит примерно следующим образом:

``` csharp
[DllImport ("libobjc.dylib")]
static extern void objc_msgSend (IntPtr handle, IntPtr selector);

static void DoSomething (NSObject obj)
{
    objc_msgSend (obj.Handle, Selector.GetHandle ("doSomething"));
}
```

P/Invoke для objc_msgSend перехватывается, и это называется вместо:

``` objective-c
void
xamarin_dyn_objc_msgSend (id obj, SEL sel)
{
    @try {
        objc_msgSend (obj, sel);
    } @catch (NSException *ex) {
        convert_to_and_throw_managed_exception (ex);
    }
}
```

А что-то подобное выполняется обратный случая (маршалинг управляемые исключения в исключения Objective-C).

Перехват исключений на границе управляемого и машинного кода не бесплатную, поэтому он не всегда включен по умолчанию:

- Xamarin.iOS/tvOS: перехват исключения Objective-C включается в симуляторе.
- Xamarin.watchOS: перехват применяется во всех случаях, поскольку позволяя Очистка среде выполнения Objective-C управляемых кадров в заблуждение сборщик мусора и либо сделайте зависание или сбой.
- Xamarin.Mac: перехват исключения Objective-C включается для отладочных сборок.

[Флаги во время сборки](#build_time_flags) разделе объясняется, как включить перехвата, если он не включен по умолчанию.

## <a name="events"></a>События

Два новых события, возникающие, когда исключение перехватывается: `Runtime.MarshalManagedException` и `Runtime.MarshalObjectiveCException`.

Оба события передаются `EventArgs` , содержащий исходное исключение, выданное ( `Exception` свойства) и `ExceptionMode` свойство, чтобы определить способ маршалинга исключение.

`ExceptionMode` Свойство может быть изменено событий обработчик для изменения поведения в соответствии с любой пользовательской обработки, сделать в обработчике. Примером может служить прервать процесс при возникновении определенного исключения.

Изменение `ExceptionMode` свойство применяется к одно событие, он не влияет на все исключения, перехвачена в будущем.

Доступны следующие режимы:

- `Default`: Значение по умолчанию зависит от платформы. Это `ThrowObjectiveCException` в режиме совместной работой (watchOS), если сборщик Мусора и `UnwindNativeCode` в противном случае (iOS и watchOS / macOS). Значение по умолчанию может измениться в будущем.
- `UnwindNativeCode`: Это прежнее поведение (неопределенную). Оно недоступно при использовании сборщик Мусора в режиме совместной работой (который является единственным параметром в watchOS; таким образом, это не является допустимым параметром для watchOS), но он является параметром по умолчанию для всех платформ.
- `ThrowObjectiveCException`: Преобразование управляемого исключения в исключения Objective-C и исключения Objective-C. Это значение по умолчанию в watchOS.
- `Abort`: Прерывает процесс.
- `Disable`: Отключает перехвата исключений, поэтому нет смысла в это значение используется в обработчике событий, но после события это слишком поздно, чтобы отключить его. В любом случае если задано, оно будет вести себя как `UnwindNativeCode`.

Для маршалинга исключения Objective-C в управляемый код, доступны следующие режимы:

- `Default`: Значение по умолчанию зависит от платформы. Это `ThrowManagedException` в режиме совместной работой (watchOS), если сборщик Мусора и `UnwindManagedCode` в противном случае (iOS и tvOS и macOS). Значение по умолчанию может измениться в будущем.
- `UnwindManagedCode`: Это прежнее поведение (неопределенную). Оно недоступно при использовании сборщик Мусора в режиме совместной работой (который является единственным допустимым режим сборки Мусора на watchOS; таким образом это не является допустимым параметром для watchOS), но это значение по умолчанию для всех платформ.
- `ThrowManagedException`: Преобразует исключения Objective-C для управляемого исключения и создает управляемое исключение. Это значение по умолчанию в watchOS.
- `Abort`: Прерывает процесс.
- `Disable`: Отключает перехвата исключений, поэтому нет смысла для задания это значение в случае вызывается обработчик, но после события, уже слишком поздно его отключить. В любом случае если задан, он прервет процесс.

Таким образом Чтобы просмотреть каждый раз при маршалинге исключение, это можно сделать:

``` csharp
Runtime.MarshalManagedException += (object sender, MarshalManagedExceptionEventArgs args) =>
{
    Console.WriteLine ("Marshaling managed exception");
    Console.WriteLine ("    Exception: {0}", args.Exception);
    Console.WriteLine ("    Mode: {0}", args.ExceptionMode);
    
};
Runtime.MarshalObjectiveCException += (object sender, MarshalObjectiveCExceptionEventArgs args) =>
{
    Console.WriteLine ("Marshaling Objective-C exception");
    Console.WriteLine ("    Exception: {0}", args.Exception);
    Console.WriteLine ("    Mode: {0}", args.ExceptionMode);
};
```

<a name="build_time_flags" />

## <a name="build-time-flags"></a>Флаги во время сборки

Можно передать следующие параметры для **mtouch** (для приложений Xamarin.iOS) и **mmp** (для приложений Xamarin.Mac), который определить Включение перехвата исключений и установить действие по умолчанию, должно выполняться:

- `--marshal-managed-exceptions=`
  - `default`
  - `unwindnativecode`
  - `throwobjectivecexception`
  - `abort`
  - `disable`

- `--marshal-objectivec-exceptions=`
  - `default`
  - `unwindmanagedcode`
  - `throwmanagedexception`
  - `abort`
  - `disable`

За исключением `disable`, эти значения идентичны `ExceptionMode` значения, передаваемые `MarshalManagedException` и `MarshalObjectiveCException` события.

`disable` Параметр будет _главным образом_ отключение перехвата, за исключением того, мы по-прежнему будет перехватывать исключения, когда оно не добавляет все затраты на выполнение. В режиме по умолчанию, что режим по умолчанию для выполнения платформы для таких исключений по-прежнему вызываются события маршалинга.

## <a name="limitations"></a>Ограничения

Мы только перехватывать P/Invokes в `objc_msgSend` семейству функций при попытке перехватывать исключения Objective-C. Это означает, что P/Invoke для другой функции C, которая затем создает все исключения Objective-C, все равно будет работать в старых и не определено поведение (это может быть повышена в будущем).

[2]: https://developer.apple.com/reference/foundation/1409609-nssetuncaughtexceptionhandler?language=objc


## <a name="related-links"></a>Связанные ссылки

- [Маршалинг исключений (пример)](https://github.com/xamarin/mac-ios-samples/tree/master/ExceptionMarshaling)
