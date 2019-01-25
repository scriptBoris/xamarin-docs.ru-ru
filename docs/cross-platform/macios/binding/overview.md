---
title: Общие сведения о привязки Objective-C
description: В этом документе содержится обзор различных способов создания C# привязки для кода Objective-C, включая командной строки привязки, привязка проектов и Sharpie цели. Здесь также обсуждается, как работает привязка.
ms.prod: xamarin
ms.assetid: 9EE288C5-8952-C5A9-E542-0BD847300EC6
author: asb3993
ms.author: amburns
ms.date: 11/25/2015
ms.openlocfilehash: 3f15eaf9171ac44b870239fb5ffa14edd6210360
ms.sourcegitcommit: ee626f215de02707b7a94ba1d0fa1d75b22ab84f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54879307"
---
# <a name="overview-of-objective-c-bindings"></a>Общие сведения о привязки Objective-C

_Сведения о принципах работы процесса привязки_

Привязка библиотек Objective-C для использования с Xamarin принимает из трех этапов:

1. Запись C# «Определение API» для описания способа собственного API предоставления в .NET, и как она сопоставляется базовой Objective-C. Это делается с помощью стандартных C# конструкции, такие как `interface` и различные привязки **атрибуты** (см. в разделе, это [простой пример](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_an_API)).

2. После написания «определение API» C#, можно скомпилировать его для создания сборки «привязки». Это можно сделать на [ **командной строки** ](#commandline) или с помощью [ **проект привязки** ](#bindingproject) в Visual Studio для Mac или Visual Studio.

3. Эту сборку «привязки» добавляется в проект приложения Xamarin, чтобы собственные функции с помощью API, вы определили.
  Проект привязки никак не связано с проектах приложений.

**Примечание.** Шаг 1 можно автоматизировать с помощью [ **Objective Sharpie**](#objectivesharpie). Он проверяет Objective-C API и создает предложенное C# «Определение API». Можно настраивать файлы, созданные программой Sharpie цели и использовать их в проекте привязки (или в командной строке) для создания привязки сборки. Цели Sharpie не приводит к созданию привязки сам по себе, он просто необязательная часть большего процесса.

Можно также прочитать дополнительные технические сведения о [принцип работы](#howitworks), который поможет вам писать привязок.

<a name="Command_Line_Bindings" /><a name="commandline" />

## <a name="command-line-bindings"></a>Привязки командной строки

Можно использовать `btouch-native` для Xamarin.iOS (или `bmac-native` при использовании Xamarin.Mac) для создания привязок напрямую. Он работает путем передачи C# API определений, которые вы создали вручную (или, с помощью Sharpie цель) в средство командной строки (`btouch-native` для iOS или `bmac-native` для Mac).


Ниже приведен общий синтаксис для вызова этих средств.

```csharp
# Use this for Xamarin.iOS:
bash$ /Developer/MonoTouch/usr/bin/btouch-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

```csharp
# Use this for Xamarin.Mac:
bash$ bmac-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

Приведенная выше команда создаст файл `cocos2d.dll` в текущем каталоге, и он будет содержать полной привязкой библиотеки, который можно использовать в проекте. Это средство, которое использует Visual Studio для Mac для создания привязок, если вы используете проект привязки (описано [ниже](#bindingproject)).


<a name="bindingproject" />

## <a name="binding-project"></a>Привязка проекта

Проект привязки можно создать в Visual Studio для Mac или Visual Studio (Visual Studio поддерживает только iOS привязки) и упрощает для редактирования и сборки определения API для привязки (а не с помощью командной строки).

Выполните это [руководство по началу работы](~/cross-platform/macios/binding/objective-c-libraries.md#Getting_Started) чтобы узнать, как создать и использовать привязки проекта для создания привязки.

<a name="objectivesharpie" />

## <a name="objective-sharpie"></a>Objective Sharpie

Цели Sharpie — средство командной строки еще одна, отдельная, которое помогает в начальной стадии создания привязки. Сама по себе не создается привязка, скорее она автоматизирует начальный этап создания определения API для целевой собственной библиотеки.

Чтение [Sharpie цели документация](~/cross-platform/macios/binding/objective-sharpie/index.md) чтобы узнать, как преобразовать собственные библиотеки, встроенных инфраструктур и CocoaPods в определения API, который может быть построен в привязках.

<a name="howitworks" />

## <a name="how-binding-works"></a>Как работает привязка

Можно использовать [[регистрация]](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/) атрибут, [[Экспорт]](https://developer.xamarin.com/api/type/Foundation.ExportAttribute/) атрибут, и [вызова селектора вручную Objective-C](~/ios/internals/objective-c-selectors.md) друг с другом, чтобы вручную выполнять привязку new (ранее несвязанная) типы Objective-C.

Во-первых найти тип, для привязки. Обсуждение целей (и простоты), мы будем привязать [NSEnumerator](http://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSEnumerator_Class/Reference/Reference.html) типа (который уже был привязан в [Foundation.NSEnumerator](https://developer.xamarin.com/api/type/Foundation.NSEnumerator/); реализация ниже просто для примера является целей).

Во-вторых, необходимо создать C# типа. Мы скорее всего захотите добавить его в пространстве имен. так как Objective-C не поддерживает пространства имен, необходимо использовать `[Register]` атрибут, чтобы изменить имя типа, который Xamarin.iOS будет зарегистрирован в среде выполнения Objective-C. C# Тип также должен наследовать от [Foundation.NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/):

```csharp
namespace Example.Binding {
    [Register("NSEnumerator")]
    class NSEnumerator : NSObject
    {
        // see steps 3-5
    }
}
```

В-третьих, см. в документации Objective-C и создайте [ObjCRuntime.Selector](https://developer.xamarin.com/api/type/ObjCRuntime.Selector/) экземпляров для каждого выбора, вы хотите использовать. Поместите их в теле класса:

```csharp
static Selector selInit       = new Selector("init");
static Selector selAllObjects = new Selector("allObjects");
static Selector selNextObject = new Selector("nextObject");
```

В-четвертых ваш тип, необходимо предоставить конструкторы. Вы *необходимо* цепочку ваш вызов конструктора для конструктора базового класса. `[Export]` Атрибуты программного кода Objective-C для вызова конструкторов с именем указанного селектора:

```csharp
[Export("init")]
public NSEnumerator()
    : base(NSObjectFlag.Empty)
{
    Handle = Messaging.IntPtr_objc_msgSend(this.Handle, selInit.Handle);
}
```

```csharp
// This constructor must be present so that Xamarin.iOS
// can create instances of your type from Objective-C code.
public NSEnumerator(IntPtr handle)
    : base(handle)
{
}
```

В пятых предоставляют методы для каждого из селекторы, объявленной в шаге 3. В них используются `objc_msgSend()` для вызова селектора на собственный объект. Обратите внимание на использование [Runtime.GetNSObject()](https://developer.xamarin.com/api/member/ObjCRuntime.Runtime.GetNSObject/(System.IntPtr)) для преобразования `IntPtr` в соответствующим образом типизированного `NSObject` (sub) типа. Если этот метод можно было вызвать из кода Objective-C, член должен *необходимо* быть **виртуального**.

```csharp
[Export("nextObject")]
public virtual NSObject NextObject()
{
    return Runtime.GetNSObject(
        Messaging.IntPtr_objc_msgSend(this.Handle, selNextObject.Handle));
}
```

```csharp
// Note that for properties, [Export] goes on the get/set method:
public virtual NSArray AllObjects {
    [Export("allObjects")]
    get {
        return (NSArray) Runtime.GetNSObject(
            Messaging.IntPtr_objc_msgSend(this.Handle, selAllObjects.Handle));
    }
}
```

Окончательная сборка:

```csharp
using System;
using Foundation;
using ObjCRuntime;

namespace Example.Binding {
    [Register("NSEnumerator")]
    class NSEnumerator : NSObject
    {
        static Selector selInit       = new Selector("init");
        static Selector selAllObjects = new Selector("allObjects");
        static Selector selNextObject = new Selector("nextObject");

        [Export("init")]
        public NSEnumerator()
            : base(NSObjectFlag.Empty)
        {
            Handle = Messaging.IntPtr_objc_msgSend(this.Handle,
                selInit.Handle);
        }

        public NSEnumerator(IntPtr handle)
            : base(handle)
        {
        }

        [Export("nextObject")]
        public virtual NSObject NextObject()
        {
            return Runtime.GetNSObject(
                Messaging.IntPtr_objc_msgSend(this.Handle,
                    selNextObject.Handle));
        }

        // Note that for properties, [Export] goes on the get/set method:
        public virtual NSArray AllObjects {
            [Export("allObjects")]
            get {
                return (NSArray) Runtime.GetNSObject(
                    Messaging.IntPtr_objc_msgSend(this.Handle,
                        selAllObjects.Handle));
            }
        }
    }
}
```

## <a name="related-links"></a>Связанные ссылки

- [Курс Xamarin University. Создание библиотеки привязки Objective-C](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Курс Xamarin University. Создание библиотеки привязки Objective-C с помощью цели Sharpie](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)