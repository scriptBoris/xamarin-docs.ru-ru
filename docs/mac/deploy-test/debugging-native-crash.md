---
title: Отладка сбоя в машинном коде в приложении Xamarin.Mac
description: Этот документ описывает отладку исключений, возникающих в среде выполнения Objective-C. В нем рассматриваются сбои утверждений, проблемы с обратными вызовами, восходящая маршрутизация исключений и многое другое.
ms.prod: xamarin
ms.assetid: B0C0CE31-2737-4969-8EA5-D39D3333E9C2
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 10/19/2016
ms.openlocfilehash: 6cf19efcd2fa0f9d74034214bfa6a4e60ce0f9e2
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103885"
---
# <a name="debugging-a-native-crash-in-a-xamarinmac-app"></a>Отладка сбоя в машинном коде в приложении Xamarin.Mac

## <a name="overview"></a>Обзор

Иногда ошибки программирования приводят к сбоям в машинной среде выполнения Objective-C. В отличие от исключений C#, они не дают информации о конкретной строке в коде, которую можно быстро исправить. Иногда отладка таких ошибок не требует особых усилий, но в некоторых случаях их очень сложно отследить. 

Давайте рассмотрим несколько примеров реальных сбоев в машинном коде.

## <a name="example-1-assertion-failure"></a>Пример 1. Сбой утверждения

Здесь вы видите несколько первых строк из описания сбоя, возникающего в простом тестовом приложении (эту информацию вы можете увидеть на панели **выходных данных приложения**):

```csharp
2014-10-15 16:18:02.364 NSOutlineViewHottness[79111:1304993] *** Assertion failure in -[NSTableView _uncachedRectHeightOfRow:], /SourceCache/AppKit/AppKit-1343.13/TableView.subproj/NSTableView.m:1855
2014-10-15 16:18:02.364 NSOutlineViewHottness[79111:1304993] NSTableView variable rowHeight error: The value must be > 0 for row 0, but the delegate <NSOutlineViewHottness_HotnessViewDelegate: 0xaa01860> gave -1.000.
2014-10-15 16:18:02.378 NSOutlineViewHottness[79111:1304993] *** Assertion failure in -[NSTableView _uncachedRectHeightOfRow:], /SourceCache/AppKit/AppKit-1343.13/TableView.subproj/NSTableView.m:1855
2014-10-15 16:18:02.378 NSOutlineViewHottness[79111:1304993] NSTableView variable rowHeight error: The value must be > 0 for row 0, but the delegate <NSOutlineViewHottness_HotnessViewDelegate: 0xaa01860> gave -1.000.
2014-10-15 16:18:02.381 NSOutlineViewHottness[79111:1304993] (
    0   CoreFoundation                      0x91888343 __raiseError + 195
    1   libobjc.A.dylib                     0x9a5e6a2a objc_exception_throw + 276
    2   CoreFoundation                      0x918881ca +[NSException raise:format:arguments:] + 138
    3   Foundation                          0x950742b1 -[NSAssertionHandler handleFailureInMethod:object:file:lineNumber:description:] + 118
    4   AppKit                              0x975db476 -[NSTableView _uncachedRectHeightOfRow:] + 373
    5   AppKit                              0x975db2f8 -[_NSTableRowHeightStorage _uncachedRectHeightOfRow:] + 143
    6   AppKit                              0x975db206 -[_NSTableRowHeightStorage _cacheRowHeights] + 167
    7   AppKit                              0x975db130 -[_NSTableRowHeightStorage _createRowHeightsArray] + 226
    8   AppKit                              0x975b5851 -[_NSTableRowHeightStorage _ensureRowHeights] + 73
    9   AppKit                              0x975b5790 -[_NSTableRowHeightStorage computeTableHeightForNumberOfRows:] + 89
    10  AppKit                              0x975b4c38 -[NSTableView _totalHeightOfTableView] + 220
```

Строки с номерами перед ними — это трассировка машинного стека. Она позволяет понять, что сбой произошел где-то внутри `NSTableView` при обработке высоты строк. После этого обработчик `NSAssertionHandler` создал исключение `NSException (objc_exception_throw)` и мы получили сбой утверждения:

```csharp
rowHeight error: The value must be > 0 for row 0, but the delegate
<NSOutlineView_ViewDelegate: 0xaa01860> gave -1.000
```

Из этой информации можно быстро понять, что какой-то из методов `NSOutlineViewDelegate` возвращает отрицательное число. В нашем примере источник проблемы заключается здесь:

```csharp
public override nfloat GetRowHeight (NSTableView tableView, nint row)
{
    return -1;
}
```

## <a name="example-2-callback-jumped-into-middle-of-nowhere"></a>Пример 2. Обратный вызов переходит в несуществующее место

```csharp
Stacktrace:

 at <unknown> <0xffffffff>
 at (wrapper managed-to-native) MonoMac.AppKit.NSApplication.NSApplicationMain (int,string[]) <IL 0x000a4, 0xffffffff>
 at MonoMac.AppKit.NSApplication.Main (string[]) [0x00041] in /Users/donblas/Programming/xamcore-master/src/AppKit/NSApplication.cs:107
 at NSOutlineViewHottness.MainClass.Main (string[]) [0x00007] in /Users/donblas/Programming/Local/NSOutlineViewHottness/NSOutlineViewHottness/Main.cs:14
 at (wrapper runtime-invoke) <Module>.runtime_invoke_void_object (object,intptr,intptr,intptr) <IL 0x00050, 0xffffffff>

Native stacktrace:

Debug info from gdb:

(lldb) command source -s 0 '/tmp/mono-gdb-commands.qrHllW'
Executing commands in '/private/tmp/mono-gdb-commands.qrHllW'.
(lldb) process attach --pid 79229
Process 79229 stopped
Executable module set to "/Users/donblas/Programming/Local/NSOutlineViewHottness/NSOutlineViewHottness/bin/Debug/NSOutlineViewHottness.app/Contents/MacOS/NSOutlineViewHottness".
Architecture set to: i386-apple-macosx.
(lldb) thread list
Process 79229 stopped
* thread #1: tid = 0x142776, 0x9af75e1a libsystem_kernel.dylib`__wait4 + 10, queue = 'com.apple.main-thread', stop reason = signal SIGSTOP
 thread #2: tid = 0x142790, 0x9af768d2 libsystem_kernel.dylib`kevent64 + 10, queue = 'com.apple.libdispatch-manager'
 thread #3: tid = 0x142792, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #4: tid = 0x142794, 0x9af6fa6a libsystem_kernel.dylib`semaphore_wait_trap + 10
 thread #5: tid = 0x142795, 0x9af75772 libsystem_kernel.dylib`__recvfrom + 10
 thread #6: tid = 0x142799, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #7: tid = 0x14279a, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #8: tid = 0x14279b, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #9: tid = 0x1427f8, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #10: tid = 0x1427fe, 0x9af6fa2e libsystem_kernel.dylib`mach_msg_trap + 10
(lldb) thread backtrace all
* thread #1: tid = 0x142776, 0x9af75e1a libsystem_kernel.dylib`__wait4 + 10, queue = 'com.apple.main-thread', stop reason = signal SIGSTOP
 * frame #0: 0x9af75e1a libsystem_kernel.dylib`__wait4 + 10
   frame #1: 0x986bfb25 libsystem_c.dylib`waitpid$UNIX2003 + 48
   frame #2: 0x028ba36d libmono-2.0.dylib`mono_handle_native_sigsegv(signal=11, ctx=0x03115fe0) + 541 at mini-exceptions.c:2323
   frame #3: 0x0290a8bb libmono-2.0.dylib`mono_arch_handle_altstack_exception(sigctx=<unavailable>, fault_addr=<unavailable>, stack_ovf=0) + 155 at exceptions-x86.c:1159
   frame #4: 0x0280b4fd libmono-2.0.dylib`mono_sigsegv_signal_handler(_dummy=<unavailable>, info=<unavailable>, context=<unavailable>) + 445 at mini.c:6861
   frame #5: 0x91ef403b libsystem_platform.dylib`_sigtramp + 43
   frame #6: 0x9a5dd0bd libobjc.A.dylib`objc_msgSend + 45
   frame #7: 0x96bcec03 libsystem_trace.dylib`_os_activity_initiate + 89
   frame #8: 0x9773ba91 AppKit`-[NSApplication sendAction:to:from:] + 548
   frame #9: 0x9773b82d AppKit`-[NSControl sendAction:to:] + 102
   frame #10: 0x97934d36 AppKit`__26-[NSCell _sendActionFrom:]_block_invoke + 176
   frame #11: 0x96bcec03 libsystem_trace.dylib`_os_activity_initiate + 89
   frame #12: 0x97787975 AppKit`-[NSCell _sendActionFrom:] + 161
   frame #13: 0x979188ea AppKit`-[NSButtonCell _sendActionFrom:] + 55
   frame #14: 0x979366e6 AppKit`__48-[NSCell trackMouse:inRect:ofView:untilMouseUp:]_block_invoke965 + 43
   frame #15: 0x96bcec03 libsystem_trace.dylib`_os_activity_initiate + 89
   frame #16: 0x977a3d00 AppKit`-[NSCell trackMouse:inRect:ofView:untilMouseUp:] + 2815
   frame #17: 0x977a2df4 AppKit`-[NSButtonCell trackMouse:inRect:ofView:untilMouseUp:] + 524
   frame #18: 0x977a233b AppKit`-[NSControl mouseDown:] + 762
   frame #19: 0x97cbc112 AppKit`-[_NSThemeWidget mouseDown:] + 378
   frame #20: 0x97d36d74 AppKit`-[NSWindow _reallySendEvent:] + 12353
   frame #21: 0x977201f9 AppKit`-[NSWindow sendEvent:] + 409
   frame #22: 0x976cdc67 AppKit`-[NSApplication sendEvent:] + 4679
   frame #23: 0x9754807c AppKit`-[NSApplication run] + 1003
```

Отслеживание такой проблемы потребует намного больше усилий. Если в верхнем уровне трассировки управляемого стека вы видите `at <unknown> <0xffffffff>` или `MonoMac.ObjCRuntime.Runtime.GetNSObject (IntPtr ptr)`, это повод предполагать, что произошла попытка выполнить управляемый код для объекта, который уже удален сборщиком мусора. Трассировка машинного стека содержит `trackMouse:inRect:ofView:untilMouseUp` в `NSCell _sendActionFrom`, а значит наш код обрабатывает событие щелчка мыши, затем пытается совершить обратный вызов в C#, и на этом все рушится.

Такие ошибки часто сложны в диагностике. Чтобы продемонстрировать ошибку воспроизводимым образом, мы добавили в обработчик событий кнопки `GC.Collect(2)` (что вызывает принудительную сборку мусора). После некоторого копания в коде и удаления его фрагментов до полного исчезновения ошибки, мы нашли [вот эту ошибку](https://bugzilla.xamarin.com/show_bug.cgi?id=23378):

```csharp
mainWindowController.Window.StandardWindowButton (NSWindowButton.CloseButton).Activated += HandleActivated;
```

Объект `NSButton`, возвращаемый из `StandardWindowButton()`, удаляется сборщиком мусора вопреки тому, что в нем зарегистрировано событие (это ошибка). Теперь, когда мы нажимаем эту кнопку, попытка вызвать это событие приводит к сбою приложения, если кнопка уже удалена сборщиком мусора.

В нашем примере это никак не отражено, но аналогичная трассировки стека может быть вызвана неверными сигнатурами методов в функциях, экспортированных в Objective-C с помощью `[Export]`. Например, если метод требует параметр типа `out string`, а вы вводите тип `string`, аварийное завершение приложения даст такой же результат.

## <a name="example-3-callbacks-and-managed-objects"></a>Пример 3. Обратные вызовы и управляемые объекты

Многие API Cocoa применяют обратные вызовы из библиотек. Такие вызовы создаются при определенных событиях, позволяя вам на них реагировать, либо при запросах на получение данных, требуемых для выполнения задачи. Самыми известными примерами такого подхода являются шаблоны **Delegate** и **DataSource**, но он реализован еще в множестве API-интерфейсов. Например, если вы переопределите методы `NSView` и добавите его в визуальное дерево, AppKit будет создавать обратные вызовы при определенных событиях.

Xamarin.Mac почти всегда правильно обрабатывает управляемый целевой объект для обратных вызовов, не позволяя сборщику мусора удалять их, пока сохраняется возможность обращения к ним. Но время от времени ошибка в привязке может приводить к нарушению этой системы. В таких случаях вы столкнетесь с такими неприятными сбоями:

```csharp
Thread 0 Crashed:: Dispatch queue: com.apple.main-thread

0   libsystem_kernel.dylib              0x98c2f69a __pthread_kill + 10
1   libsystem_pthread.dylib             0x90341f19 pthread_kill + 101
2   libsystem_c.dylib                   0x9453feee abort + 156
3   libmonosgen-2.0.dylib               0x020bfba5 mono_handle_native_sigsegv + 757
4   libmonosgen-2.0.dylib               0x0210b812 mono_arch_handle_altstack_exception + 162
5   libmonosgen-2.0.dylib               0x0200c55e mono_sigsegv_signal_handler + 446
6   libsystem_platform.dylib            0x9513003b _sigtramp + 43
7   ???                                 0xffffffff 0 + 4294967295
8   libmonosgen-2.0.dylib               0x0200c3a0 mono_sigill_signal_handler + 48
9   com.apple.AppKit                    0x99f76041 -[NSView setFrame:] + 448
10  com.apple.AppKit                    0x9a1fd4ea -[NSToolbarView adjustToWindow:attachedToEdge:] + 198
11  com.apple.AppKit                    0x9a1fd414 -[NSToolbar _adjustViewToWindow] + 68
12  com.apple.AppKit                    0x9a01eb0d -[NSToolbar _windowWillShowToolbar] + 79

```

Это руководство подскажет вам, как отслеживать ошибки такого рода, как правильно сообщать о них для устранения проблемы, и как создать временный код для обхода проблемы, пока устраняется ее причина.

### <a name="locating"></a>Поиск причины

Почти в любом сообщении об ошибке такого рода главным симптомом указывается сбой в машинном коде, чаще всего это `mono_sigsegv_signal_handler` или `_sigtrap` в верхнем кадре стека. Cocoa пытается выполнить обратный вызов в код C#, объект с которым уже удален сборщиком мусора, что приводит к сбою. Однако не каждая аварии с такими симптомами вызвана ошибкой привязки. Чтобы проверить причину проблемы, следует выполнить некоторые дополнительные исследования. 

Сложность в диагностике такой ошибки вызвана тем, что она возникает только **после** удаления проблемного объекта сборщиком мусора. Если вы полагаете, что столкнулись с ошибкой такого рода, добавьте в последовательность запуска проекта такой код:

```csharp
new System.Threading.Thread (() => 
{
    while (true) {
         System.Threading.Thread.Sleep (1000);
         GC.Collect ();
    }
}).Start (); 
```

Теперь сборщик мусора будет выполняться в приложении каждую секунду. Снова запустите приложение и попробуйте воспроизвести ошибку. Если ранее нестабильный сбой стал проявляться быстро и (или) стабильно, значит вы на правильном пути.

### <a name="reporting"></a>Отчеты

Теперь нам следует передать эту проблему в Xamarin, чтобы привязка была исправлена в будущих версиях. Если у вас есть корпоративная лицензия, создайте запрос в службу поддержки на странице 

[http://xamarin.com/support](http://xamarin.com/support)

В противном случае начните с поиска существующей проблемы.

- Проверьте [форумы Xamarin.Mac](https://forums.xamarin.com/categories/mac).
- Проверьте [репозиторий проблем](https://github.com/xamarin/xamarin-macios/issues).
- До перехода на GitHub проблемы Xamarin отслеживались в [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi). Попробуйте найти там похожие проблемы.
- Если вы не можете найти проблему, с которой столкнулись, поместите ее описание в [репозиторий проблем GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

Все проблемы GitHub находятся в открытом доступе. Здесь нет возможности скрыть комментарии или вложения. 

Предоставьте следующие сведения с максимально возможными подробностями.

- Простой пример, воспроизводящий проблему, если это возможно. Такая помощь будет **бесценной**. 
- Полная трассировка стека после сбоя.
- Код C#, относящийся к проявлению сбоя.   

### <a name="working-around"></a>Временное решение

После того, как вы найдете причину проблем, вы можете достаточно легко создать временное решение и применить его, пока не исправлена привязка. Вам нужно сделать так, чтобы проблемный объект (**представление**, **делегат**, **источник данных**) сохранялся в памяти при работе сборщика мусора. Для этого достаточно поставить явную ссылку на него.

В простых случаях, если существует только один экземпляр объекта, измените фрагменты кода такого вида:

```csharp
void AddObject ()
{
    item.View = new MyView ();
    ...
}
```

И примените вместо него статические переменные, примерно так:

```csharp
static NSObject view;
...

void AddObject ()
{
    view = new MyView ();
    item.View = view;
    ...
}
```

Если создаются несколько экземпляров объекта, попробуйте применить статический `HashSet`:

```csharp
static HashSet<NSObject> collection = new HashSet<NSObject> ();
...

void AddObject ()
{
    item.View = new MyView ();
    collection.Add (item.View );
    ...
}
```

Этот код можно удалить позднее, когда привязка будет исправлена и вы обновите Xamarin.Mac до версии, учитывающей это исправление.

## <a name="exception-bubbling-and-objective-c"></a>Восходящая маршрутизация исключений при работе с Objective-c

Никогда не допускайте "выход" исключений из управляемого кода C# в вызывающий метод Objective-C. Результаты такой маршрутизации исключений не определены, но обычно она завершается сбоем. Мы применяем все возможное для правильной восходящей маршрутизации событий при сбоях в машинном и управляемом коде, чтобы предоставить вам полезную информацию для быстрого решения проблем.

Мы не будем здесь углубляться в описание технических причин, но настройка инфраструктуры, гарантирующей перехват исключений из управляемого кода на всех границах взаимодействия между машинным и управляемым кодом, приводит к невероятно высоким затратам ресурсов. Часто самые обычные действиях включают _огромное_ количество таких переходов. Многие операции, особенно имеющие отношение к пользовательскому интерфейсу, должны завершаться быстро. В противном случае приложение будет работать скверно и недопустимо неэффективно. Существенная часть обратных вызовов выполняют простейшие действия с низкой вероятностью исключений, и для таких случаев строгий контроль будет только увеличивать затраты, но не принесет реальной пользы.

Поэтому мы не создаем такие блоки перехвата исключений. Во тех местах кода, где вы выполняете нестандартные или сложные действия (скажем, сложнее получения логических значений или простых математических расчетов), организуйте перехват самостоятельно. 

