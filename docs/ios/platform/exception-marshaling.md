---
title: Маршалинг в Xamarin.iOS исключение
description: В этом документе описывается работа с машинного и управляемого исключения в приложении Xamarin.iOS. В нем описывается возникают проблемы и решения этих проблем.
ms.prod: xamarin
ms.assetid: BE4EE969-C075-4B9A-8465-E393556D8D90
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/05/2017
ms.openlocfilehash: dcf1074aacb6d139d107dac01fa86f459831d5f9
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34786747"
---
# <a name="exception-marshaling-in-xamarinios"></a>Маршалинг в Xamarin.iOS исключение

_Xamarin.iOS содержит новые события для реагирования на исключения, особенно в машинном коде._

Управляемый код и Objective-C была добавлена поддержка исключений среды выполнения (try/catch/finally-предложения).

Однако реализаций различаются, которой означает, что библиотеки времени выполнения (Mono среды выполнения и библиотек времени выполнения C цель) проблем при наличии у них для обработки исключений, а затем запустите код, написанный на других языках.

В этом документе описаны проблемы, которые могут возникнуть, а также возможные решения.

Он также включает пример проекта, [маршалинг исключение](https://github.com/xamarin/mac-ios-samples/tree/master/ExceptionMarshaling), которого можно использовать для проверки различных сценариев и способы их решения.

## <a name="problem"></a>Проблема

Проблема возникает, если исключение вызывается и обнаружена во время очистки кадра стека, не соответствующие тип исключения.

Типичным примером это Xamarin.iOS или Xamarin.Mac при собственный API приводит к возникновению исключения Objective-C, а затем это исключение Objective-C нужно каким-либо образом обработать, если процесс освобождения стека достигает управляемого кадра.

Действие по умолчанию — не выполнять никаких действий. В предыдущем примере это означает, позволяя кадры очистки управляемых Objective-C времени выполнения. Это представляет собой проблему, поскольку среда выполнения C цель не знает, как выполнять управляемые фреймы; Например она не будет выполняться любые `catch` или `finally` предложений в этом кадре.

### <a name="broken-code"></a>Неработающий код

Рассмотрим следующий пример кода:

``` csharp
var dict = new NSMutableDictionary ();
dict.LowLevelSetObject (IntPtr.Zero, IntPtr.Zero); 
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

Фрагменты кода 0-3 не кадры машинного кода и очистки стека во время выполнения Objective-C _можно_ очистки кадры. В частности, он будет выполняться Objective-C `@catch` или `@finally` предложения.

Тем не менее, средство очистки стека Objective-C — _не_ может правильно очистки управляемого кадров (кадры 4 – 6), в том, что кадры будет очищен, но управляемого исключения она не будет выполняться.

Это означает, что он обычно не позволяет перехватывать эти исключения следующим образом:

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

Это так, как средство очистки стека Objective-C не знает об управляемых `catch` предложение, а также не будет `finally` выполняться предложения.

При выше образец кода _—_ эффективнее, его, так как Objective-C имеет метод уведомления о необработанных исключений Objective-C [`NSSetUncaughtExceptionHandler`][2], который Xamarin.iOS и Xamarin.Mac использовать и в этот момент пытается преобразовать все исключения Objective-C для управляемых исключений.

## <a name="scenarios"></a>Сценарии

### <a name="scenario-1---catching-objective-c-exceptions-with-a-managed-catch-handler"></a>Сценарий 1 - перехват исключений Objective-C с управляемых catch

В следующем сценарии, существует возможность перехвата исключений Objective-C с помощью управляемых `catch` обработчиков:

1. Objective-C создается исключение.
2. Среда выполнения C цель выполняет обход стека (но не его очистки), поиск собственный `@catch` обработчик, который может обработать исключение.
3. Среда выполнения C цель не находит любой `@catch` обработчики, вызывает `NSGetUncaughtExceptionHandler`и вызывает обработчик, установленные Xamarin.iOS/Xamarin.Mac.
4. Обработчик Xamarin.iOS/Xamarin.Mac's преобразовать исключение Objective-C в управляемое исключение и создают его. С момента Objective-C (только рассмотреть его), среда выполнения не Очищать стек, текущий кадр является тем же, где Objective-C возникло исключение.

Другой причиной проблемы здесь Mono среды выполнения не знает, как для очистки Objective-C кадры должным образом.

При вызове обратного вызова исключений неперехваченных Objective-C Xamarin.iOS стек — следующим образом:

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

Здесь — это только управляемые фреймы кадры 8-10, но управляемого исключения в кадре 0. Это означает, что Mono среды выполнения должны выполнять очистку стековых фреймов машинного кода 0-7, чего эквивалентное проблемы, описанные выше проблемы: несмотря на то, что моно среда выполнения будет развернуть стековых фреймов машинного кода, он не будет выполняться Objective-C `@catch` или `@finally` предложения .

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

И `@finally` предложение не будет выполнен, поскольку Mono среды выполнения, которые возвращает этот кадр не знает о нем.

В варианте такого подхода является вызова управляемого исключения в управляемом коде и затем очистки через кадры машинного кода для получения первого управляемого `catch` предложения:

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

Управляемый `UIApplication:Main` вызывает собственный метод `UIApplicationMain` метода, а затем — iOS будет выполнять много выполнение машинного кода перед вызовом управляемого `AppDelegate:FinishedLaunching` метод со множеством по-прежнему кадры машинного кода в стеке при управляемого исключения исключение:

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

Кадры 0-1 и 27-30 управляются, пока все порты в от являются собственными. Если моно освобождает через эти кадры Objective C `@catch` или `@finally` предложения будет выполняться.

### <a name="scenario-2---not-able-to-catch-objective-c-exceptions"></a>Сценарий 2 - не удается для перехвата исключений Objective-C

В следующем сценарии это _не_ возможность перехвата исключений Objective-C с помощью управляемого `catch` обработчики так, как было обработано исключение Objective-C другим способом:

1. Objective-C создается исключение.
2. Среда выполнения C цель выполняет обход стека (но не его очистки), поиск собственный `@catch` обработчик, который может обработать исключение.
3. Objective-C среда выполнения находит `@catch` обработчик, освобождает стек и начинает выполнение `@catch` обработчика.

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

Это означает, что в основном потоке нет никогда не действительно необрабатываемое исключение Objective-C, и таким образом нашей обратный вызов, который преобразует Objective-C исключения в управляемых исключений никогда не вызывается.

Кроме того, это довольно часто при отладке приложений Xamarin.Mac на более ранней версии macOS, чем Xamarin.Mac поддерживается, так как проверка большинство объектов пользовательского интерфейса в отладчике попытается извлечь соответствующие свойства на селекторы, которые не существуют на выполнение (платформы Поскольку Xamarin.Mac включает поддержку для более поздней версии macOS). Вызов таких селекторы вызовет `NSInvalidArgumentException` («Неизвестный селектор отправляется...»), что в конечном итоге приводит к сбою процесса.

Таким образом, наличие среды выполнения Objective-C или кадры Mono среды выполнения очистки, которые не запрограммированы на дескриптор может привести к неопределенным поведения, такие как сбои, утечки памяти и другие виды поведения неопределенное (системам).

## <a name="solution"></a>Решение

В Xamarin.iOS 10 и Xamarin.Mac 2.10 мы добавили поддержку для перехвата исключений управляемый и Objective-C на границу управляемого и машинного кода, а также для преобразования в другой тип это исключение.

На псевдокоде выглядит примерно следующим образом:

``` csharp
[DllImport ("libobjc.dylib")]
static extern void objc_msgSend (IntPtr handle, IntPtr selector);

static void DoSomething (NSObject obj)
{
    objc_msgSend (obj.Handle, Selector.GetHandle ("doSomething"));
}
```

P/Invoke для objc_msgSend перехватывается, и этот процесс называется:

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

И примерно так, как выполняется в случае обратного (маршалинга управляемых исключений для исключений Objective-C).

Перехват исключений на границе управляемого и машинного кода не бесплатную, поэтому он не всегда включен по умолчанию:

- Xamarin.iOS/tvOS: перехват исключений Objective-C включается в симуляторе.
- Xamarin.watchOS: перехват применяется во всех случаях, так как позволить Очистка среды выполнения C цель управляемых кадров в заблуждение сборщик мусора и либо сделайте зависания или сбоя.
- Xamarin.Mac: перехват исключений Objective-C включен для отладочных сборок.

[Флаги во время построения](#build_time_flags) разделе описывается включение перехвата, когда она не включена по умолчанию.

## <a name="events"></a>События

Два новых события, возникающие, когда исключение перехватывается: `Runtime.MarshalManagedException` и `Runtime.MarshalObjectiveCException`.

Оба события передаются `EventArgs` , содержащий исходное исключение, выданное ( `Exception` свойства) и `ExceptionMode` свойство, чтобы определить, каким образом следует маршалировать исключение.

`ExceptionMode` Свойство можно изменить в событии обработчика, чтобы изменить поведение в соответствии с любая Настраиваемая обработка выполняется в обработчике. Примером может служить прервать процесс при возникновении определенного исключения.

Изменение `ExceptionMode` свойство применяется к одно событие, не влияет на все исключения перехватываются в будущем.

Доступны следующие режимы:

- `Default`: Значение по умолчанию зависит от платформы. Это `ThrowObjectiveCException` в режиме совместной (watchOS), если сервер глобального Каталога и `UnwindNativeCode` в противном случае (iOS и watchOS и macOS). Значение по умолчанию может измениться в будущем.
- `UnwindNativeCode`: Это прежнее поведение (неопределенную). Оно недоступно при использовании сборки Мусора в режиме совместной (который является единственным вариантом на watchOS, таким образом, это не является допустимым значением параметра на watchOS), но это параметр по умолчанию для всех платформ.
- `ThrowObjectiveCException`: Преобразования управляемого исключения в Objective-C исключения и исключение Objective-C. Это значение по умолчанию на watchOS.
- `Abort`: К прерыванию процесса.
- `Disable`: Отключает перехвата исключений, чтобы не имеет смысла для настройки этого значения в обработчике событий, но после события это слишком поздно, чтобы отключить ее. В любом случае если задано, он ведет себя как `UnwindNativeCode`.

Для маршалинга исключения Objective-C для управляемого кода, доступны следующие режимы:

- `Default`: Значение по умолчанию зависит от платформы. Это `ThrowManagedException` в режиме совместной (watchOS), если сервер глобального Каталога и `UnwindManagedCode` в противном случае (iOS и tvOS и macOS). Значение по умолчанию может измениться в будущем.
- `UnwindManagedCode`: Это прежнее поведение (неопределенную). Оно недоступно при использовании сборки Мусора в режиме совместной (который является единственным допустимым режим сборки Мусора на watchOS; таким образом это не является допустимым значением параметра на watchOS), но это значение по умолчанию для всех платформ.
- `ThrowManagedException`: Преобразование исключения Objective-C управляемого исключения и вызова управляемого исключения. Это значение по умолчанию на watchOS.
- `Abort`: К прерыванию процесса.
- `Disable`: Отключает перехват исключения, не имеет смысла для задания этого значения в случае, когда обработчик, но после это событие возникает, он слишком поздно отключить ее. В любом случае если задано, оно приведет к прерыванию процесса.

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

Можно передать следующие параметры для **mtouch** (для приложений Xamarin.iOS) и **mmp** (для Xamarin.Mac приложений), которой определить Включение перехвата исключений, а задать действие по умолчанию должно выполняться:

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

`disable` Параметр будет _основном_ отключение перехвата, за исключением того, мы по-прежнему будет перехватывать исключения, при любой нагрузка не добавляются. В режиме по умолчанию выполняется режимом по умолчанию для выполнения платформы для этих исключений по-прежнему возникают события, маршалинга.

## <a name="limitations"></a>Ограничения

Мы перехватывать только P/Invokes в `objc_msgSend` семейства функций при попытке перехвата исключений Objective-C. Это означает, что P/Invoke для другой функции C, которая затем создает все исключения Objective-C, по-прежнему будут выполняться в старых и не определено поведение (это может быть улучшена в будущем).

[2]: https://developer.apple.com/reference/foundation/1409609-nssetuncaughtexceptionhandler?language=objc


## <a name="related-links"></a>Связанные ссылки

- [Маршалинг исключение (пример)](https://github.com/xamarin/mac-ios-samples/tree/master/ExceptionMarshaling)
