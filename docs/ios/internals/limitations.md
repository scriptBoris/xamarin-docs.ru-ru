---
title: Ограничения Xamarin.iOS
description: В этом документе описаны ограничения для Xamarin.iOS, посвященные универсальные шаблоны, общие подклассы NSObjects, P/Invoke в универсальных объектов и многое другое.
ms.prod: xamarin
ms.assetid: 5AC28F21-4567-278C-7F63-9C2142C6E06A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/09/2018
ms.openlocfilehash: e154e4e1688b8a3d03459956934409fa9d5aef35
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998089"
---
# <a name="limitations-of-xamarinios"></a>Ограничения Xamarin.iOS

Так как приложения для iPhone с помощью Xamarin.iOS компилируются для статического кода, не можно использовать все функции, для которых требуется создание кода во время выполнения.

Существуют следующие ограничения Xamarin.iOS, по сравнению с рабочего стола Mono:

 <a name="Limited_Generics_Support" />


## <a name="limited-generics-support"></a>Поддержка ограниченного универсальных типов

В отличие от традиционных Mono и .NET код на iPhone статически компилируется заранее вместо компиляции по требованию с помощью JIT-компилятора.

Mono [полный AOT](http://www.mono-project.com/docs/advanced/aot/#full-aot) технология имеет ряд ограничений по отношению к универсальные шаблоны, они возникают, так как не все возможные универсального экземпляра можно определить заранее во время компиляции. Это не проблема для регулярного сред выполнения .NET и Mono как во время выполнения, используя непосредственно в компилятор всегда компиляции кода. Но это препятствие для статическим компилятором как Xamarin.iOS.

Ниже перечислены некоторые из распространенных проблем, с которыми разработчики столкнуться.

 <a name="Generic_Subclasses_of_NSObjects_are_limited" />


### <a name="generic-subclasses-of-nsobjects-are-limited"></a>Универсальный подклассы NSObjects ограничены

В настоящее время Xamarin.iOS имеет ограниченную поддержку создания общие подклассы NSObject класса, такие как поддержка универсальных методов. По состоянию на 7.2.1 с помощью универсального подклассы NSObjects возможна, похожее на следующее:

```csharp
class Foo<T> : UIView {
    [..]
}
```

> [!NOTE]
> Хотя универсальные подклассы NSObjects возможны, существуют некоторые ограничения. Чтение [подклассы nsobject](~/ios/internals/api-design/nsobject-generics.md) Дополнительные сведения



### <a name="pinvokes-in-generic-types"></a>P/вызывает в универсальных типах

P/Invoke в универсальных классах не поддерживаются:

```csharp
class GenericType<T> {
    [DllImport ("System")]
    public static extern int getpid ();
}
```

 <a name="Property.SetInfo_on_a_Nullable_Type_is_not_supported" />


### <a name="propertysetinfo-on-a-nullable-type-is-not-supported"></a>Property.SetInfo на тип Nullable не поддерживается.

С помощью отражения Property.SetInfo значение Nullable&lt;T&gt; сейчас не поддерживается.

 <a name="Value_types_as_Dictionary_Keys" />


### <a name="value-types-as-dictionary-keys"></a>Типы значений в качестве ключей словаря

С помощью типа значения как словарь&lt;TKey, TValue&gt; ключ является проблематичным, по умолчанию словарь конструктор пытается использовать EqualityComparer&lt;TKey&gt;. По умолчанию. EqualityComparer&lt;TKey&gt;. По умолчанию, в свою очередь, пытается использовать отражение для создания нового типа, реализующего IEqualityComparer&lt;TKey&gt; интерфейс.

Это работает для ссылочных типов (как отражение + создать новый тип шаг пропускается), но для значения типов это сбоев и записывает довольно быстро, при попытке использовать его на устройстве.

 **Инструкции по решению**: вручную реализовать [IEqualityComparer&lt;TKey&gt; ](xref:System.Collections.Generic.IEqualityComparer`1) интерфейс в новом типе и укажем экземпляр этого типа [словарь&lt;TKey, TValue&gt; ](xref:System.Collections.Generic.Dictionary`2) [(IEqualityComparer&lt;TKey&gt;)](xref:System.Collections.Generic.IEqualityComparer`1) конструктор.


 <a name="No_Dynamic_Code_Generation" />


## <a name="no-dynamic-code-generation"></a>Без создания динамического кода

Поскольку iPhone ядра не позволяет приложению динамически создания кода Mono на iPhone не поддерживает динамическое создание кода в любой форме. Сюда входит следующее.

-  System.Reflection.Emit недоступна.
-  Отсутствует поддержка System.Runtime.Remoting.
-  Отсутствует поддержка динамического создания типов (не Type.GetType («MyType "1»)), несмотря на то, что поиск существующих типов (Type.GetType («System.String»), отлично работает). 
-  Необходимо зарегистрировать обратный обратных вызовов со средой выполнения во время компиляции.


 
 <a name="System.Reflection.Emit" />


### <a name="systemreflectionemit"></a>System.Reflection.Emit

Отсутствие System.Reflection. **Выдавать** означает, что будет работать без кода, который зависит от создания кода среды выполнения. Сюда входят, например:

-  Среда выполнения динамического языка.
-  Любых языков, созданная на основе выполнения динамического языка.
-  TransparentProxy системе удаленного взаимодействия или все, что приведет к среде выполнения динамически создавать код. 


 **Важно:** не следует путать **Reflection.Emit** с **отражения**. Reflection.Emit — о создании кода динамически и у этого кода JIT-компилироваться и компилируются в машинный код. Из-за ограничений, налагаемых на iPhone (не JIT-компиляции) не поддерживается.

Но весь API отражения, включая Type.GetType («someClass»), методы, список свойств, получение, атрибуты и значения работает нормально.

### <a name="using-delegates-to-call-native-functions"></a>Использование делегатов для вызова неуправляемых функций

Чтобы вызвать собственную функцию через делегат C#, объявление делегата должен быть снабжен атрибутом один из следующих атрибутов:

- [UnmanagedFunctionPointerAttribute](xref:System.Runtime.InteropServices.UnmanagedFunctionPointerAttribute) (предпочтительно, поскольку это кросс платформенные и совместимых с .NET Standard 1.1 +)
- [MonoNativeFunctionWrapperAttribute](https://developer.xamarin.com/api/type/ObjCRuntime.MonoNativeFunctionWrapperAttribute)

Не удалось предоставить один из этих атрибутов приведет к ошибку во время выполнения, такие как:

```
System.ExecutionEngineException: Attempting to JIT compile method '(wrapper managed-to-native) YourClass/YourDelegate:wrapper_aot_native(object,intptr,intptr)' while running in aot-only mode.
```
 
 <a name="Reverse_Callbacks" />


### <a name="reverse-callbacks"></a>Обратный обратные вызовы

В стандартных Mono можно передать в неуправляемый код вместо указатель на функцию экземпляры делегата в C#. Среда выполнения обычно будет преобразовывать эти указатели функций в небольшой преобразователя, который позволяет неуправляемого кода для обратного вызова в управляемом коде.

В Mono этих мостах реализуются Just-in-Time компилятора. При использовании компилятора ahead-of-time, необходимый для iPhone, существуют два важных ограничения на этом этапе:

-  Необходимо пометить все методы обратного вызова с [MonoPInvokeCallbackAttribute](https://developer.xamarin.com/api/type/ObjCRuntime.MonoPInvokeCallbackAttribute) 
-  Методы должны быть статическими методами, не поддерживается для экземпляра метода. 
 
<a name="No_Remoting" />

## <a name="no-remoting"></a>Нет удаленного взаимодействия

Стек удаленного взаимодействия не доступен на Xamarin.iOS.


 <a name="Runtime_Disabled_Features" />


## <a name="runtime-disabled-features"></a>Среда выполнения отключены функции

В iOS Mono среды выполнения были отключены следующие функции:

-  профилировщик
-  Reflection.Emit
-  Функциональные возможности Reflection.Emit.Save
-  Привязки модели COM
-  JIT-модуль
-  Средство проверки метаданных (поскольку без JIT)


 <a name=".NET_API_Limitations" />


## <a name="net-api-limitations"></a>Ограничения API .NET

.NET API, предоставляемого — это подмножество полной платформы виде не все, что доступно в iOS. См. в разделе часто задаваемые вопросы о [список сборок, поддерживаемых в настоящее время](~/cross-platform/internals/available-assemblies.md).



В частности профиль API, используемый в Xamarin.iOS не входит System.Configuration, поэтому невозможно использовать внешние файлы XML позволяют настроить поведение среды выполнения.
